---
title: Kapcsolódás az Office 365 Outlookhoz
description: Az Office 365 Outlook ban e-maileket, névjegyeket és naptárakat kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732689"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>E-mailek, partneradatok és naptárak kezelése az Office 365 Outlookban az Azure Logic Apps használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és az Office [365 Outlook-összekötő](/connectors/office365connector/)segítségével automatikus feladatokat és munkafolyamatokat hozhat létre, amelyek logikai alkalmazások létrehozásával kezelik az Office 365-fiókját. Például automatizálja ezeket a feladatokat:

* E-mail beküldése, küldése és megválaszolása. 
* Értekezletek ütemezése a naptárban.
* Névjegyek felvétele és szerkesztése. 

Bármely eseményindító thasználhat a munkafolyamat elindításához, például amikor egy új e-mail érkezik, amikor egy naptárelem frissül, vagy amikor egy esemény történik egy különbségszolgálatban, például a Salesforce. Olyan műveleteket használhat, amelyek válaszolnak az eseményindító eseményre, például e-mailt küldhet, vagy új naptáreseményt hozhat létre. 

> [!NOTE]
> Egy @outlook.com fiók @hotmail.com vagy fiók feladatainak automatizálásához használja a Outlook.com [összekötőt.](../connectors/connectors-create-api-outlook.md)

## <a name="prerequisites"></a>Előfeltételek

* [Office 365-fiók](https://www.office.com/)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni az Office 365 Outlook-fiókjához. Ha a munkafolyamatot Office 365 Outlook-eseményindítóval szeretné elindítani, [üres logikai alkalmazással kell rendelkeznie.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ha office 365 Outlook-műveletet szeretne hozzáadni a munkafolyamathoz, a logikai alkalmazásnak már rendelkeznie kell egy eseményindítóval.

## <a name="add-a-trigger"></a>Eseményindító hozzáadása

Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) olyan esemény, amely elindítja a munkafolyamatot a logikai alkalmazásban. Ez a példa logikai alkalmazás egy "lekérdezési" eseményindítót használ, amely a megadott időköz és gyakoriság alapján ellenőrzi az e-mail fiókban lévő frissített naptáreseményeket.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A keresőmezőbe írja `office 365 outlook` be szűrőként. Ez a példa a **Közelgő esemény hamarosan kezdődő beállítását választja.**
   
   ![A logikai alkalmazás elindításához válassza az eseményindítót](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Ha a rendszer kéri a bejelentkezést, adja meg Office 365-ös hitelesítő adatait, hogy a logikai alkalmazás kapcsolódhasson a fiókjához. Ellenkező esetben, ha a kapcsolat már létezik, adja meg az eseményindító tulajdonságainak adatait.

   Ez a példa kiválasztja az eseményindító által végzett naptárat, például:

   ![Az eseményindító tulajdonságainak konfigurálása](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Az eseményindítóban állítsa be a **Gyakoriság** és **az Intervallum** értékeket. További elérhető eseményindító-tulajdonságok, például **az Időzóna**hozzáadásához jelölje ki ezeket a tulajdonságokat az **Új paraméter hozzáadása** listából.

   Ha például azt szeretné, hogy az eseményindító 15 percenként ellenőrizze a `15`naptárat, állítsa a **Gyakoriság** lehetőséget **Perc**értékre, és állítsa az **Intervallum** értékét . 

   ![Az eseményindító gyakoriságának és időközének beállítása](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. A tervező eszköztárán válassza a **Mentés gombot.**

Most adjon hozzá egy műveletet, amely az eseményindító tüzek után fut. Hozzáadhatja például a Twilio **Üzenet küldése** műveletet, amely 15 perc múlva küld egy szöveget, amikor egy naptári esemény elindul.

## <a name="add-an-action"></a>Művelet hozzáadása

A [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) olyan művelet, amelyet a munkafolyamat futtat a logikai alkalmazásban. Ez a példa logikai alkalmazás új névjegyet hoz létre az Office 365 Outlookban. A kapcsolattartó létrehozásához használhatja egy másik eseményindító vagy művelet kimenetét. Tegyük fel például, hogy a logikai alkalmazás a Dynamics 365 eseményindítót, **amikor egy rekord jön létre.** Hozzáadhatja az Office 365 Outlook **Partner létrehozása** műveletet, és a SalesForce-eseményindító kimenetei segítségével létrehozhatja az új névjegyet.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Ha egy műveletet szeretne a munkafolyamat utolsó lépéseként felvenni, válassza az **Új lépés lehetőséget.** 

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A keresőmezőbe írja `office 365 outlook` be szűrőként. Ez a példa a **Kapcsolattartó létrehozása**lehetőséget választja.

   ![A logikai alkalmazásban futtatni kívánt művelet kiválasztása](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Ha a rendszer kéri a bejelentkezést, adja meg Office 365-ös hitelesítő adatait, hogy a logikai alkalmazás kapcsolódhasson a fiókjához. Ellenkező esetben, ha a kapcsolat már létezik, adja meg a művelet tulajdonságainak adatait.

   Ez a példa azt a névjegymappát jelöli ki, amelyben a művelet létrehozza az új névjegyet, például:

   ![A művelet tulajdonságainak konfigurálása](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   További elérhető művelettulajdonságok hozzáadásához jelölje ki ezeket a tulajdonságokat az **Új paraméter hozzáadása** listából.

1. A tervező eszköztárán válassza a **Mentés gombot.**

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az eseményindítók, műveletek és korlátok technikai részleteit az összekötő Swagger-fájljában leírtak szerint tekintse meg az [összekötő referencialapján.](/connectors/office365connector/) 

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
