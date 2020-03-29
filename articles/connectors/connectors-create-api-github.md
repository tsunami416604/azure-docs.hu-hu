---
title: A GitHub-tártár elérése, figyelése és kezelése
description: A GitHub-események figyelése és a GitHub-tártár kezelése automatikus munkafolyamatok létrehozásával az Azure Logic Apps alkalmazásokkal
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378449"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>A GitHub-tártár figyelése és kezelése az Azure Logic Apps használatával

A GitHub egy webalapú Git-tárház hosting szolgáltatás, amely a Git összes elosztott verzióvezérlési és forráskód-kezelési (SCM) funkcióját és egyéb funkciókat kínál.

A GitHub-összekötő első lépéseihez [hozzon létre egy logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-github"></a>Kapcsolat létrehozása a GitHubbal

A GitHub-összekötő logikai alkalmazásban való használatához először létre kell hoznia egy *kapcsolatot,* majd meg kell adnia a következő tulajdonságok részleteit: 

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| Jogkivonat | Igen | Adja meg a GitHub-hitelesítő adatait. |

A kapcsolat létrehozása után végrehajthatja a műveleteket, és figyelheti a cikkben ismertetett eseményindítókat.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az [összekötő referenciaoldalát.](/connectors/github/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)