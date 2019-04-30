---
title: Kapcsolódás az Office 365 videó – Azure Logic Apps |} A Microsoft Docs
description: Office 365 videó REST API-k és az Azure Logic Apps-videók kezelése
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c10a2aa097b63fd3751be01bbfeb6097080bbb9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105819"
---
# <a name="get-started-with-the-office365-video-connector"></a>Az Office 365 videó-összekötő használatának első lépései
Csatlakozás az Office 365 videó videó az Office 365 adatainak beolvasása, videók és egyéb listáját. Az Office 365 videó segítségével:

* Az üzleti folyamat kap az Office 365 videó adatok alapján történő létrehozása. 
* A videóportál állapotának ellenőrzése, az összes videó egy csatornát, és további listája-műveletek használata. Ezek a műveletek válaszol, és végezze el a kimenetet más műveletek esetében érhető el. Ha például a Bing Search connector használata az Office 365-videók keresése, és az Office 365 videó-összekötő használatával, hogy a videó adatainak beolvasása. Ha a videó megfelel a követelményeknek, a Facebookon könyvelheti ezt a videót. 

Hozzon létre egy logikai alkalmazás most már megkezdheti, lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Kapcsolatot létesíthet az Office 365 videó-összekötő
Ha ezt az összekötőt a logic apps hozzáad, kell jelentkezzen be fiókjába az Office 365 videó és a logic apps a fiókhoz való csatlakozás engedélyezése.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Miután létrehozta a kapcsolatot, az Office 365 videó tulajdonságait, például a bérlő nevét adja meg, vagy csatorna azonosítója. 


## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).