---
title: Az EDI-üzenetek kötegelt feldolgozása csoportként
description: EDI-üzenetek küldése és fogadása kötegként, csoportként vagy gyűjteményként kötegelt feldolgozással az Azure Logic Apps-ben
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666669"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>EDI-üzenetek cseréje kötegként vagy csoportokként a kereskedelmi partnerek között az Azure Logic Apps alkalmazásban

A vállalkozások közötti (B2B) forgatókönyvekben a partnerek gyakran cserélnek üzeneteket csoportokban vagy *kötegekben.* Amikor kötegelési megoldást hoz létre a Logic Apps alkalmazásokkal, üzeneteket küldhet a kereskedelmi partnereknek, és kötegekben feldolgozhatja ezeket az üzeneteket. Ez a cikk bemutatja, hogyan kötegelheti fel az EDI-üzenetek feldolgozását, példaként az X12 használatával egy "kötegelt küldő" logikai alkalmazás és egy "kötegelt fogadó" logikai alkalmazás létrehozásával. 

Az X12-üzenetek kötegelése más üzenetek kötegeléséhez hasonlóan működik; olyan kötegeseményindítót használ, amely az üzeneteket kötegbe gyűjti, és egy kötegelt műveletet, amely üzeneteket küld a kötegnek. Az X12 kötegelés egy X12 kódolási lépést is tartalmaz, mielőtt az üzenetek a kereskedelmi partnerhez vagy más rendeltetési helyre kerülnek. Ha többet szeretne megtudni a kötegelőprogramról és a műveletről, olvassa el a [Kötegelt feldolgozási üzenetek című témakört.](../logic-apps/logic-apps-batch-process-send-receive-messages.md)

Ebben a cikkben kötegelési megoldást hozhat létre két logikai alkalmazás létrehozásával ugyanazon az Azure-előfizetésen, az Azure-régióban, és ezt a konkrét sorrendet követve:

* A ["batch receiver"](#receiver) logikai alkalmazás, amely elfogadja és összegyűjti az üzeneteket egy kötegbe, amíg a megadott feltételek teljesülnek az üzenetek felszabadítása és feldolgozása. Ebben a forgatókönyvben a kötegfogadó is kódolja az üzeneteket a kötegben a megadott X12-megállapodás vagy partner identitások használatával.

  Győződjön meg arról, hogy először hozza létre a kötegfogadót, hogy később kiválaszthassa a kötegcélt a kötegküldő létrehozásakor.

* A ["batch sender"](#sender) logikai alkalmazás, amely elküldi az üzeneteket a korábban létrehozott kötegelt fogadó. 

Győződjön meg arról, hogy a kötegelt fogadó és a kötegelt feladó ugyanazt az Azure-előfizetést *és* Az Azure-régiót osztja. Ha nem, akkor nem választhatja ki a kötegelt fogadót a kötegküldő létrehozásakor, mert azok nem láthatók egymás számára.

## <a name="prerequisites"></a>Előfeltételek

A példa követéséhez a következő elemekre van szükség:

* Azure-előfizetés. Ha nem rendelkezik [előfizetéssel, kezdheti egy ingyenes Azure-fiókkal.](https://azure.microsoft.com/free/) Vagy [regisztráljon egy kiosztón-fel- már-előfordulhat rakoncátor előfizetésre.](https://azure.microsoft.com/pricing/purchase-options/)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az Azure-előfizetéshez társított és a logikai alkalmazásokhoz társított meglévő [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Legalább két meglévő [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) az integrációs fiókban. Minden partnernek az X12 (Standard Carrier Alpha Code) minősítőt kell üzleti identitásként használnia a partner tulajdonságaiközött.

* Meglévő [X12-megállapodás](../logic-apps/logic-apps-enterprise-integration-x12.md) az integrációs fiókban

* Ha az Azure Portal helyett a Visual Studio-t szeretné használni, győződjön meg arról, hogy [a Visual Studio-t a Logic Apps használatára állította be.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12 kötegfogadó létrehozása

Mielőtt üzeneteket küldhetne egy kötegnek, a kötegnek először meg kell lennie az üzenetek küldésének céljaként. Tehát először létre kell hoznia a "batch receiver" logikai alkalmazást, amely a **Batch** eseményindítóval kezdődik. Így a "kötegelt küldő" logikai alkalmazás létrehozásakor kiválaszthatja a kötegelt fogadó logikai alkalmazást. A kötegelt fogadó folytatja az üzenetek gyűjtését, amíg a megadott feltételek nem teljesülnek az üzenetek kiadására és feldolgozására. Bár a kötegelt fogadóknak nem kell semmit tudniuk a kötegelt feladókról, a kötegelt feladóknak ismerniük kell az üzenetek küldésének helyét. 

Ehhez a kötegelt fogadóhoz adja meg a kötegmódot, a nevet, a kiadási feltételeket, az X12-szerződést és az egyéb beállításokat. 

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio-ban hozzon létre egy logikai alkalmazást a következő névvel: "BatchX12Messages"

2. [Kapcsolja össze a logikai alkalmazást az integrációs fiókkal.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

3. A Logic Apps Designerben adja hozzá a **Batch** eseményindítót, amely elindítja a logikai alkalmazás munkafolyamatát. A keresőmezőbe írja be szűrőként a "batch" szót. Válassza ki ezt az eseményindítót: **Kötegelt üzenetek**

   ![Kötegeseményindító hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. A kötegelt vevő tulajdonságainak beállítása: 

   | Tulajdonság | Érték | Megjegyzések | 
   |----------|-------|-------|
   | **Kötegelt mód** | Beágyazott |  |  
   | **Köteg neve** | TestBatch (TestBatch) | Csak a **Szövegközi** kötegelt módban érhető el | 
   | **Kiadási feltételek** | Üzenetszám alapú, Ütemezés alapú | Csak a **Szövegközi** kötegelt módban érhető el | 
   | **Üzenetek száma** | 10 | Csak **az üzenetszám-alapú** kiadási feltételekkel érhető el | 
   | **Intervallum** | 10 | Csak **ütemezésalapú** kiadási feltételekkel érhető el | 
   | **Frekvencia** | minute | Csak **ütemezésalapú** kiadási feltételekkel érhető el | 
   ||| 

   ![Kötegelőindító részleteinek biztosítása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Ez a példa nem állít be partíciót a köteghez, így minden köteg ugyanazt a partíciókulcsot használja. A partíciókról a [Kötegelt feldolgozási üzenetek (Batch process messages) (Kötegelt feldolgozási üzenetek) (Kötegelt feldolgozási üzenetek) (Kötegelt feldolgozási](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)

5. Most adjon hozzá egy műveletet, amely kódolja az egyes kötegeket: 

   1. A kötegeseményindító alatt válassza az **Új lépés lehetőséget.**

   2. A keresőmezőbe írja be szűrőként az "X12 batch" értéket, és válassza ki ezt a műveletet (bármely verzió): **A kötegelt kódolja <*> verziót* - X12** 

      ![X12 kötegkódolási művelet kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Ha korábban nem csatlakozott az integrációs fiókhoz, hozza létre most a kapcsolatot. Adja meg a kapcsolat nevét, válassza ki a kívánt integrációs fiókot, és válassza a **Létrehozás gombot.**

      ![Kapcsolat létrehozása a kötegkódoló és az integrációs fiók között](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Állítsa be ezeket a tulajdonságokat a kötegelőkódoló művelethez:

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Az X12-megállapodás neve** | Nyissa meg a listát, és válassza ki a meglévő szerződést. <p>Ha a lista üres, győződjön meg [arról, hogy csatolja a logikai alkalmazást az integrációs fiók,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) amely a kívánt megállapodást. | 
      | **Kötegneve** | Kattintson ebbe a mezőbe, és miután a dinamikus tartalomlista megjelenik, válassza a **Kötegnév** jogkivonatot. | 
      | **Partícióneve** | Kattintson ebbe a mezőbe, és miután a dinamikus tartalomlista megjelenik, válassza a **Partíció név** tokent. | 
      | **Elemek** | Zárja be az elem részletei jelölőnégyzetet, majd kattintson ebbe a mezőbe. A dinamikus tartalomlista megjelenítése után válassza a **Kötegelt elemek** token. | 
      ||| 

      ![Kötegkódolási művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Az **Elemek** mezőben:

      ![Kötegkódolási műveletelemek](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Mentse a logikai alkalmazást. 

7. Ha a Visual Studio-t használja, telepítse [a kötegelt vevő logikai alkalmazását az Azure-ba.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) Ellenkező esetben nem választhatja ki a kötegelt fogadót a kötegküldő létrehozásakor.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak érdekében, hogy a kötegfogadó a várt módon működjön, hozzáadhat egy HTTP-műveletet tesztelési célokra, és elküldhet egy kötegelt üzenetet a [Kérelemraktár szolgáltatásnak.](https://requestbin.com/) 

1. Az X12 kódolási művelet csoportban válassza az **Új lépés lehetőséget.** 

2. A keresőmezőbe írja be szűrőként a "http" szót. Válassza a következő műveletet: **HTTP - HTTP**
    
   ![HTTP-művelet kijelölése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. A HTTP-művelet tulajdonságainak beállítása:

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Módszer** | Ebből a listából válassza a **POST**lehetőséget. | 
   | **Uri** | Hozzon létre egy URI-t a kérelemtárolóhoz, majd írja be ezt az URI-t ebbe a mezőbe. | 
   | **Törzs** | Kattintson ebbe a mezőbe, és a dinamikus tartalomlista megnyitása után válassza ki a **Törzs** token, amely megjelenik a **szakaszban, Batch kódolni megállapodás neve**. <p>Ha nem látja a **Törzs** jogkivonatot, a **Batch kódolva a szerződés neve**mellett lehetőséget válassza a **Tovább**lehetőség lehetőséget. | 
   ||| 

   ![HTTP-művelet részleteinek biztosítása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Mentse a logikai alkalmazást. 

   A kötegelt fogadó logikai alkalmazás így néz ki: 

   ![A kötegelt fogadó logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12 kötegküldő létrehozása

Most hozzon létre egy vagy több logikai alkalmazást, amelyek üzeneteket küldenek a kötegelt fogadó logikai alkalmazásnak. Minden kötegküldőben megadhatja a kötegelt vevő logikai alkalmazását és a kötegnevét, az üzenet tartalmát és minden egyéb beállítást. Adott esetben megadhat egy egyedi partíciókulcsot, amely felosztja a köteget részhalmazokba, hogy üzeneteket gyűjtsön az adott kulccsal. 

* Győződjön meg arról, hogy már [létrehozta a kötegfogadót,](#receiver) így a kötegküldő létrehozásakor kiválaszthatja a meglévő kötegfogadót célkötegként. Bár a kötegelt fogadóknak nem kell semmit tudniuk a kötegelt feladókról, a kötegelt feladóknak tudniuk kell, hogy hová küldjenek üzeneteket. 

* Győződjön meg arról, hogy a kötegelt fogadó és a kötegelt feladó ugyanazt az Azure-régiót *és* az Azure-előfizetést osztja. Ha nem, akkor nem választhatja ki a kötegelt fogadót a kötegküldő létrehozásakor, mert azok nem láthatók egymás számára.

1. Hozzon létre egy másik logikai alkalmazást ezzel a névvel: "SendX12MessagesToBatch" 

2. A keresőmezőbe írja be szűrőként a "http-kérelem esetén" kifejezést. Jelölje be ezt az **eseményindítót: HTTP-kérelem érkezésekor** 
   
   ![A kérelem eseményindítójának hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Művelet hozzáadása kötegbe küldött üzenetekhez.

   1. A HTTP-kérelem művelet csoportban válassza az **Új lépés lehetőséget.**

   2. A keresőmezőbe írja be szűrőként a "batch" szót. 
   Válassza ki a **Műveletek** listát, majd válassza ki ezt a műveletet: **Válasszon egy logikai alkalmazások munkafolyamatot kötegelt eseményindítóval – Üzenetek küldése kötegelt**

      ![Válassza a "Logic Apps munkafolyamat kiválasztása kötegelt eseményindítóval" lehetőséget.](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Most válassza ki a "BatchX12Messages" logikai alkalmazást, amelyet korábban létrehozott.

      ![Válassza ki a "batch receiver" logikai alkalmazást](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Válassza ki ezt a műveletet: **Batch_messages - <a *kötegelt vevő* > **

      !["Batch_messages" művelet kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Állítsa be a kötegelt feladó tulajdonságait.

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Köteg neve** | A fogadó logikai alkalmazás által meghatározott kötegnév, amely ebben a példában a "TestBatch" <p>**Fontos:** A kötegnév futásidőben érvényesítve lesz, és meg kell egyeznie a fogadó logikai alkalmazás által megadott névvel. A kötegnév módosítása a kötegküldő sikertelensét okozza. | 
   | **Üzenet tartalma** | Az elküldeni kívánt üzenet tartalma, amely ebben a példában a **Törzs** token | 
   ||| 
   
   ![Kötegtulajdonságok beállítása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Mentse a logikai alkalmazást. 

   A kötegelt küldő logikai alkalmazása a következő példával néz ki:

   ![A kötegelt küldő logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logikai alkalmazások tesztelése

A kötegelési megoldás teszteléséhez tegye közzé az X12-es üzeneteket a [postástól](https://www.getpostman.com/postman) vagy egy hasonló eszköztől a kötegelt küldő logikai alkalmazásában. Hamarosan elkezdi az X12-üzenetek et a kérelemtárolóban, akár 10 percenként, akár 10-es kötegekben, mindugyanazzal a partíciós kulccsal.

## <a name="next-steps"></a>További lépések

* [Üzenetek feldolgozása kötegként](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
