---
title: Kapcsolódás a Outlook.com
description: E-mailek, naptárak és névjegyek kezelése a Outlook.com REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789365"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>E-mailek, naptárak és névjegyek kezelése a Outlook.com-ben Azure Logic Apps

Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti a Outlook.com-fiókját egy logikai alkalmazásban a Box Connector használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a Outlook.com-fiókhoz tartozó feladatokat és munkafolyamatokat, például:

* E-mail küldése. 
* Értekezletek beosztása.
* Névjegyek hozzáadása. 

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [Outlook.com-fiók](https://outlook.live.com/owa/)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az a logikai alkalmazás, amelyhez el szeretné érni a Outlook.com-fiókját. A logikai alkalmazás Outlook-triggerrel való elindításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség. 

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Kapcsolódás a Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő hencegő fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/outlook/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése