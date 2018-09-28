---
title: Kötegelt feldolgozási üzenetek egy csoport vagy a gyűjtemény – Azure Logic Apps |} A Microsoft Docs
description: Üzenetek küldése és fogadása az Azure Logic Appsben kötegként
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: bd31de8f60fff5630141f708714083fe76220d11
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410153"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Küldés, fogadhatja és kötegelt feldolgozási üzenetek az Azure Logic Appsben

Küldéséhez és üzenetek feldolgozása együttesen a meghatározott módon csoportokként, a kötegelés megoldás, amely gyűjti az üzeneteket hozhat létre egy *batch* mindaddig, amíg az adott feltételek teljesülése ad ki, és a kötegelt üzenetek feldolgozására. Kötegelés csökkentheti a logikai alkalmazás milyen gyakran üzenetet feldolgozza. Ez a cikk bemutatja, hogyan hozhat létre a kötegelés megoldást ugyanazon Azure-előfizetésen belül, két logikai alkalmazások létrehozásával az Azure-régióban, és a konkrét sorrendje a következő: 

* A ["batch fogadó"](#batch-receiver) logikai alkalmazást, amely fogadja, és gyűjti az üzeneteket, a batch a megadott feltételeknek megfelelő teljesüléséig ad ki, és ezeket az üzeneteket feldolgozásához.

  Ellenőrizze, hogy először hozzon létre a batch fogadó ezért kiválaszthatja a batch cél később a batch küldő létrehozásakor.

* Egy vagy több ["batch küldő"](#batch-sender) logikai alkalmazások, amely üzeneteket küld a korábban létrehozott batch-fogadó. 

   Megadhat egyedi kulcs, például egy ügyfél száma is, amely *partíciók* vagy osztja fel a cél batch logikai részekre a kulcs alapján. Ezzel a módszerrel a fogadó alkalmazás azonos kulccsal rendelkező összes elem összegyűjtheti, és együtt feldolgozni azokat.

Győződjön meg arról, hogy a batch fogadó és a batch küldő megosztása az Azure-előfizetéshez *és* az Azure-régióban. Ha nem, a batch fogadó a batch küldő létrehozásakor, mert azok még nem látható, egymáshoz nem választhat.

## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a példában, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Másik lehetőségként [regisztrálhat egy használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Egy e-mail-fiók összes [Azure Logic Apps által támogatott e-mail-szolgáltatónál](../connectors/apis-list.md)

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Szeretné használni az Azure portal helyett a Visual Studióban, győződjön meg arról, hogy [beállítása a Visual Studio használata a Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>A batch fogadó létrehozása

Üzeneteket küldhet egy kötegben, a batch először léteznie kell a célhelyeként, ahol ezeket az üzeneteket küld. Ezért először létre kell hoznia a "batch fogadó" logikai alkalmazást, amely kezdődik a **Batch** eseményindító. Ezzel a módszerrel a "batch küldő" logikai alkalmazás létrehozásakor kiválaszthatja a batch fogadó logikai alkalmazást. A batch fogadó továbbra is gyűjtése üzeneteket a megadott feltételeknek megfelelő teljesüléséig ad ki, és ezeket az üzeneteket feldolgozásához. Batch fogadók nem batch feladók ismernie kell, amíg a batch feladók ismernie kell a cél, ahol az üzeneteket küldenek. 

1. Az a [az Azure portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy logikai alkalmazást ezen a néven: "BatchReceiver" 

2. A Logic Apps Designer, adja hozzá a **Batch** eseményindító, amely elindítja a logikai alkalmazás munkafolyamatának. A Keresés mezőbe írja be a "batch" szűrőként. Válassza ki a következő eseményindítót: **Batch-üzenetek**

   ![Adja hozzá a "Batch-üzenetek" eseményindító](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Állítsa be a batch-fogadó tulajdonságokat: 

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Kötegelt mód** | - **Beágyazott**: az a batch eseményindítón belül kiadási feltételek meghatározása <br>- **Integrációs fiók**: használatával több kiadási feltételek konfiguráció meghatározása egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Az integrációs fiókot akkor is fenntartható egy helyen, nem pedig külön logic appsben ezeket a konfigurációkat. | 
   | **Kötegnév** | A neve, amely "TestBatch" Ebben a példában, és csak a batch **beágyazott** kötegelt mód |  
   | **Kiadási feltételek** | Csak **beágyazott** kötegelt módban, és kijelöli a feltételeket minden egyes köteg feldolgozása előtt kielégítése érdekében: <p>- **Message count alapú**: 10 üzenetet a kötegben, például gyűjthet üzenetek száma <br>- **Méretalapú**: A Köteg maximális mérete bájtban, például 100 MB <br>- **Ütemezésalapú**: időközét és gyakoriságát között a batch-kiadások, például 10 perc. A minimális ismétlődési 60 másodperc vagy 1 percre. Percenkénti kitalálva hatékonyan 1 percre van kerekítve. Adja meg a kezdő dátum és idő, válassza a **speciális beállítások megjelenítése**. <br>- **Válassza ki az összes**: a megadott feltétel alapján. | 
   ||| 
   
   Ebben a példában az összes feltétel kiválasztása:

   ![Adja meg a Batch a trigger részletei](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Adjon hozzá egy vagy több műveletet, és minden egyes kötegelt feldolgozására. 

   Ebben a példában adjon hozzá egy műveletet, amely e-mailt küld, amikor a batch-eseményindító aktiválódik. 
   Az eseményindító fut, és a egy e-mailt küld, ha a köteg rendelkezik 10 üzenetet, eléri a 10 MB-ot, vagy 10 perc múlva adja át.

   1. A batch-eseményindító területén válassza a **új lépés**.

   2. A keresőmezőbe írja be szűrőként az „e-mail küldése” kifejezést.
   Az e-mail-szolgáltatóhoz alapján, válassza ki egy e-mail-összekötőt.
      
      Ha például rendelkezik egy személyes fiók, például @outlook.com vagy @hotmail.com, válassza az Outlook.com-összekötőt. 
      Ha rendelkezik egy Gmail-fiókba, válassza ki a Gmail-összekötőt. 
      Ebben a példában az Office 365 Outlookot használja. 

   3. Válassza a következő műveletet: **– e-mail küldése <*e-mail-szolgáltatónál*>**

      Példa:

      ![Válassza ki a "E-mail küldése" műveletet az e-mail-szolgáltatóhoz](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

6. Állítsa be a tulajdonságokat a hozzáadott művelet.

   * A **Címzett** mezőben adja meg a címzett e-mail-címét. 
   Tesztelési célokra használhatja a saját e-mail-címét.

   * Az a **tulajdonos** be, amikor a dinamikus tartalmak listája jelenik meg, válassza ki a **partíciónévre** mező.

     ![A dinamikus tartalmú listából válassza ki a "Partíció neve"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Egy későbbi szakasz ismerteti, amely elosztja a célként megadott köteg logikai részekre, amelyben üzeneteket küldhet az egyedi partíciókulccsal is megadhat. 
     Egyes a batch küldő logikai alkalmazás által létrehozott egyedi szám van. 
     A funkció lehetővé teszi a kötegek az több részhalmazainak, majd adja meg az egyes részhalmazok az Ön által megadott névvel.

     > [!IMPORTANT]
     > Egy partíció legfeljebb 5000 üzenetek vagy a 80 MB rendelkezik. Ha mindkét feltétel teljesül, a Logic Apps előfordulhat, hogy engedje a batch, akkor is, ha a megadott kiadási feltétel nem teljesül.

   * Az a **törzs** be, amikor a dinamikus tartalmak listája jelenik meg, válassza ki a **üzenetazonosító** mező. 

     A Logic Apps Designerben automatikusan hozzáad egy "mindegyikre" hurkot körül e-mail-küldési művelet, mert a művelet a kimeneti kezeli egy kötegelt helyett egy gyűjteményt az előző művelet a. 

     ![A "Törzs" válassza a "Üzenet azonosítója"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Mentse a logikai alkalmazást. Sikeresen létrehozott egy batch-fogadó.

    ![A logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Ha Visual Studio használata esetén győződjön meg arról, hogy [batch fogadó logikai alkalmazás üzembe helyezése az Azure-bA](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Ellenkező esetben a batch fogadó nem választható, ha a batch küldő létrehozása.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Hozzon létre batch-feladó

Most hozzon létre egy vagy több batch küldő logikai alkalmazásokat, amelyek üzeneteket küldeni a batch fogadó logikai alkalmazás. Minden egyes batch küldő a batch fogadó és a batch-név, üzenet tartalma és a további beállításokat kell megadni. A batch osztani az üzenetek ezzel a kulccsal gyűjtéséhez logikai részekre egyedi partíciókulccsal is megadhat. 

* Győződjön meg arról, hogy már [létrehozott batch fogadó](#batch-receiver) , a batch küldő létrehozásakor kiválaszthatja a cél kötegelt a már meglévő batch fogadó. Batch fogadók nem batch feladók ismernie kell, amíg a batch feladók tudnia kell, hova küldhetők üzenetek. 

* Ellenőrizze, hogy a batch fogadó és a batch küldő megosztása megegyező Azure-régióban *és* Azure-előfizetést. Ha nem, a batch fogadó a batch küldő létrehozásakor, mert azok még nem látható, egymáshoz nem választhat.

1. Egy másik logikai alkalmazás létrehozása ezen a néven: "BatchSender"

   1. A keresőmezőbe írja be a "recurrence" szűrőként. 
   Válassza ki a következő eseményindítót: **ismétlődés - ütemezés**

      ![Az "Ismétlődés – ütemezés" eseményindító hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Adja a gyakoriságát és a küldő futtatási időköze a logikai alkalmazás percenként.

      ![Gyakorisággal és időközzel ismétlődési trigger beállítása](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Adjon hozzá egy új műveletet üzenetek küldése az egy kötegben.

   1. Az ismétlődési eseményindító területén válassza a **új lépés**.

   2. A Keresés mezőbe írja be a "batch" szűrőként. 
   Válassza ki a **műveletek** listában, és válassza ki a következő műveletet: **válassza ki egy kötegtriggert - kötegelendő üzenetek küldése a Logic Apps-munkafolyamatot**

      ![Válassza a "Válasszon egy kötegtriggert tartalmazó Logic Apps-munkafolyamatot"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Válassza ki a batch fogadó logikai alkalmazást, amelyet korábban hozott létre.

      ![Válassza ki a "batch fogadó" logikai alkalmazás](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista minden más logikai alkalmazás kötegtriggereket rendelkező is bemutatja. 
      > 
      > Ha Visual Studio használata, és nem lát minden batch fogadók, jelölje be, ellenőrizze, hogy az Azure-ban üzembe helyezett batch fogadó. Ha még nem, megtudhatja, hogyan [batch fogadó logikai alkalmazás üzembe helyezése az Azure-bA](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Válassza a következő műveletet: **Batch_messages - <*a batch-fogadó*>**

      ![Válassza a következő műveletet: "Batch_messages - < saját-logic-app >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Állítsa be a batch-feladó tulajdonságai:

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Kötegnév** | A batch-nevét, a fogadó logikai alkalmazást, amely ebben a példában "TestBatch" által definiált <p>**Fontos**: batch-név futásidőben beolvassa érvényesítve, és meg kell egyeznie a fogadó logikai alkalmazás által megadott név. A batch nevének módosítása hatására a batch küldő sikertelen. | 
   | **Üzenet tartalma** | A tartalom az elküldendő üzenet | 
   ||| 

   Ebben a példában a kifejezés, amely az aktuális dátum és idő szúr be a batch küldendő üzenet tartalma hozzáadása:

   1. Kattintson a **üzenet tartalom** mezőbe. 

   2. Ha a dinamikus tartalmak listája jelenik meg, válassza ki a **kifejezés**. 

   3. Adja meg a kifejezés `utcnow()`, és válassza a **OK**. 

      ![A "Üzenet tartalom" válassza az "Expression", "utcnow()" adja meg és válassza az "OK gombra".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Most állítsa be a Batch egy partíciót. A "BatchReceiver" műveletet válassza **speciális beállítások megjelenítése** és állítsa be ezeket a tulajdonságokat:

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Partíció neve** | Egy nem kötelező egyedi partíciókulccsal értékkel való osztásának a célként használandó logikai részekre batch- és gyűjtheti az üzeneteket a kulcs alapján | 
   | **Üzenet azonosítója** | Egy választható üzenet azonosítója, amely egy előállított globálisan egyedi azonosítóra (GUID), ha üres | 
   ||| 

   Ebben a példában a a **partíciónévre** adjon hozzá egy kifejezés, amely egy és 5 között véletlenszerűen állít elő. Hagyja a **üzenetazonosító** mező üres.
   
   1. Kattintson a **partíciónévre** mezőre, hogy a dinamikus tartalmak listája jelenik meg. 

   2. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.
   
   3. Adja meg a kifejezés `rand(1,6)`, és válassza a **OK**.

      ![Állítsa be a cél Batch partíció](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Ez **rand** függvény létrehoz egy és 5 közötti számot. 
      Tehát ebben a kötegben vannak osztásával öt számozott partíció, amely ebben a kifejezésben dinamikusan állítja be.

5. Mentse a logikai alkalmazást. Küldő logikai alkalmazás most az alábbihoz hasonlít:

   ![A küldő logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logic apps tesztelése

A kötegelés megoldás teszteléséhez, hagyja a logikai alkalmazások futtatása néhány percet. Hamarosan, megkezdődik az e-mailek öt ugyanazzal a partíciókulccsal rendelkező összes csoportokban.

A batch küldő logikai alkalmazás minden percben futni fog, egy és 5 között véletlenszerűen állít elő, és a létrehozott számot használja a célként megadott köteg partíciókulcsként, ahol az üzenetek küldése történik. Minden alkalommal, amikor a köteg öt elemek ugyanazzal a partíciókulccsal, a batch fogadó logikai alkalmazás akkor aktiválódik, majd az egyes üzenetekhez levelet küld.

> [!IMPORTANT]
> Ha végzett tesztelése, győződjön meg arról, hogy tiltsa le a BatchSender logikai alkalmazás üzenetek küldésének leállítására, és elkerülheti a Beérkezett üzenetek terhelve.

## <a name="next-steps"></a>További lépések

* [EDI-üzenetek kötegelése és küldése](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Logikaialkalmazás-definíciók alapján JSON használatával hozhat létre](../logic-apps/logic-apps-author-definitions.md)
* [A Visual Studióban, az Azure Logic Apps és Functions kiszolgáló nélküli alkalmazás készítése](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Kivételkezelés és a hibanaplózás logikai alkalmazásokhoz](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
