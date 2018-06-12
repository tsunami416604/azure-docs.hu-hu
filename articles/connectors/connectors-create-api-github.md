---
title: GitHub - Azure Logic Apps csatlakozni |} Microsoft Docs
description: A GitHub REST API-k és az Azure Logic Apps GitHub-események figyelésére
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295812"
---
# <a name="connect-to-github"></a>Kapcsolódás a Githubon

GitHub egy olyan webes Git tárház üzemeltetési szolgáltatás, amely az összes elosztott változat vezérlő és forrás kódot (SCM) felügyeleti funkciókat a Git és egyéb szolgáltatásokat kínál.

A GitHub-összekötővel, első lépésként [először hozzon létre egy logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Kapcsolatot létesíthet a Githubon

A logikai alkalmazás a GitHub-összekötő használatára, akkor először létre kell hoznia egy *kapcsolat* részletek adja meg ezeket a tulajdonságokat: 

| Tulajdonság | Szükséges | Leírás | 
| -------- | -------- | ----------- | 
| Jogkivonat | Igen | Adja meg a GitHub hitelesítő adatokat. |

Miután létrehozta a kapcsolatot, a műveletek végrehajtása, és a jelen cikkben ismertetett eseményindítók figyelését.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Eseményindítók és műveletek Swagger és bármely korlátok, tekintse át a [connector részleteket](/connectors/github/).

## <a name="find-more-connectors"></a>További összekötők keresése

* Tekintse át a [összekötőket felsoroló](apis-list.md).