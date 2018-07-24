---
title: Kapcsolódás az Office 365 Outlook – Azure Logic Apps |} A Microsoft Docs
description: E-mailek, partneradatok és az Office 365 REST API-k és az Azure Logic Apps naptárak kezelése
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
ms.openlocfilehash: 0355f121a09e1ba89f98a8af5037eb1371db2242
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215638"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Az Office 365 Outlook-összekötő használatának első lépései
Az Office 365 Outlook-összekötő lehetővé teszi, hogy az Office 365 Outlook-szal. Ez az összekötő használatával létrehozása, szerkesztése, és a partnerek és a naptári elemek, frissítése és is, küldése és válasz e-mail-cím.

Az Office 365 Outlookban hogy:

* Létrehozhatja a munkafolyamatot, használja az e-mailek és a naptári funkciókat az Office 365-ben. 
* Indítsa el a munkafolyamatot, ha van egy új e-mailt, amikor frissül egy naptárelemet és más triggerek használatát.
* E-mailt küldeni, hozzon létre egy új naptáresemény és egyéb műveletek használata. Például ha egy új objektumot a Salesforce-ban (egy eseményindító), e-mail küldése az Office 365 Outlookba (művelet). 

Ez a cikk bemutatja, hogyan használható az Office 365 Outlook-összekötőt, a logikai alkalmazás, és felsorolja a triggereket és műveleteket.

> [!NOTE]
> A cikk e verziója a Logic Apps általánosan (elérhetővé tétel GA) vonatkozik.
> 
> 

Logic Apps szolgáltatással kapcsolatos további tudnivalókért lásd: [logic Apps](../logic-apps/logic-apps-overview.md) és [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz
A logikai alkalmazás bármely szolgáltatáshoz férjenek hozzá, akkor először hozzon létre egy *kapcsolat* a szolgáltatáshoz. Kapcsolatot biztosít a Logic Apps-alkalmazás és a egy másik szolgáltatás közötti kapcsolat. Ha például szeretne csatlakozni az Office 365 Outlook, először egy Office 365 *kapcsolat*. A kapcsolat létrehozásához írja be a szokásos módon szeretne csatlakozni a szolgáltatáshoz való hozzáféréshez használt hitelesítő adatokat. Ezért az Office 365 Outlookban, adja meg a hitelesítő adatokat az Office 365-fiók, a kapcsolat létrehozásához.

## <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Triggert
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. Eseményindítók "elindítja a lekérdezést" a szolgáltatás az intervallum és a kívánt gyakorisággal. [További tudnivalók a triggerek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Írja be a logikai alkalmazás "office 365", az eseményindítók listájának lekérése:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Válassza ki **Office 365 Outlook – Ha egy közelgő esemény hamarosan kezdődik**. Ha a kapcsolat már létezik, majd naptár kijelölése a legördülő listából.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Ha a rendszer kéri, jelentkezzen be, akkor írja be a bejelentkezést a kapcsolat létrehozásához szükséges adatok. [Hozza létre a kapcsolatot](connectors-create-api-office365-outlook.md#create-the-connection) Ez a témakör felsorolja azokat a lépéseket. 
   
   > [!NOTE]
   > Ebben a példában a logikai alkalmazás fut, amikor frissül egy naptáresemény. Ez az eseményindító eredményeinek megtekintéséhez adjon hozzá egy másik művelet, amely egy szöveges üzenetet küld Önnek. Például adja hozzá a Twilio *üzenetküldés* művelet a szövegek, ha a naptáresemény kezdetekor 15 percet vesz igénybe. 
   > 
   > 
3. Válassza ki a **szerkesztése** gombra, és állítsa be a **gyakorisága** és **időköz** értékeket. Például, ha azt szeretné, hogy az eseményindító 15 percenként lekérdezéséhez, majd állítsa be a **gyakorisága** való **perc**, és állítsa be a **időköz** való **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Mentés** a módosításokat (bal felső sarokban az eszköztár). A logikai alkalmazás a rendszer menti, és előfordulhat, hogy automatikusan engedélyezve kell lennie.

## <a name="use-an-action"></a>Egy művelet használata
Művelet definiálva, a logikai alkalmazás a munkafolyamat által végzett művelet. [További információért azokról a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Kattintson a plusz jelre. Láthatja, hogy sokféle lehetőség: **művelet hozzáadása**, **feltétel hozzáadása**, vagy az egyik a **további** beállítások.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Válasszon **művelet hozzáadása**.
3. A szövegmezőbe írja be a "office 365" az elérhető műveletek listáját.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Ebben a példában válassza **Office 365 Outlook - névjegy létrehozása**. Ha a kapcsolat már létezik, majd válassza a **Mappaazonosító**, **Utónév**, és egyéb tulajdonságok:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Ha a rendszer kéri a kapcsolati információkat, adja meg a részleteket a kapcsolat létrehozásához. [Hozza létre a kapcsolatot](connectors-create-api-office365-outlook.md#create-the-connection) Ez a témakör azt ismerteti, ezeket a tulajdonságokat. 
   
   > [!NOTE]
   > Ebben a példában egy új névjegyet az Office 365 Outlook hozunk létre. Egy másik trigger kimenete használatával hozzon létre az ügyfélhez. Hozzáadhat például a SalesForce *objektum létrehozásakor* eseményindító. Majd adja hozzá az Office 365 Outlook *névjegy létrehozása* műveletet, amely a SalesForce-mezők használatával hozzon létre az új névjegyet az Office 365-ben. 
   > 
   > 
5. **Mentés** a módosításokat (bal felső sarokban az eszköztár). A logikai alkalmazás a rendszer menti, és előfordulhat, hogy automatikusan engedélyezve kell lennie.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/office365connector/). 

## <a name="next-steps"></a>Következő lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ismerje meg az egyéb elérhető összekötők a Logic Apps, a [API-k listája](apis-list.md).

