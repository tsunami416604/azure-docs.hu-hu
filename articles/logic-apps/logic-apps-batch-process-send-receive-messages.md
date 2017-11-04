---
title: "A Batch-folyamat üzenetek egy csoport vagy a gyűjtemény - Azure Logic Apps |} Microsoft Docs"
description: "A kötegelt feldolgozáson a logic apps üzeneteket küldjön és fogadjon"
keywords: "kötegelt, kötegelt folyamat"
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: e0b7292f25a145c699dbafaf4e31e3f9d072b957
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Küldése, fogadására és a batch-folyamat üzenetek a logic Apps alkalmazások

Együtt a csoportokban lévő üzenetek feldolgozásához, elküldheti az elemeket, vagy üzenetek, a egy *kötegelt*, és majd dolgozni elemeket a köteg. Ez a módszer akkor hasznos, ha azt szeretné, hogy az elemeket a meghatározott módon vannak csoportosítva, és dolgoznak együtt. 

A logic apps elemek fogadó kötegként használatával hozhat létre a **kötegelt** eseményindító. A logic apps által küldött elemeket a köteg használatával is létrehozhat a **kötegelt** művelet.

Ez a témakör bemutatja, hogyan hozhat létre egy kötegelési megoldás ezen feladatok végrehajtásával: 

* [Fogadó és elemek gyűjt kötegként logikai alkalmazás létrehozása](#batch-receiver). A "batch fogadó" logikai alkalmazás határozza meg a kötegelt nevét és verzióját leíró feltételek teljesítéséhez, mielőtt a fogadó logikai alkalmazás kiadja, és feldolgozza elemeket. 

* [Hozzon létre egy logikai alkalmazás, amely elemeket küld egy kötegelt](#batch-sender). A "batch küldő" logikai alkalmazás határozza meg, hova küldje a cikkek, amelyek egy meglévő kötegelt fogadó logic app kell lennie. Egyedi kulcs, például egy ügyfél száma "partíció", vagy ossza, a cél kötegelt kulcs alapján részekre is megadható. Így minden elem ezzel a kulccsal összegyűjti és feldolgozza együtt. 

## <a name="requirements"></a>Követelmények

Kövesse az alábbi példát, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/logic-apps-create-a-logic-app.md) 

* Az összes e-mail fiókot [Azure Logic Apps által támogatott e-mail szolgáltató](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Üzenetek fogadása egy kötegelt logic Apps-alkalmazások létrehozása

Mielőtt üzeneteket küldhetnek egy tranzakcióköteghez, létre kell hoznia egy "batch fogadó" logikai alkalmazást a a **kötegelt** eseményindító. Ezzel a módszerrel választhat a fogadó logikai alkalmazás a küldő logikai alkalmazás létrehozásakor. A címzett adja meg a kötegelt, kiadási feltételek, és egyéb beállításokat. 

Küldő logic Apps alkalmazásokat kell tudja, hova küldje a elemek, amíg a fogadó logic Apps alkalmazásokat nem kell ismernie a küldők.

1. Az a [Azure-portálon](https://portal.azure.com), logikai alkalmazás létrehozása ezen a néven: "BatchReceiver" 

2. A Logic Apps Designer, adja hozzá a **kötegelt** eseményindító, amely elindítja a logic app munkafolyamatot. A keresési mezőbe írja be a "batch" szűrőként. Válassza ki az ehhez az eseményindítóhoz: **kötegelt – kötegelt üzenetek**

   ![Kötegelt eseményindító hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Adjon meg egy nevet a kötegelt, és a feltételek megadása a kötegelt például feloldása:

   * **A Batch-név**: A a kötegben, amely "TestBatch" Ebben a példában azonosítására használt nevet.
   * **A kiadási feltételek**: az üzenetek száma, az ütemezés vagy mindkettőt alapulhat kötegelt kiadási feltételek.
   
     ![Kötegelt eseményindító részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Üzenetek száma**: ahhoz, hogy a kötegelt feldolgozásra, amely "5" Ebben a példában kibocsátása előtt üzenetek száma.

     ![Kötegelt eseményindító részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Ütemezés**: A kötegelt feldolgozásra, amely "5 percenként" Ebben a példában kiadás ütemezése.

     ![Kötegelt eseményindító részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Adja hozzá egy másik művelet, amely egy e-mailt küld, amikor a kötegelt eseményindító következik be. Minden alkalommal, amikor a kötegelt rendelkezik öt elemek vagy az elmúlt 5 perc, a logikai alkalmazást egy e-mailt küld.

   1. Válassza ki a kötegelt eseményindító **+ új lépés** > **művelet hozzáadása**.

   2. A keresési mezőbe írja be az "e-mail" szűrőként.
   Az e-mailt provider alapján, válassza ki az e-mailek csatlakozó.
   
      Például, ha munkahelyi vagy iskolai fiókkal rendelkezik, jelölje be az Office 365 Outlook-összekötő. 
      Ha Gmail fiókkal rendelkezik, válassza ki a Gmail összekötőt.

   3. Válassza ki a művelet az összekötőhöz:  **{*e-mailt provider*}-küldjön egy e-mailek **

      Példa:

      ![Válassza ki az e-mailt Provider "E-mail küldési" műveletet](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Ha a kapcsolat korábban az e-mailek szolgáltató nem hozott létre, e-mailek hitelesítő adatok megadása a hitelesítéshez, ha a rendszer kéri. További információ [az e-mailek hitelesítő adatok hitelesítése](../logic-apps/logic-apps-create-a-logic-app.md).

6. A művelet az előzőekben adott hozzá tulajdonságainak beállítása.

   * A **Címzett** mezőben adja meg a címzett e-mail-címét. 
   Tesztelési célokra használhatja a saját e-mail-címét.

   * A a **tulajdonos** be, amikor a **dinamikus tartalom** megjelenik a listán, válassza ki a **partíciónév** mező.

     ![A "Dinamikus tartalom" listából válassza a "Partíció neve"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Egy későbbi szakasz ismerteti adjon meg egy egyedi partíciós kulcs, amely felosztja a célként megadott köteg logikai készletek ahol küldhet üzeneteket. 
     Minden a küldő logikai alkalmazás által létrehozott egyedi szám van. 
     A funkció lehetővé teszi a kötegek használjon több részhalmazának, és adja meg a névvel, Ön által biztosított minden részhalmaza.

   * A a **törzs** mezőben, ha a **dinamikus tartalom** megjelenik a listán, válassza ki a **üzenetazonosítója** mező.

     ![A "Törzs" válassza a "Üzenet azonosítója"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Mivel az e-mail küldési művelet a megadott tömb, automatikusan hozzáadja a Tervező egy **az egyes** körül hurok a **egy e-mailek küldése** művelet. 
     Ez a ciklus a kötegben lévő egyes elemek belső műveletet végez. 
     Így a kötegelt eseményindító öt elemet beállítva, az beszerzése öt e-mailek minden idő az eseményindító következik be.

7.  Most, hogy a létrehozott logikai alkalmazás kötegelt fogadó, mentse a Logic Apps alkalmazást.

    ![A logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > A partíciónak legfeljebb 5000 üzenetek vagy 80 MB. Ha mindkét feltétel teljesül, a kötegelt előfordulhat, hogy mikorra várható, akkor is, ha a felhasználó által definiált feltétel nem teljesül.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Üzenetek küldése egy kötegelt logic Apps-alkalmazások létrehozása

Most hozzon létre egy vagy több logikai alkalmazások által küldött elemeket a köteg határozzák meg a fogadó logikai alkalmazást. A küldő megadhatja a fogadó logikai alkalmazás és a neve, üzenet tartalma és egyéb beállításokat. Megadhat egyedi partíciókulcsot részekre a kötegelt elemek gyűjtéséhez ezzel a kulccsal.

Küldő logic Apps alkalmazásokat kell tudja, hova küldje a elemek, amíg a fogadó logic Apps alkalmazásokat nem kell ismernie a küldők.

1. Egy másik logikai alkalmazás létrehozása ezen a néven: "BatchSender"

   1. A keresési mezőbe írja be a "ismétlődési" szűrőként. 
   Válassza ki az ehhez az eseményindítóhoz: **ütemezés - ismétlődési**

      ![A "Ismétlődés ütemezése" eseményindító hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Adja meg a gyakoriság és a feladó futtatásának időköze logikai alkalmazás percenként.

      ![Gyakoriság és eseményindító ismétlődési időköz beállítása](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Adjon hozzá egy új lépést egy kötegelt üzenetküldésre.

   1. Válassza ki az ismétlődési eseményindító **+ új lépés** > **művelet hozzáadása**.

   2. A keresési mezőbe írja be a "batch" szűrőként. 

   3. Ez a művelet kiválasztása: **üzenetek küldését a batch- – válassza ki a Logic Apps-munkafolyamat kötegelt eseményindító**

      ![Jelölje be a "Batch-üzenetek küldése"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Most válassza a "BatchReceiver" logikai alkalmazás, amely korábban hozott létre, amely csomópontként jelenik meg a műveletet.

      ![Válassza ki a "batch fogadó" logikai alkalmazás](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > A listán az más logikai alkalmazások kötegelt eseményindítók is látható.

3. A kötegelt beállításainak megadása.

   * **A Batch-név**: határozzák meg a fogadó logikai alkalmazás, amely "TestBatch" Ebben a példában, és futásidőben érvényesíti a batch-neve.

     > [!IMPORTANT]
     > Győződjön meg arról, hogy nem módosíthatja a kötegelt nevét, amely meg kell egyeznie a kötegelt a fogadó logikai alkalmazás által meghatározott.
     > A kötegelt nevének módosítása hatására a küldő logikai alkalmazás sikertelen lesz.

   * **Üzenet-tartalom**: az elküldeni kívánt üzenet tartalma. 
   Ebben a példában adja hozzá a kifejezés, amely az aktuális dátum és idő szúr be a a kötegelt küldendő üzenet tartalma:

     1. Ha a **dinamikus tartalom** megjelenik a listán, válassza a **kifejezés**. 
     2. Adja meg a kifejezés **utcnow()**, és válassza a **OK**. 

        ![A "Üzenet tartalom" válassza a "Kifejezése". Adja meg a "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Most már készen a köteg partíció. Válassza ki a "BatchReceiver" művelet **speciális beállítások megjelenítése**.

   * **Partícióazonosító neve**: egy nem kötelező egyedi partíciókulcs a célként megadott köteg megosztásának használatára. Vegye fel az ebben a példában egy kifejezés, amely egy és öt közötti véletlenszerű számot állít elő.
   
     1. Ha a **dinamikus tartalom** megjelenik a listán, válassza a **kifejezés**.
     2. Adja meg a kifejezés: **rand(1,6)**

        ![A célként megadott köteg partíció beállítása](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Ez **VÉL** függvény hoz létre egy és 5 közötti számot. 
        Ezért ez a köteg vannak felosztásával öt számozott partíciókra, amely ebben a kifejezésben dinamikusan.

   * **Üzenet azonosítója**: nem kötelező azonosítót és egy előállított GUID Azonosítóhoz, ha üres. 
   Ehhez a példához hagyja üresen a mezőt.

5. Mentse a Logic Apps alkalmazást. A küldő logikai alkalmazás most hasonlít-e ebben a példában:

   ![Mentse a küldő Logic Apps alkalmazást](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>A logic Apps alkalmazások tesztelése

A kötegelési megoldás teszteléséhez hagyja meg a logic Apps alkalmazásokat futtató néhány percig. Hamarosan először e-mailek első öt, mind az azonos partíciókulcsú csoportokban.

A BatchSender Logic Apps alkalmazást percenként fut, egy és öt közötti véletlenszerű számot állít elő, és a létrehozott számot használja a célként megadott köteg partíciókulcsnak, ahol az üzenetek küldése történik. Minden alkalommal, amikor a kötegelt rendelkezik ugyanazzal a partíciós kulccsal, öt elemet a BatchReceiver Logic Apps alkalmazást következik be, és minden üzenetet az e-mail küldése.

> [!IMPORTANT]
> Ha elkészült teszteléshez, győződjön meg arról, hogy tiltsa le az üzenetek küldése és kerülje a túl van terhelve beérkezett BatchSender logikai alkalmazás.

## <a name="next-steps"></a>Következő lépések

* [A JSON logikai alkalmazás definícióiról létrehozása](../logic-apps/logic-apps-author-definitions.md)
* [Egy kiszolgáló nélküli alkalmazást a Visual Studio és az Azure Logic Apps és függvények létrehozása](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Kivétel kezelése és a hibanaplózás a logic Apps alkalmazások](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
