---
title: A Microsoft Graph Security & biztonsági műveletek integrálása és kezelése
description: Javítsa az alkalmazás fenyegetésvédelmi, észlelési és válaszát a Microsoft Graph Security & Az Azure Logic Apps segítségével
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598833"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>A fenyegetések elleni védelem javítása a biztonsági műveletek nek a Microsoft Graph Security & az Azure Logic Apps alkalmazásokkal való integrálásával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Microsoft Graph [Security](https://docs.microsoft.com/graph/security-concept-overview) összekötő segítségével a Microsoft biztonsági termékeinek, szolgáltatásainak és partnereinek integrálására automatizált munkafolyamatok létrehozásával javíthatja a fenyegetések észlelésének, védelmének és az azokra való reagálásnak a módját. Létrehozhat például [az Azure Security Center forgatókönyveit,](../security-center/security-center-playbooks.md) amelyek figyelik és kezelik a Microsoft Graph biztonsági entitásokat, például a riasztásokat. Íme néhány forgatókönyv, amelyet a Microsoft Graph Security összekötő támogat:

* Riasztások at kaphat lekérdezések vagy riasztásazonosító alapján. Például lehívhat egy listát, amely tartalmazza a magas súlyosságú riasztások.

* Értesítések frissítése. Frissítheti például a riasztási hozzárendeléseket, megjegyzéseket fűzhet a riasztásokhoz, vagy megcímkézheti a riasztásokat.

* Riasztási [előfizetések (webhookok)](https://docs.microsoft.com/graph/api/resources/webhooks)létrehozásával figyelheti, hogy mikor jönnek létre vagy módosítanak riasztásokat.

* A riasztási előfizetések kezelése. Például beszerezheti az aktív előfizetéseket, meghosszabbíthatja egy előfizetés lejárati idejét, vagy törölheti az előfizetéseket.

A logikai alkalmazás munkafolyamata olyan műveleteket használhat, amelyek válaszokat kapnak a Microsoft Graph Security összekötőről, és elérhetővé teszik a kimenetet a munkafolyamat más műveletei számára. A munkafolyamatban más műveletek is használhatják a Microsoft Graph Security összekötő műveleteinek kimenetét. Ha például a Microsoft Graph Security összekötőn keresztül kap magas súlyosságú riasztásokat, ezeket a riasztásokat az Outlook-összekötő használatával e-mailben küldheti el. 

A Microsoft Graph Security szolgáltatásról a [Microsoft Graph Security API áttekintése című témakörben olvashat bővebben.](https://aka.ms/graphsecuritydocs) Ha most kezdi meg a logikai alkalmazásokat, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Ha microsoft flow-t vagy PowerApps-et keres, olvassa el [a Mi a Flow?](https://flow.microsoft.com/) vagy [a Mi a PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Microsoft Graph Security-összekötő használatához *explicit* Azure Active Directorybeli bérlői rendszergazdai hozzájárulás szükséges, amely a [Microsoft Graph Security hitelesítési követelményei](https://aka.ms/graphsecurityauth) közé is tartozik. A hozzájáruláshoz a Microsoft Graph Security összekötő alkalmazásazonosítója és neve szükséges, amelyet az [Azure Portalon](https://portal.azure.com)is megtalál:

  | Tulajdonság | Érték |
  |----------|-------|
  | **Alkalmazásnév** | `MicrosoftGraphSecurityConnector` |
  | **Alkalmazásazonosító** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Az összekötő hozzájárulásának megadásához az Azure AD-bérlőrendszergazdája az alábbi lépéseket hajthatja végre:

  * [Adjon bérlői rendszergazdai jóváhagyást az Azure AD-alkalmazásokhoz](../active-directory/develop/v2-permissions-and-consent.md).

  * A logikai alkalmazás első futtatásakor az alkalmazás az [alkalmazás-jóváhagyási felületen](../active-directory/develop/application-consent-experience.md) kérhet jóváhagyást az Azure AD bérlői rendszergazdájától.
   
* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a Microsoft Graph biztonsági entitások, például a riasztások. A Microsoft Graph Biztonsági eseményindító használatához üres logikai alkalmazásra van szükség. A Microsoft Graph biztonsági művelet használatához olyan logikai alkalmazásra van szükség, amely a forgatókönyvnek megfelelő eseményindítóval kezdődik.

## <a name="connect-to-microsoft-graph-security"></a>Csatlakozás a Microsoft Graph biztonságához 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén adja hozzá az eseményindítót és a kívánt műveleteket a Microsoft Graph biztonsági művelet hozzáadása előtt.

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésben, amikor hozzá szeretne adni egy Microsoft Graph biztonsági műveletet, válassza az **Új lépés lehetőséget.**

   – vagy –

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a megjelenő pluszjelet (+), és válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "Microsoft Graph security" kifejezést. A műveletek listájában jelölje ki a kívánt műveletet.

1. Jelentkezzen be a Microsoft Graph biztonsági hitelesítő adataival.

1. Adja meg a kiválasztott művelethez szükséges részleteket, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="add-triggers"></a>Eseményindítók hozzáadása

Az Azure Logic Apps minden logikai alkalmazás kell kezdeni egy [eseményindító,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy adott esemény bekövetkezésekor, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító aktiválódik, a Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, és megkezdi az alkalmazás munkafolyamatának futtatását.

> [!NOTE] 
> Amikor egy eseményindító aktiválódik, az eseményindító feldolgozza az összes új riasztást. Ha nem érkezik riasztás, az eseményindító futtatása kimarad. A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési időköz alapján történik.

Ez a példa bemutatja, hogyan indíthat el egy logikai alkalmazás munkafolyamatát, amikor új riasztásokat küld az alkalmazásnak.

1.  Az Azure Portalon vagy a Visual Studio hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic App Designer. Ez a példa az Azure Portalt használja.

1.  A tervező keresőmezőjében írja be szűrőként a "Microsoft Graph security" kifejezést. Az eseményindítók listájából válassza ezt az **eseményindítót: Minden új riasztáson**

1.  Az eseményindítóban adja meg a figyelni kívánt riasztásokadatait. További tulajdonságok esetén nyissa meg az **Új paraméter hozzáadása** listát, és jelöljön ki egy paramétert, amely hozzáadja a tulajdonságot az eseményindítóhoz.

   | Tulajdonság | Ingatlan (JSON) | Kötelező | Típus | Leírás |
   |----------|-----------------|----------|------|-------------|
   | **Intervallum** | `interval` | Igen | Egész szám | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján. A minimális és maximális intervallumok a következők: <p><p>- Hónap: 1-16 hónap <br>- Nap: 1-500 nap <br>- Óra: 1-12.000 óra <br>- Perc: 1-72.000 perc <br>- Második: 1-9,999,999 másodperc <p>Ha például az intervallum 6, és a gyakoriság "Hónap", akkor az ismétlődés 6 havonta történik. |
   | **Frekvencia** | `frequency` | Igen | Sztring | Az ismétlődés időegysége: **Második,** **Perc,** **Óra,** **Nap,** **Hét**vagy **Hónap** |
   | **Időzóna** | `timeZone` | Nem | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az eseményindító nem fogadja el [az UTC eltolást.](https://en.wikipedia.org/wiki/UTC_offset) Jelölje ki az alkalmazni kívánt időzónát. |
   | **Kezdési idő** | `startTime` | Nem | Sztring | Adja meg a kezdési dátumot és az időt ebben a formátumban: <p><p>YÉÉÉ-HH-DDThh:mm:ss, ha időzónát választ <p>– vagy – <p>YÉÉÉ-HH-DDThh:mm:ssZ, ha nem választ ki időzónát <p>Ha például 2017. Vagy adja meg a "2017-09-18T14:00:00Z" értéket időzóna nélkül. <p>**Megjegyzés:** Ennek a kezdési időpontnak a jövőben legfeljebb 49 éve van, és [utc dátumidő-formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kell követnie az [ISO 8601 dátumidő-meghatározási](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) szintet , de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül . Ha nem jelöl ki időzónát, a végén szóközök nélkül kell hozzáadnia a "Z" betűt. Ez a "Z" az egyenértékű [hajózási időre](https://en.wikipedia.org/wiki/Nautical_time)vonatkozik . <p>Egyszerű ütemezések esetén a kezdési időpont az első előfordulás, míg az összetett ütemezések esetén az eseményindító nem aktiválódik hamarabb, mint a kezdési időpont. [*Hogyan használhatom a kezdő dátumot és az időt?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

1.  Most folytassa egy vagy több művelet hozzáadásával a logikai alkalmazáshoz az eseményindító eredményekkel végrehajtani kívánt feladatokhoz.

## <a name="add-actions"></a>Műveletek hozzáadása

Az alábbiakban részletesebb részleteket olvashat a Microsoft Graph Security összekötővel elérhető különböző műveletek használatáról.

### <a name="manage-alerts"></a>Riasztások kezelése

A legfrissebb eredmények szűréséhez, rendezéséhez vagy lefelvételéhez *csak* a [Microsoft Graph által támogatott ODATA lekérdezési paramétereket](https://docs.microsoft.com/graph/query-parameters)adja meg. *Ne adja meg* a teljes alap URL-címet `https://graph.microsoft.com/v1.0/security/alerts`vagy a `GET` `PATCH` HTTP-műveletet, például a , vagy a műveletet. Íme egy konkrét példa, amely a **Riasztások beszedése** művelet paramétereit mutatja be, ha magas súlyosságú riasztásokat tartalmazó listát szeretne:

`Filter alerts value as Severity eq 'high'`

Az összekötővel használható lekérdezésekről a [Microsoft Graph Security riasztások hivatkozási dokumentációjában](https://docs.microsoft.com/graph/api/alert-list)olvashat bővebben. Az összekötőt bővített élmények létrehozásához további információ a [séma tulajdonságai,](https://docs.microsoft.com/graph/api/resources/alert) amelyek az összekötő támogatja.

| Műveletek | Leírás |
|--------|-------------|
| **Értesítések beszerezni** | Riasztások szűrése egy vagy több [riasztási tulajdonság](https://docs.microsoft.com/graph/api/resources/alert) `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`alapján, például . | 
| **Riasztás beszerezni azonosítóval** | A riasztásazonosító alapján adott riasztás t. | 
| **Riasztás frissítése** | Adott riasztás frissítése a riasztásazonosító alapján. Ha biztosszeretne átadni a szükséges és szerkeszthető tulajdonságokat a kérelemben, tekintse meg a [riasztások szerkeszthető tulajdonságait.](https://docs.microsoft.com/graph/api/alert-update) Ha például egy biztonsági elemzőhöz szeretne riasztást rendelni, hogy azok megvizsgálhassák, frissítheti a riasztás **tulajdonsághoz rendelt** riasztását. |
|||

### <a name="manage-alert-subscriptions"></a>Riasztási előfizetések kezelése

A Microsoft Graph támogatja az [*előfizetéseket*](https://docs.microsoft.com/graph/api/resources/subscription)és [*a webhookokat*](https://docs.microsoft.com/graph/api/resources/webhooks). Az előfizetések beszerezéséhez, frissítéséhez vagy törléséhez adja meg a Microsoft Graph `security/alerts` által támogatott [ODATA lekérdezési paramétereket](https://docs.microsoft.com/graph/query-parameters) a Microsoft Graph entitásszerkezetnek, és adja meg az okat az ODATA-lekérdezésnek. *Ne adja meg* például az `https://graph.microsoft.com/v1.0`alap URL-címet. Ehelyett használja a példában szereplő formátumot:

`security/alerts?$filter=status eq 'New'`

| Műveletek | Leírás |
|--------|-------------|
| **Előfizetések létrehozása** | [Hozzon létre egy előfizetést,](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) amely értesíti Önt a változásokról. Ezt az előfizetést a kívánt riasztástípusokra szűrheti. Létrehozhat például egy előfizetést, amely értesíti a súlyos riasztásokról. |
| **Aktív előfizetések beszereznie** | [Le nem járt előfizetések beszereznie.](https://docs.microsoft.com/graph/api/subscription-list) | 
| **Előfizetés frissítése** | [Előfizetés frissítése](https://docs.microsoft.com/graph/api/subscription-update) az előfizetésazonosító megadásával. Például az előfizetés meghosszabbításához frissítheti az `expirationDateTime` előfizetés tulajdonságát. | 
| **Előfizetés törlése** | [Előfizetés törlése](https://docs.microsoft.com/graph/api/subscription-delete) az előfizetésazonosító megadásával. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Fenyegetésintelligencia-mutatók kezelése

A legfrissebb eredmények szűréséhez, rendezéséhez vagy lefelvételéhez *csak* a [Microsoft Graph által támogatott ODATA lekérdezési paramétereket](https://docs.microsoft.com/graph/query-parameters)adja meg. *Ne adja meg* a teljes alap URL-címet `https://graph.microsoft.com/beta/security/tiIndicators`vagy a `GET` `PATCH` HTTP-műveletet, például a , vagy a műveletet. Íme egy konkrét példa, amely a **TiIndicators leget művelet** paramétereit `DDoS` mutatja be, ha olyan listát szeretne, amely rendelkezik a fenyegetés típusával:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Az összekötővel használható lekérdezésekről a [Microsoft Graph Security fenyegetésfelderítési jelzőjének referenciadokumentációjában található "Választható lekérdezési paraméterek" című témakörben olvashat bővebben.](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http) Az összekötőt bővített élmények létrehozásához további információ a [séma tulajdonságainak az](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) összekötő által támogatott fenyegetésfelderítési jelzőről.

| Műveletek | Leírás |
|--------|-------------|
| **Fenyegetésintelligencia-jelzők beszereznie** | TiIndicator szűrése egy vagy több [tiIndicator tulajdonság](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta)alapján, például`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Fenyegetésfelderítés-jelző beszerezni azonosítóval** | Kap egy adott tiIndicator alapján tiIndicator ID. | 
| **Fenyegetésfelderítési mutató létrehozása** | Hozzon létre egy új tiIndicator feladással a tiIndicators gyűjtemény. Annak érdekében, hogy a kérelemben megtekintse a szükséges tulajdonságokat, olvassa el a [tiIndicator létrehozásához szükséges tulajdonságokat.](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http) |
| **Több fenyegetésintelligencia-mutató beküldése** | Hozzon létre több új tiIndicators közzétételével tiIndicators gyűjtemény. Annak érdekében, hogy a kérelemben a szükséges tulajdonságokat adja át, olvassa el a [több tiIndicators elküldéséhez szükséges tulajdonságokat.](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http) |
| **A fenyegetésfelderítés jelzőjének frissítése** | Egy adott tiIndicator frissítése a tiIndicator ID alapján. Ha biztosan át szeretne adni a kérelemben szükséges és szerkeszthető tulajdonságokat, tekintse meg a [tiIndicator szerkeszthető tulajdonságait.](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http) Ha például frissíteni szeretné a műveletet, hogy a mutató a targetProduct biztonsági eszközből egyezjön, frissítheti a tiIndicator **művelettulajdonságát.** |
| **Több fenyegetésintelligencia-mutató frissítése** | Több tiInitor frissítés. Annak érdekében, hogy a kérelemben a szükséges tulajdonságokat adja át, olvassa el a [több tiIndicators frissítéséhez szükséges tulajdonságokat.](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http) |
| **Fenyegetésfelderítési jelző törlése azonosító szerint** | Egy adott tiIndicator törlése a tiIndicator ID alapján. |
| **Több fenyegetésfelderítési mutató törlése azonosítók alapján** | Több tiIndicators törlése az azonosítójuk alapján. Annak érdekében, hogy a kérelemben megadott tulajdonságokat adja át, tekintse meg a [több tiIndicators azonosítók általi törléséhez szükséges tulajdonságokat.](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http) |
| **Több fenyegetésintelligencia-jelző törlése külső azonosítók szerint** | Több tiIndicators törlése a külső azonosítók alapján. Annak érdekében, hogy a kérelemben megadott tulajdonságokat adja át, tekintse meg a [szükséges tulajdonságokat több tiIndicators külső azonosítók alapján történő törléséhez.](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http) |
|||

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](https://aka.ms/graphsecurityconnectorreference)

## <a name="next-steps"></a>További lépések

További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
