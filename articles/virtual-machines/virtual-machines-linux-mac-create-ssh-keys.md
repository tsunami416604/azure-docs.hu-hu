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
    ms.date="10/06/2016"
    ms.author="v-livech"/>


# SSH-kulcsok létrehozása Linux és Mac rendszerben Linux virtuális gépek számára az Azure-ban

Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, aminek köszönhetően nincs szükség jelszavakra a bejelentkezéshez.  A jelszavak kitalálhatók, és szüntelen találgatásos támadási kísérleteknek teszik ki a virtuális gépet a jelszó kiderítése céljából. Az Azure-sablonokkal vagy az `azure-cli` használatával létrehozott virtuális gépek az üzembe helyezés részeként tartalmazhatják a nyilvános SSH-kulcsot, így nincs szükség az üzembe helyezés utáni konfigurálásra.  Ha Linux rendszerű virtuális géphez csatlakozik a Windowsból, tekintse meg [ezt a dokumentumot](virtual-machines-linux-ssh-from-windows.md).

## Gyors parancslista

Az alábbi parancspéldákban cserélje a &lt; és &gt; közötti értékeket a saját környezet értékeire.  Kezdje a könyvtárak módosításával, `cd ~/.ssh/` hogy minden ssh-kulcs abban a könyvtárban legyen létrehozva.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Adja meg a `~/.ssh/` könyvtárba mentett fájl nevét:

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

A nyilvános és titkos SSH-kulcsok használata biztosítja a legegyszerűbb módot a Linux-kiszolgálókra való bejelentkezéshez. A [nyíltkulcsú titkosítás](https://en.wikipedia.org/wiki/Public-key_cryptography) a jelszavak használatánál egy sokkal biztonságosabb módszert is lehetővé tesz a Linux vagy BSD virtuális gépekre való bejelentkezéshez az Azure-ban, hiszen a jelszavak sokkal könnyebben törhetők fel találgatásos módszeren alapuló támadással. A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal.  A titkos SSH-kulcsnak [nagyon biztonságos jelszóval](https://www.xkcd.com/936/) kell rendelkeznie (forrás: [xkcd.com](https://xkcd.com)) a védelme érdekében.  Ez a jelszó csak a titkos SSH-kulcs elérésére szolgál, **nem** pedig a felhasználói fiók jelszava.  Amikor jelszót ad hozzá az SSH-kulcshoz, az titkosítja a titkos kulcsot, így a titkos kulcs használhatatlan az azt feloldó jelszó nélkül.  Ha egy támadó ellopná a titkos kulcsot, és azt nem védené jelszó, a támadó a titkos kulccsal bejelentkezhetne azon kiszolgálókra, amelyeken a megfelelő nyilvános kulcs lett telepítve.  Ha egy titkos kulcsot jelszó véd, a kulcsot nem használhatja a támadó, így ez további biztonsági réteget nyújt az Azure-beli infrastruktúra számára.



Ez a cikk az *ssh-rsa* formátumú kulcsfájlok létrehozását ismerteti, mivel ezek ajánlottak a Resource Managerben üzemelő példányok esetén.  Az *ssh-rsa* kulcsokra van szükség a [portálon](https://portal.azure.com) a klasszikus és Resource Managerben üzemelő példányokhoz is.


## Az SSH-kulcsok létrehozása

Az Azure legalább 2048 bites ssh-rsa formátumú nyilvános és titkos kulcsokat követel meg. A kulcsok létrehozásához az `ssh-keygen` parancsot használja, amely kérdések sorát teszi fel, majd létrehoz egy titkos kulcsot és egy megfelelő nyilvános kulcsot. Egy Azure virtuális gép létrehozásakor a rendszer a `~/.ssh/authorized_keys` mappába másolja a nyilvános kulcsot.  A rendszer a `~/.ssh/authorized_keys` mappában lévő SSH-kulcsokat használja az ügyfél ellenőrzésére, hogy egyezik-e a megfelelő titkos kulcs egy SSH-bejelentkezési kapcsolat esetén.


## Az ssh-keygen használata

Ez a parancs jelszóval védett (titkosított) SSH-kulcspárt hoz létre 2048 bites RSA használatával, és az egyszerű azonosítás érdekében megjegyzéssel van ellátva.  Kezdje a könyvtárak váltásával, `cd ~/.ssh/` hogy minden ssh-kulcs abban a könyvtárban legyen létrehozva.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_A parancs ismertetése_

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-t rsa` = a létrehozni kívánt kulcs típusa, amely az [RSA formátum] (https://en.wikipedia.org/wiki/RSA_(cryptosystem),

`-b 2048` = a kulcs bitjeinek száma,

`-C "ahmet@fedoraVMAzure"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében.  Általában e-mailt használnak megjegyzésként, de használhat bármit, ami a leginkább megfelel az infrastruktúra szempontjából.

### PEM-kulcsok használata

Ha a klasszikus üzembe helyezési modellt használja (a klasszikus Azure portált vagy az Azure szolgáltatásfelügyeleti CLI-t `asm`), előfordulhat, hogy PEM-formátumú SSH-kulcsokat kell használnia a Linux rendszerű virtuális gépek eléréséhez.  Az alábbiakban láthatja, hogyan hozhat létre PEM-kulcsot egy meglévő nyilvános SSH-kulcsból és egy meglévő x509-tanúsítványból.

PEM-formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## Az ssh-keygen bemutatása

Az alábbiakban az egyes lépesek részletes ismertetését olvashatja.  Elsőként váltson a `~/.ssh` könyvtárra, majd futtassa a következőt: `ssh-keygen`.

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

A cikkben használt kulcspár neve.  Az **id_rsa** nevű kulcspár az alapértelmezett, és mivel egyes eszközök az **id_rsa** nevű titkos kulcsfájlt várhatják, célszerű ezt a nevet adni a kulcspárnak. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye.

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
A `~/.ssh` könyvtár listázása. `ssh-keygen` létrehozza a `~/.ssh` könyvtárat, ha az még nem létezik, és beállítja a megfelelő tulajdonjogokat és fájlmódokat.

Kulcs jelszava:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` a jelszóra „hozzáférési kódként” hivatkozik.  *Erősen* ajánlott jelszót hozzáadni a kulcspárokhoz. A kulcspárt védő jelszó nélkül bárki bejelentkezhet a titkos kulcsfájllal bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. A jelszó hozzáadása sokkal nagyobb védelmet nyújt arra az esetre, ha valaki megszerezné a titkos kulcsot, mert így lesz ideje módosítani a hitelesítéséhez használt kulcsokat.

## Az ssh-agent használata a titkos kulcs jelszavának tárolásához

Ha nem szeretné beírni a titkos kulcsfájl jelszavát minden SSH-bejelentkezéskor, az `ssh-agent` segítségével gyorsítótárazhatja a titkos kulcsfájl jelszavát. Ha Mac gépet használ, az OSX Kulcskarika biztonságos módon menti a titkos kulcsok jelszavát az `ssh-agent` indításakor.

Először ellenőrizze, hogy az `ssh-agent` fut-e.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

A titkos kulcs jelszava ekkor az `ssh-agent` ügynökben lesz tárolva.

## SSH konfigurációs fájl létrehozása és konfigurálása

Ajánlott létrehozni és konfigurálni egy `~/.ssh/config` fájlt a bejelentkezések felgyorsításához és az SSH-ügynök működésének optimalizálásához.

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
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Ez az SSH-konfiguráció szakaszokat ad hozzá mindegyik kiszolgálóhoz, hogy mindegyik saját dedikált kulcspárral rendelkezzen. Az alapértelmezett beállítások (`Host *`) a konfigurációs fájlban feljebb szereplő adott állomásoknak nem megfelelő állomásokra vonatkoznak.


### A konfigurációs fájl ismertetése

`Host` = a terminálon hívott állomás neve.  `ssh fedora22` a `Host fedora22` címkéjű beállításblokkban található értékek használatára szólítja fel az `SSH`-t. MEGJEGYZÉS: Ez a bármilyen olyan címke lehet, amely logikus a használatra vonatkozóan, és egyetlen kiszolgáló tényleges állomásnevét sem képviseli.

`Hostname 102.160.203.241` = azon kiszolgáló IP-címe vagy DNS-neve, amelyhez hozzáfér.

`User git` = a használni kívánt távoli felhasználói fiók.

`PubKeyAuthentication yes` = közli az SSH-val, hogy SSH-kulcsot kíván használni a bejelentkezéshez.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = a titkos SSH-kulcs és a hitelesítéshez használt megfelelő nyilvános kulcs.


## SSH-ból Linuxba jelszó nélkül

Most, hogy rendelkezik SSH-kulcspárral és konfigurált SSH konfigurációs fájllal, gyorsan és biztonságosan jelentkezhet be a Linux virtuális gépre. Amikor először jelentkezik be egy kiszolgálóra SSH-kulccsal, a parancs felszólítja a kulcs jelszavának megadására.

```bash
ssh fedora22
```

### A parancs ismertetése

Az `ssh fedora22` futtatásakor az SSH először megkeresi és betölti a `Host fedora22` blokk beállításait, majd betölti az összes többi beállítást az utolsó `Host *` blokkból.

## Következő lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával.  A bejelentkezéshez nyilvános SSH-kulccsal létrehozott Azure virtuális gépek biztonságosabbak, mint az alapértelmezett bejelentkezési módszer jelszavaival létrehozott virtuális gépek.  Az SSH-kulcsokkal létrehozott Azure virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami megakadályozza a találgatásos támadásokat.

- [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Biztonságos Linux virtuális gép létrehozása az Azure Portal használatával](virtual-machines-linux-quick-create-portal.md)
- [Biztonságos Linux virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-quick-create-cli.md)



<!--HONumber=Oct16_HO3-->


