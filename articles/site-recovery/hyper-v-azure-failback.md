---
title: A feladat-visszavétel futtatása Hyper-v virtuális gépek egy helyszíni helyhez |} A Microsoft Docs
description: Az Azure Site Recovery koordinálja a replikációt, feladatátvételének és helyreállításának virtuális gépek és fizikai kiszolgálók. Ismerje meg a feladat-visszavétel az Azure-ból a helyszíni adatközpontba.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: fd171251ef465a28e4844901a529e0a3eaaf8f9d
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920872"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>A feladat-visszavétel futtatása Hyper-V virtuális gépek

Ez a cikk ismerteti, hogyan végezhet biztonsági Hyper-V virtuális gépek Site Recovery által védett.

## <a name="prerequisites"></a>Előfeltételek
1. Győződjön meg arról, hogy rendelkezik-e a részletek tekintse át a a [különböző típusú feladat-visszavétel](concepts-types-of-failback.md) és a megfelelő korlátozásokkal.
1. Győződjön meg arról, hogy az elsődleges hely VMM-kiszolgáló vagy Hyper-V gazdakiszolgáló kapcsolódik az Azure-bA.
2. Végrehajtása után kell **véglegesítése** a virtuális gépen.

## <a name="perform-failback"></a>Hajtsa végre a feladat-visszavétel
Feladatátvétel után az elsődleges kiszolgálóról a másodlagos helyre replikált virtuális gépek Site Recovery által nem védett, és a másodlagos hely most hálózataként viselkedik az aktív hely. Sikertelen a helyreállítási terv tervezett feladatátvételt a másodlagos helyről az elsődleges, a következő virtuális gépek biztonsági mentéséhez. 
1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvételi** > **tervezett feladatátvételt**.
2. Az a **tervezett feladatátvétel megerősítése** lapon, válassza ki a forrás- és helyek. Megjegyzés: a feladatátvétel iránya. Ha a feladatátvételt az elsődleges, a várt és az összes virtuális gép Ez az információ csak a másodlagos helyen.
3. Ha az Azure-ból, válassza ki a beállítások **adatszinkronizálás**:
    - **Szinkronizálja az adatokat (szinkronizálás csak a változásokat) a feladatátvétel előtt**– Ez a beállítás a virtuális gépek állásidő minimálisra csökkenti a szerint szinkronizálja azok leállítása nélkül. A következő lépéseket hajtja végre:
        - 1. fázis: Pillanatképet készít a virtuális gép az Azure-ban, és átmásolja a helyszíni Hyper-V gazdagépen. A gépet továbbra is fut az Azure-ban.
        - 2. fázis: Az Azure-ban a virtuális gép leáll, így új módosítása nélkül hiba fordul elő. A módosítások átkerülnek a helyszíni kiszolgáló és a helyszíni virtuális gép változásreplikációjához végső készletének már elindult.

    - **Adatok csak a feladatátvétel során (teljes letöltés) szinkronizálása**– Ez a beállítás akkor gyorsabb.
        - Ezt a beállítást, hogy a lemez a legtöbb megváltozott, és nem szeretnénk ellenőrzőösszeg számítása időt várhatóan azért gyorsabb. A lemez letöltési hajtja végre. Ez hasznos is a helyszíni virtuális gép törölve lett.
        - Javasoljuk, hogy ezt a beállítást használja, ha futtatja, az Azure egy ideig (egy hónapig vagy még több) vagy a helyszíni virtuális gép törölve lett. Ez a beállítás nem számításokat bármely ellenőrzőösszeg.


4. Ha engedélyezett az adattitkosítás a felhőhöz, a **titkosítási kulcs** válassza ki a tanúsítványt, amely adta ki, ha engedélyezte az adattitkosítást a VMM-kiszolgálón a szolgáltató telepítése közben.
5. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
6. Ha a választott is szinkronizálhatók az adatok a feladatátvétel előtt, Miután befejeződött a kezdeti szinkronizálás, és készen áll a állítsa le a virtuális gépek az Azure-ban kattintson a **feladatok** tervezett feladatátvételi feladat neve **Feladatátvétel befejezéséhez**. Ez leállítja az Azure-beli gép, átviszi a legutóbbi változtatásokat a helyszíni virtuális géphez, és a helyszíni virtuális gépek elindul.
7. Így jelentkezzen be a virtuális gép érvényesíti érhető el, a várt.
8. A virtuális gép van függőben lévő állapotba. Kattintson a **véglegesítési** véglegesíti a feladatátvételt.
9. Most kattintson a feladat-visszavétel végrehajtásához **visszirányú replikálása** az elsődleges helyen a virtuális gép védelmének megkezdéséhez.


Kövesse az alábbi eljárásokat a feladat-visszavételhez az eredeti elsődleges hely. Ez az eljárás ismerteti, hogyan futtathat egy tervezett feladatátvételt a helyreállítási csomagokra. Alternatív megoldásként futtathatja a feladatátvételt egyetlen virtuális gép a **virtuális gépek** fülre.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>A Hyper-V környezetben egy másik helyre történő feladat-visszavétel
Ha már telepítette a védelem között egy [Hyper-V-hely és az Azure](site-recovery-hyper-v-site-to-azure.md) lehetővé teszi a feladat-visszavétel az Azure-ból egy másodlagos helyszíni helyre kell. Ez akkor hasznos, ha új helyszíni hardverelemek kell. Itt látható, hogyan teheti.

1. Ha új hardver beállítása telepítse a Windows Server 2012 R2 és a Hyper-V szerepkört a kiszolgálón.
2. Hozzon létre egy virtuális hálózati kapcsolót volt az eredeti kiszolgálón található ugyanazzal a névvel.
3. Válassza ki **védett elemek** -> **védelmi csoport**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> feladat-visszavételt, és válassza ki a kívánt **tervezett Feladatátvétel**.
4. A **tervezett feladatátvétel megerősítése** kiválasztása **létrehozása a helyszíni virtuális gépet, ha még nem létezik**.
5. A gazdagép neve ** válassza ki az új Hyper-V gazdakiszolgálón, amelyen el szeretné helyezni a virtuális gépet.
6. Az adatszinkronizálás, javasoljuk, hogy azt a lehetőséget választja **szinkronizálja az adatokat a feladatátvétel előtt**. Így minimálisra csökkenthető a virtuális gépek állásidő, szinkronizálja őket leállítása nélkül. A következőket teszi:

    - 1. fázis: Pillanatképet készít a virtuális gép az Azure-ban, és átmásolja a helyszíni Hyper-V gazdagépen. A gépet továbbra is fut az Azure-ban.
    - 2. fázis: Az Azure-ban a virtuális gép leáll, így új módosítása nélkül hiba fordul elő. Végső készletének módosítások átkerülnek a helyszíni kiszolgáló és a helyszíni virtuális gép már elindult.
    
7. Kattintson a pipára a feladatátvétel (feladat-visszavétel) kezdődik.
8. Miután a kezdeti szinkronizálás befejezését, és készen áll a állítsa le a virtuális gépet az Azure-ban kattintson a **feladatok** > <planned failover job> > **befejezheti a feladatátvételt**. Ez leállítja az Azure-beli gép, átviszi a legutóbbi változtatásokat a helyszíni virtuális gép, és elindítja azt.
9. Ellenőrizze, hogy minden a várt módon működik a helyszíni virtuális géphez jelentkezhet be. Kattintson a **véglegesítése** a feladatátvétel befejezéséhez. Véglegesítés törli az Azure virtuális gépre és annak lemezeire, és előkészíti a virtuális gép újra kell védeni.
10. Kattintson a **visszirányú replikálása** a helyszíni virtuális gép védelmének megkezdéséhez.

    > [!NOTE]
    > A feladat-visszavételi feladat adatszinkronizálás lépésben pedig megszüntetése esetén a helyszíni virtuális gép sérült állapotú lesz. Ez azért, mert Adatszinkronizálás a legújabb adatokat másol az Azure Virtuálisgép-lemezek a helyszíni adatok lemezeket, és addig, amíg a szinkronizálás befejeződött, a lemez adatait nem lehet konzisztens lesz. Ha a helyszíni virtuális gép akkor indul el, miután az adatok szinkronizálása meg lett szakítva, akkor nem elindulhat. Feladatátvétel végrehajtásához az adatszinkronizálás újraindítása ellenőrzéssel.


## <a name="why-is-there-no-button-called-failback"></a>Miért nem létezik feladat-visszavétel nevű gomb?
A portál nincs nincs explicit kézmozdulat nevű feladat-visszavétel. Feladat-visszavétel egy lépést, ahol, térjen vissza az elsődleges hely. Definíció szerint feladat-visszavétel akkor, ha az elsődleges biztonsági a helyreállítási virtuális gépek feladatátvételét.

Kezdeményezzen feladatátvételt, ha a panel nyújt tájékoztatást, amelyben a virtuális gépeket áthelyezni, ha irányát az Azure-ból a helyszíni,-e a feladat-visszavételhez van irányát.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Miért nem csak egy tervezett feladatátvételt kézmozdulat történő feladat-visszavételhez?
Az Azure magas rendelkezésre állású környezet és a virtuális gépek mindig rendelkezésre állnak. Feladat-Visszavételhez egy tervezett tevékenység, ha úgy dönt, hogy eltarthat egy kis állásidő, azt a számítási feladatokat újra futtatni a helyszíni. Ez vár, nincs adatvesztés. Ezért csak egy tervezett feladatátvételt kézmozdulat érhető el, kapcsolja ki a virtuális gépek az Azure-ban, a legutóbbi változtatásokat letölti és biztosítására ne legyen adatvesztés nélkül.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>A folyamatkiszolgáló, az Azure-ban történő feladat-visszavételhez Hyper-v van szükségem?
Nem, a folyamatkiszolgáló csak a VMware virtuális gépek védelmét akkor szükség. További összetevők szükség, amikor a védelem, illetve-visszavétel Hyper-v virtuális gépek üzembe helyezni.


## <a name="time-taken-to-failback"></a>Feladat-visszavételhez szükséges idő
Az adatok szinkronizálása befejeződik, majd indítsa el a virtuális gépet szükséges idő számos tényezőtől függ. Abba az idő, amivel is teszünk, és mi történik, az adatszinkronizálás során.

Adatszinkronizálás pillanatképet készít a virtuális gépek lemezeit, és elindítja a blokkonként ellenőrzi és kiszámítja az ellenőrzőösszeg érvényesítése után. A számított ellenőrzőösszeg az azonos blokkhoz a helyszíni ellenőrzőösszege összehasonlítás a helyszíni érkezik. Abban az esetben az ellenőrzőösszeg egyezik, az adatblokkból származtatjuk nem kerül át. Ha nem egyezik, a helyszíni kerül az adatblokkból származtatjuk. Átviteli ezúttal attól függ, hogy a rendelkezésre álló sávszélességet. Az ellenőrzőösszeg sebességétől néhány GB / perc. 

Gyorsítsa fel az adatok letöltését, konfigurálhatja úgy a MARS-ügynök több szál segítségével párhuzamosíthatja a letöltést. Tekintse meg a [itt dokumentum](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) hogyan módosítható az ügynök a letöltési szálat.


## <a name="next-steps"></a>További lépések

Miután **véglegesítése**, is kezdeményezhető a *visszirányú replikálása*. Ekkor elindul a virtuális gép védelmét a helyszínről Azure-bA. Csak ez replikálja a módosításokat, mivel a virtuális gép ki van kapcsolva az Azure-ban, és ezért a csak a különbözeti változásokat továbbítja.
