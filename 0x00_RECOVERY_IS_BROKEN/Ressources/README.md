
# Recovery is broken 

## Context summary
Currently we use the page http://192.168.56.103/?page=recover, which we normally use to recover a forgotten password. We see that no entry is visible.

## Vulnerability

The vulnerability lies in the authentication which is simply broken in the recovery section, if you inspect the page you can find a hidden input :

- `<input type="hidden" name="mail" value="webmaster@borntosec.com" maxlength="15">`

By replacing the email with another, the flag is displayed in plain text.

## Attack Design
By changing the recipient's address followed by a submit action we obtain the flag in clear text.
- `<input type="hidden" name="mail" value="my@gmail.com" maxlength="15">`

## Remediation

> Flag : `1d4855f7337c0c14b6f44946872c4eb33853f40b2d54393fbe94f49f1e19bbb0`
