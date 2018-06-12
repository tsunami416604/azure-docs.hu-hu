---
title: Google-meghajtó - Azure Logic Apps csatlakozni |} Microsoft Docs
description: Létrehozásához és kezeléséhez a Google Drive REST API-k és az Azure Logic Apps fájlokat
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
ms.openlocfilehash: 4994687afbdd23f0265138cd1d4eff53bb47c163
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295883"
---
# <a name="get-started-with-the-google-drive-connector"></a>A Google meghajtó összekötő az első lépései
Google-meghajtó létrehozása, fájlok, a get sorok és a további csatlakozni. Google-meghajtó a következőket teheti: 

* Az üzleti folyamata, a keresés származó adatok alapján történő létrehozása. 
* Műveletek segítségével képek keresni, keresse a híreket és még sok más. Ezeket a műveleteket válaszol, és végezze el a kimeneti más műveletek érhető el. Például keresse meg a videót, és Twitter használatával, amely egy Twitter-hírcsatorna a videó utáni.

Most hozzon létre egy logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Google-meghajtóra kapcsolat létrehozása
Ezt az összekötőt a logic apps hozzáadásakor engedélyeznie kell a logic apps a Google-meghajtóról való kapcsolódáshoz.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Miután létrehozta a kapcsolatot, a Google-meghajtó tulajdonságainak, például a mappa elérési útját vagy a fájl nevét adja meg. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/googledrive/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).
