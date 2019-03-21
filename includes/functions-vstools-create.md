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
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227218"
---
A Visual Studio Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. Csoport funkciók függvényalkalmazás felügyeleti, üzembe helyezéséhez és erőforrás-megosztás használhatja egy logikai egységet.

1. A Visual Studióban az a **fájl** menüjében válassza **új** > **projekt**.

2. Az a **új projekt** párbeszédpanelen jelölje ki **telepített** > **Visual C#**   >  **felhőalapú**  >  **Az azure Functions**. Adjon meg egy nevet a projektnek, és válassza ki **OK**. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

    ![Függvény létrehozása a Visual Studio új projekt párbeszédpanel](./media/functions-vstools-create/functions-vs-new-project.png)

3. Használja a kép alatti táblázatban megadott beállításokat.

    ![Új függvény párbeszédpanel a Visual Studióban](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions 2.x <br />(.NET Core) | Ez a beállítás a verzió 2.x verziójú futtatókörnyezet, az Azure Functions, amely támogatja a .NET Core használó Functions-projektet hoz létre. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információkért lásd: [cél Azure Functions futtatókörnyezet-verzió](../articles/azure-functions/functions-versions.md).   |
    | **Sablon** | HTTP eseményindító | Ezzel a beállítással létrehoz egy HTTP-kérelem által aktivált függvény. |
    | **Storage-fiók**  | Storage emulator | HTTP-trigger nem használja az Azure-Tárfiók kapcsolata. A többi triggernek érvényes Storage-fiókhoz tartozó kapcsolati sztringre van szüksége. |
    | **Hozzáférési jogosultságok** | Névtelen | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokról és az engedélyezésről a [HTTP- és webhookkötések](../articles/azure-functions/functions-bindings-http-webhook.md) című témakör [Engedélyezési kulcsok](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) szakaszában talál további információt. |
    
    > [!NOTE]
    > Mindenképpen állítsa be a **hozzáférési jogosultságok** való `Anonymous`. Ha úgy dönt, hogy az alapértelmezett szint, `Function`, módosítania kell mutatnia a [függvénykulcs](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) a kérések a függvény-végpontot.
    
4. Válassza ki **OK** a Functions-projektet és a HTTP által aktivált függvény létrehozásához.
