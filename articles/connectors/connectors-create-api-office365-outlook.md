---
title: "Adja hozzá az Office 365 Outlook-összekötőt a Logic Apps |} Microsoft Docs"
description: "Hozzon létre a logic apps Office 365-összekötő és az Office 365 közötti interakció. Például: létrehozása, szerkesztése és a partnerek és a naptári elemek frissítése."
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: ea0b2f657f808bb6ba4bf427497955b9e520c669
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Az Office 365 Outlook-összekötő az első lépései
Az Office 365 Outlook-összekötő lehetővé teszi, hogy az Office 365 Outlook való együttműködéshez. Ez az összekötő segítségével létrehozása, szerkesztése, és frissítse a névjegyeket és a naptári elemek, és is, küldése és e-mail válaszolni.

Az Office 365 Outlook meg:

* Office 365 belül e-mailek és a naptári funkciókat használ, a munkafolyamat létrehozása. 
* Eseményindítók segítségével a munkafolyamat indítható el, amikor egy új e-mailt, a naptárelemek frissítésekor és még sok más.
* Műveletek segítségével egy e-mailt küldeni, hozzon létre egy új naptár esemény, és több. Például ha van egy új objektumot a Salesforce (trigger), küldjön egy e-mailt a az Office 365 Outlook (művelet). 

Ez a cikk bemutatja, hogyan használható az Office 365 Outlook összekötő logikai alkalmazás, és eseményindítók és műveletek is tartalmazza.

> [!NOTE]
> A cikk e verziója a Logic Apps általános elérhetőségével (GA) vonatkozik.
> 
> 

A Logic Apps kapcsolatos további információkért lásd: [Mik azok a logic apps](../logic-apps/logic-apps-overview.md) és [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először létre kell hoznia egy *kapcsolat* a szolgáltatáshoz. Egy kapcsolat egy logikai alkalmazást és egy másik szolgáltatás közötti kapcsolatot biztosít. Például Office 365 Outlook csatlakozhat, először az Office 365 *kapcsolat*. VPN-kapcsolat létrehozásához adja meg a hitelesítő adatok általában segítségével éri el a szolgáltatást, amelyhez csatlakozni szeretne. Ezért az Office 365 Outlook, adja meg a hitelesítő adatokat az Office 365-fiókra, a VPN-kapcsolat létrehozásához.

## <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Eseményindítók
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. Eseményindítók "lekérdezésére" a szolgáltatás egy intervallum és a kívánt gyakoriságát. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. A logikai alkalmazásban írjon be egy "office 365" listájának az eseményindítók:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Válassza ki **Office 365 Outlook - jövőbeli esemény hamarosan indításakor**. Már létezik egy kapcsolat, a naptárban ezután válassza ki a legördülő listából.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Ha a bejelentkezéshez kéri, akkor írja be a bejelentkezési a kapcsolat létrehozásához szükséges adatok. [A kapcsolat létrehozása](connectors-create-api-office365-outlook.md#create-the-connection) a témakör felsorolja azokat a lépéseket. 
   
   > [!NOTE]
   > Ebben a példában a logikai alkalmazás fut, amikor a naptár esemény frissül. Ehhez az eseményindítóhoz eredményeit, vegye fel egy újabb műveletet, amely egy szöveges üzenetet küld Önnek. Adja hozzá például a Twilio *üzenet küldése* művelet adott szövegek, a naptár esemény indításakor 15 percen belül. 
   > 
   > 
3. Válassza ki a **szerkesztése** gombra, majd a **gyakoriság** és **időköz** értékeket. Például, ha azt szeretné, hogy az eseményindító, és kérdezze le a 15 percenként, majd állítsa be a **gyakoriság** való **perc**, és állítsa be a **időköz** való **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Mentés** a módosításokat (bal felső sarkában az eszköztár). A Logic Apps alkalmazást menti, és lehet, hogy automatikusan engedélyezve.

## <a name="use-an-action"></a>Egy művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Kattintson a plusz ikonra. Több beállítások megtekintéséhez: **művelet hozzáadása**, **feltétel hozzáadása**, vagy az egyik a **további** beállítások.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Válasszon **művelet hozzáadása**.
3. A szövegmezőbe írja be a "office 365" az összes elérhető műveletek listájának beolvasása.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Válassza ki a fenti példában **Office 365 Outlook - ügyfél létrehozása**. Ha már létezik egy kapcsolat, majd válassza ki a **mappa azonosítója**, **Utónév**, és egyéb tulajdonságok:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Ha a kapcsolati adatokat kéri, adja meg a részleteket a VPN-kapcsolat létrehozásához. [A kapcsolat létrehozása](connectors-create-api-office365-outlook.md#create-the-connection) a témakörben ismertetett ezeket a tulajdonságokat. 
   
   > [!NOTE]
   > Ebben a példában az Office 365 Outlook új ügyfél tudjuk létrehozni. Egy másik eseményindító kimenete hozhat létre az ügyfélhez. Adja hozzá például a SalesForce *egy objektumának létrejöttekor* eseményindító. Adja meg az Office 365 Outlook *ügyfél létrehozása* művelet, amely a SalesForce mezők használja az Office 365-ben az új új ügyfél létrehozásához. 
   > 
   > 
5. **Mentés** a módosításokat (bal felső sarkában az eszköztár). A Logic Apps alkalmazást menti, és lehet, hogy automatikusan engedélyezve.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/office365connector/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők Logic Apps, megismerkedhet a [API-k lista](apis-list.md).

