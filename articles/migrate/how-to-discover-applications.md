---
title: Alkalmazások, szerepkörök és szolgáltatások felderítése helyszíni kiszolgálókon Azure Migrate
description: Ismerje meg, hogyan derítheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon Azure Migrate Server Assessment használatával.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: ae00e390bb49f3a54f7f7ce7d6491cf7ee882491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84770509"
---
# <a name="discover-machine-apps-roles-and-features"></a>Gépi alkalmazások, szerepkörök és szolgáltatások felderítése

Ez a cikk bemutatja, hogyan derítheti fel az alkalmazásokat, szerepköröket és szolgáltatásokat a helyszíni kiszolgálókon Azure Migrate: Server Assessment használatával.

A helyszíni gépeken futó alkalmazások, szerepkörök és szolgáltatások leltárának felderítése segít azonosítani és testre szabni az Azure-ba történő áttelepítési útvonalat a számítási feladatokhoz. Az App-Discovery a Azure Migrate berendezés használatával hajtja végre a felderítést a VM vendég hitelesítő adataival. Az App-Discovery ügynök nélküli. A virtuális gépeken nincs telepítve semmi.

> [!NOTE]
> Az alkalmazás-felderítés jelenleg előzetes verzióban érhető el a VMware virtuális gépek számára, és csak a felderítésre korlátozódik. Még nem kínálunk alkalmazás-alapú értékelést. 


## <a name="before-you-start"></a>Előkészületek

- Győződjön meg az alábbiakról:
    - [Létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
    - [Hozzáadta](how-to-assess.md) a Azure Migrate: kiszolgáló-értékelési eszközt egy projekthez.
- Tekintse át [az alkalmazás-felderítési támogatást és a követelményeket](migrate-support-matrix-vmware.md#vmware-requirements).
- Győződjön meg arról, hogy az App-Discovery alkalmazást futtató virtuális gépeken telepítve van a PowerShell 2,0-es vagy újabb verziója, és a VMware-eszközök (a 10.2.0-nél későbbiek) telepítve vannak.
- Az Azure Migrate berendezés telepítésére [vonatkozó követelmények](migrate-appliance.md) megtekintése.


## <a name="deploy-the-azure-migrate-appliance"></a>A Azure Migrate berendezés üzembe helyezése

1. [Tekintse át](migrate-appliance.md#appliance---vmware) az Azure Migrate berendezés üzembe helyezésének követelményeit.
2. Tekintse át azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati felhőkben](migrate-appliance.md#government-cloud-urls).
3. [Tekintse át](migrate-appliance.md#collected-data---vmware) a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-vmware.md#port-access-requirements) fel a port hozzáférési követelményeit a készülékhez.
5. [Telepítse a Azure Migrate készüléket](how-to-set-up-appliance-vmware.md) a felderítés elindításához. A készülék üzembe helyezéséhez le kell töltenie és importálnia kell egy PETESEJT-sablont a VMware-be a készülék VMware virtuális gépként való létrehozásához. Konfigurálja a készüléket, majd regisztrálja azt a Azure Migrate.
6. A berendezés üzembe helyezésekor a folyamatos felderítés elindításához a következőket kell megadnia:
    - Annak a vCenter Server a neve, amelyhez csatlakozni szeretne.
    - A készülékhez a vCenter Serverhoz való kapcsolódáshoz létrehozott hitelesítő adatok.
    - A készülékhez a Windows/Linux rendszerű virtuális gépekhez való kapcsolódáshoz létrehozott fiók hitelesítő adatai.

A készülék üzembe helyezése és a hitelesítő adatok megadása után a készülék elindítja a virtuális gépek metaadatait és teljesítményadatait, valamint az alkalmazások, szolgáltatások és szerepkörök felderítését.  Az alkalmazások felderítésének időtartama attól függ, hogy hány virtuális gép van. Általában egy órát vesz igénybe az 500 virtuális gépek alkalmazás-felderítése során.

## <a name="verify-permissions"></a>Engedélyek ellenőrzése

[Létrehozott egy vCenter Server írásvédett fiókot](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) a felderítéshez és értékeléshez. A csak olvasási jogosultsággal rendelkező fióknak engedélyezve kell lennie **Virtual Machines**  >  **vendég műveleteihez**, hogy együttműködjön a virtuális géppel az alkalmazások felderítése érdekében.

### <a name="add-the-user-account-to-the-appliance"></a>A felhasználói fiók hozzáadása a berendezéshez

Adja hozzá a felhasználói fiókot a következő módon:

1. Nyissa meg a berendezés-kezelő alkalmazást. 
2. Navigáljon az **adja meg a vCenter részletei** panelt.
3. Az **alkalmazás és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre.
3. Válassza ki az **operációs rendszert**, adjon meg egy felhasználóbarát nevet a fiókhoz, és a **Felhasználónév** / **jelszava**
6. Kattintson a **Save** (Mentés) gombra.
7. Kattintson **a Mentés gombra, és indítsa el a felderítést**.

    ![VM-felhasználói fiók hozzáadása](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>A leltár áttekintése és exportálása

Ha a felderítést követően megadták a hitelesítő adatokat az alkalmazás felderítéséhez, tekintse át és exportálja az alkalmazás leltárát a Azure Portal.

1. A **Azure Migrate-** Servers  >  **Azure Migrate: kiszolgáló értékelése**lapon kattintson a megjelenített darabszámra a **felderített kiszolgálók** lap megnyitásához.

    > [!NOTE]
    > Ezen a ponton lehetőség van a függőségi elemzések megadására is a felderített gépeken, így a függőségek megjeleníthetők a felmérni kívánt gépek között. [További](concepts-dependency-visualization.md) információ a függőségek elemzéséről.

2. A **felderített alkalmazásokban**kattintson a megjelenített darabszámra.
3. Az **alkalmazás leltárában**megtekintheti a felderített alkalmazásokat, szerepköröket és szolgáltatásokat.
4. A leltár exportálásához a **felderített kiszolgálók**területen kattintson az **alkalmazás leltározásának exportálása**lehetőségre.

Az alkalmazás leltára Excel-formátumban van exportálva és letöltve. Az **alkalmazás leltározási** lapja az összes gépen felderített alkalmazást jeleníti meg.

## <a name="next-steps"></a>További lépések

- [Felmérés létrehozása](how-to-create-assessment.md) a felderített kiszolgálókhoz.
- SQL Server adatbázisok értékelése [Azure Migrate használatával: adatbázis-értékelés](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
