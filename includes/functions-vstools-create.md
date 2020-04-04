---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 477f3cf270377bc1341e65ab500093e05277afa8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657501"
---
Az Azure Functions projektsablon a Visual Studio-ban létrehoz egy projektet, amelyet közzétehet egy függvényalkalmazásban az Azure-ban. Egy függvényalkalmazás segítségével csoportosíthatja a függvényeket logikai egységként az erőforrások egyszerűbb kezelése, üzembe helyezése, méretezése és megosztása érdekében.

1. A Visual Studio menüben válassza az**Új** > projekt **fájlja** > **parancsot.**

1. Az **Új projekt létrehozása párbeszédpanelen**írja be a *függvényeket* a keresőmezőbe, válassza az **Azure Functions** sablont, majd válassza a **Tovább**gombot.

1. Az **Új projekt konfigurálása**csoportban adja meg a **projekt nevét,** majd válassza a **Létrehozás lehetőséget.** A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Az **Új Azure-függvényalkalmazás beállításainak létrehozásához** használja az alábbi táblázatban szereplő értékeket:

    | Beállítás      | Érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Függvények futásideje** | **Azure Functions v2 <br />(.NET Core)** | Ez az érték egy függvényprojektet hoz létre, amely az Azure Functions 2.x-es futásidejét használja, amely támogatja a .NET Core-t. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információ: [Azure Functions runtime versions overview](../articles/azure-functions/functions-versions.md).   |
    | **Függvénysablon** | **HTTP-eseményindító** | Ez az érték egy HTTP-kérelem által aktivált függvényt hoz létre. |
    | **Tárfiók**  | **Storage Emulator** | Mivel egy Azure-függvény tárfiókot igényel, az egyik hozzá van rendelve vagy létrejön, amikor közzéteszi a projektet az Azure-ban. A HTTP-eseményindító nem használ Azure Storage-fiók kapcsolati karakterláncot; minden más eseményindító-típushoz érvényes Azure Storage-fiók kapcsolati karakterlánc szükséges.  |
    | **Hozzáférési jogosultságok** | **Névtelen** | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokról és az engedélyezésről további információt az [Engedélyezési kulcsok,](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) valamint [a HTTP- és webhook-kötések című témakörben talál.](../articles/azure-functions/functions-bindings-http-webhook.md) |
    

    
    ![Az Azure Functions projektbeállításai](./media/functions-vs-tools-create/functions-project-settings.png)

    Győződjön meg arról, hogy a **hozzáférési jogokat** **Névtelen**beállításra állította. Ha a függvény alapértelmezett szintjét **választja,** akkor a függvényvégpont eléréséhez szükséges kérelmekben meg kell mutatnia a [függvénykulcsot.](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)

1. Válassza a **Létrehozás** lehetőséget a függvényprojekt és a HTTP-eseményindító függvény létrehozásához.
