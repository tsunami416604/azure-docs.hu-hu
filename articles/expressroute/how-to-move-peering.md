---
title: Helyezze át a nyilvános társviszony-létesítés a Microsoft társviszony-létesítés – Azure ExpressRoute |} A Microsoft Docs
description: Ez a cikk bemutatja, a lépések végrehajtásával helyezhetők át, a nyilvános társviszony-létesítés a Microsoft társviszony-létesítést az expressroute-on.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592143"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Helyezze át a nyilvános társviszony-létesítés Microsoft társviszony-létesítésre

Ez a cikk segít helyezze át a nyilvános társviszony-létesítés konfigurációját a Microsoft társviszony-létesítés üzemkimaradás nélkül. Az ExpressRoute támogatja a Microsoft-társviszony-létesítés útvonalszűrőkkel való használatát az Azure PaaS-szolgáltatások, például az Azure Storage és az Azure SQL Database esetén. A Microsoft PaaS- és SaaS-szolgáltatásokhoz való hozzáféréshez most csak egy útválasztási tartományra van szüksége. Útvonalszűrők segítségével szelektíven meghirdetheti a PaaS-szolgáltatások előtagjait a felhasználni kívánt Azure-régiókhoz.

1 NAT IP-címmel társított minden BGP-munkamenet az Azure nyilvános társviszony-létesítés rendelkezik. Microsoft társviszony-létesítés lehetővé teszi a saját NAT-hozzárendelések konfigurálása, valamint a szelektív előtag hirdetmények útvonalszűrők használni. Nyilvános társviszony-létesítés egy egyirányú szolgáltatás, mely kapcsolat használatával mindig lehet kezdeményezni a WAN a Microsoft Azure-szolgáltatások. Microsoft Azure-szolgáltatások nem lesz képes kezdeményeznek kapcsolatokat a hálózatban az útválasztási tartomány segítségével.

Nyilvános társviszony-létesítés engedélyezését követően csatlakozhat Azure-szolgáltatásokhoz. Azt teszi lehetővé külön-külön válassza ki a szolgáltatásokat, amelyhez a Microsoft felé haladó útvonalak meghirdetéséhez. Pedig a Microsoft társviszony-létesítés egy kétirányú kapcsolat, a kapcsolat a Microsoft Azure-szolgáltatást és a WAN kezdeményezhetők. Útválasztási tartományok és a társviszony-létesítés kapcsolatos további információkért lásd: [ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).

## <a name="before"></a>Előkészületek

Ha csatlakozni szeretne a Microsoft társviszony-létesítés, kell beállítása és kezelése helyezkedik el. A kapcsolatszolgáltató előfordulhat, hogy beállítása és kezelése a NAT egy felügyelt szolgáltatásként. Ha azt tervezi, az Azure PaaS és Microsoft társviszony-létesítés Azure SaaS-szolgáltatások eléréséhez, fontos a megfelelő méretezés a NAT IP-készlet. Az ExpressRoute NAT kapcsolatos további információkért lásd: a [Microsoft társviszony-létesítés NAT-követelményei](expressroute-nat.md#nat-requirements-for-microsoft-peering). Ha a Microsoft Azure ExpressRoute(Microsoft peering) keresztül csatlakozik, a Microsoft több kapcsolat van. Az egyik kapcsolat a meglévő internetkapcsolat, a másik pedig az ExpressRoute-on keresztüli kapcsolat. Előfordulhat, hogy a forgalom egy része az interneten keresztül jut el a Microsofthoz, de az ExpressRoute-on át tér vissza, vagy fordítva.

![Kétirányú kapcsolat](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.

Tekintse meg [az aszimmetrikus útválasztás több hálózati elérési úttal](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) a figyelmeztetések az aszimmetrikus útválasztás konfigurálása a Microsoft társviszony-létesítés előtt.

* Ha a nyilvános társviszony-létesítés használata jelenleg rendelkezik az IP-hálózati szabályok használt nyilvános IP-címek hozzáférés [Azure Storage](../storage/common/storage-network-security.md) vagy [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), győződjön meg róla, hogy a NAT IP-címkészlet konfigurálva kell a Microsoft társviszony-létesítés tartalmazza az Azure storage-fiók vagy az Azure SQL-fiók nyilvános IP-címek listáját.<br>
* Annak érdekében, hogy a Microsoft társviszony-létesítés üzemkimaradás nélkül át, kövesse a lépéseket ebben a sorrendben jelenjenek meg ezek a cikkben.

## <a name="create"></a>1. A Microsoft társviszony-létesítés

Microsoft társviszony-létesítés nem lett létrehozva, ha bármely, a következő cikkek segítségével a Microsoft társviszony-létesítés. Ha felügyelt kapcsolat szolgáltató ajánlatait igényei 3 services layer, megkérheti a kapcsolatszolgáltató engedélyezése a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.

Ha a 3. rétegbeli Ön kezeli a következő információkat szükség, mielőtt továbblépne:

* Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.<br>
* Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.<br>
* Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos hivatkozásokat kell használnia az ugyanazon VLAN-azonosítót.<br>
* Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.<br>
* Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha azt tervezi, az előtagok megadni, küldhet egy vesszővel tagolt lista. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.<br>
* Útválasztási jegyzék neve: Megadhatja az RIR / IRR jegyzék nevét, amely az AS-szám és az előtagok regisztrálva vannak.

* **Nem kötelező** -ügyfél ASN-ként: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-szám, megadhatja az AS-számot, amelyre regisztrálva vannak.<br>
* **Nem kötelező** – Ha úgy dönt, hogy válasszon egy MD5-kivonat.

Engedélyezze a Microsoft társviszony-létesítést részletes utasításokat a következő cikkekben található:

* [Hozzon létre a Microsoft társviszony-létesítés Azure portal használatával](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Hozzon létre a Microsoft társviszony-létesítés Azure Powershell-lel](expressroute-howto-routing-arm.md#msft)<br>
* [Hozzon létre a Microsoft társviszony-létesítés Azure CLI használatával](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Ellenőrizze a Microsoft társviszony-létesítés engedélyezve van

Győződjön meg arról, hogy a Microsoft társviszony-létesítés engedélyezve van, és a meghirdetett nyilvános előtagok konfigurált állapotban vannak.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurálása és a egy útvonalszűrőhöz csatlakoztatása kapcsolatcsoporthoz

Alapértelmezés szerint új Microsoft társviszony-létesítés nem hirdetünk bármely előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport. Egy útvonalszűrő-szabály létrehozásakor megadhatja az Azure-régiók, amelyeket szeretne felhasználni Azure PaaS-szolgáltatások szolgáltatási Közösségek listája. Ezáltal rugalmasan, a szűrési az útvonalak a követelmény megfelelően az alábbi képernyőképen látható módon:

![Nyilvános társviszony-létesítés egyesítése](./media/how-to-move-peering/routefilter.jpg)

Adja meg a következő cikkek bármelyikével útvonalszűrők:

* [A Microsoft társviszony-létesítést az Azure portal használatával útvonalszűrőinek konfigurálása](how-to-routefilter-portal.md)<br>
* [Microsoft társviszony-létesítés Azure PowerShell-lel útvonalszűrőinek konfigurálása](how-to-routefilter-powershell.md)<br>
* [Microsoft társviszony-létesítés Azure CLI-vel útvonalszűrőinek konfigurálása](how-to-routefilter-cli.md)

## <a name="delete"></a>4. A nyilvános társviszony-létesítés törlése

Miután ellenőrizte, hogy a Microsoft társviszony-létesítésre van konfigurálva, és az előtagok fel kívánja használni a Microsoft társviszony-létesítés megfelelően van-e hirdetve, majd törölheti a nyilvános társviszony-létesítés. A nyilvános társviszony-létesítés törléséhez használja a következő cikkek valamelyikét:

* [Az Azure nyilvános társviszony-létesítés törlése az Azure portal használatával](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Az Azure nyilvános társviszony-létesítés törlése az Azure PowerShell-lel](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Az Azure nyilvános társviszony-létesítés törlése parancssori felület használatával](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Társviszony-létesítéseket megtekintése
  
Minden ExpressRoute-Kapcsolatcsoportok és a társviszony az Azure Portalon listáját láthatja. További információkért lásd: [megtekintése a Microsoft társviszony-létesítés részleteinek](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
