---
title: "A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek replikálása egy másodlagos adatközpontba Site Recovery szolgáltatással |} Microsoft Docs"
description: "Megtudhatja, hogyan Hyper-V virtuális gépek feladatátvétele a másodlagos helyszíni helyre, és vissza az elsődleges hely, az Azure Site Recovery sikertelen"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>A feladatátvétel, és sikertelen lesz a másodlagos helyszíni helyre replikált Hyper-V virtuális gépek biztonsági

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeli, és koordinálja a replikációt, a feladatátvételi és a feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Ez az oktatóanyag ismerteti a Hyper-V virtuális gépek kezelése a System Center Virtual Machine Manager (VMM) felhő, egy másodlagos VMM helyre a feladatátvétel. Miután keresztül korábban meghiúsult, hogy visszaadják feladataikat a helyszíni hely a rendelkezésre álló. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Feladatok átadása a Hyper-V virtuális gépek egy elsődleges felhőből a VMM egy másodlagos VMM-felhő
> * Állítsa a másodlagos hely az elsődleges és a feladat-visszavételt
> * Opcionálisan a replikálása az elsődleges másodlagos újra elindítani

## <a name="overview"></a>Áttekintés

A feladatátvételi és a feladat-visszavétel rendelkezik három fázisból áll:

1. **Feladatok átadása a másodlagos hely**: feladatátvételt gépeket az elsődleges helyről másodlagos.
2. **A másodlagos helyről sikertelen**: virtuális gépek replikálása az elsődleges a másodlagos, és futtassa a tervezett feladatátvételt a feladat-visszavételt.
3. A tervezett feladatátvétel után opcionálisan replikálást indítani az elsődleges helyről a másodlagos újra.


## <a name="fail-over-to-a-secondary-site"></a>Feladatok átadása a másodlagos hely

### <a name="failover-prerequisites"></a>Feladatátvételi Előfeltételek

Győződjön meg arról, hogy befejezte a [vész-helyreállítási részletezési](tutorial-dr-drill-secondary.md) ellenőrizze, hogy minden a várt módon működik.


### <a name="run-a-failover-from-primary-to-secondary"></a>Futtassa a feladatátvétel az elsődleges másodlagos

Hyper-V virtuális gépek futtatása rendszeres vagy tervezett feladatátvétel.

- Váratlan leállás rendszeres feladatátvevő használja. Ennél a feladatátvételnél futtatásakor a Site Recovery hoz létre egy virtuális Gépet a másodlagos hely, és amely rendszert működtet azt. A feladatátvételi futtatni egy adott helyreállítási pontot. Adatvesztés fordulhat elő, attól függően, hogy a helyreállítási pont használja.
- Egy tervezett feladatátvételt a karbantartás vagy várt leállás során használható. Ez a beállítás nulla adatveszteséget biztosít. Egy tervezett feladatátvételt akkor váltódik ki, ha a forrás virtuális gépek állnak le. Nem szinkronizált adatok szinkronizálása, és akkor váltódik ki, a feladatátvételt. 
- 
Ez az eljárás rendszeres feladatátvételt ismerteti.


1. A **beállítások** > **replikált elemek** kattintson a virtuális gép > **feladatátvételi**.
2. A **feladatátvételi** válassza ki a **helyreállítási pont** feladatokat. Az alábbi lehetőségek egyikét használhatja:
    - **Legújabb** (alapértelmezett): Ez a beállítás először dolgozza fel a Site Recovery számára küldött minden adathoz. A legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés) mert a replika virtuális gép létrehozása után feladatátvételi rendelkezik, amely a feladatátvétel kiváltásakor a Site Recovery replikálása összes adatot tartalmaz.
    - **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat a virtuális gép a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott. Ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése), mert nincs van feldolgozásával töltött idő feldolgozatlan adatokat.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a virtuális Gépet, a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak. 
3. A titkosítási kulcs nem releváns ebben a forgatókönyvben.
4. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. A Site Recovery is megpróbálja küldött még nem még a másodlagos helyet ahhoz, hogy kiváltsa a feladatátvétel a helyszíni adatok szinkronizálása. Vegye figyelembe, hogy a feladatátvétel továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
5. A virtuális Gépet a másodlagos VMM-felhőben most kell meg.
6. Miután ellenőrizte, hogy a virtuális gép **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: elindítani a feladatátvételt, mielőtt a virtuális gép replikációs le van állítva. Ha megszakítja a folyamatban lévő feladatátvételi leáll, a feladatátvétel, de a virtuális gép nem fog újra replikálni.  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>Lássa el újból védelemmel, és újból az elsődleges a másodlagos sikertelen

### <a name="prerequisites-for-failback"></a>Feladat-visszavételhez szükséges előfeltételek

Feladat-visszavétel befejezése, győződjön meg arról, hogy az elsődleges és másodlagos VMM-kiszolgáló csatlakozik-e a Site Recovery.


### <a name="reprotect-and-fail-back"></a>Lássa el újból védelemmel, és a feladat-visszavételt

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

