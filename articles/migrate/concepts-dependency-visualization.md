---
title: "Függőség képi megjelenítés Azure áttelepítése |} Microsoft Docs"
description: "Értékelési számítások az Azure áttelepítése szolgáltatás áttekintése."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 10bc249aa70852dce71c96e46319925cececdd8b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="dependency-visualization"></a>Függőségek vizualizációja

A [Azure áttelepítése](migrate-overview.md) szolgáltatások értékelésére a helyszíni gépeket az Azure-bA áttelepítés csoportját. Gépek csoportosíthatja, függőség képi megjelenítés is használhat. A cikkben a szolgáltatásról.


## <a name="overview"></a>Áttekintés

Függőség képi megjelenítés Azure áttelepítése lehetővé teszi nagyobb megbízhatósággal értékeléséhez áttelepítési csoportok létrehozásához. Függőség képi megjelenítés használata a hálózati függőségeit, az adott gépek vagy gépcsoport keresztül tekintheti meg. Ez akkor hasznos, annak érdekében, hogy egyik funkció sem vagy elveszett (vagy elfelejtett gépek) az áttelepítési folyamat során, amikor az alkalmazások és számítási feladatok futtatása több számítógépen.  

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure áttelepítése használja a [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md) megoldás [Naplóelemzési](../log-analytics/log-analytics-overview.md) függőségi a képi megjelenítéshez tartozó.
- Egy Azure áttelepítési projekt létrehozásakor az előfizetéshez egy OMS Naplóelemzési munkaterület jön létre.
- A munkaterület neve az a név, az áttelepítés projekt, előtagként a megadott **áttelepítése-**, és opcionálisan toldalékokkal számmal. 
- A Naplóelemzési munkaterület navigáljon a **Essentials** projekt **áttekintése** lap.
- A létrehozott munkaterület van megjelölve, a kulccsal **MigrateProject**, és értéket **projektnevet**. Ezek használhatja a Keresés az Azure-portálon.  

    ![Log Analytics-munkaterület](./media/concepts-dependency-visualization/oms-workspace.png)

Függőség képi megjelenítés használata esetén meg kell töltse le és telepítse az ügynököt minden a helyszíni gépen, amely az elemezni kívánt.  

## <a name="do-i-need-to-pay-for-it"></a>Kell azt díj ellenében?

Igen. Alapértelmezés szerint a Naplóelemzési munkaterület jön létre, de függőségi képi megjelenítés használata az Azure áttelepítése nem használ. Ha függőségi képi megjelenítés használja (vagy a munkaterületen kívül Azure áttelepítése), van szó, a munkaterület-használat.  [További](https://azure.microsoft.com/pricing/details/insight-analytics/) vonatkozó Szolgáltatástérkép megoldás. 

## <a name="how-do-i-manage-the-workspace"></a>Hogyan kezelhetem a munkaterület?

A Naplóelemzési munkaterület kívül Azure áttelepítéséhez használható. Ha törli az áttelepítési projekt, amelyben létrehozták nem törlődik. Ha már nem szükséges a munkaterület [törölni](../log-analytics/log-analytics-manage-access.md) manuálisan.

Ne törölje a hozta létre Azure az áttelepítés munkaterületen, hacsak nem törli az áttelepítési projekt. Ha így tesz, függőségek nem működik megfelelően.

## <a name="next-steps"></a>Következő lépések

[Csoport gépek gép függőségek](how-to-create-group-machine-dependencies.md)