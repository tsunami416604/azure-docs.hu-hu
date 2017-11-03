---
title: "A biztonsági mentés céljaként NetBackup a StorSimple 8000 sorozat |} Microsoft Docs"
description: "A StorSimple biztonsági mentési cél konfiguráció Veritas NetBackup ismerteti."
services: storsimple
documentationcenter: 
author: harshakirank
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: hkanna
ms.openlocfilehash: b1878c181a77ac6d54654fc55228907743243c45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>Biztonsági mentési cél a NetBackup StorSimple

## <a name="overview"></a>Áttekintés

Az Azure StorSimple hibrid felhőalapú tárolási megoldás a Microsoft. StorSimple exponenciális adatmennyiség-növekedés bonyolultságára úgy kezeli a helyszíni megoldás bővítményként Azure storage-fiók használatával, és automatikusan rétegezéséhez adatok között a helyszíni és felhőalapú tárolására.

Ez a cikk arról lesz szó Veritas NetBackup és ajánlott eljárások a mindkét megoldás integrálása StorSimple integrációját. Azt is ajánlásokat a legjobb integrálása StorSimple Veritas NetBackup beállításával. A Microsoft késlelteti a Veritas gyakorlati tanácsok, biztonsági mentési mérnökök és rendszergazdák az a legjobb módszer Veritas NetBackup beállítása az egyes biztonsági mentés követelményeinek, és a szolgáltatásszint-szerződések (SLA).

Azt mutatja be a konfigurációs lépéseket és a főbb fogalmait, bár ez a cikk semmiképpen sem részletes konfigurációs vagy a telepítési útmutató is. Feltételezzük, hogy az alapvető összetevői és az infrastruktúra működő sorrendben és készen áll a fogalmakat, azt a leíró támogatja.

### <a name="who-should-read-this"></a>Ez célközönsége?

A cikkben szereplő információkat biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolási fejlesztők ismerő, tárolási, a Windows Server 2012 R2, a Ethernet, a felhőszolgáltatások és a Veritas NetBackup hasznos lesz.

### <a name="supported-versions"></a>Támogatott verziók

-   NetBackup 7.7.x és újabb verziók
-   [StorSimple Update 3 és újabb verziók](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Miért StorSimple biztonsági mentési cél?

StorSimple nem jó választás az egy biztonsági mentési cél, mert:

-   Standard, a helyi tároló biztonsági mentési alkalmazások gyors biztonsági mentés célhelye, módosítás nélkül az biztosít. StorSimple gyors helyreállítás a legutóbbi biztonsági mentések is használja.
-   A felhő rétegezéséhez zökkenőmentesen integrálva van az Azure felhőalapú társzolgáltatás fiókja költséghatékony Azure Storage használatához.
-   Vész-helyreállítási külső helyszínen történő tárolás automatikusan biztosít.

## <a name="key-concepts"></a>Fő fogalmak

Csakúgy, mint bármely tárolási megoldás, a megoldás tárolási teljesítményt, szolgáltatásiszint-szerződések, gondosan meg kell értékelése változások és kapacitásigények növekedési aránya fontos a sikeres. Lényege, hogy a felhő szintjén, a Csatlakozás ideje és a felhő Play StorSimple képességét a munkaköre alapvető szerepet teljesítmények szemben.

StorSimple-t üzemeltető alkalmazás tárhelyet biztosítson egy jól meghatározott munkakészletének (kiemelt) adatokat a célja. Ebben a modellben a helyi rétegeken tárolt adatok munkakészletének, és a fennmaradó szabadnap/cold/archivált adatok készletét többszintű a felhőbe. Az alábbi ábrán ez a modell jelöli. A művelet megközelítette az egyszerű zöld sor a helyi rétegeken a StorSimple eszköz tárolt adatok jelöli. A piros sor összes rétegek között a StorSimple megoldásban tárolt adatok teljes mennyisége jelöli. Egyszerű zöld és exponenciális piros görbe közötti terület a felhőben tárolt adatok teljes mennyisége jelöli.

**StorSimple rétegezéséhez**
![StorSimple rétegezési diagramja](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Ezzel az architektúrával figyelembe találja, hogy a StorSimple kiválóan biztonsági mentési cél fog működni. A StorSimple használhatja:
-   A leggyakoribb helyreállítást végrehajtani az adatok helyi munkakészlete.
-   Használja a felhő vész-helyreállítási külső helyszínen és a régebbi adatokat visszaállítások esetén ritkábban.

## <a name="storsimple-benefits"></a>StorSimple előnyei

StorSimple egy helyszíni megoldást nyújt, amely zökkenőmentesen integrálva van a Microsoft Azure-ban, ha kihasználja a zökkenőmentes hozzáférést a helyszíni és felhőbeli tárhelyén.

StorSimple használ, a helyszíni eszköz, amelynek SSD eszköz (SSD) és a soros csatlakozású SCSI (SAS) tárolók, valamint az Azure Storage automatikus rétegezéséhez. Automatikus rétegezéséhez tartja a gyakran használt adatok helyi, az SSD és a SAS rétegen. Azure Storage áthelyezi azt a ritkábban használt adatokhoz.

StorSimple alábbi előnyökkel jár:

-   A felhő eléréséhez, az egyedülálló deduplikációs szinteket használó egyedi a deduplikáció és a tömörítést algoritmusok
-   Magas rendelkezésre állás
-   Georeplikálási Azure georeplikáció használatával
-   Azure-integráció
-   Adatok titkosítása a felhőben
-   Továbbfejlesztett vész-helyreállítási és megfelelőség

Bár a StorSimple mutatja be két fő telepítési forgatókönyvek (biztonsági mentési cél elsődleges és másodlagos biztonsági mentési cél), alapvetően, egy egyszerű, blokktárolóeszköz. StorSimple does a tömörítés és a deduplikáció. Zökkenőmentesen küld, és lekéri a felhőalapú és az alkalmazás és a fájlrendszer közötti adatokat.

További információ a StorSimple: [StorSimple 8000 series: hibrid felhő tárolási megoldás](storsimple-overview.md). Emellett áttekintheti a [StorSimple 8000 series műszaki specifikációk](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> A StorSimple eszköz biztonsági mentési cél használata támogatott csak a StorSimple 8000 Update 3 és újabb verziók.

## <a name="architecture-overview"></a>Az architektúra áttekintése

Az alábbi táblázatok bemutatják az eszköz kezdeti modell-architektúra-útmutatót.

**StorSimple kapacitások helyi és felhőbeli tárhelyén**

| Tárkapacitás       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Helyi tárolási kapacitás | &lt;10 TiB\*  | &lt;20 TiB\*  |
| Felhőalapú tárolási kapacitás | &gt;200 TiB\* | &gt;500 TiB\* |
\*Tárméret azt feltételezi, hogy nem a deduplikáció és a tömörítést.

**StorSimple kapacitások elsődleges és másodlagos biztonsági mentések tiltása**

| Biztonsági mentési forgatókönyv  | Helyi tárolási kapacitás  | Felhőalapú tárolási kapacitás  |
|---|---|---|
| Elsődleges biztonsági mentése  | A gyors helyreállítás helyi tárolóban tárolt legutóbbi biztonsági felel meg a helyreállítási időkorlát (RPO) | Felhő kapacitása a megfelelő biztonsági mentési előzményeit (RPO) |
| Másodlagos biztonsági mentése | Felhő kapacitása tárolható biztonsági mentési adatok másodlagos példányát  | N/A  |

## <a name="storsimple-as-a-primary-backup-target"></a>Elsődleges biztonsági mentési cél StorSimple

Ebben a forgatókönyvben a StorSimple-köteteket a biztonsági mentési alkalmazásra, amely a biztonsági mentések egyetlen tárháza jelennek meg. Az alábbi ábrán látható, amelyben az összes biztonsági mentés használata StorSimple rétegzett kötetek biztonsági mentést és helyreállítást megoldási.

![StorSimple, elsődleges biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Elsődleges célja a biztonsági mentési logikai lépések

1.  A helykiszolgáló biztonsági mentése a cél biztonságimásolat-készítő ügynök kapcsolódik, és a biztonságimásolat-készítő ügynök adatátvitelt a helykiszolgáló biztonsági mentése.
2.  A helykiszolgáló biztonsági mentése írja az adatokat a StorSimple rétegzett kötet.
3.  A helykiszolgáló biztonsági mentése a katalógus-adatbázis frissíti, és majd befejezi a biztonsági mentési feladat.
4.  A pillanatkép-parancsfájl elindítja a StorSimple snapshot Managerben (kezdeti vagy törlése).
5.  A helykiszolgáló biztonsági mentése törli a lejárt biztonsági mentések adatmegőrzési szabály alapján.

### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállítási logikai lépésre

1.  A helykiszolgáló biztonsági mentése elindul, a megfelelő adatok helyreállításához a tárolási tárházból.
2.  A biztonságimásolat-készítő ügynök az adatokat fogad a helykiszolgáló biztonsági mentése.
3.  A helykiszolgáló biztonsági mentése a visszaállítási feladat befejeződik.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple másodlagos biztonsági mentési cél

Ebben az esetben a StorSimple-köteteket elsősorban a hosszú távú megőrzési vagy használatosak archiválása.

Az alábbi ábra az architektúra látható, mely kezdeti biztonsági mentései, és egy nagy teljesítményű célkötet visszaállítja. Ezek a biztonsági másolatok másolta, és a storsimple-kötet archiválva rétegzett kötet beállított ütemezés szerint.

Fontos a nagy teljesítményű kötet méretének, hogy az adatmegőrzési házirend kapacitást és teljesítményt követelményeit is képes kezelni.

![StorSimple, a másodlagos biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>A biztonsági mentési logikai lépések másodlagos cél

1.  A helykiszolgáló biztonsági mentése a cél biztonságimásolat-készítő ügynök kapcsolódik, és a biztonságimásolat-készítő ügynök adatátvitelt a helykiszolgáló biztonsági mentése.
2.  A helykiszolgáló biztonsági mentése nagy teljesítményű tárolást írja az adatokat.
3.  A helykiszolgáló biztonsági mentése a katalógus-adatbázis frissíti, és majd befejezi a biztonsági mentési feladat.
4.  A helykiszolgáló biztonsági mentése egy megőrzési házirend alapján StorSimple biztonsági mentések másolja.
5.  A pillanatkép-parancsfájl elindítja a StorSimple snapshot Managerben (kezdeti vagy törlése).
6.  A helykiszolgáló biztonsági mentése törli a lejárt biztonsági mentések adatmegőrzési szabály alapján.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél visszaállítási logikai lépésre

1.  A helykiszolgáló biztonsági mentése elindul, a megfelelő adatok helyreállításához a tárolási tárházból.
2.  A biztonságimásolat-készítő ügynök az adatokat fogad a helykiszolgáló biztonsági mentése.
3.  A helykiszolgáló biztonsági mentése a visszaállítási feladat befejeződik.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezéséhez

A megoldás három lépésből áll:
1. A hálózati infrastruktúra előkészítése.
2. A StorSimple eszköz üzembe helyezése a biztonsági mentés céljaként.
3. Veritas NetBackup telepíteni.

A következő szakaszokban részletesen tárgyalt egyes lépéseit.

### <a name="set-up-the-network"></a>A hálózat beállítása

StorSimple olyan megoldás, amely integrálva van az Azure felhőben, mert a StorSimple egy aktív és az Azure felhőben működő kapcsolatot igényel. A kapcsolat például felhőalapú pillanatfelvételek, kezelése és metaadatok adatátviteli műveletek elvégzéséhez használható, és régebbi réteghez, kisebb használt adatokat pedig Azure felhőalapú tárolást.

A megoldás optimális azt javasoljuk, hogy pontosan kövesse az alábbi hálózati gyakorlati tanácsok:

-   A hivatkozás, amely összeköti a StorSimple rétegezéséhez Azure meg kell felelnie a sávszélesség-követelményekkel. Ennek érdekében a megfelelő szolgáltatásminőség (QoS) szint alkalmazása azokra a infrastruktúra kapcsolókra megfelel-e a helyreállítási Időkorlát és a helyreállítási idő célkitűzése (RTO) SLA-k.

-   Maximális Azure Blob storage-hozzáférési késéseket körülbelül 80 ms kell lennie.

### <a name="deploy-storsimple"></a>StorSimple telepítése

Részletes üzembe helyezési útmutatót a StorSimple, lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>NetBackup telepítése

Lépésenkénti NetBackup 7.7.x telepítési útmutatásért tekintse meg a [NetBackup 7.7.x dokumentáció](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>A megoldás beállítása

Ebben a szakaszban a bemutatjuk, konfigurációs példákat. A következő példák és javaslatok bemutatják az alapszintű és a alapvető végrehajtására. Ez a megvalósítás nem feltétlenül vonatkoznak közvetlenül az adott biztonsági mentés követelményeinek.

### <a name="set-up-storsimple"></a>StorSimple beállítása

| StorSimple telepítési feladatok  | További megjegyzéseket |
|---|---|
| A helyszíni StorSimple eszköz üzembe helyezése. | Támogatott verziók: 3 és újabb verziók frissítése. |
| Kapcsolja be a biztonsági mentési cél. | Ezek a parancsok használata, kapcsolja be, vagy kapcsolja ki a biztonsági mentési cél módot, és állapotának beolvasása. További információkért lásd: [távolról csatlakozhat a StorSimple eszköz](storsimple-remote-connect.md).</br> Biztonsági mentés módja bekapcsolása: `Set-HCSBackupApplianceMode -enable`. </br> Biztonsági mentés módja kikapcsolása: `Set-HCSBackupApplianceMode -disable`. </br> A biztonsági mentés módja beállítások aktuális állapot: `Get-HCSBackupApplianceMode`. |
| A kötet, amely tárolja a biztonsági mentési adatok közös kötettároló létrehozása. A kötettároló összes adatot deduplikált. | StorSimple kötettárolók deduplikációs tartományok definiálása.  |
| StorSimple-köteteket hozhat létre. | Hozzon létre köteteket, mérete megközelíti a várható használati lehetséges, mert a kötet mérete befolyásolja a pillanatkép-időtartam felhő. A kötet méretének kapcsolatos információkért olvassa el [adatmegőrzési](#retention-policies).</br> </br> Használja a StorSimple rétegzett kötet, és válassza ki a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet. </br> Csak a helyileg rögzített kötetek használata nem támogatott. |
| Hozzon létre egy egyedi StorSimple biztonsági mentési házirendet a biztonsági mentési tároló kötetek. | A StorSimple biztonsági mentési házirend a kötet konzisztencia csoportját határozza meg. |
| Tiltsa le az ütemezést, ahogyan a pillanatképek érvényessége lejár. | A pillanatképek utófeldolgozási műveletként aktiválódnak. |

### <a name="set-up-the-host-backup-server-storage"></a>A fogadó helykiszolgáló biztonsági mentése tárolás beállítása

Állítsa be a helykiszolgáló biztonsági mentése tárolók szerint ezeket az irányelveket:  

- Ne használjon átnyúló kötetek (hozta létre a Windows Lemezkezelés); átnyúló kötetek nem támogatottak.
- Formázza a NTFS fájlrendszerrel 64 KB-os foglalási méretű köteteket.
- A StorSimple-köteteket közvetlenül a NetBackup kiszolgáló hozzárendelését.
    - ISCSI használata fizikai kiszolgálók.
    - Használja a csatlakoztatott lemezeket virtuális kiszolgálók.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Gyakorlati tanácsok a StorSimple és NetBackup

Az alábbiakban az igényeinek megfelelően a megoldás beállítása néhány szakaszok.

### <a name="operating-system-best-practices"></a>Operációs rendszer ajánlott eljárások

-   Tiltsa le a Windows Server titkosítás és a deduplikáció az NTFS fájlrendszerhez.
-   Tiltsa le a StorSimple-köteteket a Windows Server töredezettségmentesítés.
-   Tiltsa le a Windows Server indexelő a StorSimple-köteteket.
-   Víruskereső futtatni a forrásállomás (nem szemben a StorSimple-köteteket).
-   Kapcsolja ki az alapértelmezett [Windows Server karbantartási](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) a Feladatkezelő. Ehhez a következő módszerek valamelyikével:
    - Kapcsolja ki a karbantartási konfiguráló a Windows Feladatütemező.
    - Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) a Windows Sysinternals. Miután letöltötte a PsExec, futtassa a Windows PowerShell rendszergazdai, írja be:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>StorSimple gyakorlati tanácsok

-   Ne feledje, hogy a rendszer frissíti a StorSimple eszköz [3-as vagy újabb frissítés](storsimple-install-update-3.md).
-   Elkülönítheti az iSCSI és a felhőalapú forgalom. StorSimple és a helykiszolgáló biztonsági mentése közötti forgalom dedikált iSCSI-kapcsolat használata.
-   Győződjön meg arról, hogy a StorSimple eszköz-e egy dedikált célhelyet. Vegyes munkaterhelések nem támogatottak, mert ezek hatással a RTO és a helyreállítási Időkorlát.

### <a name="netbackup-best-practices"></a>NetBackup gyakorlati tanácsok

-   A NetBackup adatbázis kell elhelyezkednie, a kiszolgáló és a StorSimple-kötet nem található.
-   Vész-helyreállítási adatbázis biztonsági mentése a NetBackup a StorSimple-kötet.
-   Ebben a megoldásban NetBackup teljes és növekményes biztonsági mentések (más néven a NetBackup különbözeti növekményes biztonsági mentések) támogatott. Azt javasoljuk, hogy ne használjon szintetikus és az összesített növekményes biztonsági mentést.
-   Biztonsági mentési adatok fájljaihoz tartalmaznia kell egy adott feladat csak azokat az adatokat. Például nincs adathordozó hozzáfűzi között különböző feladatok engedélyezettek.

A legújabb NetBackup beállításokat, és ezek a követelmények megvalósításához ajánlott eljárások a NetBackup dokumentációját a című [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Adatmegőrzési házirendek

A biztonsági mentés megőrzési házirend leggyakoribb egyik szerzett, Édesapja és fia (GFS) házirend. A GFS házirendek egy növekményes biztonsági mentés napi és heti és havi végzett teljes biztonsági mentés. A csoportházirend-eredményhez hat StorSimple a rétegzett kötet: egy kötetet a heti, havi és éves teljes biztonsági másolatait tartalmazza; a többi öt kötet napi növekményes biztonsági mentések tárolására.

A következő példában egy GFS Elforgatás használjuk. A példa azt feltételezi, hogy a következő:

-   A nem deduplikált vagy a tömörített adatok szolgál.
-   Teljes biztonsági mentés olyan 1 TiB.
-   Napi növekményes biztonsági mentések 500 GiB.
-   Négy heti biztonsági mentései egy hónapig tartanak.
-   12 havi biztonsági mentés évente tartanak.
-   Egy 10 éve éves biztonsági másolatok.

A fenti feltételek alapján, hozzon létre egy 26-TiB StorSimple rétegzett kötet a havi és éves teljes biztonsági mentés. Hozzon létre egy 5-TiB StorSimple rétegzett kötet az egyes napi növekményes biztonsági mentései.

| Biztonsági mentés típusa megőrzési | Méret (TiB) | GFS szorzója\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Heti teljes | 1 | 4  | 4 |
| Napi növekményes | 0.5 | 20 (ciklusok egyenlő hetek száma havonta) | 12 (további kvótához 2) |
| Havi teljes | 1 | 12 | 12 |
| Éves teljes | 1  | 10 | 10 |
| GFS követelmény |   | 38 |   |
| További kvótát  | 4  |   | 42 teljes GFS követelmény  |
\*A GFS többszöröző példányszám történő védelmére, és a biztonsági mentési házirend követelményeinek megfelelően szüksége.

## <a name="set-up-netbackup-storage"></a>NetBackup tárolás beállítása

### <a name="to-set-up-netbackup-storage"></a>NetBackup tárolás beállítása

1.  A NetBackup felügyeleti konzolban válassza **Media és kezelés** > **eszközök** > **lemezkészleteket**. A lemez tárolókészlet konfigurációs varázslóban válassza ki a tárolási **AdvancedDisk**, majd válassza ki **következő**.

    ![NetBackup felügyeleti konzolon, a készlet a konfiguráció varázsló](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Válassza ki a kiszolgálót, majd válassza ki **következő**.

    ![NetBackup felügyeleti konzolt, válassza ki a kiszolgálót](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Válassza ki a StorSimple-kötet.

    ![NetBackup felügyeleti konzolt, válassza ki a StorSimple-kötet lemezt](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Adja meg a biztonsági mentés célhelyén nevét, majd válassza ki **következő** > **tovább** a varázsló befejezéséhez.

5.  Tekintse át a beállításokat, majd válassza ki **Befejezés**.

6.  Minden kötet hozzárendelés végén a javasolt felel meg a tárolási eszköz beállítások módosításával [gyakorlati tanácsok a StorSimple és NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Ismételje 1-6 végzett hozzárendelése a StorSimple-köteteket.

    ![NetBackup hivatkozásokra lemezkonfiguráció](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Elsődleges biztonsági mentési cél StorSimple beállítása

> [!NOTE]
> Adatok visszaállítása biztonsági mentésből van a felhőhöz már rétegzett felhő sebességek fordulhat elő.

Az alábbi ábrán egy tipikus kötetet a biztonsági mentési feladatot leképezése. Ebben az esetben a heti biztonsági mentései a szombat teljes lemezre van leképezve, és a növekményes biztonsági mentések hétfőtől péntekig növekményes lemezek hozzárendelését. Minden visszaállítások és biztonsági mentés a storsimple-kötet a rétegzett kötet.

![Elsődleges biztonsági mentési cél konfigurációs logikai diagramja ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Elsődleges biztonsági mentési cél GFS StorSimple ütemezése – példa

Itt látható egy példa négy hét, a havi és éves GFS Elforgatás ütemezése:

| Gyakoriság vagy biztonsági mentés típusa | Korlátlan | Növekményes (1-5 nap)  |   
|---|---|---|
| Heti (1 – 4 hét) | Szombat | Hétfőtől péntekig |
| Havi  | Szombat  |   |
| Éves ütemezéshez | Szombat  |   |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-köteteket hozzárendelése NetBackup biztonságimásolat-készítő feladat

Az alábbi sorrendben feltételezi, hogy megfelel-e a NetBackup ügynök irányelvek NetBackup és a cél gazdagép vannak konfigurálva.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-köteteket hozzárendelése NetBackup biztonságimásolat-készítő feladat

1.  A NetBackup felügyeleti konzolban válassza **NetBackup felügyeleti**, kattintson a jobb gombbal **házirendek**, majd válassza ki **új házirend**.

    ![NetBackup felügyeleti konzolján, egy új házirend létrehozása](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2.  Az a **adjon hozzá egy új házirendet** párbeszédpanelen adjon meg egy nevet a házirend, és válassza a **házirend konfigurációs varázsló használata** jelölőnégyzetet. Kattintson az **OK** gombra.

    ![NetBackup felügyeleti konzolján hozzáadása egy új házirend párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3.  A biztonsági mentési házirend konfigurációs varázsló en, és válassza a biztonsági mentés típusát **következő**.

    ![NetBackup felügyeleti konzolt, jelölje be a biztonsági mentés típusa](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4.  Válassza ki, hogy a házirend típusát, **szabványos**, majd válassza ki **következő**.

    ![NetBackup felügyeleti konzolon válassza a házirend típusa](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5.  Válassza ki a gazdagép, válassza ki a **ügyfél operációs rendszer észlelése** jelölje be a jelölőnégyzetet, majd válassza ki **Hozzáadás**. Válassza ki **következő**.

    ![NetBackup felügyeleti konzolján, egy új házirendet a lista ügyfelek](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6.  Válassza ki a meghajtót, készítsen biztonsági másolatot szeretne.

    ![NetBackup felügyeleti konzolján, egy új házirendet a biztonsági mentési beállítások](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7.  Válassza ki a gyakoriság és megőrzési értékeket, a biztonsági mentési Elforgatás követelményeknek.

    ![NetBackup felügyeleti konzol, a biztonsági mentési gyakoriság és az új házirend elforgatásának](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8.  Válassza ki **következő** > **következő** > **Befejezés**.  Az ütemezés a házirend létrehozása után módosíthatja.

9.  Lehetőséget, és bontsa ki az imént házirend létrehozása, és válassza **ütemezések**.

    ![NetBackup felügyeleti konzol, az új szabályzathoz ütemezések](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10.  Kattintson a jobb gombbal **különbségi-Inc**, jelölje be **másolja az új**, majd válassza ki **OK**.

    ![NetBackup felügyeleti konzolján, egy új házirendet ütemezését](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11.  Kattintson a jobb gombbal az újonnan létrehozott ütemezést, és válassza ki **módosítása**.

12.  Az a **attribútumok** lapon jelölje be a **bírálja felül a csoportházirend-tároló kiválasztása** jelölőnégyzetet, majd válassza ki a kötet hétfő növekményes biztonsági mentések hová.

    ![NetBackup felügyeleti konzolon, ütemezés módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13.  Az a **Start ablak** lapra, válassza ki a biztonsági másolatok a időszak.

    ![NetBackup felügyeleti konzolon, kezdő ablak módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14.  Kattintson az **OK** gombra.

15.  Ismételje meg a 10-14. minden egyes növekményes biztonsági mentést. Válassza ki a megfelelő kötetet és ütemezését minden biztonsági másolatot hoz létre.

16.  Kattintson a jobb gombbal a **különbségi-Inc** ütemezhet, és törölje azt.

17.  A biztonsági mentési igényeknek megfelelő teljes ütemezés módosítása.

    ![NetBackup felügyeleti konzolon, teljes ütemezés módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18.  Módosítsa a start-ablakban.

    ![NetBackup felügyeleti konzolján, a start ablak módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19.  A végső ütemezés így néz ki:

    ![NetBackup felügyeleti konzolján, a végső ütemezése](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Másodlagos biztonsági mentési cél StorSimple beállítása

> [!NOTE]
>Adatok visszaállítása biztonsági mentésből van a felhőhöz már rétegzett felhő sebességek fordulhat elő.

Ebben a modellben (eltérő StorSimple) adathordozóra rendelkeznie kell egy ideiglenes gyorsítótár szolgál. Például egy redundáns tömbje (RAID) független lemezek kötet segítségével terület, a bemeneti/kimeneti (I/O) és a sávszélesség. RAID 5, 50 és 10 használatát javasoljuk.

A következő ábrán látható tipikus rövid távú megőrzés (a kiszolgáló) helyi köteteket, és hosszú távú megőrzési archiválja a köteteket. Ebben a forgatókönyvben minden biztonsági mentés futtatása a helyi (a kiszolgáló) a RAID-köteten. A biztonsági mentése rendszeres időközönként ismétlődő és archivált archívumokat kötetre. Fontos a helyi (a kiszolgáló) a RAID-kötet méretezése, hogy a rövid távú megőrzési kapacitás és teljesítménybeli követelményeit is képes kezelni.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>A másodlagos biztonsági mentési cél GFS példaként StorSimple

![StorSimple, a másodlagos biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

Az alábbi táblázat bemutatja, hogyan állíthatja be biztonsági mentéseket a helyi és a StorSimple lemezek futtatásához. Ez magában foglalja az egyes és a teljes kapacitás követelményeinek.

### <a name="backup-configuration-and-capacity-requirements"></a>Biztonsági mentési konfigurációhoz és kapacitásigények

| Biztonsági mentés típusa és a megőrzési | Konfigurált tároló | Méret (TiB) | GFS szorzója | Teljes kapacitás\* (TiB) |
|---|---|---|---|---|
| (Teljes és növekményes) 1 hét |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| StorSimple hét 2 – 4 |StorSimple lemez (hosszú távú) | 1 | 4 | 4 |
| Havi teljes |StorSimple lemez (hosszú távú) | 1 | 12 | 12 |
| Éves teljes |StorSimple lemez (hosszú távú) | 1 | 1 | 1 |
|GFS kötetek méretkövetelményt |  |  |  | 18*|
\*Teljes kapacitás 17 TiB a StorSimple-lemezek és a helyi RAID kötetre 1 TiB tartalmaz.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS példa ütemezés: GFS Elforgatás heti, havi és éves ütemezése

| Hét | Korlátlan | Növekményes nap 1 | Növekményes napja 2 | Növekményes nap 3 | Növekményes naponta 4 | Növekményes napja 5 |
|---|---|---|---|---|---|---|
| 1 hét | Helyi RAID kötetre  | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre |
| 2 hét | StorSimple hét 2 – 4 |   |   |   |   |   |
| Hét 3 | StorSimple hét 2 – 4 |   |   |   |   |   |
| 4 hét | StorSimple hét 2 – 4 |   |   |   |   |   |
| Havi | StorSimple havonta |   |   |   |   |   |
| Éves ütemezéshez | StorSimple évente  |   |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple-köteteket rendelhet egy NetBackup archiválási és a párhuzamos feladat

Mivel NetBackup azokat a beállításokat a tárolási és media management kínál, javasoljuk, hogy olvassa el a tárolási életciklusának (SLP-ből) házirend követelményeinek megfelelően felmérni Veritas vagy a NetBackup felelős mérnök.

Miután meghatározta a kezdeti lemezkészleteket, meg kell határoznia a három további tárhely életciklusokkal, összesen négy házirendek:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple-köteteket hozzárendelése egy NetBackup archiválási és a párhuzamos feladat

1.  A NetBackup felügyeleti konzolban válassza **tárolási** > **tárolási életciklusokkal** > **új tárolási életciklusra vonatkozó szabályzata**.

    ![NetBackup felügyeleti konzolon, új tárolási életciklusra vonatkozó szabályzata](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2.  Adjon meg egy nevet a pillanatképet, majd válassza ki **Hozzáadás**.

3.  Az a **új művelet** párbeszédpanel a **tulajdonságok** lapon, a **művelet**, jelölje be **biztonsági mentés**. Válassza ki a kívánt értékeket **Destination tároló**, **megőrzési típus**, és **megőrzési időszak**. Kattintson az **OK** gombra.

    ![NetBackup felügyeleti konzolon, új művelet párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

    Ez az első biztonsági mentés és a tárház határoz meg.

4.  Lehetőséget, és jelölje ki az előző művelet, és válassza ki **Hozzáadás**. Az a **módosítás tárolási művelet** párbeszédpanelen jelölje ki a kívánt értékek **Destination tároló**, **megőrzési típus**, és **megőrzési időszak**.

    ![NetBackup felügyeleti konzolján, a módosítás tárolási művelet párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5.  Lehetőséget, és jelölje ki az előző művelet, és válassza ki **Hozzáadás**. Az a **új tárolási életciklusra vonatkozó szabályzata** párbeszédpanelen adjon hozzá a havi biztonsági mentései egy év.

    ![NetBackup felügyeleti konzolon, új tárolási életciklusra vonatkozó szabályzata párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6.  Ismételje meg a 4-5 mindaddig, amíg a szükséges átfogó SLP-ből adatmegőrzési létrehozott.

    ![NetBackup felügyeleti konzol, az új tárolási életciklusra vonatkozó szabályzata párbeszédpanelen Hozzáadás házirendek](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7.  Ha befejezte a meghatározása az SLP-ből adatmegőrzési **házirend**, a biztonsági mentési házirend meghatározása ismertetett lépéseket követve [NetBackup biztonságimásolat-készítő feladat hozzárendelése a StorSimple-kötetek](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8.  A **ütemezések**, a a **ütemezés módosítása** párbeszédpanel, kattintson a jobb gombbal **teljes**, majd válassza ki **módosítása**.

    ![NetBackup felügyeleti konzolon, ütemezés módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9.  Válassza ki a **bírálja felül a csoportházirend-tároló kiválasztása** jelölőnégyzetet, majd válassza ki az SLP-ből adatmegőrzési 1-6. lépésben létrehozott.

    ![NetBackup felügyeleti konzolon, felülbírálás házirend tároló kiválasztása](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10.  Válassza ki **OK**, és ismételje meg a növekményes biztonsági mentés ütemezése.

    ![NetBackup felügyeleti konzolján, a növekményes biztonsági mentések ütemezésének módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Biztonsági mentés típusa megőrzési | Méret (TiB) | GFS szorzója\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Heti teljes |  1  |  4 | 4  |
| Napi növekményes  | 0.5  | 20 (ciklust megegyeznek havonta hetek száma) | 12 (további kvótához 2) |
| Havi teljes  | 1 | 12 | 12 |
| Éves teljes | 1  | 10 | 10 |
| GFS követelmény  |     |     | 38 |
| További kvótát  | 4  |    | 42 teljes GFS követelmény |
\*A GFS többszöröző példányszám történő védelmére, és a biztonsági mentési házirend követelményeinek megfelelően szüksége.

## <a name="storsimple-cloud-snapshots"></a>StorSimple felhőalapú pillanatfelvételek

StorSimple felhőalapú pillanatfelvételek a StorSimple eszköz található adatok védelme. A felhő pillanatkép megegyezik egy külső létesítmény helyi másolatot tartalmazó szalagok szállítási. Azure georedundáns tárolás használata, ha egy felhő-pillanatfelvételt létrehozása megegyezik szállítási mentést tartalmazó szalagok több különböző helyére. Ha eszköz visszaállítása után egy olyan vészhelyzet esetén, előfordulhat, hogy egy másik StorSimple eszköz online állapotba, és feladatátvétellel. A feladatátvétel után akkor tudná a legutóbbi felhő pillanatképből (felhő sebességek) adatok eléréséhez.

A következő szakasz ismerteti, hogyan indítsa el, és törölje a StorSimple felhőalapú pillanatfelvételek biztonsági mentés utáni feldolgozás során rövid parancsprogram létrehozásához.

> [!NOTE]
> Létrehozott pillanatfelvételek manuálisan vagy programon keresztül ne hajtsa végre a StorSimple snapshot elévülési szabályzatának. Ezeket a pillanatképeket manuálisan vagy programon keresztül törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Indítsa el, és törölje a felhő pillanatképeket parancsfájl használatával

> [!NOTE]
> Gondosan mérje fel a megfelelőség és az adatok megőrzési következményekkel a StorSimple snapshot törlése előtt. A biztonsági mentés utáni parancsfájl futtatásával kapcsolatos további információkért lásd: a [NetBackup dokumentáció](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Biztonsági mentési életciklusa

![Biztonsági mentési életciklus diagramja](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Követelmények

-   A parancsfájlt futtató kiszolgáló Azure felhőalapú erőforrásokhoz való hozzáféréssel kell rendelkeznie.
-   A felhasználói fióknak a szükséges engedélyekkel kell rendelkeznie.
-   A StorSimple biztonsági mentési házirend, a társított StorSimple kötetekkel kell beállítva, de nincs bekapcsolva.
-   Szüksége lesz a StorSimple-erőforrás nevét, a regisztrációs kulcsot, az eszköz nevét és biztonsági mentési házirend-azonosítóhoz.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Indítsa el, vagy egy felhő-pillanatfelvételt törlése

1.  [Telepítse az Azure PowerShellt](/powershell/azure/overview).
2. Letöltési és telepítési [kezelése-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-parancsfájlt.
3. A parancsfájl futtatása a kiszolgálón futtassa a PowerShell rendszergazdaként. Győződjön meg arról, hogy a parancsprogram futtatása `-WhatIf $true` mi változása a parancsfájl biztosítják. Ha az ellenőrzés befejeződött, adja át `-WhatIf $false`. Futtassa az alábbi parancsot:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  A parancsfájl hozzáadása a biztonságimásolat-készítő feladat NetBackup. Ehhez a NetBackup feladat beállításainak módosítása előzetes feldolgozás és utáni parancsok feldolgozásakor.

> [!NOTE]
> Azt javasoljuk, hogy a StorSimple felhőalapú pillanatfelvétel a biztonsági mentési házirend utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. További információ biztonsági mentése és visszaállítása a biztonságimásolat-készítő alkalmazás környezet segítséget nyújtanak az RPO és RTO, lépjen kapcsolatba a biztonsági mentési felelős mérnök.

## <a name="storsimple-as-a-restore-source"></a>StorSimple visszaállítási forrásként

Visszaállítja a StorSimple eszköz munkahelyi hasonlóan a bármely blokktárolóeszköz visszaállítások. Az adatok, amelyek a felhőbe többszintű visszaállítások felhő sebességek következik be. A helyi adatok visszaállítások elvégzi az eszköz a helyi lemez sebessége. A visszaállítás áttelepítésről további információkért lásd: a [NetBackup dokumentáció](http://www.veritas.com/docs/000094423). Azt javasoljuk, hogy megfeleljenek NetBackup visszaállítási ajánlott eljárások.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvételi és katasztrófa-helyreállítás

> [!NOTE]
> Biztonsági mentési cél forgatókönyvek esetén a StorSimple felhő készülék visszaállítási cél nem támogatott.

Egy olyan vészhelyzet esetén számos tényező okozhatja. Az alábbi táblázat általános vész-helyreállítási eljárással.

| Forgatókönyv | Gyakorolt hatás | Helyreállítása | Megjegyzések |
|---|---|---|---|
| A StorSimple eszköz hibája | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a hibás eszközt, és végezze el [StorSimple feladatátvétel és vész-helyreállítási](storsimple-device-failover-disaster-recovery.md). | Ha egy eszköz a helyreállítás után visszaállítást végezhet van szüksége, teljes adatkészletek működő lekért a felhőben az új eszköz. Minden olyan felhőalapú sebességgel. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a vizsgált és a helyi eszközön réteghez, amely időigényes folyamat lehet, hogy a felhő szintjén lekért összes biztonsági mentés. |
| NetBackup: kiszolgálóhiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | A biztonsági mentési kiszolgáló újraépítése, és végezze el az adatbázis visszaállítása. | Építse újra kell, vagy állítsa vissza a NetBackup kiszolgáló, a vész-helyreállítási helyen. Állítsa vissza az adatbázist a legutóbbi pontnak. A visszaállított NetBackup adatbázis nincs szinkronban vannak a legújabb biztonsági mentési feladatok, ha az indexelést és katalogizálni szükség. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a vizsgált és a felhő szintjén a helyi eszközön réteghez lekért összes biztonsági mentés. Így további időigényes. |
| A helykiszolgáló biztonsági mentése és a StorSimple elvesztését eredményezi helyen sikertelen | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, és helyreállíthatja a NetBackup. | Először állítsa vissza a StorSimple, és helyreállíthatja a NetBackup. Ha egy eszköz a helyreállítás után visszaállítást végezhet van szüksége, a teljes adatkészletet működő lekért a felhőben az új eszköz. Minden olyan felhőalapú sebességgel. |

## <a name="references"></a>Referencia

Ez a cikk a hivatkozott a következő dokumentumokat:

- [StorSimple-többutas i/o-telepítő](storsimple-configure-mpio-windows-server.md)
- [Tárolási forgatókönyvek: a dinamikus kiosztás](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Meghajtók GPT használata](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Állítson be megosztott mappák árnyékmásolatai](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Következő lépések

- További tudnivalók a [biztonságimásolat-készletből való visszaállítása](storsimple-restore-from-backup-set-u2.md).
- További áttelepítésről [eszköz feladatátvételi és katasztrófa-helyreállítás](storsimple-device-failover-disaster-recovery.md).
