---
title: Csatlakozás az Outlook.com-ra – Azure Logic Apps |} A Microsoft Docs
description: E-mailt, naptárak és az Outlook.com-os REST API-k és az Azure Logic Apps névjegyek kezelése
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8030ab9d317c1deefaf441008b9022c4a26bb17c
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746162"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>E-mailt, naptárak és névjegyeit, állítsa be az Azure Logic Apps Outlook.com-os kezelése

Ez a cikk bemutatja, hogyan hozzon létre és kezelheti az Outlook.com-fiókjából belül egy logikai alkalmazást, a Box-összekötővel. Ezzel a módszerrel hozhat létre a logic apps, feladatok és automatizáló munkafolyamatokat az Outlook.com-fiókjából, például:

* E-mail küldése. 
* Értekezletek ütemezését.
* Adja hozzá a névjegyekhez. 

Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [Outlook.com-fiókjából](https://outlook.live.com/owa/)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A logikai alkalmazás, ahol szeretné elérni az Outlook.com-fiókjából. A logikai alkalmazás elindításához egy Outlook-eseményindítóval kell egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Csatlakozás az Outlook.com-ra

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részleteket, például a triggereket, műveletek és -korlátok, az összekötő Swagger-fájl által leírt: a [összekötő referencialapja](/connectors/outlook/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)