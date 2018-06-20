---
title: Az Azure integrációs szolgáltatások referencia-architektúrában
description: Bemutatja, hogyan valósítja meg a Logic Apps, az API Management, a Service Bus és a esemény rács egy vállalati integrációs mintát referencia-architektúrában
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232493"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Vállalati együttműködés a várólisták és események: hivatkozhat architektúrája

## <a name="overview"></a>Áttekintés

A referencia-architektúrában bevált gyakorlat az integráció alkalmazás által használt Azure integrációs szolgáltatások készletként jeleníti meg. Ez az architektúra ezek alapján HTTP API-k, a munkafolyamat és a vezénylési igénylő számos különböző alkalmazás minták lehetnek.

*Sok lehetséges alkalmazások integrációs technológia, egyszerű pont közötti pont-alkalmazás a teljes vállalati service Bus vannak. Az architektúra adatsorozat meghatározza a újrafelhasználható összetevő kijelzők bármely integrációs alkalmazások készítéséhez – a fejlesztők gondolja át az alkalmazásaikat és infrastruktúrájukat a rendszer szükséges összetevőket.*

![Architektúra ábrája - várólisták & események vállalati integrációja](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architektúra

Ez az architektúra látható egy épít [egyszerű vállalati integrációs](logic-apps-architectures-simple-enterprise-integration.md). A következő összetevőket tartalmazza:

- Erőforráscsoport. Egy erőforráscsoportot az Azure-erőforrások logikai tárolója.
- Az Azure API Management. Az Azure API Management az egy teljes körűen felügyelt platform közzétételéhez, biztonságossá tétele és átalakítása HTTP API-k.
- Az Azure API Management fejlesztői portálján. A fejlesztői portálra visszatérve adjon hozzáférést a dokumentáció, Kódminták és tesztelheti az API-k olyan Azure API Management-példányokhoz tartalmaz.
- Az Azure Logic Apps. A Logic Apps szolgáltatás egy kiszolgáló nélküli platform vállalati munkafolyamat és integrációs készítéséhez.
- Összekötők. Összekötők Logic Apps a gyakran használt szolgáltatásokhoz használják. A Logic Apps már van másik összekötők több száz, de ezek is létrehozhat egyéni összekötő használatával.
- Az Azure Service Bus. A Service Bus biztonságos és megbízható üzenetküldést biztosít. Üzenetküldési deszerializálni egymástól alkalmazások be-és egyéb üzenet-alapú rendszerek integrálása használható.
- Az Azure Event rács. Esemény rács egy kiszolgáló nélküli platform közzétételéhez, és kézbesíti az eseményeket.
- IP-címet. Az Azure API Management szolgáltatás egy rögzített nyilvános IP-cím és tartománynév rendelkezik. A tartománynév azure-api.net, például a contoso.azure-api.net altartománya. A Logic Apps és a Service Bus is rendelkeznek egy nyilvános IP-címet; azonban ebben az architektúrában azt hozzáférés korlátozása hívásához használandó Logic apps végpontok csak az IP-címet a API kezelése (biztonság). A Service Bus hívásainak egy közös hozzáférésű jogosultságkódot által védett.
- Az Azure DNS. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. Használhat egy egyéni tartománynevet (például contoso.com) hozzon létre DNS-rekordokat, amelyek kapcsolódnak az egyéni tartománynevet az IP-címet. További információkért lásd: Configure Azure API Management egy egyéni tartománynevet.
- Azure Active Directory (Azure AD). A hitelesítéshez használja az Azure AD-t vagy egy másik identitásszolgáltatót. Az Azure AD hozzáférési API végpontokhoz történő hitelesítést biztosít (úgy, hogy a JSON Web Token az API Management ellenőrzése), és biztonságossá teheti az API Management fejlesztői portálján (Standard és Premium rétegek csak) elérésére.

Ez az architektúra rendelkezik néhány alapvető minták működését:

1. HTTP API-k segítségével az API Management fejlesztői portálján, amely lehetővé teszi a fejlesztők számára közzétett létező háttérrendszerek (vagy belső a szervezet, külső vagy mindkettő) ezen API-k hívásainak integrálja alkalmazások.
2. Összetett API-k használatával Logic Apps; készített hívások SAAS rendszereken futó, Azure-szolgáltatásokat és minden API-k közzé az API Management. A Logic Apps is közzé az API Management Developer portálon.
3. Alkalmazások szerezni az OAuth 2.0 biztonsági jogkivonat-szükséges az API-k használata az Azure Active Directory elérésekor.
4. Az Azure API Management érvényesíti a biztonsági jogkivonatot, és továbbítja a kérelmet a háttérkiszolgálón API vagy logikai alkalmazást.
5. Service Bus-üzenetsorok használata leválasztja az alkalmazást a tevékenység, és zökkenőmentes igényeiben jelentkező terhelés szolgálnak. Üzenetek hozzáadott Logic Apps, 3. fél alkalmazások, várólisták és (nem szabad) azzal, hogy a várólista közzétesz, egy HTTP API-t az API Management keresztül.
6. Üzenetek kerülnek a Service Bus-üzenetsorba, ha az esemény akkor következik be. A logikai alkalmazás által az esemény akkor váltódik ki, és feldolgozza az üzenetet.
7. Hasonlóképpen más Azure-szolgáltatásokhoz (Blob Storage tárolóban, az Event Hubs) is közzé eseményeket esemény rács. Ezek indítható el a Logic Apps kapnak az esemény, és hajtsa végre a szükséges műveleteket.

## <a name="recommendations"></a>Javaslatok

Az Ön követelményei eltérhetnek az itt leírt architektúrától. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="service-bus-tier"></a>A Service Bus réteg

Service Bus prémium szintű csomagot használja, ha ez az esemény rács értesítések jelenleg támogatja (a támogatási összes rétegek között várt). Lásd: [Service Bus árképzési](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Esemény rács díjszabása

Esemény rács működik egy kiszolgáló nélküli modell használatával – számlázási alapján van kiszámítva műveletek (esemény végrehajtása) száma. Lásd: [esemény rács árképzési](https://azure.microsoft.com/pricing/details/event-grid/) további információt. Jelenleg nincs esemény rács réteg szempontjai.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>PeekLock használja, ha a Service Bus üzenetek felhasználása

A Service Bus üzenetek lefoglalhatja a Logic Apps létrehozásakor [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) a logikai alkalmazásban az üzenetek csoport elérésére. A logikai alkalmazás végezhet el befejezése vagy megszakítása előtt minden üzenetet ellenőrzésének lépéseit. Ez a megközelítés üzenet véletlen adatvesztést véd.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Több objektum keresése, amikor egy esemény rács eseményindító következik be.

Rács eseményindítók kiváltó egyszerűen azt jelenti, hogy "legalább 1 ezeket a műveleteket végrehajtó". Amikor esemény rács váltja ki a logikai alkalmazás, egy üzenet jelenik meg a Service Bus-üzenetsorba, a logikai alkalmazást kell mindig tegyük fel például előfordulhat, hogy egy vagy több üzenetek feldolgozásához használható.

### <a name="region"></a>Régió

Használhatók a hálózati késés csökkentése érdekében érdemes az API Management, Logic Apps és a Service Bus ugyanabban a régióban. Általában érdemes a felhasználókhoz legközelebbi régiót választani.

Az erőforráscsoport is rendelkezik egy régiót, amely megadja a központi telepítési metaadatok tárolására, és ha a központi telepítési sablont a végrehajtása. Az erőforráscsoportot és a hozzá tartozó erőforrásokat helyezze ugyanabba a régióba. Ez javíthatja a rendelkezésre állást az üzembe helyezés során.

## <a name="scalability-considerations"></a>Méretezési szempontok

Az Azure Service Bus prémium csomagban is kibővíthető méretezhetőség eléréséhez üzenetkezelési egységek száma. Prémium szintű 1, 2 vagy 4 üzenetkezelési egység lehet. Az Azure Service Bus skálázás További útmutatóért lásd: [gyakorlati tanácsok a teljesítménnyel kapcsolatos fejlesztések használatával a Service Bus üzenetkezelés](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Írásának időpontjában a szolgáltatásiszint-szerződéssel (SLA) az Azure API Management 99,9 %-os Basic, Standard és Premium rétege számára. Prémium szint konfigurációhoz telepítés két vagy több régióban legalább egy egység van szolgáltatásiszint-szerződésben garantált 99,95 %-os.

Írásának időpontjában a szolgáltatásiszint-szerződés (SLA) Azure Logic Apps 99,9 %-os.

### <a name="disaster-recovery"></a>Vészhelyreállítás

Vegye fontolóra földrajzi-vész-helyreállítási a Service Bus prémium szintű engedélyezése feladatátvétel súlyos kimaradás esetén. Tudjon meg többet az [Azure Service Bus földrajzi-vész-helyreállítási](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Felügyeleti szempontok

Termelési környezetben, fejlesztési, külön erőforráscsoportokat létrehozni, és tesztelési környezetben. Ez megkönnyíti az üzemelő példányok felügyeletét, a tesztkörnyezetek törlését és a hozzáférési jogok kiosztását.
Amikor erőforrásokat rendel az erőforráscsoportokhoz, vegye figyelembe a következőket:

- Életciklus. Általában érdemes az azonos életciklusú erőforrásokat ugyanabba az erőforráscsoportba helyezni.
- Hozzáférés. Használhat [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) hozzáférési házirendek alkalmazása az erőforrások csoportba.
- Számlázás. Megtekintheti az erőforráscsoport összegzett költségeit.
- Tarifacsomag kiválasztása az API Management-használatát javasoljuk fejlesztői réteg fejlesztési és tesztkörnyezetek. Üzem előtti javasoljuk az éles környezet replika telepítése tesztek futtatása, és a költségek minimalizálása érdekében leáll.

További információkért lásd: [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) áttekintése.

### <a name="deployment"></a>Környezet

Azt javasoljuk, hogy használjon [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md) Azure API Management, a Logic Apps, az esemény rács és a Service Bus központi telepítéséhez. Sablonok megkönnyítik a PowerShell vagy az Azure parancssori felület (CLI) használatával központi telepítések automatizálásához.

Azt javasoljuk, hogy saját külön Resource Manager-sablonok az Azure API Management, minden egyes Logic Apps, esemény rács témakörök és Szolgáltatásbusz-névterek üzembe. Ez lehetővé teszi a forrás vezérlő rendszerekbe tárolja őket. Ezek a sablonok majd együtt vagy külön-külön a folyamatos integrációs/folyamatos (CI/CD) központi telepítési folyamat részeként telepíthető.

### <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

A Service Bus, például az API Management és a Logic Apps Azure Monitor segítségével követhetők nyomon. Az Azure a figyelő alapértelmezés szerint engedélyezve van, és a beállítást minden egyes szolgáltatáshoz más metrikák alapján tájékoztatást fogunk adni.

## <a name="security-considerations"></a>Biztonsági szempontok

A Service Bus használata egy közös hozzáférésű Jogosultságkód biztonságos. [SAS hitelesítési](../service-bus-messaging/service-bus-sas.md) lehetővé teszi egy felhasználó hozzáférést biztosít a Service Bus erőforrásainak adott jogosultságokkal. Tudjon meg többet az [Service Bus hitelesítési és engedélyezési](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Emellett a Service Bus-üzenetsorba kell érhető el (az új üzenetek közzététel engedélyezése) HTTP-végponttal, biztonságossá fronting a végpont az API Management kell használni. Ez majd védve legyenek a tanúsítványok vagy OAuth szükség szerint. Ennek legegyszerűbb módja az köztes egy kérelem-válasz HTTP-eseményindítóval van használata egy logikai alkalmazást.

Esemény rács esemény kézbesítési egy érvényesítési kód biztonságossá tételére. Ha LogicApps használatával az események felhasználásához, ez automatikusan történik. További részleteket tudhat meg [esemény rács biztonsági és hitelesítési](../event-grid/security-authentication.md).

## <a name="next-steps"></a>További lépések

* [Egyszerű vállalati integrációs](logic-apps-architectures-simple-enterprise-integration.md)
