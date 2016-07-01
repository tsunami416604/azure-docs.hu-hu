<properties
    pageTitle="SSH-kulcsok létrehozása Linux és Mac rendszerben | Microsoft Azure"
    description="SSH-kulcsokat hozhat létre és használhat Linux és Mac rendszerben a Resource Manager és a klasszikus telepítési üzembe helyezési modellek számára az Azure-ban."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="v-livech"/>

# SSH-kulcsok létrehozása Linux és Mac rendszerben Linux virtuális gépek számára az Azure-ban

Jelszóval védett nyilvános és titkos SSH-kulcs létrehozásához meg kell nyitnia egy terminált a munkaállomáson.  Az SSH-kulcsok beszerzése után létrehozhat új virtuális gépeket ezzel a kulccsal alapértelmezés szerint, vagy meglévő virtuális gépekhez adhatja hozzá a nyilvános kulcsot az Azure parancssori felület és Azure-sablonok használatával.  Ez jelszó nélküli bejelentkezéseket tesz lehetővé SSH-n keresztül a sokkal biztonságosabb, jelszavak helyett kulcsokat használó hitelesítési módszerrel.

## Gyors parancslista

Az alábbi parancspéldákban cserélje a &lt; és &gt; közötti értékeket a saját környezet értékeire.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Adja meg a `~/.ssh/` könyvtárban menteni kívánt fájl nevét:

```bash
<azure_fedora_id_rsa>
```

Adja meg az azure_fedora_id_rsa jelszavát:

```bash
<correct horse battery staple>
```

Adja az újonnan létrehozott kulcsot a Linux és Mac `ssh-agent` ügynökéhez (a rendszer az OSX Kulcskarikájához is hozzáadja):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Másolja a nyilvános SSH-kulcsot a Linux-kiszolgálóra:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Tesztelje a bejelentkezést jelszó helyett kulcsok használatával:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## Bevezetés

A nyilvános és titkos SSH-kulcsok használata biztosítja a legegyszerűbb módot a Linux-kiszolgálókra való bejelentkezéshez, emellett pedig a [nyíltkulcsú titkosítás](https://en.wikipedia.org/wiki/Public-key_cryptography) a jelszavak használatánál egy sokkal biztonságosabb módszert is lehetővé tesz a Linux vagy BSD virtuális gépekre való bejelentkezéshez az Azure-ban, hiszen a jelszavak sokkal könnyebben törhetők fel találgatásos módszeren alapuló támadással. A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal.  A létrehozott titkos SSH-kulcsot [biztonságos jelszó](https://www.xkcd.com/936/) védi, és ez a jelszó csak a titkos SSH-kulcs elérésére szolgál, **nem** pedig a felhasználói fiók jelszava.  Amikor jelszót ad hozzá az SSH-kulcshoz, az titkosítja a titkos kulcsot, így a titkos kulcs nem használható az azt feloldó jelszó nélkül.  Ha egy támadó el tudná lopni a titkos kulcsot, és azt nem védené jelszó, a titkos kulccsal bejelentkezhetne azon kiszolgálókra, amelyeken a megfelelő nyilvános kulcs lett telepítve.  Ha a titkos kulcsot jelszó védi, a kulcsot nem használhatja a támadó, így ez további biztonsági réteget nyújt az Azure-beli infrastruktúra számára.


Ez a cikk az *ssh-rsa* formátumú kulcsfájlok létrehozását ismerteti, mivel ezek ajánlottak a Resource Managerben üzemelő példányok esetén, és ezekre van szükség a [portálon](https://portal.azure.com) a klasszikus és Resource Managerben üzemelő példányokhoz is.


## Az SSH-kulcsok létrehozása

Az Azure legalább 2048 bites ssh-rsa formátumú nyilvános és titkos kulcsokat követel meg. A pár létrehozásához az `ssh-keygen` parancsot fogjuk használni, amely kérdések sorát teszi fel, majd létrehoz egy titkos kulcsot és egy megfelelő nyilvános kulcsot. Az Azure virtuális gép létrehozásakor át kell adnia a nyilvános kulcs tartalmát, amelyet a Linux virtuális gépre másol a rendszer, majd a helyi és biztonságosan tárolt titkos kulcsot egyszerre használja a hitelesítéshez a bejelentkezéskor.

## Az ssh-keygen használata

Ez a parancs jelszóval védett (titkosított) SSH-kulcspárt hoz létre 2048 bites RSA használatával, és az egyszerű azonosítás érdekében megjegyzéssel lesz ellátva.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_A parancs ismertetése_

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-t rsa` = a létrehozni kívánt kulcs típusa, amely az [RSA formátum] (https://en.wikipedia.org/wiki/RSA_(cryptosystem),

`-b 2048` = a kulcs bitjeinek száma,

`-C "ahmet@fedoraVMAzure"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében.  Általában e-mailt használnak megjegyzésként, de használhat bármit, ami a leginkább megfelel az infrastruktúra szempontjából.

## Az ssh-keygen bemutatása

Az alábbiakban az egyes lépesek részletes ismertetését olvashatja.  Először futtassa a `ssh-keygen` parancsot.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Mentett kulcsfájlok:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

A cikkben használt kulcspár neve.  Az **id_rsa** nevű kulcspár az alapértelmezett, és mivel egyes eszközök az **id_rsa** nevű titkos kulcsfájlt várhatják, célszerű ezt a nevet adni a kulcspárnak. (A `~/.ssh/` általában az összes SSH-kulcspár és az SSH konfigurációs fájl alapértelmezett helye.)

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Ez a parancs az új kulcspárokat és azok engedélyeit jeleníti meg. `ssh-keygen` létrehozza a `~/.ssh` könyvtárat, ha az még nem létezik, és beállítja a megfelelő tulajdonjogokat és fájlmódokat.

Kulcs jelszava:

`Enter passphrase (empty for no passphrase):`

Erősen ajánlott jelszót (a `ssh-keygen` ezt „hozzáférési kódnak” nevezi) hozzáadni a kulcspárokhoz. A kulcspárt védő jelszó nélkül bárki bejelentkezhet a titkos kulcsfájl másolatával bármely olyan kiszolgálóra – a kiszolgálóira –, amely rendelkezik a megfelelő nyilvános kulccsal. A jelszó hozzáadása így sokkal nagyobb védelmet nyújt arra az esetre, ha valaki megszerezné a titkos kulcsot, mert így lesz ideje módosítani a hitelesítéséhez használt kulcsokat.

## Az ssh-agent használata a titkos kulcs jelszavának tárolásához

Ha nem szeretné beírni a titkos kulcsfájl jelszavát minden SSH-bejelentkezéskor, az `ssh-agent` segítségével gyorsítótárazhatja a titkos kulcsfájl jelszavát a Linux virtuális gépekre való gyors és biztonságos bejelentkezéshez.  Ha OSX rendszert használ, a Kulcskarika biztonságosan módon menteni fogja a titkos kulcsok jelszavát az `ssh-agent` indításakor.

Először ellenőrizze, hogy az `ssh-agent` fut-e.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal, amivel OSX rendszeren elindítja a hitelesítő adatok tárolására szolgáló Kulcskarikát.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

A titkos kulcs jelszavát a rendszer mentette, így nem kell beírni a kulcs jelszavát minden SSH-bejelentkezéskor.

## SSH konfigurációs fájl létrehozása és konfigurálása

Jóllehet nem feltétlenül szükséges Linux virtuális gép beüzemeléséhez, az ajánlott eljárás szerint érdemes létrehozni és konfigurálni egy `~/.ssh/config` fájlt, hogy ne használjon véletlenül jelszavakat a virtuális gépekkel való bejelentkezéshez, ne használja automatikusan különböző Azure virtuális gépek különböző kulcspárjait, illetve hogy ne konfiguráljon más programokat (például **git**) több kiszolgálóra vonatkozóan.

Az alábbi példában a szabványos konfiguráció látható.

### A fájl létrehozása

```bash
touch ~/.ssh/config
```

### A fájl szerkesztése az új SSH-konfiguráció hozzáadásához:

```bash
vim ~/.ssh/config
```

### `~/.ssh/config` példafájl:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Ez az SSH-konfiguráció szakaszokat ad hozzá mindegyik szolgáltatáshoz, hogy mindegyik saját dedikált kulcspárral rendelkezzen. Az alapértelmezett beállítások a fenti csoportoknak nem megfelelő olyan állomásokra vonatkoznak, amelyekre bejelentkezett.


### A konfigurációs fájl ismertetése

`Host` = a terminálon hívott állomás neve.  `ssh fedora22` a `Host fedora22` címkéjű beállításblokkban található értékek használatára szólítja fel az `SSH`-t. MEGJEGYZÉS: Ez a bármilyen olyan címke lehet, amely logikus a használatra vonatkozóan, és egyetlen kiszolgáló tényleges állomásnevét sem képviseli.

`Hostname 102.160.203.241` = azon kiszolgáló IP-címe vagy DNS-neve, amelyre bejelentkezik. A cím a kiszolgálóra való irányításra szolgál, és lehet egy belső IP-címre leképezett külső IP-cím.

`User git` = a használni kívánt távoli felhasználói fiók az Azure virtuális gépbe való bejelentkezéskor.

`PubKeyAuthentication yes` = közli az SSH-val, hogy SSH-kulcsot kíván használni a bejelentkezéshez.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = közli az SSH-val, hogy mely kulcspárt mutassa be a kiszolgálónak a bejelentkezés hitelesítéséhez.


## SSH-ból Linuxba jelszó nélkül

Most, hogy rendelkezik SSH-kulcspárral és konfigurált SSH konfigurációs fájllal, gyorsan és biztonságosan jelentkezhet be a Linux virtuális gépre. Amikor először jelentkezik be egy kiszolgálóra SSH-kulccsal, a parancs felszólítja a kulcs jelszavának a megadására.

```bash
ssh fedora22
```

### A parancs ismertetése

Az `ssh fedora22` futtatásakor az SSH először megkeresi és betölti a `Host fedora22` blokk beállításait, majd betölti az összes többi beállítást az utolsó `Host *` blokkból.

## További lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával.  A bejelentkezéshez nyilvános SSH-kulccsal létrehozott Azure virtuális gépek biztonságosabbak, mint az alapértelmezett bejelentkezési módszer jelszavaival létrehozottak.  A rendszer alapértelmezés szerint úgy konfigurálja a bejelentkezéshez SSH-kulcsokat használó Azure virtuális gépeket, hogy letiltsa a jelszavas bejelentkezéseket, így elkerülje a találgatásos támadáson alapuló betörési kísérleteket.

- [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Biztonságos Linux virtuális gép létrehozása az Azure portál használatával](virtual-machines-linux-quick-create-portal.md)
- [Biztonságos Linux virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-quick-create-cli.md)



<!--HONumber=Jun16_HO2--->


