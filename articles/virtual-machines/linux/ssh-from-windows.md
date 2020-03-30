---
title: Az SSH-kulcsok és a Windows együttes használata Linux rendszerű virtuális gépek esetében
description: Ismerje meg, hogyan hozhat létre és használhat SSH-kulcsokat egy Windows-számítógépen az Azure-beli Linux-alapú virtuális géphez való csatlakozáshoz.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e01fb23bbf1720f7d8df9c269373c1b8dc3ec75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034808"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Az SSH-kulcsok használata a Windows rendszerrel az Azure-ban

Ez a cikk ismerteti, hogyan hozhat létre és használhat *biztonságos rendszerhéj* (SSH) kulcsokat a Windows-számítógépen egy Linux virtuális gép (VM) létrehozásához és az Azure-beli virtuális géphez való csatlakozáshoz. A Linux vagy macOS-ügyfél SSH-kulcsainak használatához tekintse meg a [gyors](mac-create-ssh-keys.md) és [részletes](create-ssh-keys-detailed.md) útmutatást.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows csomagok és SSH-ügyfelek
Linuxos virtuális gépekhez csatlakozhat és kezelhető az Azure-ban egy *SSH-ügyfél*használatával. A Linux ot vagy macOS rendszert futtató számítógépek általában SSH-parancscsomaggal rendelkeznek az SSH-kulcsok létrehozásához és kezeléséhez, valamint az SSH-kapcsolatok létrehozásához. 

A Windows számítógépeken nem mindig vannak hasonló SSH-parancsok telepítve. A Windows 10 legújabb verziói [OpenSSH ügyfélparancsokat](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) biztosítanak az SSH-kulcsok létrehozásához és kezeléséhez, valamint az SSH-kapcsolatok parancssorból történő létrehozásához és kezeléséhez. A legújabb Windows 10-es verziók közé tartozik a [Windows Alrendszer Linuxhoz,](https://docs.microsoft.com/windows/wsl/about) hogy futtassa és hozzáférjen a segédprogramokhoz, például egy SSH ügyfélhez natívan egy Bash rendszerhéjon belül. 

A helyileg telepíthető egyéb gyakori Windows SSH-ügyfelek a következő csomagokban találhatók:

* [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git Windows-hoz](https://git-for-windows.github.io/)
* [MobaXterm között](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Az Azure Cloud Shellben a Bash ben elérhető SSH segédprogramokat is [használhatja.](../../cloud-shell/overview.md) 

* A Cloud Shell elérése [https://shell.azure.com](https://shell.azure.com) a webböngészőben vagy az [Azure Portalon.](https://portal.azure.com) 
* Az [Azure-fiókbővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)telepítésével terminálként érheti el a Cloud Shellt terminálként a Visual Studio-kódból.

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
A következő szakaszok két lehetőséget tartalmaznak az SSH-kulcspár Windows rendszeren való létrehozásához. Használhatja a shell`ssh-keygen`parancs ( ) vagy a GUI eszköz (PuTTYgen). Azt is vegye figyelembe, ha a Powershell használatával hozzon létre egy kulcsot, töltse fel a nyilvános kulcsot ssh.com(SECSH) formátumban. Cli használata esetén a kulcs feltöltés előtt alakítsa openssh formátumba. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>SSH-kulcsok létrehozása ssh-keygen segítségével

Ha olyan parancshéjat futtat a Windows rendszeren, amely támogatja az SSH-ügyféleszközöket (vagy `ssh-keygen` az Azure Cloud Shellt használja), hozzon létre egy SSH-kulcspárt a paranccsal. Írja be a következő parancsot, és válaszoljon a kérdésekre. Ha a kiválasztott helyen létezik Egy SSH kulcspár, a rendszer felülírja ezeket a fájlokat. 

```bash
ssh-keygen -t rsa -b 2048
```

További hátteret és információkat az SSH-kulcsok létrehozásának `ssh-keygen` [gyors](mac-create-ssh-keys.md) és [részletes](create-ssh-keys-detailed.md) lépései című témakörben talál.

### <a name="create-ssh-keys-with-puttygen"></a>SSH-kulcsok létrehozása puttygen segítségével

Ha inkább egy GUI-alapú eszköz létrehozására SSH kulcsok, használhatja a PuTTYgen kulcs generátor, tartalmazza a [PuTTY letöltési csomag](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

SSH RSA kulcspár létrehozása a PuTTYgen-nel:

1. Indítsa el a PuTTYgen-t.

2. Kattintson a **Létrehozás** lehetőségre. Alapértelmezés szerint a PuTTYgen 2048 bites SSH-2 RSA kulcsot hoz létre.

4. Mozgassa az egeret körül az üres területen, hogy véletlenszerűen a kulcsot.

5. A nyilvános kulcs létrehozása után adja meg és erősítse meg a jelszót. A rendszer kéri a jelszót, amikor hitelesíti magát a virtuális gép a saját SSH-kulccsal. Jelszó nélkül, ha valaki beszerzi a személyes kulcsot, akkor jelentkezzen be bármely virtuális gép vagy szolgáltatás, amely használja a kulcsot. Javasoljuk, hogy hozzon létre egy jelszót. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.

6. A nyilvános kulcs az ablak tetején jelenik meg. Másolja a teljes nyilvános kulcsot, majd illessze be az Azure Portalra vagy egy Azure Resource Manager-sablonba, amikor linuxos virtuális gép létrehozásakor. A nyilvános **kulcs mentése** lehetőséget is választhatja a másolat számítógépre mentéséhez:

    ![PuTTY nyilvános kulcsfájl mentése](./media/ssh-from-windows/save-public-key.png)

7. Ha a személyes kulcsot PuTTy személyes kulcsformátumban (.ppk fájl) szeretné menteni, válassza a **Személyes kulcs mentése**lehetőséget. A .ppk fájlra később a PuTTY használatához SSH-kapcsolat létesítéséhez szükség lesz a virtuális géphez.

    ![PuTTY személyes kulcsfájl mentése](./media/ssh-from-windows/save-ppk-file.png)

    Ha a személyes kulcsot OpenSSH formátumban, a sok SSH ügyfél által használt titkos kulcsformátumban szeretné menteni, válassza a **Konverziók** > **exportálása OpenSSH kulcsot**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>SSH nyilvános kulcs biztosítása virtuális gép telepítésekor

Hozzon létre egy Linux virtuális gép, amely SSH-kulcsokat használ a hitelesítéshez, adja meg az SSH nyilvános kulcsot, amikor létrehozza a virtuális gép az Azure Portalon vagy más módszerekhasználatával.

A következő példa bemutatja, hogyan másolja és illessze be ezt a nyilvános kulcsot az Azure Portalon, amikor linuxos virtuális gép létrehozása. A nyilvános kulcs általában tárolja a ~/.ssh/authorized_key könyvtárban az új virtuális gép.

   ![Nyilvános kulcs használata virtuális gép létrehozásakor az Azure Portalon](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

Az egyik módja annak, hogy egy SSH kapcsolatot a Linux virtuális gép a Windows, hogy egy SSH kliens. Ez az előnyben részesített módszer, ha egy SSH-ügyfél telepítve van a Windows rendszer, vagy ha az SSH-eszközöket használja az Azure Cloud Shell Bash. Ha a GUI-alapú eszközt részesíti előnyben, csatlakozhat a PuTTY-hoz.  

### <a name="use-an-ssh-client"></a>SSH-ügyfél használata
A nyilvános kulcs üzembe helyezése az Azure virtuális gép, és a személyes kulcs a helyi rendszeren, SSH a virtuális gép a virtuális gép IP-címét vagy DNS-nevét használja. Cserélje le az *azureusert* és a következő parancsban *myvm.westus.cloudapp.azure.com* a rendszergazda felhasználónevére és a teljesen minősített tartománynévre (vagy IP-címre):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor megadott egy jelszót, írja be a jelszót, amikor a bejelentkezési folyamat során rákérdez.

Ha a virtuális gép a just-in-time hozzáférési szabályzatot használja, hozzáférést kell kérnie, mielőtt csatlakozhatna a virtuális géphez. A just-in-time szabályzatról a [Virtuálisgép-hozzáférés kezelése az just in time házirend használatával című témakörben](../../security-center/security-center-just-in-time.md)talál további információt.

### <a name="connect-with-putty"></a>Csatlakozás a PuTTY-hoz

Ha telepítette a [PuTTY letöltési csomagot,](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) és korábban létrehozott egy PuTTY titkos kulcs (.ppk) fájlt, csatlakozhat egy Linux VM-hez a PuTTY-val.

1. Indítsd a putty-t.

2. Töltse ki a virtuális gép állomásnevét vagy IP-címét az Azure Portalon:

    ![Új PuTTY-kapcsolat megnyitása](./media/ssh-from-windows/putty-new-connection.png)

3. Válassza ki a **Connection** > **SSH** > **Auth kategóriát.** Tallózással keresse meg és válassza ki a PuTTY személyes kulcsot (.ppk fájl):

    ![Válassza ki a PuTTY személyes kulcsot a hitelesítéshez](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kattintson **a Megnyitás** gombra a virtuális géphez való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

* Az SSH-kulcsokkal való munka részletes lépéseit, beállításait és részletes példáit az [SSH-kulcspárok létrehozásának részletes lépései](create-ssh-keys-detailed.md)című témakörben talál.

* Az Azure Cloud Shellben a PowerShell használatával SSH-kulcsokat hozhat létre, és SSH-kapcsolatokat hozhat létre linuxos virtuális gépekhez. Tekintse meg a [PowerShell rövid útmutató.](../../cloud-shell/quickstart-powershell.md#ssh)

* Ha az SSH segítségével nehezen tud csatlakozni a Linux os virtuális gépekhez, olvassa [el az SSH-kapcsolatok hibaelhárítása Azure Linux os virtuális géphez című témakört.](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
