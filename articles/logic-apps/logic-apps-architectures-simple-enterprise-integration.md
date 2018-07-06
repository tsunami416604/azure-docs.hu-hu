---
title: Azure integrációs szolgáltatásai – egyszerű vállalati integráció
description: A referenciaarchitektúra az Azure Logic Apps és az Azure API Management egy egyszerű enterprise-integrációs minta megvalósítása megjelenítése
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
ms.openlocfilehash: df86ca5aed405ab5eda05c1dd207f0b6656bfd96
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860197"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Egyszerű vállalati integráció – referenciaarchitektúra

## <a name="overview"></a>Áttekintés

Ez a referenciaarchitektúra egy adatintegrációs alkalmazás által használt Azure integrációs szolgáltatások bevált eljárásokat mutat be. Ez az architektúra a HTTP API-k, a munkafolyamat és a koordinálást igénylő számos különböző alkalmazásminták alapját szolgálhatnak.

*Számos lehetséges alkalmazás integrációs technológiával, az egyszerű pont a pont-alkalmazás, a teljes enterprise service bus is. Ez architektúra sorozat határoz meg egy általános adatintegrációs alkalmazás készítéséhez alkalmazandók újrafelhasználható összetevőit – architects gondolja át az alkalmazásaikat és infrastruktúrájukat megvalósítása kell adott összetevőit.*

   ![Architekturális diagramja – egyszerű vállalati integráció](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architektúra

Az architektúra a következő összetevőkből áll:

- Erőforráscsoport. Az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az Azure-erőforrások logikai tárolója.
- Az Azure API Management. [Az Azure API Management](https://docs.microsoft.com/azure/api-management/) közzététele, biztosítása és a HTTP API-k átalakítása egy teljes körűen felügyelt platform.
- Az Azure API Management fejlesztői portálon. Az Azure API Management minden példányának tartalmaz egy [fejlesztői portál](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), dokumentáció, Kódminták és tesztelhetik az API-KHOZ való hozzáférés megadását.
- Az Azure Logic Apps. [A Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) egy kiszolgáló nélküli platform nagyvállalati munkafolyamat és integrációs létrehozásához.
- Az összekötők. [Összekötők](https://docs.microsoft.com/azure/connectors/apis-list) Logic Apps által gyakran használt serviceshez való csatlakozáshoz használt. A Logic Apps már rendelkezik a különböző összekötők több száz, de ezek is hozható létre egy egyéni összekötőt.
- IP-cím. Az Azure API Management szolgáltatás rendelkezik egy nyilvános [IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) és a egy tartomány nevét. A tartománynév altartománya API.NET-webhelyen, azure, például a contoso.azure-API.NET webhelyen. A Logic Apps és a Service Bus is rendelkezik egy nyilvános IP-cím; azonban ebben az architektúrában a Microsoft-hozzáférés korlátozása hívja fel a Logic apps végpontok csak az IP cím az API Management (az biztonság). A Service Bus-hívások közös hozzáférésű jogosultságkód védik.
- Az Azure DNS. [Az Azure DNS](https://docs.microsoft.com/azure/dns/) egy üzemeltetési szolgáltatás DNS-tartományok biztosítani a névfeloldást a Microsoft Azure infrastruktúráját használja. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. Egy egyéni tartománynevet (például contoso.com) használatához hozzon létre az egyéni tartománynév leképezése az IP-cím DNS-rekordjait. További információkért lásd: Configure egy egyéni tartománynevet az Azure API Management szolgáltatásban.
- Az Azure Active Directory (Azure AD). Használat [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) vagy egy másik identitásszolgáltatót a hitelesítéshez. Az Azure AD hitelesítési API-hozzáférés végpontokra biztosít (átadásával egy [JSON Web Token az API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) ellenőrzése), és biztosíthatja a hozzáférést az API Management fejlesztői portálon (Standard és prémium szinten csak).

Ez az architektúra rendelkezik a művelet néhány alapvető minták:

2. Összetett API-k felhasználásával történik a Logic Apps; replikálásával segít a vállalatnak SAAS-rendszerekhez hívások, Azure-szolgáltatások és minden API-k közzététele és az API Management. A [Logic Apps is közzétett](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) az API Management fejlesztői portálon keresztül.
3. Alkalmazások [az OAuth 2.0 biztonsági jogkivonat beszerzése](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) szükséges API-khoz, az Azure Active Directory elérésekor.
4. Az Azure API Management [érvényesíti a biztonsági jogkivonatot](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad), és továbbítja a háttérrendszeri API-t vagy logikai alkalmazást.

## <a name="recommendations"></a>Javaslatok

A konkrét követelmények eltérhetnek az itt leírt általános architektúra. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="azure-api-management-tier"></a>Az Azure API Management-szint

Használja az alapszintű, Standard vagy prémium szint esetében, mivel egy éles SLA-t kínál, és támogatja a horizontális felskálázás az Azure-régión belül (szint szerint változó egységek száma). A prémium szintű is támogatja a horizontális felskálázás több Azure-régiók között. A réteg a szükséges átviteli sebességtől mértéke a kiválasztott alap- és szolgáltatáskészleteket. További információkért lásd: [az API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).

Ha még futnak az összes API Management-példány díja. Ha vertikálisan rendelkezik, és nincs szükség ilyen szintű teljesítmény az idő, vegye figyelembe, kihasználhatja az API Management óraalapú számlázás és méretezési.

### <a name="logic-apps-pricing"></a>A Logic Apps díjszabása

A Logic Apps működik, mint egy [kiszolgáló nélküli](logic-apps-serverless-overview.md) modell, a számlázás a műveleti és összekötőhöz végrehajtási alapján kiszámítja. Lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps/) további információt. Jelenleg nincs a Logic Apps szint szempontjai.

### <a name="logic-apps-for-asynchronous-api-calls"></a>A Logic Apps aszinkron API-hívások esetében

A Logic Apps a legalkalmasabb az alacsony késés – pl. aszinkron nem igénylő forgatókönyvek vagy félstatikus-hosszú ideig futó API-hívások. Ha kis késleltetésre szükség (például a hívás, amely letiltja a felhasználói felület) azt javasoljuk, hogy API-t vagy egy másik technológiát, például az Azure Functions vagy az üzembe helyezett App Service használatával Web API művelet végrehajtására. Továbbra is javasoljuk, hogy kell-e az API-t az API Management használata API-fogyasztókat fronted.

### <a name="region"></a>Régió

Az API Management üzembe helyezése és Logic Apps ugyanabban a régióban a hálózati késés minimalizálása érdekében. Általában érdemes a felhasználókhoz legközelebbi régiót választani.

Az erőforráscsoport szintén van régiója, amely megadja a központi telepítési metaadatok tárolódnak, és ahol a központi telepítési sablont a hajtja végre. Az erőforráscsoportot és a hozzá tartozó erőforrásokat helyezze ugyanabba a régióba. Ez javíthatja a rendelkezésre állást az üzembe helyezés során.

## <a name="scalability-considerations"></a>Méretezési szempontok

Az API Management-rendszergazdák hozzá kell adnia [gyorsítótárazási házirendek](../api-management/api-management-howto-cache.md) adott esetben a kapacitás bővítése érdekében a szolgáltatás és a háttérszolgáltatások terhelésének csökkentése.

Az Azure API Management alapszintű, Standard és Premium szintű kiterjeszthető az Azure-régióban a nagyobb kapacitást kínálnak. A rendszergazdák a kapacitás metrika belül a metrikák menü segítségével a a szolgáltatás használatának elemzése és vertikális felskálázás vagy leskálázás megfelelő módon.

Az API Management-szolgáltatás méretezése javaslatok:

- Skálázási igényeihez figyelembevétele forgalmi minták – több ideiglenes forgalmat rendelkező ügyfelek lesz a megnövekedett kapacitás nagyobb szükség.
- Konzisztens kapacitás 66 %-ot meghaladó jele lehet, vertikális kell.
- 20 % alatti konzisztens kapacitás jele lehet, vertikális leskálázás lehetőséget.
- Mindig ajánlott betölteni az API Management szolgáltatás egy tipikus terhelés tesztelése éles környezetben engedélyezése előtt.

Prémium szintű szolgáltatások kiterjeszthető több Azure-régiók között. Ügyfelek központi telepítése, ilyen módon magasabb SLA (99,95 %-os figyelésekor 99,9 %-os) érhetnek el, és építhető ki a felhasználók több régióban figyeléséért szolgáltatások.

A Logic Apps kiszolgáló nélküli modelljének köszönhetően a rendszergazdáknak nem kell, hogy a szolgáltatás méretezhetősége; a további szempontok a szolgáltatás automatikusan méretezi az igényeknek.

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Írása idején a szolgáltatásiszint-szerződés (SLA) az Azure API Management az alapszintű, Standard és prémium szint esetében 99,9 %-os. Prémium szintű konfigurációk legalább egy egység két vagy több régióban üzembe helyezéssel rendelkezik 99,95 %-os SLA-t.

Írása idején a szolgáltatásiszint-szerződés (SLA) az Azure Logic Apps-ja 99,9 %.

### <a name="backups"></a>Biztonsági másolatok

Az Azure API Management-beállításokat kell megadni [rendszeresen biztonsági másolat](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (megfelelően a változtatás rendszerességét alapján), és a egy helyet, vagy az Azure-régió, ahol a szolgáltatás található különböző tárolt biztonsági mentési fájlokat. Ügyfelek ezután választhat a DR-stratégiájuk két lehetőség egyikét:

1. A DR esemény egy új API Management-példány üzembe van helyezve, visszaállítja azt a biztonsági mentés és a DNS-rekordok vannak repointed.
2. Ügyfelek megtartása passzív másolat egy másik Azure-régiót (költségekkel jár további) biztonsági szolgáltatása sikeréért rendszeresen állítja vissza azt. A DR esemény csak a DNS-rekordokat kell repointed visszaállítani a szolgáltatás.

Logikai alkalmazások is nagyon gyorsan újra létrehozza és kiszolgáló nélküli, akkor készül biztonsági másolat által a társított Azure Resource Manager-sablon másolatának mentése. Ezek melyekbe menthetők a forrás-vezérlő/integrált ügyfelek általi folyamatos integráció/folyamatos készregyártás (CI/CD) folyamatot.

A Logic Apps, API Management szolgáltatáson keresztül közzétett kell frissíteni a helyüket, helyezze át egy másik adatközpontba. Ez egy egyszerű PowerShell-szkriptet a háttérrendszer tulajdonság frissítése az API használatával is elvégezhető.

## <a name="manageability-considerations"></a>Felügyeleti szempontok

Hozzon létre külön erőforráscsoportok éles környezetben, fejlesztési, és tesztelési környezetek. Ez megkönnyíti az üzemelő példányok felügyeletét, a tesztkörnyezetek törlését és a hozzáférési jogok kiosztását.
Amikor erőforrásokat rendel az erőforráscsoportokhoz, vegye figyelembe a következőket:

- Életciklus. Általában érdemes az azonos életciklusú erőforrásokat ugyanabba az erőforráscsoportba helyezni.
- Hozzáférés. Használhat [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) a csoportokban található erőforrások hozzáférési szabályzatok alkalmazásához.
- Számlázás. Megtekintheti az erőforráscsoport összegzett költségeit.
- Tarifacsomag kiválasztása az API Management – javasoljuk a fejlesztői csomag fejlesztési és tesztelési környezetek esetében. Éles üzem előtti javasolt egy replikát, az éles környezet üzembe helyezése tesztek futtatása, és a költségek minimalizálása érdekében leállítása.

További információkért lásd: [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) áttekintése.

### <a name="deployment"></a>Környezet

Javasoljuk, hogy használjon [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md) üzembe helyezéséhez az Azure API Management és az Azure Logic Apps. Sablonok megkönnyítik a PowerShell vagy az Azure parancssori felület (CLI) üzembe helyezések automatizálását.

Azt javasoljuk, hogy a saját külön Resource Manager-sablonok az Azure API Management és az összes egyéni Logic Apps üzembe. Ez lehetővé teszi a verziókövetési rendszerekben tárolja őket. Ezek a sablonok majd együtt vagy külön-külön egy folyamatos integráció/folyamatos (CI/CD) folyamatot részeként telepíthető.

### <a name="versions"></a>Verziók

Minden alkalommal, amikor egy konfigurációs győződjön meg arról, módosítsa a logikai alkalmazás (vagy egy Resource Manager-sablon segítségével frissítést telepíteni), azt a verziót másolatát adatért a felhasználók kényelme érdekében (futtatási előzmények rendelkező összes verzió folyamatosan). Ezek a fájlok segítségével korábbi változásainak követése, és lehet a logikai alkalmazás; az aktuális konfiguráció verziója is támogatja Ez azt jelenti, is hatékonyan visszaállítási egy logikai alkalmazást, például.

Az API Management rendelkezik két különböző (de díjtalan) [versioning fogalmak](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Verziók biztosít az API-fogyasztókat alkalmazásmodelleknek köszönhetően az API-t fizetnek sikerült (pl. v1, v2 vagy bétaverzió, éles környezetben) igényeik alapján.
- Változatok, hogy a rendszergazdák API biztonságosan módosítja egy API-t, és telepíthet a felhasználók számára a nem kötelező mellékmondatokkal.

Üzembe helyezés – változatok arra, hogy biztonságosan, hajtsa végre a módosításokat kell alkalmazni az API Management kontextusában tartsa a módosítási előzményeit, és győződjön meg arról, az API-fogyasztókat figyelembe ezeket a módosításokat. Egy változat fejlesztői környezetben létrehozott és telepített más környezetek használatával a Resource Manager-sablonok között lehet.

Változatok használható API-k tesztelése előtt az "aktuális" történik, és a felhasználók számára elérhető, bár nem javasoljuk ezt a mechanizmust betöltése vagy integrációs tesztelés – külön tesztelési vagy éles üzem előtti környezetben kell használni helyette.

### <a name="configuration"></a>Konfiguráció

Soha ne tároljon jelszavakat, hozzáférési kulcsokat és kapcsolati karakterláncokat a verziókövetési. Ha szükség van rájuk, használja a megfelelő módszer üzembe helyezéséhez és biztonságos ezeket az értékeket. 

A Logic Apps esetében minden szükséges a logikai alkalmazásban (azaz nem hozható létre kapcsolat formájában) bizalmas értékeket kell az Azure Key Vaultban tárolt és a Resource Manager-sablon hivatkozik. Emellett javasoljuk, hogy központi telepítési sablon paramétereit használatával az egyes környezetekhez paraméterfájlokkal együtt. További útmutatást [biztonságossá tétele a paramétereket és a egy munkafolyamaton belül bemenetek](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Az API Management titkos kulcsok segítségével kezelt objektumokban, úgynevezett nevű értékek, illetve a tulajdonságokat. Ezek biztonságosan tárolja elérhető értékeket az API Management házirendek. Lásd: hogyan [az API Management titkos kódok kezelése](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

Mindkét [az API Management](../api-management/api-management-howto-use-azure-monitor.md) és [Logic Apps](logic-apps-monitor-your-logic-apps.md) támogatja a műveletek figyelése keresztül [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure Monitor alapértelmezés szerint engedélyezve van, és minden szolgáltatáshoz beállítva a különböző mérőszámok alapján információkkal szolgálnak.

Ezenkívül további lehetőség van az egyes szolgáltatásokhoz:

- Logic Apps-naplók elküldött [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) mélyebb elemzésre és dashboarding.
- Az API Management konfigurálása az Application Insights figyelési és-üzemeltetői támogatja.
- Az API Management támogatja a [Power BI Megoldássablon az egyéni API-analytics](http://aka.ms/apimpbi). Ez a megoldássablon lehetővé teszi az üzleti felhasználók számára a Power bi-ban elérhető jelentéseket a saját egyéni elemzési megoldás létrehozásához.

## <a name="security-considerations"></a>Biztonsági szempontok

Ebben a szakaszban leírt ebben a cikkben leírtak szerint az architektúra üzembe helyezett Azure-szolgáltatásokra vonatkozó biztonsági szempontokat sorolja fel. Nem tartalmazza az összes ajánlott biztonsági eljárást.

- Szerepköralapú hozzáférés-vezérlés (RBAC) segítségével győződjön meg, hogy megfelelő szintű hozzáféréssel a felhasználók számára.
- Az API Management OAuth/nyílt IDConnect használ nyilvános API-végpontok védelme. Ehhez egy identitásszolgáltató konfigurálása és a egy JWT-ellenőrzési szabályzat felvétele.
- Csatlakozás a Háttérszolgáltatásokhoz az API Management-tanúsítványok kölcsönös használatával
- HTTP eseményindító-alapú logikai alkalmazások védelme a hozzon létre egy IP-cím engedélyezett az IP cím az API Management mutat. Ez megakadályozza, hogy a logikai alkalmazás hívása a nyilvános interneten keresztül az API Management első áthaladás nélkül.

Ez a referenciaarchitektúra bemutatta, hogyan hozhat létre egy egyszerű vállalati integrációs platform Azure integrációs szolgáltatások használatával.

## <a name="next-steps"></a>További lépések

- [Enterprise Integration és üzenetsorokat és események](logic-apps-architectures-enterprise-integration-with-queues-events.md)
