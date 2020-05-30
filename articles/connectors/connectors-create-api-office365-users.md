---
title: Kapcsolódás az Office 365-felhasználókhoz
description: A profilokat az Office 365 felhasználói profiljaiban lekérdező és kezelő feladatok és munkafolyamatok automatizálása Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 0361d42ea614b394142e32a9193ab1d48d1a4a6c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194238"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Az Office 365-felhasználók profiljainak lekérése és kezelése Azure Logic Apps használatával

Kapcsolódjon az Office 365-felhasználókhoz profilok, keresés a felhasználók és egyebek között. Az Office 365-felhasználókkal a következőket teheti:

* Az Office 365-felhasználóktól kapott adatok alapján hozza létre üzleti folyamatát. 
* Olyan műveleteket használhat, amelyek közvetlen jelentéseket kapnak, beszerezhetik a felettes felhasználói profilját és egyebeket. Ezek a műveletek választ kapnak, majd elérhetővé teszik a kimenetet más műveletekhez. Például beszerezhet egy személy közvetlen jelentéseit, majd megteheti ezeket az adatokat, és frissítheti Azure SQL Database-adatbázisát. 

A logikai alkalmazások létrehozásának első lépéseiről a [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakörben olvashat.

## <a name="create-a-connection-to-office-365-users"></a>Office 365-felhasználók közötti kapcsolatok létrehozása

Ha hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, be kell jelentkeznie az Office 365 felhasználói fiókjába, hogy Azure Logic Apps tud csatlakozni a fiókjához.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

A kapcsolatok létrehozása után adja meg az Office 365-felhasználók tulajdonságait, például a felhasználói azonosítót. A cikkben szereplő **REST API-hivatkozás** ezeket a tulajdonságokat ismerteti.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az eseményindítók, műveletek és korlátok részletes technikai részleteiért lásd az összekötők feladatainak leírását, és tekintse át az [összekötő hivatkozási oldalát](/connectors/officeusers/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](apis-list.md) megismerése