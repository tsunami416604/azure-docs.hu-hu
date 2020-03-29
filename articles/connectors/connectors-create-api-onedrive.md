---
title: Fájlok elérése és kezelése a Microsoft OneDrive-ban
description: Fájlok feltöltése és kezelése a OneDrive-on automatikus munkafolyamatok létrehozásával az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378432"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Fájlok elérése és kezelése a OneDrive-összekötőben az Azure Logic Apps használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a [OneDrive-összekötő](/connectors/onedriveconnector/)használatával automatizált feladatokat és munkafolyamatokat hozhat létre a fájlok kezeléséhez, beleértve a feltöltést, a beget, a fájlok törlését és egyebeket. A OneDrive segítségével az alábbi feladatokat hajthathatja végre:

* Munkafolyamatot hozhat létre a OneDrive-on tárolt fájlokkal, vagy frissítheti a meglévő fájlokat a OneDrive-ban. 
* Az eseményindítók segítségével elindíthatja a munkafolyamatot, amikor egy fájlt hoz létre vagy frissít a OneDrive-on belül.
* Műveletek használatával hozhat létre fájlt, törölhet egy fájlt stb. Ha például egy új Office 365-ös e-mail érkezik egy melléklettel (eseményindítóval), hozzon létre egy új fájlt a OneDrive-on (művelet).

Ez a cikk bemutatja, hogyan használhatja a OneDrive-összekötőt egy logikai alkalmazásban, és felsorolja az eseményindítókat és műveleteket is.

Ha többet szeretne megtudni a Logic Apps alkalmazásokkal kapcsolatban, olvassa el a [Mik a logikai alkalmazások,](../logic-apps/logic-apps-overview.md) és [hozzon létre egy logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="connect-to-onedrive"></a>Csatlakozás a OneDrive-hoz

Mielőtt a logikai alkalmazás bármely szolgáltatáshoz hozzáférhetne, először hozzon létre *kapcsolatot* a szolgáltatással. A kapcsolat kapcsolatot biztosít egy logikai alkalmazás és egy másik szolgáltatás között. Ha például a OneDrive-hoz szeretne csatlakozni, először *OneDrive-kapcsolatra*van szüksége. Kapcsolat létrehozásához adja meg azokat a hitelesítő adatokat, amelyeket általában a csatlakozni kívánt szolgáltatás eléréséhez használ. Így a OneDrive-on adja meg a OneDrive-fiókjához szükséges hitelesítő adatokat a kapcsolat létrehozásához.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Eseményindító használata

Az eseményindító olyan esemény, amely egy logikai alkalmazásban definiált munkafolyamat elindításához használható. Elindítja a szolgáltatás "lekérdezését" a kívánt időközönként és gyakorisággal. [További információ az eseményindítókról.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. A Logic App Designer, írja be `onedrive` az eseményindítók listájának leképezéséhez:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Jelölje be **a Fájl módosításakor**jelölőnégyzetet. Ha már létezik kapcsolat, kattintson a Választó megjelenítése gombra egy mappa kijelöléséhez.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Ha a rendszer kéri a bejelentkezést, adja meg a bejelentkezési adatokat a kapcsolat létrehozásához. [A kapcsolat létrehozása](connectors-create-api-onedrive.md#create-the-connection) ebben a cikkben felsorolja a lépéseket.

   Ebben a példában a logikai alkalmazás akkor fut, amikor a választott mappában lévő fájl frissül. Az eseményindító eredményének megtekintéséhez adjon hozzá egy másik műveletet, amely e-mailt küld Önnek. Például adja hozzá az Office 365 Outlook *Küldése e-mailben* műveletet, amely e-mailben, ha egy fájl frissítése.

3. Válassza a **Szerkesztés** gombot, és állítsa be a **Gyakoriság** és **az Intervallum** értékeket. Ha például azt szeretné, hogy az eseményindító 15 percenként legyen leszavazásban, állítsa a **Gyakoriság** értékét **Perc**értékre, és állítsa az **Intervallum** **értékét 15-re.** 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Mentse** a módosításokat (az eszköztár bal felső sarkában). A logikai alkalmazás mentésre kerül, és előfordulhat, hogy automatikusan engedélyezve van.

## <a name="use-an-action"></a>Művelet használata

A művelet egy logikai alkalmazásban definiált munkafolyamat által végrehajtott művelet. [További információ a műveletekről.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. Válassza ki a pluszjelet. Több választási lehetőség is megjelenik: **Művelet hozzáadása**, **Feltétel hozzáadása**vagy az **Egyebek** lehetőség egyike.

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Válassza **a Művelet hozzáadása**lehetőséget.

3. A keresőmezőbe írja `onedrive` be az összes elérhető művelet listájának legördülő lista beírását.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. A példánkban válassza a **OneDrive – Fájl létrehozása lehetőséget.** Ha már létezik kapcsolat, jelölje ki a **mappaelérési utat** a fájl elhelyezéséhez, írja be a **Fájlnév**lehetőséget, és válassza ki a kívánt **fájltartalmat:**  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Ha a rendszer kéri a kapcsolatadatait, adja meg a kapcsolatot a jelen témakörben [leírtak szerint.](#create-the-connection)

   Ebben a példában új fájlt hoz létre egy OneDrive-mappában. A OneDrive-fájl létrehozásához egy másik eseményindító kimenetét használhatja. Például adja hozzá az Office 365 *Outlookot, amikor új e-mail érkezik* az eseményindítóhoz. Ezután adja hozzá a OneDrive *Create file* műveletet, amely a ForEach mellékletei és tartalomtípus mezőit használja az új fájl onedrive-on való létrehozásához.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Mentse** a módosításokat (az eszköztár bal felső sarkában). A logikai alkalmazás mentésre kerül, és előfordulhat, hogy automatikusan engedélyezve van.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Tekintse meg a swaggerben definiált eseményindítókat és műveleteket, és tekintse meg a korlátokat az [összekötő részleteiben.](/connectors/onedriveconnector/)

## <a name="next-steps"></a>További lépések

* [Az Azure Logic Apps összekötői](apis-list.md)