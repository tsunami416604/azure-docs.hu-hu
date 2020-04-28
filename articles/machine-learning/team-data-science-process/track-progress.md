---
title: A csoportos adatelemzési folyamat projektjei előrehaladásának nyomon követése
description: Hogyan követheti nyomon az adatelemzési projektek előrehaladását az adattudományi csoport kezelői, a Team-érdeklődők és a projekt-érdeklődők.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864196"
---
# <a name="track-the-progress-of-data-science-projects"></a>Az adatelemzési projektek előrehaladásának nyomon követése

Az adatelemzési csoport kezelői, a Team-érdeklődők és a projekt-érdeklődők nyomon követhetik projektjeik előrehaladását.  A vezetők szeretnék tudni, hogy milyen munkát végeztek el, ki a munkát, és mi marad a munka.   Az elvárások kezelése a siker fontos eleme.

## <a name="azure-devops-dashboards"></a>Azure DevOps-irányítópultok

Ha Azure-DevOps használ, irányítópultokat hozhat létre az adott agilis projekthez társított tevékenységek és munkaelemek nyomon követéséhez. Az irányítópultokkal kapcsolatos további információkért lásd: [irányítópultok, jelentések és widgetek](/azure/devops/report/dashboards/).

Az irányítópultok és widgetek Azure DevOps való létrehozásával és testreszabásával kapcsolatos utasításokért tekintse meg a következő rövid útmutatókat:

- [Irányítópultok hozzáadása és kezelése](/azure/devops/report/dashboards/dashboards)
- [Widgetek hozzáadása az irányítópulthoz](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Minta irányítópult

Íme egy egyszerű példa az irányítópultra, amely egy agilis adatelemzési projekt Sprint-tevékenységeit követi nyomon, beleértve a társított adattárakhoz való véglegesítés számát is. 

- A **Countdown (visszaszámlálás** ) csempén az aktuális sprintben maradó napok száma látható. 

- A két **kódrészlet** az elmúlt hét nap során a két projekt adattárházában lévő véglegesítés számát mutatja. 

- A **TDSP-projekt munkaelemei** az összes munkaelem és azok állapotának lekérdezési eredményeit jelenítik meg. 

- Az **összesítő folyamatábra** (CFD) a lezárt és az aktív munkaelemek számát jeleníti meg.

- A **burndown diagramon** látható, hogy a munka még mindig a sprintben hátralévő idő után fejeződik be.

- A **burnup diagram** a Sprint teljes munkamennyiségéhez képest befejezett munkát mutat.

![Irányítópult](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>További lépések

[A csoportos adatelemzési folyamathoz](walkthroughs.md) tartozó útmutatók a folyamat lépéseinek bemutatására szolgáló útmutatókat listázza. A hivatkozott forgatókönyvek bemutatják, hogyan kezelheti a Felhőbeli és a helyszíni erőforrásokat intelligens alkalmazásokba. 
