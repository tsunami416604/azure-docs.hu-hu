---
title: Értékelés létrehozása az Azure Áttelepítési kiszolgáló értékelésével | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként hozhat létre értékelést az Azure Áttelepítési kiszolgáló értékelése eszközzel
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229101"
---
# <a name="create-an-assessment"></a>Értékelés létrehozása

Ez a cikk ismerteti, hogyan hozhat létre egy értékelést a helyszíni VMware virtuális gépek vagy hyper-v vm-ek az Azure Migrate: Server Assessment.

[Az Azure Migrate](migrate-services-overview.md) segítségével áttelepülaz Azure-ba. Az Azure Migrate egy központi központot biztosít a helyszíni infrastruktúra, alkalmazások és adatok Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint külső független szoftverszállítói (ISV) ajánlatokhoz. 

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [hozzáadta](how-to-assess.md) az Azure Migrate: Server Assessment eszközt.
- Felmérés létrehozásához be kell állítania egy Azure Migrate-berendezést [a VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md)számára. A készülék felderíti a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld az Azure Migrate: Server Assessment szolgáltatásnak. [További információ](migrate-appliance.md).


## <a name="assessment-overview"></a>Értékelés áttekintése
Az Azure Migrate használatával kétféle értékelést hozhat létre: Kiszolgálói értékelés.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Összegyűjtött teljesítményadatokon alapuló értékelések | **Ajánlott virtuális gép mérete:** A PROCESSZOR és a memória kihasználtsági adatai alapján.<br/><br/> **Ajánlott lemeztípus (standard vagy prémium díjas felügyelt lemez)**: Az IOPS és a helyszíni lemezek átviteli hatása alapján.
**A helyszíni** | A helyszíni méretezésen alapuló értékelések. | **Ajánlott virtuális gép mérete:** A helyszíni virtuális gép mérete alapján<br/><br> **Ajánlott lemeztípus:** Az értékeléshez kiválasztott tárolási típusbeállítás alapján.

[További információ](concepts-assessment-calculation.md) az értékelésekről.

## <a name="run-an-assessment"></a>Értékelés futtatása

Végezze el az értékelést az alábbiak szerint:

1. Tekintse át az értékelések létrehozásának [ajánlott gyakorlatait.](best-practices-assessment.md)
2. A **Kiszolgálók** lap **Azure Migrate: Server Assessment (Kiszolgálói felmérés)** csempéjén kattintson a **Felértékelés gombra.**

    ![Kiértékelés](./media/how-to-create-assessment/assess.png)

2. A **Kiszolgálók felmérése**területen adja meg az értékelés nevét.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelési tulajdonságok](./media/how-to-create-assessment//view-all.png)

3. A **Csoport kijelölése vagy létrehozása**csoportban válassza az Új **létrehozása**lehetőséget, és adja meg a csoport nevét. Egy csoport összegyűjti egy vagy több virtuális gépek együtt értékelésre.
4. A **Gépek hozzáadása a csoporthoz**csoportban válassza a csoporthoz hozzáadni kívánt virtuális gépek lehetőséget.
5. Kattintson **az Értékelés létrehozása** a csoport létrehozásához, és futtassa az értékelést.

    ![Értékelés létrehozása](./media/how-to-create-assessment//assessment-create.png)

6. Az értékelés létrehozása után tekintse meg a **Kiszolgálók** > **Azure Áttelepítés: Kiszolgálóértékelési** > **értékelések**című részben.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készenlét:** Függetlenül attól, hogy a virtuális gépek alkalmasak-e az Azure-ba való migráláshoz.
- **Havi költségbecslés:** A virtuális gépek Azure-beli futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költség becslés:** Az áttelepítés utáni lemeztárolás becsült költségei.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  **kiszolgálói között**kattintson az Értékelések **az** **Azure Áttelepítés: Kiszolgálói értékelés**elemre.
2. Az **Értékelések menüben**kattintson egy értékelésre a megnyitásához.

    ![Az értékelés összefoglalója](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure-ra való felkészültség áttekintése

1. Az **Azure-ban készenléti**ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való migrálásra.
2. Tekintse át a virtuális gép állapotát:
    - **Készen áll az Azure-ra:** Az Azure Migrate a virtuális gépek méretének és költségbecslésének ajánlott a felmérésben.
    - **Kész a feltételekkel:** Problémák és javasolt szervizelés megjelenítése.
    - **Nem áll készen az Azure-ra:** Problémák és javasolt szervizelés megjelenítése.
    - **Készenlét ismeretlen:** Akkor használatos, ha az Azure Migrate nem tudja felmérni a készenlétet az adatok rendelkezésre állásával kapcsolatos problémák miatt.

2. Kattintson egy **Azure-készenléti** állapotra. Megtekintheti a virtuális gépek készenléti részleteit, és leáshat a virtuális gép részleteinek megtekintéséhez, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>Költségrészletek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségét jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítve vannak az értékelt csoport összes virtuális gépénél.

    - A költségbecslések a gép méretre vonatkozó javaslatain, valamint lemezein és tulajdonságain alapulnak.
    - A számítási és tárolási becsült havi költségek láthatók.
    - A költségbecslés a helyszíni virtuális gépek iaaS virtuális gépekként való futtatásához. Az Azure Áttelepítési kiszolgáló értékelése nem veszi figyelembe a PaaS- vagy SaaS-költségeket.

2. Megtekintheti a havi tárolási költségbecsléseket. Ez a nézet az értékelt csoport összesített tárolási költségeit mutatja, különböző típusú tárolólemezekre osztva.
3. Részletezheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Teljesítményalapú értékelések futtatásakor megbízhatósági minősítés van rendelve az értékeléshez.

![Megbízhatósági minősítés](./media/how-to-create-assessment/confidence-rating.png)

- Az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést kapja.
- A megbízhatósági besorolás segít megbecsülni az értékelés által biztosított méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

Az értékelés megbízhatósági minősítései a következők.

**Az adatpont elérhetősége** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag




## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [függőségi leképezést](how-to-create-group-machine-dependencies.md) magas megbízhatósági csoportok létrehozásához.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
