---
title: HANA feladatátvételi eljárást az SAP Hana az Azure-ban (nagyméretű példányok) egy vészhelyen |} A Microsoft Docs
description: Hogyan hajthat végre a feladatátvételt egy vész-helyreállítási webhelyként, az SAP Hana az Azure-ban (nagyméretű példányok)
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
ms.openlocfilehash: f7d4f6216b4a57796ab5c0296713316dd97c47a8
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987895"
---
# <a name="disaster-recovery-failover-procedure"></a>Vészhelyreállítási feladatátvételi eljárás


>[!IMPORTANT]
>Ez a cikk nem helyettesíti a felügyeleti dokumentáció SAP HANA vagy SAP-megjegyzések. Terveink szerint már, hogy egy szilárd ismeretekkel és szaktudását a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, visszaállítás, magas rendelkezésre állás és vészhelyreállítás (DR). Ebben a cikkben beállítást mutató képernyőképek a SAP HANA Studio jelennek meg. Tartalom struktúra és az SAP-felügyeleti eszközök és az eszközök magukat a képernyők jellege módosulhat kiadásban a kiadási SAP HANA-ból.

Nincsenek két esetben érdemes figyelembe venni, amikor feladatátvételt DR-webhelyként:

- Szüksége lesz az SAP HANA-adatbázis legutóbbi állapota, valamint adatok való visszatéréshez. Ebben az esetben van egy önkiszolgáló parancsfájlt, amellyel anélkül, hogy forduljon a Microsofthoz kellene a feladatátvételt hajthat végre. A feladat-visszavételhez kell együttműködés a Microsofttal.
- Szeretné visszaállítani, amely nem a legújabb replikált snapshot storage pillanatkép. Ebben az esetben kell együttműködés a Microsofttal. 

>[!NOTE]
>Az alábbi lépéseket kell elvégezni, a nagyméretű HANA-példány egységen, amely DR egységét jelöli. 
 
A legújabb replikált tároló pillanatképek visszaállításához kövesse "Végezze el a DR feladatátvételének - azure_hana_dr_failover teljes" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Ha több SAP HANA-példányok feladatátvételt szeretne használni, futtassa többször a azure_hana_dr_failover parancsot. Kérés esetén adja meg az SAP HANA SID átadja a feladatokat, és állítsa vissza. 


A tényleges replikációs kapcsolat befolyásolása nélkül is tesztelheti a Vészhelyreállítási feladatátvétel. Feladatátvételi teszt végrehajtásához kövesse a "Végrehajtása egy teszt feladatátvétel DR - azure_hana_test_dr_failover" lépéseit [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Tegye *nem* éles tranzakciók futtassa a folyamatot a DR helyen létrehozott példányon **tesztelési feladatátvétel**. A parancs azure_hana_test_dr_failover jönnek létre a köteteket, amelyek semmiféle kapcsolatban nem az elsődleges helyre. Ennek eredményeképpen a szinkronizálás az elsődleges hely van *nem* lehetséges. 

Ha több SAP HANA-példányok tesztelni szeretne használni, futtassa a szkriptet több alkalommal. Kérés esetén adja meg az SAP HANA SID-példány feladatátvevő tesztelni szeretné. 

>[!NOTE]
>Ha átadja a feladatokat a DR hely mentési néhány órával ezelőtt törölt adatokat, és a DR-kötetek kell beállítani egy korábbi pillanatkép kell módosítania, ez az eljárás alkalmazható. 

1. Állítsa le a HANA éles példányát az HANA nagyméretű példányok, Ön által futtatott vész-helyreállítási egységen. A HANA inaktív éles üzemelő példányok előre telepítve van.
1. Győződjön meg arról, hogy futnak-e egyetlen SAP HANA-folyamat. A következő parancsot használja ezt az ellenőrzést:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A kimenet meg kell jelenítenie a **hdbdaemon** feldolgozása a leállított állapotba, és nincs más HANA folyamatok futó vagy elindított állapotú.
1. Határozza meg, melyik pillanatkép neve vagy az SAP HANA biztonsági másolat azonosítója szeretne használni a vész-helyreállítási webhelyként, visszaállítva. A valódi katasztrófa utáni helyreállítás esetben a pillanatkép általában a a legújabb pillanatkép. Ha az elveszett adatok visszaállítására van szüksége, válasszon ki egy korábbi pillanatkép.
1. Lépjen kapcsolatba az Azure-támogatási keresztül magas prioritású támogatási kérést. Kérje meg az adott nevű pillanatkép visszaállítása és a pillanatkép vagy a HANA biztonsági másolat azonosítója a DR webhelyen dátuma. Az alapértelmezett érték a műveletek oldalon, hogy visszaállítja a/hana/adatmennyiség csak. Ha szeretné a/hana/logbackups kötetek túl rendelkezik, kifejezetten megállapítják, hogy szeretné. *Ne állítsa vissza a /hana/shared kötetet.* Válassza a megadott fájlok, például global.ini közül a **.snapshot** könyvtár, illetve annak alkönyvtáraiba, miután újracsatlakoztatása/hana/PRD a megosztott fürtkötet. 

   A műveletek oldalon a következő lépések történnek:

   a. A pillanatképek a termelési kötetről a vész-helyreállítási kötet replikációja le van állítva. Előfordulhat, hogy ez a megszakítás már elvégzett kell elvégeznie a vész-helyreállítási folyamattal okát az éles webhely-e.
   
   b. A storage pillanatkép neve, vagy pillanatkép készítése a biztonsági mentési azonosítójú választotta, a vész-helyreállítási kötetek visszaállítása megtörtént.
   
   c. A visszaállítás után a vész-helyreállítási kötet csatlakoztatnia kell a vészhelyreállítási régióban lévő nagyméretű HANA-példány-egységre érhető el.
      
1. Csatlakoztassa a vész helyreállítási kötetek a nagyméretű HANA-példány egységet a vész-helyreállítási helyként. 
1. Indítsa el az inaktív SAP HANA éles üzemelő példányok.
1. Ha úgy döntött, hogy másolja a tranzakciós napló biztonsági mentési naplók csökkenteni az RPO, egyesítse a tranzakciónapló biztonsági mentései az újonnan csatlakoztatott DR/hana/logbackups könyvtárba. Ne írja felül a meglévő biztonsági másolatok. Másolja, amely nem a legújabb tárolási pillanatkép-replikációval replikált újabb biztonsági mentéseket.
1. A pillanatképek nem replikált /hana/shared/PRD kötethez a Vészhelyreállítás Azure-régióban egyetlen fájlt is helyreállíthatja.

A következő lépések bemutatják, hogyan helyreállítani az SAP HANA éles üzemelő példányok, a storage visszaállított pillanatkép és a tranzakciónapló biztonsági mentései által biztosított alapján.

1. A biztonsági mentési helyének módosításához **/hana/logbackups** SAP HANA Studio használatával.

   ![Módosítsa a biztonsági mentési DR-helyreállítás helyének megadása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA megvizsgálja a biztonságimásolat-fájl helyeken keresztül, és a legutóbbi tranzakciónapló biztonsági mentését való visszaállításához javasol. A vizsgálat is igénybe vehet néhány percet, amíg egy képernyő, például a következő jelenik meg:

   ![Tranzakciónapló biztonsági mentései DR-helyreállítás listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Módosítsa az alapértelmezett beállításokat:

      - Egyértelmű **használja a különbözeti biztonsági mentések**.
      - Válassza ki **inicializálni a naplózási terület**.

   ![Az inicializálás log terület beállítása](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Válassza a **Finish** (Befejezés) elemet.

   ![A Vészhelyreállítási visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Egy folyamat ablakban is látható itt, például meg kell jelennie. Ne feledje, hogy a példában azonban egy katasztrófa utáni helyreállítást egy három csomópontos horizontális felskálázás az SAP HANA-konfiguráció.

![Visszaállítási állapota](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítás leáll a **Befejezés** képernyőn, és nem a folyamat képernyője megjelenítése, győződjön meg arról, hogy a feldolgozó csomópontokon futó SAP HANA-példányok futnak. Ha szükséges, indítsa el manuálisan a SAP HANA-példányokhoz.


## <a name="failback-from-a-dr-to-a-production-site"></a>A Vészhelyreállítás a feladat-visszavétel éles helyhez
A DR ból éles helyhez sikertelen. Tekintsünk meg egy olyan forgatókönyvet, amelyben a feladatátvétel, a vész-helyreállítási webhelyként okozta problémák az éles Azure-régióban, és nem az elveszett adatok helyreállításához szükséges. 

Futtatja, a SAP éles számítási feladat egy ideig a vész-helyreállítási helyként. Az élesben használt helyet a problémák megoldásával szeretne feladat-visszavételhez az éles webhelyét. Adatok nem megszakad, mert az élesben használt helyet be újra a lépés magában foglalja a több lépéseket és az SAP HANA az Azure üzemeltetési csapat szorosan együttműködve. Van arra, hogy aktiválja az üzemeltetési csapat a problémák feloldása után vissza az élesben használt helyet a szinkronizálás elindításához.

Kövesse az alábbi lépéseket:

1. Az SAP HANA az Azure üzemeltetési csapat az eseményindító szinkronizálni az éles tárolóköteteket a vész helyreállítási tároló kötetekről, amelyek most már tartalmazzák a állapotig beolvasása. Ebben az állapotban leállítja az élesben használt helyet a nagyméretű HANA-példány egységet.
1. Az SAP HANA az Azure üzemeltetési csapat figyeli a replikálást, és gondoskodik arról, hogy azt történt előtt tájékoztatni Önt.
1. Leállítja az alkalmazások, amelyek az üzemi HANA-példány használata a vész-helyreállítási helyként. Ezt követően elvégezheti egy HANA tranzakciónapló biztonsági mentését. Következő lépésként állítsa le a HANA-példány, amelyen fut, a nagyméretű HANA-példány egységig bővíthetik a vész-helyreállítási helyként.
1. A HANA-példányt, amely futtatja a nagyméretű HANA-példány egységet a vész-helyreállítási webhelyként leáll, miután az üzemeltetési csapat manuálisan szinkronizálja a kötetek újra.
1. Az SAP HANA az Azure üzemeltetési csapat indítja el a HANA nagyméretű példányok egység az élesben használt helyet újra. Ezek közreműködése is. Győződjön meg arról, hogy az SAP HANA-példány leállítási állapotban van a nagyméretű HANA-példány egység indításkor.
1. Az azonos adatbázis visszaállítási lépéseket, amelyet korábban nem adja a feladatát a vész-helyreállítási webhelyként során fogja végrehajtani.

## <a name="monitor-disaster-recovery-replication"></a>Vész-helyreállítási replikálás figyeléséről

A storage-replikáció állapotát állapotának figyelése, futtassa a szkriptet `azure_hana_replication_status`. Ez a parancs, amely a vész-helyreállítási hely elvárt egységből származó kell futtatni. A parancs működik, függetlenül attól, hogy replikálás aktív-e. A parancs futtatható a vész-helyreállítási hely található a bérlő minden nagyméretű HANA-példány egységéhez. A rendszerindító kötet kapcsolatos részletek beszerzése érdekében nem használható. 

A parancs, és a kimenetét további információkért lásd: "Get DR replikációs állapota – azure_hana_replication_status" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>További lépések
- Lásd: [figyelése és hibaelhárítása HANA oldaláról](hana-monitor-troubleshoot.md).
