---
title: "Csatlakozás az Azure Logic Apps GitHub |} Microsoft Docs"
description: "A github használatára az Azure Logic Apps munkafolyamatainak automatizálásához"
services: logic-apps
documentationcenter: 
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
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
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