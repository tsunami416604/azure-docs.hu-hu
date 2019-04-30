---
title: HANA feladatátvételi eljárást az SAP Hana az Azure-ban (nagyméretű példányok) vészhelyen |} A Microsoft Docs
description: Hogyan vész-helyreállítási webhelyként feladatátvételt hajt végre az SAP Hana az Azure-ban (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76d8bb816bdf229d13a49fa61337899a8bf29ecd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098286"
---
# <a name="disaster-recovery-failover-procedure"></a>Vészhelyreállítási feladatátvételi eljárás


>[!IMPORTANT]
>Ez a dokumentáció a SAP HANA-felügyelet dokumentációja és az SAP-megjegyzések helyettesítése. Valószínű, hogy az olvasó rendelkezik egy szilárd ismeretekkel és szaktudását a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, visszaállítás, magas rendelkezésre állású és vész-helyreállítási témaköröket. Ebben a dokumentációban beállítást mutató képernyőképek a SAP HANA Studio jelennek meg. Tartalom struktúra és az SAP-felügyeleti eszközök és az eszközök magukat a képernyők jellege módosulhat kiadásban a kiadási SAP HANA-ból.

Nincsenek figyelembe kell venni a DR-helyre feladatátvétele két kapcsolattípushoz:

- Szüksége lesz az SAP HANA-adatbázis legutóbbi állapota, valamint adatok való visszatéréshez. Ebben az esetben van egy önkiszolgáló parancsfájlt, amellyel anélkül, hogy forduljon a Microsofthoz kellene a feladatátvételt hajthat végre. Azonban a feladat-visszavételhez szüksége a Microsofthoz.
- Szeretné visszaállítani, amely nem a legújabb replikált snapshot storage pillanatkép. Ebben az esetben kell együttműködés a Microsofttal. 

>[!NOTE]
>Az alábbi lépéseket kell hajtható végre, a nagyméretű HANA-példány egységen, amely DR egységét jelöli. 
 
A legújabb replikált tároló pillanatképek visszaállításához hajtsa végre a lépéseket a szakaszban felsorolt **"Hajtsa végre a teljes Vészhelyreállítási feladatátvétel - azure_hana_dr_failover"** a dokumentum [a Microsoft Azure-beli SAP Hana pillanatkép-eszközök ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Ha több SAP HANA-példányok feladatátvételt szeretne használni, több alkalommal a azure_hana_dr_failover parancs futtatásához szükséges. Kérés esetén írja be a SAP HANA biztonsági azonosító átadja a feladatokat, és állítsa vissza. 


A tényleges replikációs kapcsolat befolyásolása nélkül is tesztelheti a Vészhelyreállítási feladatátvétel. Feladatátvételi teszt végrehajtásához kövesse **"DR feladatátvételének - azure_hana_test_dr_failover ellenőrzést végez a"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Tegye *nem* éles tranzakciók futtassa a folyamatot a DR helyen létrehozott példányon **tesztelési feladatátvétel**. A parancs azure_hana_test_dr_failover jönnek létre a köteteket, amelyek semmiféle kapcsolatban nem az elsődleges helyre. Ennek eredményeképpen a szinkronizálás az elsődleges hely van *nem* lehetséges. 

Több SAP HANA-példányok tesztelni szeretne használni, ha több alkalommal a parancsfájl futtatásához szükséges. Kérés esetén írja be a feladatátvételhez tesztelni kívánt példány SAP HANA biztonsági azonosítója. 

>[!NOTE]
>Ha átadja a feladatokat a DR hely mentési néhány órával ezelőtt törölt adatokat, és a DR-kötetek kell beállítani egy korábbi pillanatkép kell módosítania, ez az eljárás alkalmazható. 

1. Állítsa le a nem éles üzemi példány Hana HANA nagyméretű példányok, Ön által futtatott vész-helyreállítási egységen. Ennek oka az, egy előre telepített inaktív HANA éles példánya.
1. Győződjön meg arról, hogy futnak-e egyetlen SAP HANA-folyamat. Ezt az ellenőrzést a következő paranccsal: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A kimenet meg kell jelenítenie a **hdbdaemon** feldolgozása a leállított állapotba, és nincs más HANA folyamatok futó vagy elindított állapotú.
1. Határozza meg, melyik pillanatkép neve vagy az SAP HANA biztonsági másolat azonosítója szeretne használni a vész-helyreállítási webhelyként, visszaállítva. A valódi katasztrófa utáni helyreállítás esetben a pillanatkép általában a a legújabb pillanatkép. Ha az elveszett adatok visszaállítására van szüksége, válasszon ki egy korábbi pillanatkép.
1. Lépjen kapcsolatba az Azure-támogatási keresztül magas prioritású támogatási kérést. Kérje meg a visszaállításhoz, a pillanatkép (a név és a pillanatkép) vagy a HANA biztonsági másolat azonosítója a DR-helyen. Az alapértelmezett érték a műveletek oldalon, hogy visszaállítja a/hana/adatmennyiség csak. A/hana/logbackups köteteket is szeretne használni, ha kifejezetten megállapítják, hogy szeretné. *Ne állítsa vissza a /hana/shared kötetet.* Ehelyett megadott fájlok, például global.ini közül kell választania a **.snapshot** könyvtár, illetve annak alkönyvtáraiba, miután újracsatlakoztatása/hana/PRD a megosztott fürtkötet. 

   A műveletek oldalon a következő lépések történnek:

   a. A pillanatképek a termelési kötetről a vész-helyreállítási kötet replikációja le van állítva. Előfordulhat, hogy ez a megszakítás már elvégzett kell elvégeznie a vész-helyreállítási folyamattal okát az éles webhely-e.
   
   b. A storage pillanatkép neve, vagy pillanatkép készítése a biztonsági mentési azonosítójú választotta, a vész-helyreállítási kötetek visszaállítása megtörtént.
   
   c. A visszaállítás után a vész-helyreállítási kötet csatlakoztatnia kell a vészhelyreállítási régióban lévő nagyméretű HANA-példány-egységre érhető el.
      
1. Csatlakoztassa a vész helyreállítási kötetek a nagyméretű HANA-példány egységet a vész-helyreállítási helyként. 
1. Indítsa el az inaktív SAP HANA éles üzemelő példányok.
1. Ha úgy döntött, hogy másolja a tranzakciós napló biztonsági mentési naplók csökkenteni az RPO, kell ezeket a tranzakciós napló biztonsági mentéseket egyesítse az újonnan csatlakoztatott DR/hana/logbackups könyvtár. Ne írja felül a meglévő biztonsági másolatok. Másolja az újabb biztonsági mentések, amelyek nem replikálta a legutóbbi replikációs tárolási pillanatkép.
1. A pillanatképek replikálta a /hana/shared/PRD kötetre, a Vészhelyreállítás Azure-régióban egyetlen fájljait is helyreállíthatja.

A következő feladatütemezési lépéseket foglalja magában, helyreállítás, az SAP HANA éles üzemelő példányok, a storage visszaállított pillanatkép és a tranzakciónapló biztonsági mentései által biztosított alapján:

1. A biztonsági mentési helyének módosításához **/hana/logbackups** SAP HANA Studio használatával.
   ![Módosítsa a biztonsági mentési DR-helyreállítás helyének megadása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA megvizsgálja a biztonságimásolat-fájl helyeken keresztül, és a legutóbbi tranzakciónapló biztonsági mentését való visszaállításához javasol. A vizsgálat is igénybe vehet néhány percet, amíg egy képernyő, például a következő jelenik meg: ![Tranzakciónapló biztonsági mentései DR-helyreállítás listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Módosítsa az alapértelmezett beállításokat:

      - Egyértelmű **használja a különbözeti biztonsági mentések**.
      - Válassza ki **inicializálni a naplózási terület**.

   ![Az inicializálás log terület beállítása](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Válassza a **Finish** (Befejezés) elemet.

   ![A Vészhelyreállítási visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Egy folyamat ablakban is látható itt, például meg kell jelennie. Ne feledje, hogy a példában azonban egy katasztrófa utáni helyreállítást egy három csomópontos horizontális felskálázás az SAP HANA-konfiguráció.

![Visszaállítási állapota](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítás úgy tűnik, hogy nem válaszol a **Befejezés** képernyőn, és nem a folyamat képernyő megjelenítése, győződjön meg arról, hogy a feldolgozó csomópontokon futó SAP HANA-példányok futnak. Ha szükséges, indítsa el manuálisan a SAP HANA-példányokhoz.


## <a name="failback-from-a-dr-to-a-production-site"></a>A Vészhelyreállítás a feladat-visszavétel éles helyhez
A DR ból éles helyhez sikertelen. Tekintsünk meg egy olyan forgatókönyvet, amelyben a feladatátvétel, a vész-helyreállítási webhelyként okozta problémák az éles Azure-régióban, és nem az elveszett adatok helyreállításához szükséges. Ön előzetesen működniük az SAP az éles számítási feladatok egy ideig a vész-helyreállítási helyként. Az élesben használt helyet a problémák megoldásával szeretne feladat-visszavételhez az éles webhelyét. Adatok nem megszakad, mert az élesben használt helyet be újra a lépés magában foglalja a több lépéseket és az SAP HANA az Azure üzemeltetési csapat szorosan együttműködve. Van arra, hogy aktiválja az üzemeltetési csapat a problémák feloldása után vissza az élesben használt helyet a szinkronizálás elindításához.

Ez a lépés a feladatütemezés:

1. Az SAP HANA az Azure üzemeltetési csapat az eseményindító szinkronizálni az éles tárolóköteteket a vész helyreállítási tároló kötetekről, amelyek most már tartalmazzák a állapotig beolvasása. Ebben az állapotban leállítja az élesben használt helyet a nagyméretű HANA-példány egységet.
1. Az SAP HANA az Azure üzemeltetési csapat a replikáció figyeli, és gondoskodik arról, hogy ez történt arról értesíti, mielőtt.
1. Leállítja az alkalmazások, amelyek az üzemi HANA-példány használata a vész-helyreállítási helyként. Ezt követően elvégezheti egy HANA tranzakciónapló biztonsági mentését. Következő lépésként állítsa le a HANA-példány fut a vész-helyreállítási webhelyként nagyméretű HANA-példány egységek.
1. A vész-helyreállítási webhelyként, a nagyméretű HANA-példány egységben futó HANA-példány leáll, miután az üzemeltetési csapat manuálisan szinkronizálja a kötetek újra.
1. Az SAP HANA az Azure üzemeltetési csapat az élesben használt helyet a nagyméretű HANA-példány egység indul újra, és átadja is. Győződjön meg arról, hogy az SAP HANA-példány leállítási állapotban van a nagyméretű HANA-példány egység indításkor.
1. Az azonos adatbázis visszaállítási lépéseket fogja végrehajtani, korábban feladatátvétele a vész-helyreállítási webhelyként hasonló módon.

## <a name="monitor-disaster-recovery-replication"></a>Vész-helyreállítási replikálás figyeléséről

A storage replikáció folyamatának állapotát figyelemmel hajtsa végre a parancsprogramot `azure_hana_replication_status`. Ez a parancs a várt módon működik a vész-helyreállítási hely futó egységből kell futtatni. A parancs működik, függetlenül attól, hogy replikálás aktív-e. A parancs futtatható a vész-helyreállítási hely található a bérlő minden nagyméretű HANA-példány egységéhez. A rendszerindító kötet kapcsolatos részletek beszerzése érdekében nem használható. A parancs és a kimenetét részleteit olvassa el a **"DR replikációs állapotának beolvasása – azure_hana_replication_status"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>További lépések
- Tekintse meg [Monitorozás és hibaelhárítás a HANA ügyféloldali](hana-monitor-troubleshoot.md).
