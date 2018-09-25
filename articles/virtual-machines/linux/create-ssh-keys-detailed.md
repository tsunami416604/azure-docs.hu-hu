---
title: Részletes lépéseket – SSH kulcspárok Azure Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Ismerje meg, hozhat létre és kezelheti a nyilvános és titkos ssh-kulcs az Azure-ban Linux rendszerű virtuális gépek részletes lépéseket.
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
ms.openlocfilehash: 804b7c0ff31575e6d62497fd5166e1a38a273076
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965584"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Részletes lépések: létrehozása és kezelése az SSH-kulcsokat a hitelesítéshez a Linux rendszerű virtuális gép az Azure-ban 
Egy secure shell-(SSH) kulcspárt létrehozhat egy Linuxos virtuális gép az Azure-ban alapértelmezés szerint az SSH-kulcsokat használnak a hitelesítéshez, aminek köszönhetően nincs szükség jelszavakra a bejelentkezéshez. Az Azure Portallal, Azure CLI-vel, Resource Managerrel létrehozott virtuális gépek sablonokat, vagy más eszközök lehetnek a nyilvános SSH-kulcsot a központi telepítést, amely állítja be az SSH-kapcsolatok SSH hitelesítés részeként. 

Ez a cikk részletes háttérinformációkért és a lépések hozhat létre és kezelhet az SSH RSA nyilvános-titkos kulcspárt SSH-ügyfél kapcsolatokhoz. Ha azt szeretné, hogy a gyors parancsok, [SSH nyilvános-titkos kulcspárok létrehozása Linux rendszerű virtuális gépekhez az Azure-ban](mac-create-ssh-keys.md).

További lehetőségek a létrehozásához és a egy Windows-számítógép SSH-kulcsok használata, lásd: [az SSH használata a Windows Azure-beli kulcsok](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Titkos kulcs jelszava
Titkos SSH-kulcsnak rendelkeznie kell egy rendkívül biztonságos hozzáférési kódot a védelme érdekében. Csak a titkos SSH-kulcsfájl elérésére szolgál, akkor ezt a jelszót és *nem* a felhasználói fiók jelszavát. Amikor hozzáad egy hozzáférési kódot az SSH-kulccsal, hogy titkosítja a titkos kulcsot 128 bites AES-titkosítással, úgy, hogy a titkos kulcs használhatatlan az a jelszó nélkül visszafejteni. Ha egy támadó ellopná a titkos kulcsot, és a kulcs nem rendelkezett egy hozzáférési kódot, hogy tudná bejelentkezhetne azon kiszolgálókra, amelyeken a megfelelő nyilvános kulccsal jelentkezzen be. Ha egy titkos kulcsot jelszó védi, akkor a támadó, egy további biztonsági réteget biztosít az Azure-beli infrastruktúra számára nem használható.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Az SSH-kulcsok használata és a használat előnyei

Egy Azure virtuális gép létrehozásakor a nyilvános kulcs megadásával Azure másolja a nyilvános kulcsot (az a `.pub` formátumban), a `~/.ssh/authorized_keys` mappa a virtuális gépen. A rendszer a `~/.ssh/authorized_keys` mappában lévő SSH-kulcsokat használja az ügyfél ellenőrzésére, hogy egyezik-e a megfelelő titkos kulcs egy SSH-bejelentkezési kapcsolat esetén. Az Azure Linux virtuális gép által használt SSH-kulcsokat a hitelesítéshez az Azure úgy konfigurálja az SSHD-kiszolgálót, hogy ne engedélyezze a jelszavas bejelentkezéseket, csak az SSH-kulcsokat. Ezért SSH-kulcsokkal való létrehozása az Azure Linux VM, segíthet a virtuális gép üzembe helyezése és megkímélheti magát az általános üzembe helyezés utáni konfigurációs lépés letiltja a jelszavakat a `sshd_config` fájlt.

Ha nem szeretne SSH-kulcsok használata, állíthat be a Linux rendszerű virtuális gép jelszavas hitelesítés használatához. A virtuális gép nem lesz közzétéve az interneten, ha elegendő jelszavak használatával lehet. Azonban továbbra is szeretné a jelszavak kezelése minden egyes Linux virtuális gép és a megfelelő jelszót házirendeket és gyakorlatokat, például a jelszó minimális hossza és a rendszeres frissítések karbantartása. SSH-kulcsokat használnak az egyes hitelesítő adatok kezelése több virtuális gép között bonyolultságát is csökkenti.

## <a name="generate-keys-with-ssh-keygen"></a>Az ssh-keygen kulcsok létrehozása

A kulcsok létrehozásához, egy előnyben részesített parancs van `ssh-keygen`, érhető el az OpenSSH segédprogramok, az Azure Cloud Shellt, macOS vagy Linux-gazdagépre, amely a [a Linux Windows alrendszere](https://docs.microsoft.com/windows/wsl/about), és más eszközök. `ssh-keygen` kérdések sorát, és majd létrehoz egy titkos kulcsot és a egy megfelelő nyilvános kulcsot. 

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.

### <a name="basic-example"></a>Alapszintű példa

A következő `ssh-keygen` parancs hoz létre 2048 bites SSH RSA nyilvános és titkos kulcs fájljai alapértelmezés szerint a `~/.ssh` könyvtár. Ha ssh-kulcs már létezik az aktuális helyen, ezeket a fájlokat írja felül.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Részletes példa
Az alábbi példa bemutatja egy SSH-RSA-kulcspár létrehozásához további parancs beállításai. Ha ssh-kulcs már létezik az aktuális helyen, ezeket a fájlokat írja felül. 

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

`-t rsa` = a kulcs létrehozására, ebben az esetben az RSA formátum típusa

`-b 4096` Ebben az esetben 4096 hogy hány bitet tartalmazzon a kulcs =

`-C "azureuser@myserver"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében. Általában egy e-mail-cím van használnak megjegyzésként, de bármilyen leginkább az infrastruktúra használata.

`-f ~/.ssh/mykeys/myprivatekey` = a titkos kulcsfájl fájlnevét, ha úgy dönt, hogy nem az alapértelmezett név használatát. Egy megfelelő nyilvános kulcs fájlját hozzáfűzi az `.pub` jön létre ugyanabban a címtárban. A könyvtár léteznie kell.

`-N mypassphrase` = egy további, a titkos kulcsfájl eléréséhez használt jelszó. 

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

A cikkben használt kulcspár neve. Nevű kulcspár `id_rsa` az alapértelmezett érték; egyes eszközök várt lehet, hogy a `id_rsa` titkos kulcsfájl nevét, így több, célszerű. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye. Ha nincs megadva a teljes elérési útvonal, az `ssh-keygen` az aktuális munkakönyvtárban hozza létre a kulcsokat, és nem az alapértelmezett `~/.ssh` könyvtárban.

#### <a name="list-of-the-ssh-directory"></a>A listában a `~/.ssh` könyvtár

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Kulcs jelszava

`Enter passphrase (empty for no passphrase):`

Ez *erősen* ajánlott hozzáférési kódot hozzáadni a titkos kulcsot. A kulcs fájljának védelmére hozzáférési kód nélkül bárki a fájl segítségével, bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal jelentkezzen be. Jelszó hozzáadása sokkal nagyobb védelmet nyújt, abban az esetben, ha valaki megszerezné a titkos kulcsot, hogy hozzáférést kapjanak lesz ideje módosítani a kulcsokat biztosít.

## <a name="generate-keys-automatically-during-deployment"></a>Kulcsok automatikus létrehozása a telepítés során

Ha használja a [Azure CLI](/cli/azure) a virtuális gép létrehozásához, igény szerint létrehozhat SSH nyilvános és titkos kulcs fájljai futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--generate-ssh-keys` lehetőséget. A kulcsok a ~/.ssh címtárban tárolódnak. Vegye figyelembe, hogy az Ez a kapcsoló nem írja felül a kulcsokat, ha azok még léteznek az adott helyen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ha egy virtuális gép üzembe helyezése, adja meg a nyilvános SSH-kulcs

Hozzon létre egy Linux rendszerű virtuális gép által használt SSH-kulcsokat a hitelesítéshez, adja meg a nyilvános SSH-kulcsot az Azure Portalon, a CLI-t, virtuális gép létrehozásakor Resource Manager-sablonok vagy más módszerekkel. A portál használata esetén adja meg magát a nyilvános kulcsot. Ha használja a [Azure CLI](/cli/azure) szeretne létrehozni a virtuális gép egy meglévő nyilvános kulccsal, adja meg az értéket, vagy a nyilvános kulcs helyét futtatásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot a `--ssh-key-value` lehetőséget. 

Ha még nem ismeri a nyilvános SSH-kulcs formátumát, a nyilvános kulcs futtatásával megtekintheti `cat` módon, és cserélje le `~/.ssh/id_rsa.pub` a saját nyilvános kulcsának helyére:

```bash
cat ~/.ssh/id_rsa.pub
```

Kimenet a (Itt kivont) a következőhöz hasonló:

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Másolja és illessze be a nyilvános kulcsfájl tartalmát az Azure Portalon vagy a Resource Manager-sablonnal, hogy nem másol felesleges szóközöket vagy további linebreaks vezetnek be. Például, ha macOS használata esetén adatcsatornán keresztül is a nyilvános kulcs fájlját (alapértelmezés szerint `~/.ssh/id_rsa.pub`) való **pbcopy** tartalmának (nincsenek más Linux programok is ugyanaz, mint például **xclip**).

Ha inkább a nyilvános kulccsal, amely egy többsoros formátumban van, a korábban létrehozott nyilvános kulcsot a pem-tárolóban is létrehozhat egy RFC4716 formátumú kulcsot.

RFC4716 formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>Az SSH-ügyfelet a virtuális gép ssh-n
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi rendszeren, ssh-KAPCSOLATOT a virtuális IP-címe vagy DNS-neve a virtuális gépet. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a következő parancsban az rendszergazdájának felhasználóneve és a teljes tartománynevet (vagy IP-cím):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során megadott hozzáférési kódot, adja meg a hozzáférési kódot, mikor a bejelentkezési folyamat során a rendszer kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Ssh-agent használatával tárolja a titkos kulcs jelszava

Nem szeretné beírni a titkos kulcsfájl jelszavát minden SSH-bejelentkezéskor, használható `ssh-agent` segítségével gyorsítótárazhatja a titkos kulcsfájl jelszavát. Ha Mac gépet használ, a macOS-Kulcskarikához biztonságosan tárolja a titkos kulcs jelszava, amikor hívhat meg `ssh-agent`.

Ellenőrizze és használja `ssh-agent` és `ssh-add` az SSH-rendszer tájékoztatni a kulcsfontosságú fájlokat, így nem kell a jelszót interaktív használata.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal.

```bash
ssh-add ~/.ssh/id_rsa
```

A titkos kulcs jelszava most tárolva `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Másolja a kulcsot egy meglévő virtuális gép ssh-példány-id használatával
Ha már létrehozott egy virtuális Gépet, telepítheti az új nyilvános SSH-kulcsot a Linux rendszerű virtuális gépre a következőhöz hasonló paranccsal:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH konfigurációs fájl létrehozása és konfigurálása

Hozhat létre és konfigurálja az SSH konfigurációs fájl (`~/.ssh/config`) napló modulok felgyorsításához és az SSH-ügyfél működésének optimalizálásához. 

Az alábbi példa bemutatja egy egyszerű konfigurálás, használatával gyorsan egy adott virtuális gépre felhasználóként jelentkezzen be az alapértelmezett titkos SSH-kulcs használatával. 

### <a name="create-the-file"></a>A fájl létrehozása

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Szerkessze a fájlt az új SSH-konfiguráció hozzáadása

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Konfigurálása – példa

Adja hozzá a virtuális gazdagépe számára megfelelő konfigurációs beállításokat.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Hozzáadhat további gazdagépekre, hogy mindegyik saját dedikált kulcspárral használandó konfigurációi is. Lásd: [SSH konfigurációs fájl](https://www.ssh.com/ssh/config/) speciális konfigurációs beállításokat.

Most, hogy rendelkezik SSH-kulcspárral és konfigurált SSH konfigurációs fájllal, gyorsan és biztonságosan jelentkezhet be a Linux virtuális gépre. A következő parancs futtatásakor az SSH megkeresi és betölti a beállítások a `Host myvm` letiltása az SSH konfigurációs fájl.

```bash
ssh myvm
```

Az első bejelentkezéskor egy kiszolgálóra SSH-kulccsal, a parancs felszólítja, a kulcs jelszavának megadására.

## <a name="next-steps"></a>További lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával. A bejelentkezéshez nyilvános SSH-kulccsal létrehozott Azure virtuális gépek biztonságosabbak, mint az alapértelmezett bejelentkezési módszer jelszavaival létrehozott virtuális gépek.

* [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Hozzon létre egy Linux rendszerű virtuális gép Azure-sablon használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
