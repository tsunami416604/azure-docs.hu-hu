---
title: Az SSH-kulcsok és a Windows együttes használata Linux rendszerű virtuális gépek esetében
description: Útmutató SSH-kulcsok létrehozásához és használatához Windows-számítógépen az Azure-beli Linux rendszerű virtuális gépekhez való kapcsolódáshoz.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 81dfac2a1623253a110833a96fddd1b41bd11b26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390227"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-kulcsok használata az Azure-ban a Windowsban

Ez a cikk bemutatja, hogyan hozhatók létre és használhatók a *Secure Shell* -(SSH-) kulcsok egy Windows rendszerű számítógépen egy linuxos virtuális gép (VM) létrehozásához és az Azure-ban való kapcsolódáshoz. Ha Linux vagy macOS rendszerű ügyfélről szeretne SSH-kulcsokat használni, tekintse meg a [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) útmutatást.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-csomagok és SSH-ügyfelek
Az Azure-beli Linux rendszerű virtuális gépeket *SSH-ügyfél*használatával lehet csatlakozni és felügyelni. A Linux vagy macOS rendszerű számítógépek általában SSH-parancsokkal rendelkeznek SSH-kulcsok létrehozásához és kezeléséhez, valamint SSH-kapcsolatok készítéséhez. 

A Windows rendszerű számítógépek nem mindig rendelkeznek hasonló SSH-parancsokkal. A Windows 10-es legújabb verziói [OpenSSH-ügyfél-parancsokat](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) biztosítanak az ssh-kulcsok létrehozásához és kezeléséhez, valamint az SSH-kapcsolatok parancssorból való futtatásához. A legújabb Windows 10-es verziók közé tartozik a [Linux rendszerhez készült Windows alrendszer](https://docs.microsoft.com/windows/wsl/about) is, amely egy bash-rendszerhéjon natív módon futtatja és érheti el a segédprogramokat, például egy SSH-ügyfelet. 

A helyileg telepíthető egyéb gyakori Windows SSH-ügyfelek a következő csomagokban találhatók:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git for Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Használhatja a Bashben elérhető SSH-segédprogramokat is a [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Cloud Shell a böngészőben [https://shell.azure.com](https://shell.azure.com) vagy a [Azure Portal](https://portal.azure.com). 
* Az [Azure-fiók bővítményének](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)telepítésével elérheti Cloud Shell terminálként a Visual Studio Code-ban.

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
A következő szakaszok két lehetőséget ismertetnek az SSH-kulcspár Windowson való létrehozásához. Használhat egy rendszerhéj-parancsot ( `ssh-keygen` ) vagy egy GUI-eszközt (PuTTYgen). Azt is vegye figyelembe, hogy ha a PowerShell használatával hoz létre kulcsot, töltse fel a nyilvános kulcsot SSH. com (SECSH) formátumban. A CLI használatakor a feltöltés előtt alakítsa át a kulcsot OpenSSH formátumba. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>SSH-kulcsok létrehozása ssh-keygen használatával

Ha olyan Windows-parancssort futtat, amely támogatja az SSH-ügyféleszközök használatát (vagy Azure Cloud Shell), hozzon létre egy SSH-kulcspárt a `ssh-keygen` parancs használatával. Írja be a következő parancsot, és válaszolja meg a kérdéseit. Ha egy SSH-kulcspár létezik a kiválasztott helyen, a rendszer felülírja ezeket a fájlokat. 

```bash
ssh-keygen -t rsa -b 2048
```

További háttér és információk: az SSH-kulcsok használatával történő létrehozásának [gyors](mac-create-ssh-keys.md) vagy [részletes](create-ssh-keys-detailed.md) lépései `ssh-keygen` .

### <a name="create-ssh-keys-with-puttygen"></a>SSH-kulcsok létrehozása a PuTTYgen

Ha az SSH-kulcsok létrehozásához GUI-alapú eszközt szeretne használni, használhatja a [Putty letöltési csomag](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)részét képező PuTTYgen kulcs-generátort is. 

SSH RSA kulcspár létrehozása a PuTTYgen:

1. Indítsa el a PuTTYgen.

2. Kattintson a **Létrehozás** lehetőségre. Alapértelmezés szerint a PuTTYgen egy 2048 bites SSH-2 RSA-kulcsot hoz létre.

3. Vigye az egérmutatót az üres részre, hogy véletlenszerű legyen a kulcshoz.

4. A nyilvános kulcs létrehozása után opcionálisan megadhatja és megerősítheti a jelszót. A rendszer a jelszó megadását fogja kérni, ha a titkos SSH-kulccsal hitelesíti magát a virtuális gépen. Jelszó nélkül, ha valaki megszerzi a titkos kulcsot, bejelentkezhet bármely olyan virtuális gépre vagy szolgáltatásba, amely ezt a kulcsot használja. Javasoljuk, hogy hozzon létre egy hozzáférési kódot. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.

5. A nyilvános kulcs az ablak tetején jelenik meg. Másolhatja ezt a teljes nyilvános kulcsot, majd beillesztheti a Azure Portalba vagy egy Azure Resource Manager sablonba, amikor létrehoz egy Linux rendszerű virtuális gépet. Azt is megteheti, hogy a **nyilvános kulcs mentése** lehetőséggel másolatot készít a számítógépre. Vegye figyelembe, hogy amikor fájlba ment, a PuTTY átalakítja a nyilvános kulcsot egy másik formátumba ( [lévő rfc4716](https://tools.ietf.org/html/rfc4716)). Előfordulhat, hogy a lévő RFC4716 formátuma nem kompatibilis az összes API-val. Ha tehát a Azure Portal szeretné használni, javasoljuk, hogy másolja a PuTTY ablakban megjelenő nyilvános kulcsot.

    ![Putty nyilvánoskulcs-fájl mentése](./media/ssh-from-windows/save-public-key.png)

6. Ha a titkos kulcsot Putty titkos kulcs formátumú (. PPK fájl) formátumban szeretné menteni, válassza a **titkos kulcs mentése**lehetőséget. Ahhoz, hogy SSH-kapcsolatokat létesítsen a virtuális géppel, szüksége lesz a. PPK fájlra a PuTTY használatával.

    ![A PuTTY titkos kulcs fájljának mentése](./media/ssh-from-windows/save-ppk-file.png)

    Ha az OpenSSH formátumban szeretné menteni a titkos kulcsot, a számos SSH-ügyfél által használt titkos kulcs formátumát válassza a **konverziók**  >  **Exportálás OpenSSH-kulcs**lehetőséget.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Nyilvános SSH-kulcs megadása virtuális gép telepítésekor

SSH-kulcsokat használó Linux rendszerű virtuális gép létrehozásához adja meg az SSH nyilvános kulcsát a virtuális gép Azure Portal vagy más módszerekkel történő létrehozásakor.

Az alábbi példa bemutatja, hogyan másolja és illessze be ezt a nyilvános kulcsot a Azure Portalba Linux rendszerű virtuális gép létrehozásakor. A nyilvános kulcsot általában az új virtuális gép ~/.ssh/authorized_key könyvtára tárolja.

   ![Nyilvános kulcs használata, amikor virtuális gépet hoz létre a Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

Az egyik lehetőség, hogy SSH-kapcsolatokat létesítsen a Linux rendszerű virtuális géppel a Windowsból, hogy SSH-ügyfelet használjon. Ez az előnyben részesített módszer, ha egy SSH-ügyfél van telepítve a Windows rendszerére, vagy ha az SSH-eszközöket használja a Bashben Azure Cloud Shell. Ha a GUI-alapú eszközt részesíti előnyben, a PuTTY segítségével csatlakozhat.  

### <a name="use-an-ssh-client"></a>SSH-ügyfél használata
Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren lévő titkos kulccsal, SSH-val a virtuális géphez a virtuális gép IP-címét vagy DNS-nevét használva. Cserélje le az *azureuser* és a *myvm.westus.cloudapp.Azure.com* parancsot a következő parancsra a rendszergazdai felhasználónévvel és a teljes TARTOMÁNYNÉVVEL (vagy IP-címmel):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor beállította a hozzáférési kódot, akkor adja meg a jelszót, amikor a rendszer a bejelentkezési folyamat során kéri.

Ha a virtuális gép az igény szerinti hozzáférési szabályzatot használja, a virtuális géphez való kapcsolódáshoz a hozzáférést kell kérnie. Az igény szerinti szabályzattal kapcsolatos további információkért lásd: [virtuális gépek hozzáférésének kezelése az igény szerinti házirend használatával](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>A PuTTY-vel való kapcsolat

Ha telepítette a [Putty letöltési csomagot](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) , és korábban létrehozta a PuTTY titkos kulcs (. PPK) fájlt, a PuTTY segítségével csatlakozhat egy linuxos virtuális géphez.

1. Indítsa el a PuTTY-t.

2. Adja meg a virtuális gép állomásnevét vagy IP-címét a Azure Portal:

    ![Új Putty-kapcsolatok megnyitása](./media/ssh-from-windows/putty-new-connection.png)

3. Válassza ki a **kapcsolatok**  >  **SSH**-  >  **hitelesítésének** kategóriáját. Keresse meg és válassza ki a PuTTY titkos kulcsát (. PPK-fájl):

    ![Válasszon Putty titkos kulcsot a hitelesítéshez](./media/ssh-from-windows/putty-auth-dialog.png)

4. Kattintson a **Megnyitás** gombra a virtuális géphez való kapcsolódáshoz.

## <a name="next-steps"></a>További lépések

* Az SSH-kulcsok használatának részletes lépéseiről, lehetőségeiről és speciális példáit az [SSH-kulcspár létrehozásának részletes lépései](create-ssh-keys-detailed.md)című cikkben tekintheti meg.

* A PowerShellt Azure Cloud Shell is használhatja SSH-kulcsok létrehozásához és SSH-kapcsolatok létrehozásához Linux rendszerű virtuális gépekhez. Tekintse meg a [PowerShell](../../cloud-shell/quickstart-powershell.md#ssh)rövid útmutatóját.

* Ha az SSH használatával nem tud csatlakozni a Linux rendszerű virtuális gépekhez, tekintse meg [az SSH-kapcsolatok Azure Linux rendszerű virtuális géphez](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)való kapcsolódásával kapcsolatos problémát.
