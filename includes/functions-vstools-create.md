---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a8be74558fb71ceb1c28c27004b27a5a5f477a8f
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650701"
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
4. A függvényprojekt és a HTTP-vel aktivált függvény létrehozásához kattintson az **OK** gombra.
