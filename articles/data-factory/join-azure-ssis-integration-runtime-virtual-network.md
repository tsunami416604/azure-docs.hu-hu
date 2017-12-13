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
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: aa570379890023c83383d291aa5d57fb79b2d5aa
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Egy Azure-SSIS-integrációs futásidejű csatlakoztatása egy virtuális hálózatot
Azure-SSIS integrációs futásidejű (IR) kell illesztés egy Azure virtuális hálózathoz (VNet), ha a következő feltételek valamelyike teljesül: 

- Az SSIS-katalógusadatbázis egy olyan felügyelt SQL Server-példányon (privát előzetes verzió) fut, amely egy virtuális hálózat része.
- Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.

 Az Azure Data Factory 2.(előzetes) verzió használatával csatlakoztathatja az Azure-SSIS integrációs modult egy klasszikus virtuális hálózathoz. Azure Resource Manager virtuális hálózat jelenleg nem támogatott. Azonban használhat az körül látható a következő szakaszban. 

 > [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárolókhoz
Ha SSIS-csomagok hozzáférést, csak a nyilvános felhő, nem kell Azure-SSIS-IR csatlakoztatása egy virtuális hálózatot. SSIS-csomagok hozzáférést, a helyszínen, ha Azure-SSIS-IR kell csatlakoztatnia egy Vnetet, amely a helyszíni hálózathoz csatlakozik. Ha a SSIS-katalógus az Azure SQL-adatbázis, amely nincs a Vneten belül helyezkedik el, akkor nyissa meg a megfelelő portokat. A SSIS-katalógus Azure SQL felügyelt példányon, amely a klasszikus Vneten belül helyezkedik el, ha ugyanazt a klasszikus virtuális hálózatot (vagy) egy másik klasszikus virtuális hálózat, amely osztály-klasszikus virtuális hálózat kapcsolattal rendelkezik, amely rendelkezik az Azure SQL-felügyelt példány egy Azure-SSIS-IR csatlakozhat. Az alábbi szakaszokban további részleteket.

Az alábbiakban néhány fontos tényezőt figyelembe venni: 

- Ha nincs meglévő VNet a helyszíni hálózathoz csatlakozni, először létre kell hoznia egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Ezt követően konfigurálja a pont-pont [VPN gateway-kapcsolatot](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) és a helyszíni hálózat az adott virtuális hálózat közötti kapcsolat.
- Ha egy meglévő, klasszikus virtuális hálózatot, a helyszíni hálózat és az Azure-SSIS-integrációs futásidejű ugyanazon a helyen lévő csatlakozik, az Azure-SSIS-integrációs futásidejű csatlakozhat hozzá.
- Ha egy létező klasszikus virtuális hálózatot az Azure-SSIS-integrációs futásidejű keresztül kapcsolódik a helyszíni hálózat egy másik helyen van, akkor először létrehozhat egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Ezt követően konfigurálja a [klasszikus-klasszikus virtuális hálózat](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) kapcsolat.
- Ha egy meglévő Azure Resource Manager virtuális hálózatot a helyszíni hálózathoz csatlakozni, először létre kell hoznia egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Ezt követően konfigurálja a [klasszikus Azure Resource Manager virtuális hálózat](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.

## <a name="domain-name-services-server"></a>Tartomány neve Services-kiszolgáló 
Ha szeretné használni a saját tartományi szolgáltatásokra (DNS) kiszolgáló egy Vnetet az Azure-SSIS-integrációs futásidejű tartományhoz, kövesse az útmutatást [győződjön meg arról, hogy az Azure-SSIS integrációs futásidejű virtuális csomópontjai oldhatja Azure-végpontok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Hálózati biztonsági csoport
Ha hálózati biztonsági csoport (NSG) megvalósíthatja egy Vnetet az Azure-SSIS-integrációs futásidejű tartományhoz van szüksége, engedélyezi a bejövő/kimenő traffics az alábbi portokon keresztül:

| Portok | Irány | Átviteli protokoll | Cél | Bejövő forrása/kimenő cél |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Bejövő | TCP | Azure-szolgáltatásokkal kommunikálni az Azure-SSIS integrációs futásidejű virtuális csomópontjai ezeket a portokat használja. | Internet | 
| 443 | Kimenő | TCP | Az Azure-SSIS integrációs futásidejű virtuális csomópontjai ezen a porton elérni az Azure-szolgáltatások, például az Azure Storage, az Event Hubs, stb. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Kimenő | TCP | Az Azure-SSIS integrációs futásidejű virtuális csomópontjai ezeket a portokat használja, az Azure SQL Database (Azure SQL felügyelt példány által üzemeltetett SSISDB nem vonatkozik) kiszolgáló által üzemeltetett SSISDB eléréséhez. | Internet | 

## <a name="configure-vnet"></a>Virtuális hálózat konfigurálása
Először konfigurálnia kell a virtuális hálózat használatával a következő módokon léptethet (parancsfájl vs. Azure-portál) előtt egy Azure-SSIS-IR csatlakoztatása a virtuális hálózat. 

### <a name="script-to-configure-vnet"></a>Parancsfájl virtuális hálózat konfigurálása 
Adja hozzá a következő parancsfájlt, és automatikusan konfigurálja a VNet engedélyek/beállításait az Azure-SSIS integráció a futási időben való csatlakozáshoz a virtuális hálózat.

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
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

### <a name="use-portal-to-configure-vnet"></a>Hálózatok konfigurálása a portál használatával
A parancsfájl futtatása a virtuális hálózat konfigurálásának legegyszerűbb módja. Ha nem rendelkezik hozzáférés konfigurálásához, hogy virtuális hálózat vagy az automatikus konfiguráció nem sikerül, a tulajdonos, hogy a vnet / próbálja meg manuálisan konfigurálja őket az alábbi lépéseket:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Az erőforrás-azonosítója az Azure virtuális hálózat található.
 
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
    
        ![jóváhagyás kötegelt regisztrálva](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Ha nem lát `Microsoft.Batch` szerepel a listán, regisztrálja őket, [üres Azure Batch-fiók létrehozása](../batch/batch-account-create-portal.md) az előfizetésben. Később is törli. 

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Hozzon létre egy Azure-SSIS-IR és egy Vnetet hozzá
Hozzon létre egy Azure-SSIS-IR, és csatlakoztassa a virtuális hálózatba egyszerre. A teljes parancsfájl és utasításokat követve hozzon létre egy Azure-SSIS-IR és csatlakoztathassa egyszerre egy virtuális hálózat: [létrehozása Azure-SSIS-IR](create-azure-ssis-integration-runtime.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Egy meglévő Azure-SSIS-IR csatlakoztatása egy virtuális hálózat
A parancsfájl a [létrehozása Azure-SSIS integrációs futásidejű](create-azure-ssis-integration-runtime.md) a cikk bemutatja, hogyan hozzon létre egy Azure-SSIS-IR, és csatlakoztassa azt egy Vnetet az ugyanazt a parancsfájlt. Ha egy meglévő Azure-SSIS, a következő lépésekkel csatlakozzon hozzá a virtuális hálózat. 

1. Állítsa le az Azure-SSIS infravörös
2. Az Azure-SSIS infravörös való csatlakozáshoz a virtuális hálózat konfigurálása. 
3. Indítsa el az Azure-SSIS infravörös 

## <a name="define-the-variables"></a>A változók megadása

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
# Get the following information from the properties page for your Classic Virtual Network in the Azure portal
# It should be in the format: 
# $VnetId = "/subscriptions/<Azure Subscription ID>/resourceGroups/<Azure Resource Group>/providers/Microsoft.ClassicNetwork/virtualNetworks/<Class Virtual Network Name>"
$VnetId = "<Name of your Azure classic virtual netowrk>"
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
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
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

## <a name="start-the-azure-ssis-ir"></a>Indítsa el az Azure-SSIS infravörös
Futtassa a következő parancsot az Azure SSIS integrációs modul indításához: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
A parancs végrehajtása **20–30 percig** is eltarthat.

## <a name="next-steps"></a>Következő lépések
Azure-SSIS futásidejű kapcsolatos további információkért lásd a következő témaköröket: 

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-deploy-ssis-packages-azure.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál a felügyelt Azure SQL-példány (privát előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
