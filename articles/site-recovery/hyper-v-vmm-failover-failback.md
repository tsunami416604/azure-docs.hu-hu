---
title: "A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek replikálása egy másodlagos adatközpontba Site Recovery szolgáltatással |} Microsoft Docs"
description: "Megtudhatja, hogyan Hyper-V virtuális gépek feladatátvétele a másodlagos helyszíni helyre, és vissza az elsődleges hely, az Azure Site Recovery sikertelen"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: raynew
ms.openlocfilehash: 3740ec9917499f6a1e87905befe86598a18f68e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>A feladatátvétel, és sikertelen lesz a másodlagos helyszíni helyre replikált Hyper-V virtuális gépek biztonsági

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeli, és koordinálja a replikációt, a feladatátvételi és a feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

A cikk ismerteti, hogy áthelyezze a feladatokat a Hyper-V virtuális gépek kezelése a System Center Virtual Machine Manager (VMM) felhő, egy másodlagos VMM-helyre. A feladatátvétel után visszaadja a feladatokat a helyszíni helyre, amint az elérhetővé válik. Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Feladatok átadása a Hyper-V virtuális gépek egy elsődleges felhőből a VMM egy másodlagos VMM-felhő
> * Állítsa a másodlagos hely az elsődleges és a feladat-visszavételt
> * Opcionálisan a replikálása az elsődleges másodlagos újra elindítani

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

A feladatátvételi és a feladat-visszavétel rendelkezik három fázisból áll:

1. **Feladatok átadása a másodlagos hely**: feladatátvételt gépeket az elsődleges helyről másodlagos.
2. **A másodlagos helyről sikertelen**: virtuális gépek replikálása az elsődleges a másodlagos, és futtassa a tervezett feladatátvételt a feladat-visszavételt.
3. A tervezett feladatátvétel után opcionálisan replikálást indítani az elsődleges helyről a másodlagos újra.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy befejezte a [vész-helyreállítási részletezési](hyper-v-vmm-test-failover.md) ellenőrizze, hogy minden a várt módon működik.
- Feladat-visszavétel befejezése, győződjön meg arról, hogy az elsődleges és másodlagos VMM-kiszolgáló csatlakozik-e a Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Futtassa a feladatátvétel az elsődleges másodlagos

Hyper-V virtuális gépek futtatása rendszeres vagy tervezett feladatátvétel.

- Váratlan leállás rendszeres feladatátvevő használja. Ennél a feladatátvételnél futtatásakor a Site Recovery hoz létre egy virtuális Gépet a másodlagos hely, és amely rendszert működtet azt. A feladatátvételi futtatni egy adott helyreállítási pontot. Adatvesztés fordulhat elő, attól függően, hogy a helyreállítási pont használja.
- Egy tervezett feladatátvételt a karbantartás vagy várt leállás során használható. Ez a beállítás nulla adatveszteséget biztosít. Egy tervezett feladatátvételt akkor váltódik ki, ha a forrás virtuális gépek állnak le. Nem szinkronizált adatok szinkronizálása, és akkor váltódik ki, a feladatátvételt. 
- 
Ez az eljárás rendszeres feladatátvételt ismerteti.


1. A **Beállítások** > **Replikált elemek** területen kattintson a virtuális gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Az alábbi lehetőségek egyikét használhatja:
    - **Legújabb** (alapértelmezett): Ez a lehetőség először feldolgozza a Site Recovery számára küldött összes adatot. A legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés) mert a replika virtuális gép létrehozása után feladatátvételi rendelkezik, amely a feladatátvétel kiváltásakor a Site Recovery replikálása összes adatot tartalmaz.
    - **Legutóbb feldolgozott**: Ez a lehetőség a virtuális gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak adja át. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a lehetőség a virtuális gépet a Site Recovery által feldolgozott legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. 
3. A titkosítási kulcs nem releváns ebben a forgatókönyvben.
4. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. A Site Recovery is megpróbálja küldött még nem még a másodlagos helyet ahhoz, hogy kiváltsa a feladatátvétel a helyszíni adatok szinkronizálása. Vegye figyelembe, hogy a feladatátvétel továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. A virtuális Gépet a másodlagos VMM-felhőben most kell meg.
6. Miután ellenőrizte, hogy a virtuális gép **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontot.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.  


## <a name="reprotect-and-fail-back"></a>Lássa el újból védelemmel, és a feladat-visszavételt

A másodlagos hely a fő replikálást indítani, és visszaadják feladataikat az elsődleges hely. Miután a virtuális gépeket újra futtat az elsődleges helyen, replikálhatja azokat a másodlagos helyre újra.  

1. A **beállítások** > **replikált elemek** kattintson a virtuális Gépet, majd engedélyezze **visszirányú replikálása**. A virtuális gép elkezdi replikálni vissza az elsődleges hely.
2. Kattintson a virtuális gép > **tervezett feladatátvétel**.
3. A **tervezett feladatátvétel megerősítése**, ellenőrizze a feladatátvételi irányba (másodlagos VMM-felhő), és válassza ki a forrás és cél helyeket. 
4. A **adatszinkronizálás**, adja meg, hogyan szeretné szinkronizálni:
    - **Szinkronizálja az adatokat a feladatátvétel előtt (csak a változási különbözeteket szinkronizálása)**– ezt a beállítást VM állásidő minimálisra csökkenti, mert a virtuális gép leállítása nélkül szinkronizálják. Íme működése:
        - Pillanatképet készít a replika virtuális Gépre, és az elsődleges Hyper-V-gazdagépre másolja. A replika virtuális gép folyamatosan működik.
        - A replika virtuális Gépre, leáll, így nem új módosítások hiba fordul elő. A változási különbözeteket végső készletének átkerülnek az elsődleges hely, és az elsődleges hely virtuális gép elindul.
    - **Szinkronizálja az adatokat csak a feladatátvétel során (teljes letöltésére)**– használja ezt a beállítást, ha Ön már fut a másodlagos hely hosszú ideig. Ez a beállítás nem gyorsabb, mert azt várhatóan több lemez változik, és ellenőrzőösszeg-számítások az időt nem. Ez a beállítás egy lemez letöltés hajt végre. Ez akkor hasznos, ha az elsődleges virtuális gép törölve lett.
5. A titkosítási kulcs nem releváns ebben a forgatókönyvben.
6. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
7. Ha a feladatátvétel előtt az adatok szinkronizálásra kijelölt, miután a kezdeti adatszinkronizálás történik, és készen áll a replika virtuális Gépre, a másodlagos hely leállítása, kattintson **feladatok** > tervezett feladatátvétel feladat neve >  **Végezze el a feladatátvételi**. Ez a másodlagos virtuális gép leáll, az elsődleges hely átviszi a legutóbbi változtatásokat, és az elsődleges virtuális gép elindul.
8. Az elsődleges VMM-felhő ellenőrizze, hogy rendelkezésre áll-e a virtuális gép.
9. Az elsődleges virtuális gép most egy függőben lévő véglegesítési állapotban van. Kattintson a **véglegesítési**, a feladatátvétel véglegesítésének.
10. Ha az elsődleges virtuális gép újra vissza egy másodlagos hely replikálást indítani, engedélyezni kívánt **visszirányú replikálása**.


> [!NOTE]
> A fordított replikáció csak replikál óta a replika virtuális gép ki lett kapcsolva, és csak a változási különbözeteket küldése történt.

## <a name="next-steps"></a>További lépések
[Tekintse át a lépés](hyper-v-vmm-disaster-recovery.md) a Hyper-V virtuális gépek replikálása másodlagos helyre.
