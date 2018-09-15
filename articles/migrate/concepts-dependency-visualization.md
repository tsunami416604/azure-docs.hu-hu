---
title: Az Azure Migrate függőségmegjelenítés |} A Microsoft Docs
description: Értékelési számítások az Azure Migrate szolgáltatás áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: raynew
ms.openlocfilehash: 5880c98b0f77b75dfb10969311408307b0c4afbd
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603362"
---
# <a name="dependency-visualization"></a>Függőségek vizualizációja

A [Azure Migrate](migrate-overview.md) szolgáltatások felméri a helyszíni gépek áttelepítése az Azure-bA a csoportjait. Gépek csoportosíthatja, használhatja a függőségek képi megjelenítéséről. Ez a cikk a szolgáltatásról.


## <a name="overview"></a>Áttekintés

Az Azure Migrate függőségmegjelenítés csoportok létrehozása a migrálás értékeléséhez megnövekedett magabiztosan teszi lehetővé. Függőségek képi megjelenítésének használatával megtekintheti az adott gép vagy gépek csoportjának hálózati függőségeket. Ez a funkció sem ahhoz, hogy hasznos vagy elveszett (vagy elfelejtett gépeket) a migrálási folyamat, amikor az alkalmazások és számítási feladatok futtatása a fájlrendszerek több gépen.  

## <a name="how-does-it-work"></a>Hogyan működik?

Azure Migrate az a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldás [Log Analytics](../log-analytics/log-analytics-overview.md) a függőségek képi megjelenítéséről.
- Az Azure Migrálási projekt létrehozásakor a Log Analytics-munkaterület jön létre az előfizetésében.
- A munkaterület nevét kötelező a migrálási projekt, előtaggal van ellátva a megadott név **áttelepítése –**, és igény szerint toldalékokkal számmal.
- Keresse meg a Log Analytics-munkaterületet a **Essentials** projekt **áttekintése** lapot.
- A létrehozott munkaterület a kulccsal van megjelölve **Migrálási projekt**, és értéket **projektnév**. Az Azure Portalon a keresés azt is használhatja.  

    ![Azure-beli monitorozási munkaterület](./media/concepts-dependency-visualization/oms-workspace.png)

Függőségmegjelenítést használ, meg kell töltse le és telepítse az ügynököt minden olyan elemezni szeretné a helyszíni gépen.  

## <a name="do-i-need-to-pay-for-it"></a>Kell fizetni?

Az Azure Migrate díjmentesen érhető el. Az Azure Migrate a függőségek képi megjelenítés funkciók használatát a Service Mapre van szükség. Az Azure Migrate-projektet hoz létre az Azure Migrate automatikusan létrehoz egy új Log Analytics-munkaterületet az Ön nevében.

> [!NOTE]
> A függőségmegjelenítési funkciót használja a Service Map Log Analytics-munkaterület-n keresztül. 2018. február 28 óta az Azure Migrate általános rendelkezésre állást, a közlemény a funkció már elérhető külön díjfizetés nélkül. Hozzon létre egy új projektet, győződjön meg arról, hogy kell használni az ingyenes használat munkaterület. Meglévő munkaterületek előtt általános availaibility chargable továbbra is, ezért azt javasoljuk, hogy helyezze át egy új projektet.

1. A Log Analytics-munkaterületen a Service Map kívül bármely megoldások használatát standard Log Analytics-díjat számolunk.
2. További költségek nélkül áttelepítési forgatókönyvek támogatása érdekében a Service Map megoldás nem számítunk fel díjakat a az Azure Migrate-projekt, amely után szabványos díjszabást alkalmazunk létrehozásától számított első 180 napban.
3. Csak a projekt létrehozásának részeként létrehozott munkaterület használható ingyenes lesz.

Ha regisztrálja az ügynököket a munkaterülethez, használja az Azonosítót és a kulcsot adja meg a projekt telepítése az ügynök lépések lapján. Nem lehet egy meglévő munkaterületet használja, és társíthatja azt az Azure Migrate-projekt.

Az Azure Migrate-projekt törlése esetén a munkaterület nem törlődik, együtt. Közzététel a projekt törlése, a Service Map használata nem ingyenes lesz, és minden egyes csomópont díját a fizetős szint a Log Analytics-munkaterület.

További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

## <a name="how-do-i-manage-the-workspace"></a>Hogyan kezelhetem a munkaterületet?

A Log Analytics-munkaterületet az Azure Migrate kívül is használhatja. Ha törli a migrálási projektben, ahol létrehozták nem törlődik. Ha már nincs szüksége a munkaterületen [törölné](../log-analytics/log-analytics-manage-access.md) manuálisan.

Ne törölje az Azure Migrate, által létrehozott munkaterület, hacsak nem törli a migrálási projektet. Ha így tesz, függőségek a nem várt módon működik.

## <a name="next-steps"></a>További lépések

[A gépek függőségeivel csoportosítása](how-to-create-group-machine-dependencies.md)
