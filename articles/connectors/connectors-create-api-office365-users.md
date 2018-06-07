---
title: Csatlakozás az Office 365 felhasználók – az Azure Logic Apps |} Microsoft Docs
description: Az Office 365 felhasználók REST API-k és az Azure Logic Apps felhasználói profilok kezelése
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 92eaa2f323da9a96b0be4379d2dbcea8d90733e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609428"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Az Office 365-felhasználók összekötő az első lépései
Office 365 felhasználói profilok, a keresés felhasználói és több csatlakozni. Az Office 365-felhasználók a következőket teheti:

* Az üzleti folyamat Office 365-felhasználók származó adatok alapján történő létrehozása. 
* Közvetlen beosztottai első használata műveleteket vezető felhasználói profil, és több kapják meg. Ezeket a műveleteket válaszol, és végezze el a kimeneti más műveletek érhető el. Például lekérni egy személy közvetlen beosztottai, majd ezt az adatot és SQL Azure-adatbázis frissítéséhez. 

Most hozzon létre egy logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Kapcsolatot létesíthet Office 365-felhasználók
Ezt az összekötőt a logic Apps alkalmazások hozzáadásakor kell bejelentkezés az Office 365-felhasználó fiókját, és lehetővé teszik a logic Apps alkalmazásokat fiókjához.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Miután létrehozta a kapcsolatot, akkor adja meg az Office 365-felhasználók tulajdonságai, például a felhasználói azonosítóját. A **REST API-referenciában** ebben a cikkben ismerteti ezeket a tulajdonságokat.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/officeusers/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).