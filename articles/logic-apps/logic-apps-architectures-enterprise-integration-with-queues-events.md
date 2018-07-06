---
title: Az Azure integrációs szolgáltatások referencia-architektúra
description: A Logic Apps, az API Management, Service Bus és Event Grid, egy vállalati integrációs minta megvalósítása megjelenítése a referencia-architektúra
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
ms.openlocfilehash: 7d14bbd587b5086348026c41f6551b10809b939a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859642"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Vállalati integráció a várólisták és események: Referenciaarchitektúra

## <a name="overview"></a>Áttekintés

Ez a referenciaarchitektúra egy adatintegrációs alkalmazás által használt Azure integrációs szolgáltatások bevált eljárásokat mutat be. Ez az architektúra a HTTP API-k, a munkafolyamat és a koordinálást igénylő számos különböző alkalmazásminták alapját szolgálhatnak.

*Számos lehetséges alkalmazás integrációs technológiával, az egyszerű pont a pont-alkalmazás, a teljes enterprise service bus is. Ez architektúra sorozat határoz meg egy általános adatintegrációs alkalmazás készítéséhez alkalmazandók újrafelhasználható összetevőit – architects gondolja át az alkalmazásaikat és infrastruktúrájukat megvalósítása kell adott összetevőit.*

![Architektúradiagram - üzenetsorok és események vállalati integráció](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architektúra

Ez az architektúra **épül** a [egyszerű vállalati integráció](logic-apps-architectures-simple-enterprise-integration.md) architektúra. A [egyszerű nagyvállalati architektúra javaslatok](logic-apps-architectures-simple-enterprise-integration.md#recommendations) is itt a alkalmazni, de a kimaradtak a [javaslatok](#recommendations) kivonatosan ebben a dokumentumban. Ez a következő összetevőket tartalmazza:

- Erőforráscsoport. Az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az Azure-erőforrások logikai tárolója.
- Az Azure API Management. [Az Azure API Management](https://docs.microsoft.com/azure/api-management/) közzététele, biztosítása és a HTTP API-k átalakítása egy teljes körűen felügyelt platform.
- Az Azure API Management fejlesztői portálon. Az Azure API Management minden példányának tartalmaz egy [fejlesztői portál](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), dokumentáció, Kódminták és tesztelhetik az API-KHOZ való hozzáférés megadását.
- Az Azure Logic Apps. [A Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) egy kiszolgáló nélküli platform nagyvállalati munkafolyamat és integrációs létrehozásához.
- Az összekötők. [Összekötők](https://docs.microsoft.com/azure/connectors/apis-list) Logic Apps által gyakran használt serviceshez való csatlakozáshoz használt. A Logic Apps már rendelkezik a különböző összekötők több száz, de ezek is hozható létre egy egyéni összekötőt.
- Az Azure Service Bus. [A Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) biztonságos és megbízható üzenetküldést biztosít. Üzenetküldési használható megszüntetéséhez összekapcsolhatja az egymástól az alkalmazások és más üzenetalapú rendszerekkel való integrációt segítik.
- Az Azure Event Griddel. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) közzétételéhez és alkalmazásesemények továbbítása egy kiszolgáló nélküli platform.
- IP-cím. Az Azure API Management szolgáltatás rendelkezik egy nyilvános [IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) és a egy tartomány nevét. A tartománynév altartománya API.NET-webhelyen, azure, például a contoso.azure-API.NET webhelyen. A Logic Apps és a Service Bus is rendelkezik egy nyilvános IP-cím; azonban ebben az architektúrában a Microsoft-hozzáférés korlátozása hívja fel a Logic apps végpontok csak az IP cím az API Management (az biztonság). A Service Bus-hívások közös hozzáférésű jogosultságkód védik.
- Az Azure DNS. [Az Azure DNS](https://docs.microsoft.com/azure/dns/) egy üzemeltetési szolgáltatás DNS-tartományok biztosítani a névfeloldást a Microsoft Azure infrastruktúráját használja. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. Egy egyéni tartománynevet (például contoso.com) használatához hozzon létre az egyéni tartománynév leképezése az IP-cím DNS-rekordjait. További információkért lásd: Configure egy egyéni tartománynevet az Azure API Management szolgáltatásban.
- Az Azure Active Directory (Azure AD). Használat [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) vagy egy másik identitásszolgáltatót a hitelesítéshez. Az Azure AD hitelesítési API-hozzáférés végpontokra biztosít (átadásával egy [JSON Web Token az API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) ellenőrzése), és biztosíthatja a hozzáférést az API Management fejlesztői portálon (Standard és prémium szinten csak).

Ez az architektúra rendelkezik a művelet néhány alapvető minták:

1. Meglévő háttérrendszer HTTP API-k közzététele az API Management fejlesztői portálon, lehetővé teszik a fejlesztőknek (vagy belső a szervezet, külső vagy mindkettő) való beépíthetik ezekkel az API-hívások.
2. Összetett API-k felhasználásával történik a Logic Apps; replikálásával segít a vállalatnak SAAS-rendszerekhez hívások, Azure-szolgáltatások és minden API-k közzététele és az API Management. A [Logic Apps is közzétett](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) az API Management fejlesztői portálon keresztül.
3. Alkalmazások [az OAuth 2.0 biztonsági jogkivonat beszerzése](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) szükséges API-khoz, az Azure Active Directory elérésekor.
4. Az Azure API Management [érvényesíti a biztonsági jogkivonatot](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad), és továbbítja a háttérrendszeri API-t vagy logikai alkalmazást.
5. Service Bus-üzenetsorok használható [szétválaszthatók](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) alkalmazás tevékenysége és [terhelés kiugrások smooth](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Üzenetek hozzáadta a Logic Apps, a 3. fél alkalmazások, várólisták és (nem szerepel) tegye közzé az üzenetsorba API Management szolgáltatáson keresztül HTTP API-ként.
6. Üzeneteket egy Service Bus-üzenetsorba van hozzáadva, amikor az esemény akkor következik be. Ez az esemény váltja ki egy logikai alkalmazást, és feldolgozza az üzenetet.
7. Ehhez hasonlóan más Azure-szolgáltatásokkal (pl. Blob Storage, az Eseményközpont) is közzé az eseményeket az Event Gridbe. Ezek indítása a Logic Apps, az eseményt, és utólagos műveleteket.

## <a name="recommendations"></a>Javaslatok

A konkrét követelmények eltérhetnek az itt leírt általános architektúra. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="service-bus-tier"></a>A Service Bus-szint

Ez az event grid értesítések jelenleg támogatja a Service Bus prémium szintű használja (a támogatási minden csomag várható). Lásd: [Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Event Grid-díjszabás

Event Grid működik egy kiszolgáló nélküli modellel – számlázási alapján lesz kiszámítva (esemény végrehajtása) műveletek száma. Lásd: [Event Grid díjszabási](https://azure.microsoft.com/pricing/details/event-grid/) további információt. Jelenleg nincsenek szint szempontok az Event Gridhez.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>PeekLock használja, ha a Service Bus-üzenetek felhasználása

A Service Bus-üzenetek lefoglalhatja a logikai alkalmazások létrehozásának, használjon [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) egy csoportot az üzenetek el a logikai alkalmazásban. A logikai alkalmazás is hajtsa végre a lépéseket befejezése vagy megszakítása előtt minden üzenetet érvényesítéséhez. Ez a megközelítés véletlen üzenet adatvesztés elleni védelmet biztosít.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Az Event Grid-trigger akkor aktiválódik, ha több objektum keresése

Event Grid eseményindítók egyszerűen azt jelenti, hogy "történt ezekről legalább 1" kiváltó. Ha például Event Grid egy logikai alkalmazást megjelenjenek a Service Bus-üzenetsorba egy üzenet aktivál, ha a logikai alkalmazás kell mindig feltételezze, hogy előfordulhat, hogy rendelkezésre állnak egy vagy több üzenetet.

### <a name="region"></a>Régió

Az API Management üzembe helyezése, a Logic Apps és a Service Bus ugyanabban a régióban a hálózati késés minimalizálása érdekében. Általában érdemes a felhasználókhoz legközelebbi régiót választani.

Az erőforráscsoport szintén van régiója, amely megadja a központi telepítési metaadatok tárolódnak, és ahol a központi telepítési sablont a hajtja végre. Az erőforráscsoportot és a hozzá tartozó erőforrásokat helyezze ugyanabba a régióba. Ez javíthatja a rendelkezésre állást az üzembe helyezés során.

## <a name="scalability-considerations"></a>Méretezési szempontok

Az Azure Service Bus prémium szintű csomag is kibővíthető magasabb, a skálázhatóság érdekében üzenetkezelési egységek száma. Prémium szintű 1, 2 vagy 4 üzenetkezelési egységgel rendelkezhetnek. Az Azure Service Bus skálázási További útmutatóért lásd: [ajánlott eljárások a teljesítmény Service Bus-üzenetkezelés használatával](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Írása idején a szolgáltatásiszint-szerződés (SLA) az Azure API Management az alapszintű, Standard és prémium szint esetében 99,9 %-os. Prémium szintű konfigurációk legalább egy egység két vagy több régióban üzembe helyezéssel rendelkezik 99,95 %-os SLA-t.

Írása idején a szolgáltatásiszint-szerződés (SLA) az Azure Logic Apps-ja 99,9 %.

### <a name="disaster-recovery"></a>Vészhelyreállítás

Vegye fontolóra Geo-vészhelyreállítás a Service Bus prémium szintű súlyos szolgáltatáskimaradás esetén a feladatátvétel helyévé. Tudjon meg többet [Azure Service Bus Geo-disaster recovery](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Felügyeleti szempontok

Hozzon létre külön erőforráscsoportok éles környezetben, fejlesztési, és tesztelési környezetek. Ez megkönnyíti az üzemelő példányok felügyeletét, a tesztkörnyezetek törlését és a hozzáférési jogok kiosztását.
Amikor erőforrásokat rendel az erőforráscsoportokhoz, vegye figyelembe a következőket:

- Életciklus. Általában érdemes az azonos életciklusú erőforrásokat ugyanabba az erőforráscsoportba helyezni.
- Hozzáférés. Használhat [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) a csoportokban található erőforrások hozzáférési szabályzatok alkalmazásához.
- Számlázás. Megtekintheti az erőforráscsoport összegzett költségeit.
- Tarifacsomag kiválasztása az API Management – javasoljuk a fejlesztői csomag fejlesztési és tesztelési környezetek esetében. Éles üzem előtti javasolt egy replikát, az éles környezet üzembe helyezése tesztek futtatása, és a költségek minimalizálása érdekében leállítása.

További információkért lásd: [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) áttekintése.

### <a name="deployment"></a>Környezet

Javasoljuk, hogy használjon [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md) üzembe helyezéséhez az Azure API Management, a Logic Apps, az Event Grid és a Service Bus. Sablonok megkönnyítik a PowerShell vagy az Azure parancssori felület (CLI) üzembe helyezések automatizálását.

Azt javasoljuk, hogy a saját külön Resource Manager-sablonok az Azure API Management, bármilyen egyéni Logic Apps, Event Grid-témakörök és Service Bus-névterek üzembe. Ez lehetővé teszi a verziókövetési rendszerekben tárolja őket. Ezek a sablonok majd együtt vagy külön-külön egy folyamatos integráció/folyamatos (CI/CD) folyamatot részeként telepíthető.

### <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

A Service Bus, mint például az API Management és a Logic Apps az Azure Monitor használatával figyelhető meg. Az Azure Monitor alapértelmezés szerint engedélyezve van, és minden szolgáltatáshoz beállítva a különböző mérőszámok alapján információkkal szolgálnak.

## <a name="security-considerations"></a>Biztonsági szempontok

A Service Bus egy közös hozzáférésű Jogosultságkód használatával biztonságos. [SAS hitelesítési](../service-bus-messaging/service-bus-sas.md) lehetővé teszi, hogy egy felhasználó hozzáférést biztosít a Service Bus-erőforrások, a megadott jogokat. Tudjon meg többet [Service Bus-hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Ezenkívül egy Service Bus-üzenetsorba kell ki vannak téve a HTTP-végpontként (így elegendő új üzenet feladása), az API Management védi meg azt a végpontot fronting által használható. Ez majd védve legyenek a tanúsítványok vagy OAuth szükség szerint. Ennek legegyszerűbb módja használ egy logikai alkalmazást egy kérés/válasz HTTP-eseményindítóval fogadásukig.

Event Grid egy érvényesítési kód eseménykézbesítés védi. Az események felhasználásához használatakor LogicApps, ez automatikusan történik. További részletekre kíváncsi [Event Grid biztonsági és hitelesítési](../event-grid/security-authentication.md).

## <a name="next-steps"></a>További lépések

- [Egyszerű vállalati integráció](logic-apps-architectures-simple-enterprise-integration.md)