---
title: Feladat-visszavétel futtatása az Azure-ból a helyszíni rendszerbe irányuló Hyper-v virtuális gépek katasztrófája miatt | Microsoft Docs
description: Ismerje meg, hogyan hajthatja végre a Hyper-V virtuális gépeket egy helyszíni helyre a vész-helyreállítás során az Azure-ba a Azure Site Recovery szolgáltatással.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 07ecc8547ab155600bccfd1ad8f1ecbb58a18fa3
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931847"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>A Hyper-V virtuális gépek feladat-visszavételének futtatása

Ez a cikk a Site Recovery által védett Hyper-V virtuális gépek feladatátvételét ismerteti.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy elolvasta a feladat- [visszavétel különböző típusairól](concepts-types-of-failback.md) és a vonatkozó kikötésekről szóló részleteket.
- Győződjön meg arról, hogy az elsődleges hely VMM-kiszolgálója vagy a Hyper-V-kiszolgáló csatlakozik az Azure-hoz.
- A virtuális gépen **véglegesíteni** kell a végrehajtást.
- Győződjön meg arról, hogy Storage-fiókot használ a replikáláshoz és a nem felügyelt lemezekhez. A Hyper-V virtuális gépek felügyeleti lemezekkel történő replikálása nem támogatott.

## <a name="perform-failback"></a>Feladat-visszavétel végrehajtása
Az elsődlegesről a másodlagos helyre történő feladatátvételt követően a replikált virtuális gépeket nem Site Recovery védi, és a másodlagos hely most aktív helyként működik. Egy helyreállítási tervben szereplő virtuális gépek feladatátvételéhez futtassa a tervezett feladatátvételt a másodlagos helyről az elsődlegesre, az alábbiak szerint. 
1. Válassza a **helyreállítási tervek** > *recoveryplan_name*lehetőséget. Kattintson a **feladatátvétel** > **tervezett feladatátvétel**elemre.
2. A **tervezett feladatátvétel megerősítése** lapon válassza ki a forrás és a cél helyét. Jegyezze fel a feladatátvétel irányát. Ha a feladatátvétel elsődlegesen a várt módon működik, és az összes virtuális gép a másodlagos helyen található, akkor ez csak tájékoztató.
3. Ha az Azure-ban nem végez biztonsági mentést, válassza az **adatok szinkronizálása**beállítást:
    - **Adatszinkronizálás a feladatátvétel előtt (csak a különbözeti változások szinkronizálása)** – ezzel a beállítással csökkenthető a virtuális gépek leállása anélkül, hogy azokat le kellene állítani. A következő lépéseket hajtja végre:
        - 1\. fázis: Pillanatképet készít az Azure-beli virtuális gépről, és átmásolja a helyszíni Hyper-V-gazdagépre. A gép továbbra is fut az Azure-ban.
        - 2\. fázis: Leállítja a virtuális gépet az Azure-ban, hogy ne történjen új változás. A különbözeti változások végső készlete a helyszíni kiszolgálóra kerül, és a helyszíni virtuális gép elindult.

    - Az **adatszinkronizálás csak feladatátvétel során (teljes letöltés)** – ez a beállítás gyorsabb.
        - Ez a beállítás gyorsabb, mert várható, hogy a lemez nagy része megváltozott, és nem szeretnénk időt tölteni az ellenőrzőösszeg kiszámításakor. Elvégzi a lemez letöltését. Akkor is hasznos, ha a helyszíni virtuális gépet törölték.
        - Azt javasoljuk, hogy ezt a lehetőséget akkor használja, ha már futtatta az Azure-t egy ideig (egy hónapig vagy többet), vagy a helyszíni virtuális gépet törölték. Ez a beállítás nem végez ellenőrzőösszeg-számításokat.


4. Ha engedélyezve van az adattitkosítás a felhőben, a **titkosítási kulcsban** válassza ki azt a tanúsítványt, amelyet a VMM-kiszolgálón a szolgáltató telepítése során adott meg, amikor engedélyezte az adattitkosítást.
5. Indítsa el a feladatátvételt. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. Ha a feladatátvétel előtt szinkronizálja az adatokat, miután a kezdeti adatszinkronizálás befejeződött, és készen áll a virtuális gépek leállítására az Azure-ban, kattintson a **feladatok** > feladat neve > **teljes feladatátvétel**lehetőségre. Ezzel leállítja az Azure-gépet, átviszi a legutóbbi módosításokat a helyszíni virtuális gépre, és elindítja a virtuális gépet a helyszínen.
7. Most már bejelentkezhet a virtuális gépre, és ellenőrizheti, hogy az a várt módon elérhető-e.
8. A virtuális gép véglegesített függő állapotban van. A feladatátvétel elvégzéséhez kattintson a **véglegesít** gombra.
9. A feladat-visszavétel befejezéséhez kattintson a **visszirányú replikálás** gombra a virtuális gép elsődleges helyen való védelmének megkezdéséhez.


Az alábbi eljárásokkal visszatérhet az eredeti elsődleges helyre. Ez az eljárás azt ismerteti, hogyan futtathat egy tervezett feladatátvételt egy helyreállítási tervhez. Azt is megteheti, hogy a feladatátvételt egyetlen virtuális géphez futtatja a **Virtual Machines** lapon.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Feladat-visszavétel egy másik helyre a Hyper-V környezetben
Ha a [Hyper-V-hely és az Azure](site-recovery-hyper-v-site-to-azure.md) közötti védelmet telepített, az Azure-ból egy másik helyszíni helyre történő feladat-visszavétel lehetősége van. Ez akkor hasznos, ha új helyszíni hardvert kell beállítania. A következőképpen teheti meg.

1. Ha új hardvert állít be, telepítse a Windows Server 2012 R2-t és a Hyper-V szerepkört a kiszolgálón.
2. Hozzon létre egy azonos nevű virtuális hálózati kapcsolót az eredeti kiszolgálón.
3. Válassza **a védett elemek** -> **védelmi csoport** -> \<ProtectionGroupName >- \<> VirtualMachineName > a feladat-visszavételhez, majd a **tervezett feladatátvétel**lehetőséget.
4. A **tervezett feladatátvétel megerősítése** területen válassza **a helyszíni virtuális gép létrehozása elemet, ha az még nem létezik**.
5. Az Állomásnév mezőben válassza ki azt az új Hyper-V-gazdagépet, amelyre a virtuális gépet helyezni kívánja.
6. Az adatszinkronizálás során javasoljuk, hogy a feladatátvétel előtt szinkronizálja az adatokat. Ez a virtuális gépek leállását a lehető legkisebbre állítja, miközben az szinkronizálása nem történik meg. A következő műveleteket végzi el:

    - 1\. fázis: Pillanatképet készít az Azure-beli virtuális gépről, és átmásolja a helyszíni Hyper-V-gazdagépre. A gép továbbra is fut az Azure-ban.
    - 2\. fázis: Leállítja a virtuális gépet az Azure-ban, hogy ne történjen új változás. A változtatások végső készlete a helyszíni kiszolgálóra kerül, és a helyszíni virtuális gép elindult.
    
7. Kattintson a pipa jelre a feladatátvétel (feladat-visszavétel) elindításához.
8. Miután a kezdeti szinkronizálás befejeződik, és készen áll a virtuális gép leállítására az Azure-ban, kattintson a **feladatok** > \<tervezett feladatátvételi feladat > > **teljes feladatátvétel**elemre. Ezzel leállítja az Azure-gépet, átviszi a legújabb módosításokat a helyszíni virtuális gépre, és elindítja azt.
9. Bejelentkezhet a helyszíni virtuális gépre annak ellenőrzéséhez, hogy minden a várt módon működik-e. Ezután kattintson a **véglegesítés** gombra a feladatátvétel befejezéséhez. A commit művelettel törli az Azure-beli virtuális gépet és annak lemezeit, és előkészíti a virtuális gépet, hogy ismét védetté legyen.
10. A helyszíni virtuális gép védelmének megkezdéséhez kattintson a **visszirányú replikálás** elemre.

    > [!NOTE]
    > Ha megszakítja a feladat-visszavételi feladatot, amíg az adatszinkronizálási lépésben szerepel, a helyszíni virtuális gép sérült állapotban lesz. Ennek az az oka, hogy az adatszinkronizálás az Azure-beli virtuális gépekről származó legfrissebb adatok átmásolását végzi a helyszíni adatlemezekre, és amíg a szinkronizálás be nem fejeződik, előfordulhat, hogy a lemez adatainak nem konzisztens állapotban vannak. Ha a helyszíni virtuális gép elindult az adatszinkronizálás megszakítása után, akkor előfordulhat, hogy a rendszer nem indul el. A feladatátvétel újraindítása az adatszinkronizálás befejezéséhez.


## <a name="why-is-there-no-button-called-failback"></a>Miért nincs a feladat-visszavétel nevű gomb?
A portálon nincs a feladat-visszavétel nevű explicit kézmozdulat. A feladat-visszavétel egy olyan lépés, amelyben visszatérhet az elsődleges helyre. Definíció szerint a feladat-visszavétel akkor történik meg, amikor a virtuális gépeket a helyreállításról az elsődlegesre végzi.

Ha feladatátvételt kezdeményez, a panel a virtuális gépek áthelyezésének irányát értesíti, ha az irány az Azure-ból a helyszíni rendszerbe kerül, a feladat-visszavétel.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Miért van csak egy tervezett feladatátvételi kézmozdulat a feladat-visszavételhez?
Az Azure egy magasan elérhető környezet, és a virtuális gépek mindig elérhetők. A feladat-visszavétel olyan tervezett tevékenység, amelyben kis állásidőt kell elvégeznie, hogy a munkaterhelések a helyszínen újra futtathatók legyenek. Ez az adatvesztést nem számítja ki. Ezért csak egy tervezett feladatátvételi kézmozdulat érhető el, amely kikapcsolja a virtuális gépeket az Azure-ban, letölti a legújabb módosításokat, és gondoskodik arról, hogy ne legyen adatvesztés.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Szükségem van egy Azure-beli Process Serverre a Hyper-v-re történő feladat-visszavétel érdekében?
Nem, a Process Server csak akkor szükséges, ha a VMware virtuális gépeket védi. A Hyper-v virtuális gépek védelmének/feladat-visszavételének biztosításához nincs szükség további összetevők telepítésére.


## <a name="time-taken-to-failback"></a>A feladat-visszavételhez szükséges idő
Az adatszinkronizálás végrehajtásához és a virtuális gép elindításához szükséges idő a különböző tényezőktől függ. Annak érdekében, hogy betekintést kapjon a szükséges idő, elmagyarázza, mi történik az adatszinkronizálás során.

Az adatok szinkronizálása pillanatképet készít a virtuális gép lemezéről, és a blokkokat blokkolja, és kiszámítja az ellenőrzőösszeget. Ezt a kiszámított ellenőrzőösszeget a rendszer a helyszínen továbbítja, hogy összehasonlítsa az azonos blokk helyszíni ellenőrzőösszegét. Ha az ellenőrzőösszegek egyeznek, az adatblokk nem kerül átvitelre. Ha nem egyezik, az adatblokkot a rendszer áthelyezi a helyszíni szolgáltatásba. Ez az adatátviteli idő a rendelkezésre álló sávszélességtől függ. Az ellenőrzőösszeg sebessége percenként néhány GB. 

Az adatletöltés felgyorsításához beállíthatja, hogy a MARS-ügynök több szálat használjon a letöltés integrálással. Az ügynök letöltési szálának módosításához tekintse meg a [dokumentumot](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) .


## <a name="next-steps"></a>További lépések

A **véglegesítés**után kezdeményezheti a *visszirányú replikálást*. Ez elindítja a virtuális gép helyszíni biztonsági mentését az Azure-ba. Ez csak a módosításokat replikálja, mivel a virtuális gép ki van kapcsolva az Azure-ban, és így csak különbözeti módosításokat küld.
