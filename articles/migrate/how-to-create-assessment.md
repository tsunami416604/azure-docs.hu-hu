---
title: Azure-beli virtuális gép értékelésének létrehozása Azure Migrate Server Assessment szolgáltatással | Microsoft Docs
description: Útmutató Azure-beli virtuális gépek értékelésének létrehozásához a Azure Migrate Server Assessment Tool eszközzel
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: c4132ca675af136d7fd50b8ddd02277919a5ed28
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361092"
---
# <a name="create-an-azure-vm-assessment"></a>Azure-beli virtuális gép felmérésének létrehozása

Ez a cikk bemutatja, hogyan hozhat létre Azure-beli virtuálisgép-értékelést helyszíni VMware virtuális gépek vagy Hyper-V virtuális gépek számára a Azure Migrate: Server Assessment használatával.

[Azure Migrate](migrate-services-overview.md) segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz. 

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Server Assessment eszközt.
- Értékelés létrehozásához be kell állítania egy Azure Migrate készüléket a [VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md)rendszerhez. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld Azure Migratenak: a kiszolgáló értékelését. [További információ](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Az Azure VM Assessment áttekintése
Kétféle méretezési feltétel használható az Azure-beli virtuális gépek értékelésének Azure Migrate: Server Assessment használatával történő létrehozásához.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)**: a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a helyszíni virtuális gép méretétől függően<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.

[További](concepts-assessment-calculation.md) információ az értékelésekről.

## <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásának [ajánlott eljárásait](best-practices-assessment.md).
2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén kattintson az **értékelés**elemre.

    ![A képernyőképen Azure Migrate-kiszolgálók láthatók a kiértékelési eszközök területen.](./media/how-to-create-assessment/assess.png)

3. A **kiszolgálók értékelése**területen válassza ki az értékelés típusát "Azure VM" néven, válassza ki a felderítés forrását, és adja meg az értékelés nevét.

    ![A kiértékelés alapjai](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Kiértékelés tulajdonságai](./media/how-to-create-assessment//view-all.png)

5. Kattintson a **Tovább** gombra az **értékelni kívánt gépek kiválasztásához**. A **Csoport kiválasztása vagy létrehozása** területen válassza az **Új létrehozása** lehetőséget, majd adja meg a csoport nevét. A csoport egy vagy több virtuális gépet foglal magába a kiértékeléshez.
6. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
7. Kattintson a **Tovább** gombra a **Felülvizsgálat + létrehozás** területre lépéshez, a kiértékelés részleteinek áttekintéséhez.
8. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/how-to-create-assessment//assessment-create.png)

9. A kiértékelés létrehozása után megtekintheti a **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** > **Értékelések** területen.
10. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-azure-vm-assessment"></a>Azure-beli virtuális gép kiértékelésének áttekintése

Az Azure-beli virtuális gépek kiértékelése a következőket tartalmazza:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: A lemezes tárolás becsült költségei a migrálás után.

### <a name="view-an-azure-vm-assessment"></a>Azure-beli virtuális gépek felmérésének megtekintése

1. Az **áttelepítési célok**  >   **kiszolgálóin**kattintson az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemre.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Kiértékelés összegzése](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure-kompatibilitás áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
2. A virtuális gép állapotának áttekintése:
    - **Azure-beli használatra kész**: Az Azure Migrate javaslatot tesz a virtuálisgép-méretre, illetve költségbecslést ad az értékelésben szereplő virtuális gépekhez.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

3. Kattintson egy **Azure-készültségi** állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>Költségadatok áttekintése

Ez a nézet a virtuális gépek az Azure-ban való futtatásával kapcsolatos becsült számítási és tárolási költséget mutatja.

1. A havi számítási és tárolási költségek áttekintése. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslések a gépre, valamint annak lemezeire és tulajdonságaira vonatkozó méretjavaslatokon alapulnak.
    - A becsült havi számítási és tárolási költségek jelennek meg.
    - A költségbecslés a helyszíni virtuális gépek IaaS virtuális gépekként való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.
3. A részletezéssel megtekintheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/how-to-create-assessment/confidence-rating.png)

- A rendszer kinyeri az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést.
- A megbízhatósági minősítés segít megbecsülni az értékelés által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

Az értékelés megbízhatósági minősítése a következő.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag




## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre nagy megbízhatóságú csoportokat a [függőségi leképezés](how-to-create-group-machine-dependencies.md) használatával.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
