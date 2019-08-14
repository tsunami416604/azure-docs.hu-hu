---
title: Az Azure SSIS Integration Runtime csatlakoztatása egy virtuális hálózathoz | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure-SSIS Integration Runtime egy Azure-beli virtuális hálózathoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 49422d73a63f1bcde267aac3a9b75e9977970cc9
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951941"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure SSIS Integration Runtime csatlakoztatása virtuális hálózathoz
Azure Data Factory (ADF) SQL Server Integration Services (SSIS) használatakor az alábbi helyzetekben kell csatlakoztatnia az Azure-SSIS Integration Runtime (IR) egy Azure-beli virtuális hálózathoz: 

- A helyszíni adattárakhoz szeretne csatlakozni az Azure-SSIS IR-SSIS-csomagokból a saját üzemeltetésű IR proxyként való konfigurálása/kezelése nélkül. 

- A SSIS katalógus Azure SQL Database-adatbázist (SSISDB) üzemelteti a virtuális hálózatban található virtuális hálózati szolgáltatás-végpontokkal/felügyelt példányokkal. 

Az ADF lehetővé teszi, hogy az Azure-SSIS IR-t egy, a klasszikus üzemi modellel vagy a Azure Resource Manager üzemi modellel létrehozott virtuális hálózathoz csatlakoztassa. 

> [!IMPORTANT]
> A klasszikus virtuális hálózat jelenleg elavult, ezért használja helyette a Azure Resource Manager virtuális hálózatot.  Ha már használja a klasszikus virtuális hálózatot, akkor a lehető leghamarabb váltson át a Azure Resource Manager virtuális hálózat használatára.

## <a name="access-to-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárakhoz
Ha a SSIS-csomagok csak a nyilvános Felhőbeli adattárakat férnek hozzá, nem kell csatlakoztatnia az Azure-SSIS IR-t egy virtuális hálózathoz. Ha a SSIS-csomagok hozzáférnek a helyszíni adattárakhoz, csatlakoztathatja az Azure-SSIS IR-t egy olyan virtuális hálózathoz, amely a helyszíni hálózathoz csatlakozik, vagy egy saját üzemeltetésű IR-proxyt konfigurálhat az Azure-SSIS IR-hez, lásd: saját üzemeltetésű integrációs modul [konfigurálása proxy az Azure-SSIS IR-](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) cikkhez. Ha az Azure-SSIS IR-t egy virtuális hálózathoz csatlakoztatja, néhány fontos szempontot figyelembe kell venni: 

- Ha a helyszíni hálózathoz nem csatlakozik meglévő virtuális hálózat, először hozzon létre egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) vagy egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS integrációs modulhoz való csatlakozáshoz. Ezután konfiguráljon egy helyek közötti [VPN Gateway-kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy a virtuális hálózat [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -kapcsolatát a helyszíni hálózatra. 

- Ha egy meglévő Azure Resource Manager vagy klasszikus virtuális hálózat van csatlakoztatva a helyszíni hálózathoz az Azure-SSIS IR-vel megegyező helyen, akkor csatlakoztathatja az IR-t a virtuális hálózathoz. 

- Ha egy meglévő klasszikus virtuális hálózat van csatlakoztatva a helyszíni hálózathoz az Azure-SSIS IR-vel egy másik helyen, először hozzon létre egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS IR-hez a csatlakozáshoz. Ezután konfigurálja a [klasszikus – klasszikus virtuális hálózati](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) kapcsolatokat. Vagy létrehozhat egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) is az Azure-SSIS integrációs modulhoz való csatlakozáshoz. Ezután konfiguráljon egy [klasszikus – Azure Resource Manager virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolatokat. 
 
- Ha egy meglévő Azure Resource Manager virtuális hálózat van csatlakoztatva a helyszíni hálózathoz az Azure-SSIS IR-vel egy másik helyen, először létre kell hoznia egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md##create-a-virtual-network) az Azure-SSIS IR-hez a csatlakozáshoz. Ezután konfiguráljon egy Azure Resource Manager – Azure Resource Manager virtuális hálózati kapcsolatokat. Vagy létrehozhat egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS IR-hez a csatlakozáshoz. Ezután konfiguráljon egy [klasszikus – Azure Resource Manager virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolatokat. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>A SSIS-katalógus adatbázisának üzemeltetése Azure SQL Database virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal
Ha a SSIS-katalógus a virtuális hálózati szolgáltatás-végpontokkal vagy a virtuális hálózatban felügyelt példányokkal Azure SQL Database üzemel, akkor az Azure-SSIS IR-t a következőhöz csatlakoztathatja: 

- Ugyanazon a virtuális hálózaton 
- Egy másik virtuális hálózat, amely hálózati és hálózati kapcsolatban áll a felügyelt példányhoz használttal 

Ha a SSIS-katalógust Azure SQL Database virtuális hálózati szolgáltatásbeli végpontokkal futtatja, akkor győződjön meg arról, hogy az Azure-SSIS IR-t ugyanahhoz a virtuális hálózathoz és alhálózathoz csatlakoztatja.

Ha az Azure-SSIS IR-t ugyanahhoz a virtuális hálózathoz csatlakoztatja, mint a felügyelt példányt, győződjön meg arról, hogy az Azure-SSIS IR a felügyelt példánytól eltérő alhálózaton van. Ha az Azure-SSIS integrációs modult egy másik virtuális hálózathoz csatlakoztatja, mint a felügyelt példány, akkor a virtuális hálózat (amely ugyanahhoz a régióhoz van korlátozva) vagy virtuális hálózat virtuális hálózati kapcsolathoz való csatlakoztatását javasoljuk. Lásd: [az alkalmazás Összekötése Azure SQL Database felügyelt példányhoz](../sql-database/sql-database-managed-instance-connect-app.md).

A virtuális hálózat minden esetben csak az Azure Resource Manager üzemi modell használatával telepíthető.

A következő szakaszokban további részleteket talál. 

## <a name="requirements-for-virtual-network-configuration"></a>A virtuális hálózati konfigurációra vonatkozó követelmények
-   Győződjön meg arról `Microsoft.Batch` , hogy a a virtuális hálózati alhálózat előfizetése alá tartozó regisztrált szolgáltató, amely az Azure-SSIS IR-t üzemelteti. Ha klasszikus virtuális hálózatot használ, a virtuális hálózat klasszikus `MicrosoftAzureBatch` virtuálisgép-közreműködői szerepköréhez is csatlakozhat. 

-   Győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. Lásd a [szükséges engedélyeket](#perms).

-   Válassza ki a megfelelő alhálózatot az Azure-SSIS IR üzemeltetéséhez. Lásd: [az alhálózat kiválasztása](#subnet). 

-   Ha a saját domain name Services-(DNS-) kiszolgálót használja a virtuális hálózaton, tekintse meg a [tartománynév-szolgáltatási kiszolgáló](#dns_server)című témakört. 

-   Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton, tekintse meg a [hálózati biztonsági csoport](#nsg) című témakört. 

-   Ha az Azure Express Route vagy a felhasználó által megadott útvonal (UDR) konfigurálását használja, tekintse [meg az Azure ExpressRoute vagy a felhasználó által megadott útvonal használatát](#route)ismertető témakört. 

-   Győződjön meg arról, hogy a virtuális hálózat erőforráscsoport bizonyos Azure hálózati erőforrásokat tud létrehozni és törölni. Tekintse [meg az erőforráscsoport követelményeit](#resource-group). 

-   Ha testreszabja az Azure-SSIS IR-t az [Egyéni telepítés az Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) -ben című cikkben leírtak szerint, az Azure-SSIS IR-csomópontok privát IP-címeket kapnak a 172.16.0.0-172.31.255.255 előre meghatározott tartományból, ezért győződjön meg arról, hogy a magánhálózati IP-cím a virtuális/helyszíni hálózatok címei nem ütköznek ezzel a tartománnyal.

Az alábbi ábra az Azure-SSIS IR szükséges kapcsolatait mutatja be:

![Azure-SSIS integrációs modul](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Szükséges engedélyek

Az Azure-SSIS Integration Runtime létrehozó felhasználónak a következő engedélyekkel kell rendelkeznie:

- Ha a SSIS IR-t egy Azure Resource Manager virtuális hálózathoz csatlakoztatja, két lehetőség közül választhat:

  - Használja a beépített *hálózati közreműködő* szerepkört. Ehhez a szerepkörhöz a _Microsoft. Network/\*_  engedély tartozik, amely a szükségesnél sokkal nagyobb hatókörű.

  - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft. Network\*/virtualNetworks//JOIN/Action_ engedélyt tartalmazza. 

- Ha a SSIS IR-t egy klasszikus virtuális hálózathoz csatlakoztatja, javasoljuk, hogy használja a *klasszikus virtuális gépek beépített közreműködői* szerepkörét. Ellenkező esetben meg kell határoznia egy egyéni szerepkört, amely magában foglalja a virtuális hálózathoz való csatlakozás engedélyét.

### <a name="subnet"></a>Válassza ki az alhálózatot
-   Ne válassza ki az Azure-SSIS Integration Runtime üzembe helyezéséhez szükséges GatewaySubnet, mert a virtuális hálózati átjárók számára van dedikált. 

-   Győződjön meg arról, hogy a kiválasztott alhálózat rendelkezik elegendő szabad hellyel az Azure-SSIS IR használatához. Hagyjon legalább 2 * IR csomópont-számot az elérhető IP-címek közül. Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatának korlátozásai?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Ne használjon olyan alhálózatot, amelyet kizárólag más Azure-szolgáltatások foglalnak magukban (például SQL Database felügyelt példány, App Service stb.). 

### <a name="dns_server"></a>Domain Name Services-kiszolgáló 
Ha saját domain name Services-(DNS-) kiszolgálót kell használnia az Azure-SSIS integrációs modulja által csatlakoztatott virtuális hálózatban, győződjön meg arról, hogy képes a globális Azure-állomásnevek (például egy Azure Storage- `<your storage account>.blob.core.windows.net`blob neve) feloldására. 

A következő lépések ajánlottak: 

-   Konfigurálja az egyéni DNS-t, hogy továbbítsa a kérelmeket a Azure DNS. Feloldatlan DNS-rekordokat továbbíthat az Azure rekurzív resolvers (168.63.129.16) IP-címére a saját DNS-kiszolgálón. 

-   Állítsa be az egyéni DNS-t elsődlegesként, Azure DNS pedig másodlagosként a virtuális hálózathoz. Regisztrálja az Azure rekurzív resolvers (168.63.129.16) IP-címét másodlagos DNS-kiszolgálóként abban az esetben, ha a saját DNS-kiszolgáló nem érhető el. 

További információ: névfeloldás, [amely a saját DNS-kiszolgálóját használja](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Hálózati biztonsági csoport
Ha az Azure-SSIS Integration Runtime által használt alhálózathoz hálózati biztonsági csoportot (NSG) kell megvalósítani, engedélyezze a bejövő/kimenő forgalmat a következő portokon keresztül: 

| Direction | Átviteli protokoll | Source | Forrásoldali porttartomány | Cél | Célport tartománya | Megjegyzések |
|---|---|---|---|---|---|---|
| Bejövő | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (ha az IR-t egy Azure Resource Manager virtuális hálózathoz csatlakoztatja) <br/><br/>10100, 20100, 30100 (ha az IR-t egy klasszikus virtuális hálózathoz csatlakoztatja)| A Data Factory szolgáltatás ezeket a portokat használja a virtuális hálózat Azure-SSIS integrációs moduljának csomópontjaival való kommunikációhoz. <br/><br/> Akár alhálózat szintű NSG hoz létre, Data Factory mindig az Azure-SSIS IR-t futtató virtuális gépekhez csatlakoztatott hálózati adapterek (NIC-EK) szintjén konfigurálja a NSG. A hálózati adapter szintű NSG csak a megadott portok Data Factory IP-címeiről érkező bejövő forgalmat engedélyezi. Még ha a portok az alhálózat szintjén is megnyitják az internetes forgalmat, akkor a nem Data Factory IP-címekről érkező adatforgalmat a hálózati adapter szintjén blokkolja a rendszer. |
| Kimenő | TCP | VirtualNetwork | * | AzureCloud<br/>(vagy nagyobb hatókör, például Internet) | 443 | A virtuális hálózat Azure-SSIS integrációs moduljának csomópontjai ezt a portot használják az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs eléréséhez. |
| Kimenő | TCP | VirtualNetwork | * | Internet | 80 | A virtuális hálózat Azure-SSIS integrációs moduljának csomópontjai ezt a portot használják a tanúsítvány-visszavonási lista internetről való letöltéséhez. |
| Kimenő | TCP | VirtualNetwork | * | SQL<br/>(vagy nagyobb hatókör, például Internet) | 1433, 11000-11999 | A virtuális hálózat Azure-SSIS integrációs moduljának csomópontjai ezeket a portokat használják a Azure SQL Database-kiszolgáló által üzemeltetett SSISDB eléréséhez. Ha az Azure SQL Database kiszolgáló-kapcsolódási házirendje az **átirányítás**helyett **proxyként** van beállítva, akkor csak az 1433-es port szükséges. Ez a kimenő biztonsági szabály nem alkalmazható a virtuális hálózaton a felügyelt példány által üzemeltetett SSISDB. |
||||||||

### <a name="route"></a>Az Azure ExpressRoute vagy a felhasználó által megadott útvonal használata
Az Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) áramkört a virtuális hálózati infrastruktúrához is összekapcsolhatja a helyszíni hálózat Azure-ra való kiterjesztéséhez. 

Az általános konfiguráció a kényszerített bújtatás használata (a virtuális hálózatra vonatkozóan a BGP-útvonal meghirdetése, 0.0.0.0/0), amely a virtuális hálózati forgalomból a helyszíni hálózati berendezés felé irányuló, ellenőrzésre és naplózásra kényszeríti az internetes forgalmat. Ez a forgalmi folyamat megszakítja a kapcsolatot az Azure-SSIS IR-vel a virtuális hálózaton a függő Azure Data Factory szolgáltatásokkal. A megoldás egy (vagy több) [felhasználó által megadott útvonal (UDR)](../virtual-network/virtual-networks-udr-overview.md) definiálása azon az alhálózaton, amely az Azure-SSIS IR-t tartalmazza. A UDR olyan alhálózat-specifikus útvonalakat határoznak meg, amelyeket a BGP-útvonal helyett tiszteletben tartanak. 

Megadhatja a felhasználó által megadott útvonalakat (UDR) is, amelyekkel kényszerítheti a kimenő internetes forgalmat az Azure-SSIS IR-t egy másik alhálózatra, amely egy Virtual Network berendezést tűzfalként vagy DMZ-gazdagépként üzemeltet a vizsgálathoz és a naplózáshoz. 

Mindkét esetben alkalmazzon egy 0.0.0.0/0 útvonalat a következő ugrási típussal **internetként** az Azure-SSIS IR-t futtató alhálózaton, hogy a Data Factory szolgáltatás és az Azure-SSIS közötti kommunikáció sikeres legyen. 

![Útvonal hozzáadása](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Ha aggódik amiatt, hogy elveszti az adott alhálózatról érkező kimenő internetes forgalom vizsgálatának lehetőségét, hozzáadhat egy NSG-szabályt is az alhálózaton, hogy az [Azure adatközpont IP-címeire](https://www.microsoft.com/download/details.aspx?id=41653)korlátozza a kimenő célhelyeket. 

Példaként tekintse meg [ezt a PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) -szkriptet. A szkriptet hetente futtatnia kell, hogy az Azure adatközpont IP-címei naprakészek maradjanak. 

### <a name="resource-group"></a>Az erőforráscsoport követelményei
-   Az Azure-SSIS IR-nek bizonyos hálózati erőforrásokat kell létrehoznia a virtuális hálózattal azonos erőforráscsoporthoz. Ezek az erőforrások többek között a következők:
    -   Egy Azure Load Balancer, amelynek neve  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
    -   Egy Azure-beli nyilvános IP-cím,  *\<GUID >-azurebatch-cloudservicepublicip*névvel.
    -   Egy hálózati biztonsági csoport, amelynek neve  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

-   Győződjön meg arról, hogy nincs olyan erőforrás-zárolása az erőforráscsoport vagy-előfizetés számára, amelyhez a virtuális hálózat tartozik. Ha egy írásvédett zárolást vagy egy törlési zárolást állít be, akkor az IR elindítása és leállítása sikertelen lehet vagy nem válaszol. 

-   Győződjön meg arról, hogy nem rendelkezik Azure-házirenddel, amely megakadályozza, hogy a következő erőforrások jöjjenek létre az erőforráscsoport vagy előfizetés alatt, amelyhez a virtuális hálózat tartozik: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory felhasználói felület)
Ez a szakasz bemutatja, hogyan csatlakozhat egy meglévő Azure-SSIS futtatókörnyezethez egy virtuális hálózathoz (klasszikus vagy Azure Resource Manager) a Azure Portal és a Data Factory felhasználói felület használatával. Először is konfigurálnia kell a virtuális hálózatot, mielőtt csatlakoztatja az Azure-SSIS IR-t. Ugorjon végig a következő két szakaszban a virtuális hálózat típusa alapján (klasszikus vagy Azure Resource Manager). Ezután folytassa a harmadik szakasszal, hogy csatlakozzon az Azure-SSIS IR-hez a virtuális hálózathoz. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager virtuális hálózat konfigurálása a portál használatával
Konfigurálnia kell egy virtuális hálózatot, mielőtt hozzá tud csatlakozni egy Azure-SSIS IR-hez. 

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. A Data Factory felhasználói felület jelenleg csak az adott böngészőkben támogatott. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Válassza a **További szolgáltatások**lehetőséget. A **virtuális hálózatok**szűrése és kiválasztása. 

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt. 

1. A **virtuális hálózat** lapon válassza a **Tulajdonságok**lehetőséget. 

1. Válassza a másolás gombot az **erőforrás** -azonosítóhoz, és másolja a virtuális hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból. 

1. Válassza ki az alhálózatok elemet a bal oldali menüben. Győződjön meg arról, hogy a **rendelkezésre álló címek** száma nagyobb, mint az Azure-SSIS Integration Runtime csomópontjai. 

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésében, akkor az előfizetése regisztrálva lesz a Azure Batchhoz. (Ha az Azure-SSIS IR-t a Data Factory portálon hozza létre, a rendszer automatikusan regisztrálja az Azure Batch szolgáltatót.) 

   a. Azure Portal a bal oldali menüben válassza az előfizetések lehetőséget. 

   b. Válassza ki előfizetését. 

   c. Válassza ki a bal oldali **erőforrás** -szolgáltatókat, és győződjön meg arról, hogy a **Microsoft. batch** egy regisztrált szolgáltató. 

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft. batcht** a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>A portál használata klasszikus virtuális hálózat konfigurálásához
Konfigurálnia kell egy virtuális hálózatot, mielőtt hozzá tud csatlakozni egy Azure-SSIS IR-hez. 

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. A Data Factory felhasználói felület jelenleg csak az alábbi böngészőkben támogatott. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Válassza a **További szolgáltatások**lehetőséget. Szűrje és válassza a **virtuális hálózatok (klasszikus)** lehetőséget. 

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt. 

1. A **virtuális hálózat (klasszikus)** lapon válassza a **Tulajdonságok**lehetőséget. 

   ![Klasszikus virtuális hálózati erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Válassza a másolás gombot az **erőforrás** -azonosítóhoz, és másolja a klasszikus hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból. 

1. Válassza ki az alhálózatok elemet a bal oldali menüben. Győződjön meg arról, hogy a **rendelkezésre álló címek** száma nagyobb, mint az Azure-SSIS Integration Runtime csomópontjai. 

   ![A virtuális hálózatban elérhető címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Csatlakozzon a **MicrosoftAzureBatch** a virtuális hálózat **klasszikus virtuálisgép-közreműködői** szerepköréhez. 

    a. A bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza ki a **szerepkör** -hozzárendelések lapot. 

    !["Hozzáférés-vezérlés" és "Hozzáadás" gomb](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

    c. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **klasszikus virtuális gépek közreműködői** szerepkört. Illessze be a **ddbf3205-c6bd-46ae-8127-60eb93363864** a **Select (kiválasztás** ) mezőbe, majd válassza a **Microsoft Azure batch** elemet a találatok listájában. 

    ![Keresési eredmények a "szerepkör-hozzárendelés hozzáadása" oldalon](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. A beállítások mentéséhez és a lap bezárásához válassza a **Mentés** lehetőséget. 

    ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Ellenőrizze, hogy megjelenik-e **Microsoft Azure batch** a közreműködők listájában. 

    ![Azure Batch hozzáférésének megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésében, akkor az előfizetése regisztrálva lesz a Azure Batchhoz. (Ha az Azure-SSIS IR-t a Data Factory portálon hozza létre, a rendszer automatikusan regisztrálja az Azure Batch szolgáltatót.) 

   a. Azure Portal a bal oldali menüben válassza az előfizetések lehetőséget. 

   b. Válassza ki előfizetését. 

   c. Válassza ki a bal oldali **erőforrás** -szolgáltatókat, és győződjön meg arról, hogy a **Microsoft. batch** egy regisztrált szolgáltató. 

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft. batcht** a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Csatlakozás az Azure-SSIS IR-hez egy virtuális hálózathoz
1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. A Data Factory felhasználói felület jelenleg csak az adott böngészőkben támogatott. 

1. A [Azure Portal](https://portal.azure.com)a bal oldali menüben válassza az adatüzemek lehetőséget. Ha a menüben nem látja az **adatok gyárait** , válassza a **További szolgáltatások**lehetőséget, majd az **intelligencia és elemzés** szakaszban válassza az **adatok** kiválasztása lehetőséget. 

   ![Az adatüzemek listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki az Azure-SSIS integrációs modult tartalmazó adatgyárat a listában. Megjelenik a saját adatelőállítójának kezdőlapja. Válassza ki a **szerző & üzembe helyezés** csempét. A Data Factory felhasználói felület külön lapon jelenik meg. 

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, válassza a **kapcsolatok**lehetőséget, majd váltson az Integration Runtimes ( **integrációs** modulok) lapra. 

   !["Integration Runtimes" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha az Azure-SSIS IR fut, az integrációs modul listában válassza a **Leállítás** gombot az Azure-SSIS IR **műveletek** oszlopában. Addig nem szerkesztheti az IR-t, amíg le nem állítja. 

   ![Az IR leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az Integration Runtime (integrációs modul) listában válassza a **Szerkesztés** gombot az Azure-SSIS IR **műveletek** oszlopában. 

   ![Az Integration Runtime szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. A **Integration Runtime telepítés** paneljén lépjen az **általános beállítások** és az **SQL-beállítások** lapokra a Next ( **tovább** ) gombra kattintva. 

1. A **Speciális beállítások** lapon végezze el a következő műveleteket: 

   a. Jelölje be a **VNet kiválasztása...** jelölőnégyzetet. 

   b. Az **előfizetés**mezőben válassza ki azt az Azure-előfizetést, amely alatt a meglévő virtuális hálózatot is kiválaszthatja. 
  
   c. A **VNet neve**mezőben válassza ki a virtuális hálózatot. 

   d. Az **alhálózat neve**mezőben válassza ki az alhálózatot a virtuális hálózaton. 

   e. Ha az Azure-SSIS IR-hez saját üzemeltetésű integrációs modult szeretne konfigurálni/kezelni, jelölje be a saját üzemeltetésű **...** jelölőnégyzetet, és tekintse meg a saját üzemeltetésű integrációs modul [konfigurálása az Azure-SSIS IR-hez](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) című cikket.

   f. Kattintson a **VNet érvényesítése** gombra, és ha sikeres, kattintson a **tovább** gombra. 

   ![Speciális beállítások az IR-telepítőhöz](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Az **Összefoglalás** lapon tekintse át az Azure-SSIS IR összes beállítását, majd kattintson a **frissítés** gombra.

1. Most már elindíthatja az Azure-SSIS IR-t az Azure-SSIS IR **műveletek** oszlopának **Start** gombjára kattintva. Körülbelül 20 – 30 percet vesz igénybe, hogy elindítsa az Azure-SSIS IR-t, amely egy virtuális hálózathoz csatlakozik. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása
Konfigurálnia kell egy virtuális hálózatot, mielőtt csatlakoztatni tudja az Azure-SSIS integrációs modulját. Az Azure-SSIS Integration Runtime virtuális hálózati engedélyeinek/beállításainak automatikus konfigurálásához a virtuális hálózathoz való csatlakozáshoz adja hozzá a következő parancsfájlt:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS integrációs modul létrehozása és csatlakoztatása egy virtuális hálózathoz
Létrehozhat egy Azure-SSIS integrációs modult, és egyszerre csatlakoztathatja azt egy virtuális hálózathoz. A teljes parancsfájl és útmutatásért lásd: [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Meglévő Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózathoz
Az [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md) című cikkben található szkript bemutatja, hogyan hozhat létre Azure-SSIS IR-t, és hogyan csatlakozhat egy virtuális hálózathoz ugyanabban a parancsfájlban. Ha rendelkezik egy meglévő Azure-SSIS IR-vel, hajtsa végre a következő lépéseket a virtuális hálózathoz való csatlakoztatáshoz: 
1. Állítsa le az Azure-SSIS IR-t. 
1. Konfigurálja az Azure-SSIS IR-t a virtuális hálózathoz való csatlakozáshoz. 
1. Indítsa el az Azure-SSIS IR-t. 

### <a name="define-the-variables"></a>Változók definiálása
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Az Azure-SSIS IR leállítása
A virtuális hálózathoz való csatlakoztatás előtt állítsa le az Azure-SSIS integrációs modult. Ez a parancs kibocsátja az összes csomópontját, és leállítja a számlázást:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Az Azure-SSIS integrációs modul virtuális hálózati beállításainak konfigurálása a csatlakozáshoz
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Az Azure-SSIS integrációs modul konfigurálása
Ha az Azure-SSIS integrációs modult a virtuális hálózathoz való csatlakozásra szeretné `Set-AzDataFactoryV2IntegrationRuntime` konfigurálni, futtassa a következő parancsot: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Az Azure-SSIS IR elindítása
Az Azure-SSIS Integration Runtime elindításához futtassa a következő parancsot: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

A parancs futása 20 – 30 percet vesz igénybe.

## <a name="next-steps"></a>További lépések
Az Azure-SSIS integrációs modulról a következő témakörökben talál további információt: 
- [Azure-SSIS integrációs](concepts-integration-runtime.md#azure-ssis-integration-runtime)modul. Ez a cikk általános tudnivalókat tartalmaz az integrációs modulokról, például az Azure-SSIS IR-ről. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez az oktatóanyag részletes útmutatást nyújt az Azure-SSIS IR létrehozásához. A Azure SQL Database használja az SSIS-katalógus üzemeltetéséhez. 
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk a SSIS-katalógus üzemeltetéséhez és az Azure-SSIS IR virtuális hálózathoz való csatlakoztatásához használt virtuális hálózati szolgáltatás végpontokkal/felügyelt példánnyal való Azure SQL Database használatával kapcsolatos útmutatást nyújt az oktatóanyaghoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ebből a cikkből megtudhatja, hogyan kérhet le adatokat az Azure-SSIS IR-ről, és a visszaadott információk alapján adja meg az állapot leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy törölni az Azure-SSIS IR-t. Azt is bemutatja, hogyan bővítheti az Azure-SSIS IR-t csomópontok hozzáadásával.
