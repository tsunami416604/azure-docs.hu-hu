---
title: Kapcsolódás az Office 365 Outlookhoz
description: A levelezést, névjegyeket és naptárakat kezelő feladatokat és munkafolyamatokat automatizálhatja az Office 365 Outlookban Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 07/27/2020
tags: connectors
ms.openlocfilehash: aa5be3d58ee1a0a40d0b817e1f5999ccd4bc423d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319235"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>E-mailek, partneradatok és naptárak kezelése az Office 365 Outlookban az Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [Office 365 Outlook Connector](/connectors/office365connector/)használatával a logikai alkalmazások létrehozásával automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek az Office 365-fiókját kezelik. Automatizálhatja például ezeket a feladatokat:

* Az e-mailek lekérése, küldése és megválaszolása. 
* Értekezletek ütemezhetnek a naptárban.
* Névjegyek hozzáadása és szerkesztése. 

Bármely eseményindítóval elindíthatja a munkafolyamatot, például amikor új e-mail érkezik, amikor egy naptári elem frissül, vagy ha egy esemény egy különbségi szolgáltatásban (például Salesforce) történik. Használhatja az eseményindító eseményre válaszoló műveleteket, például e-mailek küldését vagy új naptári esemény létrehozását. 

> [!NOTE]
> Egy vagy fiókhoz tartozó feladatok automatizálásához @outlook.com @hotmail.com használja az [Outlook.com-összekötőt](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

* [Office 365-fiók](https://www.office.com/)

* Az a logikai alkalmazás, amelyhez el szeretné érni az Office 365 Outlook-fiókját. Ha a munkafolyamatot Office 365 Outlook-triggerrel szeretné elindítani, [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség. Ha Office 365 Outlook-műveletet szeretne hozzáadni a munkafolyamathoz, a logikai alkalmazásnak már rendelkeznie kell egy triggerrel.

## <a name="add-a-trigger"></a>Eseményindító hozzáadása

Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan esemény, amely elindítja a munkafolyamatot a logikai alkalmazásban. Ez a példa a logikai alkalmazás egy "lekérdezés" eseményindítót használ, amely a megadott intervallum és gyakoriság alapján ellenőrzi az e-mail-fiókjában lévő frissített naptári eseményeket.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A keresőmezőbe írja be `office 365 outlook` szűrőként a kifejezést. Ez a példa **egy közelgő esemény hamarosan történő megkezdését**választja ki.
   
   ![Válassza ki az triggert a logikai alkalmazás indításához](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Ha a rendszer kéri, hogy jelentkezzen be, adja meg az Office 365 hitelesítő adatait, hogy a logikai alkalmazás csatlakozhasson a fiókjához. Ellenkező esetben, ha a hálózat már létezik, adja meg az aktiváló tulajdonságok információit.

   > [!NOTE]
   > A szolgáltatás nem jár le a visszavonás előtt, még akkor is, ha módosítja a bejelentkezési hitelesítő adatait. További információ: [konfigurálható jogkivonatok élettartama Azure Active Directoryban](../active-directory/active-directory-configurable-token-lifetimes.md).

   Ez a példa kiválasztja azt a naptárat, amelyet a trigger ellenőriz, például:

   ![Az trigger tulajdonságainak konfigurálása](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. A triggerben állítsa be a **gyakoriság** és az **intervallum** értékét. Más elérhető trigger-tulajdonságok (például az **időzóna**) hozzáadásához válassza ki ezeket a tulajdonságokat az **új paraméter hozzáadása** listából.

   Ha például azt szeretné, hogy a trigger 15 percenként ellenőrizze a naptárat, állítsa a **gyakoriságot** **percre**, és állítsa be az **intervallumot** a következőre: `15` . 

   ![Az trigger gyakoriságának és intervallumának megadása](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. A tervező eszköztárán válassza a **Mentés**lehetőséget.

Most adjon hozzá egy műveletet, amely az eseményindító elindítása után fut. Hozzáadhatja például a Twilio- **üzenetküldés** műveletet, amely szöveget küld, ha egy naptári esemény 15 percen belül elindul.

## <a name="add-an-action"></a>Művelet hozzáadása

A [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan művelet, amelyet a logikai alkalmazás munkafolyamata futtat. Ez a példa logikai alkalmazás létrehoz egy új névjegyet az Office 365 Outlookban. A névjegy létrehozásához használhatja a másik triggerből vagy műveletből származó kimenetet is. Tegyük fel például, hogy a logikai alkalmazás a Dynamics 365 triggert használja a **rekordok létrehozásakor**. Hozzáadhatja az Office 365 Outlook **Create contact** műveletet, és a Salesforce trigger kimeneteit használva létrehozhatja az új kapcsolattartót.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha egy műveletet a munkafolyamat utolsó lépéseként szeretne hozzáadni, válassza az **új lépés**lehetőséget. 

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be `office 365 outlook` szűrőként a kifejezést. Ez a példa kiválasztja a **kapcsolat létrehozása**elemet.

   ![Válassza ki a logikai alkalmazásban futtatandó műveletet](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Ha a rendszer kéri, hogy jelentkezzen be, adja meg az Office 365 hitelesítő adatait, hogy a logikai alkalmazás csatlakozhasson a fiókjához. Ellenkező esetben, ha a kapcsolat már létezik, adja meg a művelet tulajdonságaira vonatkozó információkat.

   > [!NOTE]
   > A szolgáltatás nem jár le a visszavonás előtt, még akkor is, ha módosítja a bejelentkezési hitelesítő adatait. További információ: [konfigurálható jogkivonatok élettartama Azure Active Directoryban](../active-directory/active-directory-configurable-token-lifetimes.md).

   Ez a példa kiválasztja azt a Névjegytár mappát, ahol a művelet létrehozza az új kapcsolattartót, például:

   ![A művelet tulajdonságainak konfigurálása](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Ha más elérhető művelet-tulajdonságokat szeretne hozzáadni, válassza ki ezeket a tulajdonságokat az **új paraméter hozzáadása** listáról.

1. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő hencegő fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/office365/). 

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
