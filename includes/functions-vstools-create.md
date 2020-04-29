---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 034e966d259f1ca5f22eec5935013de32c883b59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80657501"
---
A Visual Studióban Azure Functions Project sablon létrehoz egy projektet, amelyet közzétehet egy Azure-beli Function alkalmazásban. A függvények használatával logikai egységként csoportosíthatja a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése, skálázása és megosztása érdekében.

1. A Visual Studióban a **fájl** menüben válassza az **új** > **projekt**lehetőséget.

1. Az **új projekt létrehozása**területen adja meg a *functions* kifejezést a keresőmezőbe, majd válassza ki a **Azure functions** sablont.

1. Az **új projekt konfigurálása**területen adja meg a projekt **nevét** , majd válassza a **Létrehozás**lehetőséget. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Az **új projekthez – &lt;a projekt neve&gt; ** beállításnál használja az alábbi táblázatban szereplő értékeket:

    | Beállítás      | Érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Függvények futtatókörnyezete** | **Azure Functions v2 <br />(.net Core)** | Ez az érték létrehoz egy Function projektet, amely a .NET Core-t támogató Azure Functions 2. x futtatókörnyezetét használja. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információ: [Azure functions Runtime Versions – áttekintés](../articles/azure-functions/functions-versions.md).   |
    | **Függvény sablonja** | **HTTP eseményindító** | Ez az érték egy HTTP-kérelem által aktivált függvényt hoz létre. |
    | **Storage-fiók**  | **Storage Emulator** | Mivel egy Azure-függvényhez Storage-fiók szükséges, az egyiket a rendszer a projekt Azure-ba való közzétételekor rendeli hozzá vagy hozza létre. A HTTP-triggerek nem használnak Azure Storage-fiókhoz tartozó kapcsolatok karakterláncát; minden más trigger-típushoz érvényes Azure Storage-fiókhoz tartozó összekapcsolási karakterlánc szükséges.  |
    | **Hozzáférési jogosultságok** | **Névtelen** | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokkal és az engedélyezéssel kapcsolatos további információkért lásd: [engedélyezési kulcsok](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) és [http-és webhook-kötések](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Azure Functions a projekt beállításait](./media/functions-vs-tools-create/functions-project-settings.png)

    Győződjön meg arról, hogy a **hozzáférési jogosultságokat** **névtelenre**állítja be. Ha az alapértelmezett **függvényt**választja, akkor a függvény-végpont eléréséhez a kérelmekben be kell mutatnia a [függvény kulcsát](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) .

1. Kattintson az **OK** gombra a függvény projekt és a http-triggert kiváltó függvény létrehozásához.
