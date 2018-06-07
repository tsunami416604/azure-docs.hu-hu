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
ms.openlocfilehash: 16bda26a80611b29fdb100736cfc48978e63f75a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34702436"
---
A Visual Studio Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

2. Az **Új projekt** párbeszédpanelen válassza a **Telepítve** elemet, bontsa ki a **Visual C#** > **Felhő** csomópontot, válassza az **Azure Functions** lehetőséget, írja be a projekt **Nevét**, majd kattintson az **OK** gombra. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

    ![Új projekt párbeszédpanel a Visual Studióban egy függvény létrehozásához](./media/functions-vstools-create/functions-vstools-add-new-project.png)

3. Használja a kép alatti táblázatban megadott beállításokat.

    ![A Visual Studio Új függvény párbeszédpanelje](./media/functions-vstools-create/functions-vstools-add-new-function.png) 

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions v1 <br />(.NET-keretrendszer) | Ez létrehoz egy függvényprojektet, amely az Azure Functions futtatókörnyezetének 1-es verzióját használja. A .NET Core-t támogató 2-es verziójú futtatókörnyezet jelenleg előzetes verzióban érhető el. További információ: [Azure Functions futtatókörnyezet-verzió megcélzása](../articles/azure-functions/functions-versions.md).   |
    | **Sablon** | HTTP eseményindító | Ez létrehoz egy HTTP-kérelem által aktivált függvényt. |
    | **Storage-fiók**  | Storage Emulator | A HTTP-triggerek nem használják a Storage-fiók kapcsolatát. A többi triggernek érvényes Storage-fiókhoz tartozó kapcsolati sztringre van szüksége. |
    | **Hozzáférési jogosultságok** | Névtelen | A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését. A kulcsokról és az engedélyezésről a [HTTP- és webhookkötések](../articles/azure-functions/functions-bindings-http-webhook.md) című témakör [Engedélyezési kulcsok](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) szakaszában talál további információt. |
4. A függvényprojekt és a HTTP-vel aktivált függvény létrehozásához kattintson az **OK** gombra.

