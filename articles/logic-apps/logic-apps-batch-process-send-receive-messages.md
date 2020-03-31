---
title: Kötegelt folyamatüzenetek csoportként
description: Üzenetek küldése és fogadása csoportokban a munkafolyamatok között kötegelt feldolgozással az Azure Logic Apps-ben
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: e48d2bb2ffce0dd4f9293417534165165d426784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666754"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Üzenetek küldése, fogadása és kötegelt feldolgozása az Azure Logic Apps alkalmazásban

Ha az üzeneteket egy adott módon csoportokként szeretné elküldeni és feldolgozni, létrehozhat egy kötegelt adatkötegbe gyűjtő kötegelt küldési *megoldást,* amíg a megadott feltételek nem teljesülnek a kötegelt üzenetek kiadásához és feldolgozásához. Kötegelés csökkentheti, hogy milyen gyakran a logikai alkalmazás feldolgozza az üzeneteket. Ez a cikk bemutatja, hogyan hozhat létre kötegelési megoldást két logikai alkalmazás létrehozásával ugyanazon az Azure-előfizetésen, az Azure-régióban, és az alábbi sorrendben: 

* A ["batch receiver"](#batch-receiver) logikai alkalmazás, amely elfogadja és összegyűjti az üzeneteket egy kötegbe, amíg a megadott feltételek teljesülnek az üzenetek felszabadítása és feldolgozása.

  Győződjön meg arról, hogy először hozza létre a kötegfogadót, hogy később kiválaszthassa a kötegcélt a kötegküldő létrehozásakor.

* Egy vagy több ["kötegküldő"](#batch-sender) logikai alkalmazás, amely elküldi az üzeneteket a korábban létrehozott kötegfogadónak. 

   Megadhat egy egyedi kulcsot is, például egy ügyfélszámot, amely *a célköteget az* adott kulcs alapján logikai részhalmazokra osztja, vagy felosztja azokat. Így a fogadó alkalmazás összegyűjtheti az összes elemet ugyanazzal a kulccsal, és együtt feldolgozhatja őket.

Győződjön meg arról, hogy a kötegelt fogadó és a kötegelt feladó ugyanazt az Azure-előfizetést *és* Az Azure-régiót osztja. Ha nem, akkor nem választhatja ki a kötegelt fogadót a kötegküldő létrehozásakor, mert azok nem láthatók egymás számára.

## <a name="prerequisites"></a>Előfeltételek

A példa követéséhez a következő elemekre van szükség:

* Azure-előfizetés. Ha nem rendelkezik [előfizetéssel, kezdheti egy ingyenes Azure-fiókkal.](https://azure.microsoft.com/free/) Vagy [regisztráljon egy kiosztón-fel- már-előfordulhat rakoncátor előfizetésre.](https://azure.microsoft.com/pricing/purchase-options/)

* E-mail fiók az [Azure Logic Apps által támogatott bármely e-mail szolgáltatóval](../connectors/apis-list.md)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Ha az Azure Portal helyett a Visual Studio-t szeretné használni, győződjön meg arról, hogy [a Visual Studio-t a Logic Apps használatára állította be.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Kötegfogadó létrehozása

Mielőtt üzeneteket küldhetne egy kötegnek, a kötegnek először meg kell lennie az üzenetek küldésének céljaként. Tehát először létre kell hoznia a "batch receiver" logikai alkalmazást, amely a **Batch** eseményindítóval kezdődik. Így a "kötegelt küldő" logikai alkalmazás létrehozásakor kiválaszthatja a kötegelt fogadó logikai alkalmazást. A kötegelt fogadó folytatja az üzenetek gyűjtését, amíg a megadott feltételek nem teljesülnek az üzenetek kiadására és feldolgozására. Bár a kötegelt fogadóknak nem kell semmit tudniuk a kötegelt feladókról, a kötegelt feladóknak ismerniük kell az üzenetek küldésének helyét. 

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio-ban hozzon létre egy logikai alkalmazást ezzel a névvel: "BatchReceiver" 

2. A Logic Apps Designerben adja hozzá a **Batch** eseményindítót, amely elindítja a logikai alkalmazás munkafolyamatát. A keresőmezőbe írja be szűrőként a "batch" szót. Válassza ki ezt az eseményindítót: **Kötegelt üzenetek**

   !["Batch üzenetek" eseményindító hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Állítsa be ezeket a tulajdonságokat a kötegelt vevőhöz: 

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Kötegelt mód** | - **Szövegközi**: A kötegeseményindítón belüli kiadási feltételek meghatározásához <br>- **Integrációs fiók**: Több kiadási feltétel konfigurációjának [definiálása integrációs fiókon](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)keresztül . Integrációs fiókkal ezeket a konfigurációkat egy helyen, nem pedig külön logikai alkalmazásokban tarthatja fenn. | 
   | **Köteg neve** | A köteg neve, amely ebben a példában a "TestBatch" név, és csak a **szövegközi** kötegmódra vonatkozik |  
   | **Kiadási feltételek** | Csak a **Szövegközi** kötegmódra vonatkozik, és kiválasztja azokat a feltételeket, amelyeknek meg kell felelnie az egyes kötegek feldolgozása előtt: <p>- **Üzenetszám alapján:** Engedje fel a köteget a köteg által gyűjtött üzenetek száma alapján. <br>- **Méret alapú:** Engedje fel a köteget a köteg által gyűjtött összes üzenet teljes bájtban lévő mérete alapján. <br>- **Ütemezés**: Engedje fel a köteget egy ismétlődési ütemezés alapján, amely egy intervallumot és gyakoriságot határoz meg. A speciális beállításokban kijelölhet egy időzónát is, és megadhatja a kezdő dátumot és az időt. <br>- **Az összes kijelölése**: Használja az összes megadott feltételt. | 
   | **Üzenetek száma** | A kötegben begyűjtandó üzenetek száma, például 10 üzenet. A kötegek határa 8000 üzenet. | 
   | **Köteg mérete** | A kötegben összegyűjtésre váró teljes méret bájtban, például 10 MB. A köteg méretkorlátja 80 MB. | 
   | **Ütemezés** | A kötegfelszabadítások közötti időköz és gyakoriság, például 10 perc. A minimális ismétlődés 60 másodperc vagy 1 perc. A törtperceket hatékonyan 1 percre kerekítjük fel. Időzóna vagy kezdési dátum és idő megadásához válassza **a Speciális beállítások megjelenítése**lehetőséget. | 
   ||| 

   > [!NOTE]
   > 
   > Ha módosítja a kiadási feltételeket, miközben az eseményindító még kötegelt, de el nem küldött üzeneteket tartalmaz, az eseményindító a frissített kiadási feltételeket használja az el nem küldött üzenetek kezeléséhez. 

   Ez a példa az összes kritériumot mutatja, de a saját teszteléshez csak egy feltételt próbáljon meg:

   ![Kötegelt eseményindító részleteinek biztosítása](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Most adjon hozzá egy vagy több műveletet, amelyek minden köteget feldolgoznak. 

   Ebben a példában adjon hozzá egy műveletet, amely e-mailt küld, amikor a köteg aktiválódik. 
   Az eseményindító fut, és küld egy e-mailt, ha a köteg vagy 10 üzenetet tartalmaz, eléri a 10 MB-ot, vagy 10 perc elteltével.

   1. A kötegeseményindító alatt válassza az **Új lépés lehetőséget.**

   2. A keresőmezőbe írja be szűrőként az „e-mail küldése” kifejezést.
   Az e-mail szolgáltató alapján válasszon egy e-mail-összekötőt.

      Ha például személyes fiókkal rendelkezik, például @outlook.com vagy @hotmail.comválassza ki a Outlook.com összekötőt. 
      Ha rendelkezik Gmail-fiókkal, válassza a Gmail-összekötőt. 
      Ez a példa az Office 365 Outlookot használja. 

   3. Válassza a következő műveletet: **E-mail küldése – <*e-mail szolgáltató* > **

      Példa:

      ![Válassza az "E-mail küldése" művelet et az e-mail szolgáltatószámára](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

6. Állítsa be a hozzáadott művelet tulajdonságait.

   * A **Címzett** mezőben adja meg a címzett e-mail-címét. 
   Tesztelési célokra használhatja a saját e-mail-címét.

   * A **Tárgy mezőben,** amikor megjelenik a dinamikus tartalomlista, jelölje be a **Partíció neve** mezőt.

     ![A dinamikus tartalomlistában válassza a "Partíció neve" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Később a kötegküldőben megadhat egy egyedi partíciókulcsot, amely a célköteget logikai részhalmazokra osztja, ahol üzeneteket küldhet. 
     Minden készlet rendelkezik egy egyedi számot, amely a kötegelt küldő logikai alkalmazás által generált. 
     Ez a funkció lehetővé teszi, hogy egyetlen köteget használjon több részhalmazsal, és minden egyes részhalmazt a megadott névvel határozzon meg.

     > [!IMPORTANT]
     > A partíció legfeljebb 5000 üzenetből vagy 80 MB-os korláttal rendelkezik. Ha bármelyik feltétel teljesül, a Logic Apps felszabadíthatja a köteget, még akkor is, ha a megadott kiadási feltétel nem teljesül.

   * A **Törzs mezőben,** amikor megjelenik a dinamikus tartalomlista, jelölje be az **Üzenetazonosító mezőt.** 

     A Logic Apps Designer automatikusan hozzáad egy "Minden" hurkot az e-mail küldése művelet körül, mert ez a művelet az előző művelet kimenetét gyűjteményként, nem pedig kötegként kezeli. 

     ![A "Törzs" területen válassza az "Üzenetazonosító" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Mentse a logikai alkalmazást. Most létrehozott egy kötegfogadót.

    ![A logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Ha a Visual Studio-t használja, telepítse [a kötegelt vevő logikai alkalmazását az Azure-ba.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) Ellenkező esetben nem választhatja ki a kötegelt fogadót a kötegküldő létrehozásakor.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Kötegküldő létrehozása

Most hozzon létre egy vagy több kötegelt küldő logikai alkalmazásokat, amelyek üzeneteket küldenek a kötegelt fogadó logikai alkalmazásnak. Minden kötegküldőben megadhatja a kötegfogadót és a köteg nevét, az üzenet tartalmát és minden egyéb beállítást. Adott esetben megadhat egy egyedi partíciókulcsot, amely a köteget logikai részhalmazokba oszthatja az üzenetek ezzel a kulccsal történő gyűjtéséhez. 

* Győződjön meg arról, hogy már [létrehozta a kötegfogadót,](#batch-receiver) így a kötegküldő létrehozásakor kiválaszthatja a meglévő kötegfogadót célkötegként. Bár a kötegelt fogadóknak nem kell semmit tudniuk a kötegelt feladókról, a kötegelt feladóknak tudniuk kell, hogy hová küldjenek üzeneteket. 

* Győződjön meg arról, hogy a kötegelt fogadó és a kötegelt feladó ugyanazt az Azure-régiót *és* az Azure-előfizetést osztja. Ha nem, akkor nem választhatja ki a kötegelt fogadót a kötegküldő létrehozásakor, mert azok nem láthatók egymás számára.

1. Hozzon létre egy másik logikai alkalmazást ezzel a névvel: "BatchSender"

   1. A keresőmezőbe írja be szűrőként az "ismétlődés" szót. 
   Jelölje be ezt az eseményindítót: **Ismétlődés – Ütemezés**

      ![Adja hozzá az "Ismétlődés - Ütemezés" eseményindítót](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Állítsa be a gyakoriságot és az időközt a küldő logikai alkalmazás percenkénti futtatásához.

      ![Az ismétlődési esemény gyakoriságának és időközének beállítása](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Új művelet hozzáadása kötegbe küldött üzenetekhez.

   1. Az ismétlődési eseményindító alatt válassza az **Új lépés lehetőséget.**

   2. A keresőmezőbe írja be szűrőként a "batch" szót. 
   Válassza ki a **Műveletek** listát, majd válassza ki ezt a műveletet: **Válasszon egy logikai alkalmazások munkafolyamatot kötegelt eseményindítóval – Üzenetek küldése kötegelt**

      ![Válassza a "Logic Apps munkafolyamat kiválasztása kötegelt eseményindítóval" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Válassza ki a kötegelt fogadó logikai alkalmazást, amelyet korábban létrehozott.

      ![Válassza ki a "batch receiver" logikai alkalmazást](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista is megjeleníti a többi logikai alkalmazások, amelyek kötegelt eseményindítók. 
      > 
      > Ha a Visual Studio- t használja, és nem lát kiválasztható kötegelt fogadókat, ellenőrizze, hogy üzembe helyezte-e a kötegelt vevőt az Azure-ba. Ha még nem, [megtudhatja, hogyan telepítheti a kötegelt fogadó logikai alkalmazást az Azure-ba.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 

   4. Válassza ki ezt a műveletet: **Batch_messages - <a *kötegelt vevő* > **

      ![Válassza ki ezt a műveletet: "Batch_messages - <a logikai-alkalmazás>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. A kötegelt feladó tulajdonságainak beállítása:

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Köteg neve** | A fogadó logikai alkalmazás által meghatározott kötegnév, amely ebben a példában a "TestBatch" <p>**Fontos:** A kötegnév futásidőben érvényesítve lesz, és meg kell egyeznie a fogadó logikai alkalmazás által megadott névvel. A kötegnév módosítása a kötegküldő sikertelensét okozza. | 
   | **Üzenet tartalma** | Az elküldeni kívánt üzenet tartalma | 
   ||| 

   Ebben a példában adja hozzá ezt a kifejezést, amely az aktuális dátumot és időt szúrja be a kötegbe küldött üzenettartalomba:

   1. Kattintson az **Üzenet tartalma** mezőre. 

   2. Amikor megjelenik a dinamikus tartalomlista, válassza a **Kifejezés**lehetőséget. 

   3. Írja be `utcnow()`a kifejezést , majd kattintson az **OK gombra.** 

      ![Az "Üzenet tartalma" mezőbe válassza a "Kifejezés" lehetőséget, írja be az "utcnow()" ( "utcnow()" ( "UT") parancsot, és válassza az "OK" parancsot.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Most állítson be egy partíciót a köteghez. A "BatchReceiver" műveletben válassza a **Speciális beállítások megjelenítése** lehetőséget, és adja meg az alábbi tulajdonságokat:

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Partíció neve** | Opcionális egyedi partíciókulcs, amelya célköteg logikai részhalmazokra osztásához és az adott kulcson alapuló üzenetek gyűjtéséhez használható | 
   | **Üzenetazonosító** | Nem kötelező üzenetazonosító, amely üresen létrehozott globálisan egyedi azonosító (GUID) | 
   ||| 

   Ebben a példában a **Partíció neve** mezőben adjon hozzá egy kifejezést, amely egy és öt közötti véletlen számot hoz létre. Hagyja üresen az **Üzenetazonosító mezőt.**
   
   1. Kattintson a **Partíció neve** mezőbe, hogy a dinamikus tartalomlista megjelenjen. 

   2. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.
   
   3. Írja be `rand(1,6)`a kifejezést , majd kattintson az **OK gombra.**

      ![Partíció beállítása a célköteghez](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Ez **a rand** függvény egy és öt közötti számot hoz létre. 
      Tehát ezt a köteget öt számozott partícióra osztja fel, amelyeket ez a kifejezés dinamikusan beállít.

5. Mentse a logikai alkalmazást. A feladó logikai alkalmazása most hasonlónak tűnik a következő példához:

   ![A feladó logikai alkalmazásának mentése](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logikai alkalmazások tesztelése

A kötegelési megoldás teszteléséhez hagyja futni a logikai alkalmazásokat néhány percig. Hamarosan elkezdi az e-maileket öt csoportban, mindegyik ugyanazzal a partíciós kulccsal.

A kötegelt küldő logikai alkalmazás percenként fut, létrehoz egy és öt közötti véletlen számot, és ezt a generált számot használja a célköteg partíciókulcsaként, ahol az üzeneteket küldik. Minden alkalommal, amikor a köteg öt elem ugyanazzal a partíciókat kulccsal rendelkezik, a kötegelt fogadó logikai alkalmazás aktiválódik, és minden egyes üzenethez küld e-mailt.

> [!IMPORTANT]
> Ha végzett a teszteléssel, győződjön meg arról, hogy letiltja a BatchSender logikai alkalmazást az üzenetek küldésének leállításához és a beérkező üzenetek túlterhelésének elkerüléséhez.

## <a name="next-steps"></a>További lépések

* [EDI-üzenetek kötegelése és küldése](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Logic app-definíciókra építve a JSON használatával](../logic-apps/logic-apps-author-definitions.md)
* [Kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban az Azure Logic-alkalmazásokkal és -funkciókkal](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [A logikai alkalmazások kivételkezelése és hibanaplózása](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
