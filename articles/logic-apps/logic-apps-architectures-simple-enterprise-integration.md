---
title: Egyszerű vállalati integrációs architektúra minta – Azure integrációs szolgáltatások
description: Ez az architektúra útmutató bemutatja, hogyan implementálható egy egyszerű vállalati integrációs minta Azure Logic Apps és az Azure API Management használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7081c9e4f6e6deee196255f04180a8f2cc792876
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122495"
---
# <a name="simple-enterprise-integration-architecture"></a>Egyszerű vállalati integráció architektúrája

Ez a cikk ismerteti az Azure integrációs szolgáltatások használata esetén egy adatintegrációs alkalmazás alkalmazhat bevált eljárásokat használó vállalati integrációs architektúra. Ez az architektúra HTTP API-k, a munkafolyamat és a koordinálást igénylő számos különböző alkalmazás-mintákat alapjaként használható.

![Architekturális diagramja – egyszerű vállalati integráció](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

Ez a sorozat azt írja le, az újrafelhasználható összetevőit, előfordulhat, hogy egy általános integrációs alkalmazások összeállítására vonatkoznak. Mert integrációs technológiával számos lehetséges alkalmazás és egyszerű pont-pont típusú alkalmazások a teljes nagyvállalati Azure Service Bus-alkalmazások, érdemes lehet meg kell valósítania az alkalmazások és infrastruktúra összetevőit.

## <a name="architecture-components"></a>Architektúra-összetevők

A vállalati integrációs architektúra az alábbi összetevőket tartalmazza:

- **Erőforráscsoport**: A [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) Azure-erőforrások logikai tárolója.

- **Az Azure API Management**: A [az API Management](https://docs.microsoft.com/azure/api-management/) service az közzététele, biztosítása és HTTP API-k átalakítása egy teljes körűen felügyelt platform.

- **Az Azure API Management fejlesztői portálon**: az Azure API Management minden példányának hozzáférést biztosít a [fejlesztői portál](../api-management/api-management-customize-styles.md). Ezen a portálon hozzáférést biztosít a dokumentációhoz és kódmintákhoz mintákhoz. API-k a fejlesztői portálon is tesztelheti.

- **Az Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) egy kiszolgáló nélküli platform a nagyvállalati munkafolyamatok és integrációk készítéséhez.

- **Összekötők**: használja a Logic Apps [összekötők](../connectors/apis-list.md) csatlakozik a gyakran használt szolgáltatások esetében. A Logic Apps-összekötők több száz kínál, de létrehozhat egy egyéni összekötőt is.

- **IP-cím**: az Azure API Management szolgáltatás rendelkezik egy nyilvános [IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md) és a egy tartomány nevét. Az alapértelmezett tartomány nevét részterületét képezi az azure-API.NET webhelyen, például contoso.azure-API.NET webhelyen, de beállíthatja úgy is [egyéni tartományok](../api-management/configure-custom-domain.md). A Logic Apps és a Service Bus is nyilvános IP-címet. Azonban a biztonság érdekében ez az architektúra korlátozza a hozzáférést az API Management csak az IP-címét, a Logic Apps-végpontok hívása. A Service Bus-hívások közös hozzáférésű jogosultságkód (SAS) védik.

- **Az Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) DNS-tartományok egy üzemeltetési szolgáltatás. Az Azure DNS névfeloldás biztosít a Microsoft Azure-infrastruktúra használatával. A tartományok Azure-ban üzemelteti, azonos hitelesítő adatokkal, API-kkal, eszközökkel és számlázási használata más Azure-szolgáltatások DNS-rekordok is kezelheti. Egyéni tartománynév, amilyen a contoso.com használatához hozzon létre az egyéni tartománynév leképezése az IP-cím DNS-rekordjait. További információkért lásd: [egyéni tartománynév beállítása az API Management](../api-management/configure-custom-domain.md).

- **Az Azure Active Directory (Azure AD)**: használhatja [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) vagy egy másik identitásszolgáltatót a hitelesítéshez. Az Azure AD hitelesítési API-végpontjainak eléréséhez átadásával biztosít egy [JSON Web Token az API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) ellenőrzése. A Standard és Premium szintű Azure ad-ben biztosíthat hozzáférést az API Management fejlesztői portálon.

## <a name="patterns"></a>Minták 

Ez az architektúra, amely az alapvető fontosságú a művelet néhány minták használja:

* Összetett API-k van építve a logic apps, amely szoftver hívásainak koordinálhatja a szoftverszolgáltatások (SaaS) rendszerek, Azure-szolgáltatások és minden API Management közzétett API-k használatával. Logikai alkalmazások is vannak [az API Management fejlesztői portálon keresztül közzétett](../api-management/import-logic-app-as-api.md).

* Az Azure AD használata az alkalmazások [az OAuth 2.0 biztonsági token beszerzése a](../api-management/api-management-howto-protect-backend-with-aad.md) , amely az API eléréséhez szükséges.

* Az Azure API Management [érvényesíti a biztonsági jogkivonatot](../api-management/api-management-howto-protect-backend-with-aad.md) , és ezután továbbítja a háttérrendszeri API-t vagy a logikai alkalmazás.

## <a name="recommendations"></a>Javaslatok

A konkrét követelmények eltérhetnek az ebben a cikkben leírt általános architektúrát. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="azure-api-management-tier"></a>Az Azure API Management-szint

Használja az API Management alapszintű, Standard vagy prémium szintű csomag esetében. Ezek a rétegek egy éles szolgáltatásiszint-szerződés (SLA) kínál, és támogatja a horizontális felskálázás az Azure-régión belül. Az egységek számát a díjcsomag szerint változik. A prémium szint is támogatja a horizontális felskálázás több Azure-régiók között. Válassza ki a réteg alapján a szolgáltatás beállítása és az átviteli sebességgel. További információkért lásd: [az API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).

Ha még futnak az összes API Management-példány díja. Ha rendelkezik vertikálisan, és nem kell szintű teljesítményre mindig, fontolja meg az API Management óradíjas kihasználásával, és vertikális leskálázás.

### <a name="logic-apps-pricing"></a>A Logic Apps díjszabása

Használja a Logic Apps egy [kiszolgáló nélküli](../logic-apps/logic-apps-serverless-overview.md) modell. A számlázás a műveleti és összekötőhöz végrehajtási alapján lesz kiszámítva. További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps/). Jelenleg nincs a Logic Apps szint szempontjai.

### <a name="logic-apps-for-asynchronous-api-calls"></a>A Logic Apps aszinkron API-hívások esetében

A Logic Apps optimális, amelyek nem igényelnek a közel valós idejű forgatókönyveket. Ha például a Logic Apps a leginkább aszinkron vagy félig hosszú ideig futó API-hívások. Ha kis késleltetésre szükség, a hívás, amely letiltja a felhasználói felületet, implementálhat például az API-t vagy a művelet a különböző technológiák használatával. Például használja az Azure Functions és a egy webes API-t úgy, hogy az Azure App Service használatával. Az API Management segítségével előtér-az API-t az API-fogyasztókat.

### <a name="region"></a>Régió

Hálózati késés minimalizálása érdekében válassza ki az API Management, a Logic Apps és a Service Bus ugyanabban a régióban. Általánosságban elmondható válassza ki a felhasználókhoz legközelebb eső régióban.

Az erőforráscsoport szintén van régiója. Ebben a régióban megadja a központi telepítési metaadatok tárolására és hol hajtsa végre a központi telepítési sablont. Üzembe helyezés során javítható a rendelkezésre állás, helyezze az erőforráscsoport és erőforrások ugyanabban a régióban.

## <a name="scalability"></a>Méretezhetőség

Adja hozzá az API Management szolgáltatás felügyelete a méretezhetőség növeléséhez [gyorsítótárazási házirendek](../api-management/api-management-howto-cache.md) ahol lehetséges. Gyorsítótárazás is segít csökkenteni a háttér-szolgáltatások.

A nagyobb kapacitást kínálnak, horizontális felskálázása az Azure API Management alapszintű, Standard és prémium szinten egy Azure-régióban. A szolgáltatás használata elemezhető a a **metrikák** menüben válassza a **kapacitás metrika** lehetőséget, és majd vertikális felskálázás vagy leskálázás megfelelő módon.

Az API Management-szolgáltatás méretezése javaslatok:

- Vegye figyelembe a forgalmi mintázatait, amikor a méretezést. Több ideiglenes forgalmat az ügyfelek nagyobb kapacitásra van szüksége.

- Összhangban kapacitásértéket, amely nagyobb, mint 66 % vertikális kell utalhat.

- Összhangban kapacitásértéket, amely a 20 %-os utalhat vertikális leskálázás lehetőséget.

- Mielőtt engedélyezné a terhelés, éles környezetben, mindig terhelési tesztek és egy tipikus terhelés mellett az API Management szolgáltatás.

Prémium szintű szolgáltatások ki lehet terjeszteni, több Azure-régiók között. Szolgáltatások felskálázásával több Azure-régióban telepít, magasabb SLA (99,95 % vagy 99,9 %-os) és üzembe helyezése szolgáltatások közelében felhasználók több régióban is kaphat.

A Logic Apps, kiszolgáló nélküli modell azt jelenti, hogy a rendszergazdák nem kell a szolgáltatás méretezhetősége tervezése. A szolgáltatás automatikusan méretezi magát, hogy megfeleljenek az igényeknek.

## <a name="availability"></a>Rendelkezésre állás

* Alapszintű, Standard és prémium szinten a szolgáltatásiszint-szerződés (SLA) az Azure API Management jelenleg 99,9 %-os. Prémium szintű konfigurációk telepítés, amelynek legalább egy egységet két vagy több régióban az SLA nem 99,95 %-os.

* Az SLA-t az Azure Logic Apps jelenleg 99,9 %-os.

### <a name="backups"></a>Biztonsági másolatok

Változás, rendszerességét alapján [rendszeresen készítsen biztonsági másolatot](../api-management/api-management-howto-disaster-recovery-backup-restore.md) az Azure API Management-konfigurációt. A biztonsági másolat Store egy helyet, vagy az Azure-régióban, ahol a szolgáltatás található eltér. Bármelyik beállítást is választja, a vész-helyreállítási stratégiát ezután választhatja:

* A vész-helyreállítási esemény egy új API Management-példány üzembe helyezése, az új példányt a biztonsági másolat visszaállítása és szolgáltatással átirányíthassa a DNS-rekordokat.

* Tartsa a szolgáltatás passzív másolat egy másik Azure-régióban, amely további költséget áll. Állítsa vissza rendszeresen biztonsági másolatokat a másolatot. Egy vész-helyreállítási esemény során a szolgáltatás helyreállításához meg kell csak szolgáltatással átirányíthassa a DNS-rekordokat.

Mivel gyorsan létrehozhatja logic apps, amelyek kiszolgáló nélküli, róluk biztonsági másolatot a társított Azure Resource Manager-sablon másolatának menti. Verziókövetési rendszerben is menti a sablonokat, és a folyamatos integráció/folyamatos készregyártás (CI/CD) folyamatot is integrálhatja a sablonokat.

Ha egy logikai alkalmazást az Azure API Management szolgáltatáson keresztül közzétett, és a logikai alkalmazás helyez át egy másik adatközpontba, frissítse a az alkalmazás helyét. Az API-k frissítheti **háttérrendszer** tulajdonság egy egyszerű PowerShell-parancsprogram használatával.

## <a name="manageability"></a>Kezelhetőségi

Hozzon létre külön erőforráscsoportok éles környezetben, fejlesztési, és tesztelési környezetek. Külön erőforráscsoportok megkönnyítik a központi telepítések felügyeletéhez szükséges, a tesztkörnyezetek törlését és a hozzáférési jogosultságok hozzárendelése.

Amikor erőforrásokat rendel erőforráscsoportok, vegye figyelembe a következőket:

* **Életciklus**: általában az ugyanabban az erőforráscsoportban az azonos életciklussal rendelkező erőforrásokat helyezze.

* **Hozzáférés**: csoportokban található erőforrások hozzáférési házirendeket alkalmazza, használhatja [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md).

* **Számlázási**: megtekintheti az erőforráscsoport összesítő költséget.

* **Az API Management tarifacsomag**: a fejlesztői tarifacsomag használatát a fejlesztési és tesztelési környezetek esetében. A költségek csökkentésére üzem előtti tesztelés során, egy replikát, az éles környezet üzembe helyezése, a tesztek futtatásához, és majd állítsa le.

További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

## <a name="deployment"></a>Környezet

* Az API Management és a Logic Apps-alkalmazások üzembe helyezéséhez használja a [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md). Sablonok egyszerűbbé automatizálhatja központi telepítések PowerShell vagy az Azure CLI használatával.

* Helyezze el az API Management és minden egyes logikai alkalmazás a saját külön Resource Manager-sablonok. Külön sablonok használatával tárolhatja verziókövetési rendszerekben az erőforrásokat. Ezután telepítheti ezeket a sablonokat együtt vagy külön-külön a folyamatos integráció/folyamatos készregyártás (CI/CD) folyamat részeként.

### <a name="versions"></a>Verziók

Minden alkalommal, amikor a logikai alkalmazás konfigurációjának módosítása, vagy a frissítést egy Resource Manager-sablon használatával helyezhet üzembe az Azure másolatot készít a kényelmi célokat szolgál, hogy verziót, és tartja a futtatási előzmények rendelkező összes verzió. Ezek a fájlok korábbi változásainak követése vagy aktuális konfigurációját a logikai alkalmazás egy verziójának támogatása is használhatja. Ha például hatékonyan állíthat vissza egy logikai alkalmazást.

Az Azure API Management rendelkezik, ezek különböző, de egymást kiegészítő [versioning fogalmak](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

* Adja meg az API-fogyasztókat, a funkció alapján saját igényeinek megfelelő, például az API-verziót választani, v1, v2, beta vagy éles-verziók

* Változatok, amelyek lehetővé teszik az API-rendszergazdák számára, hogy biztonságosan végezze el a módosításokat az API-k, illetve telepítheti ezeket a módosításokat nem kötelező mellékmondatokkal rendelkező felhasználók számára

Üzembe helyezés fontolja meg az API Management-változatok arra, hogy biztonságosan végezze el a módosításokat, tartsa módosítási előzményeit, és ezeket a módosításokat az API-fogyasztókat kommunikálni. Győződjön meg arról, egy változat fejlesztői környezetben, és ezt a módosítást, más környezetekben üzembe helyezése a Resource Manager-sablonok használatával.

Változatok használata API-k tesztelése előtt ezeket a módosításokat "aktuális" és a felhasználók számára elérhető, bár ez a módszer betöltése vagy integráció tesztelése nem ajánlott. Ehelyett használjon külön vizsgálat vagy üzem előtti környezetben.

### <a name="configuration-and-sensitive-information"></a>Konfiguráció és a bizalmas adatokat

Soha ne tároljon jelszavakat, hozzáférési kulcsokat és kapcsolati sztringekat a forrásvezérlőben. Ha ezekre az értékekre szükség, biztonságos, majd központilag telepítenie ezeket az értékeket a megfelelő módszerek. 

A Logic Apps Ha egy logikai alkalmazást igényel minden olyan bizalmas értéket, amely egy adott kapcsolattal nem hozható létre, tárolhatja ezeket az értékeket az Azure Key Vaultban, és hivatkozni tudjon rájuk a Resource Manager-sablonnal. Használja a központi telepítési sablon paramétereit és paraméterfájlt az egyes környezetekhez. További információkért lásd: [biztonságossá tétele a paramétereket és a egy munkafolyamaton belül bemenetek](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Az API Management objektumokban, úgynevezett használatával kezeli a titkos kulcsok *értékek nevű* vagy *tulajdonságok*. Ezek az objektumok tárolja az értékeket, amelyeket az API Management házirendek keresztül érheti el biztonságosan. További információkért lásd: [az API Management titkos kódok kezelése](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

Használhat [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) mindkét üzemi figyeléséhez [az API Management](../api-management/api-management-howto-use-azure-monitor.md) és [Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md). Az Azure Monitor nyújt információkat a metrikák minden szolgáltatáshoz beállítva az alapján, és alapértelmezés szerint engedélyezve van.

Minden szolgáltatás van még ezeket a beállításokat:

* Mélyebb elemzésre és dashboarding, a Logic Apps-naplókat elküldheti [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* Fejlesztési és üzemeltetési figyelés konfigurálhatja úgy az Azure Application Insights API-kezeléshez.

* Az API Management támogatja a [Power BI megoldássablon az egyéni API-analytics](http://aka.ms/apimpbi). Ez a megoldássablon használhatja a saját elemzési megoldás létrehozásához. Az üzleti felhasználók a Power BI jelentések elérhetővé teszi.

## <a name="security"></a>Biztonság

Bár ez a lista nem teljes mértékben ismerteti az összes ajánlott biztonsági eljárások, az alábbiakban néhány biztonsági szempontot, amelyek kifejezetten az Azure-szolgáltatások üzembe helyezve, az ebben a cikkben leírt architektúra a alkalmazni:

* Ahhoz, hogy a felhasználók rendelkeznek a megfelelő hozzáférési szintekkel, a szerepköralapú hozzáférés-vezérlés (RBAC) használja.

* Biztonságos nyilvános, az API Management API-végpontokat, OAuth vagy OpenID Connect használatával. A biztonságos nyilvános API-végpontokat, egy identitásszolgáltató konfigurálása, és adjon hozzá egy JSON webes jogkivonat (JWT) érvényesség-ellenőrzési szabályzat.

* Csatlakoztassa a háttérszolgáltatások az API Management kölcsönös tanúsítványok használatával.

* Biztonságos HTTP-eseményindító-alapú logikai alkalmazások létrehozásával az IP cím-engedélyezési lista, amely az API Management IP-címre mutat. Egy engedélyezési listához hozzáadni kívánt IP-cím megakadályozza, hogy a logikai alkalmazás hívása a nyilvános interneten keresztül az API Management első áthaladás nélkül.

## <a name="next-steps"></a>További lépések

* Ismerje meg [várólisták és események vállalati integráció](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
