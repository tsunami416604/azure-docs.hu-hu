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
ms.openlocfilehash: 5c82d7ad3cf9c2318d3bf5d0157f00730a24a968
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57457811"
---
A Visual Studio Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

2. Az **Új projekt** párbeszédpanelen válassza a **Telepítve** elemet, bontsa ki a **Visual C#** > **Felhő** csomópontot, válassza az **Azure Functions** lehetőséget, írja be a projekt **Nevét**, majd kattintson az **OK** gombra. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

    ![Új projekt párbeszédpanel a Visual Studióban egy függvény létrehozásához](./media/functions-vstools-create/functions-vs-new-project.png)

3. Használja a kép alatti táblázatban megadott beállításokat.

    ![A Visual Studio Új függvény párbeszédpanelje](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions 2.x <br />(.NET Core) | Ez létrehoz egy függvényprojektet, amely az Azure Functions futtatókörnyezetének .NET Core-t támogató 2.x verzióját használja. Az Azure Functions 1.x támogatja a .NET-keretrendszert. További információ: [Azure Functions futtatókörnyezet-verzió megcélzása](../articles/azure-functions/functions-versions.md).   |
    | **Sablon** | HTTP eseményindító | Ez létrehoz egy HTTP-kérelem által aktivált függvényt. |
    | **Storage-fiók**  | Storage Emulator | A HTTP-triggerek nem használják a Storage-fiók kapcsolatát. A többi triggernek érvényes Storage-fiókhoz tartozó kapcsolati sztringre van szüksége. |
    | **Hozzáférési jogosultságok** | Névtelen | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokról és az engedélyezésről a [HTTP- és webhookkötések](../articles/azure-functions/functions-bindings-http-webhook.md) című témakör [Engedélyezési kulcsok](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) szakaszában talál további információt. |
    
    > [!NOTE]
    > Mindenképpen állítsa be a **hozzáférési jogosultságok** való `Anonymous`. Ha úgy dönt, hogy az alapértelmezett szintű `Function`, kell mutatnia a [függvénykulcs](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) a kérések a függvény-végpontot.
    
4. A függvényprojekt és a HTTP-vel aktivált függvény létrehozásához kattintson az **OK** gombra.
