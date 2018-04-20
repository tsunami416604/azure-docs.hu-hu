---
title: SSH-kulcsok használata a Windows rendszerben Linux virtuális gépek |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és használhat SSH-kulcsok Windows rendszerű számítógépeken egy Linux virtuális gépet az Azure való kapcsolódáshoz.
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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-kulcsok használata a Windows Azure-on

Ez a cikk készítése és a secure shell (SSH) kulcsok használata Windows rendszerű számítógépeken a létrehozása, és kapcsolódjon a Linux virtuális gépek (VM) az Azure-ban módjai be. A Linux vagy macOS ügyfélben SSH-kulcsok használatát ismerteti a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) útmutatást.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-csomagok és az SSH-ügyfél
Csatlakozhat, és a Linux virtuális gépek kezelése az Azure használatával egy *SSH-ügyfél*. Linux- vagy macOS általában futtató számítógépek rendelkezik SSH parancskészlet szeretnék létrehozni és felügyelni az SSH-kulcsokat, és, hogy az SSH-kapcsolatokhoz. 

Windows rendszerű számítógépek mindig összehasonlítható SSH parancsa sincs telepítve. Amely tartalmazza a Windows 10-verziók a [Linux rendszerhez készült Windows alrendszer](https://docs.microsoft.com/windows/wsl/about) lehetővé teszik futtatására és segédprogramok például egy SSH-ügyfél alapértelmezés szerint a rendszerhéjakba belül elérésére. 

Ha valami nem Bash Windows használni kívánt, közös Windows SSH ügyfelek helyileg is telepítheti a következő csomagokat tartalmazza:

* [A puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [A Windows Git](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Egy másik lehetőség, hogy a Bash elérhető az SSH segédeszközök használja a [Azure Cloud rendszerhéj](../../cloud-shell/overview.md). 

* Felhő rendszerhéj eléréséhez a böngészőben, [ https://shell.azure.com ](https://shell.azure.com) vagy a [Azure-portálon](https://portal.azure.com). 
* Egy terminált a Visual Studio kód felhő rendszerhéj telepítésével elérni a [Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Ez a szakasz ismerteti a Windows egy SSH-kulcspárral létrehozásához két lehetőség közül választhat.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Az ssh-keygen SSH-kulcsok létrehozása

Ha a parancs-rendszerhéj például Bash a Windows vagy GitBash (vagy a Bash Azure Cloud rendszerhéj) futtatása, hozzon létre egy SSH kulcspár az a `ssh-keygen` parancsot. Írja be a következő parancsot, és válaszolja meg a megjelenő utasításokat. Ha egy SSH-kulcspárral megtalálható az aktuális helyen, a rendszer felülírja ezeket a fájlokat. 

```bash
ssh-keygen -t rsa -b 2048
```

További háttér és adatokat, a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) a kulcsok létrehozásához szükséges lépéseket `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>A PuTTYgen SSH-kulcsok létrehozása

Ha jobban szeret SSH-kulcsok létrehozása a GUI-alapú eszköz segítségével, használhatja a PuTTYgen megosztottelérésikulcs-készítő, mellékelt a [PuTTY letöltőcsomag](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Egy SSH-RSA kulcspár létrehozása PuTTYgen:

1. Indítsa el a puttygen eszközzel készíthet.

2. Kattintson a **készítése**. Alapértelmezés szerint a PuTTYgen egy 2048 bites SSH-2 RSA-kulcsot hoz létre.

4. Az egér néhány annak véletlenszerű, a kulcs létrehozásához üres terület felett.

5. Után a nyilvános kulcs jön létre, igény szerint adja meg, és erősítse meg a jelszót. Bekéri a jelszavát Önt a hitelesítéshez a virtuális gép SSH-kulcsban. Nélkül egy hozzáférési kódot Ha valaki beszerzi a titkos kulcsot, akkor is bejelentkezhetnek a valamennyi virtuális gép által használt kulcs. Azt javasoljuk, hogy hozzon létre egy hozzáférési kódot. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.

6. A nyilvános kulcs jelenik meg a az ablak tetején. Ön lemásolhatja és beillesztheti az egysoros formátumú nyilvános kulcsot az Azure-portálon vagy az Azure Resource Manager-sablon Linux virtuális gép létrehozásakor. Is **mentés nyilvános kulcs** , mentse a számítógéphez:

    ![PuTTY nyilvánoskulcs-fájl mentése](./media/ssh-from-windows/save-public-key.png)

7. A PuTTy titkos kulcs formátumaként (.ppk fájlt) a titkos kulcs mentéséhez kattintson **mentés titkos kulcs**. A .ppk fájlt a PuTTY SSH-kapcsolat legyen a virtuális Gépet későbbi használatra szeretné.

    ![Mentse a PuTTY titkos kulcs fájlja](./media/ssh-from-windows/save-ppk-file.png)

    A titkos kulcs a protokoll OpenSSH formátumban menti, a titkos kulcs formátumaként sok SSH-ügyfelek, kattintson a **átalakítások** > **exportálása OpenSSH-kulcsot**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>A virtuális gép telepítésekor adja meg a nyilvános SSH-kulcs

Hozzon létre egy Linux virtuális gép SSH-kulcsokat használ, adja meg a nyilvános SSH-kulcsot az Azure-portálon vagy más módszerrel virtuális gép létrehozásakor.

A következő példa bemutatja, hogyan, akkor másolja és illessze be a nyilvános kulcs be az Azure portálon Linux virtuális gép létrehozásakor. A nyilvános kulcsot általában majd tárolja `~/.ssh/authorized_keys` a új virtuális gépén.

   ![Használja a nyilvános kulcsot, ha a virtuális gép létrehozása az Azure-portálon](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Csatlakoztassa a virtuális Gépet

Egy kapcsolat egy SSH a Linux virtuális gépre a Windows módja egy SSH-ügyfél használata. Ha egy SSH-ügyfél Windows rendszerre telepített, vagy Azure Cloud rendszerhéj Bash használ SSH-eszközök esetében előnyben részesített módszert. Ha inkább a GUI-alapú eszköz, a PuTTY kapcsolatba léphet.  

### <a name="use-an-ssh-client"></a>Egy SSH-ügyfél használata
A nyilvános kulcsot az Azure virtuális gépen telepített, és a titkos kulcsot a helyi számítógépen, a virtuális géphez az IP-cím vagy a DNS-neve, a virtuális gép SSH. Cserélje le *azureuser* és *myvm.westus.cloudapp.azure.com* a rendszergazdai felhasználónevet és a teljes tartománynév (vagy IP-címet) az alábbi parancs:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor konfigurált egy jelszót, írja be a jelszót, amikor a rendszer kéri a bejelentkezési folyamat során.

### <a name="connect-with-putty"></a>Csatlakozzon a PuTTY

Ha telepítette a [PuTTY letöltőcsomag](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) korábban létrehozott PuTTY titkos kulcs (.ppk fájlt), és a Linux virtuális Géphez a PuTTY kapcsolódhat.

1. Indítsa el a PuTTy.

2. Adja meg az állomásnevet vagy IP-címet a virtuális gép az Azure-portálon:

    ![Új PuTTY kapcsolat megnyitása](./media/ssh-from-windows/putty-new-connection.png)

3. Mielőtt kiválasztja **nyissa meg**, kattintson a **kapcsolat** > **SSH** > **Auth** lapon. Keresse meg és jelölje ki a PuTTY titkos kulcsot (.ppk fájlt):

    ![Válassza ki a PuTTY titkos kulcsot a hitelesítéshez](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kattintson a **nyitott** a virtuális Géphez való kapcsolódáshoz.

## <a name="next-steps"></a>További lépések

* Részletes lépéseket, a beállításokat és az SSH-kulcsok speciális példák, lásd: [SSH létrehozásának részletes lépései kulcs párok](create-ssh-keys-detailed.md).

* Azure Cloud rendszerhéj PowerShell is használható SSH-kulcsok létrehozása és az SSH-kapcsolat létrehozása a Linux virtuális gépek. Tekintse meg a [PowerShell gyors üzembe helyezés](../../cloud-shell/quickstart-powershell.md#ssh).

* Ha gondja támad az SSH használata a Linux virtuális gépek csatlakozni, lásd: [hibaelhárítása SSH kapcsolódik az Azure Linux virtuális gép](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
