---
title: AVS-Értékelés létrehozása Azure Migrate Server Assessment szolgáltatással | Microsoft Docs
description: Leírja, hogyan lehet AVS-értékelést létrehozni a Azure Migrate Server Assessment Tool eszközzel
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: 89dc962c73a6446afc79eec2a0fcfd5c0e32e84e
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576710"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware-megoldás (AVS) értékelésének létrehozása

Ez a cikk azt ismerteti, hogyan hozható létre Azure VMware-megoldás (AVS) felmérés a helyszíni VMware virtuális gépekhez a Azure Migrate: Server Assessment használatával.

[Azure Migrate](migrate-services-overview.md) segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Server Assessment eszközt.
- Az értékelés létrehozásához létre kell hoznia egy Azure Migrate készüléket a [VMware](how-to-set-up-appliance-vmware.md)-hez, amely felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld a kiszolgálónak Azure Migrate: Server Assessment. [További információ](migrate-appliance.md).
- [A kiszolgálói metaadatokat](tutorial-assess-import.md) vesszővel tagolt (CSV) formátumban is importálhatja.


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Az Azure VMware-megoldás (AVS) értékelésének áttekintése

A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V virtuális gépeket](how-to-set-up-appliance-hyper-v.md)és a [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) felhasználhatja az Azure-ba való áttelepítéshez ezzel az értékelési típussal. [További információ](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> Az Azure VMware Solutionbe (AVS-be) történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md).[További információ](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Az Azure VMware Solution (AVS) felmérés jelenleg előzetes verzióban érhető el, és csak VMware virtuális gépekhez hozható létre.


Az Azure VMware Solution (AVS) felmérések létrehozásához két típusú méretezési feltétel használható:

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A helyszíni virtuális gépek összegyűjtött teljesítményadatok alapján végzett értékelések. | **Ajánlott csomópont mérete**: a CPU és a memória kihasználtsági adatai, valamint a csomópont típusa, a tárolási típus és a pénzügyi tranzakciós beállítás alapján, amelyet az értékeléshez választott.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott csomópont mérete**: a helyszíni virtuális gép méretétől, valamint a csomópont típusától, a tárolási típustól és az értékeléshez kiválasztott tranzakciós beállítástól függően.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware-megoldás (AVS) értékelésének futtatása

Futtassa az Azure VMware-megoldás (AVS) értékelését az alábbiak szerint:

1. Tekintse át az értékelések létrehozásának [ajánlott eljárásait](best-practices-assessment.md).

2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén kattintson az **értékelés**elemre.

    ![A képernyőképen Azure Migrate-kiszolgálók láthatók a kiértékelési eszközök területen.](./media/how-to-create-assessment/assess.png)

3. A **kiszolgálók értékelése**területen válassza ki az értékelés típusát "Azure VMware Solution (AVS)" néven, válassza ki a felderítés forrását, és adja meg az értékelés nevét.

    ![A kiértékelés alapjai](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![AVS Assessment-tulajdonságok](./media/how-to-create-avs-assessment/avs-view-all.png)

5. Kattintson a **Tovább** gombra az **értékelni kívánt gépek kiválasztásához**. A **Csoport kiválasztása vagy létrehozása** területen válassza az **Új létrehozása** lehetőséget, majd adja meg a csoport nevét. A csoport egy vagy több virtuális gépet foglal magába a kiértékeléshez.

6. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.

7. Kattintson a **Tovább** gombra a **Felülvizsgálat + létrehozás** területre lépéshez, a kiértékelés részleteinek áttekintéséhez.

8. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![AVS-felmérés létrehozása](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. A kiértékelés létrehozása után megtekintheti a **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** > **Értékelések** területen.

10. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure-beli VMware-megoldás (AVS) értékelésének áttekintése

Az Azure VMware-megoldás (AVS) értékelése a következőket ismerteti:

- **Azure VMware-megoldás (AVS) készültsége**: azt határozza meg, hogy a helyszíni virtuális gépek alkalmasak-e az Azure VMware-megoldásra (AVS) való áttelepítésre.
- **AVS-csomópontok száma**: a virtuális gépek futtatásához szükséges AVS-csomópontok becsült száma.
- **Használat az AVS-csomópontok között**: a processzor, a memória és a tárhely kihasználtsága az összes csomóponton keresztül.
- **Havi költségbecslés**: a helyszíni virtuális gépeket futtató összes Azure VMware Solution (AVS) csomópont becsült havi költségei.


### <a name="view-an-assessment"></a>Kiértékelés megtekintése

1. Az **áttelepítési célok**  >   **kiszolgálóin**kattintson az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemre.

2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![AVS Assessment – összefoglalás](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Az Azure VMware-megoldás (AVS) készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az AVS-re való áttelepítésre.

2. A virtuális gép állapotának áttekintése:
    - **AVS-re kész**: a gép áttelepíthető az Azure-ba (AVS) módosítás nélkül. A teljes AVS-támogatással indul az AVS-ben.
    - Felmerülő **feltételek**: a virtuális gép kompatibilitási problémákba ütközhet a jelenlegi vSphere-verzióval, valamint az esetlegesen vmware-eszközök és egyéb beállítások megkövetelésével, mielőtt a virtuális gép teljes funkcionalitása elérhető legyen az AVS-ben.
    - **Nem áll készen az AVS-re**: a virtuális gép nem indul el az AVS-ben. Ha például a helyszíni VMware virtuális gép rendelkezik külső eszközzel, például egy CD-ROM-meghajtóval, a VMotion művelet sikertelen lesz (ha a VMware VMotiont használja).
    - **Felkészültség ismeretlen**: Azure Migrate nem tudta megállapítani a gép készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

3. Tekintse át a javasolt eszközt:
    - **VMware HCX vagy Enterprise**: VMware-es gépek esetén a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Ismeretlen**: A CSV-fájllal importált gépek esetében az alapértelmezett migrálási eszköz ismeretlen. A VMware-gépek esetében azonban javasolt a VMware Hybrid Cloud Extension (HCX) megoldás használata. 

4. Kattintson egy **AVS-készültségi** állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>Költségadatok áttekintése

Ez a nézet a virtuális gépek Azure VMware-megoldásban (AVS) való futtatásának becsült költségeit jeleníti meg.

1. Tekintse át a havi teljes költséget. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban. 

    - A költségbecslés az összes virtuális gép erőforrás-követelményeit figyelembe véve szükséges AVS-csomópontok számától függ.
    - Mivel az Azure VMware-megoldás (AVS) díjszabása csomópontos, a teljes költség nem rendelkezik számítási költséggel és a tárolási költség eloszlásával.
    - A költségbecslés a helyszíni virtuális gépek AVS-ben való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.
    
2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.

3. A részletezéssel megtekintheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/how-to-create-assessment/confidence-rating.png)

- A rendszer kinyeri az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést.
- A megbízhatósági minősítés segít megbecsülni az értékelés által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.
- A teljesítmény-alapú méretezés érdekében a kiszolgáló-értékeléshez tartozó AVS-értékeléseknek szüksége van a processzor és a virtuális gép memóriájának kihasználtsági adataira. A rendszer a következő teljesítményadatokat gyűjti, de nem használja az AVS-értékelések méretezési javaslataiban:
  - A lemez a virtuális géphez csatolt minden lemez IOPS és adatátviteli adatokkal rendelkezik.
  - A hálózati I/O-t a virtuális géphez csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

Az értékelés megbízhatósági minősítése a következő.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](concepts-azure-vmware-solution-assessment-calculation.md) információ a teljesítményadatok szolgáltatásról 


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan hozhat létre nagy megbízhatóságú csoportokat a [függőségi leképezés](how-to-create-group-machine-dependencies.md) használatával.
- [További](concepts-azure-vmware-solution-assessment-calculation.md) információ az AVS-értékelések kiszámításáról.
