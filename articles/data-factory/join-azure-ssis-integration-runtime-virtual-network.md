---
title: Azure-SSIS integrációs futásidejű csatlakoztatása egy virtuális hálózati |} Microsoft Docs
description: Ismerje meg, hogyan lehet Azure virtuális hálózat az Azure-SSIS-integrációs futásidejű csatlakoztatni.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: fa496271d949f131da53a4cdab0f3b9a15e82007
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268142"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Egy Azure-SSIS-integrációs futásidejű csatlakoztatása egy virtuális hálózatot
Az Azure-SSIS-integrációs futásidejű (IR) csatlakoztassa egy Azure virtuális hálózatra, a következő esetekben: 

- Az Azure SSIS integrációs modulon futó SSIS-csomagokkal helyszíni adattárakhoz szeretne csatlakozni.

- Az SQL Server Integration Services (SSIS) katalógus adatbázis az Azure SQL Database virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) üzemeltet.

 Az Azure Data Factory (előzetes verzió) 2-es lehetővé teszi az Azure-SSIS-integrációs futásidejű csatlakoztatása a klasszikus üzembe helyezési modellt vagy az Azure Resource Manager telepítési modell használatával létrehozott virtuális hálózatban. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általában elérhetőségével (GA), 1 verziójának használatakor tekintse meg a [Data Factory 1-es verziójú dokumentáció](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Hozzáférés a helyszíni adattárolókhoz.
Ha SSIS-csomagok hozzáférést, csak a nyilvános felhő, nem kell az Azure-SSIS infravörös csatlakoztatása egy virtuális hálózatot. SSIS-csomagok hozzáférést, a helyszínen, ha az Azure-SSIS infravörös a helyszíni hálózathoz csatlakozó virtuális hálózathoz kell csatlakoztatni. 

Az alábbiakban néhány fontos tényezőt figyelembe venni: 

- Ha nincs meglévő a helyszíni hálózathoz csatlakozó virtuális hálózati, akkor először létre kell hoznia egy [Azure Resource Manager virtuális hálózati](../virtual-network/quick-create-portal.md#create-a-virtual-network) vagy egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS-integráció futásidejű való csatlakozáshoz. Ezt követően konfigurálja a pont-pont [VPN gateway-kapcsolatot](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) vagy [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) és a helyszíni hálózat a virtuális hálózat közötti kapcsolat.
- Ha egy meglévő Azure Resource Manager vagy a klasszikus virtuális hálózatot a helyszíni hálózat és az Azure-SSIS-IR ugyanazon a helyen lévő csatlakozik, az infravörös csatlakozhat a virtuális hálózaton.
- Ha egy létező klasszikus virtuális hálózatot az Azure-SSIS-IR egy másik helyen lévő a helyszíni hálózathoz kapcsolódik, először létrehozhat egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) az Azure-SSIS-IR való csatlakozáshoz. Ezt követően konfigurálja a [klasszikus-klasszikus virtuális hálózati](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) kapcsolat. Vagy létrehozhat egy [Azure Resource Manager virtuális hálózati](../virtual-network/quick-create-portal.md#create-a-virtual-network) az Azure-SSIS integráció a futási időben való csatlakozáshoz. Konfigurálja a [klasszikus Azure erőforrás-kezelő virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.
- Ha egy meglévő Azure Resource Manager virtuális hálózatot az Azure-SSIS-IR keresztül kapcsolódik a helyszíni hálózat egy másik helyen van, akkor először létrehozhat egy [Azure Resource Manager virtuális hálózati](../virtual-network/quick-create-portal.md##create-a-virtual-network) az az Azure-SSIS Infravörös való csatlakozáshoz. Ezt követően konfigurálja az Azure Resource Manager-Azure Resource Manager virtuális hálózati kapcsolat. Másik lehetőségként létrehozhat egy [klasszikus virtuális hálózatot](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) csatlakozni az Azure-SSIS-IR. Ezt követően konfigurálja a [klasszikus Azure erőforrás-kezelő virtuális hálózati](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) kapcsolat.

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance-preview"></a>A gazdagép virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) Azure SQL Database SSIS-katalógus adatbázis
A SSIS-katalógus a virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) az Azure SQL Database helyezkedik el, ha az Azure-SSIS infravörös kapcsolhatja össze:

- Az azonos virtuális hálózatban
- Egy másik virtuális hálózatot, amely a hálózat-hálózati kapcsolattal rendelkezik, amely az Azure SQL Database használt virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió)

A virtuális hálózat a klasszikus üzembe helyezési modellt vagy az Azure Resource Manager telepítési modell telepíthető. Ha azt tervezi, hogy az Azure-SSIS-IR való csatlakozás a *azonos virtuális hálózatban* , amely már csatlakoztatva van a példány által felügyelt (előzetes verzió), győződjön meg arról, hogy az Azure-SSIS-IR egy *másik alhálózat* használja az Felügyelt példány (előzetes verzió).   

Az alábbi szakaszokban további részleteket.

## <a name="requirements-for-virtual-network-configuration"></a>Virtuális hálózati konfigurációs követelményei
-   Győződjön meg arról, hogy `Microsoft.Batch` az előfizetésben a virtuális hálózati alhálózat az Azure-SSIS infravörös futtató regisztrált szolgáltató Klasszikus virtuális hálózatot használ, ha is csatlakozás `MicrosoftAzureBatch` a klasszikus virtuális gép közreműködő szerepkört az adott virtuális hálózati.

-   Válassza ki a megfelelő alhálózati az Azure-SSIS infravörös üzemeltetéséhez Lásd: [jelölje ki az alhálózatot](#subnet).

-   Ha a saját tartományi szolgáltatásokra (DNS) kiszolgáló használ a virtuális hálózaton, lásd: [tartománynév-szolgáltatásokat server](#dns_server).

-   Ha a hálózati biztonsági csoport (NSG) használ az alhálózaton, lásd: [hálózati biztonsági csoport](#nsg)

-   Ha használja az Azure Express Route vagy felhasználó megadott útvonal (UDR) konfigurálása, lásd: [használata Azure expressroute-on vagy a felhasználó definiált útvonal](#route).

-   Győződjön meg arról, hogy az erőforráscsoport, a virtuális hálózat létrehozása, és bizonyos Azure hálózati erőforrások törlése. Lásd: [erőforráscsoport követelményei](#resource-group).

### <a name="subnet"></a> Jelölje ki az alhálózatot
-   Ne válassza a GatewaySubnet üzembe helyezéséhez egy Azure-SSIS-integráció futtatókörnyezetet, mert virtuális hálózati átjárók külön.

-   Ügyeljen arra, hogy a kiválasztott alhálózat az Azure-SSIS-IR használandó elegendő elérhető címtérrel. Hagyja legalább 2 * IR csomópont elérhető IP-címek számát. Azure fenntartja az egyes IP-címek minden alhálózaton belül, és ezeknél a címeknél nem használható. Az első és utolsó IP-címek alhálózatok protokoll megfelelési, valamint három további címek az Azure-szolgáltatásokhoz használt számára vannak fenntartva. További információkért lásd: [vannak-e bármilyen korlátozás belül ezek alhálózatok IP-címeket használnak?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

-   Ne használjon a kizárólag más Azure-szolgáltatásokhoz (például SQL adatbázis felügyelt példány (előzetes verzió), az App Service, stb.) által betöltött alhálózat.

### <a name="dns_server"></a> Tartomány neve Services-kiszolgáló 
Ha szeretné használni a saját tartományi szolgáltatásokra (DNS) kiszolgáló tartományhoz az Azure-SSIS-integrációs futásidejű virtuális hálózatban, győződjön meg arról, hogy feloldja az Azure-gazdagép nevét (például egy Azure Storage blob neve `<your storage account>.blob.core.windows.net`).

A következő lépéseket javasoljuk:

-   Egyéni DNS az Azure DNS-kérések továbbítására konfigurálása. Feloldatlan Azure rekurzív feloldókat (168.63.129.16) IP-címének DNS-rekordokat továbbíthatja a saját DNS-kiszolgálón.

-   Állítsa be az elsődleges egyéni DNS és az Azure DNS-ben, mint a másodlagos virtuális hálózat. Regisztrálja a másodlagos DNS-kiszolgáló Azure rekurzív feloldókat (168.63.129.16) IP-címét, abban az esetben a saját DNS-kiszolgáló nem érhető el.

További információk: [névfeloldás a saját DNS-kiszolgálót használó](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="nsg"></a> Hálózati biztonsági csoport
Ha kell megvalósítani a hálózati biztonsági csoport (NSG) segítségével az Azure-SSIS-integrációs futásidejű virtuális hálózatban, engedélyezze a bejövő/kimenő forgalom az alábbi portokon keresztül:

| Irány | Átviteli protokoll | Forrás | Forrás porttartomány | Cél | Célport tartomány | Megjegyzések |
|---|---|---|---|---|---|---|
| Bejövő | TCP | Internet | * | VirtualNetwork | 29876, 29877 (ha az infravörös csatlakoztatása az Azure Resource Manager virtuális hálózat) <br/><br/>10100, 20100, 30100 (ha az infravörös csatlakoztatása a klasszikus virtuális hálózatot)| A Data Factory szolgáltatásnak ezeket a portokat használ a virtuális hálózat az Azure-SSIS integrációs futásidejű csomópontjai folytatott kommunikációhoz. <br/><br/> Egy NSG-t vagy nem ad meg, hogy adat-előállító mindig konfigurálja a egy NSG-t a csatolva az Azure-SSIS infravörös üzemeltető virtuális gépek szintjén, a hálózati adapterek (NIC) A Data Factory IP-címek csak a bejövő forgalom engedélyezett. Akkor is, ha az internetes forgalmat a portok megnyitásához, amelyek nincsenek Data Factory IP-címek IP-címekről érkező forgalom le van tiltva, a hálózati adapter szintjén. |
| Kimenő | TCP | VirtualNetwork | * | Internet | 443 | A virtuális hálózat az Azure-SSIS integrációs futásidejű csomópontjai Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs eléréséhez használja ezt a portot. |
| Kimenő | TCP | VirtualNetwork | * | Internet vagy Sql | 1433-as számú 11000-11999, 14000-14999 | Az Azure-SSIS integrációs futásidejű SSISDB elérni ezeket a portokat az Azure SQL Database-kiszolgálóhoz - által üzemeltetett virtuális hálózat használatban csomópontjai erre a célra esetében nem érvényes SSISDB felügyelt példány (előzetes verzió) üzemelteti. |
||||||||

### <a name="route"></a> Használja az Azure expressroute-on vagy a felhasználó által megadott útvonal
A Kapcsolódás egy [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolat a virtuális hálózati infrastruktúra, a helyszíni hálózat kiterjesztése az Azure-bA. 

A közös konfigurálás az, hogy a kényszerített bújtatás használata (BGP-útvonal, a virtuális hálózathoz 0.0.0.0/0 hirdetési) amely kényszeríti kimenő internetes forgalmat a virtuális hálózat az adatfolyamban a helyszíni hálózati berendezések vizsgálat és naplózás. A forgalom áramlását az Azure-SSIS infravörös függő Azure Data Factory szolgáltatással a virtuális hálózat közötti kapcsolat megszakad. A megoldás, hogy egy (vagy több) megadása [felhasználó által definiált útvonalak (udr-EK)](../virtual-network/virtual-networks-udr-overview.md) , amely tartalmazza az Azure-SSIS infravörös az alhálózaton Egy UDR határozza meg az alhálózat-specifikus útvonalakat, amelyek figyelembe véve a BGP-útvonal helyett.

Vagy a felhasználó által definiált útvonalak (udr-EK) kimenő internetforgalom kényszerítheti az alhálózatból az Azure-SSIS infravörös egy másik alhálózatnak, a virtuális hálózati berendezések üzemeltet egy tűzfal vagy egy Szegélyhálózaton állomás futtató vizsgálati és naplózási adhat meg.

Mindkét esetben alkalmazni a 0.0.0.0/0 útvonal a következő ugrás típusa, **Internet** , hogy a Data Factory szolgáltatásnak és az Azure-SSIS van infravörös közötti kommunikáció sikeres lehet az Azure-SSIS infravörös futtató az alhálózaton. 

![Adjon hozzá egy útvonalat](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Ha aggódik való vizsgálja meg az adott alhálózat kimenő internetforgalom, azt is megteheti az NSG-szabályok kimenő célok korlátozhatja az alhálózaton [Azure adatközpont IP-címek](https://www.microsoft.com/download/details.aspx?id=41653).

Lásd: [a PowerShell parancsfájl](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) példát. Akkor kell futtatnia a parancsfájl hetente és naprakész állapotban tarthatja az Azure data center IP-címek listájából.

### <a name="resource-group"></a> Az erőforráscsoport követelmények
Az Azure-SSIS-IR kell létrehoznia a virtuális hálózatnak, beleértve az Azure terheléselosztó, az Azure nyilvános IP-cím és munkahelyi hálózati biztonsági csoport ugyanabban az erőforráscsoportban bizonyos hálózati erőforrásokhoz.

-   Győződjön meg arról, hogy nincs-e bármilyen erőforrás zárolását az erőforráscsoportba vagy előfizetést, amelyhez a virtuális hálózat tartozik. Ha konfigurál egy olvasási zárolás vagy a delete zárolja, indítása és leállítása az infravörös is sikertelen, vagy lefagy.

-   Győződjön meg arról, hogy nincs-e az Azure-házirendet, amely megakadályozza a következő erőforrások az erőforráscsoportban vagy előfizetést, amelyhez a virtuális hálózat tartozik:
    -   Microsoft.Network/LoadBalancers
    -   Microsoft.Network/NetworkSecurityGroups
    -   Microsoft.Network/PublicIPAddresses

## <a name="azure-portal-data-factory-ui"></a>Azure-portálon (Data Factory UI)
Ez a szakasz bemutatja, hogyan lehet egy meglévő Azure-SSIS-futtatókörnyezet egy virtuális hálózat (klasszikus vagy az Azure Resource Manager) az Azure portál és a Data Factory felhasználói felület használatával. Először meg kell a virtuális hálózat megfelelő konfigurálása az Azure-SSIS-IR csatlakoztatása előtt. Nyissa meg a virtuális hálózat (klasszikus vagy az Azure Resource Manager) típusa a következő két szakasz egyikével. Ezt követően a harmadik szakasz az Azure-SSIS-IR csatlakoztatása a virtuális hálózat. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Az Azure Resource Manager virtuális hálózat konfigurálása a portál használatával
Virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakozhat hozzá kell.

1. Indítsa el a Microsoft Edge vagy a Google Chrome. A Data Factory felhasználói felület jelenleg csak az adott webböngésző használata támogatott.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
3. Válassza ki **további szolgáltatások**. Szűrhet, és válassza ki **virtuális hálózatok**.
4. Szűrhet, és a listában jelölje ki a virtuális hálózat. 
5. Az a **virtuális hálózati** lapon jelölje be **tulajdonságok**. 
6. Válassza a Másolás gombra **erőforrás-azonosító** az erőforrás-azonosítója a virtuális hálózat másolása a vágólapra. Mentse az Azonosítót a OneNote-bA vagy egy fájlt a vágólapról.
7. Válassza ki **alhálózatok** a bal oldali menüben. Győződjön meg arról, hogy hány **elérhető címek** nagyobb, mint az Azure-SSIS-integrációs futásidejű csomópontján.
8. Győződjön meg arról, hogy az Azure Batch-szolgáltató regisztrálva van az Azure-előfizetés, amely rendelkezik a virtuális hálózat. Vagy az Azure Batch-szolgáltató regisztrálása. Ha már rendelkezik Azure Batch-fiók az előfizetéshez, majd az előfizetés az Azure Batch van regisztrálva. (A Data Factory-portálon az Azure-SSIS infravörös hoz létre, ha az Azure Batch-szolgáltató automatikusan regisztrálva van az Ön.)

   a. Azure-portálon, válassza ki a **előfizetések** a bal oldali menüben.

   b. Válassza ki előfizetését. 
   
   c. Válassza ki **erőforrás-szolgáltató** a bal oldali, és ellenőrizze, hogy **Microsoft.Batch** egy regisztrált szolgáltató.
      !["Regisztrált" állapotának megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)
      
   Ha nem lát **Microsoft.Batch** a listában, regisztrálja őket, [üres Azure Batch-fiók létrehozása](../batch/batch-account-create-portal.md) az előfizetésben. Később is törli.

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Klasszikus virtuális hálózat konfigurálása a portál használatával
Virtuális hálózat konfigurálása előtt egy Azure-SSIS-IR csatlakozhat hozzá kell.

1. Indítsa el a Microsoft Edge vagy a Google Chrome. A Data Factory felhasználói felület jelenleg csak az alábbi böngészők használata támogatott.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
3. Válassza ki **további szolgáltatások**. Szűrhet, és válassza ki **virtuális hálózatok (klasszikus)**.
4. Szűrhet, és a listában jelölje ki a virtuális hálózat. 
5. Az a **virtuális hálózat (klasszikus)** lapon jelölje be **tulajdonságok**.
    ![Klasszikus virtuális hálózati erőforrás-azonosító](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
    
6. Válassza a Másolás gombra **erőforrás-azonosító** az erőforrás-azonosító, a klasszikus hálózat másolása a vágólapra. Mentse az Azonosítót a OneNote-bA vagy egy fájlt a vágólapról.
7. Válassza ki **alhálózatok** a bal oldali menüben. Győződjön meg arról, hogy hány **elérhető címek** nagyobb, mint az Azure-SSIS-integrációs futásidejű csomópontján.
    ![A virtuális hálózaton rendelkezésre álló címek száma](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
    
8. Csatlakozás **MicrosoftAzureBatch** számára a **klasszikus virtuális gép közreműködő** szerepkör a virtuális hálózat.

    a. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali menüben, majd válassza a **Hozzáadás** az eszköztáron.
      !["Hozzáférés-vezérlés" és "Hozzáadás" gombok](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)
      
    b. Az a **engedélyek hozzáadása** lapon jelölje be **klasszikus virtuális gép közreműködő** a **szerepkör**. Beillesztés **ddbf3205-c6bd-46ae-8127-60eb93363864** a a **válasszon** mezőbe, majd válassza ki **Microsoft Azure Batch** a keresési eredmények listájáról.
      !["Engedélyek hozzáadása" lapon a keresési eredmények](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
      
    c. Válassza ki **mentése** menti a beállításokat, és zárja be a lapot.
      ![Hozzáférési beállítások mentése](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
      
    d. Ellenőrizze, hogy látható **Microsoft Azure Batch** közreműködők listájában.
      ![Azure Batch hozzáférés megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
      
9. Győződjön meg arról, hogy az Azure Batch-szolgáltató regisztrálva van az Azure-előfizetés, amely rendelkezik a virtuális hálózat. Vagy az Azure Batch-szolgáltató regisztrálása. Ha már rendelkezik Azure Batch-fiók az előfizetéshez, majd az előfizetés az Azure Batch van regisztrálva. (A Data Factory-portálon az Azure-SSIS infravörös hoz létre, ha az Azure Batch-szolgáltató automatikusan regisztrálva van az Ön.)

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

   b. A **típus**, adja meg, hogy a virtuális hálózat, a klasszikus virtuális hálózatot, vagy egy Azure Resource Manager virtuális hálózatot. 

   c. A **VNet neve**, válassza ki a virtuális hálózatot.

   d. A **alhálózati név**, jelölje ki az alhálózatot a virtuális hálózat.

   e. Kattintson a **VNet érvényesítési** , és ha sikeres, kattintson **frissítés**. 

   ![Az IR-telepítő speciális beállítások](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Most, megkezdheti a IR segítségével a **Start** gombra a **műveletek** az Azure-SSIS infravörös oszlopban Egy Azure-SSIS infravörös elindításához körülbelül 20-30 percet vesz igénybe 

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása
Virtuális hálózat konfigurálása előtt az Azure-SSIS-IR csatlakozhat hozzá kell. Automatikus konfigurálása az Azure-SSIS integrációs futásidejű való csatlakozáshoz a virtuális hálózat virtuális hálózati engedélyek/beállításait, vegye fel a következő parancsfájlt:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Hozzon létre egy Azure-SSIS-IR, és csatlakoztassa a virtuális hálózathoz
Hozzon létre egy Azure-SSIS-IR, és egyszerre egy virtuális hálózathoz csatlakozzon hozzá. A teljes parancsfájl és utasításokat lásd: [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Egy meglévő Azure-SSIS-IR csatlakoztatása egy virtuális hálózatot
A parancsfájl a [hozzon létre egy Azure-SSIS-integrációs futásidejű](create-azure-ssis-integration-runtime.md) cikkből megtudhatja, hogyan hozzon létre egy Azure-SSIS-IR, és ugyanazt a parancsfájlt a virtuális hálózathoz csatlakozzon hozzá. Ha egy meglévő Azure-SSIS-IR, a következő lépésekkel csatlakozzon hozzá a virtuális hálózathoz: 

1. Állítsa le az Azure-SSIS infravörös
2. Konfigurálja az Azure-SSIS infravörös a virtuális hálózathoz csatlakoztatni. 
3. Indítsa el az Azure-SSIS infravörös 

### <a name="define-the-variables"></a>A változók megadása
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
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

### <a name="configure-the-azure-ssis-ir"></a>Az Azure-SSIS infravörös konfigurálása
Az Azure-SSIS integrációs futásidejű való csatlakozáshoz a virtuális hálózat konfigurálásához futtassa a `Set-AzureRmDataFactoryV2IntegrationRuntime` parancs: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk részletesen hozzon létre egy Azure-SSIS infravörös Az Azure SQL Database segítségével a SSIS-katalógust. 
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk kibővíti az oktatóanyag, és utasításokkal szolgál az Azure SQL Database szolgáltatást a virtuális hálózati szolgáltatás végpontok/felügyelt példány (előzetes verzió) a SSIS-katalógust és az infravörös csatlakoztatása egy virtuális hálózati. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutatja, hogyan horizontális felskálázás az Azure-SSIS-IR csomópontok hozzáadásával. 
