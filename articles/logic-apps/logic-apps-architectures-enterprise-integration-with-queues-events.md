---
title: Vállalati integráció az architekturális mintáról – Azure integrációs szolgáltatások
description: Ez az architektúra útmutató bemutatja, hogyan valósítható meg az Azure Logic Apps, az Azure API Management, az Azure Service Bus és az Azure Event Grid, egy vállalati integrációs minta
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 8fbc84b4016659b6d0d6ce9ec47c05a0b241c3d9
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855374"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Vállalati integrációs architektúra az üzenetsorok és események

Ez a cikk ismerteti az Azure integrációs szolgáltatások használata esetén egy adatintegrációs alkalmazás alkalmazhat bevált eljárásokat használó vállalati integrációs architektúra. Ez az architektúra HTTP API-k, a munkafolyamat és a koordinálást igénylő számos különböző alkalmazás-mintákat alapjaként használható.

![Architektúradiagram - üzenetsorok és események vállalati integráció](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

Ez a sorozat azt írja le, az újrafelhasználható összetevőit, előfordulhat, hogy egy általános integrációs alkalmazások összeállítására vonatkoznak. Mert integrációs technológiával számos lehetséges alkalmazás és egyszerű pont-pont típusú alkalmazások a teljes nagyvállalati Azure Service Bus-alkalmazások, érdemes lehet meg kell valósítania az alkalmazások és infrastruktúra összetevőit.

## <a name="architecture-components"></a>Architektúra-összetevők

Ez az architektúra számos tekintetben az a cikkben leírt architektúra [architektúra-Útmutató: egyszerű vállalati integráció](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md). Az architektúra [javaslatok](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations) itt is érvényesek, de kihagytuk, ez a cikk az áttekinthetőség kedvéért kihagyja a javasolt lépéseket a [javaslatok](#recommendations) szakaszban. A vállalati integrációs architektúra az alábbi összetevőket tartalmazza:

- **Erőforráscsoport**: A [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) Azure-erőforrások logikai tárolója.

- **Az Azure API Management**: A [az API Management](https://docs.microsoft.com/azure/api-management/) service az közzététele, biztosítása és HTTP API-k átalakítása egy teljes körűen felügyelt platform.

- **Az Azure API Management fejlesztői portálon**: az Azure API Management minden példányának hozzáférést biztosít a [fejlesztői portál](../api-management/api-management-customize-styles.md). Ezen a portálon hozzáférést biztosít a dokumentációhoz és kódmintákhoz mintákhoz. API-k a fejlesztői portálon is tesztelheti.

- **Az Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) egy kiszolgáló nélküli platform a nagyvállalati munkafolyamatok és integrációk készítéséhez.

- **Összekötők**: használja a Logic Apps [összekötők](../connectors/apis-list.md) csatlakozik a gyakran használt szolgáltatások esetében. A Logic Apps-összekötők több száz kínál, de létrehozhat egy egyéni összekötőt is.

- **Az Azure Service Bus**: [a Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) biztonságos és megbízható üzenetküldést biztosít. Elválasztás alkalmazások és a többi üzenet-alapú rendszerek integrálása üzenetkezelést is használhatja.

- **Az Azure Event Grid**: [Event Grid](../event-grid/overview.md) közzétételéhez és alkalmazásesemények továbbítása egy kiszolgáló nélküli platform.

- **IP-cím**: az Azure API Management szolgáltatás rendelkezik egy nyilvános [IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md) és a egy tartomány nevét. Az alapértelmezett tartomány nevét részterületét képezi az azure-API.NET webhelyen, például contoso.azure-API.NET webhelyen, de beállíthatja úgy is [egyéni tartományok](../api-management/configure-custom-domain.md). A Logic Apps és a Service Bus is nyilvános IP-címet. Azonban a biztonság érdekében ez az architektúra korlátozza a hozzáférést az API Management csak az IP-címét, a Logic Apps-végpontok hívása. A Service Bus-hívások közös hozzáférésű jogosultságkód (SAS) védik.

- **Az Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) DNS-tartományok egy üzemeltetési szolgáltatás. Az Azure DNS névfeloldás biztosít a Microsoft Azure-infrastruktúra használatával. A tartományok Azure-ban üzemelteti, azonos hitelesítő adatokkal, API-kkal, eszközökkel és számlázási használata más Azure-szolgáltatások DNS-rekordok is kezelheti. Egyéni tartománynév, amilyen a contoso.com használatához hozzon létre az egyéni tartománynév leképezése az IP-cím DNS-rekordjait. További információkért lásd: [egyéni tartománynév beállítása az API Management](../api-management/configure-custom-domain.md).

- **Az Azure Active Directory (Azure AD)**: használhatja [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) vagy egy másik identitásszolgáltatót a hitelesítéshez. Az Azure AD hitelesítési API-végpontjainak eléréséhez átadásával biztosít egy [JSON Web Token az API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) ellenőrzése. A Standard és Premium szintű Azure ad-ben biztosíthat hozzáférést az API Management fejlesztői portálon.

## <a name="patterns"></a>Minták 

Ez az architektúra, amely az alapvető fontosságú a művelet néhány minták használja:

* Meglévő háttér-HTTP API-k tesszük közzé az API Management fejlesztői portálon keresztül. A portálon, fejlesztőknek vagy a szervezet, vagy külső, belső  
Ezekkel az API-hívások is integrálható alkalmazásokat.

* Összetett API-k van építve a logic apps, amely szoftver hívásainak koordinálhatja a szoftverszolgáltatások (SaaS) rendszerek, Azure-szolgáltatások és minden API Management közzétett API-k használatával. Logikai alkalmazások is vannak [az API Management fejlesztői portálon keresztül közzétett](../api-management/import-logic-app-as-api.md).

* Az Azure AD használata az alkalmazások [az OAuth 2.0 biztonsági token beszerzése a](../api-management/api-management-howto-protect-backend-with-aad.md) , amely az API eléréséhez szükséges.

* Az Azure API Management [érvényesíti a biztonsági jogkivonatot](../api-management/api-management-howto-protect-backend-with-aad.md) , és ezután továbbítja a háttérrendszeri API-t vagy a logikai alkalmazás.

* Az Azure Service Bus-üzenetsorok használhatók [elválasztás](../service-bus-messaging/service-bus-messaging-overview.md) alkalmazás tevékenység és a [terhelés kiugrások simítás](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Üzenetek hozzáadta a logic apps, a harmadik féltől származó alkalmazások, várólisták és (nem látható) tegye közzé az üzenetsorba API Management szolgáltatáson keresztül HTTP API-ként.

* Üzeneteket egy Service Bus-üzenetsorba van hozzáadva, amikor az esemény akkor következik be. Az esemény elindít egy logikai alkalmazást, amely ezután feldolgozza az üzenetet.

* Más Azure-szolgáltatásokkal, például az Azure Blob Storage és az Azure Event Hubs is közzé az eseményeket az Event Gridbe. Ezek a szolgáltatások indítása a logic apps az eseményt, és hajtsa végre az ezt követő műveleteket.

## <a name="recommendations"></a>Javaslatok

A konkrét követelmények eltérhetnek az ebben a cikkben leírt általános architektúrát. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="service-bus-tier"></a>A Service Bus-szint

A Service Bus prémium szint, amely támogatja az Event Grid értesítések használata. További információkért lásd: [Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Event Grid-díjszabás

Event Grid egy kiszolgáló nélküli modellt használ. A számlázás a műveletek (esemény végrehajtások) száma alapján lesz kiszámítva. További információkért lásd: [Event Grid díjszabási](https://azure.microsoft.com/pricing/details/event-grid/). Jelenleg nincsenek szint szempontok az Event Gridhez.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>PeekLock használata a Service Bus-üzenetek

Amikor létrehoz egy logikai alkalmazást a Service Bus-üzenetek lefoglalhatja, rendelkezik a logikai alkalmazás használja [PeekLock](../service-bus-messaging/service-bus-messaging-overview.md#queues) üzenetek csoportja eléréséhez. PeekLock használatakor a logikai alkalmazás lépésekkel végrehajtása vagy a megszakítása előtt minden üzenetet érvényesítéséhez. Ez a megközelítés véletlen üzenet adatvesztés elleni védelmet biztosít.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Az Event Grid-trigger akkor aktiválódik, ha több objektum keresése

Az Event Grid-trigger akkor aktiválódik, amikor ez a művelet egyszerűen azt jelenti, hogy "ezekről legalább egy történt." Event Grid egy logikai alkalmazást, amely akkor jelenik meg a Service Bus-üzenetsorba egy üzenet aktivál, ha a logikai alkalmazás kell mindig tegyük fel, hogy van egy vagy több üzenetet rendelkezésre állnak.

### <a name="region"></a>Régió

Hálózati késés minimalizálása érdekében válassza ki az API Management, a Logic Apps és a Service Bus ugyanabban a régióban. Általánosságban elmondható válassza ki a felhasználókhoz legközelebb eső régióban.

Az erőforráscsoport szintén van régiója. Ebben a régióban megadja a központi telepítési metaadatok tárolására és hol hajtsa végre a központi telepítési sablont. Üzembe helyezés során javítható a rendelkezésre állás, helyezze az erőforráscsoport és erőforrások ugyanabban a régióban.

## <a name="scalability"></a>Méretezhetőség

Magasabb szintű skálázhatóság érdekében a Service Bus prémium szintű horizontálisan üzenetkezelési egységek száma. Prémium szintű konfigurációk egy, kettő vagy négy üzenetkezelési egységre is rendelkezhet. A Service Bus méretezésével kapcsolatos további információkért lásd: [ajánlott eljárások a teljesítmény Service Bus-üzenetkezelés használatával](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Rendelkezésre állás

* Alapszintű, Standard és prémium szinten a szolgáltatásiszint-szerződés (SLA) az Azure API Management jelenleg 99,9 %-os. Prémium szintű konfigurációk telepítés, amelynek legalább egy egységet két vagy több régióban az SLA nem 99,95 %-os.

* Az SLA-t az Azure Logic Apps jelenleg 99,9 %-os.

### <a name="disaster-recovery"></a>Vészhelyreállítás

Ahhoz, hogy feladatátvétel súlyos kimaradás során, vegye fontolóra geo-vészhelyreállítás a Service Bus prémium szintű. További információkért lásd: [Azure Service Bus geo-disaster recovery](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Kezelhetőségi

Hozzon létre külön erőforráscsoportok éles környezetben, fejlesztési, és tesztelési környezetek. Külön erőforráscsoportok megkönnyítik a központi telepítések felügyeletéhez szükséges, a tesztkörnyezetek törlését és a hozzáférési jogosultságok hozzárendelése.

Amikor erőforrásokat rendel erőforráscsoportok, vegye figyelembe a következőket:

* **Életciklus**: általában az ugyanabban az erőforráscsoportban az azonos életciklussal rendelkező erőforrásokat helyezze.

* **Hozzáférés**: csoportokban található erőforrások hozzáférési házirendeket alkalmazza, használhatja [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md).

* **Számlázási**: megtekintheti az erőforráscsoport összesítő költséget.

* **Az API Management tarifacsomag**: a fejlesztői tarifacsomag használatát a fejlesztési és tesztelési környezetek esetében. A költségek csökkentésére üzem előtti tesztelés során, egy replikát, az éles környezet üzembe helyezése, a tesztek futtatásához, és majd állítsa le.

További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

## <a name="deployment"></a>Környezet

* Az API Management, a Logic Apps, az Event Grid és a Service Bus üzembe helyezéséhez használja a [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md). Sablonok egyszerűbbé automatizálhatja központi telepítések PowerShell vagy az Azure CLI használatával.

* Helyezze el az API Management, minden egyes logic apps, az Event Grid-témakörök és a Service Bus-névterek saját külön Resource Manager-sablonok. Külön sablonok használatával tárolhatja verziókövetési rendszerekben az erőforrásokat. Ezután telepítheti ezeket a sablonokat együtt vagy külön-külön a folyamatos integráció/folyamatos készregyártás (CI/CD) folyamat részeként.

## <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

Az API Management és a Logic Apps, mint a Service Bus figyelheti az Azure Monitor, amely alapértelmezés szerint engedélyezve van. Az Azure Monitor információk alapján, hogy az egyes szolgáltatások vannak konfigurálva. 

## <a name="security"></a>Biztonság

A Service Bus biztonságos, használja a közös hozzáférésű jogosultságkód (SAS). Például is hozzáférést biztosít egy felhasználó a megadott jogokat a Service Bus-erőforrások használatával [SAS hitelesítési](../service-bus-messaging/service-bus-sas.md). További információkért lásd: [Service Bus-hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Ha szeretne közzétenni egy HTTP-végpontot, Service Bus-üzenetsorba, például az új üzeneteket tehet közzé az API Management védheti a várólista fronting a végpont által. A végpont tanúsítványokat vagy OAuth-hitelesítés megfelelő majd gondoskodhat. A legegyszerűbb védelmének módja lehet a végpont köztes egy logikai alkalmazást használ egy HTTP-kérés/válasz eseményindító.

Az Event Grid szolgáltatás egy érvényesítési kód eseménykézbesítés védi. Ha a Logic Apps használatával az események felhasználásához, érvényesítés automatikusan történik. További információkért lásd: [Event Grid biztonsági és hitelesítési](../event-grid/security-authentication.md).

## <a name="next-steps"></a>További lépések

* Ismerje meg [egyszerű vállalati integráció](logic-apps-architectures-simple-enterprise-integration.md)
