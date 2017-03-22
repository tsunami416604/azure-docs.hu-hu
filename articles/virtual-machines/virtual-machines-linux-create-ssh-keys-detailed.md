---
title: "SSH kulcspárok létrehozásának részletes lépései Linux rendszerű virtuális gépekhez az Azure-on | Microsoft Docs"
description: "Ismerje meg a nyilvános és titkos SSH-kulcspárok létrehozásának a további lépéseit az Azure-ban futó Linux rendszerű virtuális gépek esetén a különböző használati esetekhez tartozó konkrét tanúsítványokkal együtt."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 950773dff7f5d5ed6cd1cb873184b00345348e45
ms.lasthandoff: 03/10/2017


---

# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Részletes útmutató egy SSH-kulcspár és további tanúsítványok Linux virtuális géphez való létrehozásához az Azure-ban
Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, aminek köszönhetően nincs szükség jelszavakra a bejelentkezéshez. A jelszavak kitalálhatók, és szüntelen találgatásos támadási kísérleteknek teszik ki a virtuális gépet a jelszó kiderítése céljából. Az Azure CLI vagy a Resource Manager-sablonok használatával létrehozott virtuális gépek az üzembe helyezés részeként tartalmazhatják a nyilvános SSH-kulcsot, így nincs szükség az SSH-hoz a jelszavas belépést letiltó, üzembe helyezés utáni konfigurálási lépésre. A cikk részletesen, több példával bemutatja a például a Klasszikus portálhoz használható tanúsítványok létrehozásának lépéseit. Ha gyorsan szeretne létrehozni és használni egy SSH-kulcspárt, tekintse meg a következő szakaszt: [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](virtual-machines-linux-mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Az SSH-kulcsok bemutatása

A nyilvános és titkos SSH-kulcsok használata biztosítja a legegyszerűbb módot a Linux-kiszolgálókra való bejelentkezéshez. A [nyíltkulcsú titkosítás](https://en.wikipedia.org/wiki/Public-key_cryptography) a jelszavak használatánál egy sokkal biztonságosabb módszert is lehetővé tesz a Linux vagy BSD virtuális gépekre való bejelentkezéshez az Azure-ban, hiszen a jelszavak sokkal könnyebben törhetők fel találgatásos módszeren alapuló támadással.

A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal.  A titkos SSH-kulcsnak [nagyon biztonságos jelszóval](https://www.xkcd.com/936/) kell rendelkeznie (forrás: [xkcd.com](https://xkcd.com)) a védelme érdekében.  Ez a jelszó csak a titkos SSH-kulcs elérésére szolgál, **nem** pedig a felhasználói fiók jelszava.  Amikor jelszót ad hozzá az SSH-kulcshoz, az a 128 bites AES segítségével titkosítja a titkos kulcsot, így a titkos kulcs használhatatlan az azt visszafejtő jelszó nélkül.  Ha egy támadó ellopná a titkos kulcsot, és azt nem védené jelszó, a támadó a titkos kulccsal bejelentkezhetne azon kiszolgálókra, amelyeken a megfelelő nyilvános kulcs lett telepítve.  Ha egy titkos kulcsot jelszó véd, a kulcsot nem használhatja a támadó, így ez további biztonsági réteget nyújt az Azure-beli infrastruktúra számára.

Ez a cikk egy SSH-protokoll 2. verziójára épülő nyilvános és titkos RSA-kulcspárt (más néven „ssh-rsa” kulcsokat) hoz létre. Ez javasolt az Azure Resource Managerrel való üzembe helyezésekhez. Az *ssh-rsa* kulcsokra van szükség a [portálon](https://portal.azure.com) a klasszikus és a Resource Managerben üzemelő példányokhoz is.

## <a name="ssh-keys-use-and-benefits"></a>Az SSH-kulcsok használata és a használat előnyei

Az Azure legalább 2048 bites, az SSH-protokoll 2. verzióját használó, RSA formátumú nyilvános és titkos kulcsokat követel meg, a nyilvános kulcsnak `.pub` tárolóformátumúnak kell lennie. (A klasszikus portál a `.pem` fájlformátumot használja. A kulcsok létrehozásához az `ssh-keygen` parancsot használja, amely kérdések sorát teszi fel, majd létrehoz egy titkos kulcsot és egy megfelelő nyilvános kulcsot. Egy Azure virtuális gép létrehozásakor az Azure a virtuális gép `~/.ssh/authorized_keys` mappájába másolja a nyilvános kulcsot. A rendszer a `~/.ssh/authorized_keys` mappában lévő SSH-kulcsokat használja az ügyfél ellenőrzésére, hogy egyezik-e a megfelelő titkos kulcs egy SSH-bejelentkezési kapcsolat esetén.  Ha az Azure Linux VM létrehozása során SSH-kulcsokat használt a hitelesítéshez, az Azure úgy konfigurálja az SSHD-kiszolgálót, hogy ne engedélyezze a jelszavas bejelentkezéseket, csak az SSH-kulcsokat.  Így az Azure Linux VM-ek SSH-kulcsokkal való létrehozása által biztonságosan telepítheti a VM-et, és megkímélheti magát az általános, üzembe helyezés utáni konfigurációs lépéstől, amely letiltja a jelszavakat az **sshd_config** konfigurációs fájlban.

## <a name="using-ssh-keygen"></a>Az ssh-keygen használata

Ez a parancs jelszóval védett (titkosított) SSH-kulcspárt hoz létre 2048 bites RSA használatával, és az egyszerű azonosítás érdekében megjegyzéssel van ellátva.  

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*A parancs ismertetése*

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-t rsa` = a létrehozni kívánt kulcs típusa, amely az RSA formátum [wikipedia] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = a kulcs bitjeinek száma,

`-C "azureuser@myserver"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében.  Általában e-mailt használnak megjegyzésként, de használhat bármit, ami a leginkább megfelel az infrastruktúra szempontjából.

## <a name="classic-portal-and-x509-certs"></a>A klasszikus portál és az X.509-tanúsítványok

Ha a [klasszikus Azure portált](https://manage.windowsazure.com/) használja, az SSH-kulcsokhoz szüksége lesz az X.509-tanúsítvány .pem fájlra.  Más típusú nyilvános SSH-kulcsok nem engedélyezettek, az X.509-tanúsítványokat *kell* használnia.

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

Ha a klasszikus üzembe helyezési modellt használja (`asm` mód a parancssori felületen), használhatja a nyilvános SSH-RSA-kulcsot, vagy egy pem-tárolóban lévő RFC4716 formátumú kulcsot.  A nyilvános SSH-RSA-kulcsot a cikk során korábban hoztuk létre az `ssh-keygen` használatával.

RFC4716 formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Ssh-keygen – példa

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
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

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

A cikkben használt kulcspár neve.  Az **id_rsa** nevű kulcspár az alapértelmezett, és mivel egyes eszközök az **id_rsa** nevű titkos kulcsfájlt várhatják, célszerű ezt a nevet adni a kulcspárnak. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye.  Ha nincs megadva a teljes elérési útvonal, az `ssh-keygen` az aktuális munkakönyvtárban hozza létre a kulcsokat, és nem az alapértelmezett `~/.ssh` könyvtárban.

A `~/.ssh` könyvtár listázása.

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Kulcs jelszava:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` a titkos kulcsfájl jelszavára „hozzáférési kódként” hivatkozik.  *Erősen* ajánlott jelszót hozzáadni a titkos kulcshoz. A kulcsfájlt védő jelszó nélkül bárki bejelentkezhet a fájllal bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. A jelszó hozzáadása sokkal nagyobb védelmet nyújt arra az esetre, ha valaki megszerezné a titkos kulcsot, mert így lesz ideje módosítani a hitelesítéséhez használt kulcsokat.

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

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>`ssh-copy-id` használata egy meglévő virtuális gép másolására.
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
