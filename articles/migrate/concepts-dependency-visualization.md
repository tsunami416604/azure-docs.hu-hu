---
title: "Függőség képi megjelenítés Azure áttelepítése |} Microsoft Docs"
description: "Értékelési számítások az Azure áttelepítése szolgáltatás áttekintése."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 78e52157-edfd-4b09-923f-f0df0880e0e0
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: e787cd6746b53c28fc1495b9a3ac62f2aa08f639
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="dependency-visualization"></a>A függőségi képi megjelenítés

A [Azure áttelepítése](migrate-overview.md) szolgáltatások értékelésére a helyszíni gépeket az Azure-bA áttelepítés csoportját. Gépek csoportosíthatja, függőség képi megjelenítés is használhat. A cikkben a szolgáltatásról.


## <a name="overview"></a>Áttekintés

Függőség képi megjelenítés Azure áttelepítése lehetővé teszi nagyobb megbízhatósággal értékeléséhez áttelepítési csoportok létrehozásához. Függőség képi megjelenítés használata a hálózati függőségeit, az adott gépek vagy gépcsoport keresztül tekintheti meg. Ez akkor hasznos, annak érdekében, hogy egyik funkció sem vagy elveszett (vagy elfelejtett gépek) az áttelepítési folyamat során, amikor az alkalmazások és számítási feladatok futtatása több számítógépen.  

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure áttelepítése használja a [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md) megoldás [Naplóelemzési](../log-analytics/log-analytics-overview.md) függőségi a képi megjelenítéshez tartozó.
- Egy Azure áttelepítési projekt létrehozásakor az előfizetéshez egy OMS Naplóelemzési munkaterület jön létre.
- A munkaterület neve az a név, az áttelepítés projekt, előtagként a megadott **áttelepítése-**, és opcionálisan toldalékokkal számmal. 
- A Naplóelemzési munkaterület navigáljon a **Essentials** projekt **áttekintése** lap.
- A létrehozott munkaterület van megjelölve, a kulccsal **MigrateProject**, és értéket **projektnevet**. Ezek használhatja a Keresés az Azure-portálon.  

    ![A Naplóelemzési munkaterület](./media/concepts-dependency-visualization/oms-workspace.png)

Függőség képi megjelenítés használata esetén meg kell töltse le és telepítse az ügynököt minden a helyszíni gépen, amely az elemezni kívánt.  

## <a name="do-i-need-to-pay-for-it"></a>Kell azt díj ellenében?

Igen. Alapértelmezés szerint a Naplóelemzési munkaterület jön létre, de függőségi képi megjelenítés használata az Azure áttelepítése nem használ. Ha függőségi képi megjelenítés használja (vagy a munkaterületen kívül Azure áttelepítése), van szó, a munkaterület-használat.  [További](https://www.microsoft.com/cloud-platform/operations-management-suite) vonatkozó Szolgáltatástérkép megoldás. 

## <a name="how-do-i-manage-the-workspace"></a>Hogyan kezelhetem a munkaterület?

A Naplóelemzési munkaterület kívül Azure áttelepítéséhez használható. Ha törli az áttelepítési projekt, amelyben létrehozták nem törlődik. Ha már nem szükséges a munkaterület [törölni](../log-analytics/log-analytics-manage-access.md) manuálisan.

Ne törölje a hozta létre Azure az áttelepítés munkaterületen, hacsak nem törli az áttelepítési projekt. Ha így tesz, függőségek nem működik megfelelően.

## <a name="next-steps"></a>Következő lépések

[Csoport gépek gép függőségek](how-to-create-group-machine-dependencies.md)