---
title: Kapcsolódás az Office 365-felhasználók – Azure Logic Apps |} A Microsoft Docs
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
ms.openlocfilehash: 3865fbc4fbc39da0860218565b0a8956b2dad8ee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105871"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Az Office 365 Users összekötővel használatának első lépései
Csatlakozhat Office 365-felhasználói profilok, a felhasználók keresése és egyéb beolvasásához. Az Office 365-felhasználók a következőket teheti:

* Az üzleti folyamat kap Office 365 felhasználói adatok alapján történő létrehozása. 
* Használható műveletek, amelyek a közvetlen beosztottak, beolvasása lekéréséhez-kezelő felhasználói profil stb. Ezek a műveletek válaszol, és végezze el a kimenetet más műveletek esetében érhető el. Ha például egy személy közvetlen beosztottak, beolvasása ezt az adatot és egy SQL Azure adatbázis frissítése. 

Hozzon létre egy logikai alkalmazás most már megkezdheti, lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Hozzon létre egy kapcsolatot az Office 365-felhasználók
Ha ezt az összekötőt a logic apps hozzáad, kell jelentkezzen be az Office 365 felhasználói fiók és a logic apps a fiókhoz való csatlakozás engedélyezése.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Miután létrehozta a kapcsolatot, akkor adja meg az Office 365-felhasználók tulajdonságai, például a felhasználói azonosítóját. A **REST API-referencia** ebben a cikkben azt ismerteti, ezeket a tulajdonságokat.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/officeusers/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).