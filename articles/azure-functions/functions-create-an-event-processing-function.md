---
title: "Eseményfeldolgozó függvény létrehozása | Microsoft Docs"
description: "Használja az Azure Functions szolgáltatást olyan C# függvény létrehozására, amely egy eseményidőzítő alapján fut."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 84bd0373-65e2-4022-bcca-2b9cd9e696f5
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: c7e4d5c0b2602ba8c12184900eba05fee48c4450


---
# <a name="create-an-event-processing-azure-function"></a>Eseményfeldolgozó Azure-függvény létrehozása
Az Azure Functions egy eseményvezérelt, számítási igények szerint működtethető szolgáltatás, amelynek a segítségével ütemezett és aktivált kódegységek hozhatók létre, különböző programnyelveken megvalósítva. További információk az Azure Functions szolgáltatásról: [Azure Functions Overview](functions-overview.md) (Az Azure Storage áttekintése).

Ez a témakor egy olyan új függvény létrehozását mutatja be a C# használatával, amely egy eseményidőzitő alapján fut, és üzeneteket ad hozzá a tárolási sorhoz. 

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy létrehozhassa a függvényt, aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-timer-triggered-function-from-the-template"></a>Időzítő által aktivált függvény létrehozása sablonból
A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Ahhoz, hogy létrehozhassa a függvényt, aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/). 

1. Lépjen az [Azure Functions portálra](https://functions.azure.com/signin), és jelentkezzen be az Azure-fiókjával.
2. Ha rendelkezik meglévő függvényalkalmazással, amelyet használhat, válassza ki a **Your function apps** (Saját függvényalkalmazások) menüből, és kattintson az **Open** (Megnyitás) gombra. Új függvényalkalmazás létrehozásához adjon meg egy egyedi **nevet** az új függvényalkalmazásnak, vagy fogadja el az automatikusan létrehozott nevet, majd válassza ki a kívánt **régiót**, és kattintson a **Create + get started** (Létrehozás és első lépések) gombra. 
3. A függvényalkalmazásban, kattintson a **+ Új funkció** > **TimerTrigger – C#** > **Létrehozás** lehetőségre. Ez létrehoz egy alapértelmezett névvel rendelkező függvényt, amely az alapértelmezett ütemezés szerint, percenként egyszer fut. 
   
    ![Időzítő által aktivált új függvény létrehozása](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)
4. Az új függvényben kattintson az **Integrate** (Integrálás) lap > >**New Output** (Új kimenet) > **Azure Storage Queue** (Azure Storage-üzenetsor) > **Select** (Kiválasztás) lehetőségre.
   
    ![Időzítő által aktivált új függvény létrehozása](./media/functions-create-an-event-processing-function/functions-create-storage-queue-output-binding.png)
5. Az **Azure Storage-üzenetsor kimeneténél** kattintson egy meglévő **Tárfiók kapcsolata** elemre, vagy hozzon létre egy újat, és kattintson a **Mentés** parancsra. 
   
    ![Időzítő által aktivált új függvény létrehozása](./media/functions-create-an-event-processing-function/functions-create-storage-queue-output-binding-2.png)
6. A **Develop** (Fejlesztés) lapon cserélje a meglévő C# szkriptet a **Code** (Kód) ablakban az alábbira:
    ```cs   
    using System;

    public static void Run(TimerInfo myTimer, out string outputQueueItem, TraceWriter log)
    {
        // Add a new scheduled message to the queue.
        outputQueueItem = $"Ping message added to the queue at: {DateTime.Now}.";

        // Also write the message to the logs.
        log.Info(outputQueueItem);
    }
    ```
   
    Ez a kód új üzenetet ad hozzá az üzenetsorhoz, amely a függvény futtatásakor aktuális idővel és dátummal rendelkezik.
7. Kattintson a **Save** (Mentés) lehetőségre, és tekintse meg a **Logs** (Naplók) ablakot, ahol ellenőrizheti a függvény következő futtatásának idejét.
8. (Nem kötelező) Menjen a tárfiókhoz, és ellenőrizze, hogy az üzenetek hozzá lettek-e adva az üzenetsorhoz.
9. Lépjen vissza az **Integrate** (Integrálás) lapra, és módosítsa az ütemezési mező értékét a következőre:`0 0 * * * *`. A függvény mostantól óránként egyszer lefut. 

Ez egy nagy mértékben leegyszerűsített példája az időzített eseményindítóknak és a tárolásisor-kimenetek kötésének. További információkat az [Azure Functions timer trigger](functions-bindings-timer.md) (Azure Functions időzített eseményindítók) és az [Azure Functions triggers and bindings for Azure Storage](functions-bindings-storage.md) (Azure Functions eseményindítók és kötések az Azure Storage-hoz) témakörökben talál.

## <a name="next-steps"></a>Következő lépések
A következő témakörökben további, az Azure Functions szolgáltatásra vonatkozó információkat talál.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Részletezi az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat, köztük a dinamikus szolgáltatáscsomagot, és segít a megfelelő csomag kiválasztásában.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


