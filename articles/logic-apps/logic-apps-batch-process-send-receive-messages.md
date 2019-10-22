---
title: Kötegelt feldolgozási üzenetek csoportként – Azure Logic Apps
description: Üzenetek küldése és fogadása kötegként a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: b56a50fceec8ac6be966c0c58a82e94e0c977143
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680439"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Üzenetek küldése, fogadása és kötegelt feldolgozása Azure Logic Apps

Az üzenetek küldéséhez és feldolgozásához egy adott módon, a csoportoknak megfelelően létrehozhat egy kötegelt megoldást, amely az üzeneteket egy *kötegbe* gyűjti, amíg a megadott feltételek teljesülnek a kötegelt üzenetek felszabadításához és feldolgozásához. A kötegelt feldolgozással csökkentheti, hogy a logikai alkalmazás milyen gyakran dolgozza fel az üzeneteket. Ez a cikk bemutatja, hogyan hozhat létre kötegelt megoldást úgy, hogy két logikai alkalmazást hoz létre ugyanabban az Azure-előfizetésben, az Azure-régióban, és az adott sorrendet követve: 

* A ["batch receiver"](#batch-receiver) logikai alkalmazás, amely az üzeneteket egy kötegbe fogadja és gyűjti, amíg a megadott feltételek teljesülnek az üzenetek felszabadításához és feldolgozásához.

  Győződjön meg arról, hogy először létrehozza a Batch-fogadót, hogy később kiválassza a Batch-célt a Batch-feladó létrehozásakor.

* Egy vagy több ["batch feladó"](#batch-sender) logikai alkalmazás, amely az üzeneteket a korábban létrehozott batch-fogadónak küldi el. 

   Megadhat egy egyedi kulcsot is, például egy vevőszámot, a *partíciókat* , illetve a cél köteget logikai részhalmazokra osztja a kulcs alapján. Így a fogadó alkalmazás összegyűjtheti az összes elemet ugyanazzal a kulccsal, és együtt is feldolgozza azokat.

Győződjön meg arról, hogy a Batch-fogadó és a Batch-küldő ugyanazzal az Azure-előfizetéssel *és Azure-* régióval rendelkezik. Ha nem, akkor nem választhatja ki a Batch-fogadót a Batch-feladó létrehozásakor, mert azok nem láthatók egymás számára.

## <a name="prerequisites"></a>Előfeltételek

A példához a következő elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Vagy regisztráljon az utólagos elszámolású [előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* E-mail-fiók minden [Azure Logic apps által támogatott e-mail-szolgáltatóval](../connectors/apis-list.md)

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Ha a Visual studiót a Azure Portal helyett szeretné használni, győződjön meg róla, hogy a [Visual studiót a Logic apps használatára állítja be](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Batch-fogadó létrehozása

Ahhoz, hogy üzeneteket küldhessen egy kötegbe, a kötegnek először az üzeneteket küldő célhelyként kell lennie. Először létre kell hoznia a "batch-fogadó" logikai alkalmazást, amely a **Batch** -triggerrel kezdődik. Így a "batch Sender" logikai alkalmazás létrehozásakor kiválaszthatja a Batch-fogadó logikai alkalmazást. A Batch-fogadó folytatja az üzenetek gyűjtését, amíg a megadott feltételek teljesülnek az üzenetek felszabadításához és feldolgozásához. Habár a Batch-fogadóknak semmit nem kell tudniuk a Batch-küldők számára, a Batch-küldőknek ismerniük kell az üzeneteket küldő célhelyet. 

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy logikai alkalmazást a következő névvel: "BatchReceiver" 

2. A Logic Apps Designerben adja hozzá a **Batch** -triggert, amely elindítja a logikai alkalmazás munkafolyamatát. A keresőmezőbe írja be szűrőként a "batch" kifejezést. Válassza ki ezt az triggert: **Batch-üzenetek**

   !["Batch-üzenetek" trigger hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Adja meg a következő tulajdonságokat a Batch-fogadó számára: 

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Batch mód** | - **inline**: a kiadási feltételek meghatározása a Batch-triggeren belül <br>- **integrációs fiók**: több kiadási feltétel konfigurációjának definiálása egy [integrációs fiókon](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)keresztül. Integrációs fiókkal a konfigurációkat egy helyen kezelheti, nem pedig külön logikai alkalmazásokban. | 
   | **Köteg neve** | A Batch neve, amely ebben a példában a "TestBatch", és csak a **beágyazott** batch üzemmódra vonatkozik |  
   | **Kiadási feltételek** | Csak a **beágyazott** batch üzemmódra vonatkozik, és az egyes kötegek feldolgozása előtt kijelöli a feltételeknek megfelelő feltételeket: <p>- **üzenetek száma**: a Batch által gyűjtött üzenetek száma alapján szabadítsa fel a köteget. <br>- **mérete**: az adott köteg által gyűjtött összes üzenet bájtjainak teljes mérete alapján szabadítsa fel a köteget. <br>- **ütemezése**: a köteg felszabadítása egy ismétlődési ütemterv alapján, amely intervallumot és gyakoriságot határoz meg. A speciális beállítások területen kiválaszthatja az időzónát, és megadhatja a kezdő dátumot és időpontot is. <br>-  az**összes kijelölése**: használja az összes megadott feltételt. | 
   | **Üzenetek száma** | A kötegben begyűjteni kívánt üzenetek száma, például 10 üzenet. A kötegek korlátja 8 000 üzenet. | 
   | **Köteg mérete** | A kötegben összegyűjteni kívánt teljes méret (bájtban), például 10 MB. A kötegek maximális mérete 80 MB. | 
   | **Ütemezés** | A kötegek kiadásainak időköze és gyakorisága, például 10 perc. A minimális ismétlődés 60 másodperc vagy 1 perc. A töredékes percek gyakorlatilag 1 percre vannak kerekítve. Időzóna vagy kezdő dátum és idő megadásához válassza a **Speciális beállítások megjelenítése**lehetőséget. | 
   ||| 

   > [!NOTE]
   > 
   > Ha módosítja a kiadási feltételeket, miközben az trigger továbbra is kötegelt, de nem elküldött üzenetekkel rendelkezik, akkor az trigger a frissített kiadási feltételeket használja az el nem küldött üzenetek kezeléséhez. 

   Ez a példa az összes feltételt mutatja, de a saját teszteléséhez próbáljon ki csak egy feltételt:

   ![Batch-trigger részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Most adjon hozzá egy vagy több olyan műveletet, amely feldolgozza az egyes kötegeket. 

   Ebben a példában olyan műveletet adunk hozzá, amely e-mailt küld a Batch-eseményindító indításakor. 
   Az trigger fut, és e-mailt küld, ha a köteg 10 üzenettel rendelkezik, 10 MB-nyi vagy 10 perces bérlet után.

   1. A Batch-trigger alatt válassza az **új lépés**lehetőséget.

   2. A keresőmezőbe írja be szűrőként az „e-mail küldése” kifejezést.
   Az e-mail-szolgáltató alapján válasszon ki egy e-mail-összekötőt.

      Ha például személyes fiókkal rendelkezik, például @outlook.com vagy @hotmail.com, válassza ki a Outlook.com-összekötőt. 
      Ha rendelkezik Gmail-fiókkal, válassza a Gmail-összekötőt. 
      Ez a példa az Office 365 Outlookot használja. 

   3. Válassza ki ezt a műveletet: **E-mail küldése <*e-mail szolgáltatónak* >**

      Példa:

      ![Az "e-mail küldése" művelet kiválasztása az e-mail-szolgáltatóhoz](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

6. Adja meg a hozzáadott művelet tulajdonságait.

   * A **Címzett** mezőben adja meg a címzett e-mail-címét. 
   Tesztelési célokra használhatja a saját e-mail-címét.

   * A **tulajdonos** mezőben a dinamikus tartalmak listájának megjelenésekor válassza a **partíció neve** mezőt.

     ![A dinamikus tartalom listából válassza a "partíció neve" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Később a Batch-feladóban megadhat egy egyedi partíciót, amely a cél köteget a logikai részhalmazokra osztja, ahol üzeneteket küldhet. 
     Mindegyik készlethez tartozik egy egyedi szám, amelyet a Batch feladó logikai alkalmazás generál. 
     Ez a funkció lehetővé teszi, hogy egyetlen köteget használjon több alkészlettel, és definiálja az egyes részhalmazokat az Ön által megadott névvel.

     > [!IMPORTANT]
     > A partíciók 5 000 vagy 80 MB-os korláttal rendelkeznek. Ha bármelyik feltétel teljesül, Logic Apps a köteget is kiszabadíthatja, még akkor is, ha a megadott kiadási feltétel nem teljesül.

   * A **törzs** mezőben, amikor megjelenik a dinamikus tartalom lista, válassza az **üzenet azonosítója** mezőt. 

     A Logic Apps Designer automatikusan hozzáadja a "for each" hurkot az e-mail küldése művelethez, mert ez a művelet az előző művelet kimenetét kezeli gyűjteményként, nem pedig kötegként. 

     ![A "Body" elemnél válassza az "üzenet azonosítója" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Mentse a logikai alkalmazást. Ezzel létrehozott egy batch-fogadót.

    ![A logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Ha a Visual studiót használja, győződjön meg arról, hogy [üzembe helyezi a Batch-fogadó logikai alkalmazást az Azure-](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)ban. Ellenkező esetben a Batch-feladó létrehozásakor nem választhatja ki a Batch-fogadót.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Batch-feladó létrehozása

Most hozzon létre egy vagy több batch-küldő logikai alkalmazást, amely üzeneteket küld a Batch-fogadó logikai alkalmazásnak. Minden batch-feladóban meg kell adnia a Batch-fogadót és a köteg nevét, az üzenet tartalmát és az egyéb beállításokat. Megadhat egy egyedi partíciós kulcsot is, amellyel a köteget logikai részhalmazokra oszthatja, hogy üzeneteket gyűjtsön az adott kulccsal. 

* Győződjön meg arról, hogy már [létrehozta a Batch-fogadót](#batch-receiver) , így a Batch-feladó létrehozásakor kiválaszthatja a meglévő batch-fogadót célként megadott kötegként. Habár a Batch-fogadóknak semmit nem kell tudniuk a Batch-küldők számára, a kötegelt küldőknek tudniuk kell, hogy hová kell küldeniük az üzeneteket. 

* Győződjön meg arról, hogy a Batch-fogadó és a Batch-küldő ugyanazzal az Azure-régióval *és Azure-* előfizetéssel rendelkezik. Ha nem, akkor nem választhatja ki a Batch-fogadót a Batch-feladó létrehozásakor, mert azok nem láthatók egymás számára.

1. Hozzon létre egy másik logikai alkalmazást a következő névvel: "BatchSender"

   1. A keresőmezőbe írja be a "Ismétlődés" kifejezést a szűrőként. 
   Válassza ki ezt az eseményindítót: **Ismétlődés – ütemezése**

      ![Az "Ismétlődés ütemezése" eseményindító hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Állítsa be a gyakoriságot és az intervallumot a küldő logikai alkalmazás percenkénti futtatásához.

      ![Ismétlődési eseményindító gyakoriságának és intervallumának megadása](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Új művelet hozzáadása az üzenetek kötegbe való küldéséhez.

   1. Az ismétlődési eseményindító alatt válassza az **új lépés**lehetőséget.

   2. A keresőmezőbe írja be szűrőként a "batch" kifejezést. 
   Válassza ki a **műveletek** listát, majd válassza ki ezt a műveletet: **válasszon Logic apps munkafolyamatot batch triggerrel – üzenetek küldése kötegbe**

      ![Válassza a "Logic Apps munkafolyamat kiválasztása a Batch triggerrel" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Válassza ki a korábban létrehozott batch-fogadó logikai alkalmazást.

      ![A "batch receiver" logikai alkalmazás kiválasztása](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > A lista a Batch-eseményindítókkal rendelkező többi logikai alkalmazást is megjeleníti. 
      > 
      > Ha a Visual studiót használja, és nem látja a kiválasztható batch-fogadókat, ellenőrizze, hogy üzembe helyezte-e a Batch-fogadót az Azure-ban. Ha még nem tette meg, Ismerje meg, hogyan [helyezheti üzembe a Batch-fogadó logikai alkalmazást az Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)-ban. 

   4. Válassza ki ezt a műveletet: **Batch_messages-<*a-Batch-fogadó* >**

      ![Válassza ki ezt a műveletet: "Batch_messages-< a-Logic-App >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. A Batch-feladó tulajdonságainak beállítása:

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Köteg neve** | A fogadó logikai alkalmazás által definiált batch-név, amely ebben a példában a "TestBatch". <p>**Fontos**: a Batch neve futásidőben lesz érvényesítve, és meg kell egyeznie a fogadó logikai alkalmazás által megadott névvel. A naplólap nevének módosítása hatására a Batch-feladó meghibásodik. | 
   | **Üzenet tartalma** | Az elküldeni kívánt üzenet tartalma | 
   ||| 

   Ebben a példában adja hozzá ezt a kifejezést, amely beszúrja az aktuális dátumot és időpontot a kötegbe küldött üzenet tartalmába:

   1. Kattintson az **üzenet tartalma** mezőre. 

   2. Amikor megjelenik a dinamikus tartalom lista, válassza a **kifejezés**lehetőséget. 

   3. Adja meg a `utcnow()` kifejezést, majd kattintson **az OK gombra**. 

      ![Az "üzenet tartalma" területen válassza a "kifejezés" értéket, írja be a "utcnow ()" kifejezést, majd kattintson az "OK" gombra.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Most állítson be egy partíciót a köteghez. A "BatchReceiver" műveletnél válassza a **Speciális beállítások megjelenítése lehetőséget** , és állítsa be a következő tulajdonságokat:

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Partíció neve** | Opcionális egyedi partíciós kulcs, amely a cél köteg logikai részhalmazokra osztására és az üzenetek az adott kulcs alapján történő összegyűjtésére használható. | 
   | **Üzenet azonosítója** | Egy opcionális, globálisan egyedi azonosítót (GUID) tartalmazó üzenet, amely üres | 
   ||| 

   Ebben a példában a **partíció neve** mezőben adjon meg egy olyan kifejezést, amely véletlenszerűen generált számot hoz létre egy és öt között. Hagyja üresen az **üzenet-azonosító** mezőt.
   
   1. Kattintson a **partíció neve** mezőre, hogy megjelenjen a dinamikus tartalmak listája. 

   2. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.
   
   3. Adja meg a `rand(1,6)` kifejezést, majd kattintson **az OK gombra**.

      ![Partíció beállítása a cél köteghez](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Ez a **Rand** függvény egy számot hoz létre egy és öt között. 
      Így a köteget öt számú partícióra osztja, amelyek ezt a kifejezést dinamikusan határozzák meg.

5. Mentse a logikai alkalmazást. A küldő logikai alkalmazás most az alábbi példához hasonlóan néz ki:

   ![A küldő logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logikai alkalmazások tesztelése

A batching-megoldás teszteléséhez a logikai alkalmazásait néhány percen belül el kell hagyni. Hamarosan megkezdi az e-mailek lekérését az öt, mind ugyanazzal a partíciós kulccsal rendelkező csoportokba.

A Batch-küldő logikai alkalmazás percenként fut, és egy véletlenszerűen generált számot hoz létre egy és öt között, és ezt a generált számot használja a cél köteghez tartozó partíciós kulcsként, ahol az üzenetek küldése történik. Minden alkalommal, amikor a kötegnek öt eleme van ugyanazzal a partíciós kulccsal, a Batch-fogadó logikai alkalmazás elindít minden üzenetet, és e-mailt küld.

> [!IMPORTANT]
> Ha végzett a teszteléssel, győződjön meg arról, hogy letiltotta a BatchSender logikai alkalmazást az üzenetek küldésének leállításához és a beérkezett fájlok túlterhelésének elkerüléséhez.

## <a name="next-steps"></a>Következő lépések

* [Köteg és EDI-üzenetek küldése](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [A Logic app-definíciók létrehozása JSON használatával](../logic-apps/logic-apps-author-definitions.md)
* [Kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban Azure Logic Apps és függvények](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Kivételek és hibák naplózása a Logic apps esetében](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
