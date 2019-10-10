---
title: A csoportos adatelemzési folyamat projektjei előrehaladásának nyomon követése
description: Hogyan követheti nyomon az adatelemzési projektek előrehaladását az adattudományi csoport kezelői, a Team-érdeklődők és a projekt-érdeklődők.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244056"
---
# <a name="track-the-progress-of-data-science-projects"></a>Az adatelemzési projektek előrehaladásának nyomon követése

Az adatelemzési csoport kezelői, a csapat-és a projekt-érdeklődők nyomon követhetik a projektjeik állapotát, például hogy milyen munkát végeztek, ki végezte el a munkát, és mi marad a munka. 

## <a name="azure-devops-dashboards"></a>Azure DevOps-irányítópultok

Ha Azure-DevOps használ, irányítópultokat hozhat létre az adott agilis projekthez társított tevékenységek és munkaelemek nyomon követéséhez. Az irányítópultokkal kapcsolatos további információkért lásd: [irányítópultok, jelentések és widgetek](/azure/devops/report/dashboards/).

Az irányítópultok és widgetek Azure DevOps való létrehozásával és testreszabásával kapcsolatos utasításokért tekintse meg a következő rövid útmutatókat:

- [Irányítópultok hozzáadása és kezelése](/azure/devops/report/dashboards/dashboards)
- [Widgetek hozzáadása az irányítópulthoz](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Példa irányítópultra

Íme egy egyszerű példa az irányítópultra, amely egy agilis adatelemzési projekt Sprint-tevékenységeit követi nyomon, beleértve a társított adattárakhoz való véglegesítés számát is. 

- A **Countdown (visszaszámlálás** ) csempén az aktuális sprintben maradó napok száma látható. 

- A két **kódrészlet** az elmúlt hét nap során a két projekt adattárházában lévő véglegesítés számát mutatja. 

- A **TDSP-projekt munkaelemei** az összes munkaelem és azok állapotának lekérdezési eredményeit jelenítik meg. 

- Az **összesítő folyamatábra** (CFD) a lezárt és az aktív munkaelemek számát jeleníti meg.

- A **burndown diagramon** látható, hogy a munka még mindig a sprintben hátralévő idő után fejeződik be.

- A **burnup diagram** a Sprint teljes munkamennyiségéhez képest befejezett munkát mutat.

![Irányítópult](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Következő lépések

[A csoportos adatelemzési folyamattal](walkthroughs.md) kapcsolatos útmutatók azokat a bemutatókat ismertetik, amelyek bemutatják a folyamat összes lépését adott forgatókönyvekhez, hivatkozásokat és miniatűr leírást. A hivatkozott forgatókönyvek bemutatják, hogyan kombinálhatja a Felhőbeli és a helyszíni eszközöket és szolgáltatásokat munkafolyamatokban vagy folyamatokban intelligens alkalmazások létrehozásához. 
