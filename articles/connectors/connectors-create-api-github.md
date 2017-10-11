---
title: "Az Azure Logic Apps GitHub összekötő |} Microsoft Docs"
description: "Az Azure App service Logic Apps alkalmazások létrehozása GitHub egy webalapú Git-tárház szolgáltatást tartalmazó. Összes elosztott változat-vezérléshez és a forrás kódot (SCM) felügyeleti funkció Git, valamint a saját szolgáltatások hozzáadására kínál."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-github-connector"></a>A GitHub-összekötő az első lépései
GitHub egy webalapú Git-tárház szolgáltatást tartalmazó. Összes elosztott változat-vezérléshez és a forrás kódot (SCM) felügyeleti funkció Git, valamint a saját szolgáltatások hozzáadására kínál.

Most hozzon létre egy Logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-github"></a>Kapcsolatot létesíthet a Githubon
A Logic apps GitHub létrehozásához, létre kell hoznia egy **kapcsolat** adja meg a részleteket a következő tulajdonságokkal: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Token |Igen |Adja meg a GitHub hitelesítő adatokat |

Miután létrehozta a kapcsolatot, használhatja a műveletek végrehajtása és a jelen cikkben ismertetett eseményindítók figyelni. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/github/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).