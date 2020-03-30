---
title: Alkalmazások, szerepkörök és funkciók felfedezése a helyszíni kiszolgálókon az Azure Migrate szolgáltatással
description: Ismerje meg, hogyan fedezheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon az Azure Migrate Server Assessment segítségével.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453582"
---
# <a name="discover-machine-apps-roles-and-features"></a>Gépi alkalmazások, szerepkörök és funkciók felfedezése

Ez a cikk ismerteti, hogyan fedezheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon az Azure Migrate: Server Assessment használatával.

A helyszíni gépeken futó alkalmazások és szerepkörök/funkciók készletének felderítése segít azonosítani és megtervezni az Azure-ba a számítási feladatokhoz szabott áttelepítési útvonalat.

> [!NOTE]
> Az alkalmazásfelderítés jelenleg csak vmware virtuális gépek előzetes verzióban érhető el, és csak felderítésre korlátozódik. Még nem kínálunk alkalmazásalapú értékelést. A helyszíni VMware virtuális gépek, a Hyper-V vM-ek és a fizikai kiszolgálók gépalapú felmérése.

Alkalmazásfelderítés az Azure Migrate használatával: A kiszolgálóértékelése ügynök nélküli. Semmi sincs telepítve a gépekre és a virtuális gépekre. Server Assessment az Azure Migrate készüléket használja a gépvendég hitelesítő adataival együtt a felderítés végrehajtásához. A készülék távolról éri el a VMware gépeket vmware API-k használatával.


## <a name="before-you-start"></a>Előkészületek

1. Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
2. Győződjön meg arról, hogy [hozzáadta](how-to-assess.md) az Azure Migrate: Server Assessment eszközt a projekthez.
4. Tekintse meg a [VMware követelményeit](migrate-support-matrix-vmware.md#vmware-requirements) a VMware virtuális gépek felderítéséhez és értékeléséhez az Azure Migrate készülékkel.
5. Tekintse meg az Azure Migrate berendezés üzembe helyezésének [követelményeit.](migrate-appliance.md)
6. [Ellenőrizze az](migrate-support-matrix-vmware.md#application-discovery) alkalmazásfelderítés támogatását és követelményeit.

## <a name="prepare-for-app-discovery"></a>Felkészülés az alkalmazásfelderítésre

1. [Felkészülés a berendezés telepítésére](tutorial-prepare-vmware.md). A felkészülés magában foglalja a készülék beállításainak ellenőrzését, valamint egy fiók beállítását, amelyet a készülék a vCenter Server eléréséhez fog használni.
2. Győződjön meg arról, hogy rendelkezik egy felhasználói fiókkal (mindegyik Windows- és Linux-kiszolgálókhoz) rendszergazdai engedélyekkel azon gépekhez, amelyeken alkalmazásokat, szerepköröket és szolgáltatásokat szeretne felderíteni.
3. [Telepítse az Azure Migrate berendezést a](how-to-set-up-appliance-vmware.md) felderítés elindításához. A készülék üzembe helyezéséhez töltsön le és importáljon egy OVA-sablont a VMware-be, hogy a készüléket VMware vm-ként hozhatja létre. Konfigurálja a készüléket, majd regisztrálja az Azure Migrate.
2. A készülék üzembe helyezésekor a folyamatos felderítés elindításához adja meg a következőket:
    - Annak a vCenter-kiszolgálónak a neve, amelyhez csatlakozni szeretne.
    - A vCenter-kiszolgálóhoz való csatlakozáshoz a készülékhez létrehozott hitelesítő adatok.
    - A windowsos/linuxos virtuális gépekhez való csatlakozáshoz a készülékhez létrehozott fiókhitelesítő adatok.

Az eszköz üzembe helyezése és a hitelesítő adatok megadása után a készülék megkezdi a virtuális gép metaadatainak és teljesítményadatainak folyamatos felderítését, valamint az alkalmazások, a funkciók és a szerepkörök felderítését.  Az alkalmazásfelderítés időtartama attól függ, hogy hány virtuális gép van. Általában egy órát vesz igénybe az 500 virtuális gépek alkalmazásfelderítése.

## <a name="review-and-export-the-inventory"></a>A készlet áttekintése és exportálása

A felderítés befejezése után, ha megadta az alkalmazásfelderítéshez szükséges hitelesítő adatokat, áttekintheti és exportálhatja az alkalmazáskészletet az Azure Portalon.

1. Az **Azure Migrate – Servers** > **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** alkalmazásban kattintson a megjelenített számra a **Felderített kiszolgálók** lap megnyitásához.

    > [!NOTE]
    > Ebben a szakaszban is beállíthatja a felderített gépek függőségi leképezését, így a felmérni kívánt gépek függőségeit is megjelenítheti. [További információ](how-to-create-group-machine-dependencies.md).

2. A **felderített alkalmazások ban**kattintson a megjelenített számra.
3. Az **Alkalmazásleltárban**megtekintheti a felderített alkalmazásokat, szerepköröket és szolgáltatásokat.
4. A készlet exportálásához a **Felderített kiszolgálók**területen kattintson az **Alkalmazáskészlet exportálása parancsra.**

Az alkalmazásleltár exportálása és letöltése Excel formátumban történik. Az **alkalmazásleltár** lap megjeleníti az összes gépen felderített összes alkalmazást.

## <a name="next-steps"></a>További lépések

- A felderített kiszolgálók felemelése és átcsoportosítása [felmérésének létrehozása.](how-to-create-assessment.md)
- SQL Server-adatbázisok felmérése az [Azure Migrate: Adatbázis-felmérés](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)használatával.
