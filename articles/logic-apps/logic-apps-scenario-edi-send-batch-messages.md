---
title: "A Batch-folyamat EDI üzenetek egy csoport vagy a gyűjtemény - Azure Logic Apps |} Microsoft Docs"
description: "A kötegelt feldolgozáson a logic apps EDI üzenetek küldése"
keywords: "kötegelt, kötegelt feldolgozni, kötegelt kódolása"
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>X12 üzenetküldés kötegben kereskedelmi partnerek

A vállalatok számára (B2B) környezetek partnerek gyakran exchange-csoportok vagy kötegek üzeneteket. Üzenetek küldése csoportok vagy kötegek kereskedelmi partnerek számára, hozzon létre egy kötegelt több elemet, majd használja a X12 kötegelt művelet elemeket feldolgozni egy kötegelt.


X12 a kötegelés üzenetek, például a többi üzenet, használja a kötegelt eseményindítója és tevékenysége fel. Is a X12, a kötegelt végighalad egy X12 Encode lépés előtt a partnerrel vagy bármely más célra. A kötegelt eseményindítója és tevékenysége kapcsolatos további információkért lásd: [kötegelt folyamat üzenetek](logic-apps-batch-process-send-receive-messages.md).

Ez a témakör bemutatja, hogyan tud feldolgozni X12 üzenetek egy kötegelt feladatok elvégzésével:
* [Fogadó elemeket, és létrehoz egy kötegelt logikai alkalmazás létrehozása](#receiver). A "címzett" logikai alkalmazás a következő műveleteket hajtja végre:
 
   * Adja meg a kötegelt nevét és verzióját leíró feltételeket, felel meg az elemek kötegként feloldása előtt.

   * Folyamatok vagy kódolja a megadott X12 használatával kötegelt elemeinek megállapodás vagy partneri identitásokat.

* [Hozzon létre egy logikai alkalmazás, amely elemeket küld egy kötegelt](#sender). A "feladó" logikai alkalmazás határozza meg, hova küldje a elemek kötegelés, amely lehet egy meglévő fogadó logikai alkalmazást.


## <a name="prerequisites"></a>Előfeltételek

Kövesse az alábbi példát, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társított

* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) integrációs fiókja meghatározott. Győződjön meg arról, hogy az egyes partnerekhez használja a X12 (szabványos Alpha szolgáltatókódot) a partner tulajdonságainak a minősítő egy üzleti identitás.

* Egy [X12 megállapodás](logic-apps-enterprise-integration-x12.md) , amely már definiálva van az integráció-fiókban

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Hozzon létre egy logikai alkalmazást, amely X12 fogad üzeneteket, és létrehoz egy kötegelt

Mielőtt üzeneteket küldhetnek egy tranzakcióköteghez, létre kell hoznia a "címzett" logikai alkalmazás a **kötegelt** eseményindító. Ezzel a módszerrel választhat a fogadó logikai alkalmazás a küldő logikai alkalmazás létrehozásakor. A címzett, adja meg a kötegelt nevét, a kiadási feltételek, X12 szerződés és az egyéb beállításokat. 


1. Az a [Azure-portálon](https://portal.azure.com), logikai alkalmazás létrehozása ezen a néven: "BatchX12Messages".

2. A Logic Apps Designer, adja hozzá a **kötegelt** eseményindító, amely elindítja a logic app munkafolyamatot. A keresési mezőbe írja be a "batch" szűrőként. Válassza ki az ehhez az eseményindítóhoz: **kötegelt – kötegelt üzenetek**

   ![Kötegelt eseményindító hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Adjon meg egy nevet a kötegelt, és a feltételek megadása a kötegelt például feloldása:

   * **A Batch-név**: A a kötegben, amely "TestBatch" Ebben a példában azonosítására használt nevet.

   * **A kiadási feltételek**: az üzenetek száma, az ütemezés vagy mindkettőt alapulhat kötegelt kiadási feltételek.
   
     ![Kötegelt eseményindító részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Üzenetek száma**: ahhoz, hogy a kötegelt feldolgozásra, amely "5" Ebben a példában kibocsátása előtt üzenetek száma.

     ![Kötegelt eseményindító részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Ütemezés**: A kötegelt feldolgozásra, amely "10 percenként" Ebben a példában kiadás ütemezése.

     ![Kötegelt eseményindító részleteinek megadása](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Adja hozzá egy másik művelet, amely a csoportosított kódolja vagy üzenetek kötegek, és létrehoz egy X12 kötegelni üzenet. 

   a. Válasszon **+ új lépés** > **művelet hozzáadása**.

   b. A keresési mezőbe, írja be az "X 12 kötegelt" szűrőként, és válassza ki a művelet **X12-kötegelt kódolása**. Például a X12 összekötő kódolására, több változata köteg kódolási művelet van. Ki lehet őket.

   ![Válassza ki a X12 művelet kötegelt kódolása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. A művelet az előzőekben adott hozzá tulajdonságainak beállítása.

   * Az a **X12 neve megállapodás** jelölje ki a szerződést a legördülő listából. Üres lista esetén győződjön meg arról, hogy létrehozott egy kapcsolatot integrációs fiókjába.

   * Az a **BatchName** mezőben válassza a **neve** mezőjét a dinamikus tartalom listából.
   
   * Az a **PartitionName** mezőben válassza a **partíciónév** mezőjét a dinamikus tartalom listából.

   * Az a **elemek** mezőben válassza a **kötegelni elemek** a dinamikus tartalom listából.

   ![Kötegelt Encode művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Tesztelési célokra, adja hozzá a kötegelt üzenetet küld a HTTP-művelethez [kérelem Bin szolgáltatás](https://requestbin.fullcontact.com/). 

   1. Adja meg a "HTTP", a szűrő be a keresőmezőbe. Ez a művelet kiválasztása: **HTTP - HTTP**
    
      ![Válassza ki a HTTP-művelet](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Az a **metódus** listáról válassza ki **POST**. Az a **Uri** mezőben, a kérelem bin URI Azonosítójának generálása, és adja meg, hogy URI. Az a **törzs** be, amikor a dinamikus listáját nyitja meg, válassza ki a **törzs** eleménél a **kötegelt kódolása szerződésnév által** szakasz. Ha nem lát **törzs**, válassza a **további** mellett **kötegelt kódolása szerződésnév által**.

      ![HTTP-művelet részleteinek megadása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Most, hogy létrehozta a fogadó logikai alkalmazás, mentse a Logic Apps alkalmazást.

    ![A logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > A partíciónak legfeljebb 5000 üzenetek vagy 80 MB. Ha mindkét feltétel teljesül, a kötegelt előfordulhat, hogy mikorra várható, akkor is, ha a felhasználó által definiált feltétel nem teljesül.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Küldő X12 logikai alkalmazás létrehozása egy kötegelt üzenetek

Most hozzon létre egy vagy több logikai alkalmazások által küldött elemeket a köteg határozzák meg a fogadó logikai alkalmazást. A küldő megadhatja a fogadó logikai alkalmazás és a neve, üzenet tartalma és egyéb beállításokat. Megadhat egyedi partíciókulcsot részekre a kötegelt elemek gyűjtéséhez ezzel a kulccsal.

A logic apps küldő hova küldje a elemek, amíg a fogadó logic Apps alkalmazásokat nem kell ismernie a küldők tudnia kell.


1. Egy másik logikai alkalmazás létrehozása ezen a néven: "X12MessageSender". Ehhez az eseményindítóhoz hozzáadása a logikai alkalmazás: **kérelem / válasz - kérés** 
   
   ![A kérelem eseményindító hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Adjon hozzá egy új lépést egy kötegelt üzenetküldésre.

   1. Válasszon **+ új lépés** > **művelet hozzáadása**.

   2. A keresési mezőbe írja be a "batch" szűrőként. 

3. Ez a művelet kiválasztása: **üzenetek küldését a batch- – válassza ki a Logic Apps-munkafolyamat kötegelt eseményindító**

   ![Jelölje be a "Batch-üzenetek küldése"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Most válassza a "BatchX12Messages" logikai alkalmazás, amely korábban hozott létre, amely csomópontként jelenik meg a műveletet.

   ![Válassza ki a "batch fogadó" logikai alkalmazás](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > A listán az más logikai alkalmazások kötegelt eseményindítók is látható.

5. A kötegelt beállításainak megadása.

   * **A Batch-név**: határozzák meg a fogadó logikai alkalmazás, amely "TestBatch" Ebben a példában, és futásidőben érvényesíti a batch-neve.

     > [!IMPORTANT]
     > Győződjön meg arról, hogy nem módosíthatja a kötegelt nevét, amely meg kell egyeznie a kötegelt a fogadó logikai alkalmazás által meghatározott.
     > A kötegelt nevének módosítása hatására a küldő logikai alkalmazás sikertelen lesz.

   * **Üzenet-tartalom**: az üzenet tartalmát, amelyet szeretne küldeni a kötegelt
   
   ![Kötegelt tulajdonságainak beállítása](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Mentse a Logic Apps alkalmazást. A küldő logikai alkalmazás most hasonlít-e ebben a példában:

   ![Mentse a küldő Logic Apps alkalmazást](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>A logic Apps alkalmazások tesztelése

A kötegelési megoldás teszteléséhez post X12 részére küldött üzenetek a küldő Logic Apps alkalmazást az [Postman](https://www.getpostman.com/postman) vagy hasonló eszköz. Hamarosan, el kell indítania az első X12 az üzeneteket, vagy kötegelt öt elemek vagy 10 percenként a kérés van – minden ugyanazzal a partíciókulccsal.

## <a name="next-steps"></a>Következő lépések

* [Folyamat üzenetek kötegek](logic-apps-batch-process-send-receive-messages.md) 
* [Egy kiszolgáló nélküli alkalmazást a Visual Studio és az Azure Logic Apps és függvények létrehozása](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Kivétel kezelése és a hibanaplózás a logic Apps alkalmazások](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
