---
title: Részletes utasítások - SSH kulcs pár Azure Linux virtuális gépek |} Microsoft Docs
description: Ismerje meg a részletes lépéseket létrehozásához és kezeléséhez az SSH nyilvános és titkos kulcsból álló kulcspárt Linux virtuális gépek Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Részletes lépései: létrehozása és kezelése a Linux virtuális Gépet az Azure-ban hitelesítéséhez SSH-kulcsok 
A secure shell (SSH) kulcspár hozzon létre egy Linux virtuális gép a használatukkal nincs szükség a bejelentkezési jelszót, amely alapértelmezés szerint az SSH-kulcsok használata a hitelesítéshez, Azure. Virtuális gépek létrehozása és az Azure portál, az Azure parancssori felület Resource Manager sablonok, vagy más eszközök lehetnek a nyilvános SSH-kulcsot a központi telepítés, amely állítja be az SSH-kapcsolatokhoz SSH hitelesítés részeként. 

Ez a cikk ismerteti a részletes háttér és létrehozása és egy SSH-RSA nyilvános-titkos kulcsot tartalmazó fájlt pár SSH-ügyfélkapcsolatok kezelésére. Ha azt szeretné, hogy a gyors parancsok, lásd: [az SSH nyilvános-titkos kulcsból álló kulcspárt létrehozása Linux virtuális gépek Azure-ban](mac-create-ssh-keys.md).

További lehetőségek hozhat létre és használhat SSH-kulcsok Windows rendszerű számítógépeken, lásd: [SSH használata a Windows Azure-kulcsok](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Titkos kulcs jelszava
A titkos SSH-kulcsot kell rendelkeznie a annak védelme rendkívül biztonságos jelszó szükséges. Ezt a jelszót csak a titkos SSH-kulcsfájl eléréséhez és *nem* a felhasználói fiók jelszavát. Amikor egy hozzáférési kódot ad hozzá az SSH-kulcsot, az titkosítja a titkos kulcsot 128 bites AES titkosítással, úgy, hogy a titkos kulcs nélkül a hozzáférési kód használhatatlan visszafejteni. Ha egy támadó ellopott a titkos kulcsot, és a kulcs nem volt egy hozzáférési kódot, azok lenne titkos kulcs használatával jelentkezzen be a megfelelő nyilvános kulccsal rendelkező kiszolgálók számára. Ha egy titkos kulcsot egy hozzáférési kódot által védett, nem használható támadó, egy további biztonsági réteget biztosítva az Azure-beli infrastruktúra.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Az SSH-kulcsok használata és a használat előnyei

Egy Azure virtuális gép létrehozásakor a nyilvános kulcs megadásával Azure másolja át a nyilvános kulcsot (a a `.pub` formátum) számára a `~/.ssh/authorized_keys` mappa a virtuális Gépen. A rendszer a `~/.ssh/authorized_keys` mappában lévő SSH-kulcsokat használja az ügyfél ellenőrzésére, hogy egyezik-e a megfelelő titkos kulcs egy SSH-bejelentkezési kapcsolat esetén. Az Azure Linux virtuális gép SSH-kulcsokat használ, az Azure a SSHD kiszolgáló konfigurálása nem engedélyezi a jelszavas bejelentkezéseket, csak az SSH-kulcsok. Ezért az Azure Linux virtuális gép SSH-kulcsokat készít, segíthet a virtuális gép üzembe és takarítson meg a szokásos telepítés utáni konfigurációs lépést, a jelszavak letiltása a `sshd_config` fájlt.

Ha nem szeretne SSH-kulcsok használata, állíthatja be a Linux virtuális gép jelszavas hitelesítés használatára. Ha a virtuális gép nem kapcsolódik az internethez, elegendő jelszóval lehet. Azonban továbbra is szeretné az egyes Linux virtuális gépek a jelszavak kezelése és karbantartása kifogástalan jelszó házirendeket és gyakorlatokat, például a jelszó minimális hossza és rendszeres frissítéseket. SSH-kulcsok használata révén egyszerűbben egyéni hitelesítő adatok kezelése több virtuális gépek között.

## <a name="generate-keys-with-ssh-keygen"></a>Az ssh-keygen kulcsok létrehozása

A kulcsok létrehozásához egy előnyben részesített parancs: `ssh-keygen`, OpenSSH segédprogramok, az Azure-felhő rendszerhéj, macOS vagy Linux gazdagép elérhető a [Linux rendszerhez készült Windows alrendszer](https://docs.microsoft.com/windows/wsl/about), és egyéb eszközöket. `ssh-keygen` a kérdések sorát, és ezután ír egy titkos kulcsot és egy megfelelő nyilvános kulcsot. 

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.

### <a name="basic-example"></a>Alapszintű – példa

A következő `ssh-keygen` parancs hoz létre 2048 bites SSH-RSA nyilvános és titkos kulcs fájlok alapértelmezés szerint a `~/.ssh` könyvtár. Ha egy SSH-kulcspárral megtalálható az aktuális helyen, a rendszer felülírja ezeket a fájlokat.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Részletes példa
A következő példa egy SSH-RSA kulcspár létrehozása további parancs lehetőségeket mutatja be. Ha egy SSH-kulcspárral megtalálható az aktuális helyen, a rendszer felülírja ezeket a fájlokat. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**A parancs ismertetése**

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-t rsa` Ebben az esetben az RSA formátum létrehozni kívánt kulcs típusa =

`-b 4096` Ebben az esetben 4096 bit, kulcs =

`-C "azureuser@myserver"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében. Általában az az e-mail címet használnak megjegyzésként, de függetlenül működik a legjobban a infrastruktúra használata.

`-f ~/.ssh/mykeys/myprivatekey` = a titkos kulcsfájl nevét, ha úgy dönt, hogy nem használja az alapértelmezett nevet. A megfelelő nyilvános kulcs fájlját hozzáíródik `.pub` ugyanabban a könyvtárban jön létre. A könyvtár léteznie kell.

`-N mypassphrase` = egy további, a titkos kulcsfájl eléréséhez használt jelszót. 

### <a name="example-of-ssh-keygen"></a>Ssh-keygen – példa

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

#### <a name="saved-key-files"></a>Mentett kulcsfájlok

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

A cikkben használt kulcspár neve. Nevű kulcspár `id_rsa` az alapértelmezett; egyes eszközök a `id_rsa` titkos kulcsfájl nevét, így több érdemes. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye. Ha nincs megadva a teljes elérési útvonal, az `ssh-keygen` az aktuális munkakönyvtárban hozza létre a kulcsokat, és nem az alapértelmezett `~/.ssh` könyvtárban.

#### <a name="list-of-the-ssh-directory"></a>A listában a `~/.ssh` könyvtár

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Kulcs jelszava

`Enter passphrase (empty for no passphrase):`

Az *erősen* javasoljuk, hogy egy hozzáférési kódot adja hozzá a titkos kulcsot. A kulcs fájljának védelmére anélkül bárki, aki a fájl segítségével jelentkezzen be a kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. Hozzáadása egy hozzáférési kódot további védelmet nyújt, ha valaki hozzáfér a titkos kulcs fájlja számára jogosultságot ad a kulcsok módosítása időt.

## <a name="generate-keys-automatically-during-deployment"></a>Központi telepítése során automatikusan kulcsok létrehozása

Ha használja a [Azure CLI 2.0](/cli/azure) a virtuális gép létrehozásához szükség létrehozhat SSH nyilvános és titkos kulcs fájlok futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--generate-ssh-keys` lehetőséget. A kulcsok ~/.ssh könyvtárban tárolják. Vegye figyelembe, hogy az Ez a kapcsoló nem írja felül a kulcsok, ha már léteznek az adott helyre.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>A virtuális gép telepítésekor adja meg a nyilvános SSH-kulcs

Hozzon létre egy Linux virtuális gép SSH-kulcsokat használ, adja meg a nyilvános SSH-kulcsot az Azure-portált használja, CLI, virtuális gép létrehozásakor Resource Manager-sablonok vagy más módszerrel. A portál használata esetén meg kell adnia a nyilvános kulcs magát. Használatakor a [Azure CLI 2.0](/cli/azure) a virtuális gép egy meglévő nyilvános kulcs létrehozásához adja meg az értéket, vagy a hely a nyilvános kulcs futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--ssh-key-value` lehetőséget. 

Ha még nem ismeri a nyilvános SSH-kulcs formátuma, megjelenik a nyilvános kulcs futtatásával `cat` következőképpen, cseréje `~/.ssh/id_rsa.pub` a saját nyilvánoskulcs-fájl helye:

```bash
cat ~/.ssh/id_rsa.pub
```

Kimeneti rendszer (Itt kivont) a következőhöz hasonló:

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Ha másolja és illessze be a nyilvánoskulcs-fájl tartalmát az Azure-portálon vagy a Resource Manager-sablon, győződjön meg arról, nem minden további szóköz másolja, vagy további linebreaks bevezetése. Például macOS használatakor átadható a nyilvános kulcs fájlját (alapértelmezés szerint `~/.ssh/id_rsa.pub`) való **pbcopy** tartalmának (nincsenek más Linux programok, amelyek az ugyanaz, mint például **xclip**).

Ha inkább egy többsoros formátumú nyilvános kulcsot használ, egy RFC4716 formázott kulcs nyilvános kulcsból, korábban létrehozott pem tároló hozhat létre.

RFC4716 formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH-kapcsolatot a virtuális gép SSH-ügyféllel
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi számítógépen, a virtuális géphez az IP-cím vagy a DNS-neve, a virtuális gép SSH. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a rendszergazdai felhasználónevet és a teljes tartománynév (vagy IP-címet) az alábbi parancs:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során megadott hozzáférési kódot, adja meg a hozzáférési kódot, mikor a bejelentkezési folyamat során a rendszer kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Ssh-ügynök használatával tárolja a titkos kulcs jelszava

Ha nem szeretné beírni a titkos kulcsfájl jelszavát minden SSH-bejelentkezéskor, használhatja a `ssh-agent` gyorsítótárazhat-e a titkos kulcsfájl jelszavát. Ha Mac használ, a macOS kulcslánc biztonságosan tárolja a titkos kulcs jelszava, indításakor `ssh-agent`.

Győződjön meg arról, és használjon `ssh-agent` és `ssh-add` az SSH-rendszer tájékoztatni a kulcsfontosságú fájlokat, így nem kell jelszót interaktív használata.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal.

```bash
ssh-add ~/.ssh/id_rsa
```

A titkos kulcs jelszava most tárolódik `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Ssh--azonosítót használja kulcsának az átmásolása egy meglévő virtuális gépre
Ha már létrehozott egy virtuális Gépet, a Linux virtuális Gépet a következő paranccsal telepítheti az új nyilvános SSH-kulcs:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH konfigurációs fájl létrehozása és konfigurálása

Hozhat létre és konfigurálja az SSH konfigurációs fájl (`~/.ssh/config`) napló-modulok felgyorsítása érdekében, és az SSH-ügyfél módot optimalizálása érdekében. 

Az alábbi példában egy egyszerű konfiguráció látható, hogy segítségével gyorsan jelentkezzen be egy felhasználó egy adott virtuális géphez az alapértelmezett titkos SSH-kulcs használatával. 

### <a name="create-the-file"></a>A fájl létrehozása

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>A fájlban adja hozzá az új SSH-konfigurációja

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Példa konfiguráció

Adja hozzá a virtuális gazdagépe megfelelő konfigurációs beállításokat.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

További állomásokat, hogy mindegyik saját dedikált kulcspárral használandó konfigurációi is hozzáadhat. Lásd: [SSH konfigurációs fájl](https://www.ssh.com/ssh/config/) további speciális konfigurációs beállítások.

Most, hogy rendelkezik SSH-kulcspárral és konfigurált SSH konfigurációs fájllal, gyorsan és biztonságosan jelentkezhet be a Linux virtuális gépre. A következő parancs futtatásakor SSH megkeresi és betölti a beállításait a `Host myvm` az SSH konfigurációs fájl letiltása.

```bash
ssh myvm
```

Az első alkalommal jelentkezik be egy kiszolgálóra SSH-kulcsot, a parancssorok meg az adott kulcsfájl jelszavát.

## <a name="next-steps"></a>További lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával. A bejelentkezéshez nyilvános SSH-kulccsal létrehozott Azure virtuális gépek biztonságosabbak, mint az alapértelmezett bejelentkezési módszer jelszavaival létrehozott virtuális gépek.

* [Linux virtuális gép létrehozása az Azure portállal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása az Azure parancssori felülettel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
