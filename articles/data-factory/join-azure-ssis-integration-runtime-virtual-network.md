---
title: "Azure-SSIS integrációs futásidejű csatlakoztatása egy virtuális hálózat |} Microsoft Docs"
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
ms.openlocfilehash: 2131aa75dcfb975f11cff9800087c3e4e7170378
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Egy Azure-SSIS-integrációs futásidejű csatlakoztatása egy virtuális hálózatot
Az Azure-SSIS-integrációs futásidejű (IR) csatlakoztassa egy Azure virtuális hálózatot (VNet) a következő esetekben: 

- Az SSIS-katalógusadatbázis egy olyan felügyelt SQL Server-példányon (privát előzetes verzió) fut, amely egy virtuális hálózat része.
- Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.

 Az Azure Data Factory (előzetes verzió) 2-es csatlakoztathatja az Azure-SSIS-integrációs futásidejű klasszikus vagy egy Azure Resource Manager virtuális hálózatot. 

 > [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárolókhoz
Ha SSIS-csomagok hozzáférést, csak a nyilvános felhő, nem kell Azure-SSIS-IR csatlakoztatása egy virtuális hálózatot. SSIS-csomagok hozzáférést, a helyszínen, ha Azure-SSIS-IR kell csatlakoztatnia egy Vnetet, amely a helyszíni hálózathoz csatlakozik. Ha a SSIS-katalógus az Azure SQL-adatbázis, amely nincs a Vneten belül helyezkedik el, akkor nyissa meg a megfelelő portokat. Ha az SSIS-katalógus Azure SQL felügyelt példányon, amely egy Azure Resource Manager virtuális hálózatot, vagy a klasszikus virtuális hálózatot, ugyanazt a virtuális hálózatot (vagy) egy másik virtuális hálózat, amely a VNet – VNet kapcsolattal rendelkezik, amely rendelkezik az Azure SQL-felügyelt példány egy Azure-SSIS-IR csatlakozhat. Az alábbi szakaszokban további részleteket.

Az alábbiakban néhány fontos tényezőt figyelembe venni: 

- Ha nincs meglévő VNet a helyszíni hálózathoz csatlakozni, először létre kell hoznia egy [Azure Resource Manager virtuális hálózat](../virtual-network/quick-create-portal.md#create-a-virtual-network) vagy egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Ezt követően konfigurálja a pont-pont [VPN gateway-kapcsolatot](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) és a helyszíni hálózat az adott virtuális hálózat közötti kapcsolat.
- Meglévő Azure Resource Manager vagy a klasszikus virtuális hálózat csatlakozik a helyszíni hálózat és az Azure-SSIS-IR ugyanazon a helyen található, az infravörös csatlakozhat, hogy a virtuális hálózatba.
- Ha egy létező klasszikus virtuális hálózatot az Azure-SSIS-IR keresztül kapcsolódik a helyszíni hálózat egy másik helyen van, akkor először létrehozhat egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS-IR való csatlakozáshoz. Ezt követően konfigurálja a [klasszikus-klasszikus virtuális hálózat](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) kapcsolat. Vagy létrehozhat egy [Azure Resource Manager virtuális hálózat](../virtual-network/quick-create-portal.md#create-a-virtual-network) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Konfigurálja a [klasszikus Azure Resource Manager virtuális hálózat](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.
- Ha egy meglévő Azure Resource Manager virtuális hálózatot az Azure-SSIS-IR keresztül kapcsolódik a helyszíni hálózat egy másik helyen található, először létrehozhat egy [Azure Resource Manager virtuális hálózat](../virtual-network/quick-create-portal.md##create-a-virtual-network) az Azure-SSIS-IR való csatlakozáshoz. Ezt követően az Azure Resource Manager Azure Resource Manager virtuális hálózat kapcsolat konfigurálása. Másik lehetőségként létrehozhat egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) csatlakozni az Azure-SSIS-IR. Ezt követően konfigurálja a [klasszikus Azure Resource Manager virtuális hálózat](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.

## <a name="domain-name-services-server"></a>Domain Name Services server 
Ha szeretné használni a saját tartományi szolgáltatásokra (DNS) kiszolgáló egy Vnetet az Azure-SSIS-integrációs futásidejű tartományhoz, kövesse az útmutatást [győződjön meg arról, hogy az Azure-SSIS integrációs futásidejű virtuális csomópontjai oldhatja Azure-végpontok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Hálózati biztonsági csoport
Ha hálózati biztonsági csoport (NSG) megvalósíthatja egy Vnetet az Azure-SSIS-integrációs futásidejű tartományhoz van szüksége, engedélyezi a bejövő/kimenő traffics az alábbi portokon keresztül:

| Portok | Irány | Átviteli protokoll | Cél | Bejövő forrása/kimenő cél |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (Ha a klasszikus virtuális hálózaton történő csatlakoztatása IR)<br/><br/>29876, 29877 (ha IR csatlakoztatása az Azure Resource Manager virtuális hálózat) | Bejövő | TCP | Azure-szolgáltatásokkal kommunikálni az Azure-SSIS integrációs futásidejű virtuális csomópontjai ezeket a portokat használja. | Internet | 
| 443 | Kimenő | TCP | Az Azure-SSIS integrációs futásidejű virtuális csomópontjai ezen a porton elérni az Azure-szolgáltatások, például az Azure Storage, az Event Hubs, stb. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Kimenő | TCP | Az Azure-SSIS integrációs futásidejű virtuális csomópontjai ezeket a portokat használja, az Azure SQL Database (Azure SQL felügyelt példány által üzemeltetett SSISDB nem vonatkozik) kiszolgáló által üzemeltetett SSISDB eléréséhez. | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure-portálon (Data Factory UI)
Ez a szakasz bemutatja, hogyan egy meglévő Azure SSIS futtatókörnyezetet csatlakoztatása egy virtuális hálózat (klasszikus vagy Azure Resource Manager) az Azure portál és a Data Factory felhasználói felület használatával. Először a virtuális hálózat megfelelő konfigurálása előtt a Azure SSIS-IR csatlakoztatása a virtuális hálózat számára. Nyissa meg a virtuális hálózat (klasszikus vagy Azure Resource Manager) típusa a következő két szakasz egyikével. Ezt követően a harmadik szakasz az Azure SSIS-IR csatlakoztatása a virtuális hálózat. 

### <a name="use-portal-to-configure-a-classic-vnet"></a>A klasszikus virtuális hálózat konfigurálása a portál használatával
Először a virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakoztatása a virtuális hálózat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **további szolgáltatások**. Szűrhet, és válassza ki **virtuális hálózatok (klasszikus)**.
3. Szűrhet, és válassza ki a **virtuális hálózati** a listában. 
4. A virtuális hálózat (klasszikus) lapon válassza **tulajdonságok**. 

    ![klasszikus virtuális hálózatot az erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Kattintson a Másolás gombra a **erőforrás-azonosító** az erőforrás-azonosító, a klasszikus hálózat másolása a vágólapra. Mentse az Azonosítót a OneNote-bA vagy egy fájlt a vágólapról.
6. Kattintson a **alhálózatok** a bal oldali menüben, és győződjön meg arról, hogy hány **elérhető címek** nagyobb, mint a csomópontok az Azure-SSIS-integrációs futásidejű.

    ![A virtuális hálózaton rendelkezésre álló címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Csatlakozás **MicrosoftAzureBatch** való **klasszikus virtuális gép közreműködő** a vnet szerepkör. 
    1. Hozzáférés-vezérlés (IAM) a bal oldali menüben kattintson, majd **Hozzáadás** az eszköztáron.
    
        ![Hozzáférés-vezérlés -> Hozzáadás](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. A **engedélyek hozzáadása** lapon jelölje be **klasszikus virtuális gép közreműködő** a **szerepkör**. Másolja és illessze be **ddbf3205-c6bd-46ae-8127-60eb93363864** a a **válasszon** szövegmezőbe, majd válassza ki azt **Microsoft Azure Batch** a keresési eredmények listájáról. 
    
        ![Engedélyek hozzáadása - keresés](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Kattintson a Mentés gombra a beállítások mentéséhez és a lap bezárásához.
    
        ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Ellenőrizze, hogy látható **MicrosoftAzureBatch** közreműködők listájában.
    
        ![AzureBatch hozzáférés megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Győződjön meg arról, hogy Azure Batch szolgáltató regisztrálva van az Azure-előfizetés, amely rendelkezik a virtuális hálózatot, vagy az Azure Batch-szolgáltató regisztrálása. Ha már rendelkezik Azure Batch-fiók az előfizetéshez, majd az előfizetés az Azure Batch van regisztrálva.
    1. Az Azure portálon kattintson **előfizetések** a bal oldali menüben. 
    2. Válassza ki a **előfizetés**. 
    3. Kattintson a **erőforrás-szolgáltató** a bal oldali, és ellenőrizze, hogy `Microsoft.Batch` egy regisztrált szolgáltató. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Ha nem lát `Microsoft.Batch` szerepel a listán, regisztrálja őket, [üres Azure Batch-fiók létrehozása](../batch/batch-account-create-portal.md) az előfizetésben. Később is törli. 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>Az Azure Resource Manager virtuális hálózat konfigurálása a portál használatával
Először a virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakoztatása a virtuális hálózat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **további szolgáltatások**. Szűrhet, és válassza ki **virtuális hálózatok**.
3. Szűrhet, és válassza ki a **virtuális hálózati** a listában. 
4. Válassza ki a virtuális hálózat lap **tulajdonságok**. 
5. Kattintson a Másolás gombra a **erőforrás-azonosító** az erőforrás-azonosítója a virtuális hálózat másolása a vágólapra. Mentse az Azonosítót a OneNote-bA vagy egy fájlt a vágólapról.
6. Kattintson a **alhálózatok** a bal oldali menüben, és győződjön meg arról, hogy hány **elérhető címek** nagyobb, mint a csomópontok az Azure-SSIS-integrációs futásidejű.
5. Győződjön meg arról, hogy Azure Batch szolgáltató regisztrálva van az Azure-előfizetés, amely rendelkezik a virtuális hálózatot, vagy az Azure Batch-szolgáltató regisztrálása. Ha már rendelkezik Azure Batch-fiók az előfizetéshez, majd az előfizetés az Azure Batch van regisztrálva.
    1. Az Azure portálon kattintson **előfizetések** a bal oldali menüben. 
    2. Válassza ki a **előfizetés**. 
    3. Kattintson a **erőforrás-szolgáltató** a bal oldali, és ellenőrizze, hogy `Microsoft.Batch` egy regisztrált szolgáltató. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Ha nem lát `Microsoft.Batch` szerepel a listán, regisztrálja őket, [üres Azure Batch-fiók létrehozása](../batch/batch-account-create-portal.md) az előfizetésben. Később is törli.

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>Az Azure SSIS infravörös csatlakoztatása egy virtuális hálózat


1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **adat-előállítók** a bal oldali menüben. Ha nem látja **adat-előállítók** válassza a menü **további szolgáltatások**, jelölje be **adat-előállítók** a a **ESZKÖZINTELLIGENCIA + ANALITIKA** a szakasz. 
    
    ![Adat-előállítók listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. A listában jelölje ki a data factory Azure SSIS-integráció futtatási idő mellett. A data factory a kezdőlap talál. Válassza ki **Szerző & telepítés** csempére. Megjelenik a Data Factory felhasználói felület (UI) egy külön lapján. 

    ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. A Data Factory felhasználói felületén, váltson a **szerkesztése** lapon jelölje be **kapcsolatok**, és váltson a **integrációs futtatókörnyezetek** lapon. 

    ![Integráció futtatókörnyezetek lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Ha az Azure SSIS-IR fut, integrációs futásidejű listájában válassza ki a **leállítása** gombra a **műveletek** az Azure SSIS infravörös oszlopban Az IR nem szerkeszthető, amíg le nem állítják azt. 

    ![Infravörös leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Az integrációs futásidejű listában válassza ki a **szerkesztése** gombra a **műveletek** az Azure SSIS infravörös oszlopban

    ![Integrációs futásidejű szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Az a **általános beállítások** oldalán a **integrációs futásidejű telepítő** ablakban válassza ki **következő**. 

    ![Infravörös telepítő – általános beállításai](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Az a **SQL-beállítások** lapján adja meg a rendszergazda **jelszó**, és válassza ki **következő**.

    ![Infravörös telepítő – SQL-beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Az a **speciális beállítások** lapon, a következő műveleteket hajthatja végre: 

    1. Jelölje be a a **egy Vnetet az Azure-SSIS integráció a futási időben való csatlakozáshoz, és az Azure-szolgáltatások engedélyezése a virtuális hálózat engedélyek/beállítások kiválasztása**. 
    2. A **típus**, adja meg, hogy a virtuális hálózat egy klasszikus virtuális hálózat vagy egy Azure Resource Manager virtuális hálózatot. 
    3. A **VNet neve**, válassza ki a virtuális hálózat.
    4. A **alhálózati név**, jelölje ki az alhálózatot a Vneten belül. 
    5. Válassza ki **frissítés**. 

        ![Infravörös telepítő - speciális beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Most, megkezdheti a IR segítségével a **Start** gombra a **műveletek** az Azure SSIS infravörös oszlopban Egy Azure SSIS infravörös elindításához körülbelül 20 percet vesz igénybe 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>Virtuális hálózat konfigurálása
Először a virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakoztatása a virtuális hálózat. Adja hozzá a következő parancsfájlt, és automatikusan konfigurálja a VNet engedélyek/beállításait az Azure-SSIS integráció a futási időben való csatlakozáshoz a virtuális hálózat.

```powershell
# Register to Azure Batch resource provider
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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Hozzon létre egy Azure-SSIS-IR és egy Vnetet hozzá
Hozzon létre egy Azure-SSIS-IR, és csatlakoztassa a virtuális hálózatba egyszerre. A teljes parancsfájl és utasításokat követve hozzon létre egy Azure-SSIS-IR és csatlakoztathassa egyszerre egy virtuális hálózat: [létrehozása Azure-SSIS-IR](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Egy meglévő Azure-SSIS-IR csatlakoztatása egy virtuális hálózat
A parancsfájl a [létrehozása Azure-SSIS integrációs futásidejű](create-azure-ssis-integration-runtime.md) a cikk bemutatja, hogyan hozzon létre egy Azure-SSIS-IR, és csatlakoztassa azt egy Vnetet az ugyanazt a parancsfájlt. Ha egy meglévő Azure-SSIS, a következő lépésekkel csatlakozzon hozzá a virtuális hálózat. 

1. Állítsa le az Azure-SSIS infravörös
2. Az Azure-SSIS infravörös való csatlakozáshoz a virtuális hálózat konfigurálása. 
3. Indítsa el az Azure-SSIS infravörös 

### <a name="define-the-variables"></a>A változók megadása

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Állítsa le az Azure-SSIS infravörös
Állítsa le az Azure-SSIS-integrációs futásidejű, egy Vnetet való csatlakoztatása előtt. Ez a parancs kiadott összes csomópontját, és leállítja a számlázási.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Csatlakozás az Azure-SSIS infravörös hálózatok beállításainak konfigurálása
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
A Set-AzureRmDataFactoryV2IntegrationRuntime parancsot konfigurálása az Azure-SSIS integrációs futásidejű való csatlakozáshoz a virtuális hálózat: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Indítsa el az Azure-SSIS infravörös
Futtassa a következő parancsot az Azure SSIS integrációs modul indításához: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
A parancs végrehajtása **20–30 percig** is eltarthat.

## <a name="next-steps"></a>További lépések
Azure-SSIS futásidejű kapcsolatos további információkért lásd a következő témaköröket: 

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál a felügyelt Azure SQL-példány (privát előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
