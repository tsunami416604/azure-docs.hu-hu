---
title: Csatlakozás a Githubhoz – Azure Logic Apps |} A Microsoft Docs
description: A GitHub REST API-k és az Azure Logic Apps GitHub-események figyelése
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
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462563"
---
# <a name="connect-to-github"></a>Csatlakozás a Githubhoz

A GitHub az olyan webes Git adattár üzemeltetési szolgáltatás, az elosztott verziókezelést, és a forrás kód management (SCM) funkciója a Gitben és a más szolgáltatások mindegyikét.

A GitHub-összekötő használatának megkezdéséhez [először hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Kapcsolatot létesíthet a Githubon

A GitHub-összekötő használatához logikai alkalmazásban, először létre kell hoznia egy *kapcsolat* részleteit adja meg ezeket a tulajdonságokat: 

| Tulajdonság | Szükséges | Leírás | 
| -------- | -------- | ----------- | 
| Jogkivonat | Igen | Adja meg a GitHub hitelesítő adataival. |

Miután létrehozta a kapcsolatot, hajtsa végre a műveleteket, és a jelen cikkben ismertetett eseményindítók figyelését.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Eseményindítók és műveletek, a Swagger és korlátait, tekintse át a [összekötő részletei](/connectors/github/).

## <a name="find-more-connectors"></a>További összekötők keresése

* Tekintse át a [összekötők listája](apis-list.md).