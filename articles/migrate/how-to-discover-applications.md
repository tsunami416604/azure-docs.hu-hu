---
title: Alkalmazások, szerepkörök és szolgáltatások felderítése helyszíni kiszolgálókon Azure Migrate
description: Ismerje meg, hogyan derítheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon Azure Migrate Server Assessment használatával.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744616"
---
# <a name="discover-machine-apps-roles-and-features"></a>Gépi alkalmazások, szerepkörök és szolgáltatások felderítése

Ez a cikk bemutatja, hogyan derítheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon Azure Migrate: Server Assessment használatával.

Az alkalmazások leltárának, valamint a helyszíni gépeken futó szerepköröknek/funkcióknak a felderítése révén azonosíthatók és megtervezhető az Azure-ba irányuló áttelepítési útvonal, amelyet a számítási feladatokhoz alakítottak ki.

> [!NOTE]
> Az alkalmazás-felderítés jelenleg előzetes verzióban érhető el a VMware virtuális gépek számára, és csak a felderítésre korlátozódik. Még nem kínálunk alkalmazás-alapú értékelést. Gépi alapú értékelés a helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók számára.

Alkalmazások felderítése Azure Migrate használatával: a kiszolgáló értékelése ügynök nélkül történik. Nincs telepítve a gépeken és a virtuális gépeken. A kiszolgáló értékelése a Azure Migrate berendezés használatával hajtja végre a felderítést a számítógép vendég hitelesítő adataival együtt. A készülék távolról éri el a VMware-gépeket a VMware API-k használatával.


## <a name="before-you-start"></a>Előkészületek

1. Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
2. Győződjön meg arról, hogy [hozzáadta](how-to-assess.md) a Azure Migrate: Server Assessment eszközt egy projekthez.
4. A VMWare virtuális gépeknek a Azure Migrate berendezéssel való felfedésére és értékelésére vonatkozó [VMware-követelmények](migrate-support-matrix-vmware.md#vmware-requirements) ellenőrzése.
5. Az Azure Migrate berendezés telepítésére [vonatkozó követelmények](migrate-appliance.md) megtekintése.
6. Ellenőrizze az alkalmazások felderítésének [támogatását és követelményeit](migrate-support-matrix-vmware.md#application-discovery) .



## <a name="deploy-the-azure-migrate-appliance"></a>A Azure Migrate berendezés üzembe helyezése

1. [Tekintse át](migrate-appliance.md#appliance---vmware) az Azure Migrate berendezés üzembe helyezésének követelményeit.
2. Tekintse át azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati felhőkben](migrate-appliance.md#government-cloud-urls).
3. [Tekintse át](migrate-appliance.md#collected-data---vmware) a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-vmware.md#port-access) fel a port hozzáférési követelményeit a készülékhez.
5. [Telepítse a Azure Migrate készüléket](how-to-set-up-appliance-vmware.md) a felderítés elindításához. A készülék üzembe helyezéséhez le kell töltenie és importálnia kell egy PETESEJT-sablont a VMware-be a készülék VMware virtuális gépként való létrehozásához. Konfigurálja a készüléket, majd regisztrálja azt a Azure Migrate.
6. A berendezés üzembe helyezésekor a folyamatos felderítés elindításához a következőket kell megadnia:
    - Annak a vCenter Server a neve, amelyhez csatlakozni szeretne.
    - A készülékhez a vCenter Serverhoz való kapcsolódáshoz létrehozott hitelesítő adatok.
    - A készülékhez a Windows/Linux rendszerű virtuális gépekhez való kapcsolódáshoz létrehozott fiók hitelesítő adatai.

A készülék üzembe helyezése és a hitelesítő adatok megadása után a készülék elindítja a virtuális gépek metaadatait és teljesítményadatait, valamint az alkalmazások, szolgáltatások és szerepkörök felderítését.  Az alkalmazások felderítésének időtartama attól függ, hogy hány virtuális gép van. Általában egy órát vesz igénybe az 500 virtuális gépek alkalmazás-felderítése során.

## <a name="prepare-a-user-account"></a>Felhasználói fiók előkészítése

Hozzon létre egy, a felderítéshez használandó fiókot, és adja hozzá a berendezéshez.

### <a name="create-a-user-account-for-discovery"></a>Felhasználói fiók létrehozása a felderítéshez

Hozzon létre egy felhasználói fiókot, hogy a kiszolgáló értékelése hozzáférhessen a virtuális géphez a felderítéshez. [További](migrate-support-matrix-vmware.md#application-discovery) információ a fiókokra vonatkozó követelményekről.


### <a name="add-the-user-account-to-the-appliance"></a>A felhasználói fiók hozzáadása a berendezéshez

Adja hozzá a felhasználói fiókot a készülékhez.

1. Nyissa meg a berendezés-kezelő alkalmazást. 
2. Navigáljon az **adja meg a vCenter részletei** panelt.
3. Az **alkalmazás és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre.
3. Válassza ki az **operációs rendszert**, adjon meg egy felhasználóbarát nevet a fiókhoz, és a **Felhasználónév**/**jelszava**
6. Kattintson a **Save** (Mentés) gombra.
7. Kattintson **a Mentés gombra, és indítsa el a felderítést**.

    ![VM-felhasználói fiók hozzáadása](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>A leltár áttekintése és exportálása

Ha a felderítést követően megadták a hitelesítő adatokat az alkalmazás felderítéséhez, tekintse át és exportálja az alkalmazás leltárát a Azure Portal.

1. A **Azure Migrate-** > Servers**Azure Migrate: kiszolgáló értékelése**lapon kattintson a megjelenített darabszámra a **felderített kiszolgálók** lap megnyitásához.

    > [!NOTE]
    > Ezen a ponton a függőségi leképezést is beállíthatja a felderített gépekhez, így a függőségeket a felmérni kívánt gépek között jelenítheti meg. [További információ](how-to-create-group-machine-dependencies.md).

2. A **felderített alkalmazásokban**kattintson a megjelenített darabszámra.
3. Az **alkalmazás leltárában**megtekintheti a felderített alkalmazásokat, szerepköröket és szolgáltatásokat.
4. A leltár exportálásához a **felderített kiszolgálók**területen kattintson az **alkalmazás leltározásának exportálása**lehetőségre.

Az alkalmazás leltára Excel-formátumban van exportálva és letöltve. Az **alkalmazás leltározási** lapja az összes gépen felderített alkalmazást jeleníti meg.

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy értékelést](how-to-create-assessment.md) a felderített kiszolgálók áttelepítésének feloldásához.
- SQL Server adatbázisok értékelése a [Azure Migrate használatával: adatbázis-értékelés](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
