---
title: Kapcsolódás az Office 365 Outlookhoz
description: Kezelheti az e-maileket, a névjegyeket és a naptárakat az Office 365 REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789610"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Ismerkedés az Office 365 Outlook connectorral
Az Office 365 Outlook Connector lehetővé teszi az Office 365-ban való interakciót az Outlookkal. Ezzel az összekötővel névjegyeket és naptári elemeket hozhat létre, szerkeszthet és frissíthet, valamint az e-mailek fogadására, küldésére és megválaszolására is választ kaphat.

Az Office 365 Outlookban a következőket végezheti el:

* Hozza létre a munkafolyamatot az Office 365 e-mail és naptár funkcióinak használatával. 
* Az eseményindítók használatával elindíthatja a munkafolyamatot, ha új e-mail-cím van, amikor a naptár elem frissül, és így tovább.
* E-mailek küldéséhez, új naptári esemény létrehozásához és egyéb műveletekhez használhatja a műveleteket. Ha például egy új objektum van a Salesforce (trigger), küldjön egy e-mailt az Office 365 Outlookba (egy művelet). 

Ez a cikk bemutatja, hogyan használható az Office 365 Outlook Connector egy logikai alkalmazásban, valamint az eseményindítók és műveletek felsorolása is.

> [!NOTE]
> A cikk jelen verziója a Logic Apps általánosan elérhető (GA) verzióra vonatkozik.
> 
> 

Ha többet szeretne megtudni a Logic Appsről, tekintse meg a [Mi a Logic apps](../logic-apps/logic-apps-overview.md) és [a logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakört.

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz
Ahhoz, hogy a logikai alkalmazás hozzáférhessen bármely szolgáltatáshoz, először létre kell hoznia egy *kapcsolatot* a szolgáltatással. A kapcsolat kapcsolatot biztosít egy logikai alkalmazás és egy másik szolgáltatás között. Például az Office 365 Outlookhoz való kapcsolódáshoz először Office 365- *kapcsolatra*van szükség. Kapcsolat létrehozásához adja meg azokat a hitelesítő adatokat, amelyeket általában a kapcsolódáshoz használni kívánt szolgáltatás eléréséhez használ. Tehát az Office 365 Outlook esetében adja meg az Office 365-fiók hitelesítő adatait a kapcsolódás létrehozásához.

## <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Trigger használata
Az eseményindító egy olyan esemény, amely a logikai alkalmazásban definiált munkafolyamat elindítására szolgál. A "lekérdezés" a szolgáltatást a kívánt időközönként és gyakorisággal indítja el. [További információ az eseményindítókkal kapcsolatban](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Az eseményindítók listájának lekéréséhez írja be az "Office 365" kifejezést a logikai alkalmazásba:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Válassza **az Office 365 Outlook lehetőséget, ha egy közelgő esemény hamarosan elindul**. Ha már létezik egy kapcsolatok, válasszon ki egy naptárat a legördülő listából.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Ha a rendszer kéri, hogy jelentkezzen be, adja meg a bejelentkezés részleteit a kapcsolat létrehozásához. A jelen témakörben található [kapcsolatok létrehozása](connectors-create-api-office365-outlook.md#create-the-connection) a lépéseket tartalmazza. 
   
   > [!NOTE]
   > Ebben a példában a logikai alkalmazás egy naptári esemény frissítésekor fut. Az trigger eredményeinek megtekintéséhez adjon hozzá egy másik műveletet, amely szöveges üzenetet küld. Adja meg például a Twilio küldése műveletet, amely a naptári esemény 15 percen belüli indításakor megjelenő szöveget *jeleníti* meg. 
   > 
   > 
3. Kattintson a **Szerkesztés** gombra, és állítsa be a **gyakoriság** és az **intervallum** értékét. Ha például azt szeretné, hogy a trigger 15 percenként lekérdezzen, állítsa a **gyakoriságot** **percre**, és állítsa be az **intervallumot** **15**-re. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Mentse** a módosításokat (az eszköztár bal felső sarkában). A logikai alkalmazás mentése megtörtént, és lehet, hogy automatikusan engedélyezve van.

## <a name="use-an-action"></a>Művelet használata
A művelet egy logikai alkalmazásban definiált munkafolyamat által végrehajtott művelet. [További információ a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Válassza ki a plusz jelet. Több lehetőség is megjelenik: **művelet hozzáadása**, **feltétel hozzáadása**vagy a **további** lehetőségek egyike.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Válassza **a művelet hozzáadása**lehetőséget.
3. A szövegmezőbe írja be az "Office 365" kifejezést az összes elérhető művelet listájának lekéréséhez.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. A példánkban válassza az **Office 365 Outlook – kapcsolat létrehozása**lehetőséget. Ha már létezik egy kapcsolatok, válassza ki a **mappa azonosítóját**, a **nevet**és a többi tulajdonságot:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Ha a rendszer a kapcsolati adatok megadását kéri, adja meg a kapcsolat létrehozásához szükséges adatokat. A jelen témakörben található [kapcsolatok létrehozása](connectors-create-api-office365-outlook.md#create-the-connection) ezeket a tulajdonságokat ismerteti. 
   
   > [!NOTE]
   > Ebben a példában egy új névjegyet hozunk létre az Office 365 Outlookban. Egy másik trigger kimenetét is használhatja a partner létrehozásához. Adja meg például, hogy a SalesForce *egy objektum létrehozásakor* trigger. Ezután adja hozzá az Office 365 Outlook *Create contact* műveletét, amely az Salesforce mezőket használja az Office 365-ben létrehozott új partner létrehozásához. 
   > 
   > 
5. **Mentse** a módosításokat (az eszköztár bal felső sarkában). A logikai alkalmazás mentése megtörtént, és lehet, hogy automatikusan engedélyezve van.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Megtekintheti a hencegés során definiált összes eseményindítót és műveletet, valamint az [összekötő részleteiben](/connectors/office365connector/)megjelenő korlátokat is. 

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése