---
title: "SSH kulcspárok létrehozása Linux rendszerű virtuális gépekhez az Azure-on | Microsoft Docs"
description: "Nyilvános és titkos SSH-kulcspárok biztonságos létrehozása Azure-beli Linux rendszerű virtuális gépekhez."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 6776fe5cf1faadfbcfc9bbd6c69ec1380c2429c7
ms.openlocfilehash: 67dce2c226f5ec5f396ef51cd59781d532d76795
ms.lasthandoff: 02/27/2017


---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>SSH nyilvános és titkos kulcspárok létrehozása Linux rendszerű virtuális gépekhez

Ez a cikk bemutatja, hogyan hozhat létre SSH nyilvános és titkos kulcspárt Linux rendszerű virtuális gépekkel való használathoz.  Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, aminek köszönhetően nincs szükség jelszavakra a bejelentkezéshez.  A jelszavak kitalálhatók, és szüntelen találgatásos támadási kísérleteknek teszik ki a virtuális gépet a jelszó kiderítése céljából. Az Azure-sablonokkal vagy az `azure-cli` használatával létrehozott virtuális gépek az üzembe helyezés részeként tartalmazhatják a nyilvános SSH-kulcsot, így nincs szükség az SSH-hoz a jelszavas belépést letiltó, üzembe helyezés utáni konfigurálási lépésre.

## <a name="quick-commands"></a>Gyors parancsok

Futtassa a következő parancsokat egy rendszerhéjból, és a példákat helyettesítse be az Önnek megfelelő értékekkel.

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.  A `-N` argumentum a titkos SSH-kulcs titkosítására szolgáló jelszót határozza meg, és *nem* a felhasználói jelszava.

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N mypassword
```

Adja az újonnan létrehozott kulcsot a következőhöz: `ssh-agent`.

```bash
ssh-add ~/.ssh/id_rsa
```

> [!NOTE] 
> A fenti parancsok szinte a Linux operációs rendszer szinte minden disztribúcióján használhatók, de nem feltétlenül működnek a tárolókban, ugyanis ez a környezet jelentősen korlátozott lehet.

## <a name="detailed-walkthrough"></a>Részletes bemutató

A nyilvános és titkos SSH-kulcsok használata biztosítja a legegyszerűbb módot a Linux-kiszolgálókra való bejelentkezéshez. A [nyíltkulcsú titkosítás](https://en.wikipedia.org/wiki/Public-key_cryptography) a jelszavak használatánál egy sokkal biztonságosabb módszert is lehetővé tesz a Linux vagy BSD virtuális gépekre való bejelentkezéshez az Azure-ban, hiszen a jelszavak sokkal könnyebben törhetők fel találgatásos módszeren alapuló támadással.

A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal.  A titkos SSH-kulcsnak [nagyon biztonságos jelszóval](https://www.xkcd.com/936/) kell rendelkeznie (forrás: [xkcd.com](https://xkcd.com)) a védelme érdekében.  Ez a jelszó csak a titkos SSH-kulcs elérésére szolgál, **nem** pedig a felhasználói fiók jelszava.  Amikor jelszót ad hozzá az SSH-kulcshoz, az a 128 bites AES segítségével titkosítja a titkos kulcsot, így a titkos kulcs használhatatlan az azt visszafejtő jelszó nélkül.  Ha egy támadó ellopná a titkos kulcsot, és azt nem védené jelszó, a támadó a titkos kulccsal bejelentkezhetne azon kiszolgálókra, amelyeken a megfelelő nyilvános kulcs lett telepítve.  Ha egy titkos kulcsot jelszó véd, a kulcsot nem használhatja a támadó, így ez további biztonsági réteget nyújt az Azure-beli infrastruktúra számára.

Ez a cikk az *ssh-rsa* formátumú kulcsfájlok létrehozását ismerteti, mivel ezek ajánlottak a Resource Managerben üzemelő példányok esetén.  Az *ssh-rsa* kulcsokra van szükség a [portálon](https://portal.azure.com) a klasszikus és a Resource Managerben üzemelő példányokhoz is.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>SSH-jelszavak letiltása SSH-kulcsokkal

Az Azure legalább 2048 bites ssh-rsa formátumú nyilvános és titkos kulcsokat követel meg. A kulcsok létrehozásához az `ssh-keygen` parancsot használja, amely kérdések sorát teszi fel, majd létrehoz egy titkos kulcsot és egy megfelelő nyilvános kulcsot. Egy Azure virtuális gép létrehozásakor a rendszer a `~/.ssh/authorized_keys` mappába másolja a nyilvános kulcsot.  A rendszer a `~/.ssh/authorized_keys` mappában lévő SSH-kulcsokat használja az ügyfél ellenőrzésére, hogy egyezik-e a megfelelő titkos kulcs egy SSH-bejelentkezési kapcsolat esetén.  Ha az Azure Linux VM létrehozása során SSH-kulcsokat használt a hitelesítéshez, az Azure úgy konfigurálja az SSHD-kiszolgálót, hogy ne engedélyezze a jelszavas bejelentkezéseket, csak az SSH-kulcsokat.  Így az Azure Linux VM-ek SSH-kulcsokkal való létrehozása által biztonságosan telepítheti a VM-et, és megkímélheti magát az általános, üzembe helyezés utáni konfigurációs lépéstől, amely letiltja a jelszavakat az sshd_config konfigurációs fájlban.

## <a name="using-ssh-keygen"></a>Az ssh-keygen használata

Ez a parancs jelszóval védett (titkosított) SSH-kulcspárt hoz létre 2048 bites RSA használatával, és az egyszerű azonosítás érdekében megjegyzéssel van ellátva.  

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.

```bash
ssh-keygen \
-t rsa \
-b 2048 \
-C "ahmet@myserver" \
-f ~/.ssh/id_rsa \
-N mypassword
```

*A parancs ismertetése*

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-t rsa` = a létrehozni kívánt kulcs típusa, amely az RSA formátum [wikipedia] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = a kulcs bitjeinek száma,

`-C "myusername@myserver"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében.  Általában e-mailt használnak megjegyzésként, de használhat bármit, ami a leginkább megfelel az infrastruktúra szempontjából.

## <a name="classic-portal-and-x509-certs"></a>A klasszikus portál és az X.509-tanúsítványok

Ha a [klasszikus Azure-portált](https://manage.windowsazure.com/) használja, az SSH-kulcsokhoz szükséges lesz az X.509-tanúsítványokra.  Más típusú nyilvános SSH-kulcsok nem engedélyezettek, az X.509-tanúsítványokat *kell* használnia.

Következőképpen hozhat létre egy X.509-tanúsítványt a meglévő titkos SSH-RSA-kulcsból:

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>Klasszikus üzembe helyezés az `asm` használatával

Ha a klasszikus üzembe helyezési modellt használja (Azure service management CLI`asm`), használhatja a nyilvános SSH-RSA-kulcsot, vagy egy pem-tárolóban lévő RFC4716 formátumú kulcsot.  A nyilvános SSH-RSA-kulcsot a cikk során korábban hoztuk létre az `ssh-keygen` használatával.

RFC4716 formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Ssh-keygen – példa

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

A cikkben használt kulcspár neve.  Az **id_rsa** nevű kulcspár az alapértelmezett, és mivel egyes eszközök az **id_rsa** nevű titkos kulcsfájlt várhatják, célszerű ezt a nevet adni a kulcspárnak. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye.  Ha nincs megadva a teljes elérési útvonal, az `ssh-keygen` az aktuális munkakönyvtárban hozza létre a kulcsokat és nem az alapértelmezett `~/.ssh` könyvtárban.

A `~/.ssh` könyvtár listázása.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Kulcs jelszava:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` a jelszóra „hozzáférési kódként” hivatkozik.  *Erősen* ajánlott jelszót hozzáadni a kulcspárokhoz. A kulcspárt védő jelszó nélkül bárki bejelentkezhet a titkos kulcsfájllal bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. A jelszó hozzáadása sokkal nagyobb védelmet nyújt arra az esetre, ha valaki megszerezné a titkos kulcsot, mert így lesz ideje módosítani a hitelesítéséhez használt kulcsokat.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Az ssh-agent használata a titkos kulcs jelszavának tárolásához

Ha nem szeretné beírni a titkos kulcsfájl jelszavát minden SSH-bejelentkezéskor, az `ssh-agent` segítségével gyorsítótárazhatja a titkos kulcsfájl jelszavát. Ha Mac gépet használ, az OSX Kulcskarika biztonságos módon menti a titkos kulcsok jelszavát az `ssh-agent` indításakor.

Ellenőrizze és használja az ssh-agent és az ssh-add parancsokat az SSH-rendszer kulcsfájlokról való informálásához, így nem kell a jelszót interaktív módon használnia.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal.

```bash
ssh-add ~/.ssh/id_rsa
```

A titkos kulcs jelszava ekkor az `ssh-agent` ügynökben lesz tárolva.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Az új kulcs telepítése az `ssh-copy-id` használatával
Ha már létrehozott egy virtuális gépet, a következővel telepítheti az új nyilvános SSH-kulcsot a Linux rendszerű virtuális gépére:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH konfigurációs fájl létrehozása és konfigurálása

Ajánlott létrehozni és konfigurálni egy `~/.ssh/config` fájlt a bejelentkezések felgyorsításához és az SSH-ügynök működésének optimalizálásához.

Az alábbi példában a szabványos konfiguráció látható.

### <a name="create-the-file"></a>A fájl létrehozása

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>A fájl szerkesztése az új SSH-konfiguráció hozzáadásához:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>`~/.ssh/config` példafájl:

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

### <a name="config-file-explained"></a>A konfigurációs fájl ismertetése

`Host` = a terminálon hívott állomás neve.  `ssh fedora22` a `Host fedora22` címkéjű beállításblokkban található értékek használatára szólítja fel az `SSH`-t. MEGJEGYZÉS: Gazdagép bármilyen olyan címke lehet, amely logikus a használatra vonatkozóan, és egyetlen kiszolgáló tényleges állomásnevét sem képviseli.

`Hostname 102.160.203.241` = azon kiszolgáló IP-címe vagy DNS-neve, amelyhez hozzáfér.

`User ahmet` = a használandó távoli felhasználói fiók a kiszolgálóra való bejelentkezéskor.

`PubKeyAuthentication yes` = közli az SSH-val, hogy SSH-kulcsot kíván használni a bejelentkezéshez.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = a titkos SSH-kulcs és a hitelesítéshez használt megfelelő nyilvános kulcs.

## <a name="ssh-into-linux-without-a-password"></a>SSH-ból Linuxba jelszó nélkül

Most, hogy rendelkezik SSH-kulcspárral és konfigurált SSH konfigurációs fájllal, gyorsan és biztonságosan jelentkezhet be a Linux virtuális gépre. Amikor először jelentkezik be egy kiszolgálóra SSH-kulccsal, a parancs felszólítja a kulcs jelszavának megadására.

```bash
ssh fedora22
```

### <a name="command-explained"></a>A parancs ismertetése

Az `ssh fedora22` futtatásakor az SSH először megkeresi és betölti a `Host fedora22` blokk beállításait, majd betölti az összes többi beállítást az utolsó `Host *` blokkból.

## <a name="next-steps"></a>Következő lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával.  A bejelentkezéshez nyilvános SSH-kulccsal létrehozott Azure virtuális gépek biztonságosabbak, mint az alapértelmezett bejelentkezési módszer jelszavaival létrehozott virtuális gépek.  Az SSH-kulcsokkal létrehozott Azure virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami megakadályozza a találgatásos támadásokat.

* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Biztonságos Linux virtuális gép létrehozása az Azure Portal használatával](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Biztonságos Linux virtuális gép létrehozása az Azure parancssori felülettel](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

