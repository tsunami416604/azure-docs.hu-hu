---
title: A StorSimple 8000 series és a biztonsági mentési Exec biztonsági mentési cél |} Microsoft Docs
description: A StorSimple biztonsági mentési cél konfiguráció Veritas Backup Exec ismerteti.
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
ms.openlocfilehash: 1326e4a84938c46a9e7acc10dd8ed94db708f62a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113085"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>A biztonsági mentési Exec biztonsági mentési cél StorSimple

## <a name="overview"></a>Áttekintés

Az Azure StorSimple hibrid felhőalapú tárolási megoldás a Microsoft. StorSimple exponenciális adatmennyiség-növekedés bonyolultságára úgy kezeli a helyszíni megoldás bővítményként Azure storage-fiók használatával, és automatikusan rétegezéséhez adatok között a helyszíni és felhőalapú tárolására.

Ez a cikk arról lesz szó Veritas Backup Exec és ajánlott eljárások a mindkét megoldás integrálása StorSimple integrációját. Azt is ajánlásokat a legjobb integrálása a StorSimple biztonsági mentés Exec beállításával. A Microsoft késlelteti a Veritas gyakorlati tanácsok, biztonsági mentési mérnökök és rendszergazdák az a legjobb módja a biztonsági mentési Exec beállítása az egyes biztonsági mentés követelményeinek, és a szolgáltatásszint-szerződések (SLA).

Azt mutatja be a konfigurációs lépéseket és a főbb fogalmait, bár ez a cikk semmiképpen sem részletes konfigurációs vagy a telepítési útmutató is. Feltételezzük, hogy az alapvető összetevői és az infrastruktúra működő sorrendben és készen áll a fogalmakat, azt a leíró támogatja.

### <a name="who-should-read-this"></a>Ez célközönsége?

A cikkben szereplő információkat biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolási fejlesztők ismerő, tárolási, a Windows Server 2012 R2, a Ethernet, a felhőszolgáltatások és a biztonsági mentési Exec hasznos lesz.

## <a name="supported-versions"></a>Támogatott verziók

-   [Biztonsági mentés Exec 16 és újabb verziók](http://backupexec.com/compatibility)
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
![StorSimple rétegezési diagramja](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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
| Helyi tárolási kapacitás | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Felhőalapú tárolási kapacitás | &gt; 200 TiB\* | &gt; 500 TiB\* |
\* Tárméret azt feltételezi, hogy nem a deduplikáció és a tömörítést.

**StorSimple kapacitások elsődleges és másodlagos biztonsági mentések tiltása**

| Biztonsági mentési forgatókönyv  | Helyi tárolási kapacitás  | Felhőalapú tárolási kapacitás  |
|---|---|---|
| Elsődleges biztonsági mentése  | A gyors helyreállítás helyi tárolóban tárolt legutóbbi biztonsági felel meg a helyreállítási időkorlát (RPO) | Felhő kapacitása a megfelelő biztonsági mentési előzményeit (RPO) |
| Másodlagos biztonsági mentése | Felhő kapacitása tárolható biztonsági mentési adatok másodlagos példányát  | –  |

## <a name="storsimple-as-a-primary-backup-target"></a>Elsődleges biztonsági mentési cél StorSimple

Ebben a forgatókönyvben a StorSimple-köteteket a biztonsági mentési alkalmazásra, amely a biztonsági mentések egyetlen tárháza jelennek meg. Az alábbi ábrán látható, amelyben az összes biztonsági mentés használata StorSimple rétegzett kötetek biztonsági mentést és helyreállítást megoldási.

![StorSimple, elsődleges biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple, a másodlagos biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A megoldás három lépésből áll:
1. A hálózati infrastruktúra előkészítése.
2. A StorSimple eszköz üzembe helyezése a biztonsági mentés céljaként.
3. Telepítse a biztonsági mentési Exec.

A következő szakaszokban részletesen tárgyalt egyes lépéseit.

### <a name="set-up-the-network"></a>A hálózat beállítása

StorSimple olyan megoldás, amely integrálva van az Azure felhőben, mert a StorSimple egy aktív és az Azure felhőben működő kapcsolatot igényel. A kapcsolat például felhőalapú pillanatfelvételek, felügyeleti és metaadatok adatátviteli műveletek elvégzéséhez használható, és régebbi réteghez, kisebb használt adatokat pedig Azure felhőalapú tárolást.

A megoldás optimális azt javasoljuk, hogy pontosan kövesse az alábbi hálózati gyakorlati tanácsok:

-   A hivatkozás, amely összeköti a StorSimple rétegezéséhez Azure meg kell felelnie a sávszélesség-követelményekkel. Ennek érdekében a szükséges szolgáltatásminőség (QoS) szint alkalmazása azokra a infrastruktúra kapcsolókra megfelel-e a helyreállítási Időkorlát és a helyreállítási idő célkitűzése (RTO) SLA-k.
-   Maximális Azure Blob storage-hozzáférési késéseket körülbelül 80 ms kell lennie.

### <a name="deploy-storsimple"></a>StorSimple telepítése

A StorSimple részletes telepítési útmutatásért lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Biztonsági mentési Exec telepítése

Biztonsági mentés Exec gyakorlati tanácsok a telepítéshez, lásd: [gyakorlati tanácsok a biztonsági mentés Exec telepítéshez](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>A megoldás beállítása

Ebben a szakaszban a bemutatjuk, konfigurációs példákat. A következő példák és javaslatok bemutatják az alapszintű és a alapvető végrehajtására. Ez a megvalósítás nem feltétlenül vonatkoznak közvetlenül az adott biztonsági mentés követelményeinek.

### <a name="set-up-storsimple"></a>StorSimple beállítása

| StorSimple telepítési feladatok  | További megjegyzések |
|---|---|
| A helyszíni StorSimple eszköz üzembe helyezése. | Támogatott verziók: 3 és újabb verziók frissítése. |
| Kapcsolja be a biztonsági mentési cél. | Ezek a parancsok használata, kapcsolja be, vagy kapcsolja ki a biztonsági mentési cél módot, és állapotának beolvasása. További információkért lásd: [távolról csatlakozhat a StorSimple eszköz](storsimple-remote-connect.md).</br> Biztonsági mentés módja bekapcsolása: `Set-HCSBackupApplianceMode -enable`. </br> Biztonsági mentés módja kikapcsolása: `Set-HCSBackupApplianceMode -disable`. </br> A biztonsági mentés módja beállítások aktuális állapot: `Get-HCSBackupApplianceMode`. |
| A kötet, amely tárolja a biztonsági mentési adatok közös kötettároló létrehozása. A kötettároló összes adatot deduplikált. | StorSimple kötettárolók deduplikációs tartományok definiálása.  |
| StorSimple-köteteket hozhat létre. | Hozzon létre köteteket, mérete megközelíti a várható használati lehetséges, mert a kötet mérete befolyásolja a pillanatkép-időtartam felhő. A kötet méretének kapcsolatos információkért olvassa el [adatmegőrzési](#retention-policies).</br> </br> Használja a StorSimple rétegzett kötet, és válassza ki a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet. </br> Csak a helyileg rögzített kötetek használata nem támogatott. |
| Hozzon létre egy egyedi StorSimple biztonsági mentési házirendet a biztonsági mentési tároló kötetek. | A StorSimple biztonsági mentési házirend a kötet konzisztencia csoportját határozza meg. |
| Tiltsa le az ütemezést, ahogyan a pillanatképek érvényessége lejár. | A pillanatképek utófeldolgozási műveletként aktiválódnak. |

### <a name="set-up-the-host-backup-server-storage"></a>A fogadó helykiszolgáló biztonsági mentése tárolás beállítása

Állítsa be a helykiszolgáló biztonsági mentése tárolók szerint ezeket az irányelveket:  

- Ne használjon átnyúló kötetek (hozta létre a Windows Lemezkezelés). A program lemezek használata nem támogatott.
- Formázza a NTFS fájlrendszerrel 64 KB-os foglalási méretű köteteket.
- A StorSimple-köteteket közvetlenül a biztonsági mentés Exec kiszolgáló hozzárendelését.
    - ISCSI használata fizikai kiszolgálók.
    - Használja a csatlakoztatott lemezeket virtuális kiszolgálók.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Gyakorlati tanácsok a StorSimple és a biztonsági mentés Exec

Az alábbi szakaszok az igényeinek megfelelően a megoldás beállítása.

### <a name="operating-system-best-practices"></a>Operációs rendszer ajánlott eljárások

-   Tiltsa le a Windows Server titkosítás és a deduplikáció az NTFS fájlrendszerhez.
-   Tiltsa le a StorSimple-köteteket a Windows Server töredezettségmentesítés.
-   Tiltsa le a Windows Server indexelő a StorSimple-köteteket.
-   Víruskereső futtatni a forrásállomás (nem szemben a StorSimple-köteteket).
-   Kapcsolja ki az alapértelmezett [Windows Server karbantartási](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) a Feladatkezelő. Ehhez a következő módszerek valamelyikével:
   - Kapcsolja ki a karbantartási konfiguráló a Windows Feladatütemező.
   - Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) a Windows Sysinternals. PsExec letöltése után az Azure PowerShell futtassa egy rendszergazda, és írja be:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>StorSimple gyakorlati tanácsok

  -   Ne feledje, hogy a rendszer frissíti a StorSimple eszköz [3-as vagy újabb frissítés](storsimple-install-update-3.md).
  -   Elkülönítheti az iSCSI és a felhőalapú forgalom. StorSimple és a helykiszolgáló biztonsági mentése közötti forgalom dedikált iSCSI-kapcsolat használata.
  -   Győződjön meg arról, hogy a StorSimple eszköz-e egy dedikált célhelyet. Vegyes munkaterhelések nem támogatottak, mert ezek hatással a RTO és a helyreállítási Időkorlát.

### <a name="backup-exec-best-practices"></a>Biztonsági mentési Exec gyakorlati tanácsok

-   Biztonsági mentési Exec telepítenie kell egy helyi meghajtó a kiszolgáló, nem pedig a StorSimple-kötet.
-   Állítsa be a biztonsági mentés Exec tárolási **az egyidejű írási műveletek** a megengedett maximumot.
    -   Állítsa be a biztonsági mentés Exec tárolási **blokk és puffer mérete** 512 KB.
    -   Kapcsolja be a biztonsági mentés Exec tárolási **olvasási és írási pufferelt**.
-   A StorSimple biztonsági mentés Exec teljes és növekményes biztonsági mentések támogatja. Azt javasoljuk, hogy ne használjon szintetikus és a különbözeti biztonsági mentés.
-   Biztonsági mentési adatok szerepelnie kell a fájlok csak az adott feladat. Például nincs adathordozó hozzáfűzi között különböző feladatok engedélyezettek.
-   Tiltsa le az ellenőrzési feladatot. Szükség esetén ellenőrzése után a legújabb biztonsági mentési feladat lehet ütemezni. Fontos állapítsa meg, hogy ez a feladat hatással van a biztonsági mentési ablakot.
-   Válassza ki **tárolási** > **a lemez** > **részletek** > **tulajdonságok**. Kapcsolja ki a **lemezterületet előre**.

A legújabb biztonsági mentés Exec beállításokat és ezek a követelmények megvalósításához ajánlott eljárások: [a Veritas webhely](https://www.veritas.com).

## <a name="retention-policies"></a>Adatmegőrzési házirendek

A biztonsági mentés megőrzési házirend leggyakoribb egyik szerzett, Édesapja és fia (GFS) házirend. A GFS házirendek egy növekményes biztonsági mentés napi és heti és havi végzett teljes biztonsági mentés. A csoportházirend-eredményhez hat StorSimple a rétegzett kötet. Egy kötet heti, havi és éves teljes biztonsági mentés tartalmaz. A többi öt kötet napi növekményes biztonsági mentések tárolására.

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
\* A GFS többszöröző példányszám történő védelmére, és a biztonsági mentési házirend követelményeinek megfelelően szüksége.

## <a name="set-up-backup-exec-storage"></a>Biztonsági mentés Exec tárolás beállítása

### <a name="to-set-up-backup-exec-storage"></a>A biztonsági mentés Exec tárolás beállítása

1.  A biztonsági mentés Exec felügyeleti konzolban válassza **tárolási** > **tárolás konfigurálása** > **lemezalapú tárolás**  >   **Következő**.

    ![Biztonsági mentés Exec kezelési konzolján, a tárolási lapjának konfigurálása](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Válassza ki **lemezegységet**, majd válassza ki **következő**.

    ![Biztonsági mentési Exec kezelőkonzoljában válassza tárolási lap](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Adjon meg egy reprezentatív nevet, például **szombat teljes**, és egy leírást. Kattintson a **Tovább** gombra.

    ![Biztonsági mentési Exec felügyeleti konzol, nevét és leírását lap](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Válassza ki a lemezt, ahol szeretné létrehozni a lemezes tárolóeszköz, majd válassza ki **következő**.

    ![Biztonsági mentési Exec felügyeleti konzol, tároló lemez kiválasztása lap](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Az írási műveletek száma növelhető **16**, majd válassza ki **következő**.

    ![Biztonsági mentési Exec kezelőkonzolon egyidejű írási műveletek beállítások lap](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Tekintse át a beállításokat, majd válassza ki **Befejezés**.

    ![Biztonsági mentési Exec felügyeleti konzol, tárolási konfiguráció összegző lap](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Minden kötet hozzárendelés végén javasolt felel meg a tárolási eszköz beállítások módosításával [gyakorlati tanácsok a StorSimple és a biztonsági mentés Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Biztonsági mentési Exec felügyeleti konzol, tárolási eszköz beállítások lap](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Ismételje 1-7 végzett biztonsági mentés Exec hozzárendeléséhez a StorSimple-köteteket.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Elsődleges biztonsági mentési cél StorSimple beállítása

> [!NOTE]
> Adatok visszaállítása biztonsági mentésből van a felhőhöz már rétegzett felhő sebességek következik be.

Az alábbi ábrán egy tipikus kötetet a biztonsági mentési feladatot leképezése. Ebben az esetben a heti biztonsági mentései a szombat teljes lemezre van leképezve, és a növekményes biztonsági mentések hétfőtől péntekig növekményes lemezek hozzárendelését. Minden visszaállítások és biztonsági mentés a storsimple-kötet a rétegzett kötet.

![Elsődleges biztonsági mentési cél konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Elsődleges biztonsági mentési cél GFS StorSimple ütemezése – példa

Itt látható egy példa négy hét, a havi és éves GFS Elforgatás ütemezése:

| Gyakoriság vagy biztonsági mentés típusa | Korlátlan | Növekményes (1-5 nap)  |   
|---|---|---|
| Heti (1 – 4 hét) | Szombat | Hétfőtől péntekig |
| Havi  | Szombat  |   |
| Évente | Szombat  |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-köteteket rendelhet egy biztonsági mentési Exec biztonsági mentési feladat

Az alábbi sorrendben feltételezi, hogy a biztonsági mentés Exec és a cél gazdagép konfigurálva összhangban a biztonsági mentés Exec ügynök irányelveket.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-köteteket rendelhet egy biztonsági mentési Exec biztonsági mentési feladat

1.  A biztonsági mentés Exec felügyeleti konzolban válassza **állomás** > **biztonsági mentés** > **lemezre történő biztonsági mentés**.

    ![Biztonsági mentési Exec felügyeleti konzol, a kiválasztott gazdagép, a biztonsági mentés és a lemezes biztonsági mentés](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Az a **biztonságimásolat-definíció tulajdonságai** párbeszédpanel **biztonsági mentés**, jelölje be **szerkesztése**.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonságimásolat-definíció tulajdonságai párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  A teljes és növekményes biztonsági mentések beállítása, hogy az RPO és RTO követelményeknek, és ajánlott eljárások Veritas felelnek meg.

4.  Az a **biztonsági mentési beállítások** párbeszédpanelen jelölje ki **tárolási**.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági beállítások tárolási párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Rendelje hozzá a biztonsági mentési ütemezést megfelelő StorSimple-köteteket.

    > [!NOTE]
    > **Tömörítés** és **titkosítási típus** vannak beállítva, hogy **nincs**.

6.  A **ellenőrizze**, jelölje be a **ne ellenőrizze a feladat adatainak** jelölőnégyzetet. Ez a beállítás hatással lehet a StorSimple rétegezéséhez.

    > [!NOTE]
    > Lemeztöredezettség-mentesítés, indexelő és háttér-ellenőrzési negatívan befolyásoló kódokat a StorSimple rétegezéséhez.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági beállítások beállításainak ellenőrzése](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Ha beállította a biztonsági mentés beállításainak az igényeinek megfelelő részeinek, válassza ki a **OK** befejezéséhez.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Másodlagos biztonsági mentési cél StorSimple beállítása

> [!NOTE]
>Adatok visszaállítása biztonsági mentésből van a felhőhöz már rétegzett felhő sebességek fordulhat elő.

Ebben a modellben (eltérő StorSimple) adathordozóra rendelkeznie kell egy ideiglenes gyorsítótár szolgál. Például egy redundáns tömbje (RAID) független lemezek kötet segítségével terület, a bemeneti/kimeneti (I/O) és a sávszélesség. RAID 5, 50 és 10 használatát javasoljuk.

A következő ábrán látható tipikus rövid távú megőrzés (a kiszolgáló) helyi köteteket, és hosszú távú megőrzési archiválja a köteteket. Ebben a forgatókönyvben minden biztonsági mentés futtatása a helyi (a kiszolgáló) a RAID-köteten. A biztonsági mentése rendszeres időközönként ismétlődő és archivált archívumokat kötetre. Fontos a helyi (a kiszolgáló) a RAID-kötet méretezése, hogy a rövid távú megőrzési kapacitás és teljesítménybeli követelményeit is képes kezelni.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>A másodlagos biztonsági mentési cél GFS példaként StorSimple

![StorSimple, mint a másodlagos biztonsági mentési tároló logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Az alábbi táblázat bemutatja, hogyan állíthatja be biztonsági mentéseket a helyi és a StorSimple lemezek futtatásához. Ez magában foglalja az egyes és a teljes kapacitás követelményeinek.

### <a name="backup-configuration-and-capacity-requirements"></a>Biztonsági mentési konfigurációhoz és kapacitásigények

| Biztonsági mentés típusa és a megőrzési | Konfigurált tároló | Méret (TiB) | GFS szorzója | Teljes kapacitás\* (TiB) |
|---|---|---|---|---|
| (Teljes és növekményes) 1 hét |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| StorSimple hét 2 – 4 |StorSimple lemez (hosszú távú) | 1 | 4 | 4 |
| Havi teljes |StorSimple lemez (hosszú távú) | 1 | 12 | 12 |
| Éves teljes |StorSimple lemez (hosszú távú) | 1 | 1 | 1 |
|GFS kötetek méretkövetelményt |  |  |  | 18*|
\* Teljes kapacitás 17 TiB a StorSimple-lemezek és a helyi RAID kötetre 1 TiB tartalmaz.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS példa ütemezés: GFS Elforgatás heti, havi és éves ütemezése

| Hét | Korlátlan | Növekményes nap 1 | Növekményes napja 2 | Növekményes nap 3 | Növekményes naponta 4 | Növekményes napja 5 |
|---|---|---|---|---|---|---|
| 1 hét | Helyi RAID kötetre  | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre |
| 2 hét | StorSimple hét 2 – 4 |   |   |   |   |   |
| Hét 3 | StorSimple hét 2 – 4 |   |   |   |   |   |
| 4 hét | StorSimple hét 2 – 4 |   |   |   |   |   |
| Havi | StorSimple havonta |   |   |   |   |   |
| Évente | StorSimple évente  |   |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>StorSimple-köteteket rendelhet egy biztonsági mentési Exec archív, és a deduplikációs feladat

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>StorSimple-köteteket rendelhet egy biztonsági mentési Exec archiválási és a párhuzamos feladat

1.  A biztonsági mentés Exec felügyeleti konzolon kattintson a jobb gombbal a feladatot, amely archiválni a StorSimple-kötet, és válassza ki a kívánt **biztonságimásolat-definíció tulajdonságai** > **szerkesztése**.

    ![Biztonsági mentési Exec felügyeleti konzol, biztonsági mentés definíció tulajdonságai lap](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Válassza ki **szakasz felvétele** > **lemezre ismétlődő** > **szerkesztése**.

    ![Biztonsági mentés Exec kezelési konzolján, a szakasz hozzáadása](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Az a **ismétlődő beállítások** párbeszédpanelen jelölje ki a használni kívánt értékeket **forrás** és **ütemezés**.

    ![Felügyeleti konzol, a biztonsági mentési definíciók tulajdonságokat és a duplikált beállításokat Exec biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Az a **tárolási** legördülő listára, válassza ki a StorSimple-kötet, ahová az archiválási feladat a adatainak tárolására.

    ![Felügyeleti konzol, a biztonsági mentési definíciók tulajdonságokat és a duplikált beállításokat Exec biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Válassza ki **ellenőrizze**, majd válassza ki a **ne ellenőrizze a feladat adatainak** jelölőnégyzetet.

    ![Felügyeleti konzol, a biztonsági mentési definíciók tulajdonságokat és a duplikált beállításokat Exec biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Kattintson az **OK** gombra.

    ![Felügyeleti konzol, a biztonsági mentési definíciók tulajdonságokat és a duplikált beállításokat Exec biztonsági mentése](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Az a **biztonsági mentés** oszlop, adja hozzá az új szakasz. A forrás használja **növekményes**. A célként válassza ki a StorSimple-kötet, ahol a növekményes biztonsági mentési feladat archiválva legyen. Ismételje meg az 1 – 6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple felhőalapú pillanatfelvételek

StorSimple felhőalapú pillanatfelvételek a StorSimple eszköz található adatok védelme. A felhő pillanatkép megegyezik egy külső létesítmény helyi másolatot tartalmazó szalagok szállítási. Azure georedundáns tárolás használata, ha egy felhő-pillanatfelvételt létrehozása megegyezik szállítási mentést tartalmazó szalagok több különböző helyére. Ha eszköz visszaállítása után egy olyan vészhelyzet esetén, előfordulhat, hogy egy másik StorSimple eszköz online állapotba, és feladatátvétellel. A feladatátvétel után akkor tudná a legutóbbi felhő pillanatképből (felhő sebességek) adatok eléréséhez.

A következő szakasz ismerteti, hogyan indítsa el, és törölje a StorSimple felhőalapú pillanatfelvételek biztonsági mentés utáni feldolgozás során rövid parancsprogram létrehozásához.

> [!NOTE]
> Létrehozott pillanatfelvételek manuálisan vagy programon keresztül ne hajtsa végre a StorSimple snapshot elévülési szabályzatának. Ezeket a pillanatképeket manuálisan vagy programon keresztül törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Indítsa el, és törölje a felhő pillanatképeket parancsfájl használatával

> [!NOTE]
> Gondosan mérje fel a megfelelőség és az adatok megőrzési következményekkel a StorSimple snapshot törlése előtt. A biztonsági mentés utáni parancsfájl futtatásával kapcsolatos további információkért lásd: a [Backup Exec dokumentáció](https://www.veritas.com/support/en_US/15047.html).

### <a name="backup-lifecycle"></a>Biztonsági mentési életciklusa

![Biztonsági mentési életciklus diagramja](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4.  A parancsfájl hozzáadása a biztonságimásolat-készítő feladat a biztonsági mentési Exec a biztonsági mentési Exec feladat beállítások előzetes feldolgozás szerkesztésével, és utáni parancsok feldolgozásakor.

    ![Biztonsági mentés Exec konzol, a biztonsági mentés, előtti és utáni feldolgozási parancsok lap](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Azt javasoljuk, hogy a StorSimple felhőalapú pillanatfelvétel a biztonsági mentési házirend utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. További információ biztonsági mentése és visszaállítása a biztonságimásolat-készítő alkalmazás környezet segítséget nyújtanak az RPO és RTO, lépjen kapcsolatba a biztonsági mentési felelős mérnök.

## <a name="storsimple-as-a-restore-source"></a>StorSimple visszaállítási forrásként

Visszaállítja a StorSimple eszköz munkahelyi hasonlóan a bármely blokktárolóeszköz visszaállítások. Az adatok, amelyek a felhőbe többszintű visszaállítások felhő sebességek következik be. A helyi adatok visszaállítások elvégzi az eszköz a helyi lemez sebessége. Információ a visszaállításhoz a biztonsági mentés Exec dokumentációjában talál. Azt javasoljuk, hogy megfeleljenek a biztonsági mentés Exec visszaállítási ajánlott eljárások.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvételi és katasztrófa-helyreállítás

> [!NOTE]
> Biztonsági mentési cél forgatókönyvek esetén a StorSimple felhő készülék visszaállítási cél nem támogatott.

Egy olyan vészhelyzet esetén számos tényező okozhatja. Az alábbi táblázat általános vész-helyreállítási eljárással.

| Forgatókönyv | Hatás | Helyreállítása | Megjegyzések |
|---|---|---|---|
| A StorSimple eszköz hibája | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a hibás eszközt, és végezze el [StorSimple feladatátvétel és vész-helyreállítási](storsimple-device-failover-disaster-recovery.md). | Ha egy eszköz a helyreállítás után visszaállítást végezhet van szüksége, teljes adatkészletek működő lekért a felhőben az új eszköz. Minden olyan felhőalapú sebességgel. Az indexelő és kategorizálás rescanning folyamat minden biztonsági mentés vizsgálata és a helyi eszközön réteghez, amely időigényes folyamat lehet, hogy a felhő szintjén lekért okozhatja. |
| Exec server sikertelen biztonsági mentéshez | Biztonsági mentési és visszaállítási műveletek megszakadnak. | A biztonsági mentési kiszolgáló újraépítése, és végezze el az adatbázis visszaállítása a [manuális biztonsági mentés és visszaállítás a biztonsági mentési Exec (BEDB) adatbázis módjáról](http://www.veritas.com/docs/000041083). | Építse újra kell, vagy állítsa vissza a biztonsági mentés Exec kiszolgáló, a vész-helyreállítási helyen. Állítsa vissza az adatbázist a legutóbbi pontnak. A visszaállított biztonsági mentési Exec adatbázis nincs szinkronban vannak a legújabb biztonsági mentési feladatok, ha az indexelést és katalogizálni szükség. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a vizsgált és a felhő szintjén a helyi eszközön réteghez lekért összes biztonsági mentés. Így további időigényes. |
| A helykiszolgáló biztonsági mentése és a StorSimple elvesztését eredményezi helyen sikertelen | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, és helyreállíthatja a biztonsági mentés Exec. | Először állítsa vissza a StorSimple, és helyreállíthatja a biztonsági mentés Exec. Ha egy eszköz a helyreállítás után visszaállítást végezhet van szüksége, a teljes adatkészletet működő lekért a felhőben az új eszköz. Minden olyan felhőalapú sebességgel. |

## <a name="references"></a>Referencia

Ez a cikk a hivatkozott a következő dokumentumokat:

- [StorSimple-többutas i/o-telepítő](storsimple-configure-mpio-windows-server.md)
- [Tárolási forgatókönyvek: a dinamikus kiosztás](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Meghajtók GPT használata](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Állítson be megosztott mappák árnyékmásolatai](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- További tudnivalók a [biztonságimásolat-készletből való visszaállítása](storsimple-restore-from-backup-set-u2.md).
- További áttelepítésről [eszköz feladatátvételi és katasztrófa-helyreállítás](storsimple-device-failover-disaster-recovery.md).
