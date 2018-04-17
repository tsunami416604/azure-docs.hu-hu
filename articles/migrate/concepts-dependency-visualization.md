---
title: Függőség képi megjelenítés Azure áttelepítése |} Microsoft Docs
description: Értékelési számítások az Azure áttelepítése szolgáltatás áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: d075ce9ee124d373a1284577324a50338245a03f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="dependency-visualization"></a>Függőségek vizualizációja

A [Azure áttelepítése](migrate-overview.md) szolgáltatások értékelésére a helyszíni gépeket az Azure-bA áttelepítés csoportját. Gépek csoportosíthatja, függőség képi megjelenítés is használhat. A cikkben a szolgáltatásról.


## <a name="overview"></a>Áttekintés

Függőség képi megjelenítés Azure áttelepítése lehetővé teszi nagyobb megbízhatósággal értékeléséhez áttelepítési csoportok létrehozásához. Függőség képi megjelenítés használata a hálózati függőségeit, az adott gépek vagy gépcsoport keresztül tekintheti meg. Ez akkor hasznos, annak érdekében, hogy egyik funkció sem vagy elveszett (vagy elfelejtett gépek) az áttelepítési folyamat során, amikor az alkalmazások és számítási feladatok futtatása több számítógépen.  

## <a name="how-does-it-work"></a>Hogyan működik?

Az Azure áttelepítése használja a [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md) megoldás [Naplóelemzési](../log-analytics/log-analytics-overview.md) függőségi a képi megjelenítéshez tartozó.
- Egy Azure áttelepítési projekt létrehozásakor az előfizetéshez a Naplóelemzési munkaterület jön létre.
- A munkaterület neve az a név, az áttelepítés projekt, előtagként a megadott **áttelepítése-**, és opcionálisan toldalékokkal számmal. 
- A Naplóelemzési munkaterület navigáljon a **Essentials** projekt **áttekintése** lap.
- A létrehozott munkaterület van megjelölve, a kulccsal **MigrateProject**, és értéket **projektnevet**. Ezek használhatja a Keresés az Azure-portálon.  

    ![Log Analytics-munkaterület](./media/concepts-dependency-visualization/oms-workspace.png)

Függőség képi megjelenítés használata esetén meg kell töltse le és telepítse az ügynököt minden a helyszíni gépen, amely az elemezni kívánt.  

## <a name="do-i-need-to-pay-for-it"></a>Kell azt díj ellenében?

Az Azure Migrate díjmentesen érhető el. Az Azure telepítse át a függőségi képi megjelenítés funkciók használatát a Szolgáltatástérkép szükséges. Egy Azure áttelepítése projekt létrehozása Azure áttelepítése automatikusan hoz létre egy új Naplóelemzési munkaterület az Ön nevében.

> [!NOTE]
> A függőség képi megjelenítés szolgáltatás Szolgáltatástérkép használja a Naplóelemzési munkaterület keresztül. 28. február 2018, mert az általános elérhetőség Azure áttelepítése hirdetmény a funkció már elérhető külön díjfizetés nélkül. Hozzon létre egy új projektet el kell használni az ingyenes használati munkaterület. Általános availaibility előtt meglévő munkaterületek érhetők továbbra is chargable, ezért azt javasoljuk, hogy helyezze át egy új projektet.

1. A Naplóelemzési munkaterület Szolgáltatástérkép kívül bármely megoldást használatát lesz függő díj terheli. Naplóelemzési szabványos. 
2. Minden további költség nélkül áttelepítési forgatókönyvek támogatására, a Service Map megoldás fog nem számítunk fel díjakat az első 180 nap való létrehozása a Azure áttelepítése projekt, amely után standard-díjak érvényesek.
3. Csak a projekt létrehozása részeként létrehozott munkaterület szabad használható lesz.

Amikor regisztrál a munkaterület ügynökök, használja az ID és a telepítési ügynök lépések lapján a projekt által megadott kulcs. Nem lehet egy meglévő munkaterületen, és rendelje hozzá azt az Azure áttelepítése projekt.

Az Azure áttelepítése projekt törlésekor a munkaterület nem törlődik. A projekt törlése utáni, a Service Map használata csak akkor szabad, és minden csomóponton a Naplóelemzési munkaterület fizetős szintjének megfelelően kell fizetni.

További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál. 

## <a name="how-do-i-manage-the-workspace"></a>Hogyan kezelhetem a munkaterület?

A Naplóelemzési munkaterület kívül Azure áttelepítéséhez használható. Ha törli az áttelepítési projekt, amelyben létrehozták nem törlődik. Ha már nem szükséges a munkaterület [törölni](../log-analytics/log-analytics-manage-access.md) manuálisan.

Ne törölje a hozta létre Azure az áttelepítés munkaterületen, hacsak nem törli az áttelepítési projekt. Ha így tesz, függőségek nem működik megfelelően.

## <a name="next-steps"></a>További lépések

[Csoport gépek gép függőségek](how-to-create-group-machine-dependencies.md)