---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a27d44a7684b4ad3d39d7fba50ca52e08e932971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769768"
---
A Visual Studio Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvények használatával logikai egységként csoportosíthatja a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése, skálázása és megosztása érdekében.

1. A Visual Studióban a **fájl** menüben válassza az **új** > **projekt**lehetőséget.

1. A **create a New Project (új projekt létrehozása** ) párbeszédpanelen keresse meg a `functions`, válassza ki a **Azure functions** sablont, és kattintson a **tovább**gombra.

1. Adja meg a projekt nevét, majd válassza a **Létrehozás**lehetőséget. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Az **új Azure functions alkalmazás létrehozása**területen használja az alábbi beállításokat:

    + **Azure Functions v2 (.NET Core)**
    + **HTTP-trigger**
    + **Storage-fiók**: **Storage Emulator**
    + **Engedélyezési szint**: **Névtelen** 

    | Lehetőség      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Függvények futtatókörnyezete** | **Azure Functions 2. x <br />(.NET Core)** | Ez a beállítás egy olyan Function projektet hoz létre, amely a .NET Core-t támogató Azure Functions 2. x futtatókörnyezetét használja. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információ: [Target Azure functions Runtime Version](../articles/azure-functions/functions-versions.md).   |
    | **Függvény sablonja** | **HTTP-trigger** | Ez a beállítás egy HTTP-kérelem által aktivált függvényt hoz létre. |
    | **Tárfiók**  | **Storage-emulátor** | Egy HTTP-trigger nem használja az Azure Storage-fiókhoz való kapcsolatokat. A többi triggernek érvényes Storage-fiókhoz tartozó kapcsolati sztringre van szüksége. Mivel a functions szolgáltatáshoz Storage-fiók szükséges, az egyiket a rendszer a projekt Azure-ba való közzétételekor rendeli hozzá vagy hozza létre. |
    | **Engedélyszint** | **Névtelen** | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokról és az engedélyezésről a [HTTP- és webhookkötések](../articles/azure-functions/functions-bindings-http-webhook.md) című témakör [Engedélyezési kulcsok](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) szakaszában talál további információt. |
    
    > [!NOTE]
    > Győződjön meg arról, hogy az **engedélyezési szintet** `Anonymous`értékre állítja. Ha a `Function`alapértelmezett szintjét választja, akkor a függvény-végpont eléréséhez a kérelmekben be kell mutatnia a [függvény kulcsát](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) .
    
4. Válassza a **Létrehozás** lehetőséget a függvény projekt és a http által aktivált függvény létrehozásához.
