---
title: Csatlakozhat a Facebookhoz – Azure Logic Apps |} A Microsoft Docs
description: Kezelheti a határidőt és a Facebook REST API-k és az Azure Logic Apps-lap
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 25595127d913d3cd093e0af3d7916e33fc7cb352
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105975"
---
# <a name="get-started-with-the-facebook-connector"></a>A Facebook-összekötő használatának első lépései
Csatlakozhat a Facebookhoz, és közzé az idővonalon, laptartalmakat és egyéb beolvasása. A Facebook, a következőket teheti:

* Az adatok facebookról kap az üzleti folyamatot hozhat létre. 
* Trigger akkor használja, ha új hozzászólás érkezik.
* Használható műveletek közzé az idővonalon, laptartalmakat és egyéb beolvasása. Ezek a műveletek válaszol, és végezze el a kimenetet más műveletek esetében érhető el. Például ha új bejegyzés van az eseményeket egy idővonalon megjelenítve, elvégezhető a szükséges, hogy a post és küldje le a Twitter-hírcsatornát. 

Hozzon létre egy logikai alkalmazás most már megkezdheti, lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Hozzon létre egy kapcsolatot a Facebookon
Ha ezt az összekötőt a logic apps hozzáad, engedélyeznie kell a logic apps szeretne csatlakozni a Facebookhoz.

1. Jelentkezzen be a Facebook-fiókban
2. Válassza ki **engedélyezés**, és lehetővé teszi a logic apps való csatlakozáshoz és a Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/facebook/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).