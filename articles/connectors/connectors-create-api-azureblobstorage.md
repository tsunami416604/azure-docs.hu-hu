---
title: Kapcsolódás az Azure Blob Storage-hoz – Azure Logic Apps
description: Blobok létrehozása és kezelése az Azure Storage-ban Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d57ea1a881980203b1c8f216239b27b64f0d71cd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051056"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Blobok létrehozása és kezelése az Azure Blob Storage-ban Azure Logic Apps

Ez a cikk bemutatja, hogyan érheti el és kezelheti a blobként tárolt fájlokat az Azure Storage-fiókban egy logikai alkalmazásban az Azure Blob Storage-összekötő használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a feladatokat és munkafolyamatokat a fájlok kezeléséhez. Létrehozhat például olyan logikai alkalmazásokat, amelyek fájlokat hozhatnak létre, kérhetnek, frissíthetnek és törölhetnek a Storage-fiókban.

Tegyük fel, hogy rendelkezik egy olyan eszközzel, amely frissítve lesz egy Azure-webhelyen. Ez a logikai alkalmazás triggerként működik. Ha ez az esemény történik, a logikai alkalmazás a blob Storage-tárolóban is frissítheti a fájlt, amely egy művelet a logikai alkalmazásban.

> [!NOTE]
> Logic Apps nem támogatja közvetlenül az Azure Storage-fiókokhoz való csatlakozást a tűzfalakon keresztül. A Storage-fiókok eléréséhez használja az alábbi lehetőségek egyikét:
>
> * Hozzon létre egy [integrációs szolgáltatási környezetet](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), amely egy Azure-beli virtuális hálózat erőforrásaihoz tud csatlakozni.
>
> * Ha már használja a API Management, ezt a szolgáltatást használhatja ehhez a forgatókönyvhöz. További információ: [Simple Enterprise Integration Architecture](https://aka.ms/aisarch).

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és [a gyors útmutató: Hozza létre az első logikai](../logic-apps/quickstart-create-first-logic-app-workflow.md)alkalmazását. Az összekötő-specifikus technikai információk az [Azure Blob Storage-összekötő dokumentációjában](/connectors/azureblobconnector/)olvashatók.

## <a name="limits"></a>Korlátok

* Alapértelmezés szerint az Azure Blob Storage-műveletek a *50 MB vagy annál kisebb*fájlokat képesek olvasni vagy írni. Ha 50 MB-nál nagyobb fájlokat szeretne kezelni, de legfeljebb 1024 MB-ra, az Azure Blob Storage-műveletek támogatják az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md). A **blob-tartalom** beolvasása művelet implicit módon adatdarabolást használ.

* Az Azure Blob Storage-eseményindítók nem támogatják a darabolást. Fájl tartalmának kérésekor a triggerek csak 50 MB vagy annál kisebb fájlokat választanak ki. A 50 MB-nál nagyobb fájlok lekéréséhez kövesse az alábbi mintát:

  * Használjon olyan Azure Blob Storage triggert, amely a fájl tulajdonságait adja vissza, például **egy blob hozzáadásakor vagy módosításakor (csak tulajdonságok)** .

  * Kövesse a triggert az Azure Blob Storage **blob-tartalom** lekérése művelettel, amely beolvassa a teljes fájlt, és implicit módon használja a darabolást.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Azure Storage-fiók és Storage-tároló](../storage/blobs/storage-quickstart-blobs-portal.md)

* Az a logikai alkalmazás, amelyen hozzá kell férnie az Azure Blob Storage-fiókhoz. A logikai alkalmazás Azure Blob Storage triggerrel való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>BLOB Storage-trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie [](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

Ebből a példából megtudhatja, hogyan indíthat el egy logikai alkalmazás-munkafolyamatot a **Blobok hozzáadásakor vagy módosításakor (csak tulajdonságok)** , ha a blob tulajdonságai hozzáadódnak vagy frissülnek a tárolóban.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert. Ez a példa a Azure Portal használja.

2. A keresőmezőbe írja be szűrőként az "Azure Blob" kifejezést. Az eseményindítók listából válassza ki a kívánt eseményindítót.

   Ez a példa a következő triggert használja: **BLOB hozzáadásakor vagy módosításakor (csak tulajdonságok)**

   ![Trigger kiválasztása](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a blob Storage](#create-connection)-kapcsolatát. Ha már létezik a kapcsolatai, adja meg a szükséges információkat az triggerhez.

   Ebben a példában válassza ki a figyelni kívánt tárolót és mappát.

   1. A **tároló** mezőben válassza a mappa ikont.

   2. A mappalistában válassza a jobb oldali szögletes zárójelet ( **>** ), majd tallózással keresse meg és válassza ki a kívánt mappát.

      ![Select folder (Mappa kiválasztása)](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Válassza ki az intervallumot és a gyakoriságot, hogy milyen gyakran szeretné megkeresni a triggert a mappa változásaihoz.

4. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

5. Most folytassa a logikai alkalmazáshoz egy vagy több művelet hozzáadását azokkal a feladatokkal, amelyeket el szeretne végezni az trigger eredményeivel.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB Storage-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben. Ez a példa a Azure Portal használja.

2. A Logic app Designerben az trigger vagy a művelet alatt válassza az **új lépés**lehetőséget.

   ![Művelet hozzáadása](./media/connectors-create-api-azureblobstorage/add-action.png) 

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

3. A keresőmezőbe írja be szűrőként az "Azure Blob" kifejezést. A műveletek listából válassza ki a kívánt műveletet.

   Ez a példa a következő műveletet használja: **BLOB tartalmának beolvasása**

   ![Művelet kiválasztása](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre most az Azure Blob Storage](#create-connection)-kapcsolatát.
Ha a kapcsolat már létezik, adja meg a szükséges információkat a művelethez.

   Ebben a példában válassza ki a kívánt fájlt.

   1. A **blob** mezőben válassza a mappa ikont.
  
      ![Select folder (Mappa kiválasztása)](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Keresse meg és válassza ki a kívánt fájlt a blob azonosítójának száma alapján. Ezt az **azonosítót** a blob metaadatokban találja, amelyeket a korábban leírt blob Storage-trigger ad vissza.

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.
A logikai alkalmazás teszteléséhez győződjön meg arról, hogy a kiválasztott mappa blobot tartalmaz.

Ez a példa csak egy blob tartalmát kéri le. A tartalom megtekintéséhez adjon hozzá egy másik műveletet, amely létrehoz egy fájlt a blobtal egy másik összekötő használatával. Adjon meg például egy olyan OneDrive műveletet, amely létrehoz egy fájlt a blob tartalma alapján.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Kapcsolódás a Storage-fiókhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő Open API (korábban hencegő) fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/azureblobconnector/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
