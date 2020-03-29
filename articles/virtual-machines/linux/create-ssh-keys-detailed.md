---
title: Az SSH kulcspár létrehozásának részletes lépései
description: Ismerje meg az Azure-beli Linux-virtuális gépek SSH-nyilvános és személyes kulcspárjának létrehozásához és kezeléséhez szükséges részletes lépéseket.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969541"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Részletes lépések: SSH-kulcsok létrehozása és kezelése linuxos virtuális gép hitelesítéséhez az Azure-ban 
Egy biztonságos rendszerhéj (SSH) kulcspár, létrehozhat egy Linux virtuális gép az Azure-ban, amely alapértelmezés szerint az SSH-kulcsok hitelesítés, így nincs szükség a jelszavak at. Az Azure Portalon, az Azure CLI-vel, a Resource Manager-sablonokkal vagy más eszközökkel létrehozott virtuális gépek az SSH nyilvános kulcsot is tartalmazhatják a központi telepítés részeként, amely sSH-kulcshitelesítést állít be az SSH-kapcsolatokhoz. 

Ez a cikk részletes hátteret és lépéseket tartalmaz egy SSH RSA nyilvános és titkos kulcsú fájlpár létrehozásához és kezeléséhez az SSH-ügyfélkapcsolatokhoz. Ha gyors parancsokat szeretne, olvassa [el Az SSH nyilvános és titkos kulcspár létrehozása Linuxos virtuális gépekhez az Azure-ban című témakört.](mac-create-ssh-keys.md)

Az SSH-kulcsok Windows-számítógépen történő létrehozásának és használatának további módjairól az [SSH-kulcsok használata az Azure-ban a Windows rendszerrel.](ssh-from-windows.md)

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Titkos kulcshoz tartozó jelszó
Az SSH titkos kulcsnak nagyon biztonságos jelszóval kell rendelkeznie a védelmére. Ez a jelszó csak a privát SSH kulcsfájl eléréséhez *szükséges,* és nem a felhasználói fiók jelszava. Amikor jelszót ad hozzá az SSH-kulcshoz, az a 128 bites AES eljárással titkosítja a titkos kulcsot, így a titkos kulcs használhatatlan az azt visszafejtő jelszó nélkül. Ha a támadó ellopta a személyes kulcsot, és a kulcs nem rendelkezik jelmondattal, akkor a személyes kulccsal bejelentkezhet a megfelelő nyilvános kulccsal rendelkező kiszolgálókra. Ha egy személyes kulcsot egy jelszó véd, azt a támadó nem tudja használni, és további biztonsági réteget biztosít az Azure-beli infrastruktúrához.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Az SSH-kulcsok használata és a használat előnyei

Amikor létrehoz egy Azure-beli virtuális gép a nyilvános kulcs megadásával, `.pub` az Azure `~/.ssh/authorized_keys` másolja a nyilvános kulcsot (formátumban) a virtuális gép mappájába. SSH-kulcsok `~/.ssh/authorized_keys` segítségével hívja az ügyfelet, hogy megfeleljen a megfelelő titkos kulcsot egy SSH-kapcsolaton. Egy Azure Linux virtuális gép, amely SSH-kulcsokat használ a hitelesítéshez, az Azure úgy konfigurálja az SSHD-kiszolgálót, hogy ne engedélyezze a jelszóbejelentkezést, csak az SSH-kulcsokat. Ezért egy SSH-kulcsokkal rendelkező Azure Linux virtuális gép létrehozásával biztosíthatja a virtuális gép üzembe helyezését, és mentheti `sshd_config` magát a jelszavak fájlba való letiltásának tipikus üzembe helyezés utáni konfigurációs lépésével.

Ha nem kívánja használni az SSH-kulcsokat, beállíthatja a Linux virtuális gép jelszó-hitelesítést. Ha a virtuális gép nincs kitéve az interneten, jelszavak használata elegendő lehet. Azonban továbbra is kezelnie kell a jelszavakat az egyes Linux virtuális gépekhez, és fenn kell tartania a megfelelő jelszóházirendeket és -gyakorlatokat, például a minimális jelszóhosszt és a rendszeres frissítéseket. Az SSH-kulcsok használata csökkenti az egyes hitelesítő adatok kezelésének összetettségét több virtuális gép között.

## <a name="generate-keys-with-ssh-keygen"></a>Kulcsok létrehozása ssh-keygen segítségével

A kulcsok létrehozásához az `ssh-keygen`előnyben részesített parancs a , amely elérhető az Azure Cloud Shell OpenSSH segédprogramjaival, egy macOS vagy Linux gazdagépben, a [Windows Alrendszer Linuxhoz](https://docs.microsoft.com/windows/wsl/about)és más eszközökhöz. `ssh-keygen`kérdéseket tesz fel, majd egy személyes kulcsot és egy hozzá illő nyilvános kulcsot ír. 

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.

### <a name="basic-example"></a>Alapszintű példa

A `ssh-keygen` következő parancs 2048 bites SSH RSA nyilvános és titkos `~/.ssh` kulcsfájlokat hoz létre alapértelmezés szerint a könyvtárban. Ha az aktuális helyen létezik Egy SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Részletes példa
A következő példa további parancsbeállításokat mutat be az SSH RSA kulcspár létrehozásához. Ha az aktuális helyen létezik Egy SSH-kulcspár, a rendszer felülírja ezeket a fájlokat. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**A parancs ismertetése**

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-m PEM`= a kulcsot PEM formátumban formázza

`-t rsa`= a létrehozandó kulcs típusa, ebben az esetben RSA formátumban

`-b 4096`= a kulcsban lévő bitek száma, ebben az esetben 4096

`-C "azureuser@myserver"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében. Általában egy e-mail címet használnak, mint a megjegyzés, de használja, ami a legjobban működik az infrastruktúra.

`-f ~/.ssh/mykeys/myprivatekey`= a személyes kulcsfájl fájlneve, ha úgy dönt, hogy nem használja az alapértelmezett nevet. A hozzáfűzött `.pub` megfelelő nyilvános kulcsfájl ugyanabban a könyvtárban jön létre. A könyvtárnak léteznie kell.

`-N mypassphrase`= a személyes kulcsfájl eléréséhez használt további jelszó. 

### <a name="example-of-ssh-keygen"></a>Ssh-keygen – példa

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Mentett kulcsfájlok

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

A cikkben használt kulcspár neve. A kulcspár `id_rsa` neve az alapértelmezett; egyes eszközök a `id_rsa` személyes kulcs fájlnevére számíthatnak, ezért jó ötlet. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye. Ha nincs megadva a teljes elérési útvonal, az `ssh-keygen` az aktuális munkakönyvtárban hozza létre a kulcsokat, és nem az alapértelmezett `~/.ssh` könyvtárban.

#### <a name="list-of-the-ssh-directory"></a>A `~/.ssh` könyvtár listája

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Kulcsjelszó

`Enter passphrase (empty for no passphrase):`

*Erősen* ajánlott jelszót hozzáadni a személyes kulcshoz. A kulcsfájl védelmét szolgáló jelszó nélkül bárki, aki a fájllal rendelkezik, betud jelentkezni bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. A jelszó hozzáadása nagyobb védelmet nyújt arra az esetre, ha valaki hozzáférhet a személyes kulcsfájljához, így időt ad a kulcsok módosítására.

## <a name="generate-keys-automatically-during-deployment"></a>Kulcsok automatikus létrehozása a telepítés során

Ha az [Azure CLI](/cli/azure) segítségével hozza létre a virtuális gép, opcionálisan sSH nyilvános és személyes kulcsfájlok futtatásával az [az vm create](/cli/azure/vm) parancsot a `--generate-ssh-keys` beállítással. A kulcsok a ~/.ssh könyvtárban tárolódnak. Ne feledje, hogy ez a parancs beállítás nem írja felül a kulcsokat, ha azok már léteznek az adott helyen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>SSH nyilvános kulcs biztosítása virtuális gép telepítésekor

Hozzon létre egy Linux virtuális gép, amely SSH-kulcsokat használ a hitelesítéshez, adja meg az SSH nyilvános kulcsot, amikor létrehozza a virtuális gép az Azure Portalon, CLI, Resource Manager-sablonok vagy más módszerek használatával. A portál használatakor maga a nyilvános kulcsot kell megadnia. Ha az [Azure CLI](/cli/azure) segítségével hozza létre a virtuális gép egy meglévő nyilvános kulcs, adja meg a nyilvános `--ssh-key-value` kulcs értékét vagy helyét az [az vm create](/cli/azure/vm) parancs futtatásával. 

Ha nem ismeri az SSH nyilvános kulcs formátumát, a nyilvános kulcsot `cat` az alábbiak `~/.ssh/id_rsa.pub` szerint futtatva láthatja, és lecseréli a saját nyilvános kulcsú fájlhelyét:

```bash
cat ~/.ssh/id_rsa.pub
```

Kimenet hasonló a következő (itt redacted):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Ha a nyilvános kulcsfájl tartalmát másolja és illeszti be az Azure Portalra vagy egy Resource Manager-sablonba, győződjön meg arról, hogy nem másol további szóközt, és nem vezet be további sortöréseket. Ha például macOS-t használ, a nyilvános kulcsfájl `~/.ssh/id_rsa.pub`(alapértelmezés szerint) **a pbcopy** segítségével másolhatja a tartalmat `xclip`(vannak más Linux-programok, amelyek ugyanezt teszik, például ).

Ha többsoros formátumú nyilvános kulcsot szeretne használni, a korábban létrehozott nyilvános kulcsból létrehozhat egy RFC4716 formátumú kulcsot egy pem tárolóban.

RFC4716 formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH a virtuális gép egy SSH kliens
A nyilvános kulcs üzembe helyezése az Azure virtuális gép, és a személyes kulcs a helyi rendszeren, SSH a virtuális gép a virtuális gép IP-címét vagy DNS-nevét használja. Cserélje le az *azureusert* és a következő parancsban *myvm.westus.cloudapp.azure.com* a rendszergazda felhasználónevére és a teljesen minősített tartománynévre (vagy IP-címre):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor megadott egy jelszót, írja be a jelszót, amikor a bejelentkezési folyamat során a rendszer kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

Ha a virtuális gép a just-in-time hozzáférési szabályzatot használja, hozzáférést kell kérnie, mielőtt csatlakozhatna a virtuális géphez. A just-in-time szabályzatról a [Virtuálisgép-hozzáférés kezelése az just in time házirend használatával című témakörben](../../security-center/security-center-just-in-time.md)talál további információt.

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Titkos kulcsjelszavaitárolás az ssh-agent használatával

Ha minden SSH-bejelentkezésnél be szeretné írni a személyes kulcsfájl-jelszót, gyorsítótárazhatja `ssh-agent` a személyes kulcsfájl-jelszót. Mac használata esetén a macOS-kulcskarika biztonságosan tárolja a személyes `ssh-agent`kulcs jelszót a meghíváskor.

Ellenőrizze és `ssh-agent` `ssh-add` használja, és tájékoztassa az SSH rendszert a kulcsfájlokról, hogy ne kelljen interaktívan használnia a jelszót.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal.

```bash
ssh-add ~/.ssh/id_rsa
```

A személyes kulcs jelmondata `ssh-agent`most a ban van tárolva.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>A kulcs másolása meglévő virtuális gépre az ssh-copy-id használatával
Ha már létrehozott egy virtuális gép, telepítheti az új SSH nyilvános kulcsot a Linux virtuális gép egy parancs hasonló a következő:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH konfigurációs fájl létrehozása és konfigurálása

SSH konfigurációs fájlt (`~/.ssh/config`) hozhat létre és konfigurálhat a bejelentkezések felgyorsításához és az SSH-ügyfél viselkedésének optimalizálásához. 

A következő példa egy egyszerű konfigurációt mutat be, amely segítségével gyorsan bejelentkezhet felhasználóként egy adott virtuális gépre az alapértelmezett SSH titkos kulccsal. 

### <a name="create-the-file"></a>A fájl létrehozása

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>A fájl szerkesztése az új SSH-konfiguráció hozzáadásához

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Konfigurációs példa

Adja meg a gazdagép virtuális gépének megfelelő konfigurációs beállításokat.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

További állomások konfigurációit is hozzáadhatja, hogy mindegyik saját dedikált kulcspárt használhasson. A speciális konfigurációs beállításokat az [SSH konfigurációs fájlban](https://www.ssh.com/ssh/config/) található.

Most, hogy van egy SSH kulcspárja és egy konfigurált SSH konfigurációs fájlja, gyorsan és biztonságosan bejelentkezhet a Linux virtuális gépbe. A következő parancs futtatásakor az SSH megkeresi `Host myvm` és betölti az SSH konfigurációs fájlban lévő blokk beállításait.

```bash
ssh myvm
```

Amikor először jelentkezik be egy kiszolgálóra SSH-kulccsal, a parancs az adott kulcsfájl jelmondatát kéri.

## <a name="next-steps"></a>További lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával. Az SSH nyilvános kulccsal létrehozott Azure-virtuális gépek a bejelentkezés imázsa miatt jobban biztonságosak, mint az alapértelmezett bejelentkezési módszerrel létrehozott virtuális gépek, a jelszavak.

* [Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linuxos virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
