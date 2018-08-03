---
title: A Batch-és EDI-üzenetek feldolgozásával egy csoport vagy a gyűjtemény – Azure Logic Apps |} A Microsoft Docs
description: A kötegelt feldolgozási, a logic apps EDI-üzenetek küldése
keywords: a Batch, kötegelt feldolgozásához, a kötegelt kódolása
author: divswa
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: fb15688968cb29039fc669ed6b8685ba64df9e81
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432133"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>A kereskedelmi partnerekkel batch üzeneteket küldhetünk X12

Vállalatközi (B2B) környezetek, a partnerek gyakran exchange-csoportok vagy kötegek üzenetek. Üzenetek küldéséhez a csoportok vagy kötegek kereskedelmi partnerek számára, létrehozhat egy batch több elemet, majd ezekhez az elemekhez feldolgozásához a Batch használatát a X12 kötegelt művelet.


Teljesítménynövelő célú kötegelés X12 üzenetek, például a többi üzenet, használja a batch eseményindító és művelet. Emellett a X12, a batch halad végig X12 Encode lépés előtt a partner vagy bármely más célhelyre. A batch-eseményindító és művelet kapcsolatos további információkért lásd: [kötegelt feldolgozási üzenetek](logic-apps-batch-process-send-receive-messages.md).

Ez a témakör bemutatja, hogyan dolgozhatja fel X12 üzenetek egy kötegelt feladatok elvégzésével:
* [Hozzon létre egy logikai alkalmazást, amely fogadja az elemeket, és létrehoz egy batch](#receiver). A "címzett" logikai alkalmazás a következő műveleteket hajtja végre:
 
   * Feltételeket határozza meg a batch nevét és verzióját egy kötegelt elemek közzététele előtt kielégítése érdekében.

   * Dolgozza fel, vagy a cikkek a Batch a megadott X12 használatával kódolja a szerződés vagy -partnerétől identitásokat.

* [Hozzon létre egy logikai alkalmazást, amely elemeket küld egy batch](#sender). A "feladó" logikai alkalmazás meghatározza, hova küldhetők elemek kötegelés, amelynek kell lennie egy meglévő fogadó logikai alkalmazást.


## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a példában, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított

* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , hogy meghatározta az integrációs fiókban. Győződjön meg arról, hogy az egyes partnerek a X12 (Standard Alpha szolgáltatókódot) használja, a partner tulajdonságai egy üzleti identitás minősítője.

* Egy [X12 szerződés](logic-apps-enterprise-integration-x12.md) , amely már definiálva van az integrációs fiók

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Hozzon létre egy logikai alkalmazást, amely X12 fogad üzeneteket, és létrehoz egy batch

Üzeneteket küldhet egy kötegelt, akkor először létre kell hoznia egy "címzett" logikai alkalmazás a **Batch** eseményindító. Ezzel a módszerrel választhat a fogadó logikai alkalmazás a küldő logikai alkalmazás létrehozásakor. A címzett, adja meg a batch-név, kiadási feltételek, X12 szerződés és az egyéb beállításokat. 


1. Az a [az Azure portal](https://portal.azure.com), hozzon létre egy logikai alkalmazást ezen a néven: "BatchX12Messages".

1. A Logic Apps Designer, adja hozzá a **Batch** eseményindító, amely elindítja a logikai alkalmazás munkafolyamatának. A Keresés mezőbe írja be a "batch" szűrőként. Válassza ki a következő eseményindítót: **Batch – kötegelt üzenetek**

   ![Batch-eseményindító hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

1. Adjon meg egy nevet a batch, és adja meg a kötegelt, például kiadása feltételeket:

   * **Batch-név**: azonosítja a batch, amely "TestBatch" Ebben a példában a neve.

   * **Kiadási feltételek**: A kötegelt kiadási feltételek, amely az üzenetek száma, az ütemezés vagy mindkettőt alapulhat.
   
     ![Adja meg a Batch a trigger részletei](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Üzenetek száma**: tárolásához egy kötegelt feldolgozásra, amely ebben a példában az "5" kibocsátása előtt üzenetek száma.

     ![Adja meg a Batch a trigger részletei](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Ütemezés**: A kötegelt feldolgozásra, amely "10 percenként" Ebben a példában kiadás ütemezése.

     ![Adja meg a Batch a trigger részletei](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


1. Adjon hozzá egy másik művelet, amely a csoportosított kódol vagy kötegeli az üzeneteket, és létrehozza a X12 kötegelt üzenet. 

   a. Válasszon **+ új lépés** > **művelet hozzáadása**.

   b. A Keresés mezőbe írja be az "X 12 batch" szűrőként, és válassza ki a művelet **X12-kötegelt kódolás**. Például a X12 összekötő kódolása, nincs művelet kódolás Batch több változata. Ki lehet őket.

   ![Válassza ki a X12 művelet kötegelt kódolása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
1. Állítsa be az imént hozzáadott művelet tulajdonságait.

   * Az a **X12 nevére szerződés** jelölje ki a szerződést a legördülő listából. Ha a lista üres, ellenőrizze, hogy létrehozta a kapcsolatot az integrációs fiókba.

   * Az a **BatchName** jelölje ki a **Kötegnév** mezőt, a dinamikus tartalmú listából.
   
   * Az a **PartitionName** jelölje ki a **partíciónévre** mezőt, a dinamikus tartalmú listából.

   * Az a **elemek** jelölje ki a **kötegelt elemek** a dinamikus tartalmú listából.

   ![Kötegelt kódolása művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

1. Tesztelési célokra, adja hozzá a kötegelt üzenet küldéséhez egy HTTP-művelet [kérelem Bin szolgáltatás](https://requestbin.fullcontact.com/). 

   1. Adja meg a "HTTP" szűrőként a keresőmezőbe. Válassza a következő műveletet: **HTTP - HTTP**
    
      ![Válassza ki a HTTP-művelet](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   1. Az a **metódus** listáról válassza ki **POST**. Az a **Uri** mezőbe, a kérelem bin URI generálása, és adja meg az URI-ra. Az a **törzs** be, amikor a dinamikus lista jelenik meg, válassza ki a **törzs** eleménél a **kötegelt kódolás a Szerződés neve szerint** szakaszban. Ha nem lát **törzs**, válassza a **Továbbiak** melletti **kötegelt kódolás a Szerződés neve szerint**.

      ![Adja meg a HTTP-művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

1.  Most, hogy a fogadó Logic Apps-alkalmazás hozta létre, mentse a logikai alkalmazást.

    ![A logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Egy partíció legfeljebb 5000 üzenetek vagy a 80 MB rendelkezik. Ha mindkét feltétel teljesül, a batch előfordulhat, hogy elérhető, akkor is, ha a felhasználó által megadott feltétel nem teljesül.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Hozzon létre egy logikai alkalmazást, amely X12 küld egy kötegelendő üzenetek

Most hozzon létre egy vagy több, a batch, a fogadó logikai alkalmazás által meghatározott elemek küldenek a logic apps. A küldő megadhatja a fogadó logikai alkalmazás és a batch-név, üzenet tartalma és a további beállításokat. Opcionálisan megadhat egy egyedi partíciókulccsal történő részekre a kötegelt elemek összegyűjtése ezzel a kulccsal.

Küldő a logic apps, hova küldhetők a cikkeket, amíg fogadó a logic apps nem kell tudnia semmit a feladók ismernie kell.


1. Egy másik logikai alkalmazás létrehozása ezen a néven: "X12MessageSender". Ez az eseményindító hozzáadása a logikai alkalmazás: **kérelem / válasz – kérelem** 
   
   ![A kérelem típusú trigger hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

1. Adjon hozzá egy új lépést, az üzenetek küldése az egy kötegben.

   1. Válasszon **+ új lépés** > **művelet hozzáadása**.

   1. A Keresés mezőbe írja be a "batch" szűrőként. 

1. Válassza a következő műveletet: **batch – válasszon ki egy kötegtriggert tartalmazó Logic Apps munkafolyamatot üzenetek küldése**

   ![Válassza a "Kötegelendő üzenetek küldése"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

1. Most válassza ki a korábban létrehozott "BatchX12Messages" logikai alkalmazás, így jelenik meg egy műveletet.

   ![Válassza ki a "batch fogadó" logikai alkalmazás](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > A lista minden más logikai alkalmazás kötegtriggereket rendelkező is bemutatja.

1. Állítsa be a batch-tulajdonságokat.

   * **Batch-név**: A batch nevét határozza meg a fogadó logikai alkalmazást, amely ebben a példában "TestBatch", és futásidőben érvényesíti.

     > [!IMPORTANT]
     > Győződjön meg arról, hogy meg kell egyeznie a batch a fogadó logikai alkalmazás által meghatározott kötegnév nem módosítja.
     > A batch nevének módosítása hatására a küldő a logikai alkalmazás sikertelen lesz.

   * **Üzenet tartalma**: a köteg küldeni kívánt üzenet tartalma
   
   ![Batch-tulajdonságainak megadása](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

1. Mentse a logikai alkalmazást. Küldő logikai alkalmazás most az alábbihoz hasonlít:

   ![A küldő logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>A logic apps tesztelése

A kötegelés megoldás teszteléséhez post X12 küldött üzeneteket a küldő logikai alkalmazást [Postman](https://www.getpostman.com/postman) vagy hasonló eszközt. Hamarosan el kell indítania az első X12 üzenetek, vagy egy kötegelt öt elemek vagy 10 percenként, a kérelem van – az azonos partíciókulccsal rendelkező összes.

## <a name="next-steps"></a>További lépések

* [Üzenetek feldolgozásával kötegként](logic-apps-batch-process-send-receive-messages.md) 
* [A Visual Studióban, az Azure Logic Apps és Functions kiszolgáló nélküli alkalmazás készítése](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Kivételkezelés és a hibanaplózás logikai alkalmazásokhoz](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
