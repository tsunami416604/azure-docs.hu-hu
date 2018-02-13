---
title: "Azure-beli virtuális hálózati szolgáltatásvégpontok | Microsoft Docs"
description: "Tudnivalók Azure-erőforrások virtuális hálózatokról történő közvetlen elérésének engedélyezéséről szolgáltatásvégpontok használatával."
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 61859e86f38e4666be01f218922ce00c698de960
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

A virtuális hálózatok (VNet) szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik a virtuális hálózat magáncímterét és a VNet identitását az Azure-szolgáltatásokra. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.

Ez a szolgáltatás a következő Azure-szolgáltatásokhoz és -régiókhoz érhető el:

- **Azure Storage**: Általánosan elérhető. Az Azure nyilvános felhő és az Azure Government minden régiója.
- **Azure SQL**: Előzetes verzióban érhető el. Az Azure nyilvános felhő összes régiója.

Az előzetes verzióval kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

>[!NOTE]
> Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Főbb előnyök

A szolgáltatásvégpontok az alábbi előnyöket nyújtják:

- **Nagyobb biztonság az Azure-szolgáltatások erőforrásai számára**: A szolgáltatásvégpontokkal az Azure-szolgáltatások erőforrásai leköthetőek a virtuális hálózathoz. A szolgáltatás-erőforrások virtuális hálózathoz való lekötése nagyobb biztonságot eredményez, mivel így az erőforrások egyáltalán nem lesznek elérhetők a nyilvános internetről, és csak a virtuális hálózatból érkező forgalom lesz engedélyezett.
- **Optimális útválasztás az Azure-szolgáltatás forgalma számára a virtuális hálózatból**: Jelenleg a virtuális hálózat minden olyan útvonala, amely az internetforgalmat a helyszínen és/vagy virtuális készülékeken keresztüli áthaladásra kényszeríti, azaz kényszerített bújtatást végez, az Azure szolgáltatás forgalmát is ugyanazon az útvonalon kényszeríti át, mint az internetforgalmat. A szolgáltatásvégpontok optimális útválasztást biztosítanak az Azure-forgalom számára. 

  A végpontok a szolgáltatás forgalmát a virtuális hálózatról közvetlenül az Azure-szolgáltatásra vezetik a Microsoft Azure gerinchálózatán át. Ha az Azure gerinchálózatán tartja az adatforgalmat, továbbra is naplózhatja és monitorozhatja a virtuális hálózatok kimenő internetforgalmát a kényszerített bújtatáson keresztül anélkül, hogy ez kihatna a szolgáltatás forgalmára. További információk [a felhasználók által meghatározott útvonalakkal és a kényszerített bújtatással](virtual-networks-udr-overview.md) kapcsolatban.
- **Egyszerű beállítás kevesebb felügyeleti igénnyel**: Többé nincs szükség lefoglalt nyilvános IP-címekre a virtuális hálózatokban az Azure-erőforrások IP-tűzfalon keresztül végzett biztosításához. A szolgáltatásvégpontok beállításához nincs szükség NAT- és útválasztó eszközökre. A szolgáltatásvégpontok egy egyszerű kattintással konfigurálhatóak az alhálózatokon. A végpontok kezeléséhez nincs szükség további erőforrásokra.

## <a name="limitations"></a>Korlátozások

- A szolgáltatás csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon érhető el.
- A végpontok az Azure-beli virtuális hálózatokon konfigurált alhálózatokon vannak engedélyezve. A végpontok nem használhatók a helyszíni eredetű, az Azure-szolgáltatásokba irányuló forgalom továbbítására. További információt [az Azure-szolgáltatások helyszíni hozzáférésének biztosítását](#securing-azure-services-to-virtual-networks) ismertető szakaszban talál.
- A szolgáltatásvégpontok csak az adott virtuális hálózat régióján belül vannak hatással az Azure-szolgáltatások forgalmára. Az Azure Storage RA-GRS- és GRS-forgalmának támogatása érdekében a végpontok azokra a párosított régiókra is kiterjednek, amelyekben a virtuális hálózat üzembe van helyezve. További információk az [Azure párosított régióiról.](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)

## <a name="securing-azure-services-to-virtual-networks"></a>Azure-szolgáltatások biztosítása virtuális hálózatokhoz

- A virtuális hálózatok szolgáltatásvégpontjai adják meg a virtuális hálózatok identitását az Azure-szolgáltatás felé. Ha a szolgáltatásvégpontok engedélyezve vannak a virtuális hálózaton, akkor Azure-szolgáltatásbeli erőforrásokat biztosíthat a virtuális hálózatnak úgy, hogy az erőforrásokhoz megad egy virtuális hálózati szabályt.
- Manapság az Azure-szolgáltatások virtuális hálózatokról induló forgalma nyilvános IP-címeket használ forrás IP-ként. A szolgáltatásvégpontok használatakor a szolgáltatások forgalma átvált a virtuális hálózatok privát címeinek forrás IP-címekként való használatára a virtuális hálózatról az Azure-szolgáltatás felé irányuló forgalom esetén. Ez a váltás lehetővé teszi a szolgáltatások elérését anélkül, hogy szükség lenne az IP-tűzfalakban használt lefoglalt, nyilvános IP-címekre.
- __Az Azure-szolgáltatások helyszíni hozzáférésének biztosítása:__

  Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatási erőforrások nem érhetők el helyszíni hálózatokról. Ha engedélyezni szeretné a helyszíni eredetű forgalmat, ahhoz engedélyeznie kell a nyilvános (általában NAT) IP-címeket is a helyszíni vagy ExpressRoute-kapcsolatokon. Ezeket az IP-címeket az Azure-szolgáltatási erőforrások IP-tűzfalainak konfigurálásával lehet megadni.

  ExpressRoute: Ha [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-ot használ a helyszíni eredetű nyilvános társviszony-létesítéshez vagy Microsoft-társviszony-létesítéshez, meg kell határoznia a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. Microsoft-társviszony-létesítés esetén a használt NAT IP-cím(ek)et vagy az ügyfél vagy a szolgáltató adja meg. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Azure-szolgáltatások biztosítása virtuális hálózatokhoz](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguráció

- A szolgáltatásvégpontokat a virtuális hálózatok alhálózatain konfiguráljuk. A végpontok bármilyen típusú számítási példányokkal működnek, ha azok az adott alhálózaton belül futnak.
- A támogatott Azure-szolgáltatásokhoz (például az Azure Storage-hez vagy az Azure SQL Database-hez) egy alhálózaton több szolgáltatásvégpont is konfigurálható.
- A virtuális hálózatoknak és az Azure-szolgáltatási erőforrásnak ugyanabban a régióban kell lenniük. A GRS- és RA-GRS Azure Storage-fiókok használata esetén az elsődleges fióknak és a virtuális hálózatnak ugyanabban a régióban kell lennie.
- Az a virtuális hálózat, ahol a végpont konfigurálva van, lehet az Azure-szolgáltatási erőforrással egyazon előfizetésben, de egy másikban is. A végpontok beállításához és az Azure-szolgáltatások biztosításához szükséges engedélyekkel kapcsolatos további információkért lásd a [kiépítést](#Provisioning) ismertető szakaszt.
- A támogatott szolgáltatások esetében a szolgáltatásvégpontokat használó virtuális hálózatokhoz biztosíthat új vagy meglévő erőforrásokat is.

### <a name="considerations"></a>Megfontolandó szempontok

- Egy szolgáltatásvégpont engedélyezése után az alhálózaton lévő virtuális gépek átváltanak a nyilvános IPv4-címek használatáról a magánhálózati IPv4-címeik forrásként való használatára, amikor az adott alhálózatról kommunikálnak a szolgáltatással. A váltás során bármilyen, a szolgáltatás felé nyitott fennálló TCP-kapcsolat bezárul. Győződjön meg arról, hogy semmilyen kritikus feladat nem fut, amikor egy alhálózaton engedélyez vagy letilt egy szolgáltatáshoz vezető szolgáltatásvégpontot. Továbbá győződjön meg arról is, hogy az alkalmazásai képesek automatikusan csatlakozni az Azure-szolgáltatásokhoz az IP-címváltást követően.

  Az IP-címváltás csak a virtuális hálózatról kimenő szolgáltatásforgalomra van hatással. A virtuális gépekhez rendelt nyilvános IPv4-címekre bejövő, illetve azokról kimenő bármilyen egyéb forgalomra a váltás nincs hatással. Azure-szolgáltatások esetében, ha a meglévő tűzfalszabályok az Azure nyilvános IP-címeit használják, akkor a virtuális hálózat privát címeire történő váltást követően a szabályok nem működnek tovább.
- A szolgáltatásvégpontok használatakor az Azure-szolgáltatások DNS-bejegyzései változatlanok maradnak, és továbbra is feloldják az Azure-szolgáltatáshoz rendelt nyilvános IP-címeket.
- Hálózati biztonsági csoportok (NSG-k) szolgáltatásvégpontokkal:
  - Alapértelmezés szerint az NSG-k engedélyezik a kimenő internetforgalmat, így a virtuális hálózatról az Azure-szolgáltatások felé tartó forgalmat is. Ez a szolgáltatásvégpontok használatával is ugyanígy működik. 
  - Ha szeretne megtagadni minden kimenő internetforgalmat, és csak a meghatározott Azure-szolgáltatások felé menő forgalmat szeretné engedélyezni, ezt az NSG-kben az __Azure-szolgáltatáscímkék__ használatával teheti meg. Az NSG-szabályok között megadhatja célként a támogatott Azure-szolgáltatásokat, az egyes címkékhez tartozó IP-címek karbantartását pedig az Azure biztosítja. További információért lásd az [NSG-khez elérhető Azure-szolgáltatáscímkéket](https://aka.ms/servicetags) ismertető szakaszt. 

### <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: Ha egy vagy több virtuális hálózaton belüli több alhálózaton is szeretné biztosítani az Azure-szolgáltatásokat, akkor engedélyezze a szolgáltatásvégpontokat külön-külön mindegyik alhálózaton, hogy mindegyik számára biztosíthassa az Azure-szolgáltatások erőforrásait.
- **A virtuális hálózatról az Azure-szolgáltatások felé irányuló forgalom szűrése**: Ha vizsgálni vagy szűrni szeretné a virtuális hálózatról egy Azure-szolgáltatás felé menő forgalmat, ahhoz egy virtuális hálózaton belül üzembe helyezhet egy hálózati virtuális készüléket. Ezután szolgáltatásvégpontokat alkalmazhat azon az alhálózaton, ahol a hálózati virtuális készülék üzemel, hogy csak ennek az alhálózatnak biztosítsa az Azure-szolgáltatási erőforrásokat. Ez a forgatókönyv akkor lehet hasznos, ha a virtuális hálózatnak az Azure-szolgáltatásokhoz való hozzáférését bizonyos Azure-erőforrásokra szeretné korlátozni a hálózati virtuális készülékek szűrésének segítségével. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json) foglalkozó témakört.
- **Azure-erőforrások biztosítása közvetlenül virtuális hálózatokra telepített szolgáltatások számára**: Különböző Azure-szolgáltatások közvetlenül a virtuális hálózatok adott alhálózatain is üzembe helyezhetők. Az Azure-szolgáltatási erőforrások a [felügyelt szolgáltatási](virtual-network-for-azure-services.md) alhálózatokon egy szolgáltatásvégpont beállításával biztosíthatók.

### <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás

Ha konfigurálta a szolgáltatásvégpontokat egy adott szolgáltatáshoz, a következőképp ellenőrizheti, hogy a szolgáltatásvégpont útvonala érvényes-e: 
 
- Szolgáltatáskérések forrás IP-címeinek hitelesítése a szolgáltatási diagnosztikában. Minden szolgáltatásvégponttal rendelkező új kérés esetében a forrás IP-címnél a virtuális hálózat magánjellegű IP-címe jelenik meg, amely a virtuális hálózatról a hozzáférést kérvényező ügyfélhez van rendelve. A végpont nélkül ez a cím egy Azure-beli nyilvános IP-cím.
- Az érvényes útvonalak megtekintése az egyes alhálózatok hálózati adapterein. A szolgáltatásra mutató útvonal:
  - Egy pontosabban meghatározott alapértelmezett útvonalat jelent, az egyes szolgáltatások előtagtartományának megfelelően.
  - Az útvonal nextHopType típusa: *VirtualNetworkServiceEndpoint*
  - Azt jelzi, hogy a szolgáltatáshoz egy közvetlenebb kapcsolat vezet, szemben bármilyen kényszerített bújtatású útvonallal.

>[!NOTE]
> Azure-szolgáltatás használata esetén a szolgáltatásvégpont útvonalai felülbírálják a megfelelő címelőtag BGP- vagy UDR-útvonalait. További tudnivalók az [érvényes útvonalak hibaelhárításáról.](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpontok a virtuális hálózatokon külön-külön konfigurálhatók egy olyan felhasználó által, akiknek írási hozzáférése van egy virtuális hálózathoz. Ahhoz, hogy egy felhasználó Azure-szolgáltatási erőforrásokat rendelhessen egy virtuális hálózathoz, rendelkeznie kell a hozzáadott alhálózatokra vonatkozó *Microsoft.Network/JoinServicetoaSubnet* engedéllyel. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a bizonyos engedélyek [egyéni szerepkörökhöz](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak ugyanahhoz az előfizetéshez, de különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, akkor az erőforrásoknak ugyanahhoz az Active Directory- (AD-) bérlőhöz kell tartozniuk. 

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpontok használatának nincs további felára. Az Azure-szolgáltatások (Azure Storage, Azure SQL Database) aktuális díjszabási modellje az irányadó.

A virtuális hálózatokon korlátlan számú szolgáltatásvégpont üzemelhet.

Az Azure-szolgáltatási erőforrások (például Azure-tárfiókok) esetében a szolgáltatások korlátozhatják az erőforrás biztosításához használt alhálózatok számát. Részletekért tekintse meg a [Következő lépések](#next-steps) című szakaszban felsorolt szolgáltatások dokumentációit.

## <a name="next-steps"></a>Következő lépések

- [Virtuális hálózati szolgáltatásvégpontok konfigurálása](virtual-network-service-endpoints-configure.md) – útmutató
- [Azure Storage-fiókok biztosítása virtuális hálózatokhoz](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) – útmutató
- [Azure SQL Database biztosítása virtuális hálózatokhoz](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) – útmutató
- [Azure-szolgáltatások integrálása virtuális hálózatokon](virtual-network-for-azure-services.md) – útmutató
-  Rövid útmutató: [Azure Resource Manager-sablon](https://azure.microsoft.com/en-us/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) szolgáltatásvégpont beállításához egy VNet alhálózatán, és egy Azure Storage-fiók létrehozása azon az alhálózaton.

