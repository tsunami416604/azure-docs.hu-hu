---
title: Az Azure virtuális hálózati szolgáltatásának végpontjai
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
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244783"
---
# <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

A Virtuális hálózat (VNet) szolgáltatásvégpontok kiterjesztik a virtuális hálózat magáncímterét. A végpontok is kiterjeszti a virtuális hálózat identitását az Azure-szolgáltatások közvetlen kapcsolaton keresztül. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.

Ez a funkció a következő Azure-szolgáltatások és -régiók számára érhető el. A *Microsoft.\* * erőforrás zárójelben van. Engedélyezze ezt az erőforrást az alhálózati oldalról a szolgáltatás végpontjainak konfigurálása közben:

**Általánosan elérhető**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage):* Általánosan elérhető az összes Azure-régióban.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Általánosan elérhető az összes Azure-régióban.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Általánosan elérhető az összes Azure-régióban.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás elérhető.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás érhető el.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Általánosan elérhető az Azure-régiókban, ahol az adatbázis-szolgáltatás érhető el.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Általánosan elérhető az összes Azure-régióban.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Általánosan elérhető az összes Azure-régióban.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Általánosan elérhető az összes Azure-régióban.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Általánosan elérhető az összes Azure-régióban.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Általánosan elérhető minden Olyan Azure-régióban, ahol az ADLS Gen1 elérhető.
- **[Azure App Service:](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** Általánosan elérhető minden Olyan Azure-régióban, ahol az alkalmazásszolgáltatás elérhető.

**Nyilvános előzetes verzió**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Előzetes verzió minden Olyan Azure-régióban elérhető, ahol az Azure Container Registry elérhető.

A legfrissebb értesítésekért tekintse meg az [Azure-beli virtuális hálózatok frissítéseinek](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

## <a name="key-benefits"></a>Főbb előnyök

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Az Azure-szolgáltatás erőforrásainak fokozott biztonsága:** A virtuális hálózat privát címterei átfedhetik egymást. Az egymást átfedő szóközök nem használhatók a virtuális hálózatból származó forgalom egyedi azonosítására. A szolgáltatásvégpontok lehetővé teszik az Azure-szolgáltatás erőforrásainak biztonságossá tétele a virtuális hálózat virtuális hálózatának a szolgáltatásra való kiterjesztésével. Miután engedélyezte a szolgáltatásvégpontok a virtuális hálózatban, hozzáadhat egy virtuális hálózati szabályt az Azure-szolgáltatás erőforrásainak védelméhez a virtuális hálózathoz. A szabály kiegészítése nagyobb biztonságot nyújt azáltal, hogy teljes mértékben eltávolítja az erőforrásokhoz való nyilvános internet-hozzáférést, és csak a virtuális hálózatról engedélyezi a forgalmat.
- **Optimális útválasztás az Azure szolgáltatásforgalmára a virtuális hálózatról:** Ma a virtuális hálózat minden olyan útvonala, amely a helyszíni és/vagy virtuális készülékekre kényszeríti az internetes forgalmat, szintén arra kényszeríti az Azure szolgáltatási forgalmát, hogy ugyanazt az útvonalat vegye, mint az internetes forgalmat. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára. 

  A végpontok a szolgáltatás forgalmát a virtuális hálózatról közvetlenül az Azure-szolgáltatásra vezetik a Microsoft Azure gerinchálózatán át. Ha az Azure gerinchálózatán tartja az adatforgalmat, továbbra is naplózhatja és monitorozhatja a virtuális hálózatok kimenő internetforgalmát a kényszerített bújtatáson keresztül anélkül, hogy ez kihatna a szolgáltatás forgalmára. A felhasználó által definiált útvonalakról és a kényszerített bújtatásról az [Azure virtuális hálózati forgalom útválasztása című](virtual-networks-udr-overview.md)témakörben talál további információt.
- **Egyszerű beállítás kevesebb felügyeleti igénnyel**: Többé nincs szükség lefoglalt nyilvános IP-címekre a virtuális hálózatokban az Azure-erőforrások IP-tűzfalon keresztül végzett biztosításához. Nincs szükség hálózati címfordításra (NAT) vagy átjáróeszközökre a szolgáltatásvégpontok beállításához. A szolgáltatásvégpontok konfigurálhatók egy alhálózatra történő egyszerű kattintással. A végpontok karbantartása nem jelent további többletköltséget.

## <a name="limitations"></a>Korlátozások

- A szolgáltatás csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon érhető el.
- A végpontok az Azure-beli virtuális hálózatokon konfigurált alhálózatokon vannak engedélyezve. Végpontok nem használható a forgalmat a telephelyéről az Azure-szolgáltatások. További információ: [Secure Azure-szolgáltatás hozzáférés a helyszíni](#secure-azure-services-to-virtual-networks)
- Az Azure SQL esetében a szolgáltatásvégpontok csak az adott virtuális hálózat régióján belül vannak hatással az Azure-szolgáltatások forgalmára. Az Azure Storage esetében a végpontok kiterjednek a párosított régiókra is, ahol a virtuális hálózatot az olvasási hozzáférésű georedundáns tárolás (RA-GRS) és a georedundáns tárolás (GRS) forgalom támogatására telepíti. További információ: [Azure párosított régiók](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Az Azure Data Lake Storage (ADLS) Gen 1 esetében a virtuális hálózat integrációs funkció csak az ugyanabban a régióban lévő virtuális hálózatokhoz érhető el. Azt is vegye figyelembe, hogy az ADLS Gen1 virtuális hálózati integrációja a virtuális hálózat és az Azure Active Directory (Azure AD) közötti virtuális hálózati végpont biztonságát használja további biztonsági jogcímek létrehozásához a hozzáférési jogkivonatban. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs Data Lake Storage-fiókkal, és engedélyezi a hozzáférést. A szolgáltatásvégpontokat támogató szolgáltatások alatt felsorolt *Microsoft.AzureActiveDirectory* címke csak az ADLS Gen 1 szolgáltatásvégpontjainak támogatására szolgál. Az Azure AD natívan nem támogatja a szolgáltatásvégpontokat. Az Azure Data Lake Store Gen 1 virtuális hálózat integrációjáról további információt az [Azure Data Lake Storage Gen1 hálózati biztonsága című témakörben talál.](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="secure-azure-services-to-virtual-networks"></a>Biztonságos Azure-szolgáltatások virtuális hálózatokhoz

- A virtuális hálózatok szolgáltatásvégpontjai adják meg a virtuális hálózatok identitását az Azure-szolgáltatás felé. Miután engedélyezte a szolgáltatásvégpontok a virtuális hálózatban, hozzáadhat egy virtuális hálózati szabályt az Azure-szolgáltatás erőforrásainak védelméhez a virtuális hálózathoz.
- Manapság az Azure-szolgáltatások virtuális hálózatokról induló forgalma nyilvános IP-címeket használ forrás IP-ként. A szolgáltatásvégpontok használatakor a szolgáltatások forgalma átvált a virtuális hálózatok privát címeinek forrás IP-címekként való használatára a virtuális hálózatról az Azure-szolgáltatás felé irányuló forgalom esetén. Ez a váltás lehetővé teszi a szolgáltatások elérését anélkül, hogy szükség lenne az IP-tűzfalakban használt lefoglalt, nyilvános IP-címekre.

   >[!NOTE]
   > A szolgáltatásvégpontokkal a szolgáltatásforgalom alhálózatán lévő virtuális gépek forrás IP-címei a nyilvános IPv4-címek használatáról átváltanak magánhálózati IPv4-címek használatára. Az Azure-beli nyilvános IP-címeket használó Azure-szolgáltatáshoz tartozó meglévő tűzfalszabályok a váltás után nem fognak működni. A szolgáltatásvégpontok beállítása előtt győződjön meg arról, hogy az Azure-szolgáltatásokhoz tartozó tűzfalszabályok engedélyezik ezt a váltást. A szolgáltatásvégpontok konfigurálása során az alhálózatról érkező szolgáltatásforgalom átmenetileg megszakadhat. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Biztonságos Azure-szolgáltatáshozzáférés a helyszíni

  Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatás-erőforrások nem érhetőek el helyszíni hálózatokról. Ha engedélyezni szeretné a helyszíni forgalmat, nyilvános (általában NAT) IP-címeket is engedélyeznie kell a helyszíni vagy ExpressRoute-ból. Ezeket az IP-címeket az Azure-szolgáltatás-erőforrások IP-tűzfal-konfigurációján keresztül adhathozzá.

  ExpressRoute: Ha az [ExpressRoute-ot](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nyilvános társviszony-létesítéshez vagy a Microsoft-társviszony-létesítéshez használja a helyiségből, azonosítania kell a használt NAT IP-címeket. Nyilvános társviszony-létesítéshez minden ExpressRoute-kapcsolati kapcsolat alapértelmezés szerint két NAT IP-címet használ az Azure szolgáltatásforgalmára, amikor a forgalom belép a Microsoft Azure hálózati gerinchálózatába. A Microsoft társviszony-létesítése esetén a NAT IP-címek vagy a szolgáltató által megadott vagy biztosított ügyfél.A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között.A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. Az ExpressRoute nyilvános és microsoftos társviszony-létesítési nat-jéről az [ExpressRoute NAT követelményei](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)című témakörben talál további információt.

![Azure-szolgáltatások biztosítása virtuális hálózatokhoz](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguráció

- Szolgáltatásvégpontok konfigurálása egy virtuális hálózat alhálózatán. A végpontok bármilyen típusú számítási példányokkal működnek, ha azok az adott alhálózaton belül futnak.
- Több szolgáltatásvégpontot is konfigurálhat az összes támogatott Azure-szolgáltatáshoz (például az Azure Storage-hoz vagy az Azure SQL Database-hez) egy alhálózaton.
- Az Azure SQL Database esetében a virtuális hálózatoknak és az Azure-szolgáltatási erőforrásnak ugyanabban a régióban kell lenniük. A GRS- és RA-GRS Azure Storage-fiókok használata esetén az elsődleges fióknak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Az összes többi szolgáltatás esetén az Azure-szolgáltatások erőforrásait bármely régióban virtuális hálózatokhoz biztosíthatja. 
- Az a virtuális hálózat, ahol a végpont konfigurálva van, lehet az Azure-szolgáltatási erőforrással egyazon előfizetésben, de egy másikban is. A végpontok beállításához és az Azure-szolgáltatások biztosításához szükséges engedélyekkel kapcsolatos további információkért lásd a [kiépítést](#provisioning) ismertető szakaszt.
- A támogatott szolgáltatások esetében a szolgáltatásvégpontokat használó virtuális hálózatokhoz biztosíthat új vagy meglévő erőforrásokat is.

### <a name="considerations"></a>Megfontolandó szempontok

- A szolgáltatásvégpont engedélyezése után az alhálózati kapcsolóban lévő virtuális gépek forrás IP-címei. A forrás IP-címek a nyilvános IPv4-címek használatáról a saját IPv4-címükhasználatára váltanak, amikor az adott alhálózatról kommunikálnak a szolgáltatással. A váltás során bármilyen, a szolgáltatás felé nyitott fennálló TCP-kapcsolat bezárul. Győződjön meg arról, hogy semmilyen kritikus feladat nem fut, amikor egy alhálózaton engedélyez vagy letilt egy szolgáltatáshoz vezető szolgáltatásvégpontot. Továbbá győződjön meg arról is, hogy az alkalmazásai képesek automatikusan csatlakozni az Azure-szolgáltatásokhoz az IP-címváltást követően.

  Az IP-címváltás csak a virtuális hálózatról kimenő szolgáltatásforgalomra van hatással. Nincs hatással a virtuális gépekhez rendelt nyilvános IPv4-címekre vagy azokból érkező más forgalomra. Azure-szolgáltatások esetében, ha a meglévő tűzfalszabályok az Azure nyilvános IP-címeit használják, akkor a virtuális hálózat privát címeire történő váltást követően a szabályok nem működnek tovább.
- A szolgáltatás-végpontok, AZ Azure-szolgáltatások DNS-bejegyzései továbbra is a mai napig, és továbbra is feloldja az Azure-szolgáltatáshoz rendelt nyilvános IP-címek.

- Hálózati biztonsági csoportok (NSG-k) szolgáltatásvégpontokkal:
  - Alapértelmezés szerint az NSG-k engedélyezik a kimenő internetes forgalmat, és a virtuális hálózatról az Azure-szolgáltatásokba irányuló forgalmat is engedélyezik. Ez a forgalom továbbra is működik a szolgáltatás végpontok, ahogy van. 
  - Ha meg szeretné tagadni az összes kimenő internetes forgalmat, és csak az adott Azure-szolgáltatások forgalmát szeretné engedélyezni, ezt az NSG-k [szolgáltatáscímkéivel](security-overview.md#service-tags) teheti meg. Megadhatja a támogatott Azure-szolgáltatások célként az NSG-szabályok és az Azure is biztosítja az egyes címkek alapjául szolgáló IP-címek karbantartását. További információért lásd az [NSG-khez elérhető Azure-szolgáltatáscímkéket](security-overview.md#service-tags) ismertető szakaszt. 

### <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: Ha egy vagy több virtuális hálózaton belüli több alhálózaton is szeretné biztosítani az Azure-szolgáltatásokat, akkor engedélyezze a szolgáltatásvégpontokat külön-külön mindegyik alhálózaton, hogy mindegyik számára biztosíthassa az Azure-szolgáltatások erőforrásait.
- **A kimenő forgalom szűrése egy virtuális hálózatról az Azure-szolgáltatásokba:** Ha meg szeretné vizsgálni vagy szűrni szeretné az Azure-szolgáltatásnak virtuális hálózatról küldött forgalmat, üzembe helyezhet egy hálózati virtuális berendezést a virtuális hálózaton belül. Ezután szolgáltatásvégpontokat alkalmazhat azon az alhálózaton, ahol a hálózati virtuális készülék üzemel, hogy csak ennek az alhálózatnak biztosítsa az Azure-szolgáltatási erőforrásokat. Ez a forgatókönyv akkor lehet hasznos, ha hálózati virtuális berendezés szűrés használatával korlátozza az Azure szolgáltatás hozzáférést a virtuális hálózatcsak bizonyos Azure-erőforrások. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](/azure/architecture/reference-architectures/dmz/nva-ha) foglalkozó témakört.
- **Az Azure-erőforrások biztonságossá tétele a közvetlenül a virtuális hálózatokba üzembe helyezett szolgáltatások számára:** Közvetlenül telepítheti a különböző Azure-szolgáltatásokat a virtuális hálózat adott alhálózataiba. Az Azure-szolgáltatási erőforrások a [felügyelt szolgáltatási](virtual-network-for-azure-services.md) alhálózatokon egy szolgáltatásvégpont beállításával biztosíthatók.
- **Lemezforgalom egy Azure virtuális gépről:** A felügyelt és nem felügyelt lemezek virtuálisgép-lemez forgalmát nem érintik az Azure Storage szolgáltatásvégpontok útválasztási módosításai. Ez a forgalom magában foglalja a diskIO, valamint a csatlakoztatásés leválasztás. Korlátozhatja a LAPblobokhoz való REST-hozzáférést, hogy a szolgáltatásvégpontokon és az [Azure Storage hálózati szabályain](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)keresztül kiválassza a hálózatokat. 

### <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás

Miután konfigurálta a szolgáltatásvégpontokat egy adott szolgáltatáshoz, ellenőrizze, hogy a szolgáltatásvégpont útvonala érvényben van-e a következőképpen: 
 
- Szolgáltatáskérések forrás IP-címeinek hitelesítése a szolgáltatási diagnosztikában. Minden szolgáltatásvégponttal rendelkező új kérés esetében a forrás IP-címnél a virtuális hálózat magánjellegű IP-címe jelenik meg, amely a virtuális hálózatról a hozzáférést kérvényező ügyfélhez van rendelve. A végpont nélkül ez a cím egy Azure-beli nyilvános IP-cím.
- Az érvényes útvonalak megtekintése az egyes alhálózatok hálózati adapterein. A szolgáltatásra mutató útvonal:
  - Egy pontosabban meghatározott alapértelmezett útvonalat jelent, az egyes szolgáltatások előtagtartományának megfelelően.
  - Az útvonal nextHopType típusa: *VirtualNetworkServiceEndpoint*
  - Azt jelzi, hogy a szolgáltatással való közvetlenebb kapcsolat a kényszerített bújtatási útvonalakhoz képest

>[!NOTE]
> Azure-szolgáltatás használata esetén a szolgáltatásvégpont útvonalai felülbírálják a megfelelő címelőtag BGP- vagy UDR-útvonalait. További információt a [Hatékony útvonalakkal kapcsolatos hibaelhárítás](diagnose-network-routing-problem.md)című témakörben talál.

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpontokat a virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználó egymástól függetlenül konfigurálhatja. Az Azure-szolgáltatás erőforrásainak virtuális hálózathoz való biztonságossá tétele érdekében a felhasználónak engedéllyel kell rendelkeznie a *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* szolgáltatáshoz a hozzáadott alhálózatokhoz. A beépített szolgáltatás-rendszergazdai szerepkörök alapértelmezés szerint tartalmazzák ezt az engedélyt. Az engedélyt egyéni szerepkörök létrehozásával módosíthatja.

A beépített szerepkörökről további információt az [Azure-erőforrások beépített szerepkörei című témakörben talál.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha további információra van haa arról, hogy milyen speciális engedélyeket rendel egyéni szerepkörökhöz, olvassa el [az Egyéni szerepkörök az Azure-erőforrásokhoz című témakört.](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak ugyanahhoz az előfizetéshez, de különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, akkor az erőforrásoknak ugyanahhoz az Active Directory- (AD-) bérlőhöz kell tartozniuk. 

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpontok használatáért nem számítunk fel további díjat. Az Azure-szolgáltatások jelenlegi díjszabási modellje (Azure Storage, Azure SQL Database stb.) a mai napig érvényes.

Nincs korlátozva a szolgáltatás végpontjainak teljes száma egy virtuális hálózatban.

Bizonyos Azure-szolgáltatások, például az Azure Storage-fiókok, korlátozhatják az erőforrás védelméhez használt alhálózatok számát. A részleteket a különböző szolgáltatások dokumentációjában találja a [Következő lépések](#next-steps) szakaszban.

## <a name="vnet-service-endpoint-policies"></a>VNet szolgáltatásvégpont-házirendek 

A VNet-szolgáltatásvégpont-szabályzatok lehetővé teszik az Azure-szolgáltatások virtuális hálózati forgalmának szűrését. Ez a szűrő csak bizonyos Azure-szolgáltatás-erőforrásokat engedélyez a szolgáltatásvégpontok felett. A szolgáltatásvégpont-szabályzatok részletes hozzáférés-vezérlést biztosítanak az Azure-szolgáltatások virtuális hálózati forgalmához. További információt a [Virtuális hálózati szolgáltatás végpontházirendjei című témakörben talál.](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Gyakori kérdések

Gyakori kérdések a [Virtuális hálózati szolgáltatás végpontjainak gyakori kérdések – gyakori kérdések](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>További lépések

- [Virtuális hálózati szolgáltatás végpontjainak konfigurálása](tutorial-restrict-network-access-to-resources.md)
- [Azure Storage-fiók biztonságossá tétele virtuális hálózatra](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure SQL-adatbázis biztonságossá tétele virtuális hálózatra](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Azure SQL Data Warehouse biztonságossá tétele virtuális hálózatra](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Az Azure szolgáltatásintegrációja a virtuális hálózatokban](virtual-network-for-azure-services.md)
- [Virtuális hálózati szolgáltatás végpontházirendjei](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager-sablon](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

