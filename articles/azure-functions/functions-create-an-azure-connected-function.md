---
title: "Egy Azure-szolgáltatáshoz kötődő Azure-függvény létrehozása | Microsoft Docs"
description: "Létrehozhat egy Azure-függvényt, amely az egyéb Azure-szolgáltatásokkal interakcióba lépő kiszolgáló nélküli alkalmazás."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: a0a46708645be91f89b0a6ae9059468bc84aeb11
ms.openlocfilehash: c6905452951910d3c62bc5152741a8ead26196ef


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Egy Azure-szolgáltatáshoz kötődő Azure-függvény létrehozása
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

Ebből a rövid videóból megtudhatja, hogyan hozhat létre egy Azure-függvényt, amely figyeli egy Azure-üzenetsor üzeneteit, és egy Azure-blobba másolja azokat.

## <a name="watch-the-video"></a>Videó megtekintése
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Bemeneti üzenetsor eseményindító függvényének létrehozása
Ezen függvény célja, hogy 10 másodpercenként üzenetet írjon egy üzenetsoroba. Ennek elvégzéséhez függvényt és üzenetsorokat kell létrehoznia, és hozzá kell adnia a kódot, amellyel üzeneteket írhat az újonnan létrehozott üzenetsorokhoz.

1. Nyissa meg az Azure Portalt és keresse meg az Azure függvényalkalmazást.
2. Kattintson az **Új függvény** > **TimerTrigger - Node** elemre. Adja a függvénynek a **FunctionsBindingsDemo1** nevet
3. Írja be a „0/10 * * * * *” értéket az ütemezéshez. Ez az érték cron kifejezés formájában szerepel. Ez úgy ütemezi az időzítőt, hogy 10 másodpercenként fusson.
4. A függvény létrehozásához kattintson a **Létrehozás** gombra.
   
    ![Eseményindító időzítő függvények hozzáadása](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. A naplóban lévő tevékenységek megtekintésével ellenőrizze a függvény működését. Lehet, hogy a jobb felső sarokban lévő **Naplók** hivatkozásra kell kattintania a napló panel megjelenítéséhez.
   
   ![A napló megtekintésével ellenőrizze a függvény működését](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Üzenetsor hozzáadása
1. Lépjen az **Integrálás** lapra.
2. Válassza az **Új kimenet** > **Azure Storage-üzenetsor** > **Kiválasztás** lehetőséget.
3. Írja be a **myQueueItem** szöveget az **Üzenetparaméter neve** szövegmezőbe.
4. Válasszon egy tárfiókot, vagy kattintson az **Új** elemre a tárfiók létrehozásához, ha még nem rendelkezik tárfiókkal.
5. Írja be a **functions-bindings** szöveget az **Üzenetsor neve** szövegmezőbe.
6. Kattintson a **Save** (Mentés) gombra.  
   
   ![Eseményindító időzítő függvények hozzáadása](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Írás az üzenetsorba
1. Térjen vissza a **Fejlesztés** lapra, és adja a következő kódot a függvényhez a meglévő kód után:
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Módosítsa a meglévő függvénykódot az 1. lépésben hozzáadott kód meghívásához. Szúrja be a következő kódot a függvény 9. sora közelében, az *if* utasítás után.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Ez a kód egy **myQueueItem** elemet hoz létre, és az **idő** tulajdonságot az aktuális timeStamp értékére állítja. Ezután az új üzenetsorelemet a kontextus myQueue kötéséhez adja.
3. Kattintson a **Mentés és futtatás** gombra.
4. A Visual Studióban az üzenetsor megtekintésével ellenőrizze a kód működését.
   
   * Nyissa meg a Visual Studiót, és lépjen a **Nézet** > **Felhő** **Explorer** elemre.
   * Keresse meg a tárfiókot és a myQueue üzenetsor létrehozásakor használt **functions-bindings** üzenetsort. Naplóadatok sorait kell látnia. Lehet, hogy a Visual Studión keresztül kell bejelentkeznie az Azure-ba.  

## <a name="create-an-output-queue-trigger-function"></a>Kimeneti üzenetsor eseményindító függvényének létrehozása
1. Kattintson az **Új függvény** > **QueueTrigger - C#** elemre. Adja a függvénynek a **FunctionsBindingsDemo2** nevet. Vegye figyelembe, hogy keverheti a nyelveket ugyanazon függvényalkalmazáson belül (ebben az esetben a Node és a C# nyelvet).
2. Írja be a **functions-bindings** szöveget az **Üzenetsor neve** mezőbe.
3. Válasszon egy használni kívánt tárfiókot vagy hozzon létre egyet.
4. Kattintson a ** Create** (Létrehozás) gombra
5. A függvény naplójának és a Visual Studio frissítéseinek megtekintésével ellenőrizze az új függvény működését. A függvény naplója szerint a függvény fut, és az elemek el lettek távolítva a sorból. Mivel a függvény a **functions-bindings** kimeneti üzenetsorhoz van kötve bemeneti eseményindítóként, a Visual Studióban a **functions-bindings** üzenetsor frissítésekor kiderül, hogy az elemek eltűntek. El lettek távolítva a sorból.   
   
   ![Kimeneti üzenetsor időzítő függvényeinek hozzáadása](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Az üzenetsorelem típusának módosítása JSON-ról objektumra
1. Cserélje a **FunctionsBindingsDemo2** fájlban lévő kódot az alábbira:    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    Ez a kód két osztályt ad hozzá, a **TableItem** és a **QItem** osztályokat, amelyekkel az üzenetsorokban olvashat és írhat. Ezenkívül a **Futtatás** függvény módosult, hogy elfogadja a **QItem** és a **TraceWriter** paramétert egy **karakterlánc** és a **TraceWriter** helyett. 
2. Kattintson a **Mentés** gombra.
3. A napló megtekintésével ellenőrizze a kód működését. Figyelje meg, hogy az Azure-függvények automatikusan szerializálják és deszerializálják az objektumot, így az üzenetsor az adatok terjesztése érdekében könnyen elérhető objektumorientált módon. 

## <a name="store-messages-in-an-azure-table"></a>Üzenetek tárolása Azure-táblában
Most, hogy az üzenetsorok együttműködnek, itt az ideje egy Azure-tábla hozzáadásának a sorban lévő adatok állandó tárolása érdekében.

1. Lépjen az **Integrálás** lapra.
2. Hozzon létre egy Azure Storage táblát a kimenethez és nevezze el **myTable** néven.
3. Adja meg a **functionsbindings** választ a „To which table should the data be written?” (Melyik táblája írja az adatokat?) kérdésre.
4. Módosítsa a **PartitionKey** beállítást **{project-id}** értékről **{partition}** értékre.
5. Válasszon egy tárfiókot, vagy hozzon létre egy újat.
6. Kattintson a **Save** (Mentés) gombra.
7. Lépjen a **Fejlesztés** lapra.
8. Hozzon létre egy **TableItem** osztályt az Azure-tábla ábrázolásához, és módosítsa a Futtatás függvényt, hogy elfogadja az újonnan létrehozott TableItem objektumot. Vegye figyelembe, hogy a **PartitionKey** és a **RowKey** tulajdonságokat kell használnia, hogy működjön.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Kattintson a **Save** (Mentés) gombra.
10. A függvény naplóinak megtekintésével és a Visual Studióban ellenőrizze a kód működését. A Visual Studióban végzett ellenőrzéshez a **Cloud Explorerrel** keresse meg a **functionbindings** Azure-táblát, és ellenőrizze, hogy vannak-e benne sorok.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


