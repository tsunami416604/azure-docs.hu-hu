---
title: "Adja hozzá a OneDrive-összekötőt a Logic Apps |} Microsoft Docs"
description: "A OneDrive-összekötő REST API-paraméterekkel rendelkező áttekintése"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 63bd33bf4e09b98aa53dcfec9fcc4a0109204952
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-onedrive-connector"></a>A OneDrive-összekötő az első lépései
Csatlakozni a onedrive-nak a fájlok kezelését, beleértve a feltöltés, get, törölje a fájlokat, és több. 

A onedrive-ról hogy: 

* A munkafolyamat létrehozása tárolja a fájlokat a onedrive-on, vagy frissítse a meglévő fájlokat a onedrive-on. 
* Eseményindítók segítségével indítsa el a munkafolyamat, amikor egy fájl létrehozásakor vagy frissítésekor a onedrive-on belül.
* Hozzon létre egy fájlt, törölje a fájlt, és egyéb műveleteket használni. Például egy új Office 365 e-mailt fogadásakor mellékletet (trigger) hozzon létre egy új fájlt a onedrive-on (művelet).

Ez a témakör bemutatja, hogyan használható a onedrive vállalati verzió összekötő logikai alkalmazás, és eseményindítók és műveletek is tartalmazza.

A Logic Apps kapcsolatos további információkért lásd: [Mik azok a logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) és [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-onedrive"></a>Kapcsolódás a onedrive vállalati verzió
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először létre kell hoznia egy *kapcsolat* a szolgáltatáshoz. Egy kapcsolat egy logikai alkalmazást és egy másik szolgáltatás közötti kapcsolatot biztosít. Például ha csatlakozni szeretne a OneDrive, először a onedrive vállalati verzió *kapcsolat*. VPN-kapcsolat létrehozásához adja meg a hitelesítő adatok általában segítségével éri el a szolgáltatást, amelyhez csatlakozni szeretne. Igen a onedrive-on, megadja a hitelesítő adatokat a OneDrive-fiókja, a VPN-kapcsolat létrehozásához.

### <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Eseményindítók
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. Eseményindítók "lekérdezésére" a szolgáltatás egy intervallum és a kívánt gyakoriságát. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Írja be a logikai alkalmazást listáját, illetve a "onedrive":  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Válassza ki **fájl módosításának**. Ha már létezik egy kapcsolat, majd válassza ki az objektumválasztó megjelenítése gomb egy mappa kiválasztására.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Ha a bejelentkezéshez kéri, akkor írja be a bejelentkezési a kapcsolat létrehozásához szükséges adatok. [A kapcsolat létrehozása](connectors-create-api-onedrive.md#create-the-connection) a témakör felsorolja azokat a lépéseket. 
   
   > [!NOTE]
   > Ebben a példában a logikai alkalmazás fut, amikor egy fájl a mappában, ha úgy dönt, hogy a rendszer frissíti. Ehhez az eseményindítóhoz eredményeinek megtekintéséhez adja hozzá az e-mailt küld egy másik művelet. Adja hozzá például az Office 365 Outlook *egy e-mailek küldése* , hogy e-mailt küld, amikor egy fájl frissítése műveletet. 

3. Válassza ki a **szerkesztése** gombra, majd a **gyakoriság** és **időköz** értékeket. Például, ha azt szeretné, hogy az eseményindító, és kérdezze le a 15 percenként, majd állítsa be a **gyakoriság** való **perc**, és állítsa be a **időköz** való **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Mentés** a módosításokat (bal felső sarkában az eszköztár). A Logic Apps alkalmazást menti, és lehet, hogy automatikusan engedélyezve.

## <a name="use-an-action"></a>Egy művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Kattintson a plusz ikonra. Több beállítások megtekintéséhez: **művelet hozzáadása**, **feltétel hozzáadása**, vagy az egyik a **további** beállítások.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Válasszon **művelet hozzáadása**.
3. A szövegmezőben írja be a rendelkezésre álló műveletek listájának "onedrive".
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. Válassza ki a fenti példában **személyes OneDrive - fájl létrehozása**. Ha már létezik egy kapcsolat, majd válassza ki a **mappa elérési útja** , amelyre a fájlt, írja be a **Fájlnév**, és válassza ki a **fájl tartalma** kívánt:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Ha a kapcsolati adatokat kéri, adja meg a részleteket a VPN-kapcsolat létrehozásához. [A kapcsolat létrehozása](connectors-create-api-onedrive.md#create-the-connection) a témakörben ismertetett ezeket a tulajdonságokat. 
   
   > [!NOTE]
   > Ebben a példában azt hozzon létre egy új fájlt a onedrive vállalati verzió mappában. Egy másik eseményindító kimenetét a OneDrive-fájl létrehozására használhatja. Adja hozzá például az Office 365 Outlook *egy új e-mailt fogadásakor* eseményindító. Adja meg a onedrive vállalati verzió *létrehozás fájl* művelet, a mellékletek és a Content-Type használó mezők egy ForEach létrehozni az új fájlt a onedrive-on belül. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Mentés** a módosításokat (bal felső sarkában az eszköztár). A Logic Apps alkalmazást menti, és lehet, hogy automatikusan engedélyezve.


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).