---
title: SSH-kulcsok használata a Windows és Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, illetve egy Windows-számítógép SSH-kulcsok használatával csatlakozhat az Azure-ban Linux rendszerű virtuális gép.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 247d09e58ded2de12fb7cc6b5a036b695e715077
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298653"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Az SSH-kulcsok használata a Windows Azure-ban

Ez a cikk ismerteti azokat a módszereket, és hozzon létre *secure shell* (SSH-) kulcsok létrehozása és csatlakoztatása Linux rendszerű virtuális géphez (VM) az Azure-ban Windows számítógépen. SSH-kulcsokat a Linux vagy MacOS rendszerű ügyfél használatához tekintse meg a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) útmutatást.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-csomagok és az SSH-ügyfél
Csatlakozhat, és a Linux rendszerű virtuális gépek kezelése az Azure-ban egy *SSH-ügyfél*. Linux vagy macOS rendszerű általában számítógépek rendelkezik SSH parancskészlet létrehozni és felügyelni az SSH-kulcsokat, és győződjön meg arról, SSH-kapcsolatokat. 

Windows-számítógépek nem mindig rendelkezik telepített összehasonlítható SSH-parancsokat. Adja meg a Windows 10 legújabb verzióit [OpenSSH-ügyfél parancsai](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) és hozhat létre és SSH-kulcsok kezelése parancsot a parancssorba az SSH-kapcsolatok. Legutóbbi Windows 10-es verziói is tartalmaznak a [a Linux Windows alrendszere](https://docs.microsoft.com/windows/wsl/about) futtatásához és eléréséhez, például egy SSH-ügyfél natív módon a Bash felületen belül segédprogramok. 

Egyéb gyakori Windows SSH ügyfelek helyben is telepítheti a következő csomagokat tartalmazza:

* [Putty-kapcsolaton keresztül](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git For Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

A rendelkezésre álló bashben SSH segédprogramok is használhatja a [Azure Cloud Shell](../../cloud-shell/overview.md). 

* A Cloud Shell eléréséhez a böngészőben, [ https://shell.azure.com ](https://shell.azure.com) vagy a [az Azure portal](https://portal.azure.com). 
* A Cloud Shell parancsot egy terminálban a Visual Studio Code belül telepítésével elérni a [Azure-fiókkiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
A következő szakaszok ismertetik az SSH-kulcspár létrehozása a Windows két alternatívája közül választhat. A felület parancs használata (`ssh-keygen`) vagy egy grafikus eszközt (PuTTYgen).

### <a name="create-ssh-keys-with-ssh-keygen"></a>Az ssh-keygen az SSH-kulcsok létrehozása

Ha egy parancs-rendszerhéj futtatja, amely támogatja az SSH-ügyfél eszközök Windows (vagy használhatja az Azure Cloud Shell), hozzon létre egy SSH kulcspár a `ssh-keygen` parancsot. Írja be a következő parancsot, és a kérések megválaszolásával. Ha ssh-kulcs már létezik a megadott helyre, azokat a fájlokat írja felül. 

```bash
ssh-keygen -t rsa -b 2048
```

További háttérinformációkért és információkat lásd: a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) lépések végrehajtásával hozza létre az SSH-kulcsok használatával `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>A PuTTYgen SSH-kulcsok létrehozása

Ha inkább a GUI-alapú eszköz segítségével hozzon létre SSH-kulcsokat, a PuTTYgen key generator mellékelt használhatja a [PuTTY letöltőcsomag](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

A PuTTYgen hozzon létre egy SSH-RSA-kulcspárt:

1. Indítsa el a PuTTYgen.

2. Kattintson a **készítése**. Alapértelmezés szerint a PuTTYgen egy 2048 bites SSH-2 RSA-kulcsot állít elő.

4. Az üres területen adja meg a kulcs a véletlenszerűségre azért mozgassa az egeret.

5. Miután a nyilvános kulcs akkor jön létre, igény szerint adja meg, és egy jelszó megerősítése. Bekéri a jelszót a titkos SSH-kulcsot a hitelesítéshez a virtuális géphez. Anélkül, hogy egy hozzáférési kódot Ha valaki megszerzi a titkos kulcs felhasználóknak a bejelentkezéshez bármely virtuális gép vagy szolgáltatás, amely ezt a kulcsot használja. Javasoljuk, hogy hozzon létre egy hozzáférési kódot. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.

6. A nyilvános kulcsot az ablak tetején jelenik meg. Másolja ki a teljes nyilvános kulcsot, és ezután illessze be az Azure portal vagy Azure Resource Manager-sablon egy Linux rendszerű virtuális gép létrehozásakor. Lehetőség kiválasztásával **mentés nyilvános kulcs** , mentse a számítógépére:

    ![Mentse a nyilvános kulcsfájl PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Szeretné menteni a titkos kulcsot a PuTTy titkos kulcs formátuma (.ppk fájlt), bejelölheti **titkos kulcs mentése**. Szüksége lesz később, a PuTTY használata az SSH-kapcsolatot a virtuális Gépen, győződjön meg arról, hogy .ppk fájlt.

    ![Mentse a titkos kulcsfájl PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Ha szeretné az OpenSSH formátumban lehet menteni a titkos kulcsot, a számos SSH-ügyfél által használt titkos kulcs formátumaként válassza **átalakítások** > **exportálása OpenSSH-kulcs**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Adja meg a nyilvános SSH-kulccsal, egy virtuális géphez

Hozzon létre egy Linux rendszerű virtuális gép által használt SSH-kulcsokat a hitelesítéshez, adja meg a nyilvános SSH-kulcsot, amikor létrehozza a virtuális gép az Azure portal és az egyéb módszerek használatával.

Az alábbi példa bemutatja, hogyan, akkor másolja és illessze be az Azure Portalon ezt a nyilvános kulcsot egy Linux rendszerű virtuális gép létrehozásakor. A nyilvános kulcs ezután általában tárolják az új virtuális gép ~/.ssh/authorized_key könyvtárába.

   ![Használja a nyilvános kulcsot, ha egy virtuális Gépet hoz létre az Azure Portalon](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

Egyik módja, hogy a Windows a Linux rendszerű virtuális gépre SSH-kapcsolatot az SSH-ügyfelet használja. Ez az előnyben részesített módszere, ha egy SSH-ügyfél a Windows rendszeren telepítve van, vagy ha az SSH az eszközöket használhatja az Azure Cloud Shell bashben. Ha inkább a GUI-alapú eszköz, csatlakoztathatja a putty-kapcsolaton keresztül.  

### <a name="use-an-ssh-client"></a>Egy SSH-ügyfél használata
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi rendszeren, ssh-KAPCSOLATOT a virtuális IP-címe vagy DNS-neve a virtuális gépet. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a következő parancsban az rendszergazdájának felhasználóneve és a teljes tartománynevet (vagy IP-cím):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során konfigurált egy hozzáférési kódot, adja meg a jelszót, amikor a rendszer kéri a bejelentkezési folyamat során.

Ha a virtuális gép nem használja a just-in-time-hozzáférési házirend, hozzáférés kérése, mielőtt az csatlakozna a virtuális géphez szeretne. A just-in-time házirenddel kapcsolatos további információkért lásd: [kezelése virtuálisgép-hozzáférés az igény szerinti szabályzat](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Csatlakozzon a putty használatával

Ha telepítette a [PuTTY letöltőcsomag](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) és a egy PuTTY titkos kulcs (.ppk) fájlt, korábban létrehozott egy Linux rendszerű virtuális gép a putty használatával csatlakozhat.

1. Start PuTTy.

2. Töltse ki a gazdagép neve vagy IP-címet a virtuális gép az Azure Portalról:

    ![Új PuTTY kapcsolat megnyitása](./media/ssh-from-windows/putty-new-connection.png)

3. Válassza ki a **kapcsolat** > **SSH** > **Auth** kategória. Keresse meg és válassza ki a PuTTY titkos kulcsot (.ppk fájlt):

    ![Válassza ki a PuTTY titkos kulcsot hitelesítéshez](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kattintson a **nyílt** a virtuális Géphez való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

* Lépésenkénti útmutató, beállítások és speciális SSH-kulcsokkal való kezelésével többek között: [részletes lépései hozzon létre SSH-kulcspárok](create-ssh-keys-detailed.md).

* PowerShell az Azure Cloud Shellben hozzon létre SSH-kulcsokat, és SSH-kapcsolatok a Linux rendszerű virtuális gépeken is használható. Tekintse meg a [PowerShell rövid](../../cloud-shell/quickstart-powershell.md#ssh).

* Ha nehézségekbe ütközik az SSH a Linux rendszerű virtuális gépek csatlakozni, lásd: [hibaelhárítása SSH kapcsolatok az Azure Linux VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
