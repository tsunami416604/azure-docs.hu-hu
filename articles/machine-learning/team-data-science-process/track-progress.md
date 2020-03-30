---
title: A csapatadat-elemzési folyamat projektjeinek előrehaladásának nyomon követése
description: Hogyan adatelemzési csoport menedzserek, csoport vezet, és a projekt-érdeklődők nyomon követheti az adatelemzési projekt előrehaladását.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864196"
---
# <a name="track-the-progress-of-data-science-projects"></a>Adatelemzési projektek előrehaladásának nyomon követése

Az adatelemzési csoport menedzserei, a csoportvezetők és a projektérdeklődők nyomon követhetik a projektjeik előrehaladását.  A vezetők tudni szeretnék, hogy milyen munkát végeztek, ki végezte a munkát, és mi a munka.   Az elvárások kezelése a siker fontos eleme.

## <a name="azure-devops-dashboards"></a>Azure DevOps-irányítópultok

Ha Az Azure DevOps használatával, irányítópultok segítségével nyomon követheti az adott Agilis projekthez társított tevékenységeket és munkaelemeket. Az irányítópultokról további információt az [Irányítópultok, jelentések és widgetek című témakörben talál.](/azure/devops/report/dashboards/)

Az Azure DevOps irányítópultjainak és widgetjeinek létrehozásáról és testreszabásáról az alábbi rövid útmutatókban talál útmutatást:

- [Irányítópultok hozzáadása és kezelése](/azure/devops/report/dashboards/dashboards)
- [Widgetek hozzáadása irányítópulthoz](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Minta irányítópult

Íme egy egyszerű példa irányítópult, amely nyomon követi az Agilis adatelemzési projekt sprint tevékenységeit, beleértve a kapcsolódó adattáraknak való véglegesítések számát. 

- A **visszaszámlálás** csempe mutatja, hogy hány nap marad az aktuális sprintben. 

- A két **kódcsempe** az elmúlt hét nap két projekttárban lévő véglegesítések számát mutatja. 

- **A TDSP Customer Project munkatételei** az összes munkaelemre vonatkozó lekérdezés eredményeit és azok állapotát jelenítik meg. 

- A **göngyölt folyamatdiagram** (CFD) a lezárt és az aktív munkaelemek számát mutatja.

- Az **égési diagram** azt mutatja, hogy a munka még mindig befejeződik a hátralévő idő vel szemben a sprintben.

- A **burnup diagram** mutatja befejezett munka képest teljes munka mennyisége a sprint.

![Irányítópult](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>További lépések

[A csapatadat-elemzési folyamatot végrehajtó forgatókönyvek](walkthroughs.md) azokat a forgatókönyveket sorolja fel, amelyek bemutatják az összes folyamatlépést. A csatolt forgatókönyvek bemutatják, hogyan kezelheti a felhőbeli és a helyszíni erőforrásokat intelligens alkalmazásokká. 
