---
title: Az Azure integrációs szolgáltatások - egyszerű vállalati integrációs
description: Bemutatja, hogyan valósítja meg az Azure Logic Apps és az Azure API Management tartalmazó egyszerű vállalati integrációs mintát referencia-architektúrában
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232351"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Egyszerű vállalati integrációs - referencia-architektúrában

## <a name="overview"></a>Áttekintés

A referencia-architektúrában bevált gyakorlat az integráció alkalmazás által használt Azure integrációs szolgáltatások készletként jeleníti meg. Ez az architektúra ezek alapján HTTP API-k, a munkafolyamat és a vezénylési igénylő számos különböző alkalmazás minták lehetnek.

*Sok lehetséges alkalmazások integrációs technológia, egyszerű pont közötti pont-alkalmazás a teljes vállalati service Bus vannak. Az architektúra adatsorozat meghatározza a újrafelhasználható összetevő kijelzők bármely integrációs alkalmazások készítéséhez – a fejlesztők gondolja át az alkalmazásaikat és infrastruktúrájukat a rendszer szükséges összetevőket.*

   ![Architektúra diagramja – egyszerű vállalati integrációs](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architektúra

Az architektúra a következő összetevőkből áll:

- Erőforráscsoport. Egy erőforráscsoportot az Azure-erőforrások logikai tárolója.
- Az Azure API Management. Az Azure API Management az egy teljes körűen felügyelt platform közzétételéhez, biztonságossá tétele és átalakítása HTTP API-k.
- Az Azure API Management fejlesztői portálján. A fejlesztői portálra visszatérve adjon hozzáférést a dokumentáció, Kódminták és tesztelheti az API-k olyan Azure API Management-példányokhoz tartalmaz.
- Az Azure Logic Apps. A Logic Apps szolgáltatás egy kiszolgáló nélküli platform vállalati munkafolyamat és integrációs készítéséhez.
- Összekötők. Összekötők Logic Apps a gyakran használt szolgáltatásokhoz használják. A Logic Apps már van másik összekötők több száz, de ezek is létrehozhat egyéni összekötő használatával.
- IP-címet. Az Azure API Management szolgáltatás egy rögzített nyilvános IP-cím és tartománynév rendelkezik. A tartománynév azure-api.net, például a contoso altartománya. Azure-api.net. A Logic Apps is rendelkezik egy nyilvános IP-címet; azonban ebben az architektúrában azt hozzáférés korlátozása hívásához használandó Logic apps végpontok csak az IP-címet a API kezelése (biztonság).
- Az Azure DNS. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. Használhat egy egyéni tartománynevet (például contoso.com) hozzon létre DNS-rekordokat, amelyek kapcsolódnak az egyéni tartománynevet az IP-címet. További információkért lásd: Configure Azure API Management egy egyéni tartománynevet.
- Azure Active Directory (Azure AD). A hitelesítéshez használja az Azure AD-t vagy egy másik identitásszolgáltatót. Az Azure AD hozzáférési API végpontokhoz történő hitelesítést biztosít (úgy, hogy a JSON Web Token az API Management ellenőrzése), és biztonságossá teheti az API Management fejlesztői portálján (Standard és Premium rétegek csak) elérésére.

Ez az architektúra rendelkezik néhány alapvető minták működését:

1. HTTP API-k segítségével az API Management fejlesztői portálján, amely lehetővé teszi a fejlesztők számára közzétett létező háttérrendszerek (vagy belső a szervezet, külső vagy mindkettő) ezen API-k hívásainak integrálja alkalmazások.
2. Összetett API-k használatával Logic Apps; készített hívások SAAS rendszereken futó, Azure-szolgáltatásokat és minden API-k közzé az API Management. A Logic Apps is közzé az API Management Developer portálon.
3. Alkalmazások szerezni az OAuth 2.0 biztonsági jogkivonat-szükséges az API-k használata az Azure Active Directory elérésekor.
4. Az Azure API Management érvényesíti a biztonsági jogkivonatot, és továbbítja a kérelmet a háttérkiszolgálón API vagy logikai alkalmazást.

## <a name="recommendations"></a>Javaslatok

Az Ön követelményei eltérhetnek az itt leírt architektúrától. A jelen szakaszban leírt javaslatokat tekintse kiindulópontnak.

### <a name="azure-api-management-tier"></a>Az Azure API Management réteg

Használja a Basic, Standard vagy prémium tiers, mivel egy éles SLA kínálnak, illetve támogatja a kibővített belül az Azure-régió (egységek száma eltérő szint). Prémium csomagban kibővített is támogatja több Azure-régiók között. A réteg a szükséges átviteli szintű választott kiinduló- és szolgáltatáskészleteket. További információkért lásd: [API Management árképzési](https://azure.microsoft.com/pricing/details/api-management/).

Van szó, a példányainak API Management amikor futnak. Ha rendelkezik-e kiterjesztett, és nincs szüksége ekkora szintű teljesítményre mindig, fontolja meg az API Management óránkénti számlázási és a skála.

### <a name="logic-apps-pricing"></a>A Logic Apps díjszabása

A Logic Apps működik, mint egy [kiszolgáló nélküli](logic-apps-serverless-overview.md) modell – számlázási kiszámítja végrehajtásának műveletet és összekötőt. Lásd: [Logic Apps árképzési](https://azure.microsoft.com/pricing/details/logic-apps/) további információt. Jelenleg nincs a Logic Apps réteg szempontjai.

### <a name="logic-apps-for-asynchronous-api-calls"></a>A Logic Apps aszinkron API-hívások

A Logic Apps működik a legjobban forgatókönyvek, amelyek nem igényelnek a kis késleltetésű – pl. aszinkron vagy félstatikus-hosszú futású API-hívásokat. Ha kis késleltetésű szükség (pl. egy hívás, amely megakadályozza a felhasználói felület) azt javasoljuk, hogy API vagy egy másik technológiával, pl. az Azure Functions vagy webes API App Service használatával telepített művelet végrehajtására. Mégis azt javasoljuk, hogy kell-e ez az API fronted API Management használata API fogyasztók számára.

### <a name="region"></a>Régió

Az API Management és a Logic Apps ugyanabban a régióban hálózati késés csökkentése érdekében érdemes kiépítéséhez. Általában érdemes a felhasználókhoz legközelebbi régiót választani.

Az erőforráscsoport is rendelkezik egy régiót, amely megadja a központi telepítési metaadatok tárolására, és ha a központi telepítési sablont a végrehajtása. Az erőforráscsoportot és a hozzá tartozó erőforrásokat helyezze ugyanabba a régióba. Ez javíthatja a rendelkezésre állást az üzembe helyezés során.

## <a name="scalability-considerations"></a>Méretezési szempontok

Az API Management rendszergazdák hozzá kell adnia [házirendek gyorsítótárazás](../api-management/api-management-howto-cache.md) adott esetben a szolgáltatás a méretezhetőség javítása és a háttér-szolgáltatások terhelését.

Az Azure API Management alapszintű, Standard és Premium rétegek kiterjeszthető a nagyobb kapacitást kínálnak az Azure-régióban. Rendszergazdák a kapacitás metrika belül a metrikák menü segítségével elemezheti a szolgáltatás használatát, és növelheti vagy csökkentheti szükség szerint.

Az API Management szolgáltatás méretezéshez ajánlásokat:

- Skálázás igényeinek figyelembevételével forgalmi minták – több "volatile" forgalmi minták rendelkező ügyfelek megnövekedett kapacitás nagyobb szükségességét fog rendelkezni.
- Egységes kapacitás 66 % feletti vertikális felskálázás kell utalhat.
- Egységes kapacitás 20 % alatt jelezheti csökkentheti lehetőséget.
- Mindig ajánlott betöltése a API-kezelés szolgáltatás a reprezentatív terhelés tesztelése éles környezetben engedélyezése előtt.

Prémium szint szolgáltatások kiterjeszthető több Azure-régiók között. Ily módon történő telepítése ügyfelek érhetnek el egy magasabb SLA-t (99,9 %-os figyelésekor 99,95 %), valamint építhető ki több régióba felhasználók közel szolgáltatások.

A Logic Apps kiszolgáló nélküli modell azt jelenti, hogy rendszergazdáknak nem kell további figyelmet szolgáltatás méretezhetőségre; hogy a szolgáltatás automatikusan méretezi igény kielégítéséhez.

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Írásának időpontjában a szolgáltatásiszint-szerződéssel (SLA) az Azure API Management 99,9 %-os Basic, Standard és Premium rétege számára. Prémium szint konfigurációhoz telepítés két vagy több régióban legalább egy egység van szolgáltatásiszint-szerződésben garantált 99,95 %-os.

Írásának időpontjában a szolgáltatásiszint-szerződés (SLA) Azure Logic Apps 99,9 %-os.

### <a name="backups"></a>Biztonsági másolatok

Az Azure API Management a konfiguráció nem lehet [rendszeresen végeznek biztonsági mentést](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (megfelelően a változás rendszerességét alapján), és egy helyet, vagy másik, ahol a szolgáltatás található a Azure-régióban található biztonsági mentési fájlok. Az ügyfelek kiválaszthatja a vész-Helyreállítási stratégia két lehetőség közül:

1. A vész-Helyreállítási esetben egy új API Management-példány lett kiépítve, a biztonsági másolatot visszaállítja azt, és DNS-rekordok vannak repointed.
2. Passzív másolat a szolgáltatás egy másik Azure-régiót (nélül további költség nélkül) biztonsági mentései során is garantálják az ügyfelek rendszeresen állítja vissza azt. A vész-Helyreállítási esetben csak a DNS-rekordokat kell repointed a szolgáltatást.

A Logic Apps nagyon gyorsan létrehozhatók és kiszolgáló nélküli, hogy biztonsági másolatot a társított Azure Resource Manager-sablon másolatának mentése. Ezek is menthető forrás-vezérlő/integrálva a felhasználók folyamatos integráció/folyamatos (CI/CD) telepítési folyamat.

A Logic Apps az API Management keresztül közzétett helyükre frissíteni kell azokat helyezze át egy másik adatközpont. Ez az API háttéralkalmazás tulajdonság egy egyszerű PowerShell-parancsprogram használatával valósítható meg.

## <a name="manageability-considerations"></a>Felügyeleti szempontok

Termelési környezetben, fejlesztési, külön erőforráscsoportokat létrehozni, és tesztelési környezetben. Ez megkönnyíti az üzemelő példányok felügyeletét, a tesztkörnyezetek törlését és a hozzáférési jogok kiosztását.
Amikor erőforrásokat rendel az erőforráscsoportokhoz, vegye figyelembe a következőket:

- Életciklus. Általában érdemes az azonos életciklusú erőforrásokat ugyanabba az erőforráscsoportba helyezni.
- Hozzáférés. Használhat [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) hozzáférési házirendek alkalmazása az erőforrások csoportba.
- Számlázás. Megtekintheti az erőforráscsoport összegzett költségeit.
- Tarifacsomag kiválasztása az API Management-használatát javasoljuk fejlesztői réteg fejlesztési és tesztkörnyezetek. Üzem előtti javasoljuk az éles környezet replika telepítése tesztek futtatása, és a költségek minimalizálása érdekében leáll.

További információkért lásd: [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) áttekintése.

### <a name="deployment"></a>Környezet

Azt javasoljuk, hogy használjon [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md) központi telepítése az Azure API Management és az Azure Logic Apps alkalmazásokat is. Sablonok megkönnyítik a PowerShell vagy az Azure parancssori felület (CLI) használatával központi telepítések automatizálásához.

Azt javasoljuk, hogy a saját külön Resource Manager-sablonok az Azure API Management és minden egyes Logic Apps üzembe. Ez lehetővé teszi a forrásrendszerek vezérlő tárolja őket. Ezek a sablonok majd együtt vagy külön-külön a folyamatos integrációs/folyamatos (CI/CD) központi telepítési folyamat részeként telepíthető.

### <a name="versions"></a>Verziók

Minden alkalommal, amikor a beállítások logikai alkalmazás módosítsa (vagy egy frissítést a Resource Manager sablon segítségével), verzió egy példányát az Ön kényelme (futtatási előzmények rendelkező összes verzió folyamatosan) tárolódik. Ezen verziói segítségével nyomon követheti a korábbi módosítások, és is támogatja, a verzió a logic app; aktuális konfigurációja Ez azt jelenti, is hatékonyan visszaállítási logikai alkalmazás, például.

Az API Management két különálló (de udvarias) tartozik [versioning fogalmak](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- A kiválaszthatja, hogy az API-t használják ki volt a API fogyasztók használt verziók igényeiknek (pl. v1, v2 vagy beta, éles) alapján.
- A változatok API rendszergazdái biztonságosan módosíthatja az API-k és telepíthet a felhasználók számára a választható magyarázatokkal.

A környezetben a központi telepítés – változatok módosításokat biztonságosan, úgy kell tekinteni az API Management módosítási előzményeit, és API fogyasztók tudomást ezeket a módosításokat. Egy változat fejlesztői környezetben létrehozott és telepített más Resource Manager-sablonok segítségével különböző környezetek között lehet.

Változatok amellyel tesztelheti az API-k előtt az "aktuális", és elérhetővé a felhasználók számára, miközben betöltés vagy integráció tesztelése a mechanizmus használatával nem ajánlott – külön teszt- vagy éles üzem előtti környezetben kell használni helyette.

### <a name="configuration"></a>Konfiguráció

Jelszavak, hívóbetűk vagy a verziókövetési kapcsolat karakterláncait keresésének mellőzése. Ha van szükség a megfelelő módszer segítségével telepítheti és biztonságos ezeket az értékeket. 

A Logic Apps minden olyan bizalmas értéket a logikai alkalmazásban (azaz nem hozható létre kapcsolat formájában) szükséges legyen tárolva az Azure Key Vault, és a Resource Manager-sablon hivatkozik. Emellett javasoljuk, hogy használja a központi telepítési sablon paraméterek, valamint paraméterfájlt minden környezetben. További útmutatást [biztonságossá tétele a paraméterek és egy munkafolyamat bemenetei](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Az API Management titkos kulcsok kezelése objektumokban, úgynevezett nevű értékek/tulajdonságok használatával. Ezek biztonságosan tárolni értékek, amelyek elérhetők az API-felügyeleti házirendek. Lásd: hogyan [az API Management titkos kulcsok kezelése](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnosztika és figyelés

Mindkét [API Management](../api-management/api-management-howto-use-azure-monitor.md) és [Logic Apps](logic-apps-monitor-your-logic-apps.md) keresztül működési figyelésére alkalmas [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure a figyelő alapértelmezés szerint engedélyezve van, és a beállítást minden egyes szolgáltatáshoz más metrikák alapján tájékoztatást fogunk adni.

Emellett lehetőség áll rendelkezésre további az egyes szolgáltatásokhoz:

- Logic Apps naplók küldhető [Naplóelemzési](logic-apps-monitor-your-logic-apps-oms.md) mélyebb elemzés és dashboarding.
- Az API Management fejlesztői Ops figyelés konfigurálása az Application Insights támogatja.
- Az API Management támogatja a [Power BI Megoldássablon az egyéni API használatával](http://aka.ms/apimpbi). Ez a megoldás sablon lehetővé teszi a felhasználóknak a saját egyéni elemzési megoldások az üzleti felhasználók számára a Power bi-ban elérhető jelentések létrehozásához.

## <a name="security-considerations"></a>Biztonsági szempontok

Ez a rész felsorolja a biztonsági szempontok adott Azure-szolgáltatás a cikkben, az architektúra foglaltak alapján telepítették. Nem tartalmazza az összes ajánlott biztonsági eljárást.

- Szerepköralapú hozzáférés-vezérlést (RBAC) használatával biztosíthatja a megfelelő szintű hozzáféréssel a felhasználók számára.
- Biztonságos nyilvános API-végpontok az API Management OAuth/megnyitása IDConnect használatával. Ehhez az identitásszolgáltató konfigurálása, és vegye fel a JWT érvényesség-ellenőrzési házirend.
- Háttér-szolgáltatások csatlakozni az API Management-tanúsítványok kölcsönös használatával
- Biztonságos HTTP eseményindító-alapú Logic Apps hozzon létre egy IP-cím engedélyezett, az IP-címet a API kezelése mutat. Ez megakadályozza, hogy a logikai alkalmazást hívja a nyilvános interneten keresztül. az API Management első áthaladás nélkül.

A referencia-architektúrában bemutatta, hogyan hozhat létre egy egyszerű vállalati integrációs platformján Azure integrációs szolgáltatásainak használatával.

## <a name="next-steps"></a>További lépések

* [Vállalati integrációs várólisták és eseményekkel](logic-apps-architectures-enterprise-integration-with-queues-events.md)
