---
title: "A biztonsági mentés céljaként Veeam a StorSimple 8000 sorozat |} Microsoft Docs"
description: "A StorSimple biztonsági mentési cél konfiguráció Veeam ismerteti."
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
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: cc1c7a3f77af76c451bb6e97a081a01c119333b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>Biztonsági mentési cél a Veeam StorSimple

## <a name="overview"></a>Áttekintés

Az Azure StorSimple hibrid felhőalapú tárolási megoldás a Microsoft. StorSimple-címek exponenciális adatmennyiség-növekedés bonyolultságára segítségével egy Azure Storage-fiókot a helyszíni megoldás, és automatikusan rétegezési adatok kiterjesztése a helyszíni és felhőalapú tárolására.

Ez a cikk arról lesz szó Veeam és ajánlott eljárások a mindkét megoldás integrálása StorSimple integrációját. Azt is ajánlásokat a legjobb integrálása a StorSimple Veeam beállításával. A Microsoft késlelteti a Veeam gyakorlati tanácsok, biztonsági mentési mérnökök és rendszergazdák az a legjobb módszer Veeam beállítása az egyes biztonsági mentés követelményeinek, és a szolgáltatásszint-szerződések (SLA).

Azt mutatja be a konfigurációs lépéseket és a főbb fogalmait, bár ez a cikk semmiképpen sem részletes konfigurációs vagy a telepítési útmutató is. Feltételezzük, hogy az alapvető összetevői és az infrastruktúra működő sorrendben és készen áll a fogalmakat, azt a leíró támogatja.

### <a name="who-should-read-this"></a>Ez célközönsége?

A cikkben szereplő információkat biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolási fejlesztők ismerő, tárolási, a Windows Server 2012 R2, Ethernet, felhőszolgáltatások és Veeam hasznos lesz.

### <a name="supported-versions"></a>Támogatott verziók

-   Veeam 9 és újabb verziók
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
![StorSimple rétegezési diagramja](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Tárkapacitás | 8100 | 8600 |
|---|---|---|
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

![StorSimple, elsődleges biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Elsődleges célja a biztonsági mentési logikai lépések

1.  A helykiszolgáló biztonsági mentése a cél biztonságimásolat-készítő ügynök kapcsolódik, és a biztonságimásolat-készítő ügynök adatátvitelt a helykiszolgáló biztonsági mentése.
2.  A helykiszolgáló biztonsági mentése írja az adatokat a StorSimple rétegzett kötet.
3.  A helykiszolgáló biztonsági mentése a katalógus-adatbázis frissíti, és majd befejezi a biztonsági mentési feladat.
4.  A pillanatkép-parancsfájl elindítja a StorSimple snapshot felhőkezelő (kezdeti vagy törlése).
5.  A helykiszolgáló biztonsági mentése törli a lejárt biztonsági mentések adatmegőrzési szabály alapján.

### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállítási logikai lépésre

1.  A helykiszolgáló biztonsági mentése elindul, a megfelelő adatok helyreállításához a tárolási tárházból.
2.  A biztonságimásolat-készítő ügynök az adatokat fogad a helykiszolgáló biztonsági mentése.
3.  A helykiszolgáló biztonsági mentése a visszaállítási feladat befejeződik.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple másodlagos biztonsági mentési cél

Ebben az esetben a StorSimple-köteteket elsősorban a hosszú távú megőrzési vagy használatosak archiválása.

Az alábbi ábra az architektúra látható, mely kezdeti biztonsági mentései, és egy nagy teljesítményű célkötet visszaállítja. Ezek a biztonsági másolatok másolta, és a storsimple-kötet archiválva rétegzett kötet beállított ütemezés szerint.

Fontos a nagy teljesítményű kötet méretének, hogy az adatmegőrzési házirend kapacitást és teljesítményt követelményeit is képes kezelni.

![StorSimple, a másodlagos biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>A biztonsági mentési logikai lépések másodlagos cél

1.  A helykiszolgáló biztonsági mentése a cél biztonságimásolat-készítő ügynök kapcsolódik, és a biztonságimásolat-készítő ügynök adatátvitelt a helykiszolgáló biztonsági mentése.
2.  A helykiszolgáló biztonsági mentése nagy teljesítményű tárolást írja az adatokat.
3.  A helykiszolgáló biztonsági mentése a katalógus-adatbázis frissíti, és majd befejezi a biztonsági mentési feladat.
4.  A helykiszolgáló biztonsági mentése egy megőrzési házirend alapján StorSimple biztonsági mentések másolja.
5.  A pillanatkép-parancsfájl elindítja a StorSimple snapshot felhőkezelő (kezdeti vagy törlése).
6.  A helykiszolgáló biztonsági mentése törli a lejárt biztonsági mentések adatmegőrzési szabály alapján.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél visszaállítási logikai lépésre

1.  A helykiszolgáló biztonsági mentése elindul, a megfelelő adatok helyreállításához a tárolási tárházból.
2.  A biztonságimásolat-készítő ügynök az adatokat fogad a helykiszolgáló biztonsági mentése.
3.  A helykiszolgáló biztonsági mentése a visszaállítási feladat befejeződik.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezéséhez

A megoldás három lépésből áll:

1. A hálózati infrastruktúra előkészítése.
2. A StorSimple eszköz üzembe helyezése a biztonsági mentés céljaként.
3. Veeam telepítése.

A következő szakaszokban részletesen tárgyalt egyes lépéseit.

### <a name="set-up-the-network"></a>A hálózat beállítása

StorSimple olyan megoldás, amely integrálva van az Azure felhőben, mert a StorSimple egy aktív és az Azure felhőben működő kapcsolatot igényel. A kapcsolat például felhőalapú pillanatfelvételek, kezelése és metaadatok adatátviteli műveletek elvégzéséhez használható, és régebbi réteghez, kisebb használt adatokat pedig Azure felhőalapú tárolást.

A megoldás optimális azt javasoljuk, hogy pontosan kövesse az alábbi hálózati gyakorlati tanácsok:

-   A hivatkozás, amely összeköti a StorSimple rétegezéséhez Azure meg kell felelnie a sávszélesség-követelményekkel. Ennek érdekében a megfelelő szint szolgáltatásminőség (QoS) az infrastruktúra alkalmazásával kapcsolókra megfelel-e a helyreállítási Időkorlát és a helyreállítási idő célkitűzése (RTO) SLA-k.
-   Maximális Azure Blob storage-hozzáférési késéseket körülbelül 80 ms kell lennie.

### <a name="deploy-storsimple"></a>StorSimple telepítése

Részletes üzembe helyezési útmutatót a StorSimple, lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Veeam telepítése

Veeam gyakorlati tanácsok a telepítéshez, lásd: [Veeam biztonsági mentés és a replikációs gyakorlati tanácsok](https://bp.veeam.expert/), és olvassa el a felhasználói útmutatóban talál: [Veeam Súgó (technikai dokumentáció)](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Ne használjon átnyúló kötetek (hozta létre a Windows Lemezkezelés). Átnyúló kötetek nem támogatottak.
- Formázza a használatával foglalásiegység-méret 64 KB-os NTFS-köteteket.
- A StorSimple-köteteket közvetlenül a Veeam kiszolgáló hozzárendelését.
    - ISCSI használata fizikai kiszolgálók.


## <a name="best-practices-for-storsimple-and-veeam"></a>Gyakorlati tanácsok a StorSimple és Veeam

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

### <a name="veeam-best-practices"></a>Veeam gyakorlati tanácsok

-   A Veeam adatbázis kell elhelyezkednie, a kiszolgáló és a StorSimple-kötet nem található.
-   Vész-helyreállítási adatbázis biztonsági mentése a Veeam a StorSimple-kötet.
-   Ez a megoldás Veeam teljes és növekményes biztonsági mentések támogatjuk. Azt javasoljuk, hogy ne használjon szintetikus és a különbözeti biztonsági mentés.
-   Biztonsági mentési adatok fájljaihoz tartalmaznia kell egy adott feladat csak azokat az adatokat. Például nincs adathordozó hozzáfűzi között különböző feladatok engedélyezettek.
-   Kapcsolja ki a feladat ellenőrzése. Szükség esetén ellenőrzése után a legújabb biztonsági mentési feladat lehet ütemezni. Fontos állapítsa meg, hogy ez a feladat hatással van a biztonsági mentési ablakot.
-   Kapcsolja be az adathordozó előtti foglalása.
-   Győződjön meg arról, hogy párhuzamos feldolgozást végző be van kapcsolva.
-   Kikapcsolja a tömörítést.
-   Kapcsolja ki a deduplikáció a biztonsági mentési feladathoz.
-   Optimalizálás beállítása **LAN cél**.
-   Kapcsolja be a **létrehozás aktív teljes biztonsági mentés** (minden második héten).
-   A biztonsági mentési tárházban beállítása **-VM biztonságimásolat-fájlok használata**.
-   Állítsa be **használhatja az egyes feladat több feltöltés adatfolyamokat** való **8** (legfeljebb 16 engedélyezett). Állítsa be úgy a szám felfelé vagy lefelé a StorSimple eszköz CPU-használat alapján.

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

## <a name="set-up-veeam-storage"></a>Veeam tárolás beállítása

### <a name="to-set-up-veeam-storage"></a>Veeam tárolás beállítása

1.  A Veeam biztonsági mentési és a replikáció-konzolon a **tárház eszközök**, és **biztonsági infrastruktúra**. Kattintson a jobb gombbal **biztonsági mentés adattárak**, majd válassza ki **biztonsági mentés tárház hozzáadása**.

    ![Veeam felügyeleti konzol, biztonsági mentési tárház lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Az a **új biztonsági másolat tárház** párbeszédpanelen adja meg a nevét és leírását a tárház. Válassza ki **következő**.

    ![Veeam felügyeleti konzol, nevét és leírását lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  A típus kiválasztása **Microsoft Windows server**. Válassza ki a Veeam kiszolgálót. Válassza ki **következő**.

    ![Veeam felügyeleti konzol, biztonsági mentési tárház select típusa](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Adja meg, hogy **hely**, keresse meg és válassza ki a kötetet. Válassza ki a **korlátozza az egyidejű feladatok maximális:** jelölje be a jelölőnégyzetet, majd az értékét állítsa **4**. Ez biztosítja, hogy csak négy virtuális lemezek feldolgozott egyidejűleg minden virtuális gép (VM) feldolgozása közben. Válassza ki a **speciális** gombra.

    ![Veeam felügyeleti konzolon válassza a kötet](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Az a **tárolási kompatibilitási beállítások** párbeszédpanelen jelölje ki a **-VM biztonságimásolat-fájlok használata** jelölőnégyzetet.

    ![Veeam felügyeleti konzol, tárolási kompatibilitási beállítások](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Az a **új biztonsági másolat tárház** párbeszédpanelen jelölje ki a **vPower NFS szolgáltatás a csatlakoztatási kiszolgálón (ajánlott) engedélyezése** jelölőnégyzetet. Válassza ki **következő**.

    ![Veeam felügyeleti konzol, biztonsági mentési tárház lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Tekintse át a beállításokat, majd válassza ki **következő**.

    ![Veeam felügyeleti konzol, biztonsági mentési tárház lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    A tárház a Veeam kiszolgáló kerül.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Elsődleges biztonsági mentési cél StorSimple beállítása

> [!IMPORTANT]
> Adatok visszaállítása biztonsági mentésből van a felhőhöz már rétegzett felhő sebességek következik be.

Az alábbi ábrán egy tipikus kötetet a biztonsági mentési feladatot leképezése. Ebben az esetben a heti biztonsági mentései a szombat teljes lemezre van leképezve, és a növekményes biztonsági mentések hétfőtől péntekig növekményes lemezek hozzárendelését. Minden visszaállítások és biztonsági mentés a storsimple-kötet a rétegzett kötet.

![Elsődleges biztonsági mentési cél konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Elsődleges biztonsági mentési cél GFS StorSimple ütemezése – példa

Itt látható egy példa négy hét, a havi és éves GFS Elforgatás ütemezése:

| Gyakoriság vagy biztonsági mentés típusa | Korlátlan | Növekményes (1-5 nap)  |   
|---|---|---|
| Heti (1 – 4 hét) | Szombat | Hétfőtől péntekig |
| Havi  | Szombat  |   |
| Éves ütemezéshez | Szombat  |   |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple-köteteket rendelhet egy Veeam biztonsági mentési feladat

Elsődleges biztonsági mentési cél forgatókönyvhöz hozzon létre egy napi feladat az elsődleges Veeam StorSimple-kötet. Másodlagos biztonsági mentési cél forgatókönyvek esetében a közvetlenül csatlakoztatott tároló (DAS), a hálózathoz csatlakoztatott tárolás (NAS) vagy a lemezek álló, lemezcsoport (JBOD) tárolási csak napi feladat létrehozása.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple-köteteket hozzárendelése Veeam biztonságimásolat-készítő feladat

1.  A Veeam biztonsági mentési és a replikációs konzolban válassza **biztonsági mentés és a replikációs**. Kattintson a jobb gombbal **biztonsági mentés**, majd válassza ki **VMware** vagy **Hyper-V**, attól függően, a környezetben.

    ![Új biztonsági mentési feladat Veeam kezelőkonzoljában](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Az a **új biztonsági mentési feladat** párbeszédpanelen adja meg egy nevet és egy leírást a napi biztonsági mentési feladat.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Válassza ki a virtuális gép biztonsági másolatot.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Válassza ki a kívánt értékeket **biztonsági mentését a proxy** és **biztonsági mentés tárház**. Válasszon értéket **visszaállítási pontok megőrzése lemezen való** alapján a helyreállítási Időkorlát és Helyreállításiidő-definíciókat a környezetnek a helyileg csatlakoztatott tárolón. Válassza ki **speciális**.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Az a **speciális beállítások** párbeszédpanel a **biztonsági mentés** lapon jelölje be **növekményes**. Ügyeljen arra, hogy a **szintetikus teljes biztonsági mentés létrehozása rendszeresen** jelölőnégyzet nincs bejelölve. Válassza ki a **aktív teljes biztonsági mentés létrehozása rendszeresen** jelölőnégyzetet. A **aktív teljes biztonsági mentés**, jelölje be a **hetente a kijelölt napok** szombat jelölőnégyzetét.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Az a **tárolási** lapra, győződjön meg arról, hogy a **beágyazott adatok deduplikációjának engedélyezése** jelölőnégyzet nincs bejelölve. Válassza ki a **kizárási swap fájlblokkokat** jelölőnégyzetet, majd válassza ki a **kizárási törölt fájlblokkokat** jelölőnégyzetet. Állítsa be **tömörítési szint** való **nincs**. Az elosztott terhelésű teljesítmény és a deduplikáció, állítsa be **tárolóoptimalizálás** való **LAN cél**. Kattintson az **OK** gombra.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Veeam deduplikációs és tömörítési beállításaik kapcsolatos információkért lásd: [adattömörítés és a Deduplikáció](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Az a **szerkesztése biztonságimásolat-készítő feladat** párbeszédpanelen kiválaszthatja a **alkalmazásfigyelő feldolgozásának engedélyezése** (nem kötelező) jelölőnégyzetet.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat Vendég feldolgozási lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Adja meg az ütemezést fusson le napi adhat meg egyszerre.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat ütemezés lapon](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Másodlagos biztonsági mentési cél StorSimple beállítása

> [!NOTE]
> Adatok visszaállítása biztonsági mentésből van a felhőhöz már rétegzett felhő sebességek fordulhat elő.

Ebben a modellben (eltérő StorSimple) adathordozóra rendelkeznie kell egy ideiglenes gyorsítótár szolgál. Például egy redundáns tömbje (RAID) független lemezek kötet segítségével terület, a bemeneti/kimeneti (I/O) és a sávszélesség. RAID 5, 50 és 10 használatát javasoljuk.

Az alábbi ábrán látható, általában rövid távú megőrzési helyi (az a kiszolgáló) és a hosszú távú megőrzési archív kötetek. Ebben a forgatókönyvben minden biztonsági mentés futtatása a helyi (a kiszolgáló) a RAID-köteten. A biztonsági mentése rendszeres időközönként ismétlődő és archivált archív kötetre. Fontos a helyi (a kiszolgáló) a RAID-kötet méretezése, hogy a rövid távú megőrzési kapacitás és teljesítménybeli követelményeit is képes kezelni.

![StorSimple, mint a másodlagos biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>A másodlagos biztonsági mentési cél GFS példaként StorSimple

Az alábbi táblázat bemutatja, hogyan állíthatja be biztonsági mentéseket a helyi és a StorSimple lemezek futtatásához. Ez magában foglalja az egyes és a teljes kapacitás követelményeinek.

| Biztonsági mentés típusa és a megőrzési | Konfigurált tároló | Méret (TiB) | GFS szorzója | Teljes kapacitás\* (TiB) |
|---|---|---|---|---|
| (Teljes és növekményes) 1 hét |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| StorSimple hét 2 – 4 |StorSimple lemez (hosszú távú) | 1 | 4 | 4 |
| Havi teljes |StorSimple lemez (hosszú távú) | 1 | 12 | 12 |
| Éves teljes |StorSimple lemez (hosszú távú) | 1 | 1 | 1 |
|GFS kötetek méretkövetelményt |  |  |  | 18*|
\*Teljes kapacitás 17 TiB a StorSimple-lemezek és a helyi RAID kötetre 1 TiB tartalmaz.


### <a name="gfs-example-schedule"></a>GFS példa ütemezése

GFS Elforgatás heti, havi és éves ütemezése

| Hét | Korlátlan | Növekményes nap 1 | Növekményes napja 2 | Növekményes nap 3 | Növekményes naponta 4 | Növekményes napja 5 |
|---|---|---|---|---|---|---|
| 1 hét | Helyi RAID kötetre  | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre |
| 2 hét | StorSimple hét 2 – 4 |   |   |   |   |   |
| Hét 3 | StorSimple hét 2 – 4 |   |   |   |   |   |
| 4 hét | StorSimple hét 2 – 4 |   |   |   |   |   |
| Havi | StorSimple havonta |   |   |   |   |   |
| Éves ütemezéshez | StorSimple évente  |   |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple-köteteket rendelhet egy Veeam másolási feladat

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple-köteteket hozzárendelése egy Veeam másolási feladat

1.  A Veeam biztonsági mentési és a replikációs konzolban válassza **biztonsági mentés és a replikációs**. Kattintson a jobb gombbal **biztonsági mentés**, majd válassza ki **VMware** vagy **Hyper-V**, attól függően, a környezetben.

    ![Új biztonsági mentési feladat oldal Veeam kezelőkonzoljában](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Az a **új biztonsági másolat másolási feladat** párbeszédpanelen adja meg a nevét és leírását, a feladathoz.

    ![Új biztonsági mentési feladat oldal Veeam kezelőkonzoljában](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Válassza ki a virtuális gépeket szeretne dolgozni. A biztonsági mentésekből, majd válassza ki és a napi biztonsági mentéshez, korábban létrehozott.

    ![Új biztonsági mentési feladat oldal Veeam kezelőkonzoljában](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Objektumok kizárása a biztonsági másolat feladatot, ha szükséges.

5.  Válassza ki a biztonsági mentési tárház, és értéket **visszaállítási pontok tartása**. Ügyeljen arra, hogy válassza ki a **a következő visszaállítási pontok megőrzése archiválási célokból** jelölőnégyzetet. Adja meg a biztonsági mentés gyakoriságát, és válassza **speciális**.

    ![Új biztonsági mentési feladat oldal Veeam kezelőkonzoljában](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  A következő speciális beállításainak megadása:

    * Az a **karbantartási** lapra, kapcsolja ki a tárolási szintű sérülés őr.

    ![Veeam felügyeleti konzol, új biztonsági másolat feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Az a **tárolási** lap, ne feledje, hogy a deduplikáció és a tömörítést kikapcsolt.

    ![Veeam felügyeleti konzol, új biztonsági másolat feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Adja meg, hogy az adatátvitel közvetlen.

8.  Adja meg a biztonsági másolat időszak ütemezése szükség szerint, és majd a varázsló.

További információkért lásd: [hozzon létre biztonsági másolatot feladatokat](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple felhőalapú pillanatfelvételek

StorSimple felhőalapú pillanatfelvételek a StorSimple eszköz található adatok védelme. A felhő pillanatkép megegyezik egy külső létesítmény helyi másolatot tartalmazó szalagok szállítási. Azure georedundáns tárolás használata, ha egy felhő-pillanatfelvételt létrehozása megegyezik szállítási mentést tartalmazó szalagok több különböző helyére. Ha eszköz visszaállítása után egy olyan vészhelyzet esetén, előfordulhat, hogy egy másik StorSimple eszköz online állapotba, és feladatátvétellel. A feladatátvétel után akkor tudná a legutóbbi felhő pillanatképből (felhő sebességek) adatok eléréséhez.

A következő szakasz ismerteti, hogyan indítsa el, és törölje a StorSimple felhőalapú pillanatfelvételek biztonsági mentés utáni feldolgozás során rövid parancsprogram létrehozásához.

> [!NOTE]
> Létrehozott pillanatfelvételek manuálisan vagy programon keresztül ne hajtsa végre a StorSimple snapshot elévülési szabályzatának. Ezeket a pillanatképeket manuálisan vagy programon keresztül törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Indítsa el, és törölje a felhő pillanatképeket parancsfájl használatával

> [!NOTE]
> Gondosan mérje fel a megfelelőség és az adatok megőrzési következményekkel a StorSimple snapshot törlése előtt. A biztonsági mentés utáni parancsfájl futtatásával kapcsolatos további információkért a Veeam dokumentációjában talál.


### <a name="backup-lifecycle"></a>Biztonsági mentési életciklusa

![Biztonsági mentési életciklus diagramja](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Követelmények

-   A parancsfájlt futtató kiszolgáló Azure felhőalapú erőforrásokhoz való hozzáféréssel kell rendelkeznie.
-   A felhasználói fióknak a szükséges engedélyekkel kell rendelkeznie.
-   A StorSimple biztonsági mentési házirend, a társított StorSimple kötetekkel kell beállítva, de nincs bekapcsolva.
-   Szüksége lesz a StorSimple-erőforrás nevét, a regisztrációs kulcsot, az eszköz nevét és biztonsági mentési házirend-azonosítóhoz.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Indítsa el, vagy egy felhő-pillanatfelvételt törlése

1. [Telepítse az Azure PowerShellt](/powershell/azure/overview).
2. Letöltési és telepítési [kezelése-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-parancsfájlt.
3. A parancsfájl futtatása a kiszolgálón futtassa a PowerShell rendszergazdaként. Győződjön meg arról, hogy a parancsprogram futtatása `-WhatIf $true` mi változása a parancsfájl biztosítják. Ha az ellenőrzés befejeződött, adja át `-WhatIf $false`. Futtassa az alábbi parancsot:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4. A parancsfájl hozzáadása a biztonságimásolat-készítő feladat, szerkessze a Speciális beállítások Veeam feladat.

    ![Veeam biztonsági mentési speciális beállítások parancsfájlok lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Azt javasoljuk, hogy a StorSimple felhőalapú pillanatfelvétel a biztonsági mentési házirend utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. További információ biztonsági mentése és visszaállítása a biztonságimásolat-készítő alkalmazás környezet segítséget nyújtanak az RPO és RTO, lépjen kapcsolatba a biztonsági mentési felelős mérnök.

## <a name="storsimple-as-a-restore-source"></a>StorSimple visszaállítási forrásként

Visszaállítja a StorSimple eszköz munkahelyi hasonlóan a bármely blokktárolóeszköz visszaállítások. Az adatok, amelyek a felhőbe többszintű visszaállítások felhő sebességek következik be. A helyi adatok visszaállítások elvégzi az eszköz a helyi lemez sebessége.

A Veeam beolvasása gyors, a részletes, fájlszintű helyreállítási StorSimple használatával a a beépített explorer nézeteket a Veeam konzolon keresztül. Használja a Veeam szoftverkategóriák elemek, például e-mailek, az Active Directory-objektumok és a SharePoint-elem helyreállítása biztonsági másolatból. A helyreállítási teheti a helyszíni virtuális gép megszakítása nélkül. Az Azure SQL Database és az Oracle-adatbázisok időpontban helyreállítást is végezhet. Veeam és a StorSimple legyen a elemszintű helyreállítás gyorsan és egyszerűen az Azure-ból. További információ a visszaállításhoz, a Veeam dokumentációjában talál:

- A [Exchange-kiszolgáló](https://www.veeam.com/microsoft-exchange-recovery.html)
- A [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- A [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- A [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- A [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvételi és katasztrófa-helyreállítás

> [!NOTE]
> Biztonsági mentési cél forgatókönyvek esetén a StorSimple felhő készülék visszaállítási cél nem támogatott.

Egy olyan vészhelyzet esetén számos tényező okozhatja. Az alábbi táblázat általános vész-helyreállítási eljárással.

| Forgatókönyv | Gyakorolt hatás | Helyreállítása | Megjegyzések |
|---|---|---|---|
| A StorSimple eszköz hibája | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a hibás eszközt, és végezze el [StorSimple feladatátvétel és vész-helyreállítási](storsimple-device-failover-disaster-recovery.md). | Ha egy eszköz a helyreállítás után visszaállítást végezhet van szüksége, teljes adatkészletek működő lekért a felhőben az új eszköz. Minden olyan felhőalapú sebességgel. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a vizsgált és a helyi eszközön réteghez, amely időigényes folyamat lehet, hogy a felhő szintjén lekért összes biztonsági mentés. |
| Veeam: kiszolgálóhiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | A biztonsági mentési kiszolgáló újraépítése, és végezze el az adatbázis visszaállítása a [Veeam Súgó (technikai dokumentáció)](https://www.veeam.com/documentation-guides-datasheets.html).  | Építse újra kell, vagy állítsa vissza a Veeam kiszolgáló, a vész-helyreállítási helyen. Állítsa vissza az adatbázist a legutóbbi pontnak. A visszaállított Veeam adatbázis nincs szinkronban vannak a legújabb biztonsági mentési feladatok, ha az indexelést és katalogizálni szükség. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a vizsgált és a felhő szintjén a helyi eszközön réteghez lekért összes biztonsági mentés. Így további időigényes. |
| A helykiszolgáló biztonsági mentése és a StorSimple elvesztését eredményezi helyen sikertelen | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, és helyreállíthatja a Veeam. | Először állítsa vissza a StorSimple, és helyreállíthatja a Veeam. Ha egy eszköz a helyreállítás után visszaállítást végezhet van szüksége, a teljes adatkészletet működő lekért a felhőben az új eszköz. Minden olyan felhőalapú sebességgel. |


## <a name="references"></a>Referencia

Ez a cikk a hivatkozott a következő dokumentumokat:

- [StorSimple-többutas i/o-telepítő](storsimple-configure-mpio-windows-server.md)
- [Tárolási forgatókönyvek: a dinamikus kiosztás](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Meghajtók GPT használata](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Állítson be megosztott mappák árnyékmásolatai](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Következő lépések

- További tudnivalók a [biztonságimásolat-készletből való visszaállítása](storsimple-restore-from-backup-set-u2.md).
- További áttelepítésről [eszköz feladatátvételi és katasztrófa-helyreállítás](storsimple-device-failover-disaster-recovery.md).
