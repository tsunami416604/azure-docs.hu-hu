---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d47caaf9dbca14364771e0d8ba1e7f6ce16ef2d6
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174622"
---
## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását.

1. A függvény teszteléséhez állítson be egy töréspontot a függvény kódjában, majd nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját. 

    ![Az Azure helyi kimenete](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. A végrehajtás a töréspont elérésekor szünetelni fog.

1. A végrehajtás folytatásakor a böngészőben a következőképp jelenik meg a válasz a GET-kérésre:

    ![A függvény által visszaadott localhost válasz a böngészőben](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. A hibakeresés leállításához nyomja le a Shift + F5 billentyűkombinációt.