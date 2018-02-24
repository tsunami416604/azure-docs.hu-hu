---
title: "Azure-SSIS integrációs futásidejű csatlakoztatása egy virtuális hálózati |} Microsoft Docs"
description: "Útmutató: Azure-SSIS integrációs futásidejű csatlakoztatása az Azure virtuális hálózat."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 6f0038ea750d05b8d59bde07b54cb4aa22ab71b9
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Egy Azure-SSIS-integrációs futásidejű csatlakoztatása egy virtuális hálózatot
Az Azure-SSIS-integrációs futásidejű (IR) csatlakoztassa egy Azure virtuális hálózatra, a következő esetekben: 

- Az SQL Server Integration Services (SSIS) katalógus adatbázis, az Azure SQL Database felügyelt-példányt (magán előnézetben) virtuális hálózatban üzemeltet.
- Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.

 Az Azure Data Factory (előzetes verzió) 2-es lehetővé teszi az Azure-SSIS-integrációs futásidejű csatlakoztatása a klasszikus üzembe helyezési modellt vagy az Azure Resource Manager telepítési modell használatával létrehozott virtuális hálózatban. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általában elérhetőségével (GA), 1 verziójának használatakor tekintse meg a [Data Factory 1-es verziójú dokumentáció](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárolókhoz.
Ha SSIS-csomagok hozzáférést, csak a nyilvános felhő, nem kell az Azure-SSIS infravörös csatlakoztatása egy virtuális hálózatot. SSIS-csomagok hozzáférést, a helyszínen, ha az Azure-SSIS infravörös a helyszíni hálózathoz csatlakozó virtuális hálózathoz kell csatlakoztatni. 

Ha a SSIS katalógus egy Azure SQL Database-példány, amely nincs a virtuális hálózatban helyezkedik el, akkor nyissa meg a megfelelő portokat. 

Ha az SSIS-katalógus SQL felügyelt adatbázispéldány virtuális hálózatban, csatlakozhat egy Azure-SSIS-IR számára:

- Az azonos virtuális hálózatban.
- Hálózatok közötti kapcsolattal rendelkezik, amely rendelkezik az SQL-adatbázis felügyelt példány egy másik virtuális hálózat. 

A virtuális hálózat a klasszikus üzembe helyezési modellt vagy az Azure Resource Manager telepítési modell telepíthető. Ha Ön készül, hogy csatlakozott az Azure-SSIS-IR a *azonos virtuális hálózatban* , amely rendelkezik az SQL-adatbázis felügyelt példány, ügyeljen arra, hogy az Azure-SSIS infravörös egy *másik alhálózat* tartozó rendelkezik az SQL-adatbázishoz Felügyelt példány.   

Az alábbi szakaszokban további részleteket.

Az alábbiakban néhány fontos tényezőt figyelembe venni: 

- Ha nincs meglévő a helyszíni hálózathoz csatlakozó virtuális hálózati, akkor először létre kell hoznia egy [Azure Resource Manager virtuális hálózati](../virtual-network/quick-create-portal.md#create-a-virtual-network) vagy egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS-integráció futásidejű való csatlakozáshoz. Ezt követően konfigurálja a pont-pont [VPN gateway-kapcsolatot](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) és a helyszíni hálózat a virtuális hálózat közötti kapcsolat.
- Ha egy meglévő Azure Resource Manager vagy a klasszikus virtuális hálózatot a helyszíni hálózat és az Azure-SSIS-IR ugyanazon a helyen lévő csatlakozik, az infravörös csatlakozhat a virtuális hálózaton.
- Ha egy létező klasszikus virtuális hálózatot az Azure-SSIS-IR egy másik helyen lévő a helyszíni hálózathoz kapcsolódik, először létrehozhat egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS-IR való csatlakozáshoz. Ezt követően konfigurálja a [klasszikus-klasszikus virtuális hálózati](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) kapcsolat. Vagy létrehozhat egy [Azure Resource Manager virtuális hálózati](../virtual-network/quick-create-portal.md#create-a-virtual-network) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Konfigurálja a [klasszikus Azure erőforrás-kezelő virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.
- Ha egy meglévő Azure Resource Manager virtuális hálózatot az Azure-SSIS-IR keresztül kapcsolódik a helyszíni hálózat egy másik helyen van, akkor először létrehozhat egy [Azure Resource Manager virtuális hálózati](../virtual-network/quick-create-portal.md##create-a-virtual-network) az az Azure-SSIS Infravörös való csatlakozáshoz. Ezt követően konfigurálja az Azure Resource Manager-Azure Resource Manager virtuális hálózati kapcsolat. Másik lehetőségként létrehozhat egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) csatlakozni az Azure-SSIS-IR. Ezt követően konfigurálja a [klasszikus Azure erőforrás-kezelő virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.

## <a name="domain-name-services-server"></a>Domain Name Services server 
Ha szeretné használni a saját tartományi szolgáltatásokra (DNS) kiszolgáló tartományhoz az Azure-SSIS-integrációs futásidejű virtuális hálózatban, kövesse az útmutatást [győződjön meg arról, hogy a virtuális hálózat az Azure-SSIS integrációs futásidejű csomópontjai oldhatja Azure végpontok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Hálózati biztonsági csoport
Ha kell megvalósítani a hálózati biztonsági csoport (NSG) segítségével az Azure-SSIS-integrációs futásidejű virtuális hálózatban, engedélyezze a bejövő/kimenő forgalom az alábbi portokon keresztül:

| Portok | Irány | Átviteli protokoll | Cél | Bejövő forrása/kimenő cél |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (ha az infravörös csatlakoztatása a klasszikus virtuális hálózatot)<br/><br/>29876, 29877 (ha az infravörös csatlakoztatása az Azure Resource Manager virtuális hálózat) | Bejövő | TCP | Azure-szolgáltatásokkal kommunikálni a csomópontok a virtuális hálózat az Azure-SSIS integrációs futtatókörnyezet ezeket a portokat használja. | Internet | 
| 443 | Kimenő | TCP | A virtuális hálózat az Azure-SSIS integrációs futásidejű csomópontjai Azure-szolgáltatásokkal, mint az Azure Storage és az Azure Event Hubs eléréséhez használja ezt a portot. | Internet | 
| 1433<br/>11000-11999<br/>14000-14999  | Kimenő | TCP | A virtuális hálózat az Azure-SSIS integrációs futásidejű csomópont (erre a célra nincs SQL adatbázis felügyelt példány által üzemeltetett SSISDB vonatkozik.) az Azure SQL Database-kiszolgáló által üzemeltetett SSISDB eléréséhez ezeket a portokat használja | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure-portálon (Data Factory UI)
Ez a szakasz bemutatja, hogyan lehet egy meglévő Azure-SSIS-futtatókörnyezet egy virtuális hálózat (klasszikus vagy az Azure Resource Manager) az Azure portál és a Data Factory felhasználói felület használatával. Először meg kell a virtuális hálózat megfelelő konfigurálása az Azure-SSIS-IR csatlakoztatása előtt. Nyissa meg a virtuális hálózat (klasszikus vagy az Azure Resource Manager) típusa a következő két szakasz egyikével. Ezt követően a harmadik szakasz az Azure-SSIS-IR csatlakoztatása a virtuális hálózat. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Klasszikus virtuális hálózat konfigurálása a portál használatával
Virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakozhat hozzá kell.

1. Indítsa el a Microsoft Edge vagy a Google Chrome. A Data Factory felhasználói felület jelenleg csak az alábbi böngészők használata támogatott.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
3. Válassza ki **további szolgáltatások**. Szűrhet, és válassza ki **virtuális hálózatok (klasszikus)**.
4. Szűrhet, és a listában jelölje ki a virtuális hálózat. 
5. Az a **virtuális hálózat (klasszikus)** lapon jelölje be **tulajdonságok**. 

    ![Klasszikus virtuális hálózati erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Válassza a Másolás gombra **erőforrás-azonosító** az erőforrás-azonosító, a klasszikus hálózat másolása a vágólapra. Mentse az Azonosítót a OneNote-bA vagy egy fájlt a vágólapról.
6. Válassza ki **alhálózatok** a bal oldali menüben. Győződjön meg arról, hogy hány **elérhető címek** nagyobb, mint az Azure-SSIS-integrációs futásidejű csomópontján.

    ![A virtuális hálózaton rendelkezésre álló címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Csatlakozás **MicrosoftAzureBatch** számára a **klasszikus virtuális gép közreműködő** szerepkör a virtuális hálózat.

    a. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali menüben, majd válassza a **Hozzáadás** az eszköztáron. 
       !["Hozzáférés-vezérlés" és "Hozzáadás" gombok](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Az a **engedélyek hozzáadása** lapon jelölje be **klasszikus virtuális gép közreműködő** a **szerepkör**. Beillesztés **ddbf3205-c6bd-46ae-8127-60eb93363864** a a **válasszon** mezőbe, majd válassza ki **Microsoft Azure Batch** a keresési eredmények listájáról.   
       !["Engedélyek hozzáadása" lapon a keresési eredmények](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Válassza ki **mentése** menti a beállításokat, és zárja be a lapot.  
       ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Ellenőrizze, hogy látható **Microsoft Azure Batch** közreműködők listájában.  
       ![Azure Batch hozzáférés megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Győződjön meg arról, hogy az Azure Batch-szolgáltató regisztrálva van az Azure-előfizetés, amely rendelkezik a virtuális hálózat. Vagy az Azure Batch-szolgáltató regisztrálása. Ha már rendelkezik Azure Batch-fiók az előfizetéshez, majd az előfizetés az Azure Batch van regisztrálva.

   a. Azure-portálon, válassza ki a **előfizetések** a bal oldali menüben.

   b. Válassza ki előfizetését.

   c. Válassza ki **erőforrás-szolgáltató** a bal oldali, és ellenőrizze, hogy **Microsoft.Batch** egy regisztrált szolgáltató.     
      !["Regisztrált" állapotának megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem lát **Microsoft.Batch** a listában, regisztrálja őket, [üres Azure Batch-fiók létrehozása](../batch/batch-account-create-portal.md) az előfizetésben. Később is törli. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Az Azure Resource Manager virtuális hálózat konfigurálása a portál használatával
Virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakozhat hozzá kell.

1. Indítsa el a Microsoft Edge vagy a Google Chrome. A Data Factory felhasználói felület jelenleg csak az adott webböngésző használata támogatott.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
3. Válassza ki **további szolgáltatások**. Szűrhet, és válassza ki **virtuális hálózatok**.
4. Szűrhet, és a listában jelölje ki a virtuális hálózat. 
5. Az a **virtuális hálózati** lapon jelölje be **tulajdonságok**. 
6. Válassza a Másolás gombra **erőforrás-azonosító** az erőforrás-azonosítója a virtuális hálózat másolása a vágólapra. Mentse az Azonosítót a OneNote-bA vagy egy fájlt a vágólapról.
7. Válassza ki **alhálózatok** a bal oldali menüben. Győződjön meg arról, hogy hány **elérhető címek** nagyobb, mint az Azure-SSIS-integrációs futásidejű csomópontján.
8. Győződjön meg arról, hogy az Azure Batch-szolgáltató regisztrálva van az Azure-előfizetés, amely rendelkezik a virtuális hálózat. Vagy az Azure Batch-szolgáltató regisztrálása. Ha már rendelkezik Azure Batch-fiók az előfizetéshez, majd az előfizetés az Azure Batch van regisztrálva.

   a. Azure-portálon, válassza ki a **előfizetések** a bal oldali menüben.

   b. Válassza ki előfizetését. 
   
   c. Válassza ki **erőforrás-szolgáltató** a bal oldali, és ellenőrizze, hogy **Microsoft.Batch** egy regisztrált szolgáltató.     
      !["Regisztrált" állapotának megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem lát **Microsoft.Batch** a listában, regisztrálja őket, [üres Azure Batch-fiók létrehozása](../batch/batch-account-create-portal.md) az előfizetésben. Később is törli.

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Az Azure-SSIS infravörös csatlakoztatása egy virtuális hálózatot


1. Indítsa el a Microsoft Edge vagy a Google Chrome. A Data Factory felhasználói felület jelenleg csak az adott webböngésző használata támogatott.
2. Az a [Azure-portálon](https://portal.azure.com), jelölje be **adat-előállítók** a bal oldali menüben. Ha nem lát **adat-előállítók** válassza a menü **további szolgáltatások**, majd válassza **adat-előállítók** a a **ESZKÖZINTELLIGENCIA + ANALITIKA**szakasz. 
    
   ![Adat-előállítók listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. A listában jelölje ki a data factory az Azure-SSIS-integráció futtatási idő mellett. A data factory a kezdőlap talál. Válassza ki a **Szerző & telepítés** csempére. A Data Factory felhasználói felületén külön lapon látható. 

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. A Data Factory felhasználói felületén, váltson a **szerkesztése** lapon jelölje be **kapcsolatok**, és váltson a **integrációs futtatókörnyezetek** lapon. 

   !["Integrációs futtatókörnyezetek" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Ha az Azure-SSIS-IR fut, integrációs futásidejű listájában válassza ki a **leállítása** gombra a **műveletek** az Azure-SSIS infravörös oszlopban Az IR nem szerkeszthető, amíg le nem állítják azt. 

   ![Állítsa le az infravörös](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Integrációs futásidejű listájából válassza ki a **szerkesztése** gombra a **műveletek** az Azure-SSIS infravörös oszlopban

   ![Az integrációs futásidejű szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Az a **általános beállítások** oldalán a **integrációs futásidejű telepítő** ablakban válassza ki **következő**. 

   ![Az IR-telepítő általános beállításai](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Az a **SQL-beállítások** lapon adja meg a rendszergazdai jelszót, és válassza ki **következő**.

   ![Az IR-telepítő SQL Server-beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Az a **speciális beállítások** lapon, a következő műveleteket hajthatja végre: 

   a. Jelölje be a **egy Vnetet az Azure-SSIS integráció a futási időben való csatlakozáshoz, és az Azure-szolgáltatások engedélyezése a virtuális hálózat engedélyek/beállítások kiválasztása**.

   b. A **típus**, adja meg, hogy a virtuális hálózat a klasszikus virtuális hálózatot, vagy egy Azure Resource Manager virtuális hálózatot. 

   c. A **VNet neve**, válassza ki a virtuális hálózatot.

   d. A **alhálózati név**, jelölje ki az alhálózatot a virtuális hálózat.

   e. Válassza ki **frissítés**. 

   ![Az IR-telepítő speciális beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Most, megkezdheti a IR segítségével a **Start** gombra a **műveletek** az Azure-SSIS infravörös oszlopban Egy Azure-SSIS infravörös elindításához körülbelül 20 percet vesz igénybe 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása
Virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakozhat hozzá kell. Automatikus konfigurálása az Azure-SSIS integrációs futásidejű való csatlakozáshoz a virtuális hálózat virtuális hálózati engedélyek/beállításait, vegye fel a következő parancsfájlt:

```powershell
# Register to the Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Hozzon létre egy Azure-SSIS-IR, és csatlakoztassa a virtuális hálózathoz
Hozzon létre egy Azure-SSIS-IR, és egyszerre egy virtuális hálózathoz csatlakozzon hozzá. A teljes parancsfájl és utasításokat lásd: [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Egy meglévő Azure-SSIS-IR csatlakoztatása egy virtuális hálózatot
A parancsfájl a [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md) cikkből megtudhatja, hogyan hozzon létre egy Azure-SSIS-IR, és ugyanazt a parancsfájlt a virtuális hálózathoz csatlakozzon hozzá. Ha egy meglévő Azure-SSIS-IR, a következő lépésekkel csatlakozzon hozzá a virtuális hálózathoz: 

1. Állítsa le az Azure-SSIS infravörös
2. Konfigurálja az Azure-SSIS infravörös a virtuális hálózathoz csatlakoztatni. 
3. Indítsa el az Azure-SSIS infravörös 

### <a name="define-the-variables"></a>A változók megadása

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Állítsa le az Azure-SSIS infravörös
Állítsa le az Azure-SSIS-integrációs futásidejű, mielőtt egy virtuális hálózathoz csatlakozik. Ez a parancs kiadott összes csomópontját, és leállítja a számlázási:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Csatlakozás az Azure-SSIS infravörös virtuális hálózati beállításainak konfigurálása
Regisztráljon az Azure Batch erőforrás-szolgáltató:

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Az Azure-SSIS infravörös konfigurálása
Az Azure-SSIS integrációs futásidejű való csatlakozáshoz a virtuális hálózat konfigurálásához futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancs: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Indítsa el az Azure-SSIS infravörös
Az Azure-SSIS-integrációs futásidejű indításához futtassa a következő parancsot: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Ez a parancs 20-30 percig tart.

## <a name="next-steps"></a>További lépések
Az Azure-SSIS futásidejű kapcsolatos további információkért lásd a következő témaköröket: 

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást integrációs futtatókörnyezetek általában, beleértve az Azure-SSIS infravörös 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk részletesen hozzon létre egy Azure-SSIS infravörös Az Azure SQL-adatbázis segítségével a SSIS-katalógust. 
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk kibővíti az oktatóanyag, és utasításokkal szolgál az Azure SQL adatbázis felügyelt példány (magán előnézetben) használatával, és az infravörös csatlakoztatása egy virtuális hálózatot. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutatja, hogyan horizontális felskálázás az Azure-SSIS-IR csomópontok hozzáadásával. 
