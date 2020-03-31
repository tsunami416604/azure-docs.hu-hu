---
title: Csatlakozás a Dropboxhoz
description: A Dropboxban az Azure Logic Apps használatával fájlokat feltöltő és kezelő feladatok és munkafolyamatok automatizálása
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665751"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Fájlok feltöltése és kezelése a Dropboxban az Azure Logic Apps használatával

A Dropbox-összekötővel és az Azure Logic Apps alkalmazásokkal automatizált munkafolyamatokat hozhat létre, amelyek fájlokat töltenek fel és kezelnek a Dropbox-fiókjában. 

Ez a cikk bemutatja, hogyan csatlakozhat a Dropboxhoz a logikai alkalmazásból, majd adja hozzá a **Dropbox-ot Fájl létrehozásakor,** és a Dropbox **Fájltartalom begete útvonalművelettel.**

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A [Dropbox számla](https://www.dropbox.com/), amely akkor iratkozzon fel ingyen. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiók közötti kapcsolat létrehozásához.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ebben a példában egy üres logikai alkalmazásra van szükség.

## <a name="add-trigger"></a>Trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. A keresőmező alatt válassza az **Összes**lehetőséget. A keresőmezőbe írja be szűrőként a "dropbox" kifejezést.
Az eseményindítók listájában válassza ezt az eseményindítót: **Fájl létrehozásakor**

   ![Dropbox-eseményindító kiválasztása](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Jelentkezzen be Dropbox-fiókjának hitelesítő adataival, és engedélyezze a hozzáférést a Dropbox-adatokhoz az Azure Logic Apps számára.

1. Adja meg az eseményindítóhoz szükséges információkat. 

   Ebben a példában jelölje ki azt a mappát, amelyben nyomon szeretné követni a fájlok létrehozását. A mappák tallózásához válassza a **Mappa** mező melletti mappaikont.

## <a name="add-action"></a>Művelet hozzáadása

Most adjon hozzá egy műveletet, amely leveszi a tartalmat bármely új fájlból.

1. Az eseményindító alatt válassza a **Következő lépés lehetőséget.** 

1. A keresőmező alatt válassza az **Összes**lehetőséget. A keresőmezőbe írja be szűrőként a "dropbox" kifejezést.
A műveletlistában válassza ezt a műveletet: **Fájltartalom beszerezni e-görbével**

1. Ha még nem engedélyezte az Azure Logic Apps számára a Dropbox elérését, engedélyezze a hozzáférést.

1. A használni kívánt fájlelérési út tallózásához a **Fájl elérési útja** mező mellett válassza a három pont (**...**) gombot. 

   A Fájl elérési **útja** mezőben is kattinthat, és a dinamikus tartalomlistában válassza a **Fájl elérési út**lehetőséget, amelynek értéke kimenetként érhető el az előző szakaszban hozzáadott eseményindítóból.

1. Ha elkészült, mentse a logikai alkalmazást.

1. A logikai alkalmazás aktiválásához hozzon létre egy új fájlt a Dropboxban.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő Swagger-fájljában leírt technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán](/connectors/dropbox/)találja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
