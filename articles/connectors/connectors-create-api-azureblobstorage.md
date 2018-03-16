---
title: "Adja hozzá az Azure blob storage összekötőt a Logic Apps |} Microsoft Docs"
description: "Első lépések és az Azure blob storage-összekötő konfigurálása a logikai alkalmazás"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: estfan; ladocs
ms.openlocfilehash: 7aaff2ac78201c4484105c6cacc5f0fef19ca7b5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>A logikai alkalmazás az Azure blob storage-összekötő használatára
Az Azure Blob storage-összekötő használatával töltse fel, frissítése, beszerzése, illetve törölni a blobot, amely a tárfiókon belül egy logikai alkalmazást.  

Az Azure blob storage szolgáltatással meg:

* A munkafolyamat új projektek feltölteni, vagy nemrég frissített fájlok első hozhat létre.
* Fájl metaadatot beszerezni, törölje a fájlt, a fájlok másolása és további műveleteket használni. Például frissítésekor egy eszközt az Azure webhelyén (trigger), majd frissítse a fájlt a blob Storage tárolóban (a műveletet). 

Ez a témakör bemutatja, hogyan a blob storage összekötő használatára a logikai alkalmazás.

A Logic Apps kapcsolatos további információkért lásd: [Mik azok a logic apps](../logic-apps/logic-apps-overview.md) és [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-blob-storage"></a>Csatlakozás az Azure blob storage
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először létre kell hoznia egy *kapcsolat* a szolgáltatáshoz. Egy kapcsolat egy logikai alkalmazást és egy másik szolgáltatás közötti kapcsolatot biztosít. Például szeretne csatlakozni egy tárfiókot, először létre kell hoznia egy blob-tároló *kapcsolat*. VPN-kapcsolat létrehozásához adja meg a hitelesítő adatokkal kell általában hozzáférhetnek a szolgáltatáshoz való kapcsolódás esetén. Ezért az Azure storage adja meg a hitelesítő adatokat a tárfiókhoz a VPN-kapcsolat létrehozásához. 

#### <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Eseményindítók
Ez az összekötő nem rendelkezik a eseményindítókat. Más eseményindítók segítségével indítsa el a logikai alkalmazás, például az ismétlődési eseményindítót, egy HTTP Webhook eseményindító, eseményindítók érhető el a többi összekötőt, és több. [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) példaként szolgál.

## <a name="use-an-action"></a>Egy művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett.

1. Kattintson a plusz ikonra. Több beállítások megtekintéséhez: **művelet hozzáadása**, **feltétel hozzáadása**, vagy az egyik a **további** beállítások.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Válasszon **művelet hozzáadása**.
3. A szövegmezőbe írja be a "blob" az összes elérhető műveletek listájának beolvasása.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. Válassza ki a fenti példában **AzureBlob - elérési út használatával Get fájlmetaadata**. Ha már létezik egy kapcsolat, majd válassza ki a **...** Válassza ki a fájlt (objektumválasztó megjelenítése) gombra.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Ha a kapcsolati adatokat kéri, adja meg a részleteket a VPN-kapcsolat létrehozásához. [A kapcsolat létrehozása](connectors-create-api-azureblobstorage.md#create-the-connection) a témakörben ismertetett ezeket a tulajdonságokat. 
   
   > [!NOTE]
   > Ebben a példában azt megszerezni a metaadatokat egy fájl. A metaadatok, vegye fel egy újabb műveletet, amely létrehoz egy új fájlt egy másik összekötő segítségével. Adja hozzá például a onedrive vállalati verzió művelet által létrehozott egy új "teszt" fájlt a metaadatok alapján. 


5. **Mentés** a módosításokat (bal felső sarkában az eszköztár). A Logic Apps alkalmazást menti, és lehet, hogy automatikusan engedélyezve.

> [!TIP]
> [A Tártallózó](http://storageexplorer.com/) kiváló eszköz a több tárfiókot kezeléséhez.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők Logic Apps, megismerkedhet a [API-k lista](apis-list.md).

