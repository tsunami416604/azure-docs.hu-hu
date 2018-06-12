---
title: Csatlakozás az Office 365 felhasználók – az Azure Logic Apps |} Microsoft Docs
description: Az Office 365 felhasználók REST API-k és az Azure Logic Apps felhasználói profilok kezelése
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: f030ac07dc1615c435c1a110836d7a03ab8a8546
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295608"
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