---
title: Ismételt védelem sikertelen volt az Azure virtuális gépen az elsődleges Azure régióba az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerteti, hogyan lehet ismételt védelme az Azure-beli virtuális gépek egy másodlagos régióban egy elsődleges régióban, az Azure Site Recovery a feladatátvétel után.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: eabb7d194a3ef65282befab1ae59e85ba56f2f5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472156"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Ismételt védelem sikertelen volt az Azure virtuális gépen az elsődleges régióba


Ha Ön [átadja a feladatokat](site-recovery-failover.md) Azure virtuális gépek egyik régióból egy másik használatával [Azure Site Recovery](site-recovery-overview.md), a virtuális gépek rendszerindítási a másodlagos régióban, nem védett állapotban. Ha visszavétel a virtuális gépek az elsődleges régióra, tegye a következőt szeretné:

- A másodlagos régió virtuális gépek ismételt védelme, hogy megkezdődhessen replikálni az elsődleges régióba.
- Miután ismételt védelem befejezése és a virtuális gépeket replikál, akkor a feladatokat őket a másodlagos elsődleges régió felé.

## <a name="prerequisites"></a>Előfeltételek
1. A virtuális gép feladatátvétel az elsődleges másodlagos régióba véglegesíteni kell lennie.
2. A cél elsődleges helyének elérhetőnek kell lennie, és meg kell tudni eléréséhez, vagy hozzon létre erőforrásokat az adott régióban.

## <a name="reprotect-a-vm"></a>A virtuális gép ismételt védelme

1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a feladatátvételen átesett virtuális gép, és válassza ki **ismételt védelem**. Az ismételt védelem irányát az elsődleges, másodlagos kell megjelennie.

   ![Az ismételt védelem](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Tekintse át az erőforrás csoport, hálózati, tárolási és rendelkezésre állási csoportokat. Ezután kattintson az **OK** gombra. Ha vannak megjelölve az új erőforrásokat, akkor jönnek létre az ismételt védelem folyamat részeként.
3. Az ismételt védelem feladat feltölti a célhelyet, a legújabb adatokkal. Amely befejezését követően a változásreplikálás kerül sor. Ezt követően is átadja a feladatokat az elsődleges hely. Kiválaszthatja a tárfiók, vagy a hálózati során használni kívánt ismételt védelme, a Testreszabás beállítás használatával.

   ![A beállítás testreszabása](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Az ismételt védelem beállításainak testreszabása

Testre szabhatja a következő tulajdonságokat a cél VMe ismételt védelem során.

![Testreszabás](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Céloldali erőforráscsoport     | Módosítsa a céloldali erőforráscsoport, amelyben a virtuális gép létrejött. Ismételt védelem részeként a cél virtuális gép törlődik. Választhat egy új erőforráscsoportot, amelybe a feladatátvételt követően a virtuális gép létrehozásához.        |
|Cél virtuális hálózattal     | A célhálózat nem módosítható a védelem-újrabeállítás során. Ha módosítani szeretné a hálózaton, ismételje meg a hálózatleképezést.         |
|Cél tárterület (a másodlagos virtuális gép nem használ felügyelt lemezeket)     | A storage-fiók, amely a virtuális gép a feladatátvételt követően módosíthatja.         |
|Replikált felügyelt lemezek (a másodlagos virtuális gép felügyelt lemezeket használ.)    | A Site Recovery replika felügyelt lemezeket az elsődleges régióból történő tükrözésének a másodlagos virtuális gép felügyelt lemezeket hoz létre.         |
|Cache Storage     | Megadhatja a replikálás során használandó gyorsítótárfiókot. Alapértelmezés szerint egy új gyorsítótárfiók van hozhatók létre, ha még nem létezik.         |
|Rendelkezésre állási csoport     |Ha a másodlagos régió virtuális gép egy rendelkezésre állási csoport része, választhat egy rendelkezésre állási csoportot a céloldali virtuális Gépet az elsődleges régióba. Alapértelmezés szerint a Site Recovery megpróbálja megkeresni a meglévő rendelkezésre állási az elsődleges régióban, és használja azt. Testreszabás megadhat egy új rendelkezésre állási csoportot.         |


### <a name="what-happens-during-reprotection"></a>Mi történik az ismételt védelem alatt?

Alapértelmezés szerint a következő történik:

1. A gyorsítótárfiók jön létre a régióban, ahol a feladatátvételen átesett virtuális gép fut-e.
2. Ha a célként megadott tárfiók (az eredeti storage-fiók az elsődleges régióba) nem létezik, a rendszer létrehoz egy új. A hozzárendelt tárfiók nevét az "asr" utótaggal másodlagos virtuális gép által használt tárfiók neve.
3. Ha a virtuális gép felügyelt lemezeket használ, a replikált felügyelt lemez jön létre az elsődleges régióban, a másodlagos virtuális gép lemezeinek replikált adatok tárolásához.
4. Ha a cél rendelkezésre állási csoport nem létezik, egy új az ismételt védelmi feladat részeként létrejön, ha szükséges. Ha az ismételt védelem beállításainak testre szabott, a kiválasztott készlet szolgál.

Ha az ismételt védelmi feladat, és a cél virtuális gép létezik indít el, az alábbiak történnek:

1. A virtuális gép ki van kapcsolva, ha céloldalon fut.
2. Ha a virtuális gép felügyelt lemezeket használ, egy másolatot az eredeti lemezek jönnek létre a "-ASRReplica" utótag. Az eredeti lemezek törlődnek. A "-ASRReplica" másolatok replikációhoz használják.
3. Ha a virtuális gép nem felügyelt lemezeket használ, a céloldali virtuális gép adatlemezek vannak leválasztott és a replikáció. Az operációsrendszer-lemez egy példányát a létrehozása és csatlakoztatása a virtuális gépen. Az eredeti operációsrendszer-lemez leválasztása, és a replikációhoz használt.
4. Csak a forrás és a cél lemezzel közötti változásokat szinkronizálja a rendszer. A különbözeti számított összehasonlításával mindkét a lemezeket, és továbbítja. Az alábbi becsült ideje ellenőrzés található.
5. A szinkronizálás befejeződése után a változásreplikálás kezdődik, és létrehoz egy helyreállítási pontot a replikációs szabályzat megfelelően.

Ha az ismételt védelmi feladat indít el, és a cél virtuális gép és a lemezek nem létezik, az alábbiak történnek:
1. Ha a virtuális gép felügyelt lemezeket használ, replika lemez jön létre a "-ASRReplica" utótag. A "-ASRReplica" másolatok replikációhoz használják.
2. Ha a virtuális gép nem felügyelt lemezeket használ, a célként megadott tárfiók a replika lemezek jönnek létre.
3. A teljes lemezek másolja a sikertelen az új célrégió régió keresztül.
4. A szinkronizálás befejeződése után a változásreplikálás kezdődik, és létrehoz egy helyreállítási pontot a replikációs szabályzat megfelelően.

#### <a name="estimated-time--to-do-the-reprotection"></a>Becsült ideje a az ismételt védelem 

A legtöbb esetben az Azure Site Recovery nem replikálja a teljes adatokat a forrásrégióban. Az alábbiakban a feltételeket, amelyek azt határozza meg, mennyi adatot replikálni szeretné:

1.  Ha a forrásoldali virtuális gép adatai törölve, sérült vagy nem érhető el, például erőforráscsoport valamilyen okból módosítása és törlése, majd az ismételt védelem befejezése integrációs modul során történik meg, nem szerepel megjeleníthető adat a forrásrégióban használatára a rendelkezésre álló.
2.  Ha a forrás virtuális gép adatai érhető majd csak különbözeti összehasonlítja a két lemez számított és továbbítja. Ellenőrizze az alábbi táblázatban beolvasni a szükséges becsült idő 

|** Példa helyzet ** | ** Az ismételt védelem szükséges idő ** |
|--- | --- |
|Forrásrégió 1 virtuális gép az 1 TB standard szintű lemezes rendelkezik<br/>– Csak 127 GB adatokat használ, és a lemez rest üres<br/>-60 MiB/S átviteli sebesség szabványosnak tekinthető lemez típusa<br/>– A feladatátvételt követően módosítása nincs adat| Becsült időt 45 perc – 1,5 óra<br/> – Ismételt védelem során a Site Recovery fel a ellenőrzőösszegei teljes adatokat, ami eltarthat 127 GB / 45 MB körülbelül 45 perc<br/>– Néhány általános időpontját kötelező megadni a Site Recovery automatikus méretezés, amely 20 – 30 perc<br/>– Nincs kimenő adatforgalmi díjak |
|Forrásrégió 1 virtuális gép az 1 TB standard szintű lemezes rendelkezik<br/>– Csak 127 GB adatokat használ, és a lemez rest üres<br/>-60 MiB/S átviteli sebesség szabványosnak tekinthető lemez típusa<br/>-45 GB adatmódosítás a feladatátvétel után| Becsült időt 1 óra – 2 óra<br/>– Ismételt védelem során a Site Recovery fel a ellenőrzőösszegei teljes adatokat, ami eltarthat 127 GB / 45 MB körülbelül 45 perc<br/>-Átviteli idő érintő módosítások, amelyek 45 GB 45 GB / 45 MB/s ~ 17 perc<br/>– A kimenő forgalom költségeit csak 45 GB adat esetében az ellenőrzőösszeg nem lenne|
 



## <a name="next-steps"></a>További lépések

Miután a virtuális gép védett, feladatátvevő is kezdeményezhető. A feladatátvétel leállítja a virtuális Gépet a másodlagos régióban és hoz létre, és az elsődleges régióban, néhány kisebb üzemkimaradás indul el a virtuális gép. Javasoljuk, hogy ennek megfelelően válassza ki egy időpontot, és a feladatátvételi teszt futtatása előtt az elsődleges hely teljes feladatátvételt kezdeményez. [További](site-recovery-failover.md) feladatátvételi kapcsolatban.
