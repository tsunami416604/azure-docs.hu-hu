---
title: Az Azure blob storage – Azure Logic Apps csatlakoztatása
description: Hozzon létre és kezelheti a blobokat az Azure Logic Apps az Azure storage-ban
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: ea3e97db9ec560306788943d92a7670025f38bdc
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310370"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Hozzon létre és kezelheti a blobokat az Azure Logic Apps az Azure blob storage-ban

Ez a cikk bemutatja, hogyan elérheti és kezelheti, mint belül egy logikai alkalmazást az Azure Blob Storage-összekötővel a az Azure storage-fiókban tárolt fájlok. Ezzel a módszerrel, amely a feladatok és a fájljainak kezelésére szolgáló munkafolyamatok automatizálása a logic apps is létrehozhat. Ha például hozhat létre logikai alkalmazásokat, amelyek létrehozása, beolvasása, frissítése és törlése a fájlokat a storage-fiókban.

Tegyük fel, hogy egy eszköz, amely frissül, és a egy Azure-webhelyen. amely a logikai alkalmazás eseményindítóját funkcionál. Ha ez az esemény történik, akkor is a logikai alkalmazás frissítése néhány fájlt a blob storage-tároló, amely egy műveletet a logikai alkalmazásban.

> [!NOTE]
> A Logic Apps nem támogatja az Azure storage-fiókok tűzfalon keresztül közvetlenül csatlakozik. Ezek a tárfiókok eléréséhez mindkét módszerhez itt:
>
> * Hozzon létre egy [integrációs szolgáltatás környezet](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), amelyhez csatlakozhat egy Azure virtuális hálózatban lévő erőforrásokra.
>
> * Ha már használja az API Management, használhatja a szolgáltatást az ebben a forgatókönyvben. További információ: [egyszerű vállalati integrációs architektúra](https://aka.ms/aisarch).

Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Összekötő-specifikus technikai tudnivalókért tekintse meg a <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">összekötő-referencia az Azure Blob Storage</a>.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy [Azure storage-fiók és a storage-tároló](../storage/blobs/storage-quickstart-blobs-portal.md)

* A logikai alkalmazás, ahol hozzáférésre van szüksége az Azure blob storage-fiókjában. A logikai alkalmazás elindításához egy Azure Blob Storage-eseményindítóval kell egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>A blob storage-eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

Ez a példa bemutatja, hogy miként indítható el a logikai alkalmazás munkafolyamatának a a **Azure Blob Storage - amikor egy blob hozzáadásakor vagy módosításakor (csak tulajdonságok)** eseményindító egy blob tulajdonságai lekérdezi hozzáadásakor, vagy frissíteni a storage-tárolóba. 

1. Az Azure Portalon vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logikaialkalmazás-Tervező. Ebben a példában az Azure Portalt használja.

2. A keresőmezőbe írja be az "azure blob" szűrőként. Az eseményindítók listában jelölje ki a kívánt eseményindító.

   Ebben a példában ez az eseményindító: **Az Azure Blob Storage - amikor egy blob hozzáadásakor vagy módosításakor (csak tulajdonságok)**

   ![Trigger kiválasztása](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Ha a kapcsolat részleteivel, kér [létrehozása a blob storage kapcsolat most](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a szükséges információkat az eseményindító.

   Ebben a példában válassza ki a tároló és a figyelni kívánt mappát.

   1. Az a **tároló** jelölje ki a mappa ikont.

   2. A mappák listájában válassza ki a szögletes zárójelet ( **>** ), és keresse meg, amíg nem található, és válassza ki a kívánt mappát.

      ![Select folder (Mappa kiválasztása)](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Válassza ki a időközét és gyakoriságát, hogy milyen gyakran szeretné ellenőrizni a mappában a változásokat az eseményindító.

4. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

5. Most már folytathatja, egy vagy több művelet hozzáadása a logikai alkalmazáshoz, a feladatok a trigger eredményekkel végrehajtására vonatkozó szándékát.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>A blob storage-művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ebben a példában a logikai alkalmazás kezdődik a [ismétlődési trigger](../connectors/connectors-native-recurrence.md).

1. Az Azure Portalon vagy a Visual Studióban nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben. Ebben a példában az Azure Portalt használja.

2. A Logic App Designerben az eseményindítót vagy műveletet, válassza **új lépés** > **művelet hozzáadása**.

   ![Művelet hozzáadása](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó nyílra. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza **művelet hozzáadása**.

3. A keresőmezőbe írja be az "azure blob" szűrőként. Jelölje ki az elvégzendő műveletek listájában.

   Ebben a példában ez a művelet: **Az Azure Blob Storage - Get blob tartalma**

   ![Művelet kiválasztása](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Ha a kapcsolat részleteivel, kér [mostantól az Azure Blob Storage-kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a művelet a szükséges információkat.

   Ebben a példában válassza ki a kívánt fájlt.

   1. Az a **Blob** jelölje ki a mappa ikont.
  
      ![Select folder (Mappa kiválasztása)](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Keresse meg és válassza ki a fájlt a blob alapján szeretne **azonosító** számát. Ez annak **azonosító** a blob metaadatai, a korábban ismertetett blob storage-eseményindító által visszaadott száma.

5. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.
A logikai alkalmazás teszteléséhez győződjön meg róla, hogy a kijelölt mappa tartalmaz egy blobot.

Ebben a példában csak lekéri egy BLOB tartalmát. A tartalom megtekintéséhez adjon hozzá egy újabb műveletet, amely létrehoz egy fájlt a blobbal együtt egy másik-összekötő használatával. Hozzáadhat például egy OneDrive-műveletet, amely létrehoz egy fájlt a blob tartalma alapján.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>A storage-fiók csatlakoztatása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő Open API (korábbi nevén Swagger) fájl, tekintse meg a [összekötő referencialapja](/connectors/azureblobconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
