---
title: "A feladatátvétel és a sikertelen biztonsági Azure virtuális gépek replikálása egy másodlagos Azure-régió, az Azure Site Recovery (előzetes verzió)"
description: "Útmutató a feladatátvétel, és egy másodlagos Azure-régió, az Azure Site Recovery hátsó Azure virtuális gépek replikáció sikertelen"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5f37a7b3534102a06f6d5dc6fb91d3abee7c0522
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>A feladatátvétel, és vissza Azure virtuális gépek között (előzetes verzió) Azure-régiók sikertelen

A [Azure Site Recovery](../site-recovery-overview.md) szolgáltatás vész-helyreállítási stratégiát infrastruktúrája azzal segíti a kezelése és koordinálása replikációjának, feladatátvételének és feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Ez az oktatóanyag egy Azure virtuális egy másodlagos Azure régióra feladatátvételt ismerteti. Miután keresztül korábban meghiúsult, hogy visszaadják feladataikat az elsődleges régióban a rendelkezésre álló. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A feladatátvétel az Azure virtuális gép
> * Adja meg a másodlagos Azure virtuális gép újra védelmét, hogy az elsődleges régióban replikált
> * A feladat-visszavételt a másodlagos virtuális gép
> * Állítsa vissza a másodlagos régióba az elsődleges virtuális gép

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy végrehajtotta a [vész-helyreállítási részletezési](azure-to-azure-tutorial-dr-drill.md) ellenőrizze, hogy minden a várt módon működik.
- Ellenőrizze a virtuális gép tulajdonságait, a feladatátvételi teszt futtatása előtt. A virtuális gép meg kell felelnie [Azure-követelményeknek](../site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="run-a-failover-to-the-secondary-region"></a>A másodlagos régióba történő feladatátvételt

1. A **replikált elemek**, válassza ki a virtuális Gépet, a feladatátvétel kívánt > **feladatátvételi**

   ![Feladatátvétel](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. A **feladatátvételi**, jelölje be a **helyreállítási pont** feladatokat. Az alábbi lehetőségek egyikét használhatja:

   * **Legújabb** (alapértelmezett): Ez a beállítás dolgozza fel a Site Recovery szolgáltatásban lévő összes adathoz, és a legalacsonyabb helyreállítási célkitűzés (RPO) biztosít.
   * **Legújabb feldolgozott**: Ez a beállítás visszaállítja a virtuális gép a legutóbbi helyreállítási pontot, a Site Recovery szolgáltatás által feldolgozott.
   * **Egyéni**: használja ezt a beállítást egy adott helyreállítási pont a feladatátvételt. Ez a beállítás akkor hasznos, a feladatátvételi teszt végrehajtásához.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen.

4. A feladatátvételi folyamat előrehaladásának kövesse a **feladatok** lap.

5. A feladatátvétel után ellenőrizze a virtuális gép hozzá van bejelentkezve. Ha a virtuális gép egy másik helyreállítási pont Ugrás, akkor használhatja **helyreállítási pont módosítása** lehetőséget.

6. Ha elégedett a feladatokat átadó virtuális gép is **véglegesítése a határidő** a feladatátvételt.
   A szolgáltatás elérhető összes helyreállítási pont véglegesítése törli. A **helyreállítási pont módosítása** beállítás már nem érhető el.

## <a name="reprotect-the-secondary-vm"></a>Állítsa a másodlagos virtuális gép

A virtuális gépek feladatátvétel után kell szüntetnie, hogy az elsődleges régióban vissza replikált.

1. Győződjön meg arról, hogy a virtuális Gépet a **feladatátvétel véglegesítve lett** kerül, és ellenőrizze, hogy az elsődleges régióban érhető el, és elvégezheti létrehozásához, és azt az új erőforrások elérését.
2. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a virtuális Gépet, amely a feladatátvétel megtörtént, és válassza **védelmének újbóli beállításához**.

   ![Kattintson a jobb gombbal a lássa el újból védelemmel](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Figyelje meg, hogy az elsődleges régióban, másodlagos védelem irányának már be van jelölve.
3. Tekintse át a **erőforráscsoport, hálózati, tárolási és rendelkezésre állási készletek** információkat. Bármely megjelölt (új) erőforrások a védelem-újrabeállítási művelet részeként jönnek létre.
4. Kattintson a **OK** elindítható egy védelem-újrabeállítási feladat. Ez a feladat magok a célhelyhez, a legújabb adatokkal. Ezt követően az eltérések replikált az elsődleges régióban. A virtuális gép már védett állapotban.

## <a name="fail-back-to-the-primary-region"></a>Visszaadják feladataikat az elsődleges régióban

Után a virtuális gépek vannak látható el újra védelemmel, akkor is nem vissza az elsődleges régióban szükség van. Ehhez kövesse a [feladatátvételi](#run-a-failover) utasításokat.
