---
title: Értékelés létrehozása Azure Migrate Server Assessment szolgáltatással | Microsoft Docs
description: Ismerteti, hogyan lehet értékelést létrehozni a Azure Migrate Server Assessment Tool eszközzel
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229101"
---
# <a name="create-an-assessment"></a>Értékelés létrehozása

Ez a cikk azt ismerteti, hogyan lehet értékelést létrehozni a helyszíni VMware virtuális gépekhez vagy a Hyper-V virtuális gépekhez Azure Migrate használatával: Kiszolgáló értékelése.

[Azure Migrate](migrate-services-overview.md) segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz. 

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Kiszolgáló-értékelési eszköz.
- Értékelés létrehozásához be kell állítania egy Azure Migrate készüléket a [VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md)rendszerhez. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld a Azure Migratenak: Kiszolgáló értékelése. [További információk](migrate-appliance.md).


## <a name="assessment-overview"></a>Értékelés áttekintése
Kétféle értékelést hozhat létre a Azure Migrate használatával: Kiszolgáló értékelése.

**Assessment** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: A CPU-és memória-kihasználtsági adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)** : A helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: A helyszíni virtuális gép méretétől függően<br/><br> **Ajánlott lemez típusa**: Az értékeléshez kiválasztott tárolási típus alapján.

[További](concepts-assessment-calculation.md) információ az értékelésekről.

## <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** lap **Azure Migratejában: Kiszolgáló-** értékelési csempe, kattintson az **értékelés**elemre.

    ![Értékelés](./media/how-to-create-assessment/assess.png)

2. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/how-to-create-assessment//view-all.png)

3. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
4. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
5. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/how-to-create-assessment//assessment-create.png)

6. Az értékelés létrehozása után tekintse meg a **kiszolgálók** > **Azure Migrate: Kiszolgáló-** értékelési > **felmérések**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: A virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: A virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségbecslés**: A lemezes tárolás becsült költségei az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  **kiszolgálóin** kattintson az ****értékelések** elemre Azure Migrateban: Kiszolgáló értékelése**.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Értékelés összegzése](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
2. A virtuális gép állapotának áttekintése:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretét és a becsült költségeket az értékelés során.
    - **Feltételekkel kész**: Megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra**: Megjeleníti a problémákat és a javasolt szervizelést.
    - **Készültség ismeretlen**: Akkor használatos, ha a Azure Migrate nem tudja értékelni a készültséget az adatelérhetőségi problémák miatt.

2. Kattintson egy **Azure** -készültségi állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
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
21%-40% | 2 csillag
41%-60% | 3 csillag
61%-80% | 4 csillag
81%-100% | 5 csillag




## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan hozhat létre nagy megbízhatóságú csoportokat a [függőségi leképezés](how-to-create-group-machine-dependencies.md) használatával.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
