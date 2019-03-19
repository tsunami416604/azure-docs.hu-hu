---
title: A StorSimple 8000 sorozat veeammel biztonsági mentési célként |} A Microsoft Docs
description: A StorSimple biztonsági mentési cél konfiguráció veeammel ismerteti.
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
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: e7659cca9081834d41f64ef0fbd8ea3686044bfd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012011"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>Biztonsági mentési célként StorSimple veeammel

## <a name="overview"></a>Áttekintés

Az Azure StorSimple hibrid felhőalapú tárolási megoldást a Microsoft a. A StorSimple használatával egy Azure Storage-fiók kiterjesztése a helyszíni megoldás, és automatikusan rétegezési adatokat, mint a helyszíni tárolók és a felhőalapú tárolás az exponenciális Adatnövekedés kiküszöböli címek.

Ebben a cikkben bemutatjuk a StorSimple-integráció a Veeam, és a két megoldás integrálására vonatkozó ajánlott eljárásokat. Azt is ajánlásokat a legjobb integrálása a StorSimple Veeam beállítása. Hogy késlelteti a Veeam ajánlott eljárásokat, a biztonsági mentési tervezőket és a rendszergazdák a Veeam beállítása az egyes biztonsági mentési követelményeknek és a szolgáltatásiszint-szerződések (SLA) a legjobb módszer.

Azt illusztrálja, konfigurációs lépésekkel és alapfogalmait, bár ez a cikk semmiképpen sem részletes konfigurációs vagy telepítési útmutató. Feltételezzük, hogy az alapszintű összetevőkre és infrastruktúrákra működő sorrendben, és készen áll a fogalmakat, amelyek ismertetünk támogatja.

### <a name="who-should-read-this"></a>Ez célközönsége?

Ebben a cikkben található információk a biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolási architects ismerő, storage, a Windows Server 2012 R2, a Ethernet, a cloud services és a Veeam hasznos lesz.

### <a name="supported-versions"></a>Támogatott verziók

-   Veeam 9 és újabb verziók
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
![StorSimple rétegezési diagramja](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Tárkapacitás | 8100 | 8600 |
|---|---|---|
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

![A StorSimple mint egy elsődleges biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

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

![A StorSimple mint egy másodlagos biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

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
3. Helyezze üzembe a Veeam.

Az egyes lépések a következő szakaszokban részletesen tárgyalja.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple olyan megoldás, amely integrálva van az Azure-felhőben, a StorSimple szükséges egy aktív és az Azure-felhőben működő kapcsolatot. Ehhez a kapcsolathoz a műveletek, mint például a felhőbeli pillanatképekkel, a felügyeletéhez és a metaadatok adatforgalom szolgál, és a régebbi szintre, kisebb használt adatokat pedig az Azure felhőalapú tárolást.

A megoldás optimális működéséhez javasoljuk, hogy kövesse az ajánlott eljárások követésével hálózati:

-   A hivatkozás, amely a StorSimple rétegezés szolgáltatással az Azure-meg kell felelnie a sávszélesség-követelmények. Ennek érdekében a szolgáltatásminőség (QoS) szükséges szintű alkalmaz az infrastruktúra vált felel meg a helyreállítási Időkorlát és a helyreállítási idő célkitűzése (RTO) SLA-k.
-   Maximális Azure Blob storage hozzáférési késések körülbelül 80 ms kell lennie.

### <a name="deploy-storsimple"></a>A StorSimple üzembe helyezése

A StorSimple üzembe helyezési útmutatót lépésről lépésre, lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Veeam üzembe helyezése

Veeam gyakorlati tanácsok a telepítéshez, lásd: [Veeam biztonsági mentés és replikáció ajánlott eljárások](https://bp.veeam.expert/), és olvassa el a felhasználói útmutatójának következő [Veeam súgóközpont (műszaki dokumentáció)](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Ne használjon átnyúló kötetek (a Windows Lemezkezelés által létrehozott). Átnyúló kötetek nem támogatottak.
- Formázza a köteteket, az NTFS Fájlrendszerrel rendelkező 64 KB-os foglalási egység méretét.
- Közvetlenül a Veeam kiszolgáló képezze le a StorSimple-köteteket.
    - ISCSI használata a fizikai kiszolgálók számára.


## <a name="best-practices-for-storsimple-and-veeam"></a>A StorSimple és a Veeam ajánlott eljárásai

Állítsa be a megoldás az Guidelines irányelveinek megfelelően a következő néhány szakaszban.

### <a name="operating-system-best-practices"></a>Operációs rendszer ajánlott eljárások

- Tiltsa le a Windows Server titkosítás és az adatdeduplikáció az NTFS fájlrendszerhez.
- Tiltsa le a StorSimple-köteteket a Windows Server töredezettségmentesítése.
- A StorSimple-köteteket a Windows Server-indexelés letiltása.
- (Nem a StorSimple-kötetek) szemben a forrás gazdagép víruskeresést futtatni.
- Kapcsolja ki az alapértelmezett [Windows Server karbantartási](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) a Feladatkezelő. Ehhez a következő módszerek valamelyikével:
  - Kapcsolja ki a karbantartási konfiguráló a Windows Feladatütemező.
  - Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) a Windows Sysinternals. PsExec a letöltés után futtassa a Windows PowerShell, rendszergazdaként, majd írja be:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>A StorSimple-ajánlott eljárások

-   Győződjön meg, hogy a StorSimple-eszköz frissül, és [Update 3 vagy újabb](storsimple-install-update-3.md).
-   Elkülönítheti az iSCSI és a felhőalapú forgalom. Dedikált iSCSI-kapcsolatok használata a StorSimple és a biztonsági mentési kiszolgálóra közötti adatforgalmat.
-   Győződjön meg arról, hogy a StorSimple-eszköz-e a biztonsági mentési cél dedikált. Vegyes számítási feladatok nem támogatottak, mert ezek hatással az RTO és RPO.

### <a name="veeam-best-practices"></a>Veeam ajánlott eljárások

-   A Veeam adatbázis kell elhelyezkednie, a kiszolgáló és a StorSimple-kötet nem található.
-   Vész-helyreállítási adatbázis biztonsági mentése a Veeam a StorSimple-kötet.
-   Ebben a megoldásban támogatjuk a Veeam teljes és növekményes biztonsági mentéseket. Azt javasoljuk, hogy nem használja szintetikus és különbségi biztonsági mentés.
-   Biztonsági mentési adatok fájljaihoz tartalmaznia kell egy adott feladat csak az adatokat. Nincs adathordozó például fűzi hozzá több különböző feladatok használata engedélyezett.
-   Kapcsolja ki az ellenőrzési feladat. Ha szükséges, ellenőrzése után a legújabb biztonsági mentési feladat lehet ütemezni. Fontos megérteni, hogy ez a feladat a biztonsági mentés időszakának érinti-e.
-   Kapcsolja be a media előzetes lefoglalás.
-   Győződjön meg arról, hogy párhuzamos feldolgozási be van kapcsolva.
-   Kikapcsolja a tömörítést.
-   Kapcsolja ki a deduplikáció a biztonsági mentési feladat.
-   Optimalizálás beállítása **LAN cél**.
-   Kapcsolja be a **létrehozás aktív teljes biztonsági mentés** (minden második héten).
-   A biztonsági mentési tárházban beállítása **VM-enkénti biztonságimásolat-fájlok használata**.
-   Állítsa be **feladatonként több feltöltési adatfolyam használata** való **8** (engedélyezett egy legfeljebb 16). Állítsa be ezt az értéket felfelé vagy lefelé a StorSimple-eszközön a processzorhasználat alapján.

## <a name="retention-policies"></a>Adatmegőrzési házirendek

A leggyakrabban használt biztonsági másolatok megőrzési házirend-típusainak egyikét egy, a Nagyapa Father és Lányomtól (GFS) szabályzat. GFS házirendben a növekményes biztonsági mentés naponta történik, és teljes biztonsági mentés heti és havi végzett. A csoportházirend-eredményhez hat StorSimple-ben rétegzett köteteket: egy kötetet tartalmaz a heti, havi és éves teljes biztonsági másolatokat; a többi öt kötetek napi növekményes biztonsági másolatok tárolására.

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

## <a name="set-up-veeam-storage"></a>Veeam tárolás beállítása

### <a name="to-set-up-veeam-storage"></a>A Veeam tárolás beállítása

1.  A Veeam biztonsági mentésének és replikációjának-konzolon a **tárház eszközök**, lépjen a **biztonsági mentési infrastruktúra**. Kattintson a jobb gombbal **Backup Tárházak**, majd válassza ki **Backup-adattár hozzáadása**.

    ![Veeam felügyeleti konzolt, biztonsági mentési adattár oldalát](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Az a **új Backup-tárház** párbeszédpanelen adja meg egy nevet és leírást a tárházban. Kattintson a **Tovább** gombra.

    ![Veeam felügyeleti konzolt, neve és leírása lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Válassza ki a típus **Microsoft Windows server**. Válassza ki a Veeam kiszolgálót. Kattintson a **Tovább** gombra.

    ![Veeam felügyeleti konzolt, biztonsági mentési tárház select típusa](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Adja meg a **hely**, keresse meg és válassza ki a kötetet. Válassza ki a **korlátozza az egyidejű feladatok maximális:** négyzet jelölését, majd állítsa az értékét **4**. Ez biztosítja, hogy csak négy virtuális lemezek feldolgozott egyidejű, amíg minden virtuális gép (VM) feldolgozása. Válassza ki a **speciális** gombra.

    ![Veeam felügyeleti konzolon válassza a kötet](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Az a **tárolási kompatibilitási beállítások** párbeszédpanelen válassza ki a **VM-enkénti biztonságimásolat-fájlok használata** jelölőnégyzetet.

    ![Veeam felügyeleti konzolt, tárolási kompatibilitási beállítások](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Az a **új Backup-tárház** párbeszédpanelen válassza ki a **vPower NFS szolgáltatást a (javasolt) csatlakoztatási kiszolgálón engedélyezze** jelölőnégyzetet. Kattintson a **Tovább** gombra.

    ![Veeam felügyeleti konzolt, biztonsági mentési adattár oldalát](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Tekintse át a beállításokat, és válassza ki **tovább**.

    ![Veeam felügyeleti konzolt, biztonsági mentési adattár oldalát](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Egy adattár a Veeam kiszolgálóra kerül.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Állítsa be a StorSimple elsődleges biztonsági mentési cél

> [!IMPORTANT]
> Adatok visszaállítása biztonsági tartozik a felhőben lett rétegzett felhőbeli sebességgel következik be.

Az alábbi ábrán egy tipikus kötetet a biztonsági mentési feladat hozzárendelését. Ebben az esetben a heti biztonsági mentések leképezése a szombat teljes lemezt, és a növekményes biztonsági mentések leképezése hétfőtől péntekig növekményes lemezeket. A biztonsági mentéseket és helyreállításokat vannak a storsimple-kötet összes rétegzett kötet.

![Elsődleges biztonsági mentési célként megadott konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>A StorSimple mint elsődleges biztonsági mentési cél GFS példa ütemezése

Íme egy példa négy hét, havi és évi GFS Elforgatás ütemezését:

| Gyakorisága, illetve biztonsági mentési típusa | Korlátlan | Növekményes (1-5 nap)  |   
|---|---|---|
| Heti (1 – 4 hét) | Szombat | Hétfőtől péntekig |
| Havonta  | Szombat  |   |
| Évente | Szombat  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple-kötetek hozzárendelése a Veeam biztonsági mentési feladat

Elsődleges biztonsági mentési cél forgatókönyvhöz hozzon létre egy napi feladat az elsődleges Veeam StorSimple-kötet. A forgatókönyvhöz egy másodlagos biztonsági mentési cél hozzon létre egy napi feladat közvetlenül csatlakoztatott tároló (DAS), a hálózathoz csatlakoztatott tárolás (NAS) vagy közvetlenül a lemezek kötegnek (JBOD) storage.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple-kötetek hozzárendelése a Veeam biztonsági mentési feladat

1.  Válassza ki a Veeam biztonsági mentésének és replikációjának konzolon **biztonsági mentés és replikáció**. Kattintson a jobb gombbal **Backup**, majd válassza ki **VMware** vagy **Hyper-V**, attól függően, a környezetben.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Az a **új biztonsági mentési feladat** párbeszédpanelen adja meg egy nevet és leírást a napi biztonsági mentési feladat.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Válassza ki a virtuális gép biztonsági másolat.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Válassza ki a használni kívánt értékeket **biztonsági mentési proxy** és **Backup tárház**. Adja meg a **visszaállítási pontok megőrzése lemezen való** RPO és RTO definíciókat a helyileg csatlakoztatott tárolón környezetének megfelelően. Válassza az **Advanced** (Speciális) lehetőséget.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Az a **speciális beállítások** párbeszédpanel a **Backup** lapon jelölje be **növekményes**. Ügyeljen arra, hogy a **hozzon létre rendszeres időközönként szintetikus teljes biztonsági mentések** jelölőnégyzet nincs bejelölve. Válassza ki a **aktív teljes biztonsági mentés létrehozása rendszeres időközönként** jelölőnégyzetet. A **aktív teljes biztonsági mentés**, jelölje be a **hetente kijelölt napjain** szombat jelölőnégyzetét.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Az a **tárolási** lapra, győződjön meg arról, hogy a **beágyazott adatok deduplikációjának engedélyezése** jelölőnégyzet nincs bejelölve. Válassza ki a **kizárási lapozófájl-kapacitás fájlblokkokat** jelölőnégyzetet, majd válassza ki a **kizárási törölt fájlblokkokat** jelölőnégyzetet. Állítsa be **tömörítési szint** való **None**. Elosztott terhelésű teljesítmény és a deduplikáció, állítsa be **tárolóoptimalizálás** való **LAN cél**. Kattintson az **OK** gombra.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Veeam deduplikáció és a tömörítés beállításaival kapcsolatos további információkért lásd: [az adattömörítés és a Deduplikáció](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Az a **biztonsági mentési feladat szerkesztése** párbeszédpanelen kiválaszthatja a **alkalmazásbarát feldolgozásának engedélyezése** (nem kötelező) jelölőnégyzetet.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat Vendég feldolgozási lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Állítsa az ütemezést is megadhat egyszerre napi egyszeri futtatásra.

    ![Veeam felügyeleti konzolt, új biztonsági mentési feladat ütemezési oldalán](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési cél

> [!NOTE]
> Adatok visszaállítása a felhőbe tartozik lett rétegzett biztonsági felhőbeli sebességgel fordulhat elő.

Ebben a modellben egy átmeneti gyorsítótárban egyikükön rendelkeznie kell egy adathordozó (nem a StorSimple). Használhatja például a kötet (RAID) független lemezek redundáns tömbjének terület, a bemeneti/kimeneti (I/O) és a sávszélesség. A RAID 10, 5 és 50 használatát javasoljuk.

A következő ábrán látható, tipikus rövid távú megőrzési helyi (a kiszolgáló) és a hosszú távú megőrzési archív kötetek. Ebben a forgatókönyvben minden biztonsági mentés futtatása a helyi (a kiszolgáló) RAID-köteten. Ezeket a biztonsági másolatokat időszakosan ismétlődő és az archív kötetek archiválva. Fontos a helyi (a kiszolgáló) RAID kötetre méretezéséhez, így képes kezelni a rövid távú adatmegőrzési kapacitást és teljesítményre vonatkozó követelményeknek.

![A StorSimple mint másodlagos biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>A StorSimple mint egy másodlagos biztonsági mentési cél GFS példa

Az alábbi táblázat bemutatja, hogyan futtathatók a helyi és a StorSimple-lemezek biztonsági mentések beállításához. Egyéni és teljes kapacitásigények tartalmazza.

| Biztonsági mentés típusát és megőrzés | Konfigurált tároló | Méret (TiB) | GFS szorzó | Teljes kapacitás\* (TiB) |
|---|---|---|---|---|
| (Teljes és növekményes) 1 hét |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| A StorSimple hét 2 – 4 |A StorSimple-lemez (hosszú távú) | 1 | 4 | 4 |
| Teljes havi |A StorSimple-lemez (hosszú távú) | 1 | 12 | 12 |
| Éves teljes |A StorSimple-lemez (hosszú távú) | 1 | 1 | 1 |
|GFS kötetek méretkövetelményt |  |  |  | 18*|

\* Teljes kapacitás 17 Tib-ra a StorSimple-lemezek és a helyi RAID kötetre 1 TiB tartalmaz.


### <a name="gfs-example-schedule"></a>Példa ütemezési GFS

Elforgatás GFS heti, havi és éves ütemterv

| Hét | Korlátlan | 1. nap növekményes | 2. napon növekményes | Növekményes naponta 3 | Növekményes naponta 4 | Növekményes nap 5 |
|---|---|---|---|---|---|---|
| 1 hét | Helyi RAID kötetre  | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre | Helyi RAID kötetre |
| 2 hét | A StorSimple hét 2 – 4 |   |   |   |   |   |
| 3 hét | A StorSimple hét 2 – 4 |   |   |   |   |   |
| 4 hét | A StorSimple hét 2 – 4 |   |   |   |   |   |
| Havonta | A StorSimple havi |   |   |   |   |   |
| Évente | StorSimple évente  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple-kötetek hozzárendelése a Veeam másolási feladat

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple-kötetek hozzárendelése a Veeam másolási feladat

1.  Válassza ki a Veeam biztonsági mentésének és replikációjának konzolon **biztonsági mentés és replikáció**. Kattintson a jobb gombbal **Backup**, majd válassza ki **VMware** vagy **Hyper-V**, attól függően, a környezetben.

    ![Veeam felügyeleti konzolt, új biztonsági másolat feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Az a **új biztonsági mentési másolási feladat** párbeszédpanelen adja meg egy nevet és leírást a feladathoz.

    ![Veeam felügyeleti konzolt, új biztonsági másolat feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Válassza ki a feldolgozni kívánt virtuális gépeket. Válassza ki a biztonsági mentésekből, és válassza ki a korábban létrehozott napi biztonsági mentés.

    ![Veeam felügyeleti konzolt, új biztonsági másolat feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Objektumok kizárása a biztonsági másolat feladatot, ha szükséges.

5.  Válassza ki a biztonsági mentési tárházzal, és beállítható egy érték **visszaállítási pontok tartani**. Ügyeljen arra, hogy válassza ki a **tartsa a következő visszaállítási archiválási célokból** jelölőnégyzetet. Adja meg a biztonsági mentés gyakoriságát, és válassza ki **speciális**.

    ![Veeam felügyeleti konzolt, új biztonsági másolat feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Adja meg a következő speciális beállításokat:

    * Az a **karbantartási** fülre, kapcsolja ki a tárolási szint sérülés guard.

    ![Veeam felügyeleti konzolt, új biztonsági másolat feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Az a **tárolási** lapra, győződjön meg, hogy a deduplikáció és a tömörítést be vannak kapcsolva.

    ![Veeam felügyeleti konzolt, új biztonsági másolat feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Adja meg, hogy az adatátvitel közvetlen.

8.  Adja meg a biztonsági másolat időszak ütemezése igény szerint, és ezután fejezze be a varázslót.

További információkért lásd: [biztonsági másolat feladatok létrehozása](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>A StorSimple a felhőbeli pillanatképek

A StorSimple a felhőbeli pillanatképek a StorSimple-eszköz lévő adatok védelmét. Felhőbeli pillanatkép létrehozásának megegyezik egy külső létesítmény helyi másolatot tartalmazó szalagok szállítási. Ha az Azure georedundáns tárolást használ, felhőbeli pillanatkép létrehozásának megegyezik szállítási szalagot több helyhez. Ha egy eszköz visszaállítása egy vészhelyzetet követően van szüksége, előfordulhat, hogy csatlakoztassa egy másik StorSimple eszközt, és hajtsa végre a feladatátvételt. A feladatátvétel után meg tudják elérheti az adatokat (felhőbeli sebességgel), a legutóbbi felhőbeli pillanatkép.

Az alábbi szakasz elindításához és a StorSimple a felhőbeli pillanatképek törlése során a biztonsági mentés utáni feldolgozási rövid parancsfájl létrehozása.

> [!NOTE]
> A pillanatképek manuálisan vagy programon keresztül létrehozott ne hajtsa végre a StorSimple snapshot elévülési szabályzatot. Ezek a pillanatképek manuálisan vagy programon keresztül törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Indítsa el, és a felhőbeli pillanatképek törlése egy parancsfájl használatával

> [!NOTE]
> Gondosan mérje fel a megfelelőség és az adatok megőrzési következményekkel StorSimple pillanatkép törlése előtt. A biztonsági mentés utáni parancsfájl futtatásával kapcsolatos további információkért a Veeam dokumentációjában talál.


### <a name="backup-lifecycle"></a>Biztonsági mentési életciklusa

![Biztonsági mentési életciklus-diagram](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Adja hozzá a parancsfájlt a biztonsági mentési feladat, szerkessze a Speciális beállítások Veeam feladat.

    ![Veeam speciális beállítások biztonsági mentési szkriptek lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Azt javasoljuk, hogy a StorSimple felhőalapú pillanatkép biztonsági mentési szabályzat utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. Biztonsági mentése és visszaállítása a biztonsági mentést végző alkalmazás környezet segítséget nyújtanak az RPO és RTO kapcsolatos további információkért tekintse meg a biztonsági mentési mérnök együtt.

## <a name="storsimple-as-a-restore-source"></a>A StorSimple-helyreállítás forrásaként

Visszaállítja a StorSimple eszköz munkahelyi például visszaállítás bármely block storage eszközről. Visszaállítás a felhőbe a rétegzett adatok felhőbeli sebességgel következik be. A helyi adatok esetében a visszaállítás fordulhat elő, az eszköz helyi lemez sebességével.

Veeam a következőket kínálja gyors, részletes, fájlszintű helyreállítási keresztül a StorSimple a beépített explorer nézeteket a Veeam konzolon keresztül. A Veeam tallózók használatával helyreállítani az egyes elemeket, például e-mailek, az Active Directory-objektumok és a SharePoint-elemek biztonsági másolatokból. A helyreállítás a helyszíni virtuális gép megszakítása nélkül elvégezhető. Az Azure SQL Database és Oracle-adatbázisok időponthoz helyreállítás is teheti. Veeam és a StorSimple megkönnyítik az elemszintű helyreállítás gyors és egyszerű Azure-ból. Hajtson végre visszaállítást kapcsolatos információkért lásd: a Veeam dokumentáció:

- A [Exchange-kiszolgáló](https://www.veeam.com/microsoft-exchange-recovery.html)
- A [az Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- A [az SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- A [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- A [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>A StorSimple feladatátvétel és a katasztrófa utáni helyreállítás

> [!NOTE]
> A biztonsági mentési cél esetben a StorSimple Cloud Appliance nem támogatott visszaállítási célként.

Katasztrófa számos tényező okozhatja. Az alábbi táblázat általános vész-helyreállítási helyzetekben.

| Forgatókönyv | Hatás | Helyreállítása | Megjegyzések |
|---|---|---|---|
| A StorSimple eszköz hiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a sikertelen eszközt, és hajtsa végre [StorSimple feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-device-failover-disaster-recovery.md). | Ha eszköz helyreállítása után a helyreállítás végrehajtásához szüksége, működő teljes adatkészletek lekért a felhő az új eszközhöz. Az összes műveletekre felhőbeli sebességgel. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a beolvasott és a helyi eszközön szintre, ami időigényes eljárás lehet, hogy a felhő szintjén lekért összes biztonságimásolat-készleteket. |
| Veeam kiszolgálóhiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Építse újra a biztonsági mentési kiszolgálóra, és végezze el az adatbázis-visszaállítás leírt módon [Veeam súgóközpont (műszaki dokumentáció)](https://www.veeam.com/documentation-guides-datasheets.html).  | Építse újra, vagy állítsa vissza a vész-helyreállítási webhelyként, a Veeam kiszolgálót kell. Állítsa vissza az adatbázist a legutóbbi pontnak. Ha a Veeam visszaállított adatbázis nem a legújabb biztonsági mentési feladatok szinkronban, indexelési és katalogizálása megadása kötelező. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a beolvasott és a felhő szintjén a helyi eszközön szintre lekért összes biztonságimásolat-készleteket. Így további időigényes. |
| Hely hiba, amely a biztonsági mentési kiszolgálóra és a StorSimple elvesztését eredményezi | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, és helyreállíthatja a Veeam. | Először állítsa vissza a StorSimple, és helyreállíthatja a Veeam. Hajtson végre visszaállítást eszköz helyreállítása után van szüksége, ha a teljes működő adatkészletek a vannak a felhőből az új eszköz beolvasni. Az összes műveletekre felhőbeli sebességgel. |


## <a name="references"></a>Referencia

Ez a cikk a hivatkozott az alábbi dokumentumokat:

- [A StorSimple-a többutas i/o-telepítő](storsimple-configure-mpio-windows-server.md)
- [Storage-forgatókönyv esetében: A dinamikus kiosztás](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Meghajtók GPT használata](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Állítsa be a megosztott mappák árnyékmásolatai](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [biztonságimásolat-készlet visszaállítási](storsimple-restore-from-backup-set-u2.md).
- További információk a végrehajtása [eszközön feladatátvétel és vészhelyreállítás helyreállítási](storsimple-device-failover-disaster-recovery.md).
