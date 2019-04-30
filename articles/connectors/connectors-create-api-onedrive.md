---
title: Onedrive-hoz – Azure Logic Apps |} A Microsoft Docs
description: Töltheti fel és kezelheti a fájlok a onedrive vállalati verzió REST API-k és az Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106227"
---
# <a name="get-started-with-the-onedrive-connector"></a>A onedrive vállalati verzió-összekötő használatának első lépései
Kezelheti a fájlokat, beleértve a feltöltés a onedrive-hoz, beolvasása, törölje a fájlokat, és több. 

A onedrive vállalati verziója akkor: 

* A munkafolyamat létrehozása-fájlok tárolása a onedrive-on, vagy frissítse a meglévő fájlokat a onedrive-on. 
* Eseményindítók használatával indítsa el a munkafolyamatot, amikor egy fájl létrehozásakor vagy frissítésekor a onedrive-on belül.
* Hozzon létre egy fájlt, törölhet egy fájlt, és további műveletek használata. Például egy új Office 365 e-mailek érkezésekor (eseményindító) mellékletet hozzon létre egy új fájlt a onedrive-on (művelet).

Ez a cikk bemutatja, hogyan a onedrive vállalati verzió-összekötő használata a Logic Apps-alkalmazás, és felsorolja a triggereket és műveleteket.

Logic Apps szolgáltatással kapcsolatos további tudnivalókért lásd: [logic Apps](../logic-apps/logic-apps-overview.md) és [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Onedrive-hoz
A logikai alkalmazás bármely szolgáltatáshoz férjenek hozzá, akkor először hozzon létre egy *kapcsolat* a szolgáltatáshoz. Kapcsolatot biztosít a Logic Apps-alkalmazás és a egy másik szolgáltatás közötti kapcsolat. Ha például szeretne csatlakozni a onedrive vállalati verzió, először egy onedrive vállalati verzió *kapcsolat*. A kapcsolat létrehozásához írja be a szokásos módon szeretne csatlakozni a szolgáltatáshoz való hozzáféréshez használt hitelesítő adatokat. Így a OneDrive, adja meg a hitelesítő adatokat hozhat létre a kapcsolatot a OneDrive-fiókjába.

### <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Triggert
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. Eseményindítók "elindítja a lekérdezést" a szolgáltatás az intervallum és a kívánt gyakorisággal. [További tudnivalók a triggerek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Írja be a logikai alkalmazás "onedrive vállalati verzió" listáját az eseményindító:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Válassza ki **fájl módosításakor**. Ha a kapcsolat már létezik, majd válasszon ki egy mappát a választó megjelenítése gombra.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Ha a rendszer kéri, jelentkezzen be, akkor írja be a bejelentkezést a kapcsolat létrehozásához szükséges adatok. [Hozza létre a kapcsolatot](connectors-create-api-onedrive.md#create-the-connection) ebben a cikkben található lépéseket tartalmazza. 
   
   > [!NOTE]
   > Ebben a példában a logikai alkalmazás fut, amikor egy fájl a mappában, úgy dönt, frissül. Ez az eseményindító eredményeinek megtekintéséhez adjon hozzá egy újabb műveletet, amely e-mailt küld Önnek. Például adja hozzá az Office 365 Outlook *e-mail küldése* műveletet, amely e-mailt küld, ha egy fájl frissül. 

3. Válassza ki a **szerkesztése** gombra, és állítsa be a **gyakorisága** és **időköz** értékeket. Például, ha azt szeretné, hogy az eseményindító 15 percenként lekérdezéséhez, majd állítsa be a **gyakorisága** való **perc**, és állítsa be a **időköz** való **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Mentés** a módosításokat (bal felső sarokban az eszköztár). A logikai alkalmazás a rendszer menti, és előfordulhat, hogy automatikusan engedélyezve kell lennie.

## <a name="use-an-action"></a>Egy művelet használata
Művelet definiálva, a logikai alkalmazás a munkafolyamat által végzett művelet. [További információért azokról a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Kattintson a plusz jelre. Sokféle lehetőség jelenik meg: **Művelet hozzáadása**, **feltétel hozzáadása**, vagy az egyik a **további** beállítások.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Válasszon **művelet hozzáadása**.
3. A szövegmezőbe írja be "onedrive vállalati verzió" az elérhető műveletek listáját.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. Ebben a példában válassza **OneDrive - fájl létrehozása**. Ha a kapcsolat már létezik, majd válassza ki a **mappa elérési útja** helyezni a fájlt, írja be a **Fájlnév**, és válassza a **fájl tartalma** szeretné:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Ha a rendszer kéri a kapcsolati információkat, adja meg a részleteket a kapcsolat létrehozásához. [Hozza létre a kapcsolatot](connectors-create-api-onedrive.md#create-the-connection) ebben a cikkben azt ismerteti, ezeket a tulajdonságokat. 
   
   > [!NOTE]
   > Ebben a példában létrehozunk egy új fájlt a OneDrive-mappában. Egy másik trigger kimenete használhatja a OneDrive-fájl létrehozásához. Például adja hozzá az Office 365 Outlook *új e-mail érkezésekor* eseményindító. Majd adja hozzá a onedrive vállalati verzió *fájl létrehozása* műveletet, amely a mellékleteket és a Content-Type mezőket egy ForEach hozhat létre az új fájl a onedrive-on belül. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Mentés** a módosításokat (bal felső sarokban az eszköztár). A logikai alkalmazás a rendszer menti, és előfordulhat, hogy automatikusan engedélyezve kell lennie.


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).