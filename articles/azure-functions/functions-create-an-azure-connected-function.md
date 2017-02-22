---
title: "Az Azure-szolgáltatásokhoz csatlakozó függvény létrehozása | Microsoft Docs"
description: "Az Azure Functions segítségével létrehozhat egy kiszolgáló nélküli alkalmazást, amely más Azure-szolgáltatásokhoz kapcsolódik."
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
ms.date: 01/23/2017
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: a8f6d111a010666bf4aaaf05e061381cc8fffed0
ms.openlocfilehash: 634781189459f26e2ffa42b25a2ffb229d3371d4


---
# <a name="use-azure-functions-to-create-a-function-that-connects-to-other-azure-services"></a>Az Azure Functions segítségével létrehozhat egy függvényt, amely más Azure-szolgáltatásokhoz kapcsolódik.

Ez a témakör olyan függvény létrehozását mutatja be az Azure Functionsben, amely egy Azure Storage-üzenetsor üzeneteit figyeli, és egy Azure Storage-táblába másolja azokat. Az üzeneteknek az üzenetsorba való betöltése egy időzítő által aktivált függvénnyel történik. Egy másik függvény beolvassa az üzeneteket az üzenetsorból, és a táblába írja azokat. Az üzenetsort és a táblát az Azure Functions hozza létre a kötési meghatározások alapján. 

A dolgokat még érdekesebbé teszi, az egyik függvény JavaScript, a másik pedig C# nyelven lett megírva. Ez bemutatja, hogy egy függvényalkalmazás különféle nyelveken írt függvényekkel is rendelkezhet. 

Ezt a forgatókönyvet a [Channel 9 blog egyik videója](https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player) mutatja be.

## <a name="create-a-function-that-writes-to-the-queue"></a>Az üzenetsorba író függvény létrehozása

Mielőtt egy tárterületi üzenetsorhoz kapcsolódna, létre kell hoznia egy függvényt, amely betölti az üzenetsort. Ez a JavaScript-függvény időzítő aktiválót használ, amely 10 másodpercenként üzenetet ír a sorba. Ha még nem rendelkezik Azure-fiókkal, keresse fel az [Azure Functions kipróbálását](https://functions.azure.com/try) lehetővé tévő webhelyet, vagy [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

1. Nyissa meg az Azure Portalt, és keresse meg a függvényalkalmazást.

2. Kattintson a **New Function** (Új függvény)  > **TimeTrigger - JavaScript** elemre. 

3. Adja a függvénynek a **FunctionsBindingsDemo1** nevet, adja meg a `0/10 * * * * *` cron kifejezésértéket a **Schedule** (Ütemezés) számára, és kattintson a **Create** (Létrehozás) elemre.
   
    ![Időzítő által aktivált hozzáadása](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)

    Létrehozott egy időzítő által aktivált függvényt, amely 10 másodpercenként fut.

5. A **Develop** (Fejlesztés) lapon kattintson a **Logs** (Naplók) elemre, és tekintse meg a tevékenységet a naplóban. 10 másodpercenként írt naplóbejegyzéseket láthat.
   
    ![A napló megtekintésével ellenőrizze a függvény működését](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

## <a name="add-a-message-queue-output-binding"></a>Üzenetsor kimeneti kötésének hozzáadása

1. Az **Integrate** (Integrálás) lapon válassza a **New Output** (Új kimenet) > **Azure Queue Storage** > **Select** (Kiválasztás) elemet.

    ![Eseményindító időzítő függvény hozzáadása](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

2. Adja meg a `myQueueItem` értéket a **Message parameter name** (Üzenetparaméter neve), illetve a `functions-bindings` értéket a **Queue name** (Üzenetsor neve) beállítás számára, válasszon ki egy meglévő kapcsolatot a **Storage account connection** (Tárfiókkapcsolat) mezőben, vagy kattintson a **new** (új) elemre egy tárfiókkapcsolat létrehozásához, majd kattintson a **Save** (Mentés) gombra.  

    ![Kimeneti kötés létrehozása a tárterület üzenetsorával](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab2.png)

1. A **Develop** (Fejlesztés) lapon egészítse ki a függvényt a következő kóddal:
   
    ```javascript
   
    function myQueueItem() 
    {
        return {
            msg: "some message goes here",
            time: "time goes here"
        }
    }
   
    ```
2. Keresse meg az *if* utasítást a függvény 9. sorának közelében, és szúrja be az alábbi kódot az utasítás után.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueueItem = toBeQed;
   
    ```  
   
    Ez a kód egy **myQueueItem** elemet hoz létre, és az **idő** tulajdonságot az aktuális timeStamp értékére állítja. Ezután az új üzenetsorelemet a kontextus **myQueueItem** kötéséhez adja.

3. Kattintson a **Mentés és futtatás** gombra.

## <a name="view-storage-updates-by-using-storage-explorer"></a>A tárterület frissítéseinek áttekintése a Storage Explorerrel
A függvény működését a létrehozott sorban található üzenetek megtekintésével ellenőrizheti.  A Visual Studio Cloud Explorer eszközével kapcsolódhat a tárterület üzenetsorához. A portálon azonban egyszerűen kapcsolódhat a tárfiókhoz a Microsoft Azure Storage Explorerrel.

1. Az **Integrate** (Integrálás) lapon kattintson az üzenetsor kimeneti kötésére, majd a **Documentation** (Dokumentáció) elemre, jelenítse meg tárfiók kapcsolati karakterláncát, és másolja az értéket. Ezt az értéket használja a tárfiókhoz való kapcsolódáshoz.

    ![Az Azure Storage Explorer letöltése](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab3.png)


2. Ha még nem tette meg, töltse le és telepítse a [Microsoft Azure Storage Explorert](http://storageexplorer.com). 
 
3. A Storage Explorerben kattintson az Azure Storage-hoz való kapcsolódásra szolgáló ikonra, illessze be a kapcsolati karakterláncot a mezőbe, és haladjon végig a varázslón.

    ![Storage Explorer – kapcsolat hozzáadása](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-storage-explorer.png)

4. A **Local and attached** (Helyi és csatolt) részen bontsa ki a **Storage Accounts** (Tárfiókok) > tárfiók > **Queues** (Üzenetsorok)  > **functions-bindings** csomópontot, és ellenőrizze, hogy az üzenetek írása az üzenetsorba megtörténik-e.

    ![Az üzenetsorban szereplő üzenetek nézete](./media/functions-create-an-azure-connected-function/functionsbindings-azure-storage-explorer.png)

    Ha az üzenetsor nem létezik vagy üres, nagy valószínűséggel a függvénykötéssel vagy a kóddal van probléma.

## <a name="create-a-function-that-reads-from-the-queue"></a>Az üzenetsorból olvasó függvény létrehozása

Most, hogy rendelkezik az üzenetsorhoz adott üzenetekkel, létrehozhat egy másik függvényt, amely az üzenetsorból olvas, és az üzeneteket véglegesen egy Azure Storage-táblába írja.

1. Kattintson a **New Function** (Új függvény) > **QueueTrigger-CSharp** elemre. 
 
2. Adja a függvénynek a `FunctionsBindingsDemo2` nevet, írja be a **functions-bindings** szöveget a **Queue name** (Üzenetsor neve) mezőbe, válasszon ki egy meglévő tárfiókot, vagy hozzon létre egyet, majd kattintson a **Create** (Létrehozás) gombra.

    ![Kimeneti üzenetsor időzítő függvényének hozzáadása](./media/functions-create-an-azure-connected-function/function-demo2-new-function.png) 

3. (Nem kötelező) Az új függvény működését az új üzenetsornak a Storage Explorerben való megtekintésével ellenőrizheti, ahogy korábban is. A Visual Studio Cloud Explorer eszközét is használhatja.  

4. (Nem kötelező) Frissítse a **functions-bindings** üzenetsort, és figyelje meg, hogy elemek lettek eltávolítva az üzenetsorból. Az eltávolítás azért történt, mert a függvény a **functions-bindings** üzenetsorhoz van kötve bemeneti eseményindítóként, és a függvény az üzenetsort olvassa. 
 
## <a name="add-a-table-output-binding"></a>Tábla kimeneti kötésének hozzáadása

1. A FunctionsBindingsDemo2 részen kattintson az **Integrate** (Integrálás) > **New Output** (Új kimenet) > **Azure Table Storage** > **Select** (Kiválasztás) elemre.

    ![Kötés hozzáadása Azure Storage-táblához](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png) 

2. Adja meg a `TableItem` értéket a **Table name** (Tábla neve), illetve a `functionbindings` értéket a **Table parameter name** (Táblaparaméter neve) beállítás számára, válasszon ki egy meglévő kapcsolatot a **Storage account connection** (Tárfiókkapcsolat) mezőben, vagy hozzon létre egy új kapcsolatot, majd kattintson a **Save** (Mentés) gombra.

    ![A Storage-tábla kötésének konfigurálása](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab2.png)
   
3. A **Develop** (Fejlesztés) lapon cserélje a meglévő függvénykódot az alábbira:
   
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
        
        // Add the item to the table binding collection.
        myTable.Add(myItem);
    
        log.Verbose($"C# Queue trigger function processed: {myItem.RowKey} | {myItem.Msg} | {myItem.Time}");
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
    A **TableItem** osztály egy sort képvisel a tárolási táblában, és az elemet adja hozzá a **TableItem** objektumok `myTable` gyűjteményéhez. A **PartitionKey** és a **RowKey** tulajdonságot be kell állítania a táblába való beszúrás lehetővé tételéhez.

4. Kattintson a **Save** (Mentés) gombra.  Végül, ellenőrizheti a függvény működését a Storage Explorerben vagy a Visual Studio Cloud Explorer eszközében.

5. (Nem kötelező) A Storage Explorerben található tárfiókban bontsa ki a **Tables** (Táblák) > **functionsbindings** csomópontot, és ellenőrizze, hogy a sorok hozzáadása a táblához megtörtént-e. Ugyanezt megteheti a Visual Studio Cloud Explorer eszközében is.

    ![A tábla sorainak nézete](./media/functions-create-an-azure-connected-function/functionsbindings-azure-storage-explorer2.png)

    Ha a tábla nem létezik vagy üres, nagy valószínűséggel a függvénykötéssel vagy a kóddal van probléma. 
 
[!INCLUDE [More binding information](../../includes/functions-bindings-next-steps.md)]

## <a name="next-steps"></a>Következő lépések
A következő témakörökben további, az Azure Functions szolgáltatásra vonatkozó információkat talál.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában. 

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->


