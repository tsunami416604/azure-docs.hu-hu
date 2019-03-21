---
title: A biztonsági mentési Exec biztonsági mentési célként a StorSimple 8000 sorozat |} A Microsoft Docs
description: A StorSimple Veritas Backup Exec biztonsági mentési cél konfiguráció ismerteti.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: e11d541f0450c0de4ba6d60f889fc7471b1fa1aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011142"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>A biztonsági mentési Exec a StorSimple mint biztonsági mentési cél

## <a name="overview"></a>Áttekintés

Az Azure StorSimple hibrid felhőalapú tárolási megoldást a Microsoft a. A StorSimple-címek kiküszöböli az exponenciális Adatnövekedés kiterjesztése a helyszíni megoldás, mint az Azure storage-fiók használatával, és automatikusan rétegezést adatok között a helyszíni és felhőbeli tárolására.

Ebben a cikkben bemutatjuk a StorSimple-integráció, a Veritas Backup Execkel és a két megoldás integrálására vonatkozó ajánlott eljárásokat. Azt is ajánlásokat a legjobb integrálása a StorSimple Backup Execkel beállítása. Hogy késlelteti a Veritas ajánlott eljárásokat, biztonsági mentési tervezők és rendszergazdái a legjobb módszer beállítása a biztonsági mentési Exec egyedi biztonsági mentési követelményeknek és a szolgáltatásiszint-szerződések (SLA).

Azt illusztrálja, konfigurációs lépésekkel és alapfogalmait, bár ez a cikk semmiképpen sem részletes konfigurációs vagy telepítési útmutató. Feltételezzük, hogy az alapszintű összetevőkre és infrastruktúrákra működő sorrendben, és készen áll a fogalmakat, amelyek ismertetünk támogatja.

### <a name="who-should-read-this"></a>Ez célközönsége?

Ebben a cikkben található információk a biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolási architects ismerő, storage, a Windows Server 2012 R2, Ethernet, a cloud services és Backup Exec hasznos lesz.

## <a name="supported-versions"></a>Támogatott verziók

-   [Biztonsági mentési Exec 16 és újabb verziók](http://backupexec.com/compatibility)
-   [A StorSimple Update 3 és újabb verziók](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Miért érdemes a StorSimple mint biztonsági mentési cél?

A StorSimple megfelelő választás az olyan biztonsági mentési cél azért, mert:

-   A biztonsági mentési alkalmazások módosítása nélkül a gyors biztonsági mentési célként használni standard, helyi tárolást biztosít. Is használhatja a StorSimple a legújabb biztonsági mentések egy gyors helyreállítás.
-   A felhőbeli rétegezés zökkenőmentesen integrálódik az költséghatékony Azure Storage használatát egy Azure cloud storage-fiókot.
-   Külső helyszíni tárolás vészhelyreállításhoz automatikusan biztosít.

## <a name="key-concepts"></a>Fő fogalmak

Csakúgy, mint bármely tárolási megoldás, a megoldás tárolási teljesítményt, SLA-k, gondosan értékelése a változások és kapacitásigények növekedési aránya, kritikus fontosságú sikeres. Lényege, hogy a felhő szintjén, az elérés időpontját és a felhő Play alapvető szerepet ehhez a StorSimple lehetővé teszi a termékváltozatot bevezetésével.

A StorSimple célja egy jól definiált munkakészletének (gyakori elérésű) adatokat a tárolás olyan alkalmazások, amelyek működéséhez. Ebben a modellben a helyi rétegeken tárolt adatok munkakészletének, és a fennmaradó Szabadnap, ritka elérésű és archivált adatok készletét többszintű a felhőbe. Ez a modell az alábbi ábrán jelölt. Az egyszerű szinte zöld vonal a helyi rétegeken, a StorSimple-eszközön tárolt adatokat jelöli. A piros vonal minden csomag a StorSimple megoldás a tárolt adatok teljes mennyisége jelenti. A fix keretösszegek zöld vonal és az exponenciális piros görbe közötti távolságot a felhőben tárolt teljes adatmennyiség jelöli.

**A StorSimple-rétegezést**
![StorSimple rétegezési diagramja](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Ezzel az architektúrával szem előtt láthatja, hogy a StorSimple biztonsági mentési célként a művelethez használandó kiválóan alkalmazható. A StorSimple használhatja:
-   Az adatok helyi munkakészletének hajtsa végre a leggyakoribb visszaállítások.
-   Visszaállítás esetén ritkábban forduljanak a felhő segítségével a külső helyszíni vészhelyreállításra és a régebbi adatokat.

## <a name="storsimple-benefits"></a>A StorSimple előnyei

A StorSimple-helyszíni megoldást nyújt, amely zökkenőmentesen integrálódik a Microsoft Azure-ral zökkenőmentes hozzáférést előnyeit kihasználva a helyszíni és felhőbeli tárhelyén.

A StorSimple használ a helyszíni eszköz, amely tartós állapotú eszköz (SSD) és a soros csatlakozású SCSI (SAS) tárolók rendelkezik, és az Azure Storage között az automatikus rétegezést. Automatikus rétegezést tartja a gyakran használt adatok helyi, az SSD és a SAS rétegeken. Az Azure Storage áthelyezi ritkán használt adatokhoz.

A StorSimple ezeket az előnyöket kínálja:

-   A felhő páratlan deduplikáció szintek eléréséhez használó egyedi deduplikáció és a tömörítés algoritmusok
-   Magas rendelkezésre állás
-   Georeplikáció az Azure georeplikáció használatával
-   Az Azure-integráció
-   Adattitkosítás a felhőben
-   Továbbfejlesztett vész-helyreállítási és megfelelőség

Bár a StorSimple alapvetően a két fő üzembe helyezési forgatókönyvet (elsődleges biztonsági mentési cél és a másodlagos biztonsági mentési cél) mutat be, egy egyszerű, block storage eszköz. A StorSimple does minden a tömörítés és a deduplikáció. Zökkenőmentesen küld, és lekéri az adatokat a felhőben és az alkalmazások és a fájlrendszer között.

További információ a StorSimple: [a StorSimple 8000 sorozat: Hibrid felhőalapú tárolási megoldást](storsimple-overview.md). Emellett áttekintheti a [StorSimple 8000 sorozat műszaki specifikációk](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> A StorSimple eszköz biztonsági mentési cél használata támogatott csak az a StorSimple 8000-es Update 3 és újabb verziókban.

## <a name="architecture-overview"></a>Az architektúra áttekintése

Az alábbi táblázatokban a modell-architektúra kezdeti útmutató eszközök.

**A helyi StorSimple-kapacitások és a felhőalapú tárolás**

| Tárkapacitás       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Helyi tárolási kapacitás | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Felhőalapú tárolási kapacitás | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Tárméret azt feltételezi, nem a deduplikáció és a tömörítést.

**A StorSimple kapacitások elsődleges és másodlagos biztonsági mentések tiltása**

| Biztonsági mentési forgatókönyv  | Helyi tárolási kapacitás  | Felhőalapú tárolási kapacitás  |
|---|---|---|
| Elsődleges biztonsági mentési  | Legutóbbi biztonsági másolatokat készítenie, a gyors helyreállításra helyi tárban tárolt helyreállítási időkorlátot (RPO) | A biztonsági mentési előzményeit (RPO) elfér a felhő kapacitása |
| Másodlagos biztonsági mentések | Másodlagos példány biztonsági mentési adatok tárolhatók a felhő kapacitása  | –  |

## <a name="storsimple-as-a-primary-backup-target"></a>A StorSimple mint elsődleges biztonsági mentési cél

Ebben a forgatókönyvben a StorSimple-köteteket a biztonsági mentési alkalmazásnak, mint a biztonsági mentések egyetlen tárház jelennek meg. A következő ábrán látható a megoldás architektúrája, amelyben az összes biztonsági mentés használata a StorSimple rétegzett kötetek biztonsági mentéseket és helyreállításokat.

![A StorSimple mint egy elsődleges biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>A cél elsődleges biztonsági mentési logikai lépésekre

1.  A biztonsági mentési kiszolgálóra kapcsolatba lép a biztonsági mentési célügynököt a számítógépre, és a biztonsági mentési ügynök továbbítja az adatokat a biztonsági mentési kiszolgálóra.
2.  A biztonsági mentési kiszolgálóra írja az adatokat a StorSimple a rétegzett köteteket.
3.  A biztonsági mentési kiszolgálóra frissíti a katalógus-adatbázisban, és ezután befejezi a biztonsági mentési feladat.
4.  A pillanatkép-parancsfájl elindítja a StorSimple snapshot felhőkezelő (kezdő vagy törlés).
5.  A biztonsági mentési kiszolgálóra törli a lejárt biztonsági mentés a megőrzési házirend alapján.


### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállítási logikai lépésekre

1.  A biztonsági mentési kiszolgálóra indítja el a megfelelő adatok visszaállítása a storage-adattárból.
2.  A backup-ügynök az adatokat fogad a biztonsági mentési kiszolgálóra.
3.  A biztonsági mentési kiszolgálóra a visszaállítási feladat befejeződik.

## <a name="storsimple-as-a-secondary-backup-target"></a>A StorSimple mint másodlagos biztonsági mentési cél

Ebben a forgatókönyvben StorSimple-kötetek vannak elsősorban a hosszú távú megőrzés és archiválása.

Az alábbi ábra az architektúra látható melyik kezdeti biztonsági mentés, és visszaállítja a célkötet egy nagy teljesítményű. Ezeket a biztonsági másolatokat másolják és a egy StorSimple archivált rétegzett kötet egy beállított ütemezés szerint.

Fontos a nagy teljesítményű kötet méretezéséhez, így képes kezelni a megőrzési házirend kapacitás és teljesítmény követelményeinek.

![A StorSimple mint egy másodlagos biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Másodlagos cél biztonsági mentési logikai lépésekre

1.  A biztonsági mentési kiszolgálóra kapcsolatba lép a biztonsági mentési célügynököt a számítógépre, és a biztonsági mentési ügynök továbbítja az adatokat a biztonsági mentési kiszolgálóra.
2.  A biztonsági mentési kiszolgálóra írja az adatokat a nagy teljesítményű tárolást.
3.  A biztonsági mentési kiszolgálóra frissíti a katalógus-adatbázisban, és ezután befejezi a biztonsági mentési feladat.
4.  A biztonsági mentési kiszolgálóra másol adatmegőrzési alapuló StorSimple biztonsági mentéseket.
5.  A pillanatkép-parancsfájl elindítja a StorSimple snapshot felhőkezelő (kezdő vagy törlés).
6.  A biztonsági mentési kiszolgálóra törli a lejárt biztonsági mentés a megőrzési házirend alapján.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél visszaállítási logikai lépésekre

1.  A biztonsági mentési kiszolgálóra indítja el a megfelelő adatok visszaállítása a storage-adattárból.
2.  A backup-ügynök az adatokat fogad a biztonsági mentési kiszolgálóra.
3.  A biztonsági mentési kiszolgálóra a visszaállítási feladat befejeződik.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A megoldás üzembe helyezése három lépésből áll:
1. Készítse elő a hálózati infrastruktúrát.
2. A StorSimple eszköz üzembe helyezése egy biztonsági mentési célként.
3. Helyezze üzembe a biztonsági mentési Exec.

Az egyes lépések a következő szakaszokban részletesen tárgyalja.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple olyan megoldás, amely integrálva van az Azure-felhőben, a StorSimple szükséges egy aktív és az Azure-felhőben működő kapcsolatot. Ezt a kapcsolatot a műveletek, mint például a felhőbeli pillanatképekkel, a felügyeleti és a metaadatok adatforgalom szolgál, és régebbi szintre, kisebb használt adatokat pedig az Azure felhőalapú tárolást.

A megoldás optimális működéséhez javasoljuk, hogy kövesse az ajánlott eljárások követésével hálózati:

-   A hivatkozás, amely a StorSimple rétegezés szolgáltatással az Azure-meg kell felelnie a sávszélesség-követelmények. Ennek érdekében érvényesek a szolgáltatásminőség (QoS) szükséges szintjét az adott infrastruktúrához vált felel meg a helyreállítási Időkorlát és a helyreállítási idő célkitűzése (RTO) SLA-k.
-   Maximális Azure Blob storage hozzáférési késések körülbelül 80 ms kell lennie.

### <a name="deploy-storsimple"></a>A StorSimple üzembe helyezése

A részletes StorSimple üzembe helyezési útmutatót lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Biztonsági mentési Exec üzembe helyezése

Backup Execkel gyakorlati tanácsok a telepítéshez, lásd: [gyakorlati tanácsok a telepítéshez Backup Execkel](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>A megoldás beállítása

Ebben a szakaszban bemutatjuk, hogyan konfigurációs példákat. A következő példák és javaslatok bemutatják az alapszintű és a alapvető végrehajtására. Ez a megvalósítás nem feltétlenül vonatkoznak közvetlenül az adott biztonsági mentés követelményeinek.

### <a name="set-up-storsimple"></a>A StorSimple beállítása

| A StorSimple üzembe helyezési feladatok  | További megjegyzések |
|---|---|
| A helyszíni StorSimple eszköz üzembe helyezése. | Támogatott verziók: 3. frissítés és újabb verziókban. |
| Kapcsolja be a biztonsági mentési cél. | A parancsok használatához kapcsolja be, vagy kapcsolja ki a biztonsági mentési cél módot, és állapotának beolvasása. További információkért lásd: [távolról csatlakozhat a StorSimple eszköz](storsimple-remote-connect.md).</br> Kapcsolja be a biztonsági mentés módja: `Set-HCSBackupApplianceMode -enable`. </br> Tiltsa le a biztonsági mentés módja: `Set-HCSBackupApplianceMode -disable`. </br> A biztonsági mentési beállításainak aktuális állapotának: `Get-HCSBackupApplianceMode`. |
| A kötet, amely tárolja a biztonsági mentési adatok közös kötettároló létrehozása. A kötettároló összes adat deduplikált. | A StorSimple-kötettároló deduplikáció tartományok definiálása.  |
| A StorSimple-köteteket hozhat létre. | Hozzon létre köteteket méretű, a várható használati közeli lehetséges, mert a kötet mérete befolyásolja a felhőbeli pillanatkép időtartamát. A kötet méretének kapcsolatos információkért olvassa el [adatmegőrzési szabályzatok](#retention-policies).</br> </br> Az StorSimple rétegzett köteteket, és válassza ki a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet. </br> Csak a helyileg rögzített kötetekről használata nem támogatott. |
| A biztonsági mentési cél-kötetek egyedi StorSimple biztonsági mentési szabályzat létrehozása. | A StorSimple biztonsági mentési szabályzat határozza meg a kötet konzisztencia csoport. |
| Tiltsa le az ütemezés a, a pillanatképek lejár. | A pillanatképek utófeldolgozási műveletként aktiválódnak. |

### <a name="set-up-the-host-backup-server-storage"></a>A gazdagép kiszolgáló biztonsági mentési tároló beállítása

Állítsa be a kiszolgáló biztonsági mentési tárolók szerint ezeket az irányelveket:  

- Ne használjon átnyúló kötetek (a Windows Lemezkezelés által létrehozott). A program lemezek használata nem támogatott.
- Formázza a köteteket, az NTFS Fájlrendszerrel 64 KB-os mérettel.
- Közvetlenül a Backup Execkel kiszolgáló képezze le a StorSimple-köteteket.
    - ISCSI használata a fizikai kiszolgálók számára.
    - Csatlakoztatott lemez használata a virtuális kiszolgálók.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Ajánlott eljárások a StorSimple és a Backup Execkel

Állítsa be az igényeinek megfelelően a megoldás az alábbi szakaszokban található.

### <a name="operating-system-best-practices"></a>Operációs rendszer ajánlott eljárások

- Tiltsa le a Windows Server titkosítás és az adatdeduplikáció az NTFS fájlrendszerhez.
- Tiltsa le a StorSimple-köteteket a Windows Server töredezettségmentesítése.
- A StorSimple-köteteket a Windows Server-indexelés letiltása.
- (Nem a StorSimple-kötetek) szemben a forrás gazdagép víruskeresést futtatni.
- Kapcsolja ki az alapértelmezett [Windows Server karbantartási](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) a Feladatkezelő. Ehhez a következő módszerek valamelyikével:
  - Kapcsolja ki a karbantartási konfiguráló a Windows Feladatütemező.
  - Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) a Windows Sysinternals. PsExec a letöltés után futtató Azure Powershellt rendszergazdaként, majd írja be:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>A StorSimple-ajánlott eljárások

  -   Győződjön meg, hogy a StorSimple-eszköz frissül, és [Update 3 vagy újabb](storsimple-install-update-3.md).
  -   Elkülönítheti az iSCSI és a felhőalapú forgalom. Dedikált iSCSI-kapcsolatok használata a StorSimple és a biztonsági mentési kiszolgálóra közötti adatforgalmat.
  -   Győződjön meg arról, hogy a StorSimple-eszköz-e a biztonsági mentési cél dedikált. Vegyes számítási feladatok nem támogatottak, mert ezek hatással az RTO és RPO.

### <a name="backup-exec-best-practices"></a>Biztonsági mentési Exec ajánlott eljárások

-   Biztonsági mentési Exec telepítve kell lennie, a kiszolgáló helyi meghajtón, és nem a StorSimple-kötet.
-   Állítsa be a Backup Execkel tárolási **párhuzamosan futó olvasási műveletek** megengedettet.
    -   Állítsa be a Backup Execkel tárolási **blokk- és puffer mérete** 512 KB.
    -   Kapcsolja be a Backup Execkel tárolási **olvasási és írási pufferelt**.
-   A StorSimple támogatja a Backup Execkel teljes és növekményes biztonsági mentéseket. Azt javasoljuk, hogy nem használhat szintetikus és különbségi biztonsági mentés.
-   Biztonsági mentési adatok fájljaihoz tartalmazhat csak egy adott feladat adatai. Nincs adathordozó például fűzi hozzá több különböző feladatok használata engedélyezett.
-   Tiltsa le a feladat-ellenőrzést. Ha szükséges, ellenőrzése után a legújabb biztonsági mentési feladat lehet ütemezni. Fontos megérteni, hogy ez a feladat a biztonsági mentés időszakának érinti-e.
-   Válassza ki **tárolási** > **a lemez** > **részletek** > **tulajdonságok**. Kapcsolja ki a **előre a lemezterület lefoglalása**.

A legújabb Backup Execkel beállításokat és ezek a követelmények megvalósításához ajánlott eljárások: [a Veritas webhely](https://www.veritas.com).

## <a name="retention-policies"></a>Adatmegőrzési házirendek

A leggyakrabban használt biztonsági másolatok megőrzési házirend-típusainak egyikét egy, a Nagyapa Father és Lányomtól (GFS) szabályzat. GFS házirendben a növekményes biztonsági mentés naponta történik, és teljes biztonsági mentés heti és havi végzett. A csoportházirend-eredményhez hat StorSimple-ben rétegzett köteteket. Egy kötet a heti, havi és éves teljes biztonsági mentést tartalmaz. A többi öt kötetek napi növekményes biztonsági másolatok tárolására.

A következő példában egy GFS Elforgatás használjuk. A példa feltételezi, hogy a következő:

-   A nem deduplikált vagy tömörített adatok szolgál.
-   Teljes biztonsági mentés olyan minden 1 Tib-ra.
-   Napi növekményes biztonsági mentések 500 GiB.
-   Négy heti biztonsági mentései egy hónapig őrzi meg.
-   12 havi biztonsági mentések egy évig őrzi meg.
-   Egy éves biztonsági másolatok 10 évig.

Az előző Előfeltevések alapján hozzon létre egy 26-TiB StorSimple rétegzett kötet a havi és éves teljes biztonsági mentéseket. Hozzon létre egy 5 TiB StorSimple rétegzett kötet a napi növekményes biztonsági mentések mindegyike esetében.

| Biztonsági másolat megőrzése | Méret (TiB) | GFS szorzó\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Heti teljes | 1 | 4  | 4 |
| Napi növekményes | 0,5 | 20 (ciklusok egyenlő hetek száma / hó) | 12 (2 további kvótáért.) |
| Teljes havi | 1 | 12 | 12 |
| Éves teljes | 1  | 10 | 10 |
| GFS követelmény |   | 38 |   |
| További kvótát  | 4  |   | 42 teljes GFS követelmény  |

\* GFS szorzó példányszám történő védelme, és a biztonsági mentési szabályzat követelményeinek szüksége.

## <a name="set-up-backup-exec-storage"></a>Backup Execkel tárolás beállítása

### <a name="to-set-up-backup-exec-storage"></a>A Backup Execkel tárolás beállítása

1.  A Backup Execkel felügyeleti konzolban válassza **tárolási** > **tárolás konfigurálása** > **lemezes tárolás**  >   **Tovább**.

    ![Biztonsági mentési Exec felügyeleti konzolt, a storage lapjának konfigurálása](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Válassza ki **Disk Storage**, majd válassza ki **tovább**.

    ![Biztonsági mentési Exec felügyeleti konzolon válassza tárolási lap](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Adjon meg egy reprezentatív nevet, például **szombat teljes**, és egy leírást. Kattintson a **Tovább** gombra.

    ![Biztonsági mentési Exec felügyeleti konzolt, neve és leírása lap](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Jelölje ki a lemezt, ahol szeretné létrehozni a lemez tárolási eszközt, és válassza ki **tovább**.

    ![Biztonsági mentési Exec felügyeleti konzol, storage-lemez kiválasztása lap](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Az írási műveletek száma növekszik **16**, majd válassza ki **tovább**.

    ![Biztonsági mentési Exec felügyeleti konzol, egyidejű írási műveleti beállítások lap](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Tekintse át a beállításokat, és válassza ki **Befejezés**.

    ![Biztonsági mentési Exec felügyeleti konzol, tárolási konfiguráció összegző lapja](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Minden kötet hozzárendelés végén javasolt megfelelő tárolási eszköz beállításainak módosítása [ajánlott eljárások a StorSimple és a Backup Execkel](#best-practices-for-storsimple-and-backup-exec).

    ![Biztonsági mentési Exec felügyeleti konzol, tárolási eszköz beállításai lapon](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Elkészült, a StorSimple-köteteket hozzárendelése Backup Execkel addig ismételje a 1-7.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Állítsa be a StorSimple elsődleges biztonsági mentési cél

> [!NOTE]
> Adatok visszaállítása biztonsági tartozik a felhőben lett rétegzett felhőbeli sebességgel következik be.

Az alábbi ábrán egy tipikus kötetet a biztonsági mentési feladat hozzárendelését. Ebben az esetben a heti biztonsági mentések leképezése a szombat teljes lemezt, és a növekményes biztonsági mentések leképezése hétfőtől péntekig növekményes lemezeket. A biztonsági mentéseket és helyreállításokat vannak a storsimple-kötet összes rétegzett kötet.

![Elsődleges biztonsági mentési célként megadott konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>A StorSimple mint elsődleges biztonsági mentési cél GFS példa ütemezése

Íme egy példa négy hét, havi és évi GFS Elforgatás ütemezését:

| Gyakorisága, illetve biztonsági mentési típusa | Korlátlan | Növekményes (1-5 nap)  |   
|---|---|---|
| Heti (1 – 4 hét) | Szombat | Hétfőtől péntekig |
| Havonta  | Szombat  |   |
| Évente | Szombat  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-kötetek hozzárendelése egy biztonsági mentési Exec biztonsági mentési feladat

A következő szakasz azt feltételezi, Backup Execkel és a cél gazdagépen konfigurált összhangban a Backup Execkel ügynök irányelveket.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-kötetek hozzárendelése egy biztonsági mentési Exec biztonsági mentési feladat

1.  A Backup Execkel felügyeleti konzolban válassza **gazdagép** > **Backup** > **lemezre történő biztonsági mentés**.

    ![Biztonsági mentési Exec felügyeleti konzol, a kiválasztott gazdagép, a biztonsági mentés és a lemezes biztonsági mentés](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Az a **biztonságimásolat-definíció tulajdonságai** párbeszédpanel **biztonsági mentés**válassza **szerkesztése**.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági mentés definíció tulajdonságai párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  A teljes és növekményes biztonsági mentések beállítása, hogy az RPO és RTO követelményeknek, és ajánlott eljárások a Veritas felelnek meg.

4.  Az a **biztonsági mentési beállítások** párbeszédpanelen jelölje ki **tárolási**.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági mentési lehetőségeket tár párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Rendelje hozzá a megfelelő StorSimple-köteteket a biztonsági mentési ütemezését.

    > [!NOTE]
    > **A tömörítés** és **titkosítási típus** vannak beállítva, hogy **None**.

6.  Alatt **ellenőrizze**, jelölje be a **ne ellenőrizze az adatok az adott feladathoz** jelölőnégyzetet. Ez a beállítás hatással lehetnek a StorSimple-rétegezést.

    > [!NOTE]
    > Lemeztöredezettség-mentesítés, indexelő és háttér-ellenőrzési negatívan befolyásolhatja a StorSimple-rétegezést.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági beállítások a beállítások ellenőrzése](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Ha beállította a biztonsági mentési lehetőségeket az igényeknek a többi, válassza ki a **OK** befejezéséhez.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési cél

> [!NOTE]
>Adatok visszaállítása a felhőbe tartozik lett rétegzett biztonsági felhőbeli sebességgel fordulhat elő.

Ebben a modellben egy átmeneti gyorsítótárban egyikükön rendelkeznie kell egy adathordozó (nem a StorSimple). Használhatja például a kötet (RAID) független lemezek redundáns tömbjének terület, a bemeneti/kimeneti (I/O) és a sávszélesség. A RAID 10, 5 és 50 használatát javasoljuk.

A következő ábrán látható tipikus rövid távú megőrzési (a kiszolgáló) helyi köteteket, és hosszú távú megőrzés archiválja a köteteket. Ebben a forgatókönyvben minden biztonsági mentés futtatása a helyi (a kiszolgáló) RAID-köteten. Ezeket a biztonsági másolatokat időszakosan ismétlődő és archivált archívumok kötetre. Fontos a helyi (a kiszolgáló) RAID kötetre méretezéséhez, így képes kezelni a rövid távú adatmegőrzési kapacitást és teljesítményre vonatkozó követelményeknek.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>A StorSimple mint egy másodlagos biztonsági mentési cél GFS példa

![A StorSimple mint másodlagos biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Az alábbi táblázat bemutatja, hogyan futtathatók a helyi és a StorSimple-lemezek biztonsági mentések beállításához. Egyéni és teljes kapacitásigények tartalmazza.

### <a name="backup-configuration-and-capacity-requirements"></a>Biztonsági mentési konfiguráció és a kapacitásbeli követelmények

| Biztonsági mentés típusát és megőrzés | Konfigurált tároló | Méret (TiB) | GFS szorzó | Teljes kapacitás\* (TiB) |
|---|---|---|---|---|
| (Teljes és növekményes) 1 hét |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| A StorSimple hét 2 – 4 |A StorSimple-lemez (hosszú távú) | 1 | 4 | 4 |
| Teljes havi |A StorSimple-lemez (hosszú távú) | 1 | 12 | 12 |
| Éves teljes |A StorSimple-lemez (hosszú távú) | 1 | 1 | 1 |
|GFS kötetek méretkövetelményt |  |  |  | 18*|

\* Teljes kapacitás 17 Tib-ra a StorSimple-lemezek és a helyi RAID kötetre 1 TiB tartalmaz.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS példa ütemezése: Elforgatás GFS heti, havi és éves ütemterv

| Hét | Korlátlan | 1. nap növekményes | 2. napon növekményes | Növekményes naponta 3 | Növekményes naponta 4 | Növekményes nap 5 |
|---|---|---|---|---|---|---|
| 1 hét | Helyi RAID kötetre  | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre |
| 2 hét | A StorSimple hét 2 – 4 |   |   |   |   |   |
| 3 hét | A StorSimple hét 2 – 4 |   |   |   |   |   |
| 4 hét | A StorSimple hét 2 – 4 |   |   |   |   |   |
| Havonta | A StorSimple havi |   |   |   |   |   |
| Évente | StorSimple évente  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>StorSimple-kötetek hozzárendelése egy Backup Execkel archive, és a deduplikációs feladat

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>StorSimple-kötetek hozzárendelése egy Backup Execkel archív és a párhuzamos feladat

1.  A Backup Execkel felügyeleti konzolon kattintson a jobb gombbal a StorSimple-kötet archiválás, és válassza ki a kívánt feladat **biztonságimásolat-definíció tulajdonságai** > **szerkesztése**.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági mentés definíció tulajdonságai lap](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Válassza ki **szakasz hozzáadása** > **lemezre ismétlődő** > **szerkesztése**.

    ![Biztonsági mentés Exec felügyeleti konzolt, szakasz felvétele](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Az a **ismétlődő beállítások** párbeszédpanelen jelölje ki a használni kívánt értékeket **forrás** és **ütemezés**.

    ![Exec felügyeleti konzol, biztonsági mentési definíciók tulajdonságok és ismétlődő beállítások biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Az a **tárolási** legördülő listára, válassza ki a StorSimple-kötet, ahol azt szeretné, hogy az archiválási feladat a adatok tárolásához.

    ![Exec felügyeleti konzol, biztonsági mentési definíciók tulajdonságok és ismétlődő beállítások biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Válassza ki **győződjön meg arról**, majd válassza ki a **ne ellenőrizze az adatok az adott feladathoz** jelölőnégyzetet.

    ![Exec felügyeleti konzol, biztonsági mentési definíciók tulajdonságok és ismétlődő beállítások biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Kattintson az **OK** gombra.

    ![Exec felügyeleti konzol, biztonsági mentési definíciók tulajdonságok és ismétlődő beállítások biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Az a **Backup** oszlop, adjon hozzá egy új szakaszt. Az adatforrás használata **növekményes**. A cél válassza ki a StorSimple-kötet, ahol a növekményes biztonsági mentési feladat archivált. Ismételje meg az 1 – 6.

## <a name="storsimple-cloud-snapshots"></a>A StorSimple a felhőbeli pillanatképek

A StorSimple a felhőbeli pillanatképek a StorSimple-eszköz lévő adatok védelmét. Felhőbeli pillanatkép létrehozásának megegyezik egy külső létesítmény helyi másolatot tartalmazó szalagok szállítási. Ha az Azure georedundáns tárolást használ, felhőbeli pillanatkép létrehozásának megegyezik szállítási szalagot több helyhez. Ha egy eszköz visszaállítása egy vészhelyzetet követően van szüksége, előfordulhat, hogy csatlakoztassa egy másik StorSimple eszközt, és hajtsa végre a feladatátvételt. A feladatátvétel után meg tudják elérheti az adatokat (felhőbeli sebességgel), a legutóbbi felhőbeli pillanatkép.

Az alábbi szakasz elindításához és a StorSimple a felhőbeli pillanatképek törlése során a biztonsági mentés utáni feldolgozási rövid parancsfájl létrehozása.

> [!NOTE]
> A pillanatképek manuálisan vagy programon keresztül létrehozott ne hajtsa végre a StorSimple snapshot elévülési szabályzatot. Ezek a pillanatképek manuálisan vagy programon keresztül törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Indítsa el, és a felhőbeli pillanatképek törlése egy parancsfájl használatával

> [!NOTE]
> Gondosan mérje fel a megfelelőség és az adatok megőrzési következményekkel StorSimple pillanatkép törlése előtt. A biztonsági mentés utáni parancsfájl futtatásával kapcsolatos további információkért lásd: a [Backup Execkel dokumentáció](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Biztonsági mentési életciklusa

![Biztonsági mentési életciklus-diagram](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Követelmények

-   A kiszolgáló, amely futtatja a szkriptet az Azure felhőbeli erőforrások hozzáféréssel kell rendelkeznie.
-   A felhasználói fióknak a szükséges engedélyekkel kell rendelkeznie.
-   A társított StorSimple-kötetek a StorSimple biztonsági mentési szabályzat kell beállítani, de nincs bekapcsolva.
-   Szüksége lesz a StorSimple-erőforrás neve, a regisztrációs kulcsot, eszközazonosítót nevénél, és a biztonsági mentési szabályzat.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Indítsa el, vagy a felhőbeli pillanatkép törlése

1. [Telepítse az Azure PowerShellt](/powershell/azure/overview).
2. Letöltési és telepítési [kezelés – CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-parancsfájlt.
3. A kiszolgálón, amely futtatja a szkriptet, futtassa a Powershellt rendszergazdaként. Győződjön meg arról, hogy a parancsfájl futtatása a `-WhatIf $true` győződjön meg, mi változik a parancsfájlt. Az érvényesítés befejezése után át `-WhatIf $false`. Futtassa az alábbi parancsot:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adja hozzá a parancsfájlt a biztonsági mentési feladat a biztonsági mentési Exec előfeldolgozásához a biztonsági mentési Exec feladat beállítások szerkesztésével, és utáni parancsok feldolgozásakor.

   ![Biztonsági mentés Exec konzol, a biztonsági mentés, előzetes és utólagos feldolgozási parancsok lap](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Azt javasoljuk, hogy a StorSimple felhőalapú pillanatkép biztonsági mentési szabályzat utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. Biztonsági mentése és visszaállítása a biztonsági mentést végző alkalmazás környezet segítséget nyújtanak az RPO és RTO kapcsolatos további információkért tekintse meg a biztonsági mentési mérnök együtt.

## <a name="storsimple-as-a-restore-source"></a>A StorSimple-helyreállítás forrásaként

Visszaállítja a StorSimple eszköz munkahelyi például visszaállítás bármely block storage eszközről. Visszaállítás a felhőbe a rétegzett adatok felhőbeli sebességgel következik be. A helyi adatok esetében a visszaállítás fordulhat elő, az eszköz helyi lemez sebességével. Információ a visszaállításhoz a Backup Execkel dokumentációjában talál. Azt javasoljuk, hogy megfeleljenek a Backup Execkel visszaállítási ajánlott eljárásokat.

## <a name="storsimple-failover-and-disaster-recovery"></a>A StorSimple feladatátvétel és a katasztrófa utáni helyreállítás

> [!NOTE]
> A biztonsági mentési cél esetben a StorSimple Cloud Appliance nem támogatott visszaállítási célként.

Katasztrófa számos tényező okozhatja. Az alábbi táblázat általános vész-helyreállítási helyzetekben.

| Forgatókönyv | Hatás | Helyreállítása | Megjegyzések |
|---|---|---|---|
| A StorSimple eszköz hiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a sikertelen eszközt, és hajtsa végre [StorSimple feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-device-failover-disaster-recovery.md). | Ha eszköz helyreállítása után a helyreállítás végrehajtásához szüksége, működő teljes adatkészletek lekért a felhő az új eszközhöz. Az összes műveletekre felhőbeli sebességgel. Előfordulhat, hogy az indexelés és katalogizálás rescanning folyamat beolvasott és a helyi eszközön szintre, ami időigényes eljárás lehet, hogy a felhő szintjén lekért összes biztonságimásolat-készleteket. |
| Biztonsági mentési Exec kiszolgálóhiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Építse újra a biztonsági mentési kiszolgálóra, és végezze el az adatbázis-visszaállítás leírt módon [manuális biztonsági mentés és visszaállítás a biztonsági mentési Exec (BEDB) adatbázis módjáról](http://www.veritas.com/docs/000041083). | Építse újra, vagy állítsa vissza a vész-helyreállítási webhelyként, a Backup Execkel kiszolgálót kell. Állítsa vissza az adatbázist a legutóbbi pontnak. Ha a biztonsági mentési Exec visszaállított adatbázis nem a legújabb biztonsági mentési feladatok szinkronban, indexelési és katalogizálása megadása kötelező. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a beolvasott és a felhő szintjén a helyi eszközön szintre lekért összes biztonságimásolat-készleteket. Így további időigényes. |
| Hely hiba, amely a biztonsági mentési kiszolgálóra és a StorSimple elvesztését eredményezi | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, és helyreállíthatja a Backup Execkel. | Először állítsa vissza a StorSimple, és helyreállíthatja a Backup Execkel. Hajtson végre visszaállítást eszköz helyreállítása után van szüksége, ha a teljes működő adatkészletek a vannak a felhőből az új eszköz beolvasni. Az összes műveletekre felhőbeli sebességgel. |

## <a name="references"></a>Referencia

Ez a cikk a hivatkozott az alábbi dokumentumokat:

- [A StorSimple-a többutas i/o-telepítő](storsimple-configure-mpio-windows-server.md)
- [Storage-forgatókönyv esetében: a dinamikus kiosztás](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Meghajtók GPT használata](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Állítsa be a megosztott mappák árnyékmásolatai](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [biztonságimásolat-készlet visszaállítási](storsimple-restore-from-backup-set-u2.md).
- További információk a végrehajtása [eszközön feladatátvétel és vészhelyreállítás helyreállítási](storsimple-device-failover-disaster-recovery.md).
