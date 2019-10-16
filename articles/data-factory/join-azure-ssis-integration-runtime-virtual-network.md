---
title: Azure SSIS Integration Runtime csatlakoztatása virtuális hálózathoz | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat Azure-SSIS integrációs futtatókörnyezethez egy Azure-beli virtuális hálózathoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 065f69cc98f05fcb19648f190a7dba4b43da1a9a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326619"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure SSIS Integration Runtime csatlakoztatása virtuális hálózathoz
Ha SQL Server Integration Servicest (SSIS) használ a Azure Data Factoryban, akkor a következő esetekben csatlakoztatnia kell az Azure-SSIS Integration Runtime (IR)-t egy Azure-beli virtuális hálózathoz: 

- A helyszíni adattárakhoz szeretne csatlakozni a Azure-SSIS IR futó SSIS-csomagokból anélkül, hogy a saját üzemeltetésű integrációs modult proxyként kellene konfigurálni vagy kezelni. 

- A virtuális hálózati szolgáltatás-végpontok által támogatott Azure-szolgáltatási erőforrásokhoz szeretne csatlakozni a Azure-SSIS IRon futó SSIS-csomagokból.

- Egy SSIS Azure SQL Database-katalógus-adatbázist (SSISDB) üzemeltet a virtuális hálózatban található virtuális hálózati szolgáltatás-végpontokkal vagy felügyelt példányokkal. 

Data Factory lehetővé teszi a Azure-SSIS IR csatlakoztatását a klasszikus üzemi modellen vagy a Azure Resource Manager üzemi modellen keresztül létrehozott virtuális hálózathoz. 

> [!IMPORTANT]
> A klasszikus virtuális hálózat elavult, ezért inkább a Azure Resource Manager virtuális hálózatot használja.  Ha már használja a klasszikus virtuális hálózatot, a lehető leghamarabb váltson a Azure Resource Manager virtuális hálózatra.

## <a name="access-to-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárakhoz
Ha a SSIS-csomagok hozzáférnek a helyszíni adattárakhoz, csatlakozhat a Azure-SSIS IR egy olyan virtuális hálózathoz, amely a helyszíni hálózathoz csatlakozik. Vagy konfigurálhat vagy kezelhet egy saját üzemeltetésű IR-t proxyként a Azure-SSIS IR számára. További információ: saját üzemeltetésű integrációs modul [beállítása proxyként egy Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

A Azure-SSIS IR virtuális hálózathoz való csatlakoztatásakor jegyezze fel ezeket a fontos pontokat: 

- Ha nincs virtuális hálózat csatlakoztatva a helyszíni hálózathoz, először hozzon létre egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) a Azure-SSIS IRhoz való csatlakozáshoz. Ezután konfiguráljon egy helyek közötti [VPN Gateway-kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy a virtuális hálózat [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -kapcsolatát a helyszíni hálózatra. 

- Ha egy Azure Resource Manager virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz ugyanazon a helyen, mint a Azure-SSIS IR, akkor csatlakoztathatja az IR-t a virtuális hálózathoz. 

- Ha egy klasszikus virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz a Azure-SSIS IR egy másik helyen, létrehozhat egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md#create-a-virtual-network) a Azure-SSIS IRhoz való csatlakozáshoz. Ezután konfiguráljon egy [klasszikus – Azure Resource Manager virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolatokat. 
 
- Ha egy Azure Resource Manager virtuális hálózat már csatlakoztatva van a helyszíni hálózathoz a Azure-SSIS IR egy másik helyén, először hozzon létre egy [Azure Resource Manager virtuális hálózatot](../virtual-network/quick-create-portal.md##create-a-virtual-network) a Azure-SSIS IRhoz való csatlakozáshoz. Ezután konfiguráljon egy Azure Resource Manager – Azure Resource Manager virtuális hálózati kapcsolatokat. 

## <a name="access-to-azure-services"></a>Hozzáférés az Azure-szolgáltatásokhoz
Ha a SSIS-csomagok hozzáférnek a [virtuális hálózati szolgáltatás végpontjai](../virtual-network/virtual-network-service-endpoints-overview.md) által támogatott Azure-szolgáltatási erőforrásokhoz, és ezeket az erőforrásokat Azure-SSIS IR szeretné védeni, akkor a virtuális hálózattal konfigurált virtuális hálózati alhálózathoz csatlakoztathatja a Azure-SSIS IR szolgáltatási végpontok. Eközben vegyen fel egy virtuális hálózati szabályt az Azure szolgáltatási erőforrásaiba, hogy engedélyezze a hozzáférést ugyanahhoz az alhálózathoz.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>A SSIS-katalógus üzemeltetése SQL Database
Ha a SSIS-katalógust Azure SQL Database virtuális hálózati szolgáltatásbeli végpontokkal futtatja, akkor győződjön meg arról, hogy a Azure-SSIS IR ugyanahhoz a virtuális hálózathoz és alhálózathoz csatlakozik.

Ha a Azure-SSIS IRt ugyanahhoz a virtuális hálózathoz szeretné csatlakoztatni, mint a felügyelt példányt, győződjön meg arról, hogy a Azure-SSIS IR a felügyelt példánytól eltérő alhálózaton található. Ha a Azure-SSIS IRt a felügyelt példánytól eltérő virtuális hálózathoz szeretné csatlakoztatni, akkor a virtuális hálózatok közötti (amely ugyanahhoz a régióhoz van korlátozva) vagy virtuális hálózatról virtuális hálózatra való csatlakozást ajánljuk. További információ: [az alkalmazás Összekötése Azure SQL Database felügyelt példányhoz](../sql-database/sql-database-managed-instance-connect-app.md).

A virtuális hálózat minden esetben csak az Azure Resource Manager üzemi modellen keresztül helyezhető üzembe.

A következő szakaszokban további részleteket talál. 

## <a name="virtual-network-configuration"></a>Virtuális hálózati konfiguráció

Állítsa be a virtuális hálózatot a következő követelmények teljesítéséhez: 

-   Győződjön meg arról, hogy a `Microsoft.Batch` egy regisztrált szolgáltató a virtuális hálózat alhálózatának azon előfizetése alatt, amely a Azure-SSIS IRt tárolja. Ha klasszikus virtuális hálózatot használ, a virtuális hálózat klasszikus virtuálisgép-közreműködői szerepköréhez is csatlakozhat @no__t – 0. 

-   Győződjön meg arról, hogy rendelkezik a szükséges engedélyekkel. További információ: [set up permissions (engedélyek beállítása](#perms)).

-   Válassza ki a megfelelő alhálózatot a Azure-SSIS IR üzemeltetéséhez. További információ: [válassza ki az alhálózatot](#subnet). 

-   Ha a saját tartománynévrendszer-(DNS-) kiszolgálót használja a virtuális hálózaton, tekintse meg [a DNS-kiszolgáló beállítása](#dns_server)című témakört. 

-   Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton, tekintse meg a [NSG beállítása](#nsg)című témakört. 

-   Ha az Azure ExpressRoute vagy egy felhasználó által megadott útvonalat (UDR) használ, tekintse meg az [Azure ExpressRoute vagy a UDR használatát](#route)ismertető témakört. 

-   Győződjön meg arról, hogy a virtuális hálózat erőforráscsoport létrehozhat és törölhet bizonyos Azure-hálózati erőforrásokat. További információ: [az erőforráscsoport beállítása](#resource-group). 

-   Ha testreszabja a Azure-SSIS IR a [Azure-SSIS IR egyéni beállítása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)című témakörben leírtak szerint, akkor a Azure-SSIS IR-csomópontok MAGÁNHÁLÓZATI IP-címeket kapnak az előre meghatározott 172.16.0.0 és 172.31.255.255 között. Ügyeljen arra, hogy a virtuális vagy a helyszíni hálózatok magánhálózati IP-címei ne ütköznek ezzel a tartománnyal.

Ez az ábrán a Azure-SSIS IR szükséges kapcsolatok láthatók:

![Azure-SSIS integrációs modul](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Engedélyek beállítása

A Azure-SSIS IR létrehozó felhasználónak a következő engedélyekkel kell rendelkeznie:

- Ha a SSIS IR-t egy Azure Resource Manager virtuális hálózathoz csatlakoztatja, két lehetőség közül választhat:

  - Használja a beépített hálózati közreműködő szerepkört. Ez a szerepkör a _Microsoft. Network/\*_ engedéllyel rendelkezik, amely sokkal nagyobb hatókört tartalmaz, mint amennyi szükséges.

  - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft. Network/virtualNetworks/\*/JOIN/Action_ engedélyt tartalmazza. 

- Ha a SSIS IR-t egy klasszikus virtuális hálózathoz csatlakoztatja, javasoljuk, hogy használja a klasszikus virtuális gépek beépített közreműködői szerepkörét. Ellenkező esetben meg kell határoznia egy egyéni szerepkört, amely magában foglalja a virtuális hálózathoz való csatlakozás engedélyét.

### <a name="subnet"></a>Válassza ki az alhálózatot

Amikor kiválaszt egy alhálózatot: 

-   Ne válassza ki a GatewaySubnet egy Azure-SSIS IR telepítéséhez. A virtuális hálózati átjárók számára dedikált. 

-   Győződjön meg arról, hogy a kiválasztott alhálózat rendelkezik-e elegendő szabad hellyel a Azure-SSIS IR használatához. Hagyjon elérhető IP-címeket legalább két alkalommal az IR-csomópont számának. Az Azure néhány IP-címet fenntart az egyes alhálózatokon belül. Ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokollok megfelelőségére van fenntartva, és három további címet használ az Azure-szolgáltatásokhoz. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Ne használjon olyan alhálózatot, amelyet kizárólag más Azure-szolgáltatások foglalnak magukban (például SQL Database felügyelt példány, App Service stb.). 

### <a name="dns_server"></a>A DNS-kiszolgáló beállítása 
Ha saját DNS-kiszolgálóját kell használnia a Azure-SSIS IR által csatlakoztatott virtuális hálózatban, győződjön meg arról, hogy képes a globális Azure-állomásnevek (például egy `<your storage account>.blob.core.windows.net` nevű Azure Storage-blob) feloldására. 

A következő lépések ajánlottak: 

-   Konfigurálja az egyéni DNS-t, hogy továbbítsa a kérelmeket Azure DNS. Feloldatlan DNS-rekordokat továbbíthat a saját DNS-kiszolgálón található Azure rekurzív resolvers (168.63.129.16) IP-címére. 

-   Állítsa be az egyéni DNS-t elsődleges DNS-kiszolgálóként a virtuális hálózathoz. Azure DNS beállítása másodlagos DNS-kiszolgálóként. Regisztrálja az Azure rekurzív feloldók (168.63.129.16) IP-címét másodlagos DNS-kiszolgálóként abban az esetben, ha a saját DNS-kiszolgáló nem érhető el. 

További információ: névfeloldás, [amely a saját DNS-kiszolgálóját használja](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>NSG beállítása
Ha meg kell valósítania egy NSG a Azure-SSIS IR által használt alhálózathoz, engedélyezze a bejövő és kimenő forgalmat a következő portokon keresztül: 

| Irány | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Bejövő | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (ha az IR-t egy Resource Manager-beli virtuális hálózathoz csatlakoztatja) <br/><br/>10100, 20100, 30100 (ha az IR-t egy klasszikus virtuális hálózathoz csatlakoztatja)| A Data Factory szolgáltatás ezeket a portokat használja a virtuális hálózatban lévő Azure-SSIS IR csomópontjaival való kommunikációhoz. <br/><br/> Azt határozza meg, hogy alhálózati NSG hoz-e létre, Data Factory mindig a Azure-SSIS IR futtató virtuális gépekhez csatlakoztatott hálózati adapterek (NIC-EK) szintjén konfigurálja a NSG. A hálózati adapter szintű NSG csak a megadott portok Data Factory IP-címeiről érkező bejövő forgalmat engedélyezi. Még ha a portok az alhálózat szintjén is megnyitják az internetes forgalmat, akkor a nem Data Factory IP-címekről érkező adatforgalmat a hálózati adapter szintjén blokkolja a rendszer. |
| Kimenő | TCP | VirtualNetwork | * | AzureCloud | 443 | A virtuális hálózat Azure-SSIS IR csomópontjai ezt a portot használják az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs eléréséhez. |
| Kimenő | TCP | VirtualNetwork | * | Internet | 80 | A virtuális hálózat Azure-SSIS IR csomópontjai ezt a portot használják a visszavont tanúsítványok listájának internetről való letöltéséhez. |
| Kimenő | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | A virtuális hálózat Azure-SSIS IR csomópontjai ezeket a portokat használják a SQL Database-kiszolgáló által üzemeltetett SSISDB eléréséhez. Ha az SQL Database kiszolgáló-kapcsolódási házirendje az **átirányítás**helyett **proxyként** van beállítva, akkor csak az 1433-es port szükséges. Ez a kimenő biztonsági szabály nem alkalmazható a virtuális hálózaton a felügyelt példány által üzemeltetett SSISDB. |
||||||||

### <a name="route"></a>Az Azure ExpressRoute vagy a UDR használata
Amikor egy [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -áramkört hoz a virtuális hálózati infrastruktúrához a helyszíni hálózat Azure-ra való kiterjesztéséhez, a közös konfiguráció kényszerített bújtatást használ (a BGP-útvonalat (0.0.0.0/0) a virtuális hálózatra). Ez a bújtatás a virtuális hálózati forgalomból a helyszíni hálózati készülékre irányuló kimenő internetes forgalmat ellenőrzésre és naplózásra kényszeríti. 
 
Azt is megteheti, hogy a [UDR](../virtual-network/virtual-networks-udr-overview.md) a kimenő internetes forgalom kényszerítésére az alhálózatról, amely a Azure-SSIS IR üzemelteti egy másik alhálózatra, amely egy hálózati virtuális berendezést (NVA) üzemeltet tűzfalként vagy Azure Firewallként a vizsgálathoz és a naplózáshoz. 

Mindkét esetben a forgalmi útvonal megtöri a szükséges bejövő kapcsolatokat a függő Azure Data Factory szolgáltatásokból (pontosabban Azure Batch felügyeleti szolgáltatásokból) a virtuális hálózatban lévő Azure-SSIS IR. Ennek elkerüléséhez adjon meg egy vagy több UDR a Azure-SSIS IR tartalmazó alhálózaton. 

A következő ugrási típussal rendelkező 0.0.0.0/0 útvonal az Azure ExpressRoute-forgatókönyvben a Azure-SSIS IR üzemeltető alhálózaton **internetként** is alkalmazható. Vagy módosíthatja a meglévő 0.0.0.0/0 útvonalat a következő ugrási típusról **virtuális készülékként** az **internetre** egy NVA-forgatókönyvben.

![Útvonal hozzáadása](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Ha aggódik amiatt, hogy elveszti a kimenő internetes forgalom vizsgálatának lehetőségét az adott alhálózatról, meghatározhatja az adott UDR úgy, hogy a forgalmat csak Azure Batch felügyeleti szolgáltatások és a Azure-SSIS IR között a következő ugrási típusú **internetre**irányítsa.

Ha például a Azure-SSIS IR a `UK South` helyen található, akkor a szolgáltatási címke IP-címtartomány [letöltése hivatkozásra kattintva](https://www.microsoft.com/en-us/download/details.aspx?id=56519) vagy a Service [tag felderítési API](https://aka.ms/discoveryapi)-n keresztül a szolgáltatás címkéje `BatchNodeManagement.UKSouth`. Ezután alkalmazza a kapcsolódó IP-címtartomány következő UDR az **Internet**következő ugrási típusával.

![Azure Batch UDR beállításai](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Ez a megközelítés További karbantartási költségekkel jár. Rendszeresen vizsgálja meg az IP-címtartományt, és vegyen fel új IP-tartományokat a UDR, hogy elkerülje a Azure-SSIS IR megszakítását. Azt javasoljuk, hogy havonta ellenőrizze az IP-címtartományt, mert ha az új IP-cím megjelenik a szolgáltatás címkéjén, az IP-cím egy másik hónapra lép életbe. 

### <a name="resource-group"></a>Az erőforráscsoport beállítása
A Azure-SSIS IR létre kell hoznia bizonyos hálózati erőforrásokat a virtuális hálózattal azonos erőforráscsoporthoz. Ezek az erőforrások a következők:
   -   Egy Azure Load Balancer, amelynek neve *\<Guid >-azurebatch-cloudserviceloadbalancer*.
   -   Egy Azure nyilvános IP-cím, amelynek neve *\<Guid >-azurebatch-cloudservicepublicip*.
   -   Egy hálózati munkahelyi biztonsági csoport, amelynek neve *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

Ezek az erőforrások akkor jönnek létre, amikor az IR elindul. Ha az IR leáll, a rendszer törli őket. Ha el szeretné kerülni az IR-leállítás blokkolását, ne használja újra ezeket a hálózati erőforrásokat a többi erőforrásban. 

Győződjön meg arról, hogy nincs erőforrás-zárolás azon az erőforráscsoport vagy előfizetés számára, amelyhez a virtuális hálózat tartozik. Ha írásvédett zárolást vagy törlési zárolást konfigurál, az IR elindítása és leállítása sikertelen lehet, vagy az IR nem válaszol. 

Győződjön meg arról, hogy nem rendelkezik olyan Azure-házirenddel, amely megakadályozza, hogy a következő erőforrások jöjjenek létre az erőforráscsoport vagy előfizetés alatt, amelyhez a virtuális hálózat tartozik: 
   -   Microsoft. Network/LoadBalancers 
   -   Microsoft. Network/NetworkSecurityGroups 
   -   Microsoft. Network/nyilvános IP 

### <a name="faq"></a>– GYAKORI kérdések

- Hogyan védhető meg a Azure-SSIS IR a bejövő kapcsolatok számára elérhető nyilvános IP-cím? Lehetséges a nyilvános IP-cím eltávolítása?
 
    Ekkor a rendszer automatikusan létrehoz egy nyilvános IP-címet, amikor a Azure-SSIS IR csatlakozik a virtuális hálózathoz. A hálózati adapterek szintjén NSG csak Azure Batch felügyeleti szolgáltatásokat lehet beérkezőként csatlakozni a Azure-SSIS IRhoz. Megadhat egy alhálózati szintű NSG is a bejövő védelemhez.

    Ha nem szeretné, hogy a nyilvános IP-cím elérhető legyen, érdemes lehet a saját üzemeltetésű integrációs modult [proxyként konfigurálni](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) a virtuális hálózat helyett a Azure-SSIS IR, ha ez a forgatókönyvre vonatkozik.
 
- Hozzáadhatom a Azure-SSIS IR statikus IP-címét az adatforrás tűzfalának engedélyezési listájához?
 
    - Ha az adatforrás a helyszínen található, miután csatlakoztatta a virtuális hálózatot a helyszíni hálózathoz, és csatlakoztatja a Azure-SSIS IR a virtuális hálózat alhálózatához, hozzáadhatja az alhálózat IP-tartományát az engedélyezési listához.
    - Ha az adatforrás olyan Azure-szolgáltatás, amelyet a virtuális hálózati szolgáltatás végpontja támogat, beállíthat egy virtuális hálózati szolgáltatási pontot a virtuális hálózaton, és csatlakoztathatja a Azure-SSIS IRt a virtuális hálózat alhálózatához. Ezt követően az IP-címtartomány helyett az Azure-szolgáltatások virtuális hálózati szabályának használatával engedélyezheti a hozzáférést.
    - Ha az adatforrás eltérő típusú felhőalapú adatforrást használ, a UDR segítségével átirányíthatja a kimenő forgalmat a Azure-SSIS IRról a NVA, vagy Azure Firewall statikus nyilvános IP-cím használatával. A NVA vagy Azure Firewall nyilvános IP-címét hozzáadhatja az engedélyezési listához.
    - Ha az előző válaszok nem felelnek meg az igényeinek, érdemes lehet úgy megadnia az adatforrások elérését, ha a [Azure-SSIS IR számára konfigurálja a saját](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)üzemeltetésű integrációs modult. Ezután hozzáadhatja annak a számítógépnek az IP-címét, amely a saját üzemeltetésű integrációs modult futtatja az engedélyezési listára ahelyett, hogy a virtuális hálózathoz csatlakoztatná a Azure-SSIS IR.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory felhasználói felület)
Ez a szakasz bemutatja, hogyan csatlakozhat egy meglévő Azure-SSIS IR egy virtuális hálózathoz (klasszikus vagy Azure Resource Manager) az Azure Portal és Data Factory felhasználói felület használatával. 

Mielőtt csatlakoztatja a Azure-SSIS IRt a virtuális hálózathoz, megfelelően konfigurálnia kell a virtuális hálózatot. Kövesse a virtuális hálózat típusára vonatkozó szakasz lépéseit (klasszikus vagy Azure Resource Manager). Ezután kövesse a harmadik szakaszban található lépéseket a Azure-SSIS IR csatlakoztatásához a virtuális hálózathoz. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager virtuális hálózat konfigurálása

A portálon konfigurálhat egy Azure Resource Manager virtuális hálózatot, mielőtt csatlakozni próbál hozzá egy Azure-SSIS IRhoz.

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

1. Válassza a **További szolgáltatások**lehetőséget. A **virtuális hálózatok**szűrése és kiválasztása. 

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt. 

1. A **virtuális hálózat** lapon válassza a **Tulajdonságok**lehetőséget. 

1. Válassza a másolás gombot az **erőforrás-azonosítóhoz** , és másolja a virtuális hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból. 

1. A bal oldali menüben válassza az **alhálózatok**lehetőséget. Győződjön meg arról, hogy a rendelkezésre álló címek száma nagyobb, mint a Azure-SSIS IR csomópontjai. 

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésben, az előfizetés regisztrálva lesz a Azure Batchhoz. (Ha a Azure-SSIS IR a Data Factory portálon hozza létre, a Azure Batch szolgáltató automatikusan regisztrálva lesz.) 

   a. A Azure Portal bal oldali menüjében válassza az **előfizetések**lehetőséget. 

   b. Válassza ki előfizetését. 

   c. A bal oldalon válassza az **erőforrás-szolgáltatók**lehetőséget, és győződjön meg arról, hogy a **Microsoft. batch** egy regisztrált szolgáltató. 

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft. batcht** a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti. 

### <a name="configure-a-classic-virtual-network"></a>Klasszikus virtuális hálózat konfigurálása
A portálon konfigurálhatja a klasszikus virtuális hálózatot, mielőtt csatlakoztatni próbál egy Azure-SSIS IR. 

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 

1. Válassza a **További szolgáltatások**lehetőséget. Szűrje és válassza a **virtuális hálózatok (klasszikus)** lehetőséget. 

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt. 

1. A **virtuális hálózat (klasszikus)** lapon válassza a **Tulajdonságok**lehetőséget. 

   ![Klasszikus virtuális hálózati erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Válassza a másolás gombot az **erőforrás-azonosítóhoz** , és másolja a klasszikus hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból. 

1. A bal oldali menüben válassza az **alhálózatok**lehetőséget. Győződjön meg arról, hogy a rendelkezésre álló címek száma nagyobb, mint a Azure-SSIS IR csomópontjai. 

   ![A virtuális hálózatban elérhető címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Csatlakozzon a **MicrosoftAzureBatch** a virtuális hálózat **klasszikus virtuálisgép-közreműködői** szerepköréhez. 

    a. A bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget, és válassza ki a **szerepkör-hozzárendelések** lapot. 

    !["Hozzáférés-vezérlés" és "Hozzáadás" gomb](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Válassza a **szerepkör-hozzárendelés hozzáadása**lehetőséget.

    c. A **szerepkör-hozzárendelés hozzáadása** lapon a **szerepkör**beállításnál válassza a **klasszikus virtuális gépek közreműködője**lehetőséget. A **Select (kiválasztás** ) mezőben illessze be a **ddbf3205-c6bd-46ae-8127-60eb93363864**, majd válassza a **Microsoft Azure batch** elemet a keresési eredmények listájából. 

    ![Keresési eredmények a "szerepkör-hozzárendelés hozzáadása" oldalon](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Válassza a **Mentés** lehetőséget a beállítások mentéséhez és az oldal bezárásához. 

    ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Ellenőrizze, hogy megjelenik-e **Microsoft Azure batch** a közreműködők listájában. 

    ![Azure Batch hozzáférésének megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésben, az előfizetés regisztrálva lesz a Azure Batchhoz. (Ha a Azure-SSIS IR a Data Factory portálon hozza létre, a Azure Batch szolgáltató automatikusan regisztrálva lesz.) 

   a. A Azure Portal bal oldali menüjében válassza az **előfizetések**lehetőséget. 

   b. Válassza ki előfizetését. 

   c. A bal oldalon válassza az **erőforrás-szolgáltatók**lehetőséget, és győződjön meg arról, hogy a **Microsoft. batch** egy regisztrált szolgáltató. 

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja a **Microsoft. batcht** a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>A Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz

Miután konfigurálta a Azure Resource Manager virtuális hálózatot vagy a klasszikus virtuális hálózatot, csatlakoztathatja a Azure-SSIS IRt a virtuális hálózathoz:

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet. 

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **adatüzemek**lehetőséget. Ha a menüben nem látja az **adatok gyárait** , válassza a **További szolgáltatások**lehetőséget, majd az **intelligencia és elemzés** szakaszban válassza az **adatüzemek**lehetőséget. 

   ![Az adatüzemek listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki az adatgyárat a listában szereplő Azure-SSIS IR. Megjelenik a saját adatelőállítójának kezdőlapja. Válassza ki a **szerző & üzembe helyezés** csempét. A Data Factory felhasználói felület külön lapon jelenik meg. 

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, válassza a **kapcsolatok**lehetőséget, majd váltson az **Integration Runtimes (integrációs** modulok) lapra. 

   !["Integration Runtimes" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha a Azure-SSIS IR fut, az **integrációs** modulok listájában, a **műveletek** oszlopban válassza a **Leállítás** gombot a Azure-SSIS IR. Addig nem szerkesztheti az IR-t, amíg le nem állítja. 

   ![Az IR leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az **integrációs** modulok listájának **műveletek** oszlopában válassza ki a Azure-SSIS IR **Szerkesztés** gombját. 

   ![Az Integration Runtime szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. A **Integration Runtime telepítés** paneljén folytassa az **általános beállítások** és az **SQL-beállítások** lapjain a **tovább** gomb kiválasztásával. 

1. A **Speciális beállítások** lapon: 

   a. Jelölje be a **VNet kiválasztása**melletti jelölőnégyzetet. 

   b. Az **előfizetés**mezőben válassza ki az Azure-előfizetését. Az előfizetés alatt választhat egy meglévő virtuális hálózatot. 
  
   c. A **VNet neve**mezőben válassza ki a virtuális hálózatot. 

   d. Az **alhálózat neve**mezőben válassza ki az alhálózatot a virtuális hálózaton. 

   e. Ha a Azure-SSIS IR saját üzemeltetésű integrációs modult is szeretne konfigurálni vagy felügyelni, jelölje be a saját üzemeltetésű **beállítás** jelölőnégyzetet. További információ: saját üzemeltetésű integrációs modul [beállítása proxyként egy Azure-SSIS IRhoz](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).

   f. Válassza a **VNet érvényesítése** gombot. Ha az ellenőrzés sikeres, kattintson a **tovább** gombra. 

   ![Speciális beállítások az IR-telepítőhöz](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Az **Összefoglalás** lapon tekintse át a Azure-SSIS IR összes beállítását. Ezután kattintson a **frissítés** gombra.

1. Indítsa el a Azure-SSIS IR a Azure-SSIS IR **műveletek** oszlop **Start** gombjának kiválasztásával. Körülbelül 20 – 30 percet vesz igénybe a virtuális hálózathoz csatlakozó Azure-SSIS IR elindítása. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása
Ahhoz, hogy csatlakoztatni tudja a Azure-SSIS IRt egy virtuális hálózathoz, konfigurálnia kell a virtuális hálózatot. A virtuális hálózati engedélyek és beállítások automatikus konfigurálásához a Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz adja hozzá a következő parancsfájlt:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR létrehozása és csatlakoztatása egy virtuális hálózathoz
Létrehozhat egy Azure-SSIS IR, és egy időben csatlakozhat egy virtuális hálózathoz. A teljes parancsfájl és utasításokért lásd: [Azure-SSIS IR létrehozása](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Meglévő Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz
Az [Azure-SSIS IR létrehozása](create-azure-ssis-integration-runtime.md) cikkből megtudhatja, hogyan hozhat létre egy Azure-SSIS IR, és hogyan csatlakozhat egy virtuális hálózathoz ugyanabban a parancsfájlban. Ha már rendelkezik Azure-SSIS IRval, a következő lépésekkel csatlakozhat a virtuális hálózathoz: 
1. Állítsa le a Azure-SSIS IR. 
1. Konfigurálja a Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz. 
1. Indítsa el a Azure-SSIS IR. 

### <a name="define-the-variables"></a>Változók definiálása
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>A Azure-SSIS IR leállítása
A virtuális hálózathoz való csatlakoztatás előtt le kell állítania a Azure-SSIS IR. Ez a parancs kibocsátja az összes csomópontját, és leállítja a számlázást:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>A Azure-SSIS IR virtuális hálózati beállításainak konfigurálása a csatlakozáshoz

Az Azure-SSIS által csatlakoztatni kívánt virtuális hálózat beállításainak konfigurálásához használja a következő parancsfájlt: 

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

### <a name="configure-the-azure-ssis-ir"></a>A Azure-SSIS IR konfigurálása
Ha úgy szeretné konfigurálni a Azure-SSIS IRt, hogy csatlakozzon a virtuális hálózathoz, futtassa a `Set-AzDataFactoryV2IntegrationRuntime` parancsot: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>A Azure-SSIS IR elindítása
A Azure-SSIS IR elindításához futtassa a következő parancsot: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

A parancs futása 20 – 30 percet vesz igénybe.

## <a name="next-steps"></a>Következő lépések
A Azure-SSIS IRról a következő cikkekben talál további információt: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk az IRs-vel kapcsolatos általános információkat tartalmaz, beleértve a Azure-SSIS IRt is. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-](tutorial-create-azure-ssis-runtime-portal.md)ban. Ez az oktatóanyag részletes útmutatást nyújt a Azure-SSIS IR létrehozásához. A Azure SQL Database használja az SSIS-katalógus üzemeltetéséhez. 
- [Hozzon létre egy Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul. Útmutatást nyújt arról, hogyan használhatja Azure SQL Database virtuális hálózati szolgáltatásbeli végpontokkal vagy felügyelt példánnyal egy virtuális hálózaton a SSIS-katalógus üzemeltetéséhez. Bemutatja, hogyan csatlakozhat a Azure-SSIS IR egy virtuális hálózathoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le adatokat a Azure-SSIS IR. A visszaadott információk állapotának leírását adja meg. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy törölni a Azure-SSIS IR. Azt is bemutatja, hogyan bővítheti a Azure-SSIS IR csomópontok hozzáadásával.
