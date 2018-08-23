---
title: Az Azure integrációs szolgáltatások egyszerű vállalati integráció referenciaarchitektúra
description: A referenciaarchitektúra bemutatja, hogyan megvalósítása az Azure Logic Apps és az Azure API Management egyszerű vállalati integrációs minta ismerteti.
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
ms.openlocfilehash: f73a9e59c0add664128b506172182afe566ca670
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444510"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Referenciaarchitektúra: egyszerű vállalati integráció

A következő referencia-architektúra, amely egy adatintegrációs alkalmazás által használt Azure integrációs szolgáltatások alkalmazhat bevált eljárásokat mutat be. Az architektúra számos különböző alkalmazásminták HTTP API-k, a munkafolyamat és a koordinálást igénylő alapjául szolgálhatnak.

![Architekturális diagramja – egyszerű vállalati integráció](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*Az integrációs technológiával számos lehetséges alkalmazások is vannak. Ezek között egy egyszerű, pont-pont típusú alkalmazást a teljes nagyvállalati Azure Service Bus-alkalmazás. Az architektúra sorozat azt írja le, az újrafelhasználható összetevőit, előfordulhat, hogy egy általános integrációs alkalmazások összeállítására vonatkoznak. Architects figyelembe kell venni, hogy melyik azok alkalmazása és az infrastruktúra megvalósításához szükséges összetevőket.*

## <a name="architecture"></a>Architektúra

Az architektúra a következő összetevőkből áll:

- **Erőforráscsoport**. Az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az Azure-erőforrások logikai tárolója.
- **Az Azure API Management**. [Az API Management](https://docs.microsoft.com/azure/api-management/) egy teljes körűen felügyelt platform, amellyel közzététele, biztosítása és a HTTP API-jai átalakíthatók.
- **Az Azure API Management fejlesztői portálon**. Az Azure API Management minden példányának együttműködik a hozzáférést a [fejlesztői portál](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Az API Management fejlesztői portálon hozzáférést biztosít a dokumentációhoz és kódmintákhoz mintákhoz. Tesztelheti az API-k a fejlesztői portálon.
- **Az Azure Logic Apps**. [A Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) egy kiszolgáló nélküli platform, amellyel vállalati munkafolyamat és integrációs hozhat létre.
- **Összekötők**. Használja a Logic Apps [összekötők](https://docs.microsoft.com/azure/connectors/apis-list) szeretne csatlakozni a gyakran használt szolgáltatásokat. A Logic Apps már rendelkezik a különböző összekötők több száz, de létrehozhat egy egyéni összekötőt is.
- **IP-cím**. Az Azure API Management szolgáltatás rendelkezik egy nyilvános [IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) és a egy tartomány nevét. Az alapértelmezett tartománynevet altartománya API.NET-webhelyen, azure, például a contoso.azure-API.NET webhelyen, de [egyéni tartományok](https://docs.microsoft.com/azure/api-management/configure-custom-domain) is konfigurálható. A Logic Apps és a Service Bus is nyilvános IP-címet. Azonban ebben az architektúrában azt korlátozza a hozzáférést a Logic Apps-végpontok csak IP-címet az API Management hívása (a biztonság). A Service Bus-hívások közös hozzáférésű jogosultságkód (SAS) védik.
- **Azure DNS**. [Az Azure DNS](https://docs.microsoft.com/azure/dns/) DNS-tartományok egy üzemeltetési szolgáltatás. Az Azure DNS névfeloldás biztosít a Microsoft Azure-infrastruktúra használatával. A tartományok Azure-ban üzemelteti, azonos hitelesítő adatokkal, API-kkal, eszközökkel és számlázási használata más Azure-szolgáltatások DNS-rekordok is kezelheti. Egyéni tartománynév például a contoso.com használatához hozzon létre az egyéni tartománynév leképezése az IP-cím DNS-rekordjait. További információkért lásd: [egyéni tartománynév beállítása az API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Használat [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) vagy egy másik identitásszolgáltatót a hitelesítéshez. Az Azure AD hitelesítési API-végpontjainak eléréséhez átadásával biztosít egy [JSON Web Token az API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) ellenőrzése. Az Azure AD biztosíthat hozzáférést az API Management fejlesztői portálon (Standard és prémium szinten csak).

Az architektúra rendelkezik néhány alapvető működéséhez a mintákat:

- Összetett API-k van építve a logic apps használatával. Ezek koordinálása a szoftvereket, mint a szoftverszolgáltatások (SaaS) rendszerek, az Azure-szolgáltatások és minden API Management közzétett API-hívások. [A Logic apps is közzétett](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) az API Management fejlesztői portálon keresztül.
- Alkalmazások használata az Azure ad-vel [az OAuth 2.0 biztonsági jogkivonat beszerzése](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , amely szükséges API-k eléréséhez.
- Az Azure API Management [érvényesíti a biztonsági jogkivonatot](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , és ezután továbbítja a háttérrendszeri API-t vagy a logikai alkalmazás.

## <a name="recommendations"></a>Javaslatok

Az adott igények szerint ebben a cikkben leírt általános architektúra eltérhetnek. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="azure-api-management-tier"></a>Az Azure API Management-szint

Használja az API Management alapszintű, Standard vagy prémium szintű csomag esetében. A rétegek egy éles szolgáltatásiszint-szerződés (SLA) kínál, és támogatja a horizontális felskálázás az Azure-régióban (szint szerint változó az egységek számát). A prémium szint is támogatja a horizontális felskálázás több Azure-régiók között. A réteg úgy dönt, hogy a szükséges átviteli sebességtől szint alapján, és a szolgáltatás beállítása. További információkért lásd: [az API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).

Ha még futnak az összes API Management-példány díja. Ha rendelkezik vertikálisan, és nem kell szintű teljesítményre mindig, fontolja meg az API Management óradíjas kihasználásával, és vertikális leskálázás.

### <a name="logic-apps-pricing"></a>A Logic Apps díjszabása

Használja a Logic Apps egy [kiszolgáló nélküli](logic-apps-serverless-overview.md) modell. A számlázás a műveleti és összekötőhöz végrehajtási alapján lesz kiszámítva. További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps/). Jelenleg nincs a Logic Apps szint szempontjai.

### <a name="logic-apps-for-asynchronous-api-calls"></a>A Logic Apps aszinkron API-hívások esetében

A Logic Apps optimális, amelyek nem igényelnek a közel valós idejű forgatókönyveket. Például, hogy a legjobban aszinkron vagy félig hosszú ideig futó API-hívások. Ha kis késleltetésre szükség (például a hívás, amely letiltja a felhasználói felület), azt javasoljuk, hogy API-t vagy a művelet végrehajtása a különböző technológiák használatával. Például használja az Azure Functions és a egy webes API-t úgy, hogy az Azure App Service használatával. Továbbra is javasoljuk, hogy az API az API-fogyasztókat előtér-e az API Management segítségével.

### <a name="region"></a>Régió

Az API Management üzembe helyezése és Logic Apps ugyanabban a régióban a hálózati késés minimalizálása érdekében. Általánosságban elmondható válassza ki a felhasználókhoz legközelebb eső régióban.

Az erőforráscsoport szintén van régiója. A régió központi telepítési metaadatok tárolódnak, és ahol hajt végre a központi telepítési sablont a adja meg. Helyezze el az erőforráscsoportot és az erőforrások üzembe helyezése során a rendelkezésre állás növelése érdekében ugyanabban a régióban.

## <a name="scalability"></a>Méretezhetőség

Az API Management-rendszergazdák hozzá kell adnia [gyorsítótárazási házirendek](../api-management/api-management-howto-cache.md) adott esetben a bővítése érdekében a szolgáltatás. Gyorsítótárazás is segít csökkenteni a háttér-szolgáltatások.

Az Azure API Management alapszintű, Standard és prémium szintjeinek kiterjeszthető az Azure-régióban a nagyobb kapacitást kínálnak. A rendszergazdák használhatják a **kapacitás metrika** beállítást a **metrikák** menüre, és fokozatosan növelheti szolgáltatásának használata elemezhető és majd vertikális felskálázás vagy leskálázás megfelelő módon.

Az API Management-szolgáltatás méretezése javaslatok:

- Skálázási igények figyelembe forgalmi minták. Több ideiglenes forgalmat rendelkező ügyfelek nincs megnövelt kapacitás nagyobb szüksége.
- A 66 % feletti konzisztens kapacitás vertikális kell utalhat.
- 20 % alatti konzisztens kapacitás utalhat vertikális leskálázás lehetőséget.
- Minden esetben javasoljuk, hogy terhelési tesztek és egy tipikus terhelés mellett az API Management szolgáltatás éles környezetben a betöltés engedélyezése előtt.

Prémium szintű szolgáltatások kiterjeszthető több Azure-régiók között. Az üzembe helyezett szolgáltatások felskálázásával több Azure-régióban magasabb SLA (99,95 % vagy 99,9 %-os) szerezhet, és a szolgáltatások közelében felhasználók több régióban is üzembe.

A Logic Apps, kiszolgáló nélküli modell azt jelenti, hogy a rendszergazdák nem kell terveznie a szolgáltatás méretezhetősége. A szolgáltatás automatikusan méretezi magát, hogy megfeleljenek az igényeknek.

## <a name="availability"></a>Rendelkezésre állás

Az SLA-t az Azure API Management jelenleg az alapszintű, Standard és prémium szint esetében 99,9 %-os. Prémium szintű konfigurációk legalább egy egység két vagy több régióban üzembe helyezéssel rendelkezik 99,95 %-os SLA-t.

Az SLA-t az Azure Logic Apps jelenleg 99,9 %-os.

### <a name="backups"></a>Biztonsági másolatok

Az Azure API Management-beállításokat kell megadni [rendszeresen biztonsági másolat](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (változás rendszerességét alapján). Egy helyet, vagy az Azure-régióban, ahol a szolgáltatás található eltér a biztonságimásolat-fájlokat kell tárolni. Ügyfelek ezután választhat a vész-helyreállítási stratégiát a két lehetőség egyikét:

- A vész-helyreállítási esemény egy új API Management-példány üzembe van helyezve, az új példány visszaállítja a biztonsági mentés és DNS-rekordok repointed vannak.
- Ügyfelek ne passzív másolat a szolgáltatás egy másik Azure-régiót (költségekkel jár további). Biztonsági másolatok rendszeresen állítja vissza a példányt. A vész-helyreállítási esemény csak a DNS-rekordokat kell repointed visszaállítani a szolgáltatás.

A logic apps újra gyorsan létrehozhatók és kiszolgáló nélküli, mert azok készül biztonsági másolat által a társított Azure Resource Manager-sablon másolatának mentése. Sablonok is menthető a forráskezelőhöz, és azok nem integrálhatók az ügyfél folyamatos integráció/folyamatos készregyártás (CI/CD) folyamatot.

Ha egy logikai alkalmazást, amely az API Management szolgáltatáson keresztül közzétett helyez át egy másik adatközpontba, frissítse az alkalmazás helyét. Frissíteni az alkalmazás helyét, egy egyszerű PowerShell-parancsprogram használatával frissítse a **háttérrendszer** tulajdonsága az API-t.

## <a name="manageability"></a>Kezelhetőségi

Hozzon létre külön erőforráscsoportok éles környezetben, fejlesztési, és tesztelési környezetek. Külön erőforráscsoportok használata megkönnyíti központi telepítések felügyeletéhez szükséges, a tesztkörnyezetek törlését és a hozzáférési jogosultságok hozzárendelése.

Amikor erőforrásokat rendel erőforráscsoportok, a következő tényezőket kell figyelembe venni:

- **Életciklus**. Általánosságban elmondható helyezze ugyanabba az erőforráscsoportba az azonos életciklussal rendelkező erőforrások.
- **Hozzáférés**. Használhat [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) a csoportokban található erőforrások hozzáférési szabályzatok alkalmazásához.
- **Számlázási**. Megtekintheti az erőforráscsoport költségeinek összesítése.
- **Az API Management tarifacsomag**. Azt javasoljuk, hogy a fejlesztői csomag fejlesztési és tesztelési környezetek. Az üzem előtti környezet azt javasoljuk egy replikát, az éles környezet üzembe helyezése tesztek futtatása, és minimalizálja a költségeket a leállítása.

További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).

### <a name="deployment"></a>Környezet

Javasoljuk, hogy használjon [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md) az API Management és a Logic Apps-alkalmazások üzembe helyezése. Sablonok megkönnyítik a PowerShell vagy az Azure CLI-vel üzembe helyezések automatizálását.

Azt javasoljuk, hogy a saját, elkülönített Resource Manager-sablonok az Azure API Management és az egyes logikai alkalmazások üzembe. Amikor külön tanúsítványsablonokat, verziókövetési rendszerekben tárolhatja az erőforrásokat. Az erőforrások együtt vagy külön-külön egy CI/CD folyamatot részeként is telepítheti.

### <a name="versions"></a>Verziók

Minden alkalommal, amikor egy konfigurációs győződjön meg arról, módosítsa a logikai alkalmazás (vagy egy Resource Manager-sablon segítségével frissítést telepíteni), azt a verziót másolatát adatért a felhasználók kényelme érdekében (futtatási előzmények rendelkező összes verzió tartanak). Ezek a fájlok korábbi változásainak követése és a egy verziót kell a logikai alkalmazás a jelenlegi konfiguráció támogatására használhatja. Ha például hatékonyan állíthat vissza egy logikai alkalmazást.

Az API Management rendelkezik két különböző (de díjtalan) [versioning fogalmak](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Adja meg az API-fogyasztókat alkalmazásmodelleknek köszönhetően az API-t is használnak (például v1, v2 vagy a bétaverzió, éles környezetben) saját igényeik szerint használt verziók.
- API-rendszergazdák számára, hogy biztonságosan módosítások végrehajtása API-khoz és majd érvénybe léptetése a módosításokat az opcionális mellékmondatokkal változatok.

Üzembe helyezési kontextusában célszerű fontolja meg az API Management-változatok arra, hogy biztonságosan végezze el a módosításokat, módosítási előzményeit, és az API-fogyasztókat figyelembe ezeket a módosításokat. A fejlesztői környezetben hozható létre, valamint üzembe helyezhetők más környezetek használatával a Resource Manager-sablonok között.

Bár a változatok használatával az API tesztelése előtt, győződjön meg arról, hogy "aktuális" és a felhasználók, a terhelés vagy integrációs tesztelés a mechanizmus használatával nem ajánlott. Ehelyett használjon külön vizsgálat vagy üzem előtti környezetben.

### <a name="configuration"></a>Konfiguráció

Soha ne ellenőrizze a jelszavakat, hozzáférési kulcsokat és kapcsolati karakterláncokat a forráskezelőhöz. Ha ezekre az értékekre szükség, használja a megfelelő módszer üzembe helyezéséhez és biztonságos ezeket az értékeket. 

A Logic Apps esetében minden bizalmas, a logikai alkalmazás (amelyek nem hozható létre kapcsolat formájában) szükséges értékeket kell az Azure Key Vaultban tárolt és a Resource Manager-sablon hivatkozik. Emellett javasoljuk, hogy minden környezethez központi telepítési sablon paramétereit és alkalmazásparaméter-fájlokat használ. További információkért lásd: [biztonságossá tétele a paramétereket és a egy munkafolyamaton belül bemenetek](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Az API Management szolgáltatásban objektumokban, úgynevezett használatával felügyelt titkos kulcsok *értékek nevű* vagy *tulajdonságok*. Az objektumok tárolja biztonságosan elérhető értékek az API Management házirendek. További információkért lásd: [az API Management titkos kódok kezelése](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

[Az API Management](../api-management/api-management-howto-use-azure-monitor.md) és [Logic Apps](logic-apps-monitor-your-logic-apps.md) egyaránt támogatja a műveletek figyelése keresztül [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure Monitor információk alapján, hogy az egyes szolgáltatások vannak konfigurálva. Az Azure Monitor alapértelmezés szerint engedélyezve van.

Ezek a beállítások is elérhetők az egyes szolgáltatásokhoz:

- Logic Apps-naplók elküldött [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) mélyebb elemzésre és dashboarding.
- Az API Management konfigurálása az Azure Application Insights támogatja a DevOps-figyeléshez.
- Az API Management támogatja a [Power BI megoldássablon az egyéni API-analytics](http://aka.ms/apimpbi). Ügyfelek használhatják a megoldássablon saját egyéni elemzési megoldás létrehozásához. Jelentések érhetők el a Power bi-ban az üzleti felhasználóknak.

## <a name="security"></a>Biztonság

Ez a szakasz biztonsági szempontokat ismerteti, amelyek adott Azure-szolgáltatásokra, amely ebben a cikkben ismertetett, és amely vannak üzembe helyezve az architektúra leírtak szerint. Nem tartalmazza az összes ajánlott biztonsági eljárást.

- Szerepköralapú hozzáférés-vezérlés (RBAC) segítségével győződjön meg, hogy megfelelő szintű hozzáféréssel a felhasználók számára.
- Tegye biztonságossá a nyilvános, az API Management API-végpontokon OAuth és OpenID Connect használatával. A biztonságos nyilvános API-végpontokat, egy identitásszolgáltató konfigurálása, és adjon hozzá egy JSON webes jogkivonat (JWT) érvényesség-ellenőrzési szabályzat.
- Csatlakoztassa a háttérszolgáltatások az API Management kölcsönös tanúsítványok használatával.
- Biztonságos HTTP-eseményindító-alapú logikai alkalmazások létrehozásával az IP cím-engedélyezési lista, amely az API Management IP-címre mutat. Egy engedélyezési listához hozzáadni kívánt IP-cím megakadályozza, hogy a logikai alkalmazás hívása a nyilvános interneten keresztül az API Management első áthaladás nélkül.

## <a name="next-steps"></a>További lépések

- Ismerje meg [várólisták és események vállalati integráció](logic-apps-architectures-enterprise-integration-with-queues-events.md).
