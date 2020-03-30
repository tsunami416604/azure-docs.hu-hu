---
title: 'Azure ExpressRoute: Nyilvános társviszony-létesítés áthelyezése a Microsoft-társviszony-létesítésbe'
description: Ez a cikk bemutatja a nyilvános társviszony-létesítés áthelyezésének lépéseit a Microsoft ExpressRoute-alapú társviszony-létesítésbe.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 48ecfcc0d6241e7926892a3ca1c9925b0dc07241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436838"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Váltáson nyilvános társviszony-létesítésről Microsoft-társviszony-létesítésre

Ez a cikk segít a nyilvános társviszony-létesítési konfiguráció áthelyezésében a Microsoft-társviszony-létesítés leállás nélkül. Az ExpressRoute támogatja a Microsoft-társviszony-létesítés útvonalszűrőkkel való használatát az Azure PaaS-szolgáltatások, például az Azure Storage és az Azure SQL Database esetén. A Microsoft PaaS- és SaaS-szolgáltatásokhoz való hozzáféréshez most csak egy útválasztási tartományra van szüksége. Útvonalszűrők segítségével szelektíven meghirdetheti a PaaS-szolgáltatások előtagjait a felhasználni kívánt Azure-régiókhoz.

Az Azure nyilvános társviszony-létesítés1 NAT IP-címet társított minden BGP-munkamenethez. A Microsoft társviszony-létesítéslehetővé teszi a saját NAT-foglalások konfigurálását, valamint a szelektív előtaghirdetések hez használható útvonalszűrők használatát. A nyilvános társviszony-létesítés egy egyirányú szolgáltatás, amelynek segítségével a kapcsolat mindig a WAN-ról a Microsoft Azure-szolgáltatásokba kerül. A Microsoft Azure-szolgáltatások nem tudnak kapcsolatot kezdeményezni a hálózatba ezen az útválasztási tartományon keresztül.

Ha a nyilvános társviszony-létesítés engedélyezve van, csatlakozhat az összes Azure-szolgáltatáshoz. Nem engedélyezzük, hogy szelektíven válasszon olyan szolgáltatásokat, amelyekre útvonalakat hirdetünk. Míg a Microsoft társviszony-létesítés kétirányú kapcsolat, ahol a kapcsolat a Microsoft Azure szolgáltatásból a WAN-nal együtt kezdeményezhető. Az útválasztási tartományokról és társviszony-létesítésről az [ExpressRoute-áramkörök és útválasztási tartományok](expressroute-circuit-peerings.md)című témakörben talál további információt.

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

A Microsoft-társviszony-létesítéshez való csatlakozáshoz be kell állítania és kezelnie kell a NAT-t. Előfordulhat, hogy a kapcsolatszolgáltató felügyelt szolgáltatásként állítja be és kezeli a Hálózati címt. Ha azt tervezi, hogy az Azure PaaS és az Azure SaaS-szolgáltatások microsoftos társviszony-létesítés, fontos, hogy a NAT IP-készlet megfelelőméretezése. Az ExpressRoute-alapú NAT-ról a [Microsoft társviszony-létesítési nat-követelményei](expressroute-nat.md#nat-requirements-for-microsoft-peering)című témakörben talál további információt. Amikor az Azure ExpressRoute(Microsoft-társviszony-létesítés) segítségével csatlakozik a Microsofthoz, több microsoftos hivatkozással rendelkezik. Az egyik kapcsolat a meglévő internetkapcsolat, a másik pedig az ExpressRoute-on keresztüli kapcsolat. Előfordulhat, hogy a forgalom egy része az interneten keresztül jut el a Microsofthoz, de az ExpressRoute-on át tér vissza, vagy fordítva.

![Kétirányú kapcsolat](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.

A Microsoft társviszony-létesítéskonfigurálása előtt tekintse meg a [több hálózati útvonallal rendelkező aszimmetrikus útválasztást.](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)

* Ha nyilvános társviszony-létesítést használ, és jelenleg rendelkezik IP-hálózati szabályokkal az [Azure Storage](../storage/common/storage-network-security.md) vagy az [Azure SQL-adatbázis](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)eléréséhez használt nyilvános IP-címekhez, győződjön meg arról, hogy a Microsoft-társviszony-létesítéssel konfigurált NAT IP-készlet szerepel az Azure storage-fiók vagy az Azure SQL-fiók nyilvános IP-címeinek listájában.<br>
* Annak érdekében, hogy a Microsoft-társviszony-létesítés állásidő nélkül, használja a lépéseket ebben a cikkben a jelen jelen iktatási sorrendben.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Microsoft-társviszony-létesítés létrehozása

Ha a Microsoft társviszony-létesítése még nem jött létre, az alábbi cikkek bármelyikével hozzon létre Microsoft-társviszony-létesítést. Ha a kapcsolódási szolgáltató felügyelt 3.

Ha a 3.

* Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet rendeli az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.<br>
* Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet rendeli az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.<br>
* Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Mind az elsődleges, mind a másodlagos hivatkozásokhoz ugyanazt a VLAN-azonosítót kell használnia.<br>
* Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.<br>
* Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagok készletét tervezi elküldeni, vesszővel tagolt listát küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.<br>
* Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.

* **Nem kötelező** – Ügyfél ASN: Ha olyan előtagokat hirdet, amelyek nincsenek regisztrálva a társviszony-létesítési AS-számhoz, megadhatja azt az AS-számot, amelyre regisztrálva vannak.<br>
* **Nem kötelező** – MD5-kivonat, ha úgy dönt, hogy használ egyet.

A Microsoft társviszony-létesítésének engedélyezésére vonatkozó részletes utasítások az alábbi cikkekben találhatók:

* [Microsoft-társviszony-létesítés létrehozása az Azure Portal használatával](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Microsoft-társviszony-létesítés létrehozása az Azure Powershell használatával](expressroute-howto-routing-arm.md#msft)<br>
* [Microsoft-társviszony-létesítés létrehozása az Azure CLI használatával](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. A Microsoft társviszony-létesítés engedélyezésének ellenőrzése

Ellenőrizze, hogy a Microsoft társviszony-létesítés engedélyezve van-e, és hogy a hirdetett nyilvános előtagok konfigurált állapotban vannak-e.

* [Azure-portál](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. Útvonalszűrő konfigurálása és csatlakoztatása az áramkörhöz

Alapértelmezés szerint az új Microsoft társviszony-létesítés nem hirdet előtagokat, amíg egy útvonalszűrő nincs csatlakoztatva a kapcsolatcsoporthoz. Amikor létrehoz egy útvonalszűrő szabályt, megadhatja az Azure-régiók azon szolgáltatási közösségeinek listáját, amelyeket az Azure PaaS-szolgáltatásokhoz kíván felhasználni. Ez rugalmasságot biztosít az útvonalak szűréséhez a követelmény nek megfelelően, ahogy az a következő képernyőképen látható:

![Nyilvános társviszony-létesítés egyesítése](./media/how-to-move-peering/routefilter.jpg)

Az útvonalszűrőkkonfigurálás az alábbi cikkek valamelyikével adható meg:

* [Útvonalszűrők konfigurálása a Microsoft társviszony-létesítéséhez az Azure Portal használatával](how-to-routefilter-portal.md)<br>
* [Útvonalszűrők konfigurálása a Microsoft társviszony-létesítéséhez az Azure PowerShell használatával](how-to-routefilter-powershell.md)<br>
* [Útvonalszűrők konfigurálása a Microsoft társviszony-létesítéséhez az Azure CLI használatával](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. Törölje a nyilvános társviszony-létesítést

Miután meggyőződött arról, hogy a Microsoft társviszony-létesítés konfigurálva van, és a kívánt előtagok megfelelően vannak meghirdetve a Microsoft társviszony-létesítéskor, törölheti a nyilvános társviszony-létesítést. A nyilvános társviszony-létesítés törléséhez használja az alábbi cikkek egyikét:

* [Az Azure nyilvános társviszony-létesítése törlése az Azure PowerShell használatával](about-public-peering.md#powershell)
* [Az Azure nyilvános társviszony-létesítése törlése a CLI használatával](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. Társviszony-létesítések megtekintése
  
Az Azure Portalon megtekintheti az összes ExpressRoute-kapcsolatés társviszony-létesítés listáját. További információt a [Microsoft társviszony-létesítési részleteinek megtekintése című témakörben talál.](expressroute-howto-routing-portal-resource-manager.md#getmsft)

## <a name="next-steps"></a>További lépések

Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
