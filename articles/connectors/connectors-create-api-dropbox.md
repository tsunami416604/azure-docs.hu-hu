---
title: Kapcsolódás a Dropboxhoz – Azure Logic Apps
description: Fájlok feltöltése és kezelése a Dropbox REST API-kkal és Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 4e0689454ec074348fcbc775373a48d6825cfac4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050979"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Fájlok feltöltése és kezelése a Dropboxban Azure Logic Apps használatával

A Dropbox Connector és a Azure Logic Apps segítségével létrehozhat olyan automatizált munkafolyamatokat, amelyek a Dropbox-fiókban lévő fájlokat töltenek fel és kezelhetnek. 

Ez a cikk bemutatja, hogyan csatlakozhat a Dropboxhoz a logikai alkalmazásból, majd hozzáadhatja a Dropboxot **egy fájl létrehozásakor** , valamint a Dropbox **Fájl letöltése a Path** művelettel.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [Dropbox-fiók](https://www.dropbox.com/), amelyet ingyenesen regisztrálhat. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiók közötti kapcsolat létrehozásához.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ebben a példában egy üres logikai alkalmazásra van szükség.

## <a name="add-trigger"></a>Eseményindító hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. A keresőmező alatt válassza az **összes**lehetőséget. A keresőmezőbe írja be a "Dropbox" kifejezést a szűrőként.
Az eseményindítók listából válassza ki a következő eseményindítót: **Fájl létrehozásakor**

   ![Dropbox-trigger kiválasztása](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Jelentkezzen be a Dropbox-fiókja hitelesítő adataival, és engedélyezze a hozzáférést a Dropbox-adatokhoz Azure Logic Apps.

1. Adja meg az triggerhez szükséges adatokat. 

   Ebben a példában válassza ki azt a mappát, ahol nyomon szeretné követni a fájl létrehozását. A mappák tallózásához válassza a mappa mező melletti mappa ikont.

## <a name="add-action"></a>Művelet felvétele

Most adjon hozzá egy műveletet, amely minden új fájlból lekéri a tartalmat.

1. A trigger alatt válassza a **következő lépés**lehetőséget. 

1. A keresőmező alatt válassza az **összes**lehetőséget. A keresőmezőbe írja be a "Dropbox" kifejezést a szűrőként.
A műveletek listából válassza ki ezt a műveletet: **Fájl tartalmának beolvasása elérési út alapján**

1. Ha még nem engedélyezte Azure Logic Apps a Dropbox elérését, engedélyezze a hozzáférést most.

1. A használni kívánt fájl elérési útjának tallózásához kattintson a **fájl elérési útja** mező melletti három pontra ( **...** ) gombra. 

   Kattintson a **fájl elérési útja** mezőre, és a dinamikus tartalmak listájából válassza ki a **fájl elérési útja**lehetőséget, amelynek értéke az előző szakaszban hozzáadott trigger kimenetként érhető el.

1. Ha elkészült, mentse a logikai alkalmazást.

1. A logikai alkalmazás elindításához hozzon létre egy új fájlt a Dropboxban.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/dropbox/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
