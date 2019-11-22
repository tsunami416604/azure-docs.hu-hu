---
title: A helyszíni kiszolgálókra telepített alkalmazások, szerepkörök és szolgáltatások felderítése Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan lehet alkalmazásokat, szerepköröket és szolgáltatásokat felderíteni a helyszíni kiszolgálókon Azure Migrate Server Assessment használatával.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 11/20/2019
ms.author: snehaa
ms.openlocfilehash: 279e326ace308b354d7bcb8366d3286980e7b8c6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278476"
---
# <a name="discover-machine-apps-roles-and-features"></a>Gépi alkalmazások, szerepkörök és szolgáltatások felderítése

Ez a cikk bemutatja, hogyan derítheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon Azure Migrate: Server Assessment használatával.

Az alkalmazások leltárának, valamint a helyszíni gépeken futó szerepköröknek/funkcióknak a felderítése révén azonosíthatók és megtervezhető az Azure-ba irányuló áttelepítési útvonal, amelyet a számítási feladatokhoz alakítottak ki.

> [!NOTE]
> Az alkalmazások felderítése jelenleg csak a VMware virtuális gépek esetében támogatott, és csak felderítésre korlátozódik. Még nem kínálunk alkalmazás-alapú értékelést.  A kiszolgáló értékelése jelenleg a helyszíni VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat vizsgálja a gépi szinten, a kiemelési és eltolási áttelepítések esetén.

Alkalmazások felderítése Azure Migrate használatával: a kiszolgáló értékelése ügynök nélkül történik. Semmit nem kell telepíteni a gépekre és a virtuális gépekre. A kiszolgáló értékelése a Azure Migrate berendezés használatával hajtja végre a felderítést a számítógép vendég hitelesítő adataival együtt. A készülék távolról éri el a VMware-gépeket a VMware API-k használatával.


## <a name="before-you-start"></a>Előkészületek

1. Tekintse át az alkalmazás szintű felderítés [támogatási korlátozásait](migrate-support-matrix-vmware.md#application-discovery) .
2. Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
3. Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Server Assessment eszközt.
4. A VMWare virtuális gépeknek a Azure Migrate berendezéssel való felfedésére és értékelésére vonatkozó [VMware-követelmények](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) ellenőrzése.
4. Az Azure Migrate berendezés telepítésére [vonatkozó követelmények](migrate-support-matrix-vmware.md#assessment-appliance-requirements) megtekintése.

## <a name="prepare-for-app-discovery"></a>Az alkalmazások felderítésének előkészítése

1. [Felkészülés a berendezések üzembe helyezésére](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware). Az előkészítés magában foglalja a berendezés beállításainak ellenőrzését, valamint egy olyan fiók beállítását, amelyet a készülék a vCenter Server eléréséhez használ majd.
2. Győződjön meg arról, hogy rendelkezik egy felhasználói fiókkal (amely a Windows-és Linux-kiszolgálókon van), és rendszergazdai jogosultságokkal rendelkezik azokon a gépeken, amelyeken alkalmazásokat, szerepköröket és szolgáltatásokat kíván felderíteni.
3. [Telepítse a VMware készüléket](how-to-set-up-appliance-vmware.md) a felderítés elindításához. A készülék üzembe helyezéséhez le kell töltenie és importálnia kell egy PETESEJT-sablont a VMware-be a készülék VMware virtuális gépként való létrehozásához. Konfigurálja a készüléket, majd regisztrálja azt a Azure Migrate.
2. A berendezés üzembe helyezésekor a folyamatos felderítés elindításához a következőket kell megadnia:
    - Annak a vCenter Server a neve, amelyhez csatlakozni szeretne.
    - A készülékhez a vCenter Serverhoz való kapcsolódáshoz létrehozott hitelesítő adatok.
    - A készülékhez a Windows/Linux rendszerű virtuális gépekhez való kapcsolódáshoz létrehozott fiók hitelesítő adatai.

A készülék üzembe helyezése és a hitelesítő adatok megadása után a készülék elindítja a virtuális gépek metaadatait és teljesítményadatait, valamint az alkalmazások, szolgáltatások és szerepkörök felderítését.  Az alkalmazások felderítésének időtartama attól függ, hogy hány virtuális gép van. Általában egy órát vesz igénybe az 500 virtuális gépek alkalmazás-felderítése során.

## <a name="review-and-export-the-inventory"></a>A leltár áttekintése és exportálása

Ha a felderítés befejeződése után megadták a hitelesítő adatokat az alkalmazás felderítéséhez, tekintse át és exportálja az alkalmazás leltárát a Azure Portal.

1. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése**lapon kattintson a megjelenített darabszámra a **felderített kiszolgálók** lap megnyitásához.

    > [!NOTE]
    > Ezen a ponton a függőségi leképezést is beállíthatja a felderített gépekhez, így a függőségeket a felmérni kívánt gépek között jelenítheti meg. [További információ](how-to-create-group-machine-dependencies.md).

2. A **felderített alkalmazásokban**kattintson a megjelenített darabszámra.
3. Az **alkalmazás leltárában**megtekintheti a felderített alkalmazásokat, szerepköröket és szolgáltatásokat.
4. A leltár exportálásához a **felderített kiszolgálók**területen kattintson az **alkalmazás leltározásának exportálása**lehetőségre.

Az alkalmazás leltára Excel-formátumban van exportálva és letöltve. Az **alkalmazás leltározási** lapja az összes gépen felderített alkalmazást jeleníti meg.

## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy értékelést](how-to-create-assessment.md) a felderített kiszolgálók áttelepítésének feloldásához.
- SQL Server adatbázisok értékelése a [Azure Migrate használatával: adatbázis-értékelés](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
