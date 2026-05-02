# Troubleshooting

## Script immediately exits on windows

There are two possible reasons:

1. your company blocks the script (some have a whitelist)... ask your administrator
2. your Antivirus or Windows Defender interfere with the installation. Disable them temporarily.

## C compiler cannot create executables

### Darwin

You need to update your XCode command line tools, e.g. [like this](https://stackoverflow.com/questions/34617452/how-to-update-xcode-from-command-line).

## Certificate authority errors (curl or wget)

GHCup uses the tools `curl` or `wget` to download files. If certificate
authority (CA) certificates are outdated or improperly configured, then these
tools may be unable to download and errors will be reported.

### Unix-like operating systems

There are two known workarounds to being unable to download with `curl`:

1.  Tell `curl` to ignore CA certificate errors (dangerous):
    ~~~sh
    `curl -k https://raw.githubusercontent.com/haskell/ghcup-hs/master/scripts/bootstrap/bootstrap-haskell | GHCUP_CURL_OPTS="-k" sh`
    ~~~

2.  Try to use `wget` instead:
    ~~~sh
    `wget -O /dev/stdout https://raw.githubusercontent.com/haskell/ghcup-hs/master/scripts/bootstrap/bootstrap-haskell | BOOTSTRAP_HASKELL_DOWNLOADER=wget sh`
    ~~~

### Windows

On Windows, GHCup uses the MSYS2-supplied `curl` or `wget` in the MSYS2
environment that has specified for use with GHCup. In some circumstances (see
further below), the MSYS2 environment may need certain CA certificates to be
manually added and trusted.

On Windows, you can disable the use of `curl` like so:
~~~pwsh
Set-ExecutionPolicy Bypass -Scope Process -Force;[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;try { & ([ScriptBlock]::Create((Invoke-WebRequest https://www.haskell.org/ghcup/sh/bootstrap-haskell.ps1 -UseBasicParsing))) -Interactive -DisableCurl } catch { Write-Error $_ }
~~~

In some circumstances, an organisation may install their own CA certificates
onto a Windows system. However, OpenSSL in a MSYS2 environment does not
integrate with the Windows system's store of certificates. In those
circumstances, the certificates need to be obtained and then the MSYS2
environment configured to use them before `curl` or `wget` will work. These
circumstances may give rise to errors that include messages such as:
~~~text
curl: (60) SSL certificate problem: unable to get local issuer certificate
More details here: https://curl.se/docs/sslcerts.html
~~~

or
~~~text
curl: (35) schannel: next InitializeSecurityContext failed: CRYPT_E_NO_REVOCATION_CHECK (0x80092012) - The revocation function was unable to check revocation for the certificate.
~~~

or
~~~text
ERROR: The certificate of ‘downloads.haskell.org’ is not trusted.
ERROR: The certificate of ‘downloads.haskell.org’ doesn't have a known issuer.
~~~

The relevant CA certificates can be identified and obtained by opening
`https://downloads.haskell.org` in a browser and getting information about the
heirarchy of certificates. In the case of Microsoft Edge, that information is
provided via (in order) the 'padlock' icon, 'Connection is secure', the
'certificate' icon, and the 'Details' tab of the 'Certificate Viewer' dialog.
Each certificate in the heirarchy above the domain needs to be chosen and
exported as a file in the relevant format (`*.pem`, `*.crt` or `*.cer` files).

All those certificate files are then copied to the directory
`/etc/pki/ca-trust/source/anchors` in the MSYS2 environment.

In a MSYS2 shell provided by that environment, the command `update-ca-trust`
will (silently) add the CA certificates in that location to those that are
trusted. This command is available as part of the basic MSYS2 installation.

See also the related MSYS2
[FAQ](https://www.msys2.org/docs/faq/#how-can-i-make-msys2pacman-trust-my-companys-custom-tls-ca-certificate).
