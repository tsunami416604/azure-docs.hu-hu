---
title: Fájlok elérése és kezelése a Microsoft OneDrive
description: Fájlok feltöltése és kezelése a OneDrive-ben automatizált munkafolyamatok létrehozásával Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75378432"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Fájlok elérése és kezelése a OneDrive-összekötőben Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [OneDrive-összekötő](/connectors/onedriveconnector/)használatával automatizált feladatokat és munkafolyamatokat hozhat létre a fájlok kezeléséhez, beleértve a feltöltési, a lekérési, a fájlok törlését és egyebeket. A OneDrive segítségével a következő feladatokat hajthatja végre:

* Hozza létre a munkafolyamatot úgy, hogy fájlokat tárol a OneDrive-ben, vagy frissíti a meglévő fájlokat a OneDrive-ben. 
* Az eseményindítók használatával elindíthatja a munkafolyamatot egy fájl létrehozásakor vagy frissítésekor a OneDrive belül.
* Egy fájl létrehozásához, egy fájl törléséhez és egyéb műveletekhez használjon műveleteket. Ha például egy új Office 365 e-mail érkezik egy melléklettel (triggerrel), hozzon létre egy új fájlt a OneDrive (egy művelet).

Ez a cikk bemutatja, hogyan használhatja a OneDrive-összekötőt egy logikai alkalmazásban, és felsorolja az eseményindítókat és műveleteket.

Ha többet szeretne megtudni a Logic Appsről, tekintse meg a [Mi a Logic apps](../logic-apps/logic-apps-overview.md) és [a logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakört.

## <a name="connect-to-onedrive"></a>Csatlakozás a OneDrive-hoz

Ahhoz, hogy a logikai alkalmazás hozzáférhessen bármely szolgáltatáshoz, először létre kell hoznia egy *kapcsolatot* a szolgáltatással. A kapcsolat kapcsolatot biztosít egy logikai alkalmazás és egy másik szolgáltatás között. Ha például csatlakozni szeretne a OneDrive-hoz, először egy OneDrive- *kapcsolatra*van szüksége. Kapcsolat létrehozásához adja meg azokat a hitelesítő adatokat, amelyeket általában a kapcsolódáshoz használni kívánt szolgáltatás eléréséhez használ. Így a OneDrive esetében adja meg a OneDrive-fiók hitelesítő adatait a kapcsolódás létrehozásához.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Trigger használata

Az eseményindító egy olyan esemény, amely a logikai alkalmazásban definiált munkafolyamat elindítására szolgál. A "lekérdezés" a szolgáltatást a kívánt időközönként és gyakorisággal indítja el. [További információ az eseményindítókkal kapcsolatban](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. A Logic app Designerben írja be a következőt az `onedrive` Eseményindítók listájának lekéréséhez:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Válassza ki **a fájl módosításának időpontját**. Ha a kapcsolat már létezik, kattintson a választó megjelenítése gombra a mappa kiválasztásához.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Ha a rendszer kéri, hogy jelentkezzen be, adja meg a bejelentkezés részleteit a kapcsolat létrehozásához. [Hozza létre a](connectors-create-api-onedrive.md#create-the-connection) jelen cikkben található, a lépéseket felsoroló témakört.

   Ebben a példában a logikai alkalmazás akkor fut le, amikor a kiválasztott mappában található fájl frissül. Az trigger eredményeinek megtekintéséhez adjon hozzá egy másik műveletet, amely e-mailt küld Önnek. Például adja hozzá az Office 365 Outlook *E-mail küldése* műveletet, amely e-mailt küld a fájl frissítésekor.

3. Kattintson a **Szerkesztés** gombra, és állítsa be a **gyakoriság** és az **intervallum** értékét. Ha például azt szeretné, hogy a trigger 15 percenként lekérdezzen, állítsa a **gyakoriságot** **percre**, és állítsa be az **intervallumot** **15**-re. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Mentse** a módosításokat (az eszköztár bal felső sarkában). A logikai alkalmazás mentése megtörtént, és lehet, hogy automatikusan engedélyezve van.

## <a name="use-an-action"></a>Művelet használata

A művelet egy logikai alkalmazásban definiált munkafolyamat által végrehajtott művelet. [További információ a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Válassza ki a plusz jelet. Több lehetőség is megjelenik: **művelet hozzáadása**, **feltétel hozzáadása**vagy a **további** lehetőségek egyike.

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Válassza **a művelet hozzáadása**lehetőséget.

3. A keresőmezőbe írja be az `onedrive` összes elérhető művelet listáját.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. A példánkban válassza a **OneDrive – fájl létrehozása**lehetőséget. Ha már létezik egy kapcsolódás, válassza ki a **mappa elérési útját** , adja meg a fájl **nevét**, és válassza ki a kívánt **fájlt** :  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Ha a rendszer a kapcsolódási adatok megadását kéri, adja meg a kapcsolat létrehozásához szükséges adatokat a jelen témakörben [leírtak szerint](#create-the-connection) .

   Ebben a példában egy új fájlt hoz létre egy OneDrive mappában. A OneDrive fájl létrehozásához egy másik trigger kimenetét is használhatja. Adja meg például az Office 365 Outlookot, *Amikor új e-mail érkezik* triggerre. Ezután adja hozzá a OneDrive *create file (fájl létrehozása* ) műveletet, amely a foreach található mellékletek és tartalomtípus mezőket használja az új fájl létrehozásához a OneDrive-ben.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Mentse** a módosításokat (az eszköztár bal felső sarkában). A logikai alkalmazás mentése megtörtént, és lehet, hogy automatikusan engedélyezve van.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Megtekintheti a hencegés során definiált összes eseményindítót és műveletet, valamint az [összekötő részleteiben](/connectors/onedriveconnector/)megjelenő korlátokat is.

## <a name="next-steps"></a>További lépések

* [Az Azure Logic Apps összekötői](apis-list.md)