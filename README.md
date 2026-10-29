# MTA-STS för resultify.se

Policyfilen för [MTA-STS](https://www.rfc-editor.org/rfc/rfc8461) publiceras med GitHub Pages på `mta-sts.resultify.se`.

DNS-posterna ligger hos nameSRS och finns inte i det här repot.

## Innehåll

| Fil | Syfte |
| --- | --- |
| `.well-known/mta-sts.txt` | Policyn som avsändande servrar hämtar |
| `CNAME` | Custom domain för Pages: `mta-sts.resultify.se` |
| `.nojekyll` | Tom fil. Utan den ignorerar Jekyll katalogen `.well-known` |

Nuvarande policy är `mode: testing` med `mx: smtp.google.com`. `testing` rapporterar avvikelser men stoppar inte leverans.

## Pages

Repot ska ligga i organisationen **resultify**. Pages deployar från grenen `main`, mappen `/` (root), med custom domain `mta-sts.resultify.se` och Enforce HTTPS.

Hos nameSRS ska `mta-sts.resultify.se` peka som CNAME mot organisationens `github.io`-värd. TXT-posten `_mta-sts.resultify.se` hör också hemma där. Ändra dem inte från det här repot.

## Ändra policyn

1. Redigera `.well-known/mta-sts.txt`.
2. Committa till `main`. Pages publicerar filen på nytt.
3. Byt `id` i TXT-posten `_mta-sts.resultify.se` hos nameSRS när innehållet ändras, så att mottagare hämtar den nya versionen innan `max_age` löper ut.

`mode` kan vara `testing`, `enforce` eller `none`. Gå till `enforce` först när loggarna visar att legitim post går till en listad MX.

## Kontroll

```bash
curl -sS -D - -o /tmp/mta-sts.txt --max-redirs 0 \
  https://mta-sts.resultify.se/.well-known/mta-sts.txt
diff -u .well-known/mta-sts.txt /tmp/mta-sts.txt
```

Förväntat svar är status 200, `content-type: text/plain` och ingen `Location`-header. `diff` ska inte skriva något.
