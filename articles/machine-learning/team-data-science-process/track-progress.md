---
title: "Tudományos adatok - projektek Azure Machine Learning végrehajtásának |} Microsoft Docs"
description: "Hogyan egy adatok tudósok nyomon követheti a adatok tudományos projekt."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: c8e1882570a8cfcb4a75f0904a2138d007e0bc5b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="track-progress-of-data-science-projects"></a>Nyomon követni az adatok tudományos projektek

Adatok tudományos csoport kezelők team érdeklődők és projekt érdeklődők kell a csapatprojektek előrehaladását úgy követheti nyomon, milyen munkahelyi őket, és ki végzett, és továbbra is megtalálható a feladatlisták. 

## <a name="vsts-dashboards"></a>VSTS irányítópultok
Ha a Visual Studio Team Services (VSTS) használata esetén is a tevékenységeket és a megadott gyors projekthez tartozó munkaelemek követéséhez irányítópultok létrehozásához. 

További információ a létrehozásáról és testre szabhatja a Visual Studio Team Services widgeteket és az irányítópultokat tekintse meg a következő utasítássor:

- [Fel és kezelheti az irányítópultok](https://docs.microsoft.com/vsts/report/dashboards/dashboards)
- [Widgetek hozzáadása az irányítópult](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Példa irányítópult

Ez egy egyszerű példa irányítópult gyors adatok tudományos projektben, valamint a véglegesítések számát sprint tevékenységeit követésére társított adattárak beépített. A **bal felső** panelen látható:

- az aktuális sprint a visszaszámlálási 
- az elmúlt 7 napban minden tárház véglegesítések száma
- a munkaelem bizonyos felhasználók részére. 

A fennmaradó panelek jelenítse meg az összesített folyamatábrája (CFD), a burndown és a projekt burnup:

- **Bal alsó részén**: CFD munkaelem egy adott állapotban mennyisége megjelenítő jóváhagyott szürke kék véglegesítve lett, és zöld történik.
- **Jobb felső**: burndown diagram bal oldali és a hátralévő idő befejezéséhez munka).
- **Jobb alsó**: burnup diagram befejezése és a munka teljes mennyisége munkáját.

![irányítópult](./media/track-progress/dashboard.png)

Hogyan hozhat létre diagramokat egy leírását lásd a quickstarts és oktatóanyagok: [irányítópultok](https://docs.microsoft.com/vsts/report/dashboards/).
 
## <a name="next-steps"></a>Következő lépések

Forgatókönyvek, amelyek azt mutatják, a folyamat lépései **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 
