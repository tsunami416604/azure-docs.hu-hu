---
title: HANA feladatátvételi eljárás az SAP HANA vészelőhelyéhez az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Feladatátvétel végrehajtása az SAP HANA vész-helyreállítási helyén az Azure-ban (nagy példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617139"
---
# <a name="disaster-recovery-failover-procedure"></a>Vészhelyreállítási feladatátvételi eljárás


>[!IMPORTANT]
>Ez a cikk nem helyettesíti az SAP HANA felügyeleti dokumentációt vagy az SAP Notes. Azt várjuk, hogy az SAP HANA felügyeletének és műveleteinek alapos megértése és szakértelme, különösen a biztonsági mentés, visszaállítás, a magas rendelkezésre állás és a vészhelyreállítás (DR) esetében. Ebben a cikkben az SAP HANA Studio képernyőképei jelennek meg. Tartalom, struktúra, és az SAP felügyeleti eszközök képernyőinek és maguk az eszközök jellege is változhat az SAP HANA kiadás kiadására.

Két esetet kell figyelembe venni, amikor egy VÉSZ-helyre átad:

- Az SAP HANA-adatbázisnak vissza kell lépnie az adatok legújabb állapotához. Ebben az esetben van egy önkiszolgáló parancsfájl, amellyel a feladatátvételt anélkül hajthatja végre, hogy kapcsolatba kellene lépnie a Microsofttal. A feladat-visszavételhez a Microsofttal kell együttműködnie.
- Vissza szeretné állítani egy tárolási pillanatképet, amely nem a legújabb replikált pillanatkép. Ebben az esetben a Microsofttal kell együttműködnie. 

>[!NOTE]
>A következő lépéseket kell elvégezni a HANA nagy példány egység, amely a VÉSZ-egység. 
 
A legújabb replikált tárolási pillanatképek visszaállításához kövesse a "Teljes VÉSZ-feladatátvétel végrehajtása – azure_hana_dr_failover" című, az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)lépéseit. 

Ha azt szeretné, hogy több SAP HANA-példányok feladatátvételt, futtassa a azure_hana_dr_failover parancsot többször. Ha kérik, adja meg az SAP HANA SID átvenni és visszaállítani. 


A VÉSZ-feladatátvételt a tényleges replikációs kapcsolat befolyásolása nélkül is tesztelheti. A tesztfeladat-átvétel végrehajtásához kövesse a "Teszt VÉSZ-feladatátvétel végrehajtása – azure_hana_test_dr_failover" című, az [Azure-beli SAP HANA Microsoft-pillanatkép-eszközeiben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)leírt lépéseket. 

>[!IMPORTANT]
>*Ne* futtasson éles tranzakciókat a vész-vész-fejlesztési helyen létrehozott példányon a **feladatátvétel tesztelése**során. A azure_hana_test_dr_failover parancs olyan köteteket hoz létre, amelyek nem állnak kapcsolatban az elsődleges helyével. Ennek eredményeképpen az elsődleges helyhez való visszaszinkronizálás *nem* lehetséges. 

Ha azt szeretné, hogy több SAP HANA-példányok teszteléséhez, futtassa a parancsfájlt többször. Ha kérik, adja meg az SAP HANA-sid a feladatátvételt tesztelni kívánt példány. 

>[!NOTE]
>Ha át kell adnia a vészhelyre a vészhelyre, hogy megmentsen néhány, órákkal ezelőtt törölt adatot, és a VÉSZ-köteteket egy korábbi pillanatképre kell állítania, ez az eljárás érvényes. 

1. Állítsa le a hana nem éles példányát a hana nagy példányok vész-helyreállítási egységén, amely fut. Egy alvó HANA éles példány előre telepítve van.
1. Győződjön meg arról, hogy nem futnak SAP HANA-folyamatok. Az ellenőrzéshez használja a következő parancsot:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A kimenet nek meg kell mutatnia a **hdbdaemon** folyamat leállított állapotban, és nincs más HANA folyamatok futó vagy elindított állapotban.
1. Határozza meg, hogy melyik pillanatkép nevét vagy AZ SAP HANA biztonsági mentési azonosítót szeretné, hogy a vész-helyreállítási hely helyreáll. Valós vész-helyreállítási esetekben ez a pillanatkép általában a legújabb pillanatkép. Ha helyre kell állítania az elveszett adatokat, válasszon egy korábbi pillanatképet.
1. Lépjen kapcsolatba az Azure-támogatással egy magas prioritású támogatási kérelemen keresztül. Kérje meg a pillanatkép visszaállítását a pillanatkép nevével és dátumával, vagy a HANA biztonsági másolat azonosítójával a VÉSZ-webhelyen. Az alapértelmezett beállítás az, hogy a műveleti oldal csak a /hana/data kötetet állítja vissza. Ha azt szeretné, hogy a /hana/logbackups kötetek is, meg kell, hogy kifejezetten azt. *Ne állítsa vissza a /hana/shared kötetet.* Ehelyett válassza ki a .snapshot könyvtárból és annak alkönyvtáraiból a /hana/shared volume for PRD /hana/shared volume fájl újracsatlakoztatása után a **.snapshot** könyvtárból és annak alkönyvtáraiból a global.ini fájlt. 

   A műveletek oldalán a következő lépések történnek:

   a. A pillanatképek replikációja a termelési kötetről a vész-helyreállítási kötetek leáll. Ez a megszakítás már előfordulhat, hogy már megtörtént, ha az éles helyen egy kimaradás az oka a vész-helyreállítási eljárás végrehajtásához.
   
   b. A tárolási pillanatkép nevét vagy pillanatképet a biztonsági azonosítót választott visszaállításra kerül a vész-helyreállítási köteteken.
   
   c. A visszaállítás után a vész-helyreállítási kötetek érhetők el a HANA nagy példány egységek a vész-helyreállítási régióban.
      
1. Csatlakoztassa a vész-helyreállítási kötetek a HANA nagy példány egység a vész-helyreállítási hely. 
1. Indítsa el a nyugvó SAP HANA termelési példány.
1. Ha úgy döntött, hogy a tranzakciónapló biztonsági mentési naplóit másolja az RPO-idő csökkentése érdekében, egyesítse a tranzakciónapló biztonsági másolatait az újonnan csatlakoztatott DR /hana/logbackups könyvtárba. Ne írja felül a meglévő biztonsági mentéseket. Másolja az újabb biztonsági másolatokat, amelyek nem replikáltak a tárolási pillanatkép legutóbbi replikációjával.
1. A DR Azure-régióban a /hana/shared/PRD-kötetre nem replikált pillanatképekből egyetlen fájlt is visszaállíthat.

A következő lépések bemutatják, hogyan állíthatja helyre az SAP HANA éles példány a visszaállított tárolási pillanatkép és a tranzakciónapló biztonsági mentések, amelyek rendelkezésre állnak.

1. Módosítsa a biztonsági mentés helyét **/hana/logbackups** az SAP HANA Studio használatával.

   ![A vészhelyreállítás biztonsági mentési helyének módosítása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. Az SAP HANA átvizsgálja a biztonsági másolat fájl helyeit, és javasolja a legutóbbi tranzakciónapló biztonsági mentését a visszaállításhoz. A vizsgálat eltarthat néhány percig, amíg a képernyő, mint a következő jelenik meg:

   ![A vészhelyreállítás tranzakciós naplójának biztonsági másolatainak listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Az alapértelmezett beállítások némelyikének módosítása:

      - Delta **biztonsági mentések használatának törlése**.
      - Válassza **a Naplóterület inicializálása**lehetőséget.

   ![A naplóterület inicializálása](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Válassza a **Finish** (Befejezés) elemet.

   ![A VÉSZ-visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Meg kell jelennie egy folyamatjelző ablaknak, mint az itt látható. Ne feledje, hogy a példa egy háromcsomópontos kibővített SAP HANA-konfiguráció vész-helyreállítási visszaállítása.

![A folyamat visszaállítása](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítás nem válaszol a **Befejezés** képernyőn, és nem jeleníti meg a folyamatjelző képernyőt, ellenőrizze, hogy a munkavégző csomópontokon lévő összes SAP HANA-példány fut-e. Ha szükséges, indítsa el az SAP HANA-példányok manuálisan.


## <a name="failback-from-a-dr-to-a-production-site"></a>Feladat-visszavétel a DR-ről egy éles helyre
A vész-visszavétel egy éles környezetben. Nézzük meg a forgatókönyvet, amelyben a feladatátvétela a vész-helyreállítási hely okozta problémák az éles Azure-régióban, és nem az elveszett adatok helyreállításának szükségességét. 

Már fut az SAP éles számítási feladatok egy ideig a vész-helyreállítási helyen. Az éles hely problémáinak megoldása esetén vissza szeretne adni a termelési helynek. Mivel nem veszíti el az adatokat, az éles környezetbe való visszalépés több lépést foglal magában, és szoros együttműködést az SAP HANA-val az Azure műveleti csapatában. Az Ön önön múlik, hogy az operatív csapat a problémák megoldása után elindítsa a szinkronizálást az éles helyszínre.

Kövesse az alábbi lépéseket:

1. Az SAP HANA az Azure-műveletek csapat leváltja az eseményindító szinkronizálni az éles tárolási kötetek a vész-helyreállítási tárolási kötetek, amelyek most képviselik az éles állapot. Ebben az állapotban a HANA nagy példány egység a termelési helyen le van állítva.
1. Az SAP HANA az Azure-műveletek csapat figyeli a replikációt, és győződjön meg arról, hogy az utolérte, mielőtt tájékoztatja Önt.
1. Állítsa le az éles HANA-példányt használó alkalmazásokat a vész-helyreállítási helyen. Ezután végre egy HANA tranzakciónapló biztonsági mentést. Ezután állítsa le a HANA-példány, amely a vész-helyreállítási helyen a HANA nagy példány egységek fut.
1. Miután a HANA-példány, amely fut a HANA nagy példány egység a vész-helyreállítási hely leáll, az operatív csapat manuálisan szinkronizálja a lemezkötetek újra.
1. Az SAP HANA az Azure-műveletek csapat elindítja a HANA nagy példány egységét az éles helyen újra. Átadják neked. Győződjön meg arról, hogy az SAP HANA-példány leállítási állapotban van a HANA nagy példány egység indítási időpontjában.
1. Ugyanazokat az adatbázis-visszaállítási lépéseket hajtja végre, mint amikor korábban átadott feladatátvételt a vész-helyreállítási helyre.

## <a name="monitor-disaster-recovery-replication"></a>Vészhelyreállítás replikációjának figyelése

A tárolóreplikáció állapotának figyeléséhez futtassa a parancsfájlt. `azure_hana_replication_status` Ezt a parancsot a vész-helyreállítási helyen futó egységről kell futtatni, hogy a várt módon működjön. A parancs működik, függetlenül attól, hogy a replikáció aktív. A parancs futtatható a bérlő minden HANA nagy példányegysége a vész-helyreállítási helyen. Nem használható a rendszerindító kötet részleteinek megszerzésére. 

A parancsról és annak kimenetéről további információt az [Azure-beli SAP HANA Microsoft-pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)"Vészbiztonsági szolgáltatás állapotának beazure_hana_replication_status" című témakörében talál.


## <a name="next-steps"></a>További lépések
- Lásd: [A HANA oldalának figyelése és hibaelhárítása.](hana-monitor-troubleshoot.md)
