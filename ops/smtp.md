# SMTP mail thawn chhuahna server siam rawh

## thuhmahruai

SMTP hian cloud vendor aṭangin direct-in service a lei thei a, chungte chu:

* [Amazon SES SMTP hmanga siam a ni](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali cloud email hmanga nawr](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

I mail server pawh i siam thei bawk - unlimited sending, low overall cost.

A hnuaiah hian mahni mail server siam dan step by step kan entir a.

## Server thlan dan tur

Self-hosted SMTP server hian public IP a mamawh a, port 25, 456, leh 587 te chu hawn a ni.

Public cloud hman tlanglawn tak takte chuan heng port te hi default-in an block a, work order tihchhuah hmangin hawn theih a ni mai thei a, mahse a tawpah chuan a buaithlak hle.

Heng ports te hi hawn tawh leh reverse domain name setting up support thei host atanga lei ka rawt a ni.

Hetah hian [Contabo hi](https://contabo.com) ka recommend a ni.

Contabo hi Germany khawpui Munich-a awm hosting provider a ni a, kum 2003 khan din a ni a, a man pawh a inel hle.

Leina pawisa atan Euro i thlang a nih chuan a man a tlawm zawk ang (8GB memory leh CPU 4 nei server pakhat chu kum khatah yuan 529 vel a ni a, a tir lama installation fee pawh kum khat chhung a thlawna pek a ni).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Order i siam dawnin remark `prefer AMD` , AMD CPU nei server chuan performance a nei tha zawk ang.

A hnuaia tarlan ang hian Contabo VPS hi entirnan ka la ang a, mahni mail server siam dan tur ka entir ang.

## Ubuntu system siam dan tur

Heta operating system hi Ubuntu 22.04 a ni

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

ssh-a server-ah `Welcome to TinyCore 13!` (a hnuaia lem a kan hmuh ang hian), system chu a la install lo tihna a ni. Khawngaihin ssh hi disconnect la, minute engemaw zat nghak la, log in leh rawh.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

`Welcome to Ubuntu 22.04.1 LTS` a lo lan chuan initialization a zo tawh a, a hnuaia step te hi i chhunzawm thei tawh ang.

### [Optional] Hmasawnna boruak chu initialize rawh

He step hi duhthlan tur a ni.

A awlsam zawk nan ubuntu software installation leh system configuration chu [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) ah ka dah a.

A hnuaia command hi run la, click khat lek hmangin install rawh.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Chinese hmangtute chuan a hnuaia command hi hmang zawk ila, tawng, time zone, etc. chu automatic in a set ang.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo hian IPV6 a tichak a

SMTP pawhin IPV6 address nei email a thawn theih nan IPV6 enable rawh.

`/etc/sysctl.conf` tih chu edit rawh

A hnuaia line te hi modify emaw add emaw rawh

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

[Contabo tutorial hi zawm la: I server-a IPv6 connectivity dah belh](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

`/etc/netplan/01-netcfg.yaml` , edit la, a hnuaia figure-a kan hmuh ang hian line tlemte add rawh (Contabo VPS default configuration file-ah hian heng line te hi a awm tawh a, uncomment mai rawh).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Tichuan `netplan apply` modified configuration chu a effect theih nan.

Configuration a hlawhtlin hnuah `curl 6.ipw.cn` hmangin i external network ipv6 address i en thei ang.

## Configuration repository ops chu clone rawh

```
git clone https://github.com/wactax/ops.soft.git
```

## I domain name atan SSL certificate a thlawna siam rawh

Mail thawn tur chuan encryption leh signing atan SSL certificate a ngai a.

Certificate siam nan [acme.sh](https://github.com/acmesh-official/acme.sh) kan hmang thin.

acme.sh hi open source hmanga certificate signing hmanrua a ni a,

Configuration warehouse ops.soft ah lut la, `./ssl.sh` run la, **upper directory ah** `conf` folder a siam ang.

[acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) atang hian i DNS provider zawng la, `conf/conf.sh` edit rawh.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Tichuan `./ssl.sh 123.com` run la, i domain name atan `123.com` leh `*.123.com` certificate siam rawh.

Run hmasa ber hian [acme.sh chu](https://github.com/acmesh-official/acme.sh) automatic in a install ang a, automatic renewal atan scheduled task a add ang. `crontab -l` i hmu thei a, hetiang line hi a awm.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Certificate siam chhuahna tur kawng chu `/mnt/www/.acme.sh/123.com_ecc。`

Certificate renewal hian `conf/reload/123.com.sh` script a ko ang a, he script hi edit la, `nginx -s reload` ang chi command te add in related application te certificate cache refresh theih a ni.

## Chasquid hmangin SMTP server siam rawh

[chasquid](https://github.com/albertito/chasquid) hi open source SMTP server a ni a, Go tawng hmanga ziah a ni.

Hmanlai mail server program Postfix leh Sendmail te thlakna atan chasquid hi a awlsam zawk a, hman a awlsam zawk a, secondary development atan pawh a awlsam zawk bawk.

Run `./chasquid/init.sh 123.com` click khatah automatic in a install ang (123.com hi i sending domain name hmangin thlak leh rawh).

## Email Signature DKIM chu configure rawh

DKIM hi lehkha thawn chu spam anga ngaih a nih loh nan email signatures thawn nan hman a ni.

Command chu hlawhtling taka a kal hnuah DKIM record (a hnuaia kan hmuh ang hian) set turin a rawn ti ang.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

I DNS-ah TXT record dah mai rawh (a hnuaia kan hmuh ang hian).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Service dinhmun & logs te en rawh

 `systemctl status chasquid` Service dinhmun en rawh.

Normal operation dinhmun chu a hnuaia lem ang hian a ni

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` emaw `journalctl -xeu chasquid` error log chu a en thei ang.

## Domain hming configuration chu reverse rawh

Reverse domain name hi IP address chu a inmil domain name-a resolve theihna tur a ni.

Reverse domain name set hian email te chu spam anga hriat theih a ni lo.

Mail a dawn chuan receiving server chuan sending server IP address-ah reverse domain name analysis a ti ang a, sending server hian reverse domain name dik tak a nei em tih a nemnghet ang.

Sending server hian reverse domain name a neih loh chuan emaw reverse domain name chu sending server IP address nen a inmil lo a nih chuan receiving server chuan email chu spam a ni tih a hre thei a, a hnawl thei bawk.

[https://my.contabo.com/rdns](https://my.contabo.com/rdns) ah lut la, a hnuaia mi ang hian configure rawh

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Reverse domain name set zawhah chuan domain name ipv4 leh ipv6 te forward resolution chu server ah configure tur a ni tih hre reng ang che.

## chasquid.conf hostname chu edit rawh

`conf/chasquid/chasquid.conf` chu reverse domain name value angin siam danglam rawh.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Tichuan `systemctl restart chasquid` run la, service chu restart leh rawh.

## Backup conf chu git repository ah a awm

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Entirnan, conf folder chu ka github process ah hetiang hian ka back up thin

Private warehouse siam hmasa phawt ang che

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Conf directory ah lut la, warehouse ah submit rawh

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Sender chu add rawh

tlan

```
chasquid-util user-add i@wac.tax
```

Sender a add thei bawk

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Password chu a set dik em tih enfiah rawh

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

User i add hnuah `chasquid/domains/wac.tax/users` update a ni ang a, warehouse ah submit ngei ngei tur a ni tih hre reng ang che.

## DNS ah hian SPF record a awm

SPF ( Sender Policy Framework ) hi email verification technology a ni a, email fraud venna atana hman thin a ni.

Mail thawntu IP address chu a domain name nia a sawi DNS record nen a inmil em tih enfiahin, mi bum hmangte chuan email lem an thawn thei lo.

SPF record dah belh hian email te chu a theih ang anga spam anga hriat a nih loh nan a veng thei a ni.

I domain name server hian SPF type a support loh chuan TXT type record kha add mai tur a ni.

Entirnan, `wac.tax` SPF chu hetiang hi a ni

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF chu `_spf.wac.tax` atan a ni

`v=spf1 a:smtp.wac.tax ~all`

Hetah hian `include:_spf.google.com` ka nei tih hre reng ang che, hei hi a chhan chu a hnuah Google mailbox-ah `i@wac.tax` chu sending address atan ka configure dawn a ni.

## DNS configuration DMARC hmanga siam a ni

DMARC hi (Domain-based Message Authentication, Reporting & Conformance) tih tawifelna a ni.

SPF bounces (configuration error vang pawh a ni thei, mi dangin spam thawn tura nangmah anga inlan a nih pawhin) capture nan hman a ni.

TXT record `_dmarc` , .

A thupui chu hetiang hi a ni

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Parameter tin awmzia chu hetiang hi a ni

### p (Policy) a ni.

SPF (Sender Policy Framework) emaw DKIM (DomainKeys Identified Mail) verification nei lo email te enkawl dan tur a tarlang. p parameter hi value pathum zinga pakhatah set theih a ni:

* none: Action lak a ni lo, verification result chauh email reporting mechanism hmangin a thawntu hnenah pek let leh a ni.
* Quarantine: Verification pass lo mail chu spam folder-ah dah la, mahse direct-a mail chu a hnawl lo ang.
* reject: Verification hlawhchham email te chu direct-in hnawl rawh.

### fo (Hlachhamna tur duhthlan tur) .

Reporting mechanism-in information a rawn thawn let zat a tarlang. A hnuaia value pakhatah hian set theih a ni:

* 0: Message zawng zawngah validation result report rawh
* 1: Verification hlawhchham message chauh report tur
* d: Domain name verification a hlawhchham chauh report tur
* s: SPF verification hlawhchhamna report chauh
* l: DKIM verification hlawhchhamna chauh report tur

### rua & ruf tih a ni

* rua (Aggregate reports atana URI report): Aggregated report dawng tur email address
* ruf (Forensic report atana URI report): report kimchang dawng tur email address

## Google Mail-a email forward turin MX records te dah tel bawk ang che

Universal address (Catch-All, he domain name-a email thawn apiang a dawng thei a, prefix-a khapna awm lovin) free corporate mailbox ka hmuh loh avangin chasquid hmangin email zawng zawng chu ka Gmail mailbox-ah ka forward vek a ni.

**I paid business mailbox i neih chuan MX hi modify lo la, he step hi skip rawh.**

`conf/chasquid/domains/wac.tax/aliases` edit la, mailbox thawn chhuahna tur set rawh

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` hian email zawng zawng a tarlang a, `i` hi a chunga kan siam sending user email address prefix a ni. Mail forward tur chuan user tin hian line an add a ngai a ni.

Tichuan MX record chu add leh ang (hetah hian reverse domain name address hi direct in ka point a, a hnuaia figure-a line hmasa bera kan hmuh ang hian).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Configuration zawh hnuah email address dang hmangin `i@wac.tax` ah email i thawn thei a, `any123@wac.tax` ah Gmail ah email i dawng thei em tih i en thei bawk.

A nih loh chuan chasquid log ( `grep chasquid /var/log/syslog` ) kha check la.

## Google Mail hmangin i@wac.tax ah email thawn theih a ni

Google Mail-in mail a dawn hnu chuan ka pianphungah chuan i.wac.tax@gmail.com tih ai chuan `i@wac.tax` hmanga chhan ka beisei a ni.

[https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) ah lut la, "Email address dang add rawh" tih kha click rawh.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Tichuan, email forward a nihna atanga verification code a dawn chu ziak lut rawh.

A tawp berah chuan default sender address angin set theih a ni (address inang hmanga chhan theihna option nen).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Chutiang chuan SMTP mail server din chu kan zo tawh a, chutih rual chuan Google Mail hmangin email thawn leh dawn kan hmang bawk.

## Configuration a hlawhtling em tih enfiah nan test email thawn rawh

`ops/chasquid` kha ziak lut rawh

Run `direnv allow` to install dependencies (direnv hi one-key initialization process hmasaah install a ni tawh a, shell-ah hook dah a ni tawh bawk)

tichuan tlan rawh

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Parameters awmzia chu hetiang hi a ni

* user: SMTP hmangtu hming
* pass: SMTP password hmanga siam a ni
* to: dawngtu

Test email pawh i thawn thei bawk.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Configuration te a hlawhtling em tih enfiah nan test email dawng turin Gmail hman a tha.

### TLS standard encryption hmanga tih a ni

A hnuaia lem a kan hmuh ang hian he lock te tak te hi a awm a, chu chu SSL certificate chu hlawhtling taka enable a ni tawh tihna a ni.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Tichuan "Show Original Email" tih kha click leh rawh.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM a ni

A hnuaia figure-a kan hmuh ang hian Gmail original mail page-ah DKIM a lang a, chu chu DKIM configuration a hlawhtling tihna a ni.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Original email header-ah Received tih kha check la, sender address chu IPV6 a ni tih i hmu thei a, chu chu IPV6 pawh hlawhtling taka configure a ni tihna a ni.
