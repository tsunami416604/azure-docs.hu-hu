---
title: Csatlakozás az Office 365-felhasználókhoz
description: Az Office 365 Felhasználói profilokat beszerző és kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666856"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Profilok beszerezni és kezelése az Office 365-felhasználók ban az Azure Logic Apps használatával

Csatlakozzon az Office 365-felhasználókhoz, hogy profilokat, keresési felhasználókat és egyebeket kapjon. Az Office 365-felhasználók segítségével a következőket teheti:

* Az Office 365-felhasználók tól kapott adatok alapján alakíthatja ki üzleti folyamatait. 
* Olyan műveleteket használjon, amelyek közvetlen jelentéseket kapnak, lekapják a vezető felhasználói profilját stb. Ezek a műveletek választ kapnak, majd a kimenetet elérhetővé teszik más műveletek számára. Például egy személy közvetlen jelentéseit, majd ezeket az információkat, és frissítse az SQL Azure-adatbázis. 

A kezdéshez hozzon létre egy logikai alkalmazást most, [lásd: Logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-office-365-users"></a>Kapcsolat létrehozása az Office 365-felhasználókkal

Amikor hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, be kell jelentkeznie az Office 365-felhasználók fiókjába, hogy az Azure Logic Apps csatlakozni tudja a fiókjához.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

A kapcsolat létrehozása után adja meg az Office 365 Felhasználók tulajdonságait, például a felhasználói azonosítót. A **REST API-hivatkozás** ebben a cikkben ismerteti ezeket a tulajdonságokat.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő Swagger-leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az [összekötő referenciaoldalát.](/connectors/officeusers/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](apis-list.md)