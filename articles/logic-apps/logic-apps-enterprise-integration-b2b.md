---
title: Exchange-üzenetek B2B vállalati integrációs forgatókönyvekhez
description: B2B-üzenetek fogadása és küldése a kereskedelmi partnerek között az Azure Logic Apps alkalmazásban a Vállalati integrációs csomag használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151206"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-üzenetek fogadása és küldése az Azure Logic Apps és az Enterprise Integration Pack használatával

Ha olyan integrációs fiókkal rendelkezik, amely meghatározza a kereskedelmi partnereket és a megállapodásokat, létrehozhat egy automatizált üzleti vállalkozások közötti (B2B) munkafolyamatot, amely az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Vállalati [integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md)használatával üzeneteket cserél a kereskedelmi partnerek között. Az Azure Logic Apps olyan összekötőkkel működik, amelyek támogatják az AS2, X12, EDIFACT és RosettaNet iparági szabványnak megfelelő protokollokat. Ezeket az összekötőket kombinálhatja a Logic Apps alkalmazásban elérhető más [összekötőkkel](../connectors/apis-list.md)is, például a Salesforce és az Office 365 Outlook alkalmazásban.

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely http-kérelmet fogad egy kérelem eseményindító használatával, dekódolja az üzenet tartalmát az AS2 és X12 műveletek használatával, majd a válaszművelet teljében választ ad vissza.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Egy üres logikai alkalmazás, amely letudja létrehozni a B2B munkafolyamatot a [Kérelem](../connectors/connectors-native-reqres.md) eseményindító használatával, amelyet a következő műveletek követnek:

  * [AS2 dekódolás](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Feltétel](../logic-apps/logic-apps-control-flow-conditional-statement.md), amely [választ](../connectors/connectors-native-reqres.md) küld attól függően, hogy az AS2 dekódolási művelet sikeres vagy sikertelen lesz

  * [X12 üzenet dekódolása](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Ha most kezdi meg a logikai alkalmazások, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md) és a rövid [útmutató: Az első logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az Azure-előfizetéshez társított és a logikai alkalmazáshoz kapcsolódó [integrációs fiók.](../logic-apps/logic-apps-enterprise-integration-accounts.md) Mind a logikai alkalmazás és az integrációs fiók léteznie kell ugyanazon a helyen vagy az Azure-régióban.

* Legalább két [olyan kereskedelmi partner,](../logic-apps/logic-apps-enterprise-integration-partners.md) amelyet már definiált az integrációs fiókjában, valamint [az AS2 és X12 megállapodásokat](logic-apps-enterprise-integration-agreements.md) ezekhez a partnerekhez.

## <a name="add-request-trigger"></a>Kérelem eseményindítóhozzáadása

Ez a példa a Logic App Designer az Azure Portalon, de a Visual Studio logikai alkalmazástervezőjének hasonló lépéseit is követheti.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A keresőmezőbe írja `when a http request`be a beírását, és válassza **a Http-kérelem beérkezésekor való beérkezésekor** lehetőséget.

   ![A logikai alkalmazás munkafolyamatának elindításához válassza a Kérelemeseményindító lehetőséget.](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Hagyja üresen a **Kérelemtörzs JSON-sémamezőjét,** mert az X12-es üzenet egy sima fájl.

   ![Hagyja üresen a "Kérelemtörzs JSON-séma"](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

   Ez a lépés a logikai alkalmazást kiváltó kérelem küldéséhez használandó **HTTP-POST URL-címet** hozza létre. Az URL másolásához jelölje ki az URL-cím melletti másolásikont.

   ![Hívások fogadására szolgáló kérelemesemény-eseményindítóhoz létrehozott URL-cím](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>AS2-es dekódolási művelet hozzáadása

Most adja hozzá a használni kívánt B2B-műveleteket. Ez a példa AS2 és X12 műveleteket használ.

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.** Az eseményindító részleteinek elrejtéséhez kattintson az eseményindító címsorára.

   ![Újabb lépés hozzáadása a logikai alkalmazás munkafolyamatához](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. A **Művelet kiválasztása csoportkereső**mezőjébe `as2 decode`írja be a tao-kódját, és válassza az **AS2 Dekódolás (v2) lehetőséget.**

   ![Keresse meg és válassza az "AS2 Dekódolás (v2)" lehetőséget](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Az **üzenet dekódolásához** adja meg azt a bemenetet, amelyet az `body` AS2 műveletdekódolni szeretne, amely a HTTP-kérelem eseményindítója által fogadott tartalom. Ezt a tartalmat többféleképpen is megadhatja bemenetként, akár a dinamikus tartalomlistából, akár kifejezésként:

   * Ha olyan listából szeretne választani, amely a rendelkezésre álló eseményindító kimeneteket jeleníti meg, kattintson az **Üzenet a dekódoláshoz** mezőben. A dinamikus tartalomlista után a **HTTP-kérelem fogadása**csoportban válassza a **Törzs** tulajdonság értékét, például:

     ![Válassza ki a "Törzs" értéket az eseményindítóból](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Ha olyan kifejezést szeretne megadni, `body` amely az eseményindító kimenetére hivatkozik, kattintson az Üzenet a **dekódoláshoz** mezőben. A dinamikus tartalomlista megjessze, és válassza **a Kifejezés**lehetőséget. A kifejezésszerkesztőben írja be a kifejezést ide, és válassza az **OK**gombot:

     `triggerOutputs()['body']`

     Vagy a **Dekódolandó üzenet** mezőben írja be közvetlenül ezt a kifejezést:

     `@triggerBody()`

     A kifejezés a **Törzs** tokenre oldódik fel.

     ![Megoldott törzskimenet az eseményindítóból](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Az **Üzenetfejlécek** tulajdonsághoz adja meg az AS2 művelethez szükséges `headers` fejléceket, amelyeket a HTTP-kérelem eseményindítója által kapott tartalom ír le.

   Ha olyan kifejezést szeretne beírni, `headers` amely az eseményindító kimenetére hivatkozik, kattintson az **Üzenetfejlécek** mezőbe. A dinamikus tartalomlista megjessze, és válassza **a Kifejezés**lehetőséget. A kifejezésszerkesztőben írja be a kifejezést ide, és válassza az **OK**gombot:

   `triggerOutputs()['Headers']`

   Ha azt szeretné, hogy ez a kifejezés tokenként oldódjon fel, váltson a tervező és a kódnézet között, például:

   ![Feloldott fejlécek kimenete az eseményindítóból](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Válaszhozzáadása művelet az üzenetvisszaigazolási értesítéshez

Ha értesíteni szeretné a kereskedelmi partnert arról, hogy az üzenet érkezett, a **Válasz** művelettel visszaküldheti az AS2 üzenetintézkedési értesítést (MDN) tartalmazó választ. Ha ezt a műveletet közvetlenül az **AS2 dekódolási** művelet után adja hozzá, ha ez a művelet sikertelen, a logikai alkalmazás nem folytatja a feldolgozást.

1. Az **AS2 Dekódolás** művelet csoportban válassza az **Új lépés lehetőséget.**

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja be a `condition` kifejezést. A **Műveletek** listában válassza a **Feltétel lehetőséget.**

   ![A "Feltétel" művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Most megjelenik a feltétel alakzat, beleértve a feltételek teljesülésének görbéit is.

   ![Feltétel alakzat döntési görbékkel](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Most adja meg a kiértékelandó feltételt. Az **Érték kiválasztása** mezőbe írja be ezt a kifejezést:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   A középső mezőben győződjön meg arról, hogy az összehasonlítási művelet beállítása `is equal to`. A jobb oldali mezőbe írja `Expected`be az értéket. Ha a kifejezést fel szeretné oldani jogkivonatként, váltson a tervező és a kódnézet között.

   ![Feltétel alakzat döntési görbékkel](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Most adja meg a válaszokat, hogy visszatérjen, hogy az **AS2 dekódolási** művelet sikeres vagy sem.

   1. Abban az esetben, ha az **AS2 Dekódolás** művelet sikeres, a **Ha igaz** alakzat, válassza **a Művelet hozzáadása**lehetőséget . A **Művelet kiválasztása csoportkereső**mezőjébe `response`írja be a tauza parancsot, és válassza a **Válasz**lehetőséget.

      ![A "Válasz" művelet megkeresése és kiválasztása](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Az AS2 MDN-nek az **AS2 dekódolási** művelet kimenetéről való eléréséhez adja meg a következő kifejezéseket:

      * A **Válasz** művelet **Fejlécek** tulajdonságába írja be ezt a kifejezést:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * A **Válasz** művelet **Törzs** tulajdonságába írja be ezt a kifejezést:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. A kifejezések tokenként való feloldásához váltson a tervező és a kódnézet között:

      ![Feloldott kifejezés az AS2 MDN eléréséhez](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Abban az esetben, ha az **AS2 dekódolási** művelet sikertelen, a **Ha hamis** alakzatban válassza **a Művelet hozzáadása**lehetőséget. A **Művelet kiválasztása csoportkereső**mezőjébe `response`írja be a tauza parancsot, és válassza a **Válasz**lehetőséget. Állítsa be a **Válasz** műveletet a kívánt állapot és hiba visszaadására.

1. Mentse a logikai alkalmazást.

## <a name="add-decode-x12-message-action"></a>Dekódoló X12 üzenetművelet hozzáadása

1. Most adja hozzá a **Decode X12 üzenet** műveletet. A **Válasz** művelet csoportban válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása csoportkereső**mezőjébe `x12 decode`írja be a tapont, és válassza **az X12-es üzenet dekódolása**lehetőséget.

   ![Az "X12 üzenet dekódolása" művelet megkeresése és kijelölése](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Ha az X12 művelet kapcsolati adatokat kér, adja meg a kapcsolat nevét, jelölje ki a használni kívánt integrációs fiókot, majd válassza a **Létrehozás gombot.**

   ![X12-kapcsolat létrehozása integrációs fiókhoz](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Most adja meg az X12 művelet bemenetét. Ez a példa az AS2 művelet kimenetét használja, amely az üzenet tartalma, de vegye figyelembe, hogy ez a tartalom JSON objektum formátumú és base64 kódolású. Tehát ezt a tartalmat karakterláncba kell konvertálnia.

   A **dekódolni kívánt X12-fájlüzenet** mezőbe írja be ezt a kifejezést az AS2 kimenet konvertálásához:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Ha a kifejezést fel szeretné oldani jogkivonatként, váltson a tervező és a kódnézet között.

    ![Base64 kódolású tartalom konvertálása karakterláncgá](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Mentse a logikai alkalmazást.

   Ha további lépésekre van szüksége ehhez a logikai alkalmazáshoz, például az üzenet tartalmának és kimenetének JSON-objektum formátumban való dekódolásához, folytassa a logikai alkalmazás létrehozásának folytatását.

Most már befejezte a B2B logikai alkalmazás beállítását. Egy valós alkalmazásban előfordulhat, hogy a dekódolt X12-adatokat egy üzletági (LOB) alkalmazásban vagy adattárban szeretné tárolni. Lásd például az alábbi cikkeket:

* [Csatlakozás SAP-rendszerekhez az Azure Logic Appsből](../logic-apps/logic-apps-using-sap-connector.md)
* [SFTP-fájlok figyelése, létrehozása és kezelése SSH és Azure Logic Apps használatával](../connectors/connectors-sftp-ssh.md)

Saját üzletági alkalmazások csatlakoztatásához és ezeknek az API-knak a logikai alkalmazásban való használatához további műveleteket adhat hozzá, vagy [egyéni API-kat írhat.](../logic-apps/logic-apps-create-api-app.md)

## <a name="next-steps"></a>További lépések

* [Bejövő HTTPS-hívások fogadása és megválaszolása](../connectors/connectors-native-reqres.md)
* [AS2-üzenetek cseréje B2B vállalati integrációhoz](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12 üzenetek a B2B vállalati integrációhoz](../logic-apps/logic-apps-enterprise-integration-x12.md)
* További információ az [Enterprise Integration Pack csomagról](../logic-apps/logic-apps-enterprise-integration-overview.md)