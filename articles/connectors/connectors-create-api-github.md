---
title: Kapcsolódás a GitHubhoz – Azure Logic Apps
description: GitHub-események monitorozása a GitHub REST API-kkal és Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050903"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Kapcsolódás a GitHubhoz Azure Logic Apps

A GitHub egy webalapú git-tárház, amely a git és más szolgáltatások összes elosztott változat-és forráskód-kezelési (SCM) funkcióját kínálja.

Első lépésként [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md)a GitHub-összekötő megkezdéséhez.

## <a name="create-a-connection-to-github"></a>Hozzon létre egy kapcsolódást a GitHubhoz

Ha logikai alkalmazásban szeretné használni a GitHub-összekötőt, először létre kell hoznia egy *kapcsolatokat* , majd meg kell adnia a következő tulajdonságokkal kapcsolatos adatokat: 

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| Jogkivonat | Igen | Adja meg a GitHub hitelesítő adatait. |

A kapcsolatok létrehozása után végrehajthatja a műveleteket, és figyelheti a cikkben ismertetett eseményindítókat.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az [összekötő hivatkozási oldalát](/connectors/github/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése