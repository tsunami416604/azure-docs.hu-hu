---
title: "Adja hozzá az Office 365-felhasználók összekötőt a Logic Apps |} Microsoft Docs"
description: "Office 365-felhasználók összekötő REST API-paraméterekkel rendelkező áttekintése"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 330f733440932a769eb0fe6031cd0d947a820080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-office-365-users-connector"></a>Az Office 365-felhasználók összekötő az első lépései
Office 365 felhasználói profilok, a keresés felhasználói és több csatlakozni. Az Office 365-felhasználók a következőket teheti:

* Az üzleti folyamat Office 365-felhasználók származó adatok alapján történő létrehozása. 
* Közvetlen beosztottai első használata műveleteket vezető felhasználói profil, és több kapják meg. Ezeket a műveleteket válaszol, és végezze el a kimeneti más műveletek érhető el. Például lekérni egy személy közvetlen beosztottai, majd ezt az adatot és SQL Azure-adatbázis frissítéséhez. 

Most hozzon létre egy logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-office-365-users"></a>Kapcsolatot létesíthet Office 365-felhasználók
Ezt az összekötőt a logic Apps alkalmazások hozzáadásakor kell bejelentkezés az Office 365-felhasználó fiókját, és lehetővé teszik a logic Apps alkalmazásokat fiókjához.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Miután létrehozta a kapcsolatot, akkor adja meg az Office 365-felhasználók tulajdonságai, például a felhasználói azonosítóját. A **REST API-referenciában** a témakörben ismertetett ezeket a tulajdonságokat.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/officeusers/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).