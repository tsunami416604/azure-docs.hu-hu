---
title: Csatlakozás az Azure blob storage - Azure Logic Apps |} Microsoft Docs
description: Létrehozása és kezelése az Azure Logic Apps Azure Storage blobs
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 15d737cd85f70717bfdf15dfb3d179f977b63c72
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "34723432"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Létrehozása és kezelése az Azure blob Storage tárolóban az Azure Logic Apps blobok

Ez a cikk bemutatja, hogyan elérheti és blobként belül egy logikai alkalmazást az Azure Blob Storage-összekötővel a az Azure storage-fiókban tárolt fájlok kezelésére. Ily módon a logic apps, feladatok és a munkafolyamatok a fájljainak kezelésére szolgáló automatizáló is létrehozhat. Logic Apps alkalmazásokat, amelyek létrehozása, beolvasása, frissítése és törlése a tárfiókban lévő fájlok például hozhat létre.

Tegyük fel, hogy rendelkezik-e olyan eszköz, amely egy Azure webhelyen frissül. amely a logikai alkalmazásnak az eseményindító funkcionál. Ha ez az esemény akkor fordul elő, akkor a Logic Apps alkalmazást, frissítse a blob storage tárolót, amely a Logic Apps alkalmazást a művelet néhány fájlt. 

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át a [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [gyors üzembe helyezés: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Összekötő-specifikus műszaki információkért lásd: a <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">Azure Blob Storage összekötő hivatkozás</a>.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-tárfiók és tároló](../storage/blobs/storage-quickstart-blobs-portal.md)

* A logikai alkalmazást, be kell hozzáférés az Azure blob storage-fiók. Egy Azure Blob Storage eseményindító a Logic Apps alkalmazást elindításához szükséges egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>A blob storage eseményindító hozzáadása

Az Azure Logic Apps, minden logikai alkalmazást be kell kezdődnie egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor indul, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító következik be, a Logic Apps motor hoz létre egy logic app-példány, az alkalmazás a munkafolyamat futásának indításakor.

Ez a példa bemutatja, hogyan megkezdheti a logic app munkafolyamat a **Azure Blob Storage - blob jelenik meg, vagy módosításakor (csak a Tulajdonságok)** egy blob tulajdonságai lekérdezi hozzáadásakor vagy frissítése a tároló az eseményindító. 

1. Az Azure portálon vagy a Visual Studio hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic App Tervező. A példa az Azure-portálon.

2. A keresési mezőbe írja be az "azure-blobot" szűrőként. Az eseményindítók listájából válassza ki a kívánt eseményindító.

   Ez a példa az eseményindító: **Azure Blob Storage - blob jelenik meg, vagy módosításakor (csak a Tulajdonságok)**

   ![Trigger kiválasztása](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Ha a kapcsolat részletes számítógép [létrehozása a blob storage kapcsolat most](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a szükséges adatokat az eseményindító.

   Ehhez a példához válassza ki a tároló és a figyelni kívánt mappa.

   1. Az a **tároló** mezőben adja meg a mappa ikonra.

   2. A mappák listájában válassza ki a szögletes zárójel ( **>** ), majd keresse meg, amíg keresse meg és válassza ki azt a mappát. 

      ![Válasszon mappát](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Válassza ki az időköz és gyakoriságát, hogy milyen gyakran szeretné az eseményindító ellenőrizze a mappát a változásokat.

4. Amikor elkészült, a designer eszköztáron válassza **mentése**.

5. Most már folytathatja hozzáadása egy vagy több műveletet szeretne végezni az eseményindító eredményekkel feladatokhoz a Logic Apps alkalmazást.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>A blob storage művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamatban, amely egy eseményindító vagy egy másik művelet a következő lépés. Az ebben a példában a logikai alkalmazás kezdődik-e a [ismétlődési eseményindító](../connectors/connectors-native-recurrence.md).

1. Az Azure portálon vagy a Visual Studio nyissa meg a Logic Apps alkalmazást Logic App tervezőben. A példa az Azure-portálon.

2. Válassza ki az eseményindító vagy a művelet, a Logic App Designer **új lépés** > **művelet hozzáadása**.

   ![Művelet hozzáadása](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Meglévő lépései közötti művelet hozzáadása az egérmutatót a kapcsolódó nyílra. 
   Válassza ki a plusz jelre (**+**), amely akkor jelenik meg, és válassza a **művelet hozzáadása**.

3. A keresési mezőbe írja be az "azure-blobot" szűrőként. Válassza ki az elvégzendő műveletek listában.

   A példában ez a művelet: **Azure Blob Storage - Get blob tartalma**

   ![Kijelölési művelet](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Ha a kapcsolat részletes számítógép [mostantól az Azure Blob Storage-kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a művelet a szükséges információkat. 

   Ehhez a példához válassza ki a kívánt fájlra.

   1. Az a **Blob** mezőben adja meg a mappa ikonra.
  
      ![Válasszon mappát](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Keresse meg és jelölje ki a kívánt alapján a blob fájlt **azonosító** számát. Ez található **azonosító** szám, amely a korábban leírt blob storage eseményindító ad vissza a blob metaadatai között.

5. Amikor elkészült, a designer eszköztáron válassza **mentése**.
A logikai alkalmazás teszteléséhez győződjön meg arról, hogy a mappa tartalmaz egy blobot.

Ebben a példában csak lekérdezi a BLOB tartalmát. A tartalom megtekintéséhez, vegye fel a blobbal együtt, egy másik-összekötő használatával létrehoz egy fájlt egy másik művelet. Adja hozzá például a onedrive vállalati verzió művelet, amely létrehoz egy blob tartalma alapján.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>A tárfiók kapcsolódni

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Összekötő-referencia

Technikai részletek, például eseményindítók műveletek vagy -korlátok, az összekötő a Swagger-fájl szerint, lásd: a [összekötő referencialapja](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Tudnivalók más [Logic Apps-összekötők](../connectors/apis-list.md)
