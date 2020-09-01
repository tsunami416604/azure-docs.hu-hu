---
title: Virtuális gépek üzembe helyezése az Azure Stack Edge-eszközön Azure PowerShell használatával
description: Ismerteti, hogyan lehet virtuális gépeket (VM-ket) létrehozni és kezelni Azure Stack peremhálózati eszközön Azure PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/11/2020
ms.author: alkohli
ms.openlocfilehash: 113f81c2c4209b851f2029bad2202fbb8bbfe103
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083852"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell-script"></a>Virtuális gépek üzembe helyezése Azure Stack peremhálózati eszközön Azure PowerShell parancsfájl használatával

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez az oktatóanyag leírja, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack peremhálózati eszközön egy Azure PowerShell parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdi a virtuális gép létrehozását és felügyeletét a Azure Stack peremhálózati eszközön ezzel a parancsfájllal, meg kell győződnie arról, hogy végrehajtotta a következő lépésekben felsorolt előfeltételeket:

### <a name="for-azure-stack-edge-device-via-the-local-web-ui"></a>Azure Stack peremhálózati eszközön a helyi webes felületen keresztül

1. A Azure Stack peremhálózati eszköz hálózati beállításait az [1. lépés: Azure stack Edge-eszköz konfigurálása című témakörben](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)leírtak szerint végezte el.

2. Hálózati adapter engedélyezése a számítási feladatokhoz. Ez a hálózati adapter IP-címe virtuális kapcsoló létrehozásához használatos a virtuális gép telepítéséhez. Az alábbi lépések végigvezetik a folyamaton:

    1. Lépjen a **számítási beállítások menüpontra**. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog.

        > [!IMPORTANT] 
        > Csak egy portot lehet beállítani a számítási feladatokhoz.

    2. A hálózati adapteren engedélyezze a számítást. Azure Stack Edge létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót.

3. Létrehozta és telepítette az összes tanúsítványt a Azure Stack Edge-eszközön és az ügyfél megbízható legfelső szintű tárolójában. Kövesse a [2. lépés: tanúsítványok létrehozása és telepítése](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)című témakörben ismertetett eljárást.

### <a name="for-your-windows-client"></a>Windows-ügyfél esetén

1. Az Azure konzisztens szolgáltatások virtuális Internet protokollját (VIP) az eszköz helyi webes FELÜLETén, a **hálózat** lapján definiálta. A következő VIP-címet kell hozzáadnia:

    - A gazdagép fájlja az ügyfélen, vagy
    - A DNS-kiszolgáló konfigurációja
    
    > [!IMPORTANT]
    > Javasoljuk, hogy módosítsa a DNS-kiszolgáló konfigurációját a végpont nevének feloldásához.

    1. Indítsa el a **jegyzettömböt** rendszergazdaként (rendszergazdai jogosultság szükséges a fájl mentéséhez), majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .
    
        ![Windows Intéző – fájl](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Adja hozzá a következő bejegyzéseket a **gazdagépek** fájljához a megfelelő értékekkel helyettesítve az eszközön:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        A Storage-fiókhoz megadhatja azt a nevet, amelyet később egy új Storage-fiók létrehozásához használni szeretne a parancsfájl. A parancsfájl nem vizsgálja meg, hogy a Storage-fiók létezik-e.

    3. Használja az alábbi képet a hivatkozáshoz. Mentse a **hosts** fájlt.

        ![tároló fájl a Jegyzettömbben](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Töltse le az](https://aka.ms/ase-vm-powershell) ebben az eljárásban használt PowerShell-szkriptet.

3. Győződjön meg arról, hogy a Windows-ügyfél a PowerShell 5,0-es vagy újabb verzióját futtatja.

4. Győződjön meg arról, hogy a `Azure.Storage Module version 4.5.0` telepítve van a rendszeren. Ezt a modult a [PowerShell-galériaból](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0)kérheti le. A modul telepítéséhez írja be a következőt:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    A telepített modul verziójának ellenőrzéséhez írja be a következőt:

    `Get-InstalledModule -name Azure.Storage`

    A többi verzió-modul eltávolításához írja be a következőt:

    `Uninstall-Module -Name Azure.Storage`

5. [Töltse le a AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy) rendszert a Windows-ügyfélre. Jegyezze fel ezt a helyet, mert a parancsfájl futtatásakor paraméterként adja át.

6. Győződjön meg arról, hogy a Windows-ügyfél TLS 1,2 vagy újabb rendszert futtat.


## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Futtassa a PowerShellt rendszergazdaként.
2. Nyissa meg azt a mappát, amelybe letöltötte a parancsfájlt az ügyfélen.  
3. A parancsfájl futtatásához használja a következő parancsot:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    A parancsfájl futtatása a Windows rendszerű virtuális gép és a Linux rendszerű virtuális gép létrehozásához mutat példát.

    **Windows rendszerű virtuális gépek esetén:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Linux rendszerű virtuális gépek esetén:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. A parancsfájl által létrehozott erőforrások törléséhez használja a következő parancsokat:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Következő lépések

[Virtuális gépek üzembe helyezése Azure PowerShell-parancsmagok használatával](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
