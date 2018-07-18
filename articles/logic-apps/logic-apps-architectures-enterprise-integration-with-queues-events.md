---
title: Az Azure integrációs szolgáltatások vállalati integráció referenciaarchitektúra
description: A referenciaarchitektúra bemutatja, hogyan egy vállalati integrációs minta megvalósítása a Logic Apps, az API Management, Service Bus és Event Grid használatával ismerteti.
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
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116112"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Referenciaarchitektúra: üzenetsorokat és események vállalati integráció

A következő referencia-architektúra, amely egy adatintegrációs alkalmazás által használt Azure integrációs szolgáltatások alkalmazhat bevált eljárásokat mutat be. Az architektúra számos különböző alkalmazásminták HTTP API-k, a munkafolyamat és a koordinálást igénylő alapjául szolgálhatnak.

![Architektúradiagram - üzenetsorok és események vállalati integráció](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*Az integrációs technológiával számos lehetséges alkalmazások is vannak. Ezek között egy egyszerű, pont-pont típusú alkalmazást a teljes nagyvállalati Azure Service Bus-alkalmazás. Az architektúra sorozat azt írja le, az újrafelhasználható összetevőit, előfordulhat, hogy egy általános integrációs alkalmazások összeállítására vonatkoznak. Architects figyelembe kell venni, hogy melyik azok alkalmazása és az infrastruktúra megvalósításához szükséges összetevőket.*

## <a name="architecture"></a>Architektúra

Az architektúra *épül* a [egyszerű vállalati integráció](logic-apps-architectures-simple-enterprise-integration.md) architektúra. [Az egyszerű enterprise architecture vonatkozó javaslatokat](logic-apps-architectures-simple-enterprise-integration.md#recommendations) Itt a alkalmazni. Ezek kihagy a [javaslatok](#recommendations) kivonatosan ebben a cikkben. 

Az architektúra a következő összetevőkből áll:

- **Erőforráscsoport**. Az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az Azure-erőforrások logikai tárolója.
- **Az Azure API Management**. [Az API Management](https://docs.microsoft.com/azure/api-management/) egy teljes körűen felügyelt platform, amellyel közzététele, biztosítása és a HTTP API-jai átalakíthatók.
- **Az Azure API Management fejlesztői portálon**. Az Azure API Management minden példányának együttműködik a hozzáférést a [fejlesztői portál](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Az API Management fejlesztői portálon hozzáférést biztosít a dokumentációhoz és kódmintákhoz mintákhoz. Tesztelheti az API-k a fejlesztői portálon.
- **Az Azure Logic Apps**. [A Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) egy kiszolgáló nélküli platform, amellyel vállalati munkafolyamat és integrációs hozhat létre.
- **Összekötők**. Használja a Logic Apps [összekötők](https://docs.microsoft.com/azure/connectors/apis-list) szeretne csatlakozni a gyakran használt szolgáltatásokat. A Logic Apps már rendelkezik a különböző összekötők több száz, de létrehozhat egy egyéni összekötőt is.
- **Az Azure Service Bus**. [A Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) biztonságos és megbízható üzenetküldést biztosít. Üzenetküldési használható alkalmazások szétválaszthatók és más üzenetalapú rendszerekkel való integrációt segítik.
- **Az Azure Event Grid**. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) egy kiszolgáló nélküli platform, amely tehet közzé és alkalmazásesemények továbbítására szolgál.
- **IP-cím**. Az Azure API Management szolgáltatás rendelkezik egy nyilvános [IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) és a egy tartomány nevét. A tartománynév altartománya API.NET-webhelyen, azure, például a contoso.azure-API.NET webhelyen. A Logic Apps és a Service Bus is nyilvános IP-címet. Azonban ebben az architektúrában azt korlátozza a hozzáférést a Logic Apps-végpontok csak IP-címet az API Management hívása (a biztonság). A Service Bus-hívások közös hozzáférésű jogosultságkód (SAS) védik.
- **Azure DNS**. [Az Azure DNS](https://docs.microsoft.com/azure/dns/) DNS-tartományok egy üzemeltetési szolgáltatás. Az Azure DNS névfeloldás biztosít a Microsoft Azure-infrastruktúra használatával. A tartományok Azure-ban üzemelteti, azonos hitelesítő adatokkal, API-kkal, eszközökkel és számlázási használata más Azure-szolgáltatások DNS-rekordok is kezelheti. Egy egyéni tartománynevet (például contoso.com) használatához hozzon létre az egyéni tartománynév leképezése az IP-cím DNS-rekordjait. További információkért lásd: [egyéni tartománynév beállítása az API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Használat [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) vagy egy másik identitásszolgáltatót a hitelesítéshez. Az Azure AD hitelesítési API-végpontjainak eléréséhez átadásával biztosít egy [JSON Web Token az API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) ellenőrzése. Az Azure AD biztosíthat hozzáférést az API Management fejlesztői portálon (Standard és prémium szinten csak).

Az architektúra rendelkezik néhány alapvető működéséhez a mintákat:

* Meglévő háttér-HTTP API-k tesszük közzé az API Management fejlesztői portálon keresztül. A portálon, a fejlesztők (vagy a szervezet vagy külső, belső) is beépíthetik a ezekkel az API-hívásokat.
* Összetett API-k van építve a logic apps használatával, és a egy szoftverszolgáltatás (SaaS) rendszerek, Azure-szolgáltatások és minden API-k és az API Management közzétett szoftver hívásainak replikálásával. [A Logic apps is közzétett](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) az API Management fejlesztői portálon keresztül.
- Alkalmazások használata az Azure ad-vel [az OAuth 2.0 biztonsági jogkivonat beszerzése](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , amely szükséges API-k eléréséhez.
- Az API Management [érvényesíti a biztonsági jogkivonatot](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , és ezután továbbítja a háttérrendszeri API-t vagy a logikai alkalmazás.
- Service Bus-üzenetsorok is használható [szétválaszthatók](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) alkalmazás tevékenységet, és [terhelés kiugrások smooth](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Üzenetek hozzáadta a logic apps, a harmadik féltől származó alkalmazások, várólisták és (nem szerepel) tegye közzé az üzenetsorba API Management szolgáltatáson keresztül HTTP API-ként.
- Üzeneteket egy Service Bus-üzenetsorba van hozzáadva, amikor az esemény akkor következik be. Az esemény váltja ki, egy logikai alkalmazást. A logikai alkalmazás ezután feldolgozza az üzenetet.
- Más Azure-szolgáltatások (például az Azure Blob storage és az Azure Event Hubs) is közzé az eseményeket az Event Gridbe. Ezek a szolgáltatások indítása a logic apps az eseményt, és hajtsa végre az ezt követő műveleteket.

## <a name="recommendations"></a>Javaslatok

A konkrét követelmények eltérhetnek az ebben a cikkben leírt általános architektúrát. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="service-bus-tier"></a>A Service Bus-szint

A Service Bus prémium szintű csomagot használja. A Premier szintű támogatja az Event Grid értesítéseket. További információkért lásd: [Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Event Grid-díjszabás

Event Grid egy kiszolgáló nélküli modellt használ. A számlázás a műveletek (esemény végrehajtása) száma alapján lesz kiszámítva. További információkért lásd: [Event Grid díjszabási](https://azure.microsoft.com/pricing/details/event-grid/). Jelenleg nincsenek szint szempontok az Event Gridhez.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>PeekLock használata a Service Bus-üzenetek

A Service Bus-üzenetek lefoglalhatja a Logic Apps-alkalmazás létrehozásakor [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) egy csoportot az üzenetek el a logikai alkalmazásban. PeekLock használatakor a logikai alkalmazás lépésekkel végrehajtása vagy a megszakítása előtt minden üzenetet érvényesítéséhez. Ez a megközelítés véletlen üzenet adatvesztés elleni védelmet biztosít.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Az Event Grid-trigger akkor aktiválódik, ha több objektum keresése

Az Event Grid-trigger akkor aktiválódik, amikor azt jelenti, hogy egyszerűen, hogy "ezekről legalább egy történt." Event Grid egy logikai alkalmazást, amely akkor jelenik meg a Service Bus-üzenetsorba egy üzenet aktivál, ha a logikai alkalmazás kell mindig tegyük fel, hogy van egy vagy több üzenetet rendelkezésre állnak.

### <a name="region"></a>Régió

Az API Management, Logic Apps, és a hálózati késés minimalizálása érdekében a Service Bus ugyanabban a régióban üzembe. Általánosságban elmondható válassza ki a felhasználókhoz legközelebb eső régióban.

Az erőforráscsoport szintén van régiója. A régió központi telepítési metaadatok tárolódnak, és ahol hajt végre a központi telepítési sablont a adja meg. Helyezze el az erőforráscsoportot és az erőforrások üzembe helyezése során a rendelkezésre állás növelése érdekében ugyanabban a régióban.

## <a name="scalability"></a>Méretezhetőség

A Service Bus prémium szintű horizontálisan magasabb, a skálázhatóság érdekében üzenetkezelési egységek száma. Prémium szintű konfigurációk egy, kettő vagy négy üzenetkezelési egységre is rendelkezhet. A Service Bus méretezésével kapcsolatos további információkért lásd: [ajánlott eljárások a teljesítmény Service Bus-üzenetkezelés használatával](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Rendelkezésre állás

A szolgáltatásiszint-szerződés (SLA) az Azure API Management jelenleg az alapszintű, Standard és prémium szint esetében 99,9 %-os. Prémium szintű konfigurációk legalább egy egység két vagy több régióban üzembe helyezéssel rendelkezik 99,95 %-os SLA-t.

Az SLA-t az Azure Logic Apps jelenleg 99,9 %-os.

### <a name="disaster-recovery"></a>Vészhelyreállítás

Vegye fontolóra geo-vészhelyreállítás a Service Bus prémium szintű súlyos kimaradás során, a feladatátvétel helyévé. További információkért lásd: [Azure Service Bus geo-disaster recovery](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Kezelhetőségi

Hozzon létre külön erőforráscsoportok éles környezetben, fejlesztési, és tesztelési környezetek. Külön erőforráscsoportok megkönnyíti a központi telepítések felügyeletéhez szükséges, a tesztkörnyezetek törlését és a hozzáférési jogosultságok hozzárendelése.

Amikor erőforrásokat rendel erőforráscsoportok, a következő tényezőket kell figyelembe venni:

- **Életciklus**. Általánosságban elmondható helyezze ugyanabba az erőforráscsoportba az azonos életciklussal rendelkező erőforrások.
- **Hozzáférés**. Használhat [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) a csoportokban található erőforrások hozzáférési szabályzatok alkalmazásához.
- **Számlázási**. Megtekintheti az erőforráscsoport költségeinek összesítése.
- **Az API Management tarifacsomag**. Azt javasoljuk, hogy a fejlesztői csomag fejlesztési és tesztelési környezetek. Az üzem előtti környezet azt javasoljuk egy replikát, az éles környezet üzembe helyezése tesztek futtatása, és minimalizálja a költségeket a leállítása.

További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

### <a name="deployment"></a>Környezet

Javasoljuk, hogy használjon [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md) az API Management, a Logic Apps, az Event Grid és a Service Bus üzembe helyezéséhez. Sablonok megkönnyítik a PowerShell vagy az Azure CLI-vel üzembe helyezések automatizálását.

Azt javasoljuk, hogy az API Management, minden egyes logic apps, az Event Grid-témakörök és a Service Bus-névterek és a saját, elkülönített Resource Manager-sablonok. Amikor külön tanúsítványsablonokat, verziókövetési rendszerekben tárolhatja az erőforrásokat. Ezután telepítheti ezeket a sablonokat együtt vagy külön-külön a folyamatos integráció/folyamatos készregyártás (CI/CD) folyamat részeként.

### <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

Az API Management és a Logic Apps, mint a Service Bus segítségével figyelheti az Azure Monitor használatával. Az Azure Monitor információk alapján, hogy az egyes szolgáltatások vannak konfigurálva. Az Azure Monitor alapértelmezés szerint engedélyezve van.

## <a name="security"></a>Biztonság

A Service Bus biztonságos SAS használatával. Használhat [SAS hitelesítési](../service-bus-messaging/service-bus-sas.md) egy felhasználó hozzáférést adott jogosultsággal rendelkező Service Bus-erőforrások. További információkért lásd: [Service Bus-hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Ha egy Service Bus-üzenetsorba ki vannak téve a HTTP-végpontként (az új üzenetek közzététele) van szüksége, az API Management biztonságossá a végpontot fronting kell használnia. A végpont majd védve legyenek a tanúsítványok vagy OAuth szükség szerint. A végpont biztonságos legegyszerűbben használatával egy logikai alkalmazást egy kérés/válasz HTTP-eseményindítóval fogadásukig.

Event Grid egy érvényesítési kód eseménykézbesítés védi. Logic Apps használatával felhasználása az eseményt, ha az érvényesítés automatikusan történik. További információkért lásd: [Event Grid biztonsági és hitelesítési](../event-grid/security-authentication.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg [egyszerű vállalati integráció](logic-apps-architectures-simple-enterprise-integration.md).