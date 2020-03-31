---
title: Csatlakozás a OneDrive Vállalati verzióhoz
description: Fájlok feltöltése és kezelése a OneDrive Vállalati REST API-kkal és az Azure Logic Apps alkalmazásokkal
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 48c819566db43e2f997309d1bea55a9436ec7085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789451"
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>A OneDrive Vállalati verzió összekötőjének első lépései
Csatlakozzon a OneDrive Vállalati verzióhoz a fájlok kezeléséhez. Különböző műveleteket hajthat végre, például fájlok feltöltését, frissítését, begetését és törlését.

A kezdéshez hozzon létre egy logikai alkalmazást most, [lásd: Logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-onedrive-for-business"></a>Kapcsolat létrehozása a OneDrive Vállalati verzióval
Ha logikai alkalmazásokat szeretne létrehozni a OneDrive Vállalati verzióval, először létre kell hoznia egy **kapcsolatot,** majd meg kell adnia a következő tulajdonságok részleteit:

| Tulajdonság | Kötelező | Leírás |
| --- | --- | --- |
| Jogkivonat |Igen |A OneDrive Vállalati verzió hitelesítő adatainak megadása |

A kapcsolat létrehozása után használhatja a műveletek végrehajtására és a cikkben leírt eseményindítók figyelésére.

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Tekintse meg a swaggerben definiált eseményindítókat és műveleteket, és tekintse meg a korlátokat az [összekötő részleteiben.](/connectors/onedriveforbusinessconnector/)

## <a name="more-connectors"></a>További csatlakozók
Lépjen vissza az [API-k listájára.](apis-list.md)