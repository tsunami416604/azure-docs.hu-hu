---
title: Csatlakozhat a Dropboxhoz – Azure Logic Apps
description: Töltheti fel és kezelheti a fájlok a Dropboxban REST API-k és Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314416"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Töltse fel, és a Dropboxban lévő fájlok kezelése az Azure Logic Apps használatával

A Dropbox-összekötő és az Azure Logic Apps hozhat létre automatizált munkafolyamatok, amelyek feltöltik, és a Dropbox-fiókjában található fájlok kezeléséhez. 

Ez a cikk bemutatja, hogyan csatlakozhat a Dropboxhoz, a logikai alkalmazást, és adja a Dropbox **amikor létrejön egy fájl** eseményindítót és a Dropbox **fájl tartalmának beolvasása elérési út segítségével** művelet.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* A [Dropbox-fiókjának](https://www.dropbox.com/), amely feliratkozhat az ingyenes. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiókjában közötti kapcsolat létrehozásához.

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ebben a példában szüksége lesz egy üres logikai alkalmazás.

## <a name="add-trigger"></a>Eseményindító hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. A keresőmező alatt válassza ki a **összes**. A keresőmezőbe írja be szűrőként "dropbox".
Az eseményindítók listában jelölje ki az eseményindító: **Amikor létrejön egy fájl**

   ![Dropbox-trigger kiválasztása](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Jelentkezzen be a Dropbox-fiókja hitelesítő adatait, és a Dropbox-adatokhoz való hozzáférés engedélyezése az Azure Logic Apps.

1. Adja meg a szükséges információkat az eseményindító. 

   Ebben a példában válassza ki a mappát, ahol nyomon követheti a fájlok létrehozási szeretné. Tallózás a mappák, válassza a mappa ikont a a **mappa** mezőbe.

## <a name="add-action"></a>Művelet felvétele

Adjon hozzá egy műveletet, amely lekérdezi a tartalom minden olyan új fájlból.

1. Az eseményindító területén válassza a **következő lépés**. 

1. A keresőmező alatt válassza ki a **összes**. A keresőmezőbe írja be szűrőként "dropbox".
A műveletek listából válassza a következő műveletet: **Fájl tartalmának beolvasása elérési út segítségével**

1. Ha már nem jogosult az Azure Logic Apps Dropbox eléréséhez, engedélyezze a hozzáférést most.

1. Tallózással keresse meg a használni kívánt, a fájl elérési útja a **fájl elérési útja** válassza ki azt a három pontra (**...** ) gombra. 

   Belül is kattinthat a **fájl elérési útja** mezőbe, és a dinamikus tartalmú listából válassza ki a **fájl elérési útja**, amelynek az értéke érhető el az eseményindítót az előző szakaszban hozzáadott kimenete.

1. Ha elkészült, mentse a logikai alkalmazást.

1. A logikai alkalmazás elindításához, hozzon létre egy új fájlt a Dropboxban.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő OpenAPI (korábbi nevén Swagger) fájl, tekintse meg a [összekötő referenciájának oldalát](/connectors/dropbox/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
