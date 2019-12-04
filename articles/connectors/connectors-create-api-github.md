---
title: Kapcsolódás a GitHubhoz
description: GitHub-események monitorozása a GitHub REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789755"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Kapcsolódás a GitHubhoz Azure Logic Apps

A GitHub egy webalapú git-tárház, amely a git és más szolgáltatások összes elosztott változat-és forráskód-kezelési (SCM) funkcióját kínálja.

Első lépésként [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md)a GitHub-összekötő megkezdéséhez.

## <a name="create-a-connection-to-github"></a>Hozzon létre egy kapcsolódást a GitHubhoz

Ha logikai alkalmazásban szeretné használni a GitHub-összekötőt, először létre kell hoznia egy *kapcsolatokat* , majd meg kell adnia a következő tulajdonságokkal kapcsolatos adatokat: 

| Tulajdonság | Szükséges | Leírás | 
| -------- | -------- | ----------- | 
| Jogkivonat | Igen | Adja meg a GitHub hitelesítő adatait. |

A kapcsolatok létrehozása után végrehajthatja a műveleteket, és figyelheti a cikkben ismertetett eseményindítókat.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az [összekötő hivatkozási oldalát](/connectors/github/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése