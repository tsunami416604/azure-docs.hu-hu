---
title: Az Azure Linux virtuális gép sablonként rögzítése |} Microsoft Docs
description: Megtudhatja, hogyan rögzítése és a Linux-alapú Azure virtuális gépek (VM) az Azure Resource Manager telepítési modellel létrehozott kép generalize.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 71c60c8d29e4db8aab1932a1bece03396a12e4da
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Azure-on futó Linux virtuális gép rögzítése
Kövesse a cikk generalize és rögzítheti az Azure Linux virtuális gép (VM) a Resource Manager üzembe helyezési modellben. Generalize a virtuális Géphez, amikor eltávolítja a személyes fiók adatait, és készítse elő a virtuális gép képként használni. Ekkor egy általánosított virtuális merevlemezt (VHD) az operációs rendszer, a virtuális merevlemezek, a mellékelt adatok lemez, a lemezkép rögzítése és egy [Resource Manager-sablon](../../azure-resource-manager/resource-group-overview.md) új virtuális gép központi telepítéséhez. Ez a cikk részletezi az Azure CLI 1.0 rendelkező Virtuálisgép-lemezkép rögzítése a virtuális gépek nem felügyelt lemezekkel. Emellett [rögzíteni a virtuális gépek Azure felügyelt lemezt az Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Felügyelt lemezek kezeli az Azure platformon, és nem igényelnek azokról az előkészítő vagy helyen tárolja azokat. További információ: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

A lemezkép használatával virtuális gépek létrehozásához minden új virtuális gép hálózati erőforrások beállítása, és a sablon (JavaScript Object Notation vagy JSON-NÁ, fájl) segítségével központilag telepítenie a rögzített VHD lemezképek. Ezzel a módszerrel replikálhatja a virtuális gép és az aktuális szoftverkonfigurációjának összeállítása hasonló ahhoz, ahogy a lemezképeket használja az Azure piactéren.

> [!TIP]
> Ha szeretne létrehozni egy másolatot a meglévő Linux virtuális gép biztonsági mentését vagy hibakeresés a speciális állapottal, lásd: [másolatot készít egy Azure-on futó Linux virtuális gép](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ha szeretne egy helyszíni virtuális gép Linux virtuális merevlemez feltöltése, tekintse meg és [feltöltése és a Linux virtuális gép létrehozása az egyéni lemezképet](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#before-you-begin) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy teljesülnek-e a következő előfeltételek teljesülését:

* **A Resource Manager üzembe helyezési modellel létrehozott Azure virtuális gép** -Linux virtuális gép még nem hozott létre, ha a [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), a [Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), vagy [Resource Manager-sablonok ](create-ssh-secured-vm-from-template.md). 
  
    Igény szerint konfigurálhatja a virtuális Gépet. Például [adatok lemezek hozzáadása a](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), frissítések és alkalmazások telepítéséhez. 
* **Az Azure CLI** – telepítse a [Azure CLI](../../cli-install-nodejs.md) a helyi számítógépen.

## <a name="step-1-remove-the-azure-linux-agent"></a>1. lépés: Az Azure Linux ügynök eltávolítása
Először futtassa a **waagent** parancsot a **deprovision** paraméter a Linux virtuális Gépre. Ez a parancs törli a fájlokat és adatokat a virtuális gép készen áll a normalizálása legyen. További információkért lásd: a [Azure Linux ügynök felhasználói útmutató](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Csatlakoztassa a Linux virtuális gép SSH-ügyfél.
2. Az SSH-ablakban írja be a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Ez a parancs csak egy virtuális gép rögzítése képként kívánt futtatható. Nem biztos, hogy a kép minden a bizalmas adatok megszűnik, vagy alkalmas terjesztését.
 
3. Típus **y** folytatja. Hozzáadhat a **-force** paraméter a megerősítési lépés elkerülése érdekében.
4. Miután befejezte a parancsot, írja be a **kilépéshez**. Ez a lépés az SSH-ügyfél bezárása után.

## <a name="step-2-capture-the-vm"></a>2. lépés: A virtuális gép rögzítése
Az Azure parancssori felület használatával generalize és a virtuális gép rögzítése. A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők **myResourceGroup**, **myVnet**, és **myVM**.

1. A helyi számítógépről nyissa meg az Azure CLI és [jelentkezzen be az Azure-előfizetéshez](/cli/azure/authenticate-azure-cli). 
2. Győződjön meg arról, hogy az erőforrás-kezelő módban van.
   
    ```azurecli
    azure config mode arm
    ```
3. Állítsa le a virtuális Gépet, amely már platformelőfizetés a következő paranccsal:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. A virtuális Gépet a következő paranccsal generalize:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Most futtassa a **azure virtuális gép rögzítése** parancsot, amely rögzíti a virtuális Gépet. A következő példában a VHD-k a rendszer rögzíti a lemezképet kezdődő neveket **MyVHDNamePrefix**, és a **-t** beállítás adja meg a sablon elérési útját **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > A kép VHD-fájlok létrehozása alapértelmezés szerint a ugyanazt a tárfiókot, amely az eredeti virtuális gép használja. Használja a *ugyanazt a tárfiókot* bármely új virtuális gépek lemezkép alapján hoz létre a virtuális merevlemezek tárolásához. 

6. A rögzített lemezkép helyének megkereséséhez nyissa meg a JSON-sablon egy szövegszerkesztőben. Az a **storageProfile**, található a **uri** , a **kép** található, a **rendszer** tároló. Például az operációs rendszer lemezképe URI hasonlít `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3. lépés: Virtuális gép létrehozása a rögzített lemezképből
Mostantól a sablon a lemezkép használatával hozzon létre egy Linux virtuális Gépet. Ezeket a lépéseket mutatja be az Azure parancssori felület és a virtuális gép létrehozása egy új virtuális hálózat rögzített JSON-fájl sablon használatát.

### <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A sablon használatához először a virtuális hálózat és a hálózati adapter beállítása az új virtuális gép. Javasoljuk, hogy hozzon létre egy erőforráscsoportot ezeket az erőforrásokat, a helyen, a Virtuálisgép-lemezkép tárolására. Futtassa a parancsokat a következő, és az erőforrások és a megfelelő Azure-beli hely (ezek a parancsok a "centralus" jelöli) neve hasonlít:

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>A hálózati adapter az beszerzése
A rögzítés során mentett JSON használatával egy virtuális Gépet a lemezképből telepítéséhez a pufferében azonosító szükséges Szerezheti be, a következő parancs futtatásával:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

A **azonosító** kimenet hasonlít `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Virtuális gép létrehozása
Most futtassa a következő parancsot a virtuális gép létrehozása a rögzített méretű kép. Használja a **-f** paraméterrel adhatja meg a mentett sablon JSON-fájl elérési útját.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

A parancs kimenetében kéri adjon meg egy új virtuális gép neve, a rendszergazda felhasználóneve és jelszava és a korábban létrehozott hálózati adapter azonosítója.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

A következő példában a sikeres telepítés lásd:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>A telepítés ellenőrzése
A virtuális géphez, ellenőrizze a központi telepítés és a használatával az új virtuális gép indítási létrehozott most SSH. SSH-kapcsolaton keresztül csatlakozni az IP-címének a virtuális gép létrehozta a következő parancs futtatásával:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

A nyilvános IP-cím szerepel a parancs kimenetét. Alapértelmezés szerint csatlakozik a Linux virtuális gép által SSH 22-es porton.

## <a name="create-additional-vms"></a>További virtuális gépek létrehozása
A rögzített lemezkép és a sablon segítségével telepítheti további virtuális gépek, az előző szakaszban leírt lépések alapján. Virtuális gépek létrehozása lemezkép alapján egyéb lehetőségei közé tartozik, a következő gyorsindítási sablonon használatával, vagy fut a **azure virtuális gép létrehozása** parancsot.

### <a name="use-the-captured-template"></a>A rögzített sablon használata
A rögzített lemezkép és a sablon használatához kövesse az alábbi lépéseket, (az előző szakaszban ismertetett):

* Ügyeljen arra, hogy a Virtuálisgép-lemezkép ugyanazt a tárfiókot, amelyen a virtuális gép virtuális merevlemez.
* Másolja ki a sablon JSON-fájlt, és adjon meg egy egyedi nevet az operációsrendszer-lemezképet, az új virtuális gép virtuális merevlemez (vagy VHD-k). Például a **storageProfile**a **vhd**, a **uri**, adjon meg egy egyedi nevet a **osDisk** VHD, hasonló `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Hozzon létre egy hálózati adapter azonos, vagy egy másik virtuális hálózatot.
* A központi telepítés a módosított sablon JSON-fájl használatával hozhat létre az erőforráscsoportban, amelyben állítsa be a virtuális hálózat.

### <a name="use-a-quickstart-template"></a>A következő gyorsindítási sablonon használata
Ha azt szeretné, hogy a hálózat beállítása automatikusan amikor létrehoz egy virtuális Gépet a lemezképből, egy sablon adhat meg ezeket az erőforrásokat. Például tekintse meg a [101-vm-a-felhasználó-lemezkép sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) a Githubról. Ez a sablon egy virtuális Gépet hoz létre az egyéni lemezképet és a szükséges virtuális hálózati, a nyilvános IP-cím és a hálózati erőforrásokhoz. A sablon használata az Azure portálon útmutatást lásd: [egy virtuális gép létrehozása a Resource Manager-sablon használatával egyéni lemezkép](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Használja az azure virtuális gép létrehozása parancs
Általában a legegyszerűbb egy Resource Manager-sablon használatával hozzon létre egy virtuális Gépet a lemezképből. Azonban a virtuális Gépet hozhat létre *imperatively* használatával a **azure virtuális gép létrehozása** parancsot a **-Q** (**--lemezkép-urn**) paraméter. Ha ezt a módszert használja, akkor is átadhatja a **-d** (**--operációsrendszer-lemez-vhd**) paramétert adja meg az új virtuális Gépet az operációs rendszer .vhd fájl helyét. Ez a fájl a kép VHD-fájlt tároló tárfióknak a VHD-k tárolóban kell lennie. A parancs másolja a VHD-t az új virtuális gép automatikusan a **VHD-k** tároló.

Futtatása előtt **azure virtuális gép létrehozása** a lemezképpel, kövesse az alábbi lépéseket:

1. Hozzon létre egy erőforráscsoportot, vagy egy meglévő erőforráscsoportot, a központi telepítés azonosítani.
2. Hozzon létre egy nyilvános IP-cím erőforrás és a hálózati erőforrások az új virtuális Gépet. Egy virtuális hálózat, a nyilvános IP-cím és a hálózati adapter létrehozása a parancssori felület használatával lépéseiért lásd: a cikk korábbi részében. (**azure virtuális gép létrehozása** is létrehozhat egy hálózati Adaptert, de csak akkor adhatók át további paraméterek, a virtuális hálózati és alhálózati.)

Ezután futtassa a parancsot, mind az új operációs rendszer VHD-fájlt, és a meglévő lemezkép URI-azonosítók kapott. Ebben a példában a mérete Standard_A1 virtuális gép létrehozása az USA keleti régiójában.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

A további parancssori kapcsolók, futtassa a `azure help vm create`.

## <a name="next-steps"></a>További lépések
A parancssori felület a virtuális gépek kezeléséhez, tekintse meg a feladatokat a [központi telepítése és kezelése a virtuális gépek Azure Resource Manager-sablonok és az Azure parancssori felület használatával](create-ssh-secured-vm-from-template.md).

