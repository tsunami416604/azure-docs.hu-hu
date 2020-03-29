---
title: Várakozás és válaszadás az eseményekre
description: Az Azure Logic Apps használatával automatizálhatja azokat a munkafolyamatokat, amelyek egy szolgáltatásvégponton történt események alapján aktiválják, szüneteltetik és folytatják
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 1578ca030bc8bab971a44e1afcce1d1ab9e1d5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674105"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatizált eseményalapú munkafolyamatok létrehozása és futtatása HTTP-webhookok használatával az Azure Logic Apps alkalmazásban

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített HTTP Webhook-összekötő segítségével automatizálhatja azokat a munkafolyamatokat, amelyek a HTTP- vagy HTTPS-végponton bekövetkező konkrét események alapján várakoznak és futnak logikai alkalmazások létrehozásával. Létrehozhat például egy logikai alkalmazást, amely egy szolgáltatásvégpontot figyel úgy, hogy egy adott eseményre vár a munkafolyamat aktiválása előtt, és futtatja a megadott műveleteket, ahelyett, hogy rendszeresen ellenőrizné vagy *legondolná* a végpontot.

Íme néhány példa eseményalapú munkafolyamatokra:

* Várja meg, amíg egy elem megérkezik egy [Azure Event Hub,](https://github.com/logicappsio/EventHubAPI) mielőtt egy logikai alkalmazás futtatásához.
* A munkafolyamat folytatása előtt várjon a jóváhagyásra.

## <a name="how-do-webhooks-work"></a>Hogyan működnek a webhookok?

A HTTP webhook eseményindító eseményalapú, amely nem függ az új elemek ellenőrzésétől vagy lekérdezésétől. Amikor egy webhook-eseményindítóval kezdődő logikai alkalmazást ment, vagy ha a logikai alkalmazást letiltottról engedélyezve módosítja, a webhook-eseményindító *előfizet* egy adott szolgáltatásra vagy végpontra egy *visszahívási URL-cím* regisztrálásával az adott szolgáltatással vagy végponttal. Az eseményindító ezután megvárja, hogy a szolgáltatás vagy a végpont hívja meg az URL-címet, amely elindítja a logikai alkalmazás futtatását. A [kérelem eseményindítóhoz](connectors-native-reqres.md)hasonlóan a logikai alkalmazás azonnal aktiválódik, amikor a megadott esemény megtörténik. Az eseményindító *leiratkozik* a szolgáltatásról vagy a végpontról, ha eltávolítja az eseményindítót, és menti a logikai alkalmazást, vagy ha módosítja a logikai alkalmazást engedélyezve lévőről letiltva.

Egy HTTP webhook-művelet is eseményalapú, és *előfizet* egy adott szolgáltatás vagy végpont regisztrálásával egy *visszahívási URL-t* az adott szolgáltatás vagy végpont. A webhook-művelet szünetelteti a logikai alkalmazás munkafolyamatát, és megvárja, amíg a szolgáltatás vagy a végpont meghívja az URL-címet, mielőtt a logikai alkalmazás folytatja a futást. A műveletlogikai alkalmazás ezekben az esetekben *leiratkozik* a szolgáltatásról vagy a végpontról:

* Amikor a webhook művelet sikeresen befejeződik
* Ha a logikai alkalmazás futtatása megszakad, miközben választ vár
* Mielőtt a logikai alkalmazás idővel kitér

Például az Office 365 Outlook-összekötő [**Jóváhagyási e-mail küldése művelet e-mail küldése**](connectors-create-api-office365-outlook.md) művelet egy példa a webhook művelet, amely követi ezt a mintát. Ezt a mintát kiterjesztheti bármely szolgáltatásra a webhook-művelet használatával.

> [!NOTE]
> A Logic Apps kényszeríti a Transport Layer Security (TLS) 1.2-es verziójának hívását a HTTP webhook eseményindítóra vagy műveletre. Ha SSL kézfogási hibákat lát, győződjön meg arról, hogy a TLS 1.2-t használja. Bejövő hívások esetén az alábbiakban a támogatott titkosítási csomagok találhatók:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

További információt az alábbi témakörökben talál:

* [HTTP Webhook eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhookok és előfizetések](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Webhookot támogató egyéni API-k létrehozása](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy már üzembe helyezett végpont vagy API URL-címe, amely támogatja a webhook-előfizetést és a [webhook-eseményindítók](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) leiratkozási mintáját a logikai alkalmazásokban vagy [a logikai alkalmazásokban végrehajtott webhook-műveletekben.](../logic-apps/logic-apps-create-api-app.md#webhook-actions)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A logikai alkalmazás, ahol meg szeretné várni a célvégponton adott eseményeket. A HTTP Webhook eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) A HTTP Webhook művelet használatához indítsa el a logikai alkalmazást bármilyen eseményindítóval, amit csak akar. Ez a példa a HTTP-eseményindítót használja első lépésként.

## <a name="add-an-http-webhook-trigger"></a>HTTP Webhook-eseményindító hozzáadása

Ez a beépített eseményindító meghívja az előfizetési végpontot a célszolgáltatáson, és regisztrál egy visszahívási URL-t a célszolgáltatással. A logikai alkalmazás ezután megvárja, `HTTP POST` amíg a célszolgáltatás kérést küld a visszahívási URL-címre. Ha ez az esemény bekövetkezik, az eseményindító aktiválódik, és a kérelemben szereplő adatokat továbbítja a munkafolyamatnak.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A tervező keresőmezőjébe írja `http webhook` be szűrőként. Az **Eseményindítók** listában válassza ki a **HTTP Webhook eseményindítót.**

   ![A HTTP Webhook eseményindító jának kiválasztása](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Ez a példa átnevezi az eseményindítót, `HTTP Webhook trigger` hogy a lépés nek leíróbb neve is van. A példa később http-webhook-műveletet is hozzáad, és mindkét névnek egyedinek kell lennie.

1. Adja meg a [HTTP Webhook eseményindító paramétereinek értékeit, amelyeket](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) az előfizetési és leiratkozási hívásokhoz kíván használni.

   Ebben a példában az eseményindító tartalmazza a módszereket, URI-kat és üzenettörzseket, amelyeket az előfizetési és leiratkozási műveletek végrehajtásakor használhat.

   ![Adja meg a HTTP Webhook eseményindító paramétereit](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Előfizetés - módszer** | Igen | A célvégpontra való feliratkozáskor használandó módszer |
   | **Feliratkozás - URI** | Igen | A célvégpontra való feliratkozáshoz használandó URL-cím |
   | **Feliratkozás - Testület** | Nem | Az előfizetési kérelemben szerepeljen bármely üzenettörzs. Ebben a példában a visszahívási URL-címet, amely egyedileg azonosítja `@listCallbackUrl()` az előfizető, amely a logikai alkalmazás, a kifejezés használatával a logikai alkalmazás visszahívási URL-címét. |
   | **Leiratkozás - Módszer** | Nem | A célvégpontról való leiratkozáskor használandó módszer |
   | **Leiratkozás - URI** | Nem | A célvégpontról való leiratkozáshoz használandó URL-cím |
   | **Leiratkozás - Testület** | Nem | A leiratkozási kérelembe foglalandó opcionális üzenettörzs <p><p>**Megjegyzés:** Ez a tulajdonság nem `listCallbackUrl()` támogatja a függvény használatát. Az eseményindító azonban automatikusan tartalmazza `x-ms-client-tracking-id` és `x-ms-workflow-operation-name`elküldi a fejléceket, és amelyet a célszolgáltatás az előfizető egyedi azonosítására használhat. |
   ||||

1. További eseményindító-tulajdonságok hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát.

   ![További eseményindító-tulajdonságok hozzáadása](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Ha például hitelesítést kell használnia, **hozzáadhatja** az Előfizetés - Hitelesítés és **leiratkozás - Hitelesítés** tulajdonságokat. A HTTP Webhook hoz elérhető hitelesítési típusokról a [Hitelesítés hozzáadása kimenő hívásokhoz című](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)témakörben talál további információt.

1. Folytassa a logikai alkalmazás munkafolyamatának felépítését olyan műveletekkel, amelyek az eseményindító aktiválódásakor futnak.

1. Ha elkészült, kész, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

   A logikai alkalmazás mentése meghívja az előfizetési végpontot a célszolgáltatáson, és regisztrálja a visszahívási URL-címet. A logikai alkalmazás ezután megvárja, `HTTP POST` amíg a célszolgáltatás kérést küld a visszahívási URL-címre. Ha ez az esemény bekövetkezik, az eseményindító aktiválódik, és a kérelemben szereplő adatokat továbbítja a munkafolyamatnak. Ha ez a művelet sikeresen befejeződik, az eseményindító leiratkozik a végpontról, és a logikai alkalmazás folytatja a fennmaradó munkafolyamatot.

## <a name="add-an-http-webhook-action"></a>HTTP Webhook művelet hozzáadása

Ez a beépített művelet meghívja az előfizetési végpontot a célszolgáltatáson, és regisztrál egy visszahívási URL-t a célszolgáltatással. A logikai alkalmazás ezután szünetel, és `HTTP POST` várja a célszolgáltatás, hogy küldjön egy kérelmet a visszahívási URL-címet. Ha ez az esemény bekövetkezik, a művelet a kérelemben szereplő adatokat továbbítja a munkafolyamatnak. Ha a művelet sikeresen befejeződik, a művelet leiratkozik a végpontról, és a logikai alkalmazás folytatja a fennmaradó munkafolyamat futtatását.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Nyissa meg a logikai alkalmazást a Logic App Designerben.

   Ez a példa a HTTP Webhook eseményindítót használja első lépésként.

1. A HTTP Webhook művelet hozzáadásának lépése alatt válassza az **Új lépés lehetőséget.**

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A tervező keresőmezőjébe írja `http webhook` be szűrőként. A **Műveletek** listában válassza a **HTTP Webhook** műveletet.

   ![HTTP Webhook művelet kijelölése](./media/connectors-native-webhook/select-http-webhook-action.png)

   Ez a példa átnevezi a műveletet "HTTP Webhook művelet", hogy a lépés egy leíró nevet.

1. Adja meg a HTTP Webhook műveletparamétereinek értékeit, amelyek hasonlóak a [HTTP Webhook eseményindító paramétereihez,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)amelyeket az előfizetési és leiratkozási hívásokhoz kíván használni.

   Ebben a példában a művelet tartalmazza a módszereket, URI-kat és üzenettörzseket, amelyeket az előfizetési és leiratkozási műveletek végrehajtásakor használhat.

   ![HTTP Webhook műveletparamétereinek megadása](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Előfizetés - módszer** | Igen | A célvégpontra való feliratkozáskor használandó módszer |
   | **Feliratkozás - URI** | Igen | A célvégpontra való feliratkozáshoz használandó URL-cím |
   | **Feliratkozás - Testület** | Nem | Az előfizetési kérelemben szerepeljen bármely üzenettörzs. Ebben a példában a visszahívási URL-címet, amely egyedileg azonosítja `@listCallbackUrl()` az előfizető, amely a logikai alkalmazás, a kifejezés használatával a logikai alkalmazás visszahívási URL-címét. |
   | **Leiratkozás - Módszer** | Nem | A célvégpontról való leiratkozáskor használandó módszer |
   | **Leiratkozás - URI** | Nem | A célvégpontról való leiratkozáshoz használandó URL-cím |
   | **Leiratkozás - Testület** | Nem | A leiratkozási kérelembe foglalandó opcionális üzenettörzs <p><p>**Megjegyzés:** Ez a tulajdonság nem `listCallbackUrl()` támogatja a függvény használatát. A művelet azonban automatikusan tartalmazza és `x-ms-client-tracking-id` `x-ms-workflow-operation-name`elküldi a fejléceket, valamint azt, hogy a célszolgáltatás mely módon azonosíthatja egyedileg az előfizetőt. |
   ||||

1. További művelettulajdonságok hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát.

   ![További művelettulajdonságok hozzáadása](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Ha például hitelesítést kell használnia, **hozzáadhatja** az Előfizetés - Hitelesítés és **leiratkozás - Hitelesítés** tulajdonságokat. A HTTP Webhook hoz elérhető hitelesítési típusokról a [Hitelesítés hozzáadása kimenő hívásokhoz című](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)témakörben talál további információt.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

   Most, amikor ez a művelet fut, a logikai alkalmazás meghívja az előfizetési végpontot a célszolgáltatáson, és regisztrálja a visszahívási URL-címet. A logikai alkalmazás ezután szünetelteti a `HTTP POST` munkafolyamatot, és megvárja, amíg a célszolgáltatás kérést küld a visszahívási URL-címre. Ha ez az esemény bekövetkezik, a művelet a kérelemben szereplő adatokat továbbítja a munkafolyamatnak. Ha a művelet sikeresen befejeződik, a művelet leiratkozik a végpontról, és a logikai alkalmazás folytatja a fennmaradó munkafolyamat futtatását.

## <a name="connector-reference"></a>Összekötő-referencia

Az egymáshoz hasonló eseményindító- és műveletparaméterekről a [HTTP Webhook-paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)című témakörben talál további információt.

### <a name="output-details"></a>Kimenet részletei

Az alábbiakban további információt talál a HTTP Webhook-eseményindító vagy -művelet kimeneteiről, amelyek ezt az információt adják vissza:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| Fejlécek | objektum | A kérelem fejlécei |
| body (Törzs) | objektum | JSON-objektum | A kérelemből származó törzstartalommal rendelkező objektum |
| állapotkód | int | A kérelem állapotkódja |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Rossz kérés |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
