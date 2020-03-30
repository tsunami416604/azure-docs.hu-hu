---
title: Hyper-V virtuális gépek visszavétele az Azure-ból az Azure Site Recovery szolgáltatással
description: Hyper-V virtuális gépek visszaadása egy helyszíni webhelyre az Azure-ból az Azure Site Recovery segítségével.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281781"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Feladat-visszavétel futtatása Hyper-V rendszerű virtuális gépekhez

Ez a cikk ismerteti, hogyan lehet feladat-visszaadás az Azure virtuális gépek, amelyek a felügyelt virtuális gépek feladatátvételután egy helyszíni helyről az Azure-ba, [az Azure Site Recovery](site-recovery-overview.md)használatával.

- Az Azure-ból származó Hyper-V virtuális gépek et az Azure-ból a helyszíni helyre tervezett feladatátvétel futtatásával visszakell adnia. Ha a feladatátvételi irány az Azure-tól a helyszíni, feladat-visszavételnek minősül.
- Mivel az Azure egy magas rendelkezésre állású környezet, és a virtuális gépek mindig elérhetők, az Azure-ból történő feladat-visszavétel tervezett tevékenység. Megtervezheti egy kis állásidőt, hogy a számítási feladatok újra futtathassák a helyszíni futtatást. 
- A tervezett feladat-visszavétel kikapcsolja a virtuális gépeket az Azure-ban, és letölti a legújabb módosításokat. Adatvesztés nem várható.

## <a name="before-you-start"></a>Előkészületek

1. Tekintse át a használható [feladat-visszavételi típusokat](failover-failback-overview.md#hyper-v-reprotectionfailback) – az eredeti hely-helyreállítást és az alternatív hely-helyreállítást.
2. Győződjön meg arról, hogy az Azure virtuális gépek egy tárfiókot használnak, és nem felügyelt lemezek. A felügyelt lemezekkel replikált Hyper-V virtuális gépek feladat-visszavétele nem támogatott.
3. Ellenőrizze, hogy a helyszíni Hyper-V állomás (vagy a System Center VMM-kiszolgáló, ha a Site Recovery használatával rendelkezik) fut, és csatlakozik az Azure-hoz. 
4. Győződjön meg arról, hogy a feladatátvétel és a véglegesítés befejeződött a virtuális gépek. Nem kell konkrét Site Recovery-összetevőket beállítania az Azure-beli Hyper-V virtuális gépek feladat-visszavételéhez.
5. Az adatok szinkronizálásának befejezéséhez és a helyszíni virtuális gép elindításához szükséges idő számos tényezőtől függ. Az adatok letöltésének felgyorsítása érdekében beállíthatja, hogy a Microsoft Recovery Services ügynök több szálat használjon a letöltés párhuzamosításához. [További információ](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Visszaadott feladat az eredeti helyre

Ha az Azure-ban lévő Hyper-V virtuális gépeket az eredeti helyszíni virtuális gépre szeretné visszaadni, futtasson egy tervezett feladatátvételt az Azure-ból a helyszíni webhelyre az alábbiak szerint:

1. A tárolóban > **replikált elemeket,** válassza ki a virtuális gép. Kattintson a jobb gombbal a virtuális gép > **tervezett feladatátvételre.** Ha nem tud visszaválasztani egy helyreállítási tervet, válassza ki a terv nevét, és kattintson a **Feladatátvétel** > **tervezett feladatátvétel gombra.**
2. A **Tervezett feladatátvétel megerősítése**csoportban válassza ki a forrás- és célhelyeket. Vegye figyelembe a feladatátvétel irányát. Ha az elsődleges feladatátvétel a várt módon működött, és az összes virtuális gép a másodlagos helyen van, ez csak tájékoztatásul szolgál.
3. Az **Adatszinkronizálás csoportban**válasszon egy beállítást:
    - **Adatok szinkronizálása feladatátvétel előtt (csak a különbözeti módosítások szinkronizálása)**– Ez a beállítás minimálisra csökkenti a virtuális gépek állásidejét, mivel azok a szinkronizálások leállítása nélkül szinkronizálódnak.
        - **1. fázis:** Pillanatképet készít az Azure virtuális gépről, és átmásolja azt a helyszíni Hyper-V gazdagépre. A gép továbbra is fut az Azure-ban.
        - **2. fázis:** Leállítja az Azure virtuális gép, hogy nincs új változás történik ott. A különbözeti módosítások végleges készlete átkerül a helyszíni kiszolgálóra, és elindul a helyszíni virtuális gép.
    - **Csak feladatátvétel során (teljes letöltés) szinkronizálja**az adatokat – Ez a beállítás gyorsabb, mert feltételezzük, hogy a lemez nagy része megváltozott, és nem szeretnénk időt tölteni az ellenőrzőösszegek kiszámításával. Ez a beállítás nem végez ellenőrzőösszeg-számításokat.
        - Ez végre egy letöltés a lemez. 
        - Azt javasoljuk, hogy használja ezt a lehetőséget, ha már egy ideje (egy hónap vagy több) azure-t futtat, vagy ha a helyszíni virtuális gép törlődik.

4. Csak a VMM esetében, ha az adattitkosítás engedélyezve van a felhőben, a **Titkosítási kulcs**ban válassza ki azt a tanúsítványt, amelyet akkor adott ki, amikor engedélyezte az adattitkosítást a Szolgáltató telepítése során a VMM-kiszolgálón.
5. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. Ha a feladatátvétel előtt kiválasztotta az adatok szinkronizálásának lehetőségét, a kezdeti adatszinkronizálás befejezése után, és készen áll a virtuális gépek leállítására az Azure-ban, kattintson a **Feladatok** > feladatnevének > **Teljes feladatátvétel gombra.** Ez a következőket teszi:
    - Leállítja az Azure-gépet.
    - A legújabb módosításokátvitele a helyszíni virtuális gépre.
    - Elindítja a helyszíni virtuális gép.
7. Most már bejelentkezhet a helyszíni virtuális gépbe, hogy ellenőrizze, hogy a várt módon elérhető-e.
8. A virtuális gép véglegesítésfüggő állapotban van. A feladatátvétel véglegesítéséhez kattintson a **Véglegesítés** gombra.
9. A feladat-visszavétel befejezéséhez kattintson a **Replikálás sztornírozása** gombra a helyszíni virtuális gép replikálásának újra az Azure-ba.



## <a name="fail-back-to-an-alternate-location"></a>Feladat-visszavétel másik helyre 

A következő helyre való visszavétel:

1. Ha új hardvert állít be, telepítse [a Windows támogatott verzióját](hyper-v-azure-support-matrix.md#replicated-vms)és a Hyper-V szerepkört a számítógépre.
2. Hozzon létre egy virtuális hálózati kapcsolót ugyanazzal a névvel, mint az eredeti kiszolgálón.
3. A **Védett elemek védelmére szolgáló** > csoport \<védelmi**csoport>** > \<-> VirtualMachineName>, jelölje ki a feladatátvételt kívánt virtuális gépet, majd válassza a Tervezett **feladatátvétel**lehetőséget.
4. A **Tervezett feladatátvétel megerősítése csoportban**válassza **a Helyszíni virtuális gép létrehozása, ha nem létezik.**
5. Az **Állomásnév területen**válassza ki azt az új Hyper-V gazdakiszolgálót, amelyre a virtuális gép kívánta elhelyezni.
6. Az **Adatszinkronizálás ban**azt javasoljuk, hogy a feladatátvétel előtt válassza ki az adatok szinkronizálásának lehetőségét. Ez minimálisra csökkenti a virtuális gépek állásidejét, mivel leállítás nélkül szinkronizálja őket. A következőket teszi:
    - **1. fázis:** Pillanatképet készít az Azure virtuális gépről, és átmásolja azt a helyszíni Hyper-V gazdagépre. A gép továbbra is fut az Azure-ban.
    - **2. fázis:** Leállítja az Azure virtuális gép, hogy nincs új változás történik ott. A módosítások végleges készlete átkerül a helyszíni kiszolgálóra, és elindul a helyszíni virtuális gép.
    
7. Kattintson a pipára a feladatátvétel (feladat-visszavétel) megkezdéséhez.
8. Miután a kezdeti szinkronizálás befejeződött, és készen áll az Azure virtuális gép leállítására, kattintson a **Feladatok** > \<tervezett feladatátvételi feladat> > **a teljes feladatátvétel.** Ez leállítja az Azure-gépet, átviszi a legújabb módosításokat a helyszíni virtuális gépre, és elindítja azt.
9. Bejelentkezhet a helyszíni virtuális gépbe, és ellenőrizheti, hogy minden a várt módon működik-e.
10. A **feladatátvétel** befejezéséhez kattintson a Véglegesítés gombra. Commit törli az Azure virtuális gép és a lemezek, és előkészíti a helyszíni virtuális gép ismét védeni kell.
10. Kattintson **a Replikálás szenlítése** gombra a helyszíni virtuális gép Azure-ba történő replikálásának megkezdéséhez. Csak a különbözeti változások, mivel a virtuális gép ki van kapcsolva az Azure-ban replikálódik.

    > [!NOTE]
    > Ha megszakítja a feladat-visszavételi feladatot az adatok szinkronizálása során, a helyszíni virtuális gép sérült állapotban lesz. Ennek az az oka, hogy az adatok szinkronizálása átmásolja a legújabb adatokat az Azure virtuálisgép-lemezekről a helyszíni adatlemezekre, és amíg a szinkronizálás befejeződik, előfordulhat, hogy a lemezadatok nem konzisztens állapotban vannak. Ha a helyszíni virtuális gép az adatszinkronizálás megszakítása után elindul, előfordulhat, hogy nem indul el. Ebben az esetben futtassa újra a feladatátvételt az adatszinkronizálás befejezéséhez.


## <a name="next-steps"></a>További lépések
Miután a helyszíni virtuális gép replikálása az Azure-ba, futtathat [egy másik feladatátvétel az](site-recovery-failover.md) Azure-ba, ha szükséges.
