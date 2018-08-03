---
title: Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz |} A Microsoft Docs
description: Útmutató az Azure-SSIS integrációs modul csatlakoztatása Azure-beli virtuális hálózathoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: aa723fb765d4432d9bcdd56e4b520bf00660f84c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444849"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz
Csatlakozás az Azure-SSIS integrációs modulját (IR) az Azure virtual Networkhöz a következő esetekben: 

- Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni. 

- Az SQL Server Integration Services (SSIS) katalógust adatbázis az Azure SQL Database virtuális hálózati szolgáltatás végpontok/Managed Instance (előzetes verzió) üzemelteti. 

 Az Azure Data Factory használatával csatlakoztathatja az Azure-SSIS integrációs modult a klasszikus üzemi modellel vagy az Azure Resource Manager üzemi modell használatával létrehozott virtuális hálózatban. 

## <a name="access-to-on-premises-data-stores"></a>A hozzáférést a helyszíni adattárak
Ha az SSIS-csomagok eléréséhez csak nyilvános felhőalapú adattárolók, nem kell az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz. SSIS-csomagok helyszíni adattárak eléréséhez, ha az Azure-SSIS integrációs modul a helyszíni hálózathoz csatlakoztatott virtuális hálózathoz kell csatlakoztatni. 

Az alábbiakban néhány fontos tudnivalók: 

- Ha nincs meglévő a helyszíni hálózatához csatlakoztatott virtuális hálózati, akkor először hozzon létre egy [Azure Resource Managerbeli virtuális hálózat](../virtual-network/quick-create-portal.md#create-a-virtual-network) vagy egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS integrációs a modul csatlakozni. Ezt követően konfigurálja a site-to-site [VPN gateway-kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) kapcsolat adott virtuális hálózatból a helyszíni hálózathoz. 

- Ha egy meglévő Azure Resource Manager vagy a klasszikus virtuális hálózat ugyanazon a helyen, mint az Azure-SSIS integrációs modul a helyszíni hálózatához csatlakoztatott, csatlakozhat az integrációs modul virtuális hálózathoz. 

- Ha a helyszíni hálózat egy másik helyen lévő csatlakozott az Azure-SSIS integrációs Modult a meglévő klasszikus virtuális hálózaton, először létrehozhat egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) csatlakozni az Azure-SSIS integrációs modul számára. Ezt követően konfigurálja a [klasszikus – klasszikus virtuális hálózat](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) kapcsolat. Vagy létrehozhat egy [Azure Resource Managerbeli virtuális hálózat](../virtual-network/quick-create-portal.md#create-a-virtual-network) számára az Azure-SSIS integrációs modul csatlakozni. Ezután konfigurálja a [klasszikus – Azure Resource Managerbeli virtuális hálózat](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat. 
 
- Ha egy meglévő Azure Resource Manager virtuális hálózatot a helyszíni hálózat egy másik helyen lévő csatlakozott az Azure-SSIS integrációs modul a, először létrehozhat egy [Azure Resource Managerbeli virtuális hálózat](../virtual-network/quick-create-portal.md##create-a-virtual-network) az Azure-SSIS-hez Integrációs modul csatlakozni. Ezt követően konfigurálja egy Azure Resource Manager – Azure Resource Manager virtuális hálózati kapcsolat. Vagy létrehozhat egy [klasszikus virtuális hálózat](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) csatlakozni az Azure-SSIS integrációs modul számára. Ezt követően konfigurálja a [klasszikus – Azure Resource Managerbeli virtuális hálózat](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance-preview"></a>Az SSIS-katalógus adatbázist, az Azure SQL Database virtuális hálózati szolgáltatás végpontok/Managed Instance (előzetes verzió)
Az SSIS katalógus Azure SQL Database-ben üzemeltetett esetén a virtuális hálózati Szolgáltatásvégpontok vagy a felügyelt példány (előzetes verzió), az Azure-SSIS integrációs modul csatlakozhat: 

- Az azonos virtuális hálózatban 
- Egy másik virtuális hálózatot, amely rendelkezik egy Azure SQL Database virtuális hálózati szolgáltatás végpontok/Managed Instance (előzetes verzió) használt hálózatok közötti kapcsolatot 

Ha az Azure-SSIS integrációs modul csatlakoztatása a felügyelt példány ugyanazon a virtuális hálózaton, győződjön meg róla, hogy az Azure-SSIS integrációs modul egy másik alhálózatot, mint a felügyelt példány. Ha az Azure-SSIS integrációs modul csatlakoztatása a felügyelt példány, mint egy másik virtuális hálózatot, javasoljuk, virtuális hálózatok közötti társviszony (amely ugyanabban a régióban legfeljebb) vagy a virtuális hálózat virtuális hálózati kapcsolat. Lásd: [alkalmazását az Azure SQL Database felügyelt példányába való csatlakozás](../sql-database/sql-database-managed-instance-connect-app.md).

A virtuális hálózathoz a klasszikus üzemi modellel vagy az Azure Resource Manager üzemi modell használatával is telepíthető.

A következő szakaszok további részleteket. 

## <a name="requirements-for-virtual-network-configuration"></a>Virtuális hálózati konfiguráció követelményei
-   Győződjön meg arról, hogy `Microsoft.Batch` egy regisztrált szolgáltató az előfizetés, a virtuális hálózati alhálózat, amely az Azure-SSIS integrációs modult. Ha a klasszikus virtuális hálózatot használ, csatlakoztatnia `MicrosoftAzureBatch` a virtuális gépek hagyományos Közreműködője szerepkörhöz, a virtuális hálózaton. 

-   Válassza ki a megfelelő alhálózatot üzemeltetni az Azure-SSIS integrációs modult. Lásd: [válassza ki az alhálózatot](#subnet). 

-   Ha a saját tartománynév-szolgáltatásokat (DNS) kiszolgáló használ a virtuális hálózaton, [tartománynév-szolgáltatásokat server](#dns_server). 

-   Ha az alhálózat egy hálózati biztonsági csoport (NSG) használ, tekintse meg [hálózati biztonsági csoport](#nsg) 

-   Ha Azure Express Route használatával vagy felhasználó által megadott útvonal (UDR) konfigurálása, lásd: [használata az Azure expressroute-on vagy a felhasználó által definiált útvonal](#route). 

-   Győződjön meg arról, hogy az erőforráscsoport, a virtuális hálózat létrehozása, és bizonyos Azure-hálózati erőforrások törlése. Lásd: [erőforráscsoport követelményei](#resource-group). 

### <a name="subnet"></a> Válassza ki az alhálózatot
-   Ne válassza az átjáró-alhálózat egy Azure-SSIS integrációs modul üzembe helyezéséhez, mert dedikált virtuális hálózati átjárókhoz. 

-   Győződjön meg arról, hogy rendelkezik-e elegendő elérhető címtereket Azure-SSIS integrációs modul használatára, válassza ki az alhálózatot. Hagyja üresen, legalább 2 * integrációs modul csomópont elérhető IP-címek számát. Az Azure lefoglalja minden egyes alhálózaton belül néhány IP-cím, és ezek a címek nem használható. Az alhálózatok első és utolsó IP-címét a protokollok megfelelősége érdekében, az Azure-szolgáltatásokhoz használt három további címek számára vannak fenntartva. További információkért lásd: [vannak ezen alhálózatok belüli IP-címek használatával korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Ne használjon egy alhálózatot, amely kizárólag más Azure-szolgáltatások (például az SQL Database felügyelt példány (előzetes verzió), az App Service, stb.) foglalt. 

### <a name="dns_server"></a> Tartomány neve Services-kiszolgáló 
Ha szeretné használni a saját tartománynév-szolgáltatásokat (DNS) kiszolgáló segítségével az Azure-SSIS integrációs modult egy virtuális hálózatban, ellenőrizze, hogy a nyilvános Azure-gazdagép nevét feloldja (például egy Azure Storage blob neve `<your storage account>.blob.core.windows.net`). 

A következő lépéseket javasoljuk: 

-   Konfigurálja az egyéni DNS-kérelmeket továbbítja az Azure DNS. Feloldatlan DNS-rekordok az Azure rekurzív feloldók (168.63.129.16) IP-címét is továbbíthatja, a saját DNS-kiszolgálón. 

-   Az egyéni DNS elsődleges és másodlagos virtuális hálózatok, az Azure DNS beállítása. Regisztrálja a másodlagos DNS-kiszolgáló IP-címét az Azure rekurzív feloldók (168.63.129.16), abban az esetben a saját DNS-kiszolgáló nem érhető el. 

További információ: [névfeloldás saját DNS-kiszolgálót használó](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Hálózati biztonsági csoport
Kell egy hálózati biztonsági csoport (NSG) megvalósítása az Azure-SSIS integrációs modul által csatlakoztatott virtuális hálózat, ha engedélyezi a bejövő/kimenő forgalom az alábbi portokon keresztül: 

| Irány | Átviteli protokoll | Forrás | Forrás porttartomány | Cél | Célporttartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Bejövő | TCP | Internet | * | VirtualNetwork | 29876-os, a 29877-es portot (ha az integrációs modul csatlakoztatása egy Azure Resource Managerbeli virtuális hálózat) <br/><br/>10100, 20100, 30100-as portokat (ha az integrációs modul csatlakoztatása egy klasszikus virtuális hálózat)| A Data Factory szolgáltatás használ a ezeket a portokat a virtuális hálózatban, az Azure-SSIS integrációs modul csomópontján folytatott kommunikációhoz. <br/><br/> Egy NSG-t, vagy nem ad meg, hogy a Data Factory mindig konfigurálja egy NSG-t a csatolva a virtuális gépek, amelyek az Azure-SSIS integrációs modult. a hálózati adapterek (NIC) szintjén Csak bejövő forgalmat a Data Factory IP-címek használata engedélyezett. Akkor is, ha az internetes forgalmat, ezek a portok megnyitásához, amelyek nem a Data Factory IP-címek IP-címekről érkező forgalom le van tiltva, a hálózati adapterek szintjén. |
| Kimenő | TCP | VirtualNetwork | * | Internet | 443 | A virtuális hálózatban, az Azure-SSIS integrációs modul csomópontján el az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs ezen a porton. |
| Kimenő | TCP | VirtualNetwork | * | Az Internet vagy az Sql | 1433-as, 11000-11999, 14000-14999 | Az Azure-SSIS integrációs modul az Azure SQL Database-kiszolgáló – futó SSISDB el ezeket a portokat a virtuális hálózat használatban csomópontjai az erre a célra nem alkalmazható a felügyelt példány (előzetes verzió) futó SSISDB. |
||||||||

### <a name="route"></a> Használja az Azure expressroute-on vagy a felhasználó által megadott útvonal
Csatlakozhat egy [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolatcsoport a virtuális hálózati infrastruktúra, a helyszíni hálózat kiterjesztése az Azure-bA. 

Kényszerített bújtatás használata az egyik gyakran alkalmazott konfiguráció (hirdesse meg egy BGP-útvonalat, a virtuális hálózathoz 0.0.0.0/0) amely kényszeríti kimenő internetes forgalmat a virtuális hálózat folyamatból a helyszíni hálózati berendezésre történő áthaladásra ellenőrzés és naplózás. A forgalom áramlását az Azure-SSIS integrációs modul a virtuális hálózatba a függő Azure Data Factory szolgáltatás közötti kapcsolat megszakad. A megoldás (legalább) meghatározására [felhasználó által megadott útvonalak (udr-EK)](../virtual-network/virtual-networks-udr-overview.md) az alhálózaton, amely tartalmazza az Azure-SSIS integrációs modult. Egy UDR határozza meg az alhálózat-specifikus útvonalakat a BGP-útvonal helyett összes régió megfelel. 

Vagy megadhatja a felhasználó által megadott útvonalak (udr-EK) az alhálózatról, amely az Azure-SSIS integrációs modul egy másik alhálózatot, amely egy virtuális hálózati berendezések egy tűzfal vagy szegélyhálózat (DMZ) gazdagépre történő kimenő internetes forgalom kényszerített, ellenőrzés és naplózás. 

Mindkét esetben a alkalmazni egy 0.0.0.0/0 útvonalat, a következő ugrási típusú **Internet** az alhálózaton, amely az Azure-SSIS integrációs modul úgy, hogy a Data Factory szolgáltatás és a rendszer Azure-SSIS integrációs közötti kommunikáció sikeres. 

![Útvonal hozzáadása](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Ha aggódik az való vizsgálja meg az adott alhálózatról kimenő internetforgalmat, is hozzáadhat egy NSG-szabály kimenő célhelyeket kell korlátozni az alhálózaton [Azure adatközpont IP-címek](https://www.microsoft.com/download/details.aspx?id=41653). 

Lásd: [a PowerShell-szkript](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) példaként. Hogy a parancsfájl futtatásához hetente és naprakész állapotban tarthatja az Azure data center IP-címek listájából. 

### <a name="resource-group"></a> Erőforráscsoport-követelményei
-   Az Azure-SSIS integrációs Modult hozhat létre bizonyos hálózati erőforrásokat a virtuális hálózatnak ugyanabban az erőforráscsoportban kell. Ilyen erőforrások többek között a következőket:
    -   Az Azure load balancer nevű  *<Guid>- azurebatch-cloudserviceloadbalancer*.
    -   Az Azure nyilvános IP-címet, a név  *<Guid>- azurebatch-cloudservicepublicip*.
    -   Egy hálózati munkahelyi biztonsági csoport nevű  *<Guid>- azurebatch-cloudservicenetworksecuritygroup*. 

-   Győződjön meg arról, hogy nem kell minden olyan erőforrás-zárolás használata az erőforráscsoportot vagy előfizetést, amelyhez a virtuális hálózat tartozik. Ha konfigurál egy írásvédettségi zárolása vagy törlési zárolás, indítása és leállítása az integrációs modul előfordulhat, hogy nem sikerül vagy lefagy. 

-   Győződjön meg arról, hogy nem rendelkezik egy Azure-házirendet, amely megakadályozza, hogy a következő erőforrások létrehozott erőforráscsoportot vagy előfizetést, amelyhez a virtuális hálózat tartozik alá: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Az Azure portal (Data Factory felhasználói felülete)
Ez a szakasz bemutatja, hogyan lehet egy meglévő Azure-SSIS modult egy virtuális hálózat (klasszikus vagy az Azure Resource Manager) az Azure portal és a Data Factory felhasználói felületének használatával. Először a virtuális hálózat megfelelő konfigurálása az Azure-SSIS integrációs modul csatlakoztatása előtt. Nyissa meg a virtuális hálózat (klasszikus vagy az Azure Resource Manager) típusa alapján a következő két szakasz valamelyikével. Ezt követően folytassa a harmadik szakasz az Azure-SSIS integrációs modul csatlakoztatása a virtuális hálózat. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Egy Azure Resource Managerbeli virtuális hálózat konfigurálása a portál használatával
Virtuális hálózat konfigurálása előtt, akkor csatlakozhat egy Azure-SSIS integrációs modul kell. 

1. Indítsa el a Microsoft Edge vagy a Google Chrome-ban. A Data Factory felhasználói felületén jelenleg csak az adott webböngésző használata támogatott. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Válassza ki **további szolgáltatások**. Állítson be szűrőt, és válassza ki **virtuális hálózatok**. 

1. Állítson be szűrőt, és válassza ki a virtuális hálózat a listában. 

1. Az a **virtuális hálózati** lapon jelölje be **tulajdonságok**. 

1. Válassza a Másolás gombra **erőforrás-azonosító** az erőforrás-azonosító, a virtuális hálózat másolása a vágólapra. Mentse az Azonosítót a vágólapról a OneNote-ban vagy egy fájlt. 

1. Válassza ki **alhálózatok** a bal oldali menüben. Ellenőrizze, hogy hány **elérhető címek** nagyobb, mint az Azure-SSIS integrációs modul csomópontján. 

1. Győződjön meg arról, hogy az Azure Batch-szolgáltató regisztrálva van az Azure-előfizetést, amely a virtuális hálózat rendelkezik. Másik lehetőségként az Azure Batch-szolgáltató regisztrálásához. Ha már rendelkezik egy Azure Batch-fiókot az előfizetésében, majd az előfizetés az Azure Batch van regisztrálva. (Ha a Data Factory-portálon hoz létre az Azure-SSIS integrációs modul, az Azure Batch-szolgáltató automatikusan regisztrálva van az Ön számára.) 

   a. Az Azure Portalon, válassza ki a **előfizetések** a bal oldali menüben. 

   b. Válassza ki előfizetését. 

   c. Válassza ki **erőforrás-szolgáltatók** a bal oldalon, és ellenőrizze, hogy **Microsoft.Batch** egy regisztrált szolgáltató. 

   ![Erősítse meg a "Regisztrálva" állapota](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem lát **Microsoft.Batch** regisztrálja, a listában lévő [hozzon létre egy üres Azure Batch-fiók](../batch/batch-account-create-portal.md) az előfizetésében. Később is törli. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Egy klasszikus virtuális hálózat konfigurálása a portál használatával
Virtuális hálózat konfigurálása előtt, akkor csatlakozhat egy Azure-SSIS integrációs modul kell. 

1. Indítsa el a Microsoft Edge vagy a Google Chrome-ban. A Data Factory felhasználói felületén jelenleg csak az ezekben a böngészőkben használata támogatott. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Válassza ki **további szolgáltatások**. Állítson be szűrőt, és válassza ki **virtuális hálózatok (klasszikus)**. 

1. Állítson be szűrőt, és válassza ki a virtuális hálózat a listában. 

1. Az a **virtuális hálózat (klasszikus)** lapon jelölje be **tulajdonságok**. 

   ![Klasszikus virtuális hálózat erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Válassza a Másolás gombra **erőforrás-azonosító** az erőforrás-azonosító, a klasszikus hálózat másolása a vágólapra. Mentse az Azonosítót a vágólapról a OneNote-ban vagy egy fájlt. 

1. Válassza ki **alhálózatok** a bal oldali menüben. Ellenőrizze, hogy hány **elérhető címek** nagyobb, mint az Azure-SSIS integrációs modul csomópontján. 

   ![A virtuális hálózaton rendelkezésre álló címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Csatlakozás **MicrosoftAzureBatch** , a **virtuális gépek hagyományos Közreműködője** szerepkör a virtuális hálózathoz. 

    a. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali menüben, és válassza a **Hozzáadás** az eszköztáron. 

    !["Hozzáférés-vezérlés" és "Hozzáadása" gomb](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Az a **engedélyek hozzáadása** lapon jelölje be **virtuális gépek hagyományos Közreműködője** a **szerepkör**. Beillesztés **ddbf3205-c6bd-46ae-8127-60eb93363864** a a **kiválasztása** mezőbe, majd válassza ki **a Microsoft Azure Batch** találatok listájából. 

    !["Engedélyek hozzáadása" lapon a keresési eredmények](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Válassza ki **mentése** a beállítások mentéséhez, majd zárja be a lapot. 

    ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Győződjön meg arról, hogy **a Microsoft Azure Batch** a közreműködők listája. 

    ![Azure Batch-hozzáférés ellenőrzése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Győződjön meg arról, hogy az Azure Batch-szolgáltató regisztrálva van az Azure-előfizetést, amely a virtuális hálózat rendelkezik. Másik lehetőségként az Azure Batch-szolgáltató regisztrálásához. Ha már rendelkezik egy Azure Batch-fiókot az előfizetésében, majd az előfizetés az Azure Batch van regisztrálva. (Ha a Data Factory-portálon hoz létre az Azure-SSIS integrációs modul, az Azure Batch-szolgáltató automatikusan regisztrálva van az Ön számára.) 

   a. Az Azure Portalon, válassza ki a **előfizetések** a bal oldali menüben. 

   b. Válassza ki előfizetését. 

   c. Válassza ki **erőforrás-szolgáltatók** a bal oldalon, és ellenőrizze, hogy **Microsoft.Batch** egy regisztrált szolgáltató. 

   ![Erősítse meg a "Regisztrálva" állapota](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem lát **Microsoft.Batch** regisztrálja, a listában lévő [hozzon létre egy üres Azure Batch-fiók](../batch/batch-account-create-portal.md) az előfizetésében. Később is törli. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Az Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz
1. Indítsa el a Microsoft Edge vagy a Google Chrome-ban. A Data Factory felhasználói felületén jelenleg csak az adott webböngésző használata támogatott. 

1. Az a [az Azure portal](https://portal.azure.com)válassza **adat-előállítók** a bal oldali menüben. Ha nem lát **adat-előállítók** válassza a menü **további szolgáltatások**, és válassza a **adat-előállítók** a a **INTELLIGENCIA és elemzés**szakaszban. 

   ![Data factoryk listájában](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki saját data factoryját az Azure-SSIS integrációs modul az a listáról. A data Factory tekintse meg a kezdőlapján. Válassza ki a **létrehozás és üzembe helyezés** csempére. A Data Factory felhasználói felületének külön lapon láthatja. 

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületén váltson a **szerkesztése** lapon jelölje be **kapcsolatok**, és váltson át a **integrációs modulok** fülre. 

   !["Integrációs modulok" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha az Azure-SSIS integrációs modul fut, az integration runtime listában válassza ki a **leállítása** gombra a **műveletek** oszlopában az Azure-SSIS integrációs modult. Egy integrációs modul nem módosítható, amíg le nem állítják. 

   ![Állítsa le az integrációs modul](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az integration runtime listában válassza ki a **szerkesztése** gombra a **műveletek** oszlopában az Azure-SSIS integrációs modult. 

   ![Az integrációs modul szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Az a **általános beállítások** lapján a **Integration Runtime telepítővarázslójának befejező** ablakban válassza **tovább**. 

   ![Az integrációs modul telepítés általános beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

1. Az a **SQL-beállítások** lapon adja meg a rendszergazdai jelszót, és válassza ki **tovább**. 

   ![Integrációs modul beállítása az SQL Server-beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

1. Az a **speciális beállítások** lapon, tegye a következőket: 

   a. Jelölje be a jelölőnégyzetet a **válassza ki a VNet engedélyezéseinek/beállításainak konfigurálása az Azure-SSIS integrációs modullal történő Összekapcsoláshoz, és engedélyezze az Azure-szolgáltatások virtuális hálózat**. 

   b. A **típus**, válassza ki, hogy a virtuális hálózat egy klasszikus virtuális hálózat vagy egy Azure Resource Managerbeli virtuális hálózat. 

   c. A **virtuális hálózat neve**, válassza ki a virtuális hálózatot. 

   d. A **Alhálózatnév**, válassza ki az alhálózatot a virtuális hálózatban. 

   e. Kattintson a **virtuális hálózat érvényesítési** , és ha sikeres, kattintson a **frissítés**. 

   ![Az integrációs modul telepítés speciális beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Most elkezdheti az integrációs modul használatával a **Start** gombra a **műveletek** oszlopában az Azure-SSIS integrációs modult. Indítsa el az Azure-SSIS integrációs modult. körülbelül 20 – 30 percet vesz igénybe 

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása
Virtuális hálózat konfigurálása előtt az Azure-SSIS integrációs modul csatlakozhat hozzá kell. Az Azure-SSIS integrációs modul csatlakozni a virtuális hálózathoz virtuális hálózat engedélyeinek/beállításainak automatikus konfigurálásához, adja hozzá a következő szkriptet:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Hozzon létre egy Azure-SSIS integrációs Modult, és csatlakoztathatja azt egy virtuális hálózathoz
Hozzon létre egy Azure-SSIS integrációs Modult, és egyszerre egy virtuális hálózathoz csatlakozzon. A teljes szkript és az utasításokért lásd: [hozzon létre egy Azure-SSIS integrációs modul](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Egy meglévő Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz
A parancsfájl a [hozzon létre egy Azure-SSIS integrációs modul](create-azure-ssis-integration-runtime.md) a cikk bemutatja, hogyan hozhat létre egy Azure-SSIS integrációs modul, és ugyanazt a parancsprogramot a virtuális hálózathoz csatlakoztatja. Ha rendelkezik egy meglévő Azure-SSIS integrációs Modult, hajtsa végre az alábbi lépések végrehajtásával csatlakoztassa a virtuális hálózat: 
1. Állítsa le az Azure-SSIS integrációs modult. 
1. Állítsa be az Azure-SSIS integrációs modul csatlakozni a virtuális hálózathoz. 
1. Indítsa el az Azure-SSIS integrációs modult. 

### <a name="define-the-variables"></a>A változók megadása
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Állítsa le az Azure-SSIS integrációs modul
Leállította az Azure-SSIS integrációs modul virtuális hálózathoz csatlakozzon. Ez a parancs kiadja az összes hozzá tartozó csomópont, és leállítja a számlázási:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Az Azure-SSIS integrációs modul csatlakozni a virtuális hálózati beállítások konfigurálása
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
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

### <a name="configure-the-azure-ssis-ir"></a>Az Azure-SSIS integrációs modul konfigurálása
Az Azure-SSIS integrációs modul csatlakozni a virtuális hálózat konfigurálásához futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancsot: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Az Azure-SSIS integrációs modul indítása
Az Azure-SSIS integrációs modul indításához futtassa a következő parancsot: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Ez a parancs a 20-30 percet vesz igénybe.

## <a name="next-steps"></a>További lépések
További információ az Azure-SSIS modult tekintse meg a következő témaköröket: 
- [Az Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást integrációs modulok általában, beleértve az Azure-SSIS integrációs modult. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk részletes utasításokat követve hozzon létre egy Azure-SSIS integrációs modult. Azure SQL Database használ az SSIS-katalógus futtatására. 
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál az Azure SQL Database használata a virtuális hálózati szolgáltatás végpontok/Managed Instance (előzetes verzió) az SSIS-katalógus futtatására, illetve az integrációs modul csatlakoztatása virtuális hálózathoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutatja, hogyan skálázhatja fel horizontálisan a az Azure-SSIS integrációs modul csomópontok hozzáadásával. 
