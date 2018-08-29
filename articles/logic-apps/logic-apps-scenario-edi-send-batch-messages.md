---
title: A Batch-és EDI-üzenetek feldolgozásával egy csoport vagy a gyűjtemény – Azure Logic Apps |} A Microsoft Docs
description: A kötegelt feldolgozási, a logic apps EDI-üzenetek küldése
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 7e058b7cebb9c2cdc3fb8b97bf99554b2f26dd8c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121575"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>EDI-üzenetek küldése kötegekben kereskedelmi partnerekkel az Azure Logic Apps

Vállalatközi (B2B) környezetek, a partnerek gyakran alkalmazásközi csoportokban vagy *kötegek*. Ha a kötegelés megoldás logikai alkalmazásokkal hoz létre, üzeneteket küldeni a kereskedelmi partnerekkel, és együtt, és kötegekben üzeneteket feldolgozni. Ez a cikk bemutatja, hogyan kötegelt EDI-üzenetek feldolgozásával, X12 használatával például egy "batch küldő" logikai alkalmazás és a egy "batch fogadó" logikai alkalmazás létrehozásával. 

Kötegelés X12 üzenetek működését hasonlóan a többi üzenet; kötegelés egy batch-trigger, amely gyűjti az üzeneteket a batch és a egy kötegelt művelet, amely üzeneteket küld a batch használhatja. Ezenkívül X12 kódolást lépés, mielőtt az üzenetek nyissa meg a kereskedelmi partnerekkel vagy más célhelyre X12 kötegelés tartalmazza. A batch-eseményindító és művelet kapcsolatos további információkért lásd: [kötegelt feldolgozási üzenetek](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Ez a cikk az Azure-előfizetéshez, az Azure-régióban és a következő két logikai alkalmazások létrehozásával a kötegelés megoldás fog létrehozni a megadott sorrendben:

* A ["batch fogadó"](#receiver) logikai alkalmazást, amely fogadja, és gyűjti az üzeneteket, a batch a megadott feltételeknek megfelelő teljesüléséig ad ki, és ezeket az üzeneteket feldolgozásához. Ebben a forgatókönyvben a batch fogadó is kódolja a kötegben található üzenetek a megadott X12 használatával szerződés vagy -partnerétől identitásokat.

  Ellenőrizze, hogy először hozzon létre a batch fogadó ezért kiválaszthatja a batch cél később a batch küldő létrehozásakor.

* A ["batch küldő"](#sender) logikai alkalmazást, amely üzeneteket küld a korábban létrehozott batch-fogadó. 

Győződjön meg arról, hogy a batch fogadó és a batch küldő megosztása az Azure-előfizetéshez *és* az Azure-régióban. Ha nem, a batch fogadó a batch küldő létrehozásakor, mert azok még nem látható, egymáshoz nem választhat.

## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a példában, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Másik lehetőségként [regisztrálhat egy használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Egy meglévő [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely az Azure-előfizetéséhez társított, és kapcsolódik a logic apps

* Legalább két meglévő [partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) az integrációs fiókban. Egyes partnerek a X12 (Standard Alpha szolgáltatókódot) kell használnia a partner tulajdonságai egy üzleti identitás minősítője.

* Egy meglévő [X12 szerződés](../logic-apps/logic-apps-enterprise-integration-x12.md) az integrációs fiókjában

* Szeretné használni az Azure portal helyett a Visual Studióban, győződjön meg arról, hogy [beállítása a Visual Studio használata a Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Hozzon létre X12 batch fogadó

Üzeneteket küldhet egy kötegben, a batch először léteznie kell a célhelyeként, ahol ezeket az üzeneteket küld. Ezért először létre kell hoznia a "batch fogadó" logikai alkalmazást, amely kezdődik a **Batch** eseményindító. Ezzel a módszerrel a "batch küldő" logikai alkalmazás létrehozásakor kiválaszthatja a batch fogadó logikai alkalmazást. A batch fogadó továbbra is gyűjtése üzeneteket a megadott feltételeknek megfelelő teljesüléséig ad ki, és ezeket az üzeneteket feldolgozásához. Batch fogadók nem batch feladók ismernie kell, amíg a batch feladók ismernie kell a cél, ahol az üzeneteket küldenek. 

A batch fogadóhoz, adja meg a kötegelt módban, a neve, a kiadási feltételekben, X12 szerződés és az egyéb beállításokat. 

1. Az a [az Azure portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy logikai alkalmazást ezen a néven: "BatchX12Messages"

2. [A logikai alkalmazáshoz csatolni az integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. A Logic Apps Designer, adja hozzá a **Batch** eseményindító, amely elindítja a logikai alkalmazás munkafolyamatának. A Keresés mezőbe írja be a "batch" szűrőként. Válassza ki a következő eseményindítót: **Batch-üzenetek**

   ![Batch-eseményindító hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Állítsa be a batch-fogadó tulajdonságokat: 

   | Tulajdonság | Érték | Megjegyzések | 
   |----------|-------|-------|
   | **Kötegelt mód** | Beágyazott |  |  
   | **Kötegnév** | TestBatch | Csak a rendelkezésre álló **beágyazott** kötegelt mód | 
   | **Kiadási feltételek** | Üzenetszám-alapú, ütemezés alapján | Csak a rendelkezésre álló **beágyazott** kötegelt mód | 
   | **Üzenetek száma** | 10 | Csak a rendelkezésre álló **Message count alapú** kiadási feltételek | 
   | **Intervallum** | 10 | Csak a rendelkezésre álló **ütemezésalapú** kiadási feltételek | 
   | **Gyakoriság** | perc | Csak a rendelkezésre álló **ütemezésalapú** kiadási feltételek | 
   ||| 

   ![Adja meg a batch a trigger részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Ebben a példában nem állítsa be a Batch egy partíciót, úgy az egyes kötegek használja ugyanazt a partíciókulcsot. A partíciók kapcsolatos további információkért lásd: [kötegelt feldolgozási üzenetek](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Adjon hozzá egy műveletet, amely az egyes kötegek kódol: 

   1. A batch-eseményindító területén válassza a **új lépés**.

   2. A Keresés mezőbe írja be az "X 12 batch" szűrőként, és válassza ki ezt a műveletet (bármilyen verzió): **kötegelt kódolás <*verzió*>-X12** 

      ![Válassza ki a X12 művelet kötegelt kódolása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Ha korábban nem csatlakozik az integrációs fiók, hozzon létre most már a kapcsolatot. Adja meg a kapcsolat nevét, válassza ki, és válassza az integrációs fiók **létrehozás**.

      ![Batch-kódoló és integrációs fiók közötti kapcsolat létrehozása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Állítsa be ezeket a tulajdonságokat, a batch-kódoló művelet:

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **X12 nevére szerződés** | Nyissa meg a listát, és válassza ki a meglévő szerződés hatálya alá. <p>Ha a lista üres, győződjön meg arról, hogy [a logikai alkalmazáshoz csatolni az integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) , amely rendelkezik a megállapodás szeretné. | 
      | **BatchName** | Ebben a mezőben kattintson, és miután a dinamikus tartalmak listája jelenik meg, válassza ki a **Kötegnév** token. | 
      | **PartitionName** | Ebben a mezőben kattintson, és miután a dinamikus tartalmak listája jelenik meg, válassza ki a **partíciónévre** token. | 
      | **elemek** | A cikk részletes bezárásához, majd kattintson a belül be ezt a jelölőnégyzetet. Miután a dinamikus tartalmak listája jelenik meg, válassza ki a **kötegelt elemek** token. | 
      ||| 

      ![Kötegelt kódolása művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Az a **elemek** mezőbe:

      ![Kötegelt kódolása máveleti elemek](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Mentse a logikai alkalmazást. 

7. Ha Visual Studio használata esetén győződjön meg arról, hogy [batch fogadó logikai alkalmazás üzembe helyezése az Azure-bA](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Ellenkező esetben a batch fogadó nem választható, ha a batch küldő létrehozása.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Ahhoz, hogy a batch-fogadó működik megfelelően, hozzáadhat egy HTTP-művelet tesztelési célokra, és a kötegelt üzenet küldése a [kérelem Bin szolgáltatás](https://requestbin.fullcontact.com/). 

1. A X12 alatt kódolása a művelet, válassza a **új lépés**. 

2. A Keresés mezőbe írja be a "http" szűrőként. Válassza a következő műveletet: **HTTP - HTTP**
    
   ![Válassza ki a HTTP-művelet](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Állítsa be a tulajdonságokat a HTTP-művelet:

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Metódus** | Válassza ki a listáról, **POST**. | 
   | **URI-t** | A kérelem bin URI generálása, és adja meg az URI-ra ebben a mezőben. | 
   | **Törzs** | Ebben a mezőben kattintson, és miután megnyílik a dinamikus tartalmú listából, válassza ki a **törzs** jogkivonatot, amely megjelenik a szakaszt, **kötegelt kódolás a Szerződés neve szerint**. <p>Ha nem látja a **törzs** token melletti **kötegelt kódolás a Szerződés neve szerint**, jelölje be **Továbbiak**. | 
   ||| 

   ![Adja meg a HTTP-művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Mentse a logikai alkalmazást. 

   A batch fogadó logikai alkalmazás az alábbihoz hasonlít: 

   ![A batch fogadó logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Hozzon létre X12 batch-feladó

Most hozzon létre egy vagy több logikai alkalmazásokat, amelyek üzeneteket küldeni a batch fogadó logikai alkalmazást. Minden egyes batch küldő a batch fogadó logikai alkalmazás és a batch-név, üzenet tartalma és a további beállításokat kell megadni. A batch osztani részekre gyűjtheti az üzeneteket ezzel a kulccsal egyedi partíciókulccsal is megadhat. 

* Győződjön meg arról, hogy már [létrehozott batch fogadó](#receiver) , a batch küldő létrehozásakor kiválaszthatja a cél kötegelt a már meglévő batch fogadó. Batch fogadók nem batch feladók ismernie kell, amíg a batch feladók tudnia kell, hova küldhetők üzenetek. 

* Ellenőrizze, hogy a batch fogadó és a batch küldő megosztása megegyező Azure-régióban *és* Azure-előfizetést. Ha nem, a batch fogadó a batch küldő létrehozásakor, mert azok még nem látható, egymáshoz nem választhat.

1. Egy másik logikai alkalmazás létrehozása ezen a néven: "SendX12MessagesToBatch" 

2. A Keresés mezőbe írja be a "Amikor egy http-kérelem" szűrőként. Válassza ki a következő eseményindítót: **amikor egy HTTP-kérés fogadásakor.** 
   
   ![A kérelem típusú trigger hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Üzeneteket küld el egy kötegelt művelet hozzáadása lehetőséget.

   1. A HTTP-kérelem művelet alatt válassza **új lépés**.

   2. A Keresés mezőbe írja be a "batch" szűrőként. 
   Válassza ki a **műveletek** listában, és válassza ki a következő műveletet: **válassza ki egy kötegtriggert - kötegelendő üzenetek küldése a Logic Apps-munkafolyamatot**

      ![Válassza a "Válasszon egy kötegtriggert tartalmazó Logic Apps-munkafolyamatot"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Most válassza ki a korábban létrehozott "BatchX12Messages" logikai alkalmazás.

      ![Válassza ki a "batch fogadó" logikai alkalmazás](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Válassza a következő műveletet: **Batch_messages - <*a batch-fogadó*>**

      !["Batch_messages" művelet kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Állítsa be a batch-feladó tulajdonságait.

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Kötegnév** | A batch-nevét, a fogadó logikai alkalmazást, amely ebben a példában "TestBatch" által definiált <p>**Fontos**: batch-név futásidőben beolvassa érvényesítve, és meg kell egyeznie a fogadó logikai alkalmazás által megadott név. A batch nevének módosítása hatására a batch küldő sikertelen. | 
   | **Üzenet tartalma** | A tartalom is szeretne küldeni, az üzenet, amely a **törzs** token ebben a példában | 
   ||| 
   
   ![Batch-tulajdonságainak megadása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Mentse a logikai alkalmazást. 

   A batch küldő logikai alkalmazás az alábbihoz hasonlít:

   ![A batch küldő logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logic apps tesztelése

A kötegelés megoldás teszteléséhez post X12 küldött üzeneteket a batch küldő logikai alkalmazás a [Postman](https://www.getpostman.com/postman) vagy egy hasonló eszközt. Hamarosan, megkezdődik az X12 üzenetek a kérelem van, vagy 10 percenként vagy 10, ugyanazzal a partíciókulccsal rendelkező összes kötegekben.

## <a name="next-steps"></a>További lépések

* [Üzenetek feldolgozásával kötegként](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
