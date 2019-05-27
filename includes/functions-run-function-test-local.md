---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132210"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A következő parancs elindítja a függvényalkalmazást. Az alkalmazás ugyanazt az Azure Functions-futtatókörnyezetet használja a futáshoz, amelyik az Azure-ban van.

```bash
func host start --build
```

A `--build` beállítás a C#-projektek fordításához szükséges. A JavaScript-projekteknél ez a beállítás nem szükséges.

A Functions-gazdagép az indulásakor a következőhöz hasonló kimenetet írja ki, amelyet a könnyebb olvashatóság érdekében lerövidítettünk:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a GET kérelemre a helyi függvény által visszaadott válasz:

![Helyi tesztelés a böngészőben](./media/functions-run-function-test-local/functions-test-local-browser.png)

Most, hogy már futtatta a függvényt helyben, létrehozhatja az Azure-ban a függvényalkalmazást és az egyéb szükséges erőforrásokat.