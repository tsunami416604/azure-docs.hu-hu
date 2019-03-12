---
title: Az Azure virtuális hálózati Szolgáltatásvégpontok
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
ms.author: sumeet.mittal
ms.custom: ''
ms.openlocfilehash: ac166161624840aae39e84aa4b149fe58a6c978e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780864"
---
# <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

A virtuális hálózatok (VNet) szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik a virtuális hálózat magáncímterét és a VNet identitását az Azure-szolgáltatásokra. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.

Ez a szolgáltatás a következő Azure-szolgáltatásokhoz és -régiókhoz érhető el:

**Általánosan elérhető**

- **[Az Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)**: Az összes Azure-régióban általánosan elérhető.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Az összes Azure-régióban általánosan elérhető.
- **[Az Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Az összes Azure-régióban általánosan elérhető.
- **[Azure Database for PostgreSQL-kiszolgáló](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Hol érhető el adatbázis-szolgáltatás Azure-régióban általánosan elérhető.
- **[Azure Database for MySQL-kiszolgáló](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Hol érhető el adatbázis-szolgáltatás Azure-régióban általánosan elérhető.
- **[Az Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Az összes Azure-régióban általánosan elérhető.
- **[Az Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)**: Az összes Azure-régióban általánosan elérhető.
- **[Az Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Az összes Azure-régióban általánosan elérhető.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Az összes Azure-régióban általánosan elérhető.
- **[Az Azure Data Lake Store 1. generációs gyűjtések](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: Hol érhető el az ADLS Gen1 minden Azure-régióban általánosan elérhető.

**Nyilvános előzetes verzió**

- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)**: Hol érhető el, Azure Database for MariaDB minden Azure-régióban elérhető előzetes.

A legfrissebb értesítésekért tekintse meg az [Azure-beli virtuális hálózatok frissítéseinek](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

## <a name="key-benefits"></a>Főbb előnyök

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Nagyobb biztonság az Azure-szolgáltatások erőforrásai számára**: Virtuális hálózat magáncímterét lehetnek egymással átfedésben lévő, és ezért nem használható a virtuális hálózatról származó forgalmat egyedi azonosításához. A szolgáltatásvégpontok lehetővé teszik, hogy az Azure-szolgáltatások erőforrásait a virtuális hálózathoz kösse a virtuális hálózat identitásának a szolgáltatásba történő kiterjesztésével. Ha a szolgáltatásvégpontok engedélyezve vannak a virtuális hálózaton, akkor Azure-szolgáltatásbeli erőforrásokat biztosíthat a virtuális hálózatnak úgy, hogy az erőforrásokhoz megad egy virtuális hálózati szabályt. Ez nagyobb biztonságot eredményez, mivel így az erőforrások egyáltalán nem lesznek elérhetők a nyilvános internetről, és csak a virtuális hálózatból érkező forgalom lesz engedélyezett.
- **Optimális útválasztás az Azure szolgáltatás forgalmát a virtuális hálózatról**: Még ma a virtuális hálózat összes olyan esetleges útvonalat, amely az internetforgalmat a helyszínen és/vagy a virtuális készülékek kényszerített bújtatást végez, más néven is az Azure szolgáltatás forgalmát a ugyanaz, mint az internetforgalmat útvonalon kényszeríti. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára. 

  A végpontok a szolgáltatás forgalmát a virtuális hálózatról közvetlenül az Azure-szolgáltatásra vezetik a Microsoft Azure gerinchálózatán át. Ha az Azure gerinchálózatán tartja az adatforgalmat, továbbra is naplózhatja és monitorozhatja a virtuális hálózatok kimenő internetforgalmát a kényszerített bújtatáson keresztül anélkül, hogy ez kihatna a szolgáltatás forgalmára. További információk [a felhasználók által meghatározott útvonalakkal és a kényszerített bújtatással](virtual-networks-udr-overview.md) kapcsolatban.
- **Egyszerű beállítás kevesebb felügyeleti igénnyel**: Lefoglalt, nyilvános IP-címek már nincs szüksége a virtuális hálózatok biztonságossá tétele Azure-erőforrások IP-tűzfalon keresztül. A szolgáltatásvégpontok beállításához nincs szükség NAT- és útválasztó eszközökre. A szolgáltatásvégpontok egy egyszerű kattintással konfigurálhatóak az alhálózatokon. A végpontok kezeléséhez nincs szükség további erőforrásokra.

## <a name="limitations"></a>Korlátozások

- A szolgáltatás csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon érhető el.
- A végpontok az Azure-beli virtuális hálózatokon konfigurált alhálózatokon vannak engedélyezve. A végpontok nem használhatók a helyszíni eredetű, az Azure-szolgáltatásokba irányuló forgalom továbbítására. További információt [az Azure-szolgáltatások helyszíni hozzáférésének biztosítását](#securing-azure-services-to-virtual-networks) ismertető szakaszban talál.
- Az Azure SQL esetében a szolgáltatásvégpontok csak az adott virtuális hálózat régióján belül vannak hatással az Azure-szolgáltatások forgalmára. Az Azure Storage RA-GRS- és GRS-forgalmának támogatása érdekében a végpontok azokra a párosított régiókra is kiterjednek, amelyekben a virtuális hálózat üzembe van helyezve. További információk az [Azure párosított régióiról](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Az ADLS általános 1 a VNet-integráció funkció csak akkor használható, az azonos régión belüli virtuális hálózatok esetében.

## <a name="securing-azure-services-to-virtual-networks"></a>Azure-szolgáltatások biztosítása virtuális hálózatokhoz

- A virtuális hálózatok szolgáltatásvégpontjai adják meg a virtuális hálózatok identitását az Azure-szolgáltatás felé. Ha a szolgáltatásvégpontok engedélyezve vannak a virtuális hálózaton, akkor Azure-szolgáltatásbeli erőforrásokat biztosíthat a virtuális hálózatnak úgy, hogy az erőforrásokhoz megad egy virtuális hálózati szabályt.
- Manapság az Azure-szolgáltatások virtuális hálózatokról induló forgalma nyilvános IP-címeket használ forrás IP-ként. A szolgáltatásvégpontok használatakor a szolgáltatások forgalma átvált a virtuális hálózatok privát címeinek forrás IP-címekként való használatára a virtuális hálózatról az Azure-szolgáltatás felé irányuló forgalom esetén. Ez a váltás lehetővé teszi a szolgáltatások elérését anélkül, hogy szükség lenne az IP-tűzfalakban használt lefoglalt, nyilvános IP-címekre.

>[!NOTE]
> A szolgáltatásvégpontokkal a szolgáltatásforgalom alhálózatán lévő virtuális gépek forrás IP-címei a nyilvános IPv4-címek használatáról átváltanak magánhálózati IPv4-címek használatára. Az Azure-beli nyilvános IP-címeket használó Azure-szolgáltatáshoz tartozó meglévő tűzfalszabályok a váltás után nem fognak működni. A szolgáltatásvégpontok beállítása előtt győződjön meg arról, hogy az Azure-szolgáltatásokhoz tartozó tűzfalszabályok engedélyezik ezt a váltást. A szolgáltatásvégpontok konfigurálása során az alhálózatról érkező szolgáltatásforgalom átmenetileg megszakadhat. 
 
- __Az Azure-szolgáltatások helyszíni hozzáférésének biztosítása:__

  Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatási erőforrások nem érhetők el helyszíni hálózatokról. Ha engedélyezni szeretné a helyszíni eredetű forgalmat, ahhoz engedélyeznie kell a nyilvános (általában NAT) IP-címeket is a helyszíni vagy ExpressRoute-kapcsolatokon. Ezeket az IP-címeket az Azure-szolgáltatási erőforrások IP-tűzfalainak konfigurálásával lehet megadni.

  ExpressRoute: Ha használ [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a helyszíni eredetű nyilvános társviszony-létesítéshez vagy Microsoft társviszony-létesítés, lesz azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítés esetén a használt NAT IP-cím(ek)et vagy az ügyfél vagy a szolgáltató adja meg. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címek keresése [nyisson egy támogatási jegyet az expressroute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon keresztül. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Azure-szolgáltatások biztosítása virtuális hálózatokhoz](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguráció

- A szolgáltatásvégpontokat a virtuális hálózatok alhálózatain konfiguráljuk. A végpontok bármilyen típusú számítási példányokkal működnek, ha azok az adott alhálózaton belül futnak.
- A támogatott Azure-szolgáltatásokhoz (például az Azure Storage-hez vagy az Azure SQL Database-hez) egy alhálózaton több szolgáltatásvégpont is konfigurálható.
- Az Azure SQL Database esetében a virtuális hálózatoknak és az Azure-szolgáltatási erőforrásnak ugyanabban a régióban kell lenniük. A GRS- és RA-GRS Azure Storage-fiókok használata esetén az elsődleges fióknak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Minden egyéb szolgáltatás esetében az Azure-szolgáltatások erőforrásai bármelyik régióban üzemelő virtuális hálózathoz leköthetők. 
- Az a virtuális hálózat, ahol a végpont konfigurálva van, lehet az Azure-szolgáltatási erőforrással egyazon előfizetésben, de egy másikban is. A végpontok beállításához és az Azure-szolgáltatások biztosításához szükséges engedélyekkel kapcsolatos további információkért lásd a [kiépítést](#Provisioning) ismertető szakaszt.
- A támogatott szolgáltatások esetében a szolgáltatásvégpontokat használó virtuális hálózatokhoz biztosíthat új vagy meglévő erőforrásokat is.

### <a name="considerations"></a>Megfontolandó szempontok

- Egy szolgáltatásvégpont engedélyezése után az alhálózaton lévő virtuális gépek átváltanak a nyilvános IPv4-címek használatáról a magánhálózati IPv4-címeik forrásként való használatára, amikor az adott alhálózatról kommunikálnak a szolgáltatással. A váltás során bármilyen, a szolgáltatás felé nyitott fennálló TCP-kapcsolat bezárul. Győződjön meg arról, hogy semmilyen kritikus feladat nem fut, amikor egy alhálózaton engedélyez vagy letilt egy szolgáltatáshoz vezető szolgáltatásvégpontot. Továbbá győződjön meg arról is, hogy az alkalmazásai képesek automatikusan csatlakozni az Azure-szolgáltatásokhoz az IP-címváltást követően.

  Az IP-címváltás csak a virtuális hálózatról kimenő szolgáltatásforgalomra van hatással. A virtuális gépekhez rendelt nyilvános IPv4-címekre bejövő, illetve azokról kimenő bármilyen egyéb forgalomra a váltás nincs hatással. Azure-szolgáltatások esetében, ha a meglévő tűzfalszabályok az Azure nyilvános IP-címeit használják, akkor a virtuális hálózat privát címeire történő váltást követően a szabályok nem működnek tovább.
- A szolgáltatásvégpontok használatakor az Azure-szolgáltatások DNS-bejegyzései változatlanok maradnak, és továbbra is feloldják az Azure-szolgáltatáshoz rendelt nyilvános IP-címeket.

- Hálózati biztonsági csoportok (NSG-k) szolgáltatásvégpontokkal:
  - Alapértelmezés szerint az NSG-k engedélyezik a kimenő internetforgalmat, így a virtuális hálózatról az Azure-szolgáltatások felé tartó forgalmat is. Ez a szolgáltatásvégpontok használatával is ugyanígy működik. 
  - Ha szeretne megtagadni minden kimenő internetforgalmat, és csak a meghatározott Azure-szolgáltatások felé menő forgalmat szeretné engedélyezni, ezt az NSG-kben a [szolgáltatáscímkék](security-overview.md#service-tags) használatával teheti meg. Az NSG-szabályok között megadhatja célként a támogatott Azure-szolgáltatásokat, az egyes címkékhez tartozó IP-címek karbantartását pedig az Azure biztosítja. További információért lásd az [NSG-khez elérhető Azure-szolgáltatáscímkéket](security-overview.md#service-tags) ismertető szakaszt. 

### <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: Biztonságos Azure-szolgáltatások több virtuális hálózaton vagy virtuális hálózaton belüli több alhálózaton, egymástól függetlenül engedélyezze a szolgáltatásvégpontokat az egyes alhálózatok, és biztonságos Azure-szolgáltatások erőforrásait az alhálózatok.
- **Az Azure-szolgáltatások virtuális hálózatról kimenő forgalom szűrése**: Ha meg szeretné vizsgálni vagy szűrni a virtuális hálózatról egy Azure-szolgáltatás felé irányuló forgalom, a virtuális hálózaton belüli hálózati virtuális készüléken is telepítheti. Ezután szolgáltatásvégpontokat alkalmazhat azon az alhálózaton, ahol a hálózati virtuális készülék üzemel, hogy csak ennek az alhálózatnak biztosítsa az Azure-szolgáltatási erőforrásokat. Ez a forgatókönyv akkor lehet hasznos, ha a virtuális hálózatnak az Azure-szolgáltatásokhoz való hozzáférését bizonyos Azure-erőforrásokra szeretné korlátozni a hálózati virtuális készülékek szűrésének segítségével. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](/azure/architecture/reference-architectures/dmz/nva-ha) foglalkozó témakört.
- **Azure-erőforrások közvetlenül virtuális hálózatokra telepített szolgáltatások biztosítása**: Különböző Azure-szolgáltatások virtuális hálózatban lévő adott alhálózatain közvetlenül is telepíthető. Az Azure-szolgáltatási erőforrások a [felügyelt szolgáltatási](virtual-network-for-azure-services.md) alhálózatokon egy szolgáltatásvégpont beállításával biztosíthatók.
- **Az Azure virtuális gép forgalmát lemez**: Virtuálisgép-lemez forgalom (ideértve a csatlakoztatást és leválasztást, Lemezforgalmát), felügyelt és nem felügyelt lemezek nem befolyásolja a Szolgáltatásvégpontok Azure Storage útválasztásának megváltozása. A lapblobok REST-alapú elérését korlátozhatja a kívánt hálózatokra szolgáltatásvégpontok és [Azure Storage-hálózati szabályok](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) segítségével. 

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

A szolgáltatásvégpontok a virtuális hálózatokon külön-külön konfigurálhatók egy olyan felhasználó által, akiknek írási hozzáférése van egy virtuális hálózathoz. Ahhoz, hogy egy felhasználó Azure-szolgáltatási erőforrásokat rendelhessen egy virtuális hálózathoz, rendelkeznie kell a hozzáadott alhálózatokra vonatkozó *Microsoft.Network/JoinServicetoaSubnet* engedéllyel. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak ugyanahhoz az előfizetéshez, de különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, akkor az erőforrásoknak ugyanahhoz az Active Directory- (AD-) bérlőhöz kell tartozniuk. 

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpontok használatának nincs további felára. Az Azure-szolgáltatások (Azure Storage, Azure SQL Database stb.) aktuális díjszabási modellje az irányadó.

A virtuális hálózatokon korlátlan számú szolgáltatásvégpont üzemelhet.

Az Azure-szolgáltatási erőforrások (például Azure-tárfiókok) esetében a szolgáltatások korlátozhatják az erőforrás biztosításához használt alhálózatok számát. Részletekért tekintse meg a [Következő lépések](#next-steps) című szakaszban felsorolt szolgáltatások dokumentációit.

## <a name="virtual-network-service-endpoint-policies"></a>Virtuális hálózati szolgáltatásvégpont-szabályzat 

Virtuális hálózati szolgáltatásvégpont-szabályzat lehetővé teszi az Azure-szolgáltatások, csak bizonyos Azure-szolgáltatási erőforrások, így keresztül a Szolgáltatásvégpontok a virtuális hálózati forgalmának szűrése. Szolgáltatásvégpont-szabályzat részletes hozzáférés-vezérlés az Azure-szolgáltatások virtuális hálózati forgalomhoz adja meg. További információ: [Virtuális hálózati szolgáltatásvégpont-szabályzat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Gyakori kérdések

A gyakori kérdések, tekintse meg [virtuális hálózati szolgáltatási végpont – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>További lépések

- [Virtuális hálózati szolgáltatásvégpontok konfigurálása](tutorial-restrict-network-access-to-resources.md) – útmutató
- [Azure Storage-fiókok biztosítása virtuális hálózatokhoz](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) – útmutató
- [Azure SQL Database biztosítása virtuális hálózatokhoz](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) – útmutató
- Ismerje meg, hogyan [biztonságossá tétele az Azure SQL Data Warehouse egy virtuális hálózathoz](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure-szolgáltatások integrálása virtuális hálózatokon](virtual-network-for-azure-services.md) – útmutató
- Ismerje meg [virtuális hálózati szolgáltatásvégpont-szabályzat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Gyors útmutató: [Az Azure resource manager-sablon](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) egy virtuális hálózat alhálózati szolgáltatásvégpont beállításához, és biztonságossá tétele az Azure Storage-fiókot, arra az alhálózatra.

