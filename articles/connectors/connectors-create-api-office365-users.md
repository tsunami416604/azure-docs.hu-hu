---
title: Kapcsolódás az Office 365-felhasználókhoz
description: Felhasználói profilok kezelése az Office 365-felhasználók REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: addb64a9b43c51af8363caa6f0fb3261a618e893
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789519"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Ismerkedés az Office 365 felhasználói összekötővel
Kapcsolódjon az Office 365-felhasználókhoz profilok, keresés a felhasználók és egyebek között. Az Office 365-felhasználókkal a következőket teheti:

* Az Office 365-felhasználóktól kapott adatok alapján hozza létre üzleti folyamatát. 
* Olyan műveleteket használhat, amelyek közvetlen jelentéseket kapnak, beszerezhetik a felettes felhasználói profilját és egyebeket. Ezek a műveletek választ kapnak, majd elérhetővé teszik a kimenetet más műveletekhez. Például beszerezhet egy személy közvetlen jelentéseit, majd elvégezheti ezeket az adatokat, és frissítheti SQL Azure adatbázisát. 

A logikai alkalmazások létrehozásának első lépéseiről a [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakörben olvashat.

## <a name="create-a-connection-to-office-365-users"></a>Office 365-felhasználók közötti kapcsolatok létrehozása
Ha hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, be kell jelentkeznie az Office 365 felhasználói fiókjába, és engedélyeznie kell a Logic apps számára a fiókhoz való csatlakozást.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

A kapcsolatok létrehozása után adja meg az Office 365-felhasználók tulajdonságait, például a felhasználói azonosítót. A cikkben szereplő **REST API-hivatkozás** ezeket a tulajdonságokat ismerteti.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Megtekintheti a hencegés során definiált összes eseményindítót és műveletet, valamint az [összekötő részleteiben](/connectors/officeusers/)megjelenő korlátokat is.

## <a name="more-connectors"></a>További összekötők
Lépjen vissza az API-k [listájára](apis-list.md).