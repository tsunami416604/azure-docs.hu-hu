---
title: Azure Virtual Network szolgáltatásbeli végpontok
titlesuffix: Azure Virtual Network
description: Tudnivalók Azure-erőforrások virtuális hálózatokról történő közvetlen elérésének engedélyezéséről szolgáltatásvégpontok használatával.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8420142e67fe4af12045a2b6fe7f7461ef384f81
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164474"
---
# <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

A virtuális hálózatok (VNet) szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik a virtuális hálózat magáncímterét és a VNet identitását az Azure-szolgáltatásokra. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.

Ez a szolgáltatás a következő Azure-szolgáltatásokhoz és-régiókhoz érhető el, és a Microsoft. * erőforrás olyan zárójelben található, amelyet engedélyezni kell az alhálózati oldalról, miközben a szolgáltatáshoz tartozó végpontokat a szolgáltatáshoz kell konfigurálnia:

**Általánosan elérhető**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (Microsoft. Storage): Általánosan elérhető az összes Azure-régióban.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Általánosan elérhető az összes Azure-régióban.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Általánosan elérhető az összes Azure-régióban.
- **[Azure Database for PostgreSQL kiszolgáló](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Database for MySQL kiszolgáló](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (Microsoft. SQL): Általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Cosmos db](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. AzureCosmosDB): Általánosan elérhető az összes Azure-régióban.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (Microsoft. kulcstartó): Általánosan elérhető az összes Azure-régióban.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. ServiceBus): Általánosan elérhető az összes Azure-régióban.
- **[Azure-Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. EventHub): Általánosan elérhető az összes Azure-régióban.
- **[1. generációs Azure Data Lake Store](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. AzureActiveDirectory): Általánosan elérhető minden olyan Azure-régióban, ahol ADLS Gen1 érhető el.
- **[Azure app Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Általánosan elérhető minden olyan Azure-régióban, ahol az App Service elérhető

**Nyilvános előzetes verzió**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (Microsoft. ContainerRegistry): Az előzetes verzió minden olyan Azure-régióban elérhető, ahol Azure Container Registry érhető el.

A legfrissebb értesítésekért tekintse meg az [Azure-beli virtuális hálózatok frissítéseinek](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

## <a name="key-benefits"></a>Főbb előnyök

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Nagyobb biztonság az Azure-szolgáltatások erőforrásaiban**: A VNet magánhálózati címtartomány átfedésben lehet, így nem használható a VNet származó forgalom egyedi azonosítására. A szolgáltatásvégpontok lehetővé teszik, hogy az Azure-szolgáltatások erőforrásait a virtuális hálózathoz kösse a virtuális hálózat identitásának a szolgáltatásba történő kiterjesztésével. Ha a szolgáltatásvégpontok engedélyezve vannak a virtuális hálózaton, akkor Azure-szolgáltatásbeli erőforrásokat biztosíthat a virtuális hálózatnak úgy, hogy az erőforrásokhoz megad egy virtuális hálózati szabályt. Ez nagyobb biztonságot eredményez, mivel így az erőforrások egyáltalán nem lesznek elérhetők a nyilvános internetről, és csak a virtuális hálózatból érkező forgalom lesz engedélyezett.
- **Optimális útválasztás az Azure-szolgáltatás forgalmához a virtuális hálózatról**: Napjainkban a virtuális hálózat bármely olyan útvonala, amely a helyszíni és/vagy virtuális készülékekre irányuló internetes forgalmat kényszeríti, más néven kényszerített bújtatásnak nevezi, az Azure szolgáltatás forgalmát is kényszeríti, hogy az internetes forgalmat megegyező útvonalat használja. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára. 

  A végpontok a szolgáltatás forgalmát a virtuális hálózatról közvetlenül az Azure-szolgáltatásra vezetik a Microsoft Azure gerinchálózatán át. Ha az Azure gerinchálózatán tartja az adatforgalmat, továbbra is naplózhatja és monitorozhatja a virtuális hálózatok kimenő internetforgalmát a kényszerített bújtatáson keresztül anélkül, hogy ez kihatna a szolgáltatás forgalmára. További információk [a felhasználók által meghatározott útvonalakkal és a kényszerített bújtatással](virtual-networks-udr-overview.md) kapcsolatban.
- **Egyszerű beállítás kevesebb felügyeleti terheléssel**: Az Azure-erőforrások IP-tűzfalon keresztüli biztonságossá tételéhez már nincs szüksége fenntartott, nyilvános IP-címekre a virtuális hálózatokban. A szolgáltatásvégpontok beállításához nincs szükség NAT- és útválasztó eszközökre. A szolgáltatásvégpontok egy egyszerű kattintással konfigurálhatóak az alhálózatokon. A végpontok kezeléséhez nincs szükség további erőforrásokra.

## <a name="limitations"></a>Korlátozások

- A szolgáltatás csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon érhető el.
- A végpontok az Azure-beli virtuális hálózatokon konfigurált alhálózatokon vannak engedélyezve. A végpontok nem használhatók a helyszíni eredetű, az Azure-szolgáltatásokba irányuló forgalom továbbítására. További információt [az Azure-szolgáltatások helyszíni hozzáférésének biztosítását](#securing-azure-services-to-virtual-networks) ismertető szakaszban talál.
- Az Azure SQL esetében a szolgáltatásvégpontok csak az adott virtuális hálózat régióján belül vannak hatással az Azure-szolgáltatások forgalmára. Az Azure Storage RA-GRS- és GRS-forgalmának támogatása érdekében a végpontok azokra a párosított régiókra is kiterjednek, amelyekben a virtuális hálózat üzembe van helyezve. További információk az [Azure párosított régióiról](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Az 1. generációs ADLS esetében a VNet-integrációs képesség csak ugyanabban a régióban lévő virtuális hálózatok esetében érhető el. Azt is vegye figyelembe, hogy a Azure Data Lake Storage Gen1 virtuális hálózati integrációja a virtuális hálózati szolgáltatás végpontjának biztonságát használja a virtuális hálózat és a Azure Active Directory (Azure AD) között, hogy további biztonsági jogcímeket állítson elő a hozzáférési jogkivonatban. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs Data Lake Storage-fiókkal, és engedélyezi a hozzáférést. A szolgáltatások támogatása szolgáltatási végpontok területen felsorolt "Microsoft. AzureActiveDirectory" címkét csak az 1. generációs ADLS támogató szolgáltatási végpontok használják. Azure Active Directory (Azure AD) nem támogatja natív módon a szolgáltatási végpontokat. További információ a [Azure Data Lake Store 1. generációs VNet](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-integrációról.

## <a name="securing-azure-services-to-virtual-networks"></a>Azure-szolgáltatások biztosítása virtuális hálózatokhoz

- A virtuális hálózatok szolgáltatásvégpontjai adják meg a virtuális hálózatok identitását az Azure-szolgáltatás felé. Ha a szolgáltatásvégpontok engedélyezve vannak a virtuális hálózaton, akkor Azure-szolgáltatásbeli erőforrásokat biztosíthat a virtuális hálózatnak úgy, hogy az erőforrásokhoz megad egy virtuális hálózati szabályt.
- Manapság az Azure-szolgáltatások virtuális hálózatokról induló forgalma nyilvános IP-címeket használ forrás IP-ként. A szolgáltatásvégpontok használatakor a szolgáltatások forgalma átvált a virtuális hálózatok privát címeinek forrás IP-címekként való használatára a virtuális hálózatról az Azure-szolgáltatás felé irányuló forgalom esetén. Ez a váltás lehetővé teszi a szolgáltatások elérését anélkül, hogy szükség lenne az IP-tűzfalakban használt lefoglalt, nyilvános IP-címekre.

>[!NOTE]
> A szolgáltatásvégpontokkal a szolgáltatásforgalom alhálózatán lévő virtuális gépek forrás IP-címei a nyilvános IPv4-címek használatáról átváltanak magánhálózati IPv4-címek használatára. Az Azure-beli nyilvános IP-címeket használó Azure-szolgáltatáshoz tartozó meglévő tűzfalszabályok a váltás után nem fognak működni. A szolgáltatásvégpontok beállítása előtt győződjön meg arról, hogy az Azure-szolgáltatásokhoz tartozó tűzfalszabályok engedélyezik ezt a váltást. A szolgáltatásvégpontok konfigurálása során az alhálózatról érkező szolgáltatásforgalom átmenetileg megszakadhat. 
 
- __Az Azure-szolgáltatások helyszíni hozzáférésének biztosítása:__

  Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatási erőforrások nem érhetők el helyszíni hálózatokról. Ha engedélyezni szeretné a helyszíni eredetű forgalmat, ahhoz engedélyeznie kell a nyilvános (általában NAT) IP-címeket is a helyszíni vagy ExpressRoute-kapcsolatokon. Ezeket az IP-címeket az Azure-szolgáltatási erőforrások IP-tűzfalainak konfigurálásával lehet megadni.

  ExpressRoute: Ha a [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -t használja a telephelyéről, a nyilvános és a Microsoft-partnerek számára, meg kell határoznia a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítés esetén a használt NAT IP-cím(ek)et vagy az ügyfél vagy a szolgáltató adja meg. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címek keresése [nyisson egy támogatási jegyet az expressroute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon keresztül. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Azure-szolgáltatások biztosítása virtuális hálózatokhoz](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguráció

- A szolgáltatásvégpontokat a virtuális hálózatok alhálózatain konfiguráljuk. A végpontok bármilyen típusú számítási példányokkal működnek, ha azok az adott alhálózaton belül futnak.
- A támogatott Azure-szolgáltatásokhoz (például az Azure Storage-hez vagy az Azure SQL Database-hez) egy alhálózaton több szolgáltatásvégpont is konfigurálható.
- Az Azure SQL Database esetében a virtuális hálózatoknak és az Azure-szolgáltatási erőforrásnak ugyanabban a régióban kell lenniük. A GRS- és RA-GRS Azure Storage-fiókok használata esetén az elsődleges fióknak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Minden egyéb szolgáltatás esetében az Azure-szolgáltatások erőforrásai bármelyik régióban üzemelő virtuális hálózathoz leköthetők. 
- Az a virtuális hálózat, ahol a végpont konfigurálva van, lehet az Azure-szolgáltatási erőforrással egyazon előfizetésben, de egy másikban is. A végpontok beállításához és az Azure-szolgáltatások biztosításához szükséges engedélyekkel kapcsolatos további információkért lásd a [kiépítést](#provisioning) ismertető szakaszt.
- A támogatott szolgáltatások esetében a szolgáltatásvégpontokat használó virtuális hálózatokhoz biztosíthat új vagy meglévő erőforrásokat is.

### <a name="considerations"></a>Megfontolandó szempontok

- Egy szolgáltatásvégpont engedélyezése után az alhálózaton lévő virtuális gépek átváltanak a nyilvános IPv4-címek használatáról a magánhálózati IPv4-címeik forrásként való használatára, amikor az adott alhálózatról kommunikálnak a szolgáltatással. A váltás során bármilyen, a szolgáltatás felé nyitott fennálló TCP-kapcsolat bezárul. Győződjön meg arról, hogy semmilyen kritikus feladat nem fut, amikor egy alhálózaton engedélyez vagy letilt egy szolgáltatáshoz vezető szolgáltatásvégpontot. Továbbá győződjön meg arról is, hogy az alkalmazásai képesek automatikusan csatlakozni az Azure-szolgáltatásokhoz az IP-címváltást követően.

  Az IP-címváltás csak a virtuális hálózatról kimenő szolgáltatásforgalomra van hatással. A virtuális gépekhez rendelt nyilvános IPv4-címekre bejövő, illetve azokról kimenő bármilyen egyéb forgalomra a váltás nincs hatással. Azure-szolgáltatások esetében, ha a meglévő tűzfalszabályok az Azure nyilvános IP-címeit használják, akkor a virtuális hálózat privát címeire történő váltást követően a szabályok nem működnek tovább.
- A szolgáltatásvégpontok használatakor az Azure-szolgáltatások DNS-bejegyzései változatlanok maradnak, és továbbra is feloldják az Azure-szolgáltatáshoz rendelt nyilvános IP-címeket.

- Hálózati biztonsági csoportok (NSG-k) szolgáltatásvégpontokkal:
  - Alapértelmezés szerint az NSG-k engedélyezik a kimenő internetforgalmat, így a virtuális hálózatról az Azure-szolgáltatások felé tartó forgalmat is. Ez a szolgáltatásvégpontok használatával is ugyanígy működik. 
  - Ha szeretne megtagadni minden kimenő internetforgalmat, és csak a meghatározott Azure-szolgáltatások felé menő forgalmat szeretné engedélyezni, ezt az NSG-kben a [szolgáltatáscímkék](security-overview.md#service-tags) használatával teheti meg. Az NSG-szabályok között megadhatja célként a támogatott Azure-szolgáltatásokat, az egyes címkékhez tartozó IP-címek karbantartását pedig az Azure biztosítja. További információért lásd az [NSG-khez elérhető Azure-szolgáltatáscímkéket](security-overview.md#service-tags) ismertető szakaszt. 

### <a name="scenarios"></a>Forgatókönyvek

- **Társ, csatlakoztatott vagy több virtuális hálózat**: Ahhoz, hogy az Azure-szolgáltatások több alhálózatra is biztonságossá váljon egy virtuális hálózaton vagy több virtuális hálózaton belül, mindkét alhálózaton engedélyezheti a szolgáltatási végpontokat, és biztonságossá teheti az Azure-szolgáltatások erőforrásait az összes alhálózaton.
- **Virtuális hálózatról az Azure-szolgáltatásokra irányuló kimenő forgalom szűrése**: Ha egy virtuális hálózatról szeretné megvizsgálni vagy szűrni egy Azure-szolgáltatásra irányuló forgalmat, üzembe helyezhet egy hálózati virtuális berendezést a virtuális hálózaton belül. Ezután szolgáltatásvégpontokat alkalmazhat azon az alhálózaton, ahol a hálózati virtuális készülék üzemel, hogy csak ennek az alhálózatnak biztosítsa az Azure-szolgáltatási erőforrásokat. Ez a forgatókönyv akkor lehet hasznos, ha a virtuális hálózatnak az Azure-szolgáltatásokhoz való hozzáférését bizonyos Azure-erőforrásokra szeretné korlátozni a hálózati virtuális készülékek szűrésének segítségével. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](/azure/architecture/reference-architectures/dmz/nva-ha) foglalkozó témakört.
- **Azure-erőforrások biztonságossá tétele közvetlenül a virtuális hálózatokon üzembe helyezett szolgáltatásokhoz**: Különböző Azure-szolgáltatások közvetlenül üzembe helyezhetők egy virtuális hálózat adott alhálózatán. Az Azure-szolgáltatási erőforrások a [felügyelt szolgáltatási](virtual-network-for-azure-services.md) alhálózatokon egy szolgáltatásvégpont beállításával biztosíthatók.
- **Lemezes forgalom egy Azure-beli virtuális gépről**: A felügyelt/nem felügyelt lemezek virtuálisgép-forgalmát (beleértve a csatlakoztatást és a leválasztást, a Lemezforgalmát) nem érinti az Azure Storage szolgáltatás-végpontok útválasztási módosításai. A lapblobok REST-alapú elérését korlátozhatja a kívánt hálózatokra szolgáltatásvégpontok és [Azure Storage-hálózati szabályok](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) segítségével. 

### <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás

Ha konfigurálta a szolgáltatásvégpontokat egy adott szolgáltatáshoz, a következőképp ellenőrizheti, hogy a szolgáltatásvégpont útvonala érvényes-e: 
 
- Szolgáltatáskérések forrás IP-címeinek hitelesítése a szolgáltatási diagnosztikában. Minden szolgáltatásvégponttal rendelkező új kérés esetében a forrás IP-címnél a virtuális hálózat magánjellegű IP-címe jelenik meg, amely a virtuális hálózatról a hozzáférést kérvényező ügyfélhez van rendelve. A végpont nélkül ez a cím egy Azure-beli nyilvános IP-cím.
- Az érvényes útvonalak megtekintése az egyes alhálózatok hálózati adapterein. A szolgáltatásra mutató útvonal:
  - Egy pontosabban meghatározott alapértelmezett útvonalat jelent, az egyes szolgáltatások előtagtartományának megfelelően.
  - Az útvonal nextHopType típusa: *VirtualNetworkServiceEndpoint*
  - Azt jelzi, hogy a szolgáltatáshoz egy közvetlenebb kapcsolat vezet, szemben bármilyen kényszerített bújtatású útvonallal.

>[!NOTE]
> Azure-szolgáltatás használata esetén a szolgáltatásvégpont útvonalai felülbírálják a megfelelő címelőtag BGP- vagy UDR-útvonalait. További tudnivalók az [érvényes útvonalak hibaelhárításáról.](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpontok a virtuális hálózatokon külön-külön konfigurálhatók egy olyan felhasználó által, akiknek írási hozzáférése van egy virtuális hálózathoz. Az Azure-szolgáltatások erőforrásainak VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a *Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelethez* az alhálózatok hozzáadásához. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak ugyanahhoz az előfizetéshez, de különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, akkor az erőforrásoknak ugyanahhoz az Active Directory- (AD-) bérlőhöz kell tartozniuk. 

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpontok használatának nincs további felára. Az Azure-szolgáltatások (Azure Storage, Azure SQL Database stb.) aktuális díjszabási modellje az irányadó.

A virtuális hálózatokon korlátlan számú szolgáltatásvégpont üzemelhet.

Bizonyos Azure-szolgáltatások, például az Azure Storage-fiókok korlátozhatják az erőforrás biztosításához használt alhálózatok számát. Részletekért tekintse meg a [Következő lépések](#next-steps) című szakaszban felsorolt szolgáltatások dokumentációit.

## <a name="virtual-network-service-endpoint-policies"></a>Virtuális hálózati szolgáltatásvégpont-szabályzat 

Virtuális hálózati szolgáltatásvégpont-szabályzat lehetővé teszi az Azure-szolgáltatások, csak bizonyos Azure-szolgáltatási erőforrások, így keresztül a Szolgáltatásvégpontok a virtuális hálózati forgalmának szűrése. Szolgáltatásvégpont-szabályzat részletes hozzáférés-vezérlés az Azure-szolgáltatások virtuális hálózati forgalomhoz adja meg. További információ: [Virtual Network szolgáltatási végpont házirendjei](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Gyakori kérdések

A gyakori kérdések, tekintse meg [virtuális hálózati szolgáltatási végpont – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>További lépések

- [Virtuális hálózati szolgáltatásvégpontok konfigurálása](tutorial-restrict-network-access-to-resources.md) – útmutató
- [Azure Storage-fiókok biztosítása virtuális hálózatokhoz](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) – útmutató
- [Azure SQL Database biztosítása virtuális hálózatokhoz](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) – útmutató
- Ismerje meg, hogyan [biztonságossá tétele az Azure SQL Data Warehouse egy virtuális hálózathoz](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure-szolgáltatások integrálása virtuális hálózatokon](virtual-network-for-azure-services.md) – útmutató
- Ismerje meg [virtuális hálózati szolgáltatásvégpont-szabályzat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Gyors üzembe helyezés: [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) a szolgáltatási végpont VNet való beállításához és az Azure Storage-fiók biztonságossá tételéhez az adott alhálózaton.

