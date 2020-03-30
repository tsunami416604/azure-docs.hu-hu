---
title: Csatlakozás Outlook.com
description: Az Azure Logic Apps használatával automatizálhatja az e-maileket, naptárakat és névjegyeket kezelő feladatokat és munkafolyamatokat Outlook.com
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707186"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>E-mailek, naptárak és névjegyek kezelése Outlook.com az Azure Logic Apps használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a [Outlook.com-összekötő](/connectors/outlook/)segítségével automatizált @outlook.com feladatokat és munkafolyamatokat hozhat létre, amelyek logikai alkalmazások létrehozásával kezelik az Ön vagy @hotmail.com a fiók. Például automatizálja ezeket a feladatokat:

* E-mail beküldése, küldése és megválaszolása.
* Értekezletek ütemezése a naptárban.
* Névjegyek felvétele és szerkesztése.

Bármely eseményindító thasználhat a munkafolyamat elindításához, például amikor új e-mail érkezik, amikor egy naptárelem frissül, vagy amikor egy esemény egy különbségszolgálatban történik. Olyan műveleteket használhat, amelyek válaszolnak az eseményindító eseményre, például e-mailt küldhet, vagy új naptáreseményt hozhat létre.

> [!NOTE]
> Microsoft munkahelyi fiók, például @fabrikam.onmicrosoft.comfeladatok automatizálásához használja az Office [365 Outlook-összekötőt.](../connectors/connectors-create-api-office365-outlook.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy [Outlook.com-fiók](https://outlook.live.com/owa/)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A logikai alkalmazás, ahol szeretné elérni a Outlook.com fiókját. Ha a munkafolyamatot Outlook.com eseményindítóval szeretné elindítani, [egy üres logikai alkalmazással kell rendelkeznie.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ha Outlook.com műveletet szeretne hozzáadni a munkafolyamathoz, a logikai alkalmazásnak már rendelkeznie kell egy eseményindítóval.

## <a name="add-a-trigger"></a>Eseményindító hozzáadása

Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) olyan esemény, amely elindítja a munkafolyamatot a logikai alkalmazásban. Ez a példa logikai alkalmazás egy "lekérdezési" eseményindítót használ, amely a megadott időköz és gyakoriság alapján ellenőrzi az e-mail fiókban lévő új e-maileket.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A keresőmezőbe írja be szűrőként az "outlook.com" kifejezést. Ebben a példában válassza a **When a new email arrives**lehetőséget.

1. Ha a rendszer kéri, hogy jelentkezzen be, adja meg a Outlook.com hitelesítő adatait, hogy a logikai alkalmazás csatlakozhat a fiókjához. Ellenkező esetben, ha a kapcsolat már létezik, adja meg az eseményindító tulajdonságainak adatait:

1. Az eseményindítóban állítsa be a **Gyakoriság** és **az Intervallum** értékeket.

   Ha például azt szeretné, hogy az eseményindító 15 percenként legyen leadott, állítsa a **Gyakoriság** értékét **Perc**értékre, és állítsa az **Intervallum** **értékét 15-re.**

1. A tervező eszköztárán válassza a **Mentés lehetőséget,** amely menti a logikai alkalmazást.

Ha válaszolni szeretne az eseményindítóra, adjon hozzá egy másik műveletet. Hozzáadhatja például a Twilio **Üzenet küldése** műveletet, amely egy e-mail érkezésekor küld egy szöveget.

## <a name="add-an-action"></a>Művelet hozzáadása

A [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) olyan művelet, amelyet a munkafolyamat futtat a logikai alkalmazásban. Ez a példa logikai alkalmazás e-mailt küld a Outlook.com-fiókjából. Használhatja a kimenetet egy másik eseményindító feltöltéséhez a műveletet. Tegyük fel például, hogy a logikai alkalmazás a SalesForce-ot **használja, amikor egy objektum létrejön.** Hozzáadhatja a Outlook.com **E-mail küldése** műveletet, és használhatja az e-mailben a SalesForce eseményindító kimeneteit.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Ha egy műveletet szeretne a munkafolyamat utolsó lépéseként felvenni, válassza az **Új lépés lehetőséget.** 

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A keresőmezőbe írja be szűrőként az "outlook.com" kifejezést. Ebben a példában válassza az **E-mail küldése**lehetőséget. 

1. Ha a rendszer kéri, hogy jelentkezzen be, adja meg a Outlook.com hitelesítő adatait, hogy a logikai alkalmazás csatlakozhat a fiókjához. Ellenkező esetben, ha a kapcsolat már létezik, adja meg a művelet tulajdonságainak adatait.

1. A tervező eszköztárán válassza a **Mentés lehetőséget,** amely menti a logikai alkalmazást.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő Swagger-fájljában leírt technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán](/connectors/outlook/)találja. 

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
