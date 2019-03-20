---
title: A StorSimple 8000 sorozat Netbackuppal a biztonsági mentési célként |} A Microsoft Docs
description: A StorSimple biztonsági mentési cél konfiguráció Veritas netbackuppal ismerteti.
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
ms.date: 06/15/2017
ms.author: hkanna
ms.openlocfilehash: 17428405a0be45854a2eaaef831864f529ed145a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994480"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple biztonsági mentési célként Netbackuppal

## <a name="overview"></a>Áttekintés

Az Azure StorSimple hibrid felhőalapú tárolási megoldást a Microsoft a. A StorSimple-címek kiküszöböli az exponenciális Adatnövekedés kiterjesztése a helyszíni megoldás, mint az Azure storage-fiók használatával, és automatikusan rétegezést adatok között a helyszíni és felhőbeli tárolására.

Ebben a cikkben bemutatjuk a StorSimple-integráció, a Veritas Netbackuppal, és a két megoldás integrálására vonatkozó ajánlott eljárásokat. Azt is ajánlásokat a legjobb integrálása a StorSimple Veritas Netbackuppal beállítása. Hogy késlelteti a Veritas ajánlott eljárásokat, biztonsági mentési tervezők és rendszergazdái a legjobb módszer Veritas Netbackuppal beállítása az egyes biztonsági mentési követelményeknek és a szolgáltatásiszint-szerződések (SLA).

Azt illusztrálja, konfigurációs lépésekkel és alapfogalmait, bár ez a cikk semmiképpen sem részletes konfigurációs vagy telepítési útmutató. Feltételezzük, hogy az alapszintű összetevőkre és infrastruktúrákra működő sorrendben, és készen áll a fogalmakat, amelyek ismertetünk támogatja.

### <a name="who-should-read-this"></a>Ez célközönsége?

Ebben a cikkben található információk a biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolási architects, akik ismerik a storage, a Windows Server 2012 R2, a Ethernet, a cloud services és a Veritas Netbackuppal hasznos lesz.

### <a name="supported-versions"></a>Támogatott verziók

-   Netbackuppal 7.7.x és újabb verziók
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
![StorSimple rétegezési diagramja](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

![A StorSimple mint egy elsődleges biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>A cél elsődleges biztonsági mentési logikai lépésekre

1.  A biztonsági mentési kiszolgálóra kapcsolatba lép a biztonsági mentési célügynököt a számítógépre, és a biztonsági mentési ügynök továbbítja az adatokat a biztonsági mentési kiszolgálóra.
2.  A biztonsági mentési kiszolgálóra írja az adatokat a StorSimple a rétegzett köteteket.
3.  A biztonsági mentési kiszolgálóra frissíti a katalógus-adatbázisban, és ezután befejezi a biztonsági mentési feladat.
4.  Egy pillanatkép szkript elindítja a StorSimple snapshot Managert (kezdő vagy törlés).
5.  A biztonsági mentési kiszolgálóra törli a lejárt biztonsági mentés a megőrzési házirend alapján.

### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállítási logikai lépésekre

1.  A biztonsági mentési kiszolgálóra indítja el a megfelelő adatok visszaállítása a storage-adattárból.
2.  A backup-ügynök az adatokat fogad a biztonsági mentési kiszolgálóra.
3.  A biztonsági mentési kiszolgálóra a visszaállítási feladat befejeződik.

## <a name="storsimple-as-a-secondary-backup-target"></a>A StorSimple mint másodlagos biztonsági mentési cél

Ebben a forgatókönyvben StorSimple-kötetek vannak elsősorban a hosszú távú megőrzés és archiválása.

Az alábbi ábra az architektúra látható melyik kezdeti biztonsági mentés, és visszaállítja a célkötet egy nagy teljesítményű. Ezeket a biztonsági másolatokat másolják és a egy StorSimple archivált rétegzett kötet egy beállított ütemezés szerint.

Fontos a nagy teljesítményű kötet méretezéséhez, így képes kezelni a megőrzési házirend kapacitás és teljesítmény követelményeinek.

![A StorSimple mint egy másodlagos biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Másodlagos cél biztonsági mentési logikai lépésekre

1.  A biztonsági mentési kiszolgálóra kapcsolatba lép a biztonsági mentési célügynököt a számítógépre, és a biztonsági mentési ügynök továbbítja az adatokat a biztonsági mentési kiszolgálóra.
2.  A biztonsági mentési kiszolgálóra írja az adatokat a nagy teljesítményű tárolást.
3.  A biztonsági mentési kiszolgálóra frissíti a katalógus-adatbázisban, és ezután befejezi a biztonsági mentési feladat.
4.  A biztonsági mentési kiszolgálóra másol adatmegőrzési alapuló StorSimple biztonsági mentéseket.
5.  Egy pillanatkép szkript elindítja a StorSimple snapshot Managert (kezdő vagy törlés).
6.  A biztonsági mentési kiszolgálóra törli a lejárt biztonsági mentések adatmegőrzési házirend alapján.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél visszaállítási logikai lépésekre

1.  A biztonsági mentési kiszolgálóra indítja el a megfelelő adatok visszaállítása a storage-adattárból.
2.  A backup-ügynök az adatokat fogad a biztonsági mentési kiszolgálóra.
3.  A biztonsági mentési kiszolgálóra a visszaállítási feladat befejeződik.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Ez a megoldás üzembe helyezése három lépésből áll:
1. Készítse elő a hálózati infrastruktúrát.
2. A StorSimple eszköz üzembe helyezése egy biztonsági mentési célként.
3. Helyezze üzembe a Veritas Netbackuppal.

Az egyes lépések a következő szakaszokban részletesen tárgyalja.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple olyan megoldás, amely integrálva van az Azure-felhőben, a StorSimple szükséges egy aktív és az Azure-felhőben működő kapcsolatot. Ehhez a kapcsolathoz a műveletek, mint például a felhőbeli pillanatképekkel, a felügyeletéhez és a metaadatok adatforgalom szolgál, és a régebbi szintre, kisebb használt adatokat pedig az Azure felhőalapú tárolást.

A megoldás optimális működéséhez javasoljuk, hogy kövesse az ajánlott eljárások követésével hálózati:

-   A hivatkozás, amely a StorSimple rétegezés szolgáltatással az Azure-meg kell felelnie a sávszélesség-követelmények. Ennek érdekében érvényesek a megfelelő szintű szolgáltatásminőség (QoS) az adott infrastruktúrához vált felel meg a helyreállítási Időkorlát és a helyreállítási idő célkitűzése (RTO) SLA-k.

-   Maximális Azure Blob storage hozzáférési késések körülbelül 80 ms kell lennie.

### <a name="deploy-storsimple"></a>A StorSimple üzembe helyezése

A StorSimple üzembe helyezési útmutatót lépésről lépésre, lásd: [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Netbackuppal üzembe helyezése

Részletes Netbackuppal 7.7.x üzembe helyezési útmutatót, tekintse meg a [Netbackuppal 7.7.x dokumentáció](http://www.veritas.com/docs/000094423).

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

- Ne használjon (Windows Lemezkezelés által létrehozott); átnyúló kötetek átnyúló kötetek nem támogatottak.
- Formázza a köteteket, az NTFS Fájlrendszerrel 64 KB-os mérettel.
- Közvetlenül a Netbackuppal kiszolgáló képezze le a StorSimple-köteteket.
    - ISCSI használata a fizikai kiszolgálók számára.
    - Csatlakoztatott lemez használata a virtuális kiszolgálók.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Ajánlott eljárások a StorSimple és Netbackuppal

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

### <a name="netbackup-best-practices"></a>Ajánlott eljárások Netbackuppal

-   A Netbackuppal adatbázis kell elhelyezkednie, a kiszolgáló és a StorSimple-kötet nem található.
-   Vész-helyreállítási adatbázis biztonsági mentése a Netbackuppal a StorSimple-kötet.
-   Ebben a megoldásban támogatott Netbackuppal teljes és növekményes biztonsági mentések (más néven a Netbackuppal különbözeti növekményes biztonsági mentések). Azt javasoljuk, hogy nem használja szintetikus és az összesített növekményes biztonsági mentést.
-   Biztonsági mentési adatok fájljaihoz tartalmaznia kell egy adott feladat csak az adatokat. Nincs adathordozó például fűzi hozzá több különböző feladatok használata engedélyezett.

A legújabb Netbackuppal beállításokat és a követelmények megvalósításához ajánlott eljárások lásd a Netbackuppal dokumentációját a [www.veritas.com](https://www.veritas.com).


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

## <a name="set-up-netbackup-storage"></a>Netbackuppal tárolás beállítása

### <a name="to-set-up-netbackup-storage"></a>Netbackuppal tároló beállítása

1.  A Netbackuppal felügyeleti konzolban válassza **média- és eszközkezelés** > **eszközök** > **lemezkészleten**. A lemez tárolókészlet konfigurációs varázslóban válassza ki a storage server **AdvancedDisk**, majd válassza ki **tovább**.

    ![Netbackuppal felügyeleti konzolon, lemez-tárolókészlet konfigurációs varázsló](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Válassza ki a kiszolgálót, és válassza **tovább**.

    ![Netbackuppal felügyeleti konzolon válassza ki a kiszolgálót](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Válassza ki a StorSimple-kötet.

    ![Netbackuppal felügyeleti konzolon, a StorSimple-kötet lemez kiválasztása](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Adjon meg egy nevet a biztonsági mentés célját, és válassza **tovább** > **tovább** a varázsló befejezéséhez.

5.  Tekintse át a beállításokat, és válassza ki **Befejezés**.

6.  Minden kötet hozzárendelés végén a javasolt megfelelő tárolási eszköz beállításainak módosítása [ajánlott eljárások a StorSimple és Netbackuppal](#best-practices-for-storsimple-and-netbackup).

7. 1 – 6. lépést ismételje végzett hozzárendelése a StorSimple-köteteket.

    ![Netbackuppal felügyeleti konzolon, a lemez konfigurációja](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Állítsa be a StorSimple elsődleges biztonsági mentési cél

> [!NOTE]
> Adatok visszaállítása a felhőbe tartozik lett rétegzett biztonsági felhőbeli sebességgel fordulhat elő.

Az alábbi ábrán egy tipikus kötetet a biztonsági mentési feladat hozzárendelését. Ebben az esetben a heti biztonsági mentések leképezése a szombat teljes lemezt, és a növekményes biztonsági mentések leképezése hétfőtől péntekig növekményes lemezeket. A biztonsági mentéseket és helyreállításokat vannak a storsimple-kötet összes rétegzett kötet.

![Elsődleges biztonsági mentési célként megadott konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>A StorSimple mint elsődleges biztonsági mentési cél GFS példa ütemezése

Íme egy példa négy hét, havi és évi GFS Elforgatás ütemezését:

| Gyakorisága, illetve biztonsági mentési típusa | Korlátlan | Növekményes (1-5 nap)  |   
|---|---|---|
| Heti (1 – 4 hét) | Szombat | Hétfőtől péntekig |
| Havonta  | Szombat  |   |
| Évente | Szombat  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-kötetek hozzárendelése Netbackuppal biztonsági mentési feladat

A következő szakasz azt feltételezi, Netbackuppal és a cél gazdagépen konfigurált összhangban a Netbackuppal ügynök irányelveket.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-kötetek hozzárendelése Netbackuppal biztonsági mentési feladat

1. A Netbackuppal felügyeleti konzolban válassza **Netbackuppal felügyeleti**, kattintson a jobb gombbal **házirendek**, majd válassza ki **új szabályzat**.

   ![Netbackuppal felügyeleti konzolján egy új szabályzat létrehozása](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Az a **adjon hozzá egy új házirendet** párbeszédpanelen adja meg a szabályzat nevét, és válassza a **házirend konfigurációs varázsló használata** jelölőnégyzetet. Kattintson az **OK** gombra.

   ![Felügyeleti konzol Netbackuppal, adjon hozzá egy új házirend párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. A biztonsági mentési szabályzat konfigurációs varázslójában használatba vétele, és válassza a biztonsági mentés típusát **tovább**.

   ![Netbackuppal felügyeleti konzolon válassza a biztonsági mentés típusa](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Állítsa be a házirend típusát, válassza a **Standard**, majd válassza ki **tovább**.

   ![Netbackuppal felügyeleti konzolon válassza a házirend típusa](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Válassza ki azt a gazdagépet, jelölje be a **ügyfél típusú operációs rendszer észlelése** jelölőnégyzetet, majd válassza ki **Hozzáadás**. Kattintson a **Tovább** gombra.

   ![Netbackuppal felügyeleti konzolján egy új házirendet a lista ügyfelek](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Válassza ki a meghajtót, készítsen biztonsági másolatot szeretne.

   ![Netbackuppal felügyeleti konzolján egy új szabályzatot a biztonsági mentési beállításokat](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Válassza ki a gyakoriság és megőrzési értékeket, a biztonsági mentési Elforgatás követelményeknek megfelelő.

   ![Netbackuppal felügyeleti konzolon, a biztonsági mentési gyakoriság és a egy új szabályzat rotációja](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Válassza ki **tovább** > **tovább** > **Befejezés**.  Az ütemezés a szabályzat létrehozása után módosíthatja.

9. Bontsa ki a szabályzat csak a létrehozott, és válassza ki a SELECT **ütemezések**.

   ![Netbackuppal felügyeleti konzolján egy új szabályzat ütemezése](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Kattintson a jobb gombbal **különbségi-Inc**válassza **átmásolása az új**, majd válassza ki **OK**.

    ![Netbackuppal felügyeleti konzolján egy új szabályzat ütemezését](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Kattintson a jobb gombbal az újonnan létrehozott ütemezést, és válassza **módosítás**.

12. Az a **attribútumok** lapon jelölje be a **felülbírálják a csoportházirend-tároló kiválasztása** jelölőnégyzetet, majd válassza ki a kötetet, hétfőn növekményes biztonsági mentések hová.

    ![Netbackuppal felügyeleti konzolon, ütemezés módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Az a **Start ablak** lapra, válassza ki a biztonsági mentések időtartományából.

    ![Netbackuppal felügyeleti konzolon, módosítási indítási ablak](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Kattintson az **OK** gombra.

15. Egyes növekményes biztonsági mentések esetében ismételje meg a 10. 14. Válassza ki a megfelelő kötetet és minden egyes létrehozott biztonsági mentés ütemezését.

16. Kattintson a jobb gombbal a **különbségi-Inc** ütemezhet, és törölje azt.

17. A biztonsági mentési igényeknek megfelelő teljes ütemezés módosítása.

    ![Netbackuppal felügyeleti konzolon, teljes ütemezés módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Módosítsa az indítási ablak.

    ![Felügyeleti konzol Netbackuppal, módosítsa az indítási ablak](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Az utolsó ütemezési így néz ki:

    ![Netbackuppal felügyeleti konzolon, a végső ütemezése](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési cél

> [!NOTE]
>Adatok visszaállítása a felhőbe tartozik lett rétegzett biztonsági felhőbeli sebességgel fordulhat elő.

Ebben a modellben egy átmeneti gyorsítótárban egyikükön rendelkeznie kell egy adathordozó (nem a StorSimple). Használhatja például a kötet (RAID) független lemezek redundáns tömbjének terület, a bemeneti/kimeneti (I/O) és a sávszélesség. A RAID 10, 5 és 50 használatát javasoljuk.

A következő ábrán látható tipikus rövid távú megőrzési (a kiszolgáló) helyi köteteket, és hosszú távú megőrzés archiválja a köteteket. Ebben a forgatókönyvben minden biztonsági mentés futtatása a helyi (a kiszolgáló) RAID-köteten. Ezeket a biztonsági másolatokat időszakosan ismétlődő és archivált archívumok kötetre. Fontos a helyi (a kiszolgáló) RAID kötetre méretezéséhez, így képes kezelni a rövid távú adatmegőrzési kapacitást és teljesítményre vonatkozó követelményeknek.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>A StorSimple mint egy másodlagos biztonsági mentési cél GFS példa

![A StorSimple mint egy másodlagos biztonsági mentési cél logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

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


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple-kötetek hozzárendelése egy Netbackuppal archív és a párhuzamos feladat

Netbackuppal biztosít a tárolás és média tartozó beállítások széles, mert azt javasoljuk, hogy olvassa a Veritas vagy a Netbackuppal mérnök megfelelően felmérni a storage életciklus (SLP) házirend követelményeinek.

Miután meghatározta a kezdeti lemezkészleteken, meg kell határoznia a három további tárhely életciklusokkal, összesen négy házirendek:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple-kötetek hozzárendelése egy Netbackuppal archív és a párhuzamos feladat

1. A Netbackuppal felügyeleti konzolban válassza **tárolási** > **tárolási életciklusokkal** > **új Storage életciklus-szabályzat**.

   ![Netbackuppal felügyeleti konzolon, az új tároló életciklus-szabályzat](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Adjon meg egy nevet a pillanatképet, és válassza **Hozzáadás**.

3. Az a **új művelet** párbeszédpanel a **tulajdonságok** lapon a **művelet**, jelölje be **Backup**. Válassza ki a használni kívánt értékeket **céltároló**, **megőrzési típus**, és **megőrzési időszak**. Kattintson az **OK** gombra.

   ![Netbackuppal felügyeleti konzolon, a művelet új párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Ez meghatározza az első biztonsági mentési művelet és a tárházban.

4. Jelölje ki az előző művelet, és válassza ki a SELECT **Hozzáadás**. Az a **módosítása tárolási művelet** párbeszédpanelen jelölje ki a kívánt értékeket a **céltároló**, **megőrzési típus**, és **megőrzésiideje**.

   ![Netbackuppal felügyeleti konzolon, a tárolási művelet módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Jelölje ki az előző művelet, és válassza ki a SELECT **Hozzáadás**. Az a **új Storage életciklus-szabályzat** párbeszédpanelen adjon hozzá a havi biztonsági mentések egy évig.

   ![Netbackuppal felügyeleti konzolon, az új tároló életciklus-szabályzat párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Mindaddig, amíg a szükséges átfogó SLP adatmegőrzési létrehozott ismételje meg 4 – 5.

   ![Netbackuppal felügyeleti konzolon, adhat hozzá szabályzatokat az új tároló életciklus-szabályzat párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Amikor végzett, a definiálása az SLP adatmegőrzési **házirend**, biztonsági mentési házirend meghatározása a következő lépések [Netbackuppal biztonsági mentési feladat hozzárendelése a StorSimple-kötetek](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. A **ütemezések**, a a **ütemezésének módosítása** párbeszédpanelen kattintson a jobb gombbal **teljes**, majd válassza ki **módosítása**.

   ![Netbackuppal felügyeleti konzolon, ütemezés módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Válassza ki a **felülbírálják a csoportházirend-tároló kiválasztása** jelölje be a jelölőnégyzetet, és válassza az 1 – 6. lépésben létrehozott SLP adatmegőrzési házirendet.

   ![Netbackuppal felügyeleti konzolon, felülírási szabályzat tároló kiválasztása](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Válassza ki **OK**, majd ismételje meg a növekményes biztonsági mentési ütemezés.

    ![Netbackuppal felügyeleti konzolon, a növekményes biztonsági mentések ütemezésének módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Biztonsági másolat megőrzése | Méret (TiB) | GFS szorzó\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Heti teljes |  1  |  4 | 4  |
| Napi növekményes  | 0,5  | 20 (ciklusok olyan hetente, havonta egyenlő) | 12 (2 további kvótáért.) |
| Teljes havi  | 1 | 12 | 12 |
| Éves teljes | 1  | 10 | 10 |
| GFS követelmény  |     |     | 38 |
| További kvótát  | 4  |    | 42 teljes GFS követelmény |

\* GFS szorzó példányszám történő védelme, és a biztonsági mentési szabályzat követelményeinek szüksége.

## <a name="storsimple-cloud-snapshots"></a>A StorSimple a felhőbeli pillanatképek

A StorSimple a felhőbeli pillanatképek a StorSimple-eszköz lévő adatok védelmét. Felhőbeli pillanatkép létrehozásának megegyezik egy külső létesítmény helyi másolatot tartalmazó szalagok szállítási. Ha az Azure georedundáns tárolást használ, felhőbeli pillanatkép létrehozásának megegyezik szállítási szalagot több helyhez. Ha egy eszköz visszaállítása egy vészhelyzetet követően van szüksége, előfordulhat, hogy csatlakoztassa egy másik StorSimple eszközt, és hajtsa végre a feladatátvételt. A feladatátvétel után meg tudják elérheti az adatokat (felhőbeli sebességgel), a legutóbbi felhőbeli pillanatkép.

Az alábbi szakasz elindításához és a StorSimple a felhőbeli pillanatképek törlése során a biztonsági mentés utáni feldolgozási rövid parancsfájl létrehozása.

> [!NOTE]
> A pillanatképek manuálisan vagy programon keresztül létrehozott ne hajtsa végre a StorSimple snapshot elévülési szabályzatot. Ezek a pillanatképek manuálisan vagy programon keresztül törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Indítsa el, és a felhőbeli pillanatképek törlése egy parancsfájl használatával

> [!NOTE]
> Gondosan mérje fel a megfelelőség és az adatok megőrzési következményekkel StorSimple pillanatkép törlése előtt. A biztonsági mentés utáni parancsfájl futtatásával kapcsolatos további információkért lásd: a [Netbackuppal dokumentáció](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Biztonsági mentési életciklusa

![Biztonsági mentési életciklus-diagram](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. A szkript hozzáadása a biztonsági mentési feladatok Netbackuppal. Ehhez a Netbackuppal feladat beállításainak módosítása előtti és utáni feldolgozási parancsokat.

> [!NOTE]
> Azt javasoljuk, hogy a StorSimple felhőalapú pillanatkép biztonsági mentési szabályzat utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. Biztonsági mentése és visszaállítása a biztonsági mentést végző alkalmazás környezet segítséget nyújtanak az RPO és RTO kapcsolatos további információkért tekintse meg a biztonsági mentési mérnök együtt.

## <a name="storsimple-as-a-restore-source"></a>A StorSimple-helyreállítás forrásaként

Visszaállítja a StorSimple eszköz munkahelyi például visszaállítás bármely block storage eszközről. Visszaállítás a felhőbe a rétegzett adatok felhőbeli sebességgel következik be. A helyi adatok esetében a visszaállítás fordulhat elő, az eszköz helyi lemez sebességével. Hajtson végre visszaállítást kapcsolatos információkért lásd: a [Netbackuppal dokumentáció](http://www.veritas.com/docs/000094423). Azt javasoljuk, hogy megfeleljenek Netbackuppal visszaállítási ajánlott eljárásokat.

## <a name="storsimple-failover-and-disaster-recovery"></a>A StorSimple feladatátvétel és a katasztrófa utáni helyreállítás

> [!NOTE]
> A biztonsági mentési cél esetben a StorSimple Cloud Appliance nem támogatott visszaállítási célként.

Katasztrófa számos tényező okozhatja. Az alábbi táblázat általános vész-helyreállítási helyzetekben.

| Forgatókönyv | Hatás | Helyreállítása | Megjegyzések |
|---|---|---|---|
| A StorSimple eszköz hiba | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a sikertelen eszközt, és hajtsa végre [StorSimple feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-device-failover-disaster-recovery.md). | Ha eszköz helyreállítása után a helyreállítás végrehajtásához szüksége, működő teljes adatkészletek lekért a felhő az új eszközhöz. Az összes műveletekre felhőbeli sebességgel. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a beolvasott és a helyi eszközön szintre, ami időigényes eljárás lehet, hogy a felhő szintjén lekért összes biztonságimásolat-készleteket. |
| Kiszolgálóhiba Netbackuppal | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Építse újra a biztonsági mentési kiszolgálóra, és végezze el az adatbázis-visszaállítás. | Építse újra, vagy állítsa vissza a Netbackuppal kiszolgálót, a vész-helyreállítási webhelyként kell. Állítsa vissza az adatbázist a legutóbbi pontnak. Ha a visszaállított Netbackuppal adatbázis nem a legújabb biztonsági mentési feladatok szinkronban, indexelési és katalogizálása megadása kötelező. Az index és a katalógus újbóli vizsgálata folyamatban okozhat a beolvasott és a felhő szintjén a helyi eszközön szintre lekért összes biztonságimásolat-készleteket. Így további időigényes. |
| Hely hiba, amely a biztonsági mentési kiszolgálóra és a StorSimple elvesztését eredményezi | Biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, és helyreállíthatja a Netbackuppal. | Először állítsa vissza a StorSimple, és helyreállíthatja a Netbackuppal. Hajtson végre visszaállítást eszköz helyreállítása után van szüksége, ha a teljes működő adatkészletek a vannak a felhőből az új eszköz beolvasni. Az összes műveletekre felhőbeli sebességgel. |

## <a name="references"></a>Referencia

Ez a cikk a hivatkozott az alábbi dokumentumokat:

- [A StorSimple-a többutas i/o-telepítő](storsimple-configure-mpio-windows-server.md)
- [Storage-forgatókönyv esetében: a dinamikus kiosztás](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Meghajtók GPT használata](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Állítsa be a megosztott mappák árnyékmásolatai](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [biztonságimásolat-készlet visszaállítási](storsimple-restore-from-backup-set-u2.md).
- További információk a végrehajtása [eszközön feladatátvétel és vészhelyreállítás helyreállítási](storsimple-device-failover-disaster-recovery.md).
