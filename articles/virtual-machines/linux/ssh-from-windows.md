---
title: SSH-kulcsok használata a Windows és Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, illetve egy Windows-számítógép SSH-kulcsok használatával csatlakozhat az Azure-ban Linux rendszerű virtuális gép.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630211"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Az SSH-kulcsok használata a Windows Azure-ban

Ez a cikk bemutatja a létrehozása és a egy Windows-számítógépre secure shell (SSH-) kulcsok használatával létrehozása és csatlakoztatása Linux rendszerű virtuális géphez (VM) az Azure-ban. SSH-kulcsokat a Linux vagy MacOS rendszerű ügyfél használatához tekintse meg a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) útmutatást.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-csomagok és az SSH-ügyfél
Csatlakozhat, és a Linux rendszerű virtuális gépek kezelése az Azure-ban egy *SSH-ügyfél*. Linux vagy macOS rendszerű általában számítógépek rendelkezik SSH parancskészlet létrehozni és felügyelni az SSH-kulcsokat, és győződjön meg arról, SSH-kapcsolatokat. 

Windows-számítógépek nem mindig rendelkezik telepített összehasonlítható SSH-parancsokat. Windows 10-verziók, amelyek tartalmazzák a [a Linux Windows alrendszere](https://docs.microsoft.com/windows/wsl/about) futtatásához és eléréséhez, például egy SSH-ügyfél natív módon a Bash felületen belül segédprogramok segítségével. 

Ha eltérő Bash Windows használni kíván, gyakori Windows SSH ügyfelek helyben is telepítheti a következő csomagok tartalmazza:

* [Putty-kapcsolaton keresztül](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git Pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Egy másik lehetőség az, hogy a rendelkezésre álló bashben SSH segédprogramok a [Azure Cloud Shell](../../cloud-shell/overview.md). 

* A Cloud Shell eléréséhez a böngészőben, [ https://shell.azure.com ](https://shell.azure.com) vagy a [az Azure portal](https://portal.azure.com). 
* A Cloud Shell parancsot egy terminálban a Visual Studio Code belül telepítésével elérni a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Ez a szakasz bemutatja, hozzon létre ssh-kulcs a Windows két alternatívája közül választhat.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Az ssh-keygen az SSH-kulcsok létrehozása

Ha egy parancs-rendszerhéj, például a Bash-a Windows vagy a GitBash (vagy az Azure Cloud Shell Bash) futtatható, hozzon létre egy SSH kulcspár a `ssh-keygen` parancsot. Írja be a következő parancsot, és a kérések megválaszolásával. Ha ssh-kulcs már létezik az aktuális helyen, ezeket a fájlokat írja felül. 

```bash
ssh-keygen -t rsa -b 2048
```

További háttérinformációkért és információkat lásd: a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) a kulcsok létrehozásához szükséges lépéseket `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>A PuTTYgen SSH-kulcsok létrehozása

Ha inkább a GUI-alapú eszköz segítségével hozzon létre SSH-kulcsokat, a PuTTYgen key generator mellékelt használhatja a [PuTTY letöltőcsomag](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

A PuTTYgen hozzon létre egy SSH-RSA-kulcspárt:

1. Indítsa el a PuTTYgen.

2. Kattintson a **készítése**. Alapértelmezés szerint a PuTTYgen egy 2048 bites SSH-2 RSA-kulcsot állít elő.

4. Az üres területen néhány vonatkozó, a kulcs létrehozásához egérmutatót.

5. Miután a nyilvános kulcs akkor jön létre, igény szerint adja meg, és egy jelszó megerősítése. Kéri a jelszót a Önt a hitelesítéshez a virtuális géphez az SSH-kulcsot. Anélkül, hogy egy hozzáférési kódot Ha valaki megszerzi a titkos kulcsot, azokat is bejelentkezhetnek bármely virtuális gép vagy szolgáltatás, amely ezt a kulcsot használja. Javasoljuk, hogy hozzon létre egy hozzáférési kódot. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.

6. A nyilvános kulcsot az ablak tetején jelenik meg. Másolja és illessze be az egysoros formátum nyilvános kulcsot az Azure portal vagy Azure Resource Manager-sablon egy Linux rendszerű virtuális gép létrehozásakor. Is **mentés nyilvános kulcs** , mentse a számítógépére:

    ![Mentse a nyilvános kulcsfájl PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Szeretné menteni a titkos kulcsot a PuTTy titkos kulcs formátuma (.ppk fájlt), rákattinthat **titkos kulcs mentése**. .Ppk fájlt van szüksége, a PuTTY SSH-kapcsolatot a virtuális Gépen, győződjön meg arról, hogy későbbi használat céljából szeretne.

    ![Mentse a titkos kulcsfájl PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Ha szeretné az OpenSSH formátumban lehet menteni a titkos kulcsot, a számos SSH-ügyfél által használt titkos kulcs formátumaként kattintson **átalakítások** > **exportálása OpenSSH-kulcs**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ha egy virtuális gép üzembe helyezése, adja meg a nyilvános SSH-kulcs

Hozzon létre egy Linux rendszerű virtuális gép által használt SSH-kulcsokat a hitelesítéshez, adja meg a nyilvános SSH-kulcsot, amikor létrehozza a virtuális gép az Azure portal és az egyéb módszerek használatával.

Az alábbi példa bemutatja, hogyan, akkor másolja és illessze be az Azure Portalon ezt a nyilvános kulcsot egy Linux rendszerű virtuális gép létrehozásakor. A nyilvános kulcsot általában majd tárolják `~/.ssh/authorized_keys` a új virtuális Gépen.

   ![Használja a nyilvános kulcsot, ha egy virtuális Gépet hoz létre az Azure Portalon](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Csatlakozzon a virtuális Géphez

Egyik módja, hogy a Windows a Linux rendszerű virtuális gépre SSH-kapcsolatot az SSH-ügyfelet használja. Ez az előnyben részesített módszere, ha egy SSH-ügyfél Windows rendszerre telepített rendelkezik, vagy az SSH-eszközöket használhatja az Azure Cloud Shell bashben. Ha inkább a GUI-alapú eszköz, csatlakoztathatja a putty-kapcsolaton keresztül.  

### <a name="use-an-ssh-client"></a>Egy SSH-ügyfél használata
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi rendszeren, ssh-KAPCSOLATOT a virtuális IP-címe vagy DNS-neve a virtuális gépet. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a következő parancsban az rendszergazdájának felhasználóneve és a teljes tartománynevet (vagy IP-cím):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során konfigurált egy hozzáférési kódot, adja meg a jelszót, amikor a rendszer kéri, a bejelentkezés során.

### <a name="connect-with-putty"></a>Csatlakozzon a putty használatával

Ha telepítette a [PuTTY letöltőcsomag](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) és a korábban létrehozott PuTTY titkos kulcsot (.ppk fájlt), a Linux rendszerű virtuális Gépet a putty-kapcsolaton keresztül kapcsolódhat.

1. Indítsa el a putty-kapcsolaton keresztül.

2. Töltse ki a gazdagép neve vagy IP-címet a virtuális gép az Azure Portalról:

    ![Új PuTTY kapcsolat megnyitása](./media/ssh-from-windows/putty-new-connection.png)

3. Kiválasztása előtt **nyílt**, kattintson a **kapcsolat** > **SSH** > **Auth** fülre. Keresse meg és válassza ki a PuTTY titkos kulcsot (.ppk fájlt):

    ![Válassza ki a PuTTY titkos kulcsot hitelesítéshez](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kattintson a **nyílt** a virtuális Géphez való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

* Lépésenkénti útmutató, beállítások és speciális SSH-kulcsokkal való kezelésével többek között: [részletes lépései hozzon létre SSH-kulcspárok](create-ssh-keys-detailed.md).

* PowerShell az Azure Cloud Shellben hozzon létre SSH-kulcsokat, és SSH-kapcsolatok a Linux rendszerű virtuális gépeken is használható. Tekintse meg a [PowerShell rövid](../../cloud-shell/quickstart-powershell.md#ssh).

* Ha az SSH használata a Linux rendszerű virtuális gépek csatlakozni, lásd: problémája [hibaelhárítása SSH kapcsolatok az Azure Linux VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
