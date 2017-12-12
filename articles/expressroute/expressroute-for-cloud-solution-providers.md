---
title: "Azure ExpressRoute felhőszolgáltatók számára | Microsoft Docs"
description: "A cikk olyan felhőszolgáltatók számára kínál információkat, akik be kívánják építeni az Azure -szolgáltatásokat és az ExpressRoute-ot az ajánlataikba."
documentationcenter: na
services: expressroute
author: richcar
manager: carmonm
editor: 
ms.assetid: f6c5f8ee-40ba-41a1-ae31-67669ca419a6
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: richcar
ms.openlocfilehash: dfae23638e31242dc795922fd62d1abb02579480
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute felhőszolgáltatók (CSP) számára
A Microsoft hatalmas léptékű szolgáltatásokat kínál a hagyományos viszonteladók és forgalmazók (CSP) számára, hogy gyorsan láthassák el ügyfeleiket új szolgáltatásokat és megoldásokat anélkül, hogy be kellene fektetniük az új szolgáltatások fejlesztésébe. Ahhoz, hogy lehetővé tegye a felhőszolgáltatók (CSP) számára az új szolgáltatások közvetlen felügyeletét, a Microsoft olyan programokat és API-kat biztosít, amelyek segítségével a CSP-k ügyfeleik nevében felügyelhetik a Microsoft Azure-erőforrásokat. Ezeknek az erőforrásoknak az egyike az ExpressRoute. Az ExpressRoute lehetővé teszi, hogy a CSP meglévő ügyfélerőforrásait csatlakoztassa az Azure-szolgáltatásokhoz. Az ExpressRoute egy nagy sebességű privát kommunikációs kapcsolat az Azure-ban lévő szolgáltatásokhoz. 

Az ExpressRoute két magas rendelkezésre állású kapcsolatcsoportból áll, amelyek egyetlen ügyfél előfizetéséhez/előfizetéseihez kapcsolódnak, és nem oszthatóak meg több ügyfél által. A két kapcsolatcsoport végét külön útválasztókra kell csatlakoztatni a magas rendelkezésre állás biztosítása érdekében.

> [!NOTE]
> Az ExpressRoute sávszélesség- és kapcsolatkorlátokkal rendelkezik, ami azt jelenti, hogy a nagyméretű/összetett megvalósításokhoz több ExpressRoute-kapcsolatcsoport szükséges ügyfelenként.
> 
> 

A Microsoft Azure egyre nagyobb számban kínál szolgáltatásokat, amelyeket Ön biztosíthat az ügyfeleinek. Az ExpressRoute segít Önnek és az ügyfeleinek kiaknázni ezen szolgáltatások előnyeit azáltal, hogy nagy sebességű, alacsony késleltetésű elérést biztosít a Microsoft Azure környezethez.

## <a name="microsoft-azure-management"></a>Microsoft Azure-kezelés
A Microsoft a CSP-k számára API-kat biztosít az Azure-ügyfelek előfizetéseinek kezeléséhez a saját szolgáltatáskezelési rendszereivel való programozott integráció révén. A támogatott kezelési képességek [itt](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx) találhatók.

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure erőforrás-kezelés
Az ügyfelével kötött szerződése határozza meg az előfizetés kezelésének módját. A CSP közvetlenül kezelheti az erőforrások létrehozását és karbantartását, vagy az ügyfél megtarthatja a Microsoft Azure-előfizetés feletti felügyeletet, és maga hozhat létre igény szerinti Azure-erőforrásokat. Ha Microsoft Azure-előfizetése keretein belül az ügyfél maga kezeli az erőforrások létrehozását, ehhez a két következő modell valamelyikét használja majd: a „*Szolgáltatón keresztüli csatlakozás*” vagy a „*Szolgáltatóhoz való csatlakozás*” modellt. A modellek leírását a következő szakaszok tartalmazzák.  

### <a name="connect-through-model"></a>Szolgáltatón keresztüli csatlakozás modell
![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

A szolgáltatón keresztüli csatlakozás modellben a CSP közvetlen kapcsolatot hoz létre az Ön adatközpontja és az ügyfél Azure-előfizetése között. A közvetlen kapcsolat ExpressRoute használatával jön létre, és hálózatát összekapcsolja az Azure-ral. Az ügyfél ezután az Ön hálózatához csatlakozik. Ebben a forgatókönyvben az ügyfélnek a CSP hálózatán keresztül kell kapcsolódnia az Azure-szolgáltatásokhoz. 

Ha az ügyfele más Azure-előfizetésekkel is rendelkezik, amelyeket nem Ön kezel, az ügyfél a nyilvános interneten vagy saját privát kapcsolaton keresztül csatlakozik a nem a CSP-n keresztül kiosztott szolgáltatásokhoz. 

Azure-szolgáltatásokat CSP-k esetén a rendszer feltételezi, hogy a CSP már rendelkezik egy korábban létrehozott ügyfélidentitás-tárolóval, amelyet utána replikál az Azure Active Directoryban az ügyfelek CSP-előfizetésének AOBO használatával történő felügyeletéhez. A forgatókönyv fő esetei, ha egy adott partner vagy szolgáltató már jól működő kapcsolattal rendelkezik az ügyféllel, az ügyfél jelenleg is használja a szolgáltató által nyújtott szolgáltatásokat, vagy ha a partner a szolgáltató által futtatott és az Azure rendszeren futtatott szolgáltatások kombinációját kívánja nyújtani, hogy rugalmasabb megoldásokat biztosíthasson, és megfelelhessen az ügyfelek olyan igényeinek, amelyeket a CSP egyedül nem tud kielégíteni. A modell az alábbi **ábrán** látható.

![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Szolgáltatóhoz való csatlakozás
![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/connect-to.png)

A szolgáltatóhoz való csatlakozás modellben a szolgáltató közvetlen kapcsolatot hoz létre az ügyfél adatközpontja és a CSP által létrehozott Azure-előfizetés között az ExpressRoute használatával az ügyfél hálózatán (ügyfélhálózat) keresztül.

> [!NOTE]
> Az ExpressRoute-hoz az ügyfélnek létre kell hoznia és fenn kell tartania egy ExpressRoute-kapcsolatcsoportot.  
> 
> 

Ebben a kapcsolódási forgatókönyvben az ügyfélnek egy ügyfélhálózaton keresztül közvetlenül kell csatlakoznia a CSP által felügyelt Azure-előfizetés eléréséhez egy teljes mértékben vagy részben az ügyfél által létrehozott, tulajdonolt és felügyelt közvetlen hálózati kapcsolat használatával. Ezen ügyfelek esetén a rendszer feltételezi, hogy a szolgáltató jelenleg nem rendelkezik kialakított ügyfélidentitás-tárolóval, és segít majd az ügyfélnek replikálni az ügyfél aktuális identitástárolóját az Azure Active Directoryba az előfizetés AOBO használatával történő felügyeletéhez. A forgatókönyv fő esetei, ha egy adott partner vagy szolgáltató már jól működő kapcsolattal rendelkezik az ügyféllel, az ügyfél jelenleg is használja a szolgáltató által nyújtott szolgáltatásokat, vagy ha a partner kizárólag az Azure-on futtatott megoldásokon alapuló olyan szolgáltatásokat kíván nyújtani, amelyekhez nincs szükség meglévő szolgáltatói adatközpontra vagy infrastruktúrára.

![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Hogy a két lehetőség közül melyiket érdemes választani, azt az ügyfél igényei és az Ön az Azure-szolgáltatások nyújtásával kapcsolatos aktuális igényei határozzák meg. A modellekkel és a kapcsolódó szerepköralapú hozzáférésvezérlés-, hálózat- és identitástervezési mintákkal kapcsolatos részletek az alábbi hivatkozásokon tekinthetők meg:

* **Szerepköralapú hozzáférés-vezérlés (RBAC)** – Az RBAC az Azure Active Directoryn alapul.  Az Azure RBAC-ról [itt](../active-directory/role-based-access-control-configure.md) talál további információt.
* **Hálózat** – A különféle hálózati témaköröket fedi le a Microsoft Azure-ban.
* **Azure Active Directory (Azure AD)** – Az Azure AD biztosítja az identitáskezelést a Microsoft Azure és a külső szolgáltatók által biztosított SaaS-alkalmazások számára. Az Azure AD-vel kapcsolatos további információkat lásd [itt](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Hálózati sebességek
Az ExpressRoute az 50 Mb/s és 10 Gb/s közti hálózati sebességeket támogatja. Az ügyfelek így az egyedi környezetük által igényelt hálózati sávszélességet vásárolhatják meg.

> [!NOTE]
> A hálózati sávszélesség igény szerint növelhető a kommunikáció megszakadása nélkül, azonban a csökkentéséhez le kell bontani a kapcsolatcsoportot, és újra kell építeni az alacsonyabb hálózati sebességen.  
> 
> 

Az ExpressRoute több vNet csatlakoztatását is támogatja egyetlen ExpressRoute-kapcsolatcsoporthoz a nagyobb sebességű kapcsolatok jobb kihasználása érdekében. Egyetlen ExpressRoute-kapcsolatcsoport egy adott ügyfél több Azure-előfizetése között is megosztható.

## <a name="configuring-expressroute"></a>Az ExpressRoute konfigurálása
Az ExpressRoute három típusú forgalom ([útválasztási tartomány](#ExpressRoute-routing-domains)) támogatására konfigurálható egyetlen ExpressRoute-kapcsolatcsoportban. Ezek a forgalomtípusok a Microsoft társviszony-létesítés, az Azure nyilvános társviszony-létesítés és a privát társviszony-létesítés. Egyetlen ExpressRoute-kapcsolatcsoportban egy vagy több forgalomtípust is továbbíthat, vagy használhat több ExpressRoute-kapcsolatcsoportot is, az ExpressRoute-kapcsolatcsoport méretétől és az ügyfél által igényelt elkülönítéstől függően. Előfordulhat, hogy az ügyfél biztonsági irányelvei, tiltják hogy a nyilvános forgalom és a privát forgalom ugyanazon a körön folyjanak.

### <a name="connect-through-model"></a>Szolgáltatón keresztüli csatlakozás modell
A szolgáltatón keresztüli csatlakozási konfigurációban Ön a felelős az összes hálózati eszközért az ügyfelek adatközponti erőforrásainak az Azure-ban futtatott előfizetésekhez való csatlakoztatásához. Mindegyik ügyfélnek, aki Azure-funkciókat kíván használni, saját ExpressRoute-kapcsolatra van szüksége, amelyet Ön kezel majd. Ön ugyanazon módon igényelhet ExpressRoute-kapcsolatcsoportot, ahogyan az ügyfél. Ezt követően az [ExpressRoute workflows for circuit provisioning and circuit states](expressroute-workflows.md) (Az ExpressRoute kapcsolatcsoport-kiépítési munkafolyamatai és a kapcsolatcsoportok állapotai) című cikkben ismertetett lépéseket kell követnie. Ezután konfigurálnia kell a Border Gateway Protocol- (BGP-) útvonalakat a helyszíni hálózat és az Azure vNet közötti forgalom vezérléséhez.

### <a name="connect-to-model"></a>Szolgáltatóhoz való csatlakozás
Szolgáltatóhoz való csatlakozási konfigurációban az ügyfél már rendelkezik meglévő Azure-kapcsolattal, vagy kezdeményezni fog egy kapcsolatot internetszolgáltatója felé, amely ExpressRoute-összeköttetést biztosít az ügyfél saját adatközpontjából közvetlenül az Azure-ba az Ön adatközpontja helyett. A kiépítési folyamat megkezdéséhez az ügyfélnek ugyanúgy a Szolgáltatón keresztüli csatlakozás modellnél fentebb ismertetett lépéseket kell végrehajtania. A kapcsolatcsoport létrehozását követően az ügyfélnek konfigurálnia kell a helyszíni útválasztókat, hogy elérhesse az Ön hálózatát és az Azure vNet hálózatokat is.

Támogatást nyújthat a kapcsolat beállításában és az útvonalak konfigurálásában, hogy az adatközpont(ok)ban található erőforrások kommunikálhassanak az ügyfél erőforrásaival az Ön adatközpontjában, vagy az Azure-ban futtatott erőforrásokkal.

## <a name="expressroute-routing-domains"></a>ExpressRoute útválasztási tartományok
Az ExpressRoute három útválasztási tartományt kínál: nyilvános, privát és Microsoft társviszony-létesítés. Az útválasztási tartományok mindegyike egyforma útválasztókkal van konfigurálva aktív-aktív konfigurációban magas rendelkezésre álláshoz. Az ExpressRoute útválasztási tartományokkal kapcsolatos további részleteket lásd [itt](expressroute-circuit-peerings.md).

Az egyéni útvonalszűrőket meghatározhatja úgy is, hogy kizárólag a kívánt vagy szükséges útvonal(ak)at engedélyezzék. A módosítások végrehajtásával kapcsolatos további információkért tekintse meg a [Create and modify routing for an ExpressRoute circuit using PowerShell](expressroute-howto-routing-classic.md) (ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása a PowerShell használatával) című cikket, amely az útválasztási szűrőkkel kapcsolatban is biztosít részletes információkat.

> [!NOTE]
> A Microsoft és a társviszony-létesítéshez a kapcsolatoknak egy, az ügyfél vagy a CSP által birtokolt nyilvános IP-címen keresztül kell megvalósulniuk, és az összes meghatározott szabálynak meg kell felelniük. További információkért lásd az [ExpressRoute-előfeltételek](expressroute-prerequisites.md) lapot.  
> 
> 

## <a name="routing"></a>Útválasztás
Az ExpressRoute az Azure Virtual Network Gatewayen keresztül csatlakozik az Azure-hálózatokhoz. A hálózati átjárók biztosítják az útvonalválasztást az Azure virtuális hálózatok számára.

Az Azure Virtual Networks létrehozásával egy alapértelmezett útválasztási tábla is létrejön a vNet számára a vNet alhálózataira irányuló, illetve onnan érkező forgalom irányításához. Ha az alapértelmezett útválasztási tábla nem elegendő a megoldáshoz, egyéni útvonalak is létrehozhatók a kimenő forgalom egyedi készülékekre való átirányításához, vagy az egyes alhálózatokra vagy külső hálózatokra irányuló útvonalak blokkolásához.

### <a name="default-routing"></a>Alapértelmezett útválasztás
Az alapértelmezett útválasztási tábla az alábbi útvonalakat tartalmazza:

* Alhálózaton belüli útválasztás
* Alhálózatok közti útvonalak a virtuális hálózaton belül
* Az internet felé
* Virtuális hálózatok közti útvonalak a VPN Gateway használatával
* Virtuális hálózat és helyszíni hálózat közötti útvonal VPN- vagy ExpressRoute-átjáró használatával

![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Felhasználó által meghatározott útválasztás (UDR)
A felhasználó által megadott útvonalak segítségével felügyelhető a hozzárendelt alhálózatról a többi alhálózatra irányuló kimenő forgalom a virtuális hálózatban vagy a többi előre definiált átjáró (ExpressRoute, internet vagy VPN) valamelyikén keresztül. A rendszer alapértelmezett útválasztási táblája lecserélhető a felhasználó által meghatározott útválasztási táblára, amely a benne lévő útvonalakat egyéni útvonalakra cseréli. A felhasználó által meghatározott útválasztás használatával az ügyfelek egyedi útvonalakat hozhatnak létre készülékekhez, például tűzfalakhoz vagy behatolás-észlelő készülékekhez, vagy blokkolhatják a hozzáférést megadott alhálózatokhoz azon alhálózatról, amelyen a felhasználó által megadott útvonal található. A felhasználó által megadott útvonalak áttekintését lásd [itt](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Biztonság
A használt modelltől – Szolgáltatóhoz való csatlakozás vagy Szolgáltatón keresztüli csatlakozás – függően az ügyfél a biztonsági házirendeket a vNetben határozza meg, vagy megadja a CSP-nek a biztonsági házirendekre vonatkozó követelményeket azok meghatározásához a vNetekben. A következő kritériumok határozhatók meg:

1. **Ügyfél elkülönítése** – Az Azure platform lehetőséget biztosít az ügyfél elkülönítésére az ügyfél-azonosító és a vNet-adatok egy biztonságos adatbázisban való eltárolásával, amelyeket arra használ, hogy az egyes ügyfelek forgalmát egy GRE-alagútba bújtassa.
2. A **hálózati biztonsági csoport (NSG)** szabályai segítségével meghatározható a vNetek alhálózatainak engedélyezett be- és kimenő forgalma az Azure-ban. Alapértelmezés szerint az NSG blokkolási szabályok használatával blokkolja az internetről a vNet-re irányuló forgalmat, és engedélyezési szabályok használatával engedélyezi a vNeten belüli forgalmat. A hálózati biztonsági csoportokkal kapcsolatos további információkat [itt](https://azure.microsoft.com/blog/network-security-groups/) tekintheti meg.
3. **Kényszerített bújtatás** – Ezzel a lehetőséggel az Azure-ból származó internetes forgalom átirányítható az ExpressRoute-kapcsolaton keresztül a helyszíni adatközpontba. A kényszerített bújtatással kapcsolatos további információkat [itt](expressroute-routing.md#advertising-default-routes) tekintheti meg.  
4. **Titkosítás** – Annak ellenére, hogy az ExpressRoute-kapcsolatcsoportok adott ügyfelekre vonatkoznak, fennáll a lehetősége, hogy a hálózatszolgáltató rendszerét feltörik, és így a behatoló megvizsgálhatja a csomagforgalmat. Az ilyen helyzetek kezelése érdekében az ügyfél vagy a CSP a kapcsolaton áthaladó forgalom titkosításához meghatározhat IPSec-bújtatás módú házirendeket a helyszíni erőforrások és az Azure-erőforrások között áramló teljes forgalomra vonatkozóan (lásd az 1-es ügyfélre vonatkozó választható bújtatás módú IPSec-házirendeket a fenti 5. ábrán: ExpressRoute-biztonság). A második lehetőség egy tűzfalkészülék használata az ExpressRoute-kapcsolatcsoport mindegyik végpontján. Ehhez további külső gyártótól származó tűzfal VM-ek/-készülékek telepítése szükséges mindkét végponton az ExpressRoute-kapcsolatcsoporton áthaladó forgalom titkosításához.

![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Következő lépések
A felhőszolgáltatói szolgáltatás segítségével anélkül növelheti az ügyfelei számára nyújtott értéket, hogy költséges infrastruktúra- és képességberuházásokat kellene eszközölnie, és így megtarthatja pozícióját, mint első számú szolgáltató a kiszervezések terén. A Microsoft Azure-ral való zökkenőmentes integráció a CSP API-n keresztül valósítható meg, amelynek segítségével a Microsoft Azure felügyelete integrálható meglévő felügyeleti keretrendszerekbe.  

További információt az alábbi hivatkozásokat követve tekinthet meg:

[Azure a felhőszolgáltatói programban](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Felkészülés a felhőszolgáltatóként kínált tranzakciók végrehajtására](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[A Microsoft felhőszolgáltatói erőforrásai](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).
