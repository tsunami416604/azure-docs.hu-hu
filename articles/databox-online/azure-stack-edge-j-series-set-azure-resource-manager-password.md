---
title: Azure Resource Manager jelszavának beállítása az Azure Stack Edge Pro GPU-eszközön
description: Ismerteti, hogyan csatlakozhat a Azure Stack Edge Pro GPU-val futó Azure Resource Managerhoz Azure PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: df5fea8101834dae089ab97354c438363321a707
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904494"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Azure Resource Manager jelszavának beállítása Azure Stack Edge Pro GPU-eszközön

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk a Azure Resource Manager jelszavának beállítását ismerteti. Ezt a jelszót kell megadnia, amikor a Azure Resource Manager használatával csatlakozik az eszköz helyi API-khoz.

A jelszó beállítására szolgáló eljárás eltérő lehet attól függően, hogy a Azure Portal vagy a PowerShell-parancsmagokat használja-e. Ezeket az eljárásokat a következő szakaszokban ismertetjük.


## <a name="reset-password-via-the-azure-portal"></a>Jelszó alaphelyzetbe állítása a Azure Portal használatával

1. A Azure Portal lépjen az eszköz kezeléséhez létrehozott Azure Stack Edge-erőforráshoz. Ugrás az **Edge-számításra > első lépések**.

2. A jobb oldali ablaktáblán, a parancssorból válassza az **Edge ARM-jelszó alaphelyzetbe állítása**lehetőséget. 

    ![A EdgeARM felhasználói jelszavának visszaállítása 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. Az **EdgeArm felhasználói jelszó alaphelyzetbe állítása** panelen adjon meg egy jelszót az eszköz helyi API-khoz való kapcsolódáshoz a Azure Resource Manager használatával. Erősítse meg a jelszót, és válassza a **visszaállítás**lehetőséget.

    ![A EdgeARM felhasználói jelszavának visszaállítása 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Jelszó alaphelyzetbe állítása a PowerShell használatával

1. Az Azure Portalon nyissa meg az eszköz kezeléséhez létrehozott Azure Stack Edge-erőforrást. Jegyezze fel a következő paramétereket az **Áttekintés** oldalon.

    - Azure Stack peremhálózati erőforrás neve
    - Előfizetés azonosítója

2. Lépjen a **beállítások > Tulajdonságok menüpontra**. Jegyezze fel a következő paramétereket a **Tulajdonságok** lapon.

    - Erőforráscsoport
    - A rendszer titkosítási kulcsa: válassza a nézet lehetőséget, majd másolja a **titkosítási kulcsot**.

    ![A lekérési-titkosítási kulcs beolvasása](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Azonosítson egy jelszót, amelyet a Azure Resource Managerhoz való kapcsolódáshoz fog használni.

4. Indítsa el a Cloud shellt. Válassza a jobb felső sarokban található ikont:

    ![Cloud Shell indítása](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Előfordulhat, hogy a Cloud Shell elindítása után a PowerShellre kell váltania.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Környezet beállítása. Típus:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Itt látható egy mintakimenet:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Ha régi PS-modulokkal rendelkezik, telepítenie kell azokat.

    `Remove-Module  Az.DataBoxEdge -force`

    Íme egy minta kimenet. Ebben a példában nem voltak telepítve régi modulok.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. A következő parancsok letöltik és futtatnak egy parancsfájlt a PowerShell-modulok telepítéséhez.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. A következő parancsokban meg kell adnia az erőforrás nevét, az erőforráscsoport nevét, a titkosítási kulcsot és az előző lépésben azonosított jelszót.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    A jelszó és a titkosítási kulcs paramétereit biztonságos karakterláncként kell átadni. A következő parancsmagok segítségével alakítsa át a jelszót és a titkosítási kulcsot a karakterláncok védelmére.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    A jelszó alaphelyzetbe állításához használja a fentiekben generált biztonságos karakterláncokat paraméterként a Set-AzDataBoxEdgeUser parancsmagban. Használja ugyanazt az erőforráscsoportot, amelyet az Azure Stack Edge Pro/Data Box Gateway erőforrás létrehozásakor használt.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Itt látható a minta kimenete.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
A Azure Resource Managerhoz való kapcsolódáshoz használja az új jelszót.

## <a name="next-steps"></a>Következő lépések

[Kapcsolódás Azure Resource Managerhoz](azure-stack-edge-j-series-connect-resource-manager.md)
