---
title: Kapcsolódás a Dropboxhoz
description: Fájlok feltöltése és kezelése a Dropbox REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: b38f9b9626e5eec0272256aacc71f7503a006dd6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789821"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Fájlok feltöltése és kezelése a Dropboxban Azure Logic Apps használatával

A Dropbox Connector és a Azure Logic Apps segítségével létrehozhat olyan automatizált munkafolyamatokat, amelyek a Dropbox-fiókban lévő fájlokat töltenek fel és kezelhetnek. 

Ez a cikk bemutatja, hogyan csatlakozhat a Dropboxhoz a logikai alkalmazásból, majd hozzáadhatja a Dropboxot **egy fájl létrehozásakor** , valamint a Dropbox **Fájl letöltése a Path** művelettel.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [Dropbox-fiók](https://www.dropbox.com/), amelyet ingyenesen regisztrálhat. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiók közötti kapcsolat létrehozásához.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ebben a példában egy üres logikai alkalmazásra van szükség.

## <a name="add-trigger"></a>Trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. A keresőmező alatt válassza az **összes**lehetőséget. A keresőmezőbe írja be a "Dropbox" kifejezést a szűrőként.
Az eseményindítók listából válassza ki ezt az eseményindítót: **fájl létrehozásakor**

   ![Dropbox-trigger kiválasztása](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Jelentkezzen be a Dropbox-fiókja hitelesítő adataival, és engedélyezze a hozzáférést a Dropbox-adatokhoz Azure Logic Apps.

1. Adja meg az triggerhez szükséges adatokat. 

   Ebben a példában válassza ki azt a mappát, ahol nyomon szeretné követni a fájl létrehozását. A mappák tallózásához válassza a **mappa mező** melletti mappa ikont.

## <a name="add-action"></a>Művelet hozzáadása

Most adjon hozzá egy műveletet, amely minden új fájlból lekéri a tartalmat.

1. A trigger alatt válassza a **következő lépés**lehetőséget. 

1. A keresőmező alatt válassza az **összes**lehetőséget. A keresőmezőbe írja be a "Dropbox" kifejezést a szűrőként.
A műveletek listából válassza a következő műveletet: **fájl tartalmának beolvasása elérési út használatával**

1. Ha még nem engedélyezte Azure Logic Apps a Dropbox elérését, engedélyezze a hozzáférést most.

1. A használni kívánt fájl elérési útjának tallózásához kattintson a **fájl elérési útja** mező melletti három pontra ( **...** ) gombra. 

   Kattintson a **fájl elérési útja** mezőre, és a dinamikus tartalmak listájából válassza ki a **fájl elérési útja**lehetőséget, amelynek értéke az előző szakaszban hozzáadott trigger kimenetként érhető el.

1. Ha elkészült, mentse a logikai alkalmazást.

1. A logikai alkalmazás elindításához hozzon létre egy új fájlt a Dropboxban.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/dropbox/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
