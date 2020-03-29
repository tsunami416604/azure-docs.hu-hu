---
title: StorSimple 8000 sorozat biztonsági cél a Veeam | Microsoft dokumentumok
description: A StorSimple biztonsági mentési célkonfigurációja a Veeam-mal ismerteti.
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
ms.author: matd
ms.openlocfilehash: 3ebf464fed1480e7452f246f04f3906faf0dd219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875303"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple tartalék célpontként a Veeam segítségével

## <a name="overview"></a>Áttekintés

Az Azure StorSimple a Microsoft hibrid felhőalapú tárolási megoldása. A StorSimple az exponenciális adatnövekedés összetettségét kezeli azáltal, hogy egy Azure Storage-fiókot használ a helyszíni megoldás kiterjesztéseként, és automatikusan rétegezi az adatokat a helyszíni és a felhőbeli tárolás között.

Ebben a cikkben a Veeam-mel való StorSimple-integrációt, valamint a két megoldás integrálásának ajánlott eljárásait tárgyaljuk. Azt is ajánlásokat tesz, hogyan kell beállítani Veeam a legjobban integrálható StorSimple. A Veeam ajánlott eljárásokra, biztonsági mentési tervezőkre és rendszergazdákra való halasztást a Veeam beállításának legjobb módja az egyéni biztonsági mentési követelmények és a szolgáltatásiszint-szerződések (SLA) teljesítéséhez.

Bár bemutatjuk a konfigurációs lépéseket és a legfontosabb fogalmakat, ez a cikk semmiképpen sem lépésenként konfigurációs vagy telepítési útmutató. Feltételezzük, hogy az alapvető összetevők és az infrastruktúra üzemképes, és készen áll, hogy támogassa az általunk leírt fogalmakat.

### <a name="who-should-read-this"></a>Kinek kellene ezt elolvasnia?

A cikkben található információk a tárolást, a Windows Server 2012 R2, az Ethernet, a felhőszolgáltatások és a Veeam szolgáltatást ismerő biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolótervezők számára lesznek a leghasznosabbak.

### <a name="supported-versions"></a>Támogatott verziók

-   Veeam 9 és újabb verziók
-   [StorSimple Update 3 és újabb verziók](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Miért a StorSimple a tartalék célpont?

StorSimple van egy jó válogatott részére egy hát cél mert:

-   Ez biztosítja a szabványos, helyi tároló biztonsági mentési alkalmazások használni, mint egy gyors biztonsági mentési cél, változtatás nélkül. A StorSimple segítségével is gyorsan visszaállíthatja a legutóbbi biztonsági mentéseket.
-   Felhőrétegezése zökkenőmentesen integrálva van egy Azure-beli felhőalapú tárfiókkal a költséghatékony Azure Storage használatához.
-   Automatikusan biztosítja a külső tárolása a vészhelyreállításhoz.


## <a name="key-concepts"></a>Fő fogalmak

Mint minden tárolási megoldás, a megoldás tárolási teljesítményének, az SL-eknek, a változási sebességnek és a kapacitásnövekedési igényeknek a gondos felmérése is elengedhetetlen a sikerhez. A fő ötlet az, hogy egy felhőszintű réteg bevezetésével a hozzáférési idők és a felhőbeli áteresztőképesség alapvető szerepet játszanak a StorSimple feladatának elérésében.

A StorSimple-t úgy tervezték, hogy jól meghatározott munkaadat-halmazon (gyorselérésű adatokon) működő alkalmazások számára biztosítson tárolást. Ebben a modellben a munkaadatok a helyi szinteken tárolódnak, és a fennmaradó nonworking/hideg/archivált adathalmaz rétegzett a felhőbe. Ez a modell az alábbi ábrán jelenik meg. A majdnem sima zöld vonal a StorSimple eszköz helyi szintjein tárolt adatokat jelöli. A piros vonal a StorSimple-megoldáson tárolt adatok teljes mennyiségét jelöli az összes rétegben. A síkzöld vonal és az exponenciális piros görbe közötti térköz a felhőben tárolt adatok teljes mennyiségét jelöli.

**StorSimple rétegezési**
![StorSimple rétegezési diagram](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Ezt az architektúrát szem előtt tartva, azt fogja találni, hogy a StorSimple ideális a biztonsági mentési célként való működéshez. A StorSimple segítségével a következőkre használható:

-   Végezze el a leggyakoribb visszaállításokat a helyi munkaadatkészletből.
-   Használja a felhőt a külső vészhelyreállításhoz és a régebbi adatokhoz, ahol a visszaállítások kevésbé gyakoriak.

## <a name="storsimple-benefits"></a>StorSimple előnyei

A StorSimple olyan helyszíni megoldást kínál, amely zökkenőmentesen integrálható a Microsoft Azure-ral, kihasználva a helyszíni és a felhőalapú tároláshoz való zökkenőmentes hozzáférést.

A StorSimple automatikus rétegezést használ a helyszíni eszköz között, amely ssd-eszközzel (SSD) és soros csatlakoztatott SCSI(SCSI) tárolóval rendelkezik, valamint az Azure Storage. Az automatikus rétegezés a gyakran használt adatokat helyi szinten tartja az SSD- és SAS-szinteken. Ritkán használt adatokat az Azure Storage-ba helyezi át.

A StorSimple a következő előnyöket kínálja:

-   Egyedi deduplikációs és tömörítési algoritmusok, amelyek a felhőt használják a soha nem látott deduplikációs szintek eléréséhez
-   Magas rendelkezésre állás
-   Georeplikáció az Azure georeplikációjával
-   Azure-integráció
-   Adattitkosítás a felhőben
-   Továbbfejlesztett katasztrófa-helyreállítás és megfelelőség

Bár a StorSimple két fő telepítési forgatókönyvet (elsődleges biztonsági mentési cél és másodlagos biztonsági mentési cél) mutat be, alapvetően egy egyszerű, blokk tárolóeszköz. StorSimple mindent megtesz a tömörítés és deduplikáció. Zökkenőmentesen küld és lekéri az adatokat a felhő és az alkalmazás és a fájlrendszer között.

A StorSimple szolgáltatásról további információt a [StorSimple 8000 sorozat: Hibrid felhőalapú tárolási megoldás című](storsimple-overview.md)témakörben talál. Is, akkor tekintse át a [műszaki StorSimple 8000 sorozat specifikációk](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> A StorSimple-eszköz biztonsági mentési célként való használata csak a StorSimple 8000 Update 3 és újabb verziók esetén támogatott.

## <a name="architecture-overview"></a>Az architektúra áttekintése

Az alábbi táblázatok az eszköz modell-architektúra kezdeti útmutatást mutatják.

**StorSimple kapacitások helyi és felhőalapú tároláshoz**

| Tárkapacitás | 8100 | 8600 |
|---|---|---|
| Helyi tárolási kapacitás | &lt;10 TiB\*  | &lt;20 TiB\*  |
| Felhőalapú tárolókapacitás | &gt;200 TiB\* | &gt;500 TiB\* |

\*A tároló mérete nem feltételez deduplikációt vagy tömörítést.

**StorSimple kapacitások elsődleges és másodlagos biztonsági mentések**

| Biztonsági mentési forgatókönyv  | Helyi tárolási kapacitás  | Felhőalapú tárolókapacitás  |
|---|---|---|
| Elsődleges biztonsági mentés  | A helyi tárolón tárolt legutóbbi biztonsági mentések a helyreállítási pont (RPO) eléréséhez gyors helyreállításhoz | A biztonsági mentési előzmények (RPO) elférnek a felhő kapacitásában |
| Másodlagos biztonsági mentés | A biztonsági mentési adatok másodlagos másolata felhőkapacitásban tárolható  | N/A  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple elsődleges biztonsági mentési célként

Ebben a forgatókönyvben a StorSimple-kötetek a biztonsági mentési alkalmazás a biztonsági mentések egyetlen tárházaként jelennek meg. Az alábbi ábrán egy megoldásarchitektúra, amelyben az összes biztonsági mentések használata StorSimple rétegzett kötetek biztonsági mentések és visszaállítások.

![StorSimple elsődleges biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Elsődleges célbiztonsági mentés logikai lépései

1.  A biztonsági másolat kiszolgálója kapcsolatba lép a célbiztonsági másolat ügynökével, és a biztonságimásolat-készítő ügynök adatokat továbbít a biztonsági másolat kiszolgálójára.
2.  A biztonsági másolat kiszolgáló adatokat ír a StorSimple rétegzett kötetek.
3.  A biztonságimásolat-kiszolgáló frissíti a katalógusadatbázist, majd befejezi a biztonsági mentési feladatot.
4.  A pillanatkép-parancsfájl elindítja a StorSimple felhőbeli pillanatkép-kezelő (indítás vagy törlés).
5.  A biztonsági másolat kiszolgáló adatmegőrzési házirend alapján törli a lejárt biztonsági mentéseket.

### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállítási logikai lépései

1.  A biztonsági másolat kiszolgálója elindítja a megfelelő adatok visszaállítását a tárolótárból.
2.  A biztonságimásolat-készítő ügynök fogadja az adatokat a biztonsági másolat kiszolgálójáról.
3.  A biztonsági másolat kiszolgálója befejezi a visszaállítási feladatot.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple másodlagos biztonsági mentési célként

Ebben a forgatókönyvben a StorSimple kötetek elsősorban hosszú távú megőrzési vagy archiválási.

Az alábbi ábrán egy architektúra látható, amelyben a kezdeti biztonsági mentések és visszaállítások nagy teljesítményű kötetet céloznak meg. Ezek a biztonsági mentések másolása és archiválása egy StorSimple rétegzett kötet egy meghatározott ütemezés szerint.

Fontos, hogy méretezze a nagy teljesítményű kötetet, hogy kezelni tudja az adatmegőrzési házirend kapacitását és teljesítménykövetelményeit.

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Másodlagos célbiztonsági mentés logikai lépései

1.  A biztonsági másolat kiszolgálója kapcsolatba lép a célbiztonsági másolat ügynökével, és a biztonságimásolat-készítő ügynök adatokat továbbít a biztonsági másolat kiszolgálójára.
2.  A biztonsági másolat kiszolgálója adatokat ír a nagy teljesítményű tárolóba.
3.  A biztonságimásolat-kiszolgáló frissíti a katalógusadatbázist, majd befejezi a biztonsági mentési feladatot.
4.  A biztonsági másolat kiszolgáló adatmásolatokat a StorSimple egy adatmegőrzési házirend alapján.
5.  A pillanatkép-parancsfájl elindítja a StorSimple felhőbeli pillanatkép-kezelő (indítás vagy törlés).
6.  A biztonsági másolat kiszolgáló adatmegőrzési házirend alapján törli a lejárt biztonsági mentéseket.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél-visszaállítási logikai lépések

1.  A biztonsági másolat kiszolgálója elindítja a megfelelő adatok visszaállítását a tárolótárból.
2.  A biztonságimásolat-készítő ügynök fogadja az adatokat a biztonsági másolat kiszolgálójáról.
3.  A biztonsági másolat kiszolgálója befejezi a visszaállítási feladatot.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A megoldás üzembe helyezése három lépést igényel:

1. Készítse elő a hálózati infrastruktúrát.
2. Telepítse a StorSimple-eszközt biztonsági mentési célként.
3. Veeam bevetése.

Minden lépést részletesen tárgyalunk a következő szakaszokban.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple az Azure-felhővel integrált megoldás, a StorSimple aktív és működő kapcsolatot igényel az Azure-felhővel. Ez a kapcsolat olyan műveletekhez használatos, mint a felhőbeli pillanatképek, az adatkezelés és a metaadat-átvitel, valamint a régebbi, kevésbé elérhető adatok azure-beli felhőalapú tárhelyre való rétegezése.

A megoldás optimális végrehajtásához azt javasoljuk, hogy kövesse az alábbi ajánlott hálózati eljárásokat:

-   A StorSimple rétegezést az Azure-hoz csatlakoztató hivatkozásnak meg kell felelnie a sávszélesség-követelményeknek. Ezt úgy érheti el, hogy a szükséges Szolgáltatásminőségi (QoS) szintet alkalmazza az infrastruktúra-kapcsolókra, hogy megfeleljen az RPO és a helyreállítási idő célkitűzésének (RTO) SLA-inak.
-   Az Azure Blob-tároló késése iményének körülbelül 80 ms-nak kell lennie.

### <a name="deploy-storsimple"></a>StorSimple telepítése

A StorSimple központi telepítési útmutatója a [helyszíni StorSimple-eszköz üzembe helyezése.](storsimple-deployment-walkthrough-u2.md)

### <a name="deploy-veeam"></a>Veeam telepítése

A Veeam telepítésével kapcsolatos gyakorlati tanácsokat a [Veeam biztonsági mentés & replikációval kapcsolatos gyakorlati tanácsai](https://bp.veeam.expert/)című témakörben találja, és olvassa el a Felhasználói útmutatót a [Veeam Súgóban (Műszaki dokumentáció).](https://www.veeam.com/documentation-guides-datasheets.html)

## <a name="set-up-the-solution"></a>A megoldás beállítása

Ebben a szakaszban bemutatjuk néhány konfigurációs példákat. A következő példák és ajánlások a legalapvetőbb és legalapvetőbb végrehajtást mutatják be. Előfordulhat, hogy ez a megvalósítás nem vonatkozik közvetlenül az adott biztonsági mentési követelményekre.

### <a name="set-up-storsimple"></a>StorSimple beállítása

| StorSimple telepítési feladatok  | További megjegyzések |
|---|---|
| Telepítse a helyszíni StorSimple-eszközt. | Támogatott verziók: 3. és újabb verziók frissítése. |
| Kapcsolja be a tartalék célpontot. | Ezekkel a parancsokkal bekapcsolhatja vagy kikapcsolhatja a biztonsági mentés célmódját, és állapotot kaphat. További információ: [Csatlakozás távolról StorSimple eszközhöz.](storsimple-remote-connect.md)</br> A biztonsági mentési `Set-HCSBackupApplianceMode -enable`mód bekapcsolása: . </br> A biztonsági mentési `Set-HCSBackupApplianceMode -disable`mód kikapcsolása: . </br> A biztonsági mentési mód beállításainak beszerezése: `Get-HCSBackupApplianceMode`. |
| Hozzon létre egy közös kötettárolót a kötethez, amely tárolja a biztonsági mentési adatokat. A kötettárolóban lévő összes adat deduplikált. | A StorSimple kötettárolók deduplikációs tartományokat határoznak meg.  |
| StorSimple kötetek létrehozása. | Hozzon létre olyan köteteket, amelyek mérete a várt használathoz a lehető legközelebb van, mivel a kötet mérete hatással van a felhőbeli pillanatkép időtartamára. A kötetek méretéről az [adatmegőrzési házirendekről](#retention-policies)olvashat.</br> </br> Használja a StorSimple rétegzett köteteket, és jelölje be a **Kötet használata a ritkábban használt archiválási adatokhoz** jelölőnégyzetet. </br> Csak a helyileg rögzített kötetek használata nem támogatott. |
| Hozzon létre egy egyedi StorSimple biztonsági mentési házirendet az összes biztonsági mentési célkötethez. | A StorSimple biztonsági mentési házirend határozza meg a kötet konzisztenciacsoport. |
| Tiltsa le az ütemezést a pillanatképek lejártakor. | A pillanatképek utófeldolgozási műveletként aktiválódnak. |

### <a name="set-up-the-host-backup-server-storage"></a>A gazdabiztonsági kiszolgáló tárolójának beállítása

Állítsa be a gazdabiztonsági kiszolgáló tárhelyét az alábbi irányelvek nek megfelelően:  

- Ne használjon átnyúló köteteket (amelyeket a Windows Lemezkezelés hozott létre). A átnyúló kötetek nem támogatottak.
- A kötetek formázása NTFS-sel, 64 KB-os foglalási egységmérettel.
- A StorSimple kötetek hozzárendelése közvetlenül a Veeam kiszolgálóra.
    - Használja az iSCSI-t fizikai kiszolgálókhoz.


## <a name="best-practices-for-storsimple-and-veeam"></a>A StorSimple és a Veeam bevált módszerei

Állítsa be a megoldást a következő néhány szakasz irányelveinek megfelelően.

### <a name="operating-system-best-practices"></a>Az operációs rendszerrel kapcsolatos gyakorlati tanácsok

- Tiltsa le a Windows Server titkosítást és a deduplikációt az NTFS fájlrendszerben.
- Tiltsa le a Windows Server töredezettségmentesítését a StorSimple köteteken.
- Tiltsa le a Windows Server indexelését a StorSimple köteteken.
- Futtasson víruskereső vizsgálatot a forrásállomáson (ne a StorSimple köteteken).
- Kapcsolja ki a Windows Server alapértelmezett [karbantartását](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) a Feladatkezelőben. Ezt az alábbi módokon tegye:
  - Kapcsolja ki a Karbantartás konfigurátort a Windows Feladatütemezőben.
  - Töltse le a [PsExec-et](https://technet.microsoft.com/sysinternals/bb897553.aspx) a Windows Sysinternals rendszerről. A PsExec letöltése után futtassa a Windows PowerShellt rendszergazdaként, és írja be a következőt:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple gyakorlati tanácsok

-   Győződjön meg arról, hogy a StorSimple eszköz [3-as vagy újabb frissítésre](storsimple-install-update-3.md)frissül.
-   Az iSCSI és a felhőforgalom elkülönítése. A StorSimple és a biztonsági másolat-kiszolgáló közötti forgalomhoz használjon dedikált iSCSI-kapcsolatokat.
-   Győződjön meg arról, hogy a StorSimple-eszköz egy dedikált biztonsági mentési cél. A vegyes számítási feladatok nem támogatottak, mert hatással vannak az RTO-ra és az RPO-ra.

### <a name="veeam-best-practices"></a>A Veeam bevált módszerei

-   A Veeam-adatbázisnak a kiszolgálón kell lennie, és nem storSimple-köteten kell lennie.
-   Vészhelyreállításhoz biztonsági másolatot kell a Veeam-adatbázisról egy StorSimple-köteten.
-   Támogatjuk a Veeam teljes és növekményes biztonsági mentéseket ehhez a megoldáshoz. Azt javasoljuk, hogy ne használjon szintetikus és különbözeti biztonsági mentéseket.
-   A biztonsági másolat adatfájljai csak egy adott feladat adatait tartalmazhatják. Például nem média hozzáfűzések különböző feladatok között engedélyezettek.
-   A feladatellenőrzés kikapcsolása. Szükség esetén az ellenőrzést a legutóbbi biztonsági mentési feladat után kell ütemezni. Fontos megérteni, hogy ez a feladat hatással van a biztonsági mentési ablak.
-   Az adathordozó előzetes allokációjának bekapcsolása.
-   Győződjön meg arról, hogy a párhuzamos feldolgozás be van kapcsolva.
-   Kapcsolja ki a tömörítést.
-   Kapcsolja ki a deduplikációt a biztonsági mentési feladatban.
-   Állítsa az optimalizálást **A LAN Target értékre.**
-   Kapcsolja be **az Aktív teljes biztonsági mentés létrehozása** (2 hetente).
-   A biztonsági másolat tárházán állítsa be **a Virtuálisgép-biztonsági másolatonkénti biztonsági másolatokat.**
-   Állítsa be **A több feltöltési adatfolyam használata feladatonként** **8-at** (legfeljebb 16 engedélyezett). Állítsa be ezt a számot fel- vagy lea StorSimple-eszközön a CPU-kihasználtság alapján.

## <a name="retention-policies"></a>Adatmegőrzési házirendek

Az egyik leggyakoribb biztonsági mentési adatmegőrzési házirend-típusok egy nagyapa, apa és a fiú (GFS) házirend. A GFS-házirendben a növekményes biztonsági mentés naponta történik, és a teljes biztonsági mentések hetente és havonta történik. Ez a házirend hat StorSimple rétegzett kötetet eredményez: egy kötet tartalmazza a heti, havi és éves teljes biztonsági mentéseket; a másik öt kötet napi növekményes biztonsági mentéseket tárol.

A következő példában egy GFS rotációt használunk. A példa a következőket feltételezi:

-   Nem deduped vagy tömörített adatokat használ.
-   A teljes biztonsági mentés egyenként 1 TiB.
-   A napi növekményes biztonsági mentések egyenként 500 GiB.Daily incremental backups are 500 GiB each.
-   Négy heti biztonsági mentés tartjuk egy hónapig.
-   Tizenkét havi biztonsági mentések egy évig tárolják.
-   Egy éves biztonsági mentés 10 évig megmarad.

Az előző feltételezések alapján hozzon létre egy 26-TiB StorSimple rétegzett kötet a havi és éves teljes biztonsági mentések. Hozzon létre egy 5-TiB StorSimple rétegzett kötet minden egyes növekményes napi biztonsági mentések.

| Biztonsági másolat típusának megőrzése | Méret (TiB) | GFS szorzó\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Heti teljes | 1 | 4  | 4 |
| Napi növekményes | 0,5 | 20 (ciklusok egyenlő számú hét havonta) | 12 (2 kiegészítő kontingens esetén) |
| Havi teljes | 1 | 12 | 12 |
| Éves encika | 1  | 10 | 10 |
| GFS-követelmény |   | 38 |   |
| További kontingens  | 4  |   | 42 teljes GFS követelmény  |

\*A GFS szorzó a biztonsági mentési házirend követelményeinek teljesítéséhez szükséges másolatok száma.

## <a name="set-up-veeam-storage"></a>Veeam-tároló beállítása

### <a name="to-set-up-veeam-storage"></a>A Veeam storage beállítása

1.  A Veeam biztonsági mentési és replikációs konzol, **a Tárház eszközök**, nyissa meg a **Biztonsági infrastruktúra**. Kattintson a jobb gombbal **a Biztonsági másolat tárházának**elemre, majd válassza **a Biztonságimásolat-tárház hozzáadása parancsot.**

    ![Veeam felügyeleti konzol, biztonsági másolat tárházának lapja](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Az **Új biztonságimásolat-tárház** párbeszédpanelen adja meg a tárház nevét és leírását. Válassza a **Tovább lehetőséget.**

    ![Veeam kezelőkonzol, név és leírás lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  A típushoz válassza a **Microsoft Windows kiszolgáló**t. Válassza ki a Veeam kiszolgálót. Válassza a **Tovább lehetőséget.**

    ![Veeam felügyeleti konzol, válassza ki a biztonsági másolat tárházának típusát](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  A **Hely**megadásához tallózással és a kötet kijelölésével. Jelölje be az **Egyidejű feladatok maximális korlátozása a következőkre** jelölőnégyzetet, és állítsa az értéket **4-re.** Ez biztosítja, hogy csak négy virtuális lemez feldolgozása egyidejűleg, míg az egyes virtuális gépek (VM) feldolgozása. Válassza a **Speciális** gombot.

    ![Veeam felügyeleti konzol, kötet kiválasztása](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  A **Tárolás kompatibilitási beállításai** párbeszédpanelen jelölje be a **Virtuálisgép-biztonsági másolatonkénti biztonsági másolatok használata jelölőnégyzetet.**

    ![Veeam felügyeleti konzol, tárolási kompatibilitási beállítások](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Az **Új biztonságimásolat-tárház** párbeszédpanelen jelölje be a **vPower NFS szolgáltatás engedélyezése a csatlakoztatási kiszolgálón (ajánlott)** jelölőnégyzetet. Válassza a **Tovább lehetőséget.**

    ![Veeam felügyeleti konzol, biztonsági másolat tárházának lapja](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Tekintse át a beállításokat, és válassza a **Tovább gombot.**

    ![Veeam felügyeleti konzol, biztonsági másolat tárházának lapja](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    A Veeam kiszolgálóhoz egy adattár kerül.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>A StorSimple beállítása elsődleges biztonsági mentési célként

> [!IMPORTANT]
> A felhőbe rétegzett biztonsági másolatból származó adat-visszaállítás felhősebességen történik.

Az alábbi ábra egy tipikus kötet biztonsági mentési feladathoz való hozzárendelését mutatja be. Ebben az esetben az összes heti biztonsági mentések leképezése a szombati teljes lemezre, és a növekményes biztonsági mentések leképezése hétfőtől péntekig növekményes lemezek. Az összes biztonsági mentések és visszaállítások egy StorSimple rétegzett kötetről.

![Elsődleges biztonsági mentési cél konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple elsődleges tartalék cél GFS ütemezés példa

Íme egy példa a GFS rotációs ütemezésnégy hét, havonta és évente:

| Gyakoriság/biztonsági mentés típusa | Összes | Növekményes (1-5. nap)  |   
|---|---|---|
| Heti (1-4. hét) | Szombat | Hétfő-Péntek |
| Havonta  | Szombat  |   |
| Éves | Szombat  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple kötetek hozzárendelése Veeam biztonsági mentési feladathoz

Az elsődleges biztonsági mentési célforgatókönyvhöz hozzon létre egy napi feladatot az elsődleges Veeam StorSimple-kötettel. Másodlagos biztonsági mentési célforgatókönyv höz hozzon létre egy napi feladatot a Direct Attached Storage (DAS), a Network Attached Storage (NAS) vagy a Just a Bunch of Disks (JBOD) tároló használatával.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple kötetek hozzárendelése Veeam biztonsági mentési feladathoz

1.  A Veeam biztonsági mentési és replikációs konzolján válassza a **Biztonsági mentés & replikáció lehetőséget.** Kattintson a jobb gombbal **a Biztonsági másolat**elemre, majd a környezettől függően válassza a **VMware** vagy a **Hyper-V**lehetőséget.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Az **Új biztonságimentési feladat** párbeszédpanelen adja meg a napi biztonsági mentési feladat nevét és leírását.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Válassza ki azt a virtuális gépet, amelyről biztonsági másolatot szeretne tartani.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Válassza ki a **Biztonsági másolat proxyés** a **Biztonságimásolat-tárház kívánt értékeit.** Válasszon ki egy értéket **a visszaállítási pontokhoz, amelyeket** a helyileg csatlakoztatott tárolón lévő RPO- és RTO-definícióknak megfelelően meg tart a lemezen. Válassza az **Advanced** (Speciális) lehetőséget.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. A **Speciális beállítások** párbeszédpanel **Biztonsági másolat** lapján válassza a **Növekményes**lehetőséget. Győződjön meg arról, hogy a **Szintetikus teljes biztonsági mentések létrehozása rendszeres időközönként** jelölőnégyzet nincs bejelölve. Jelölje be az **Aktív teljes biztonsági mentések létrehozása rendszeresen jelölőnégyzetet.** Az **Aktív teljes biztonsági mentés csoportban**jelölje be a Heti a kijelölt **napokon** jelölőnégyzetet szombatra.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. A **Tárolás** lapon győződjön meg arról, hogy a **szövegközi adatdeduplikáció engedélyezése** jelölőnégyzet nincs bejelölve. Jelölje be a **Cserefájlblokkok kizárása** jelölőnégyzetet, majd a **Törölt fájlblokkok kizárása** jelölőnégyzetet. A **None** **Tömörítési szint nincs.** A kiegyensúlyozott teljesítmény és deduplikáció érdekében állítsa **a Tároló optimalizálását** **LAN-célra.** Válassza **az OK gombot.**

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    A Veeam deduplikációs és tömörítési beállításairól az [Adattömörítés és deduplikáció](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)című témakörben olvashat.

7.  A **Biztonságimásolat-készítő feladat szerkesztése** párbeszédpanelen jelölje be az **Alkalmazásbarát feldolgozás engedélyezése** jelölőnégyzetet (nem kötelező).

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat vendég feldolgozási lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Állítsa be, hogy az ütemezés naponta egyszer fusson, a megadott időpontban.

    ![Veeam felügyeleti konzol, új biztonsági mentési feladat ütemezése oldal](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési célként

> [!NOTE]
> A felhőbe rétegzett biztonsági másolatból származó adat-visszaállítások felhősebességen történnek.

Ebben a modellben ideiglenes gyorsítótárként való kiszolgálóadathordozóval (a StorSimple-től eltérő) kell rendelkeznie. Használhatja például a raid-kötetek redundáns tömbjét a hely, a bemeneti/kimeneti (I/O) és a sávszélesség befogadására. Javasoljuk a RAID 5, 50 és 10 használatát.

Az alábbi ábra a tipikus rövid távú megőrzési helyi (a kiszolgálóra) köteteket és a hosszú távú megőrzési archívumköteteket mutatja be. Ebben az esetben az összes biztonsági mentés a helyi (a kiszolgálóra) RAID-köteten fut. Ezeket a biztonsági másolatokat a rendszer rendszeresen megkettőzi és archiválja egy archív kötetre. Fontos, hogy méretezze a helyi (a kiszolgálóra) RAID kötetet, hogy képes legyen kezelni a rövid távú megőrzési kapacitást és a teljesítménykövetelményeket.

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple másodlagos tartalék célGFS példa

Az alábbi táblázat bemutatja, hogyan állíthatja be a biztonsági másolatokat a helyi és a StorSimple lemezeken való futtatáshoz. Ez magában foglalja az egyéni és a teljes kapacitásra vonatkozó követelményeket.

| Biztonsági másolat típusa és megőrzése | Konfigurált tároló | Méret (TiB) | GFS szorzó | Teljes\* kapacitás (TiB) |
|---|---|---|---|---|
| 1. hét (teljes és növekményes) |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| StorSimple hét 2-4 |StorSimple lemez (hosszú távú) | 1 | 4 | 4 |
| Havi teljes |StorSimple lemez (hosszú távú) | 1 | 12 | 12 |
| Éves encika |StorSimple lemez (hosszú távú) | 1 | 1 | 1 |
|GFS-mennyiségek méretére vonatkozó követelmény |  |  |  | 18*|

\*A teljes kapacitás 17 TiB StorSimple lemezt és 1 TiB helyi RAID kötetet tartalmaz.


### <a name="gfs-example-schedule"></a>GFS példa ütemezése

GFS rotációs heti, havi és éves ütemezés

| Hét | Összes | Növekményes nap 1 | 2. növekményes nap | Növekményes nap 3 | 4. nap | Növekményes nap 5 |
|---|---|---|---|---|---|---|
| 1. hét | Helyi RAID-hangerő  | Helyi RAID-hangerő | Helyi RAID-hangerő | Helyi RAID-hangerő | Helyi RAID-hangerő | Helyi RAID-hangerő |
| 2. hét | StorSimple hét 2-4 |   |   |   |   |   |
| 3. hét | StorSimple hét 2-4 |   |   |   |   |   |
| 4. hét | StorSimple hét 2-4 |   |   |   |   |   |
| Havonta | StorSimple havonta |   |   |   |   |   |
| Éves | StorSimple évente  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple kötetek hozzárendelése Veeam másolási feladathoz

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple kötetek hozzárendelése Veeam másolási feladathoz

1.  A Veeam biztonsági mentési és replikációs konzolján válassza a **Biztonsági mentés & replikáció lehetőséget.** Kattintson a jobb gombbal **a Biztonsági másolat**elemre, majd a környezettől függően válassza a **VMware** vagy a **Hyper-V**lehetőséget.

    ![Veeam felügyeleti konzol, új biztonsági másolat feladat oldal](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Az **Új biztonságimásolat-másolási feladat** párbeszédpanelen adja meg a feladat nevét és leírását.

    ![Veeam felügyeleti konzol, új biztonsági másolat feladat oldal](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Jelölje ki a feldolgozni kívánt virtuális gépeket. Válasszon a biztonsági mentések közül, majd válassza ki a korábban létrehozott napi biztonsági másolatot.

    ![Veeam felügyeleti konzol, új biztonsági másolat feladat oldal](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Szükség esetén zárja ki az objektumokat a biztonsági másolati feladatból.

5.  Válassza ki a biztonsági másolat tárházát, és állítson be egy értéket a **visszatartandó pontokhoz.** Ügyeljen arra, hogy bejelölje **a Következő visszaállítási pontok megtartása archiválási célokra jelölőnégyzetet.** Adja meg a biztonsági mentés gyakoriságát, majd válassza a **Speciális**lehetőséget.

    ![Veeam felügyeleti konzol, új biztonsági másolat feladat oldal](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Adja meg a következő speciális beállításokat:

    * A **Karbantartás** lapon kapcsolja ki a tárolási szintű sérülésvédőt.

    ![Veeam felügyeleti konzol, új biztonsági másolat idoben végzett feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * A **Tárolás** lapon győződjön meg arról, hogy a deduplikáció és a tömörítés ki van kapcsolva.

    ![Veeam felügyeleti konzol, új biztonsági másolat idoben végzett feladat speciális beállítások lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Adja meg, hogy az adatátvitel közvetlen.

8.  Adja meg a biztonsági másolat ablakütemezését az igényeinek megfelelően, majd fejezze be a varázslót.

További információt a Biztonsági másolati feladatok létrehozása című [témakörben talál.](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html)

## <a name="storsimple-cloud-snapshots"></a>StorSimple felhőbeli pillanatképek

A StorSimple felhőbeli pillanatképei védik a StorSimple-eszközben található adatokat. A felhőbeli pillanatkép létrehozása egyenértékű a helyi biztonsági mentési szalagok külső létesítménybe történő szállításával. Ha az Azure georedundáns tárolás, hozzon létre egy felhőbeli pillanatkép egyenértékű a szállítási biztonsági mentési szalagok több helyen. Ha egy katasztrófa után vissza kell állítania egy eszközt, előfordulhat, hogy egy másik StorSimple-eszközt hoz online állapotba, és feladatátvételt végez. A feladatátvétel után a legutóbbi felhőbeli pillanatképből (felhősebességen) hozzáférhet az adatokhoz.

A következő szakasz ismerteti, hogyan hozhat létre egy rövid parancsfájlt a StorSimple felhőbeli pillanatképek indításához és törléséhez a biztonsági mentés utófeldolgozása során.

> [!NOTE]
> Manuálisan vagy programozott módon létrehozott pillanatképek nem követik a StorSimple pillanatkép lejárati szabályzatát. Ezeket a pillanatképeket manuálisan vagy programozott módon törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Felhőbeli pillanatképek indítása és törlése parancsfájl használatával

> [!NOTE]
> A StorSimple-pillanatkép törlése előtt gondosan értékelje a megfelelőségi és adatmegőrzési következményeket. A biztonsági mentés utáni parancsfájlok futtatásáról a Veeam dokumentációjában olvashat bővebben.


### <a name="backup-lifecycle"></a>Biztonsági mentés életciklusa

![Biztonsági mentés életciklus-diagramja](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Követelmények

-   A parancsfájlt futtató kiszolgálónak hozzáféréssel kell rendelkeznie az Azure felhőalapú erőforrásaihoz.
-   A felhasználói fióknak rendelkeznie kell a szükséges engedélyekkel.
-   A társított StorSimple kötetekkel rendelkező StorSimple biztonsági mentési házirendet be kell állítani, de nem kell bekapcsolni.
-   Szüksége lesz a StorSimple erőforrás nevét, regisztrációs kulcs, eszköz nevét, és a biztonsági mentési házirend-azonosító.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Felhőbeli pillanatkép indítása vagy törlése

1. [Telepítse az Azure PowerShellt.](/powershell/azure/overview)
2. Töltse le és állítsa be [a Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-parancsfájlt.
3. A parancsfájlt futtató kiszolgálón futtassa a PowerShellt rendszergazdaként. Győződjön meg arról, hogy a parancsfájl futtatásához, `-WhatIf $true` hogy milyen módosításokat fog végrehajtani a parancsfájl. Az ellenőrzés befejezése után `-WhatIf $false`adja át a menetet. Futtassa az alábbi parancsot:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Ha hozzá szeretné adni a parancsfájlt a biztonsági mentési feladathoz, szerkesztheti a Veeam-feladat speciális beállításait.

    ![Veeam backup speciális beállítások szkriptek lap](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Azt javasoljuk, hogy futtassa a StorSimple felhőbeli pillanatkép biztonsági mentési szabályzatot utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. Ha további információra van szüksége arról, hogyan készíthet biztonsági mentést és visszaállítást a biztonsági mentési alkalmazáskörnyezetről, hogy könnyebben megfeleljen az RPO-nak és az RTO-nak, kérjük, forduljon a biztonsági mentési tervezőhöz.

## <a name="storsimple-as-a-restore-source"></a>StorSimple mint visszaállítási forrás

A StorSimple-eszközről történő visszaállítás a blokktároló eszközről történő visszaállításhoz hasonlóan működik. A felhőbe rétegzett adatok visszaállítása felhősebességen történik. A helyi adatok visszaállítása az eszköz helyi lemezsebességén.

A Veeam segítségével gyors, szemcsés, fájlszintű helyreállítást érhet el a StorSimple-en keresztül a Veeam konzol beépített felfedező nézetein keresztül. A Veeam Explorers segítségével helyreállíthatja az egyes elemeket, például az e-maileket, az Active Directory-objektumokat és a SharePoint-elemeket a biztonsági másolatból. A helyreállítás a helyszíni virtuális gép megszakítása nélkül is elvégezhető. Az Azure SQL Database és az Oracle adatbázisok időponthoz képest helyreállítást is elvégezhet. A Veeam és a StorSimple gyorsan és egyszerűen teszi lehetővé az Azure-ból való elemszintű helyreállítás folyamatát. A visszaállítás végrehajtásáról a Veeam dokumentációjában olvashat:

- [Exchange-kiszolgáló esetén](https://www.veeam.com/microsoft-exchange-recovery.html)
- [Active Directory esetén](https://www.veeam.com/microsoft-active-directory-explorer.html)
- [SQL Server esetén](https://www.veeam.com/microsoft-sql-server-explorer.html)
- [SharePoint esetén](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Az [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html) számára


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvétel és vészhelyreállítás

> [!NOTE]
> A biztonsági mentési célforgatókönyvek, StorSimple Cloud Appliance nem támogatott visszaállítási célként.

A katasztrófát számos tényező okozhatja. Az alábbi táblázat a gyakori vész-helyreállítási forgatókönyveket sorolja fel.

| Forgatókönyv | Hatás | Hogyan lehet visszaállítani | Megjegyzések |
|---|---|---|---|
| StorSimple eszközhiba | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a meghibásodott eszközt, és hajtsa végre a [StorSimple feladatátvételt és a vészhelyreállítást.](storsimple-device-failover-disaster-recovery.md) | Ha az eszköz helyreállítása után kell végrehajtania a visszaállítást, a teljes adatkészletek lekérése a felhőből az új eszközre történik. Minden művelet felhősebességen van. Az index- és katalógus-újraszkennelési folyamat az összes biztonságimásolat-készlet beolvasását és lekérése a felhőrétegből a helyi eszközszintre eredményezheti, ami időigényes folyamat lehet. |
| A Veeam kiszolgáló hibája | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Építse újra a biztonsági másolat kiszolgálóját, és végezze el az adatbázis-visszaállítást a [Veeam Súgóban (Műszaki dokumentáció)](https://www.veeam.com/documentation-guides-datasheets.html)részletezett módon.  | Újra kell építenie vagy vissza kell állítania a Veeam-kiszolgálót a vész-helyreállítási helyen. Az adatbázis visszaállítása a legutóbbi pontra. Ha a visszaállított Veeam-adatbázis nincs szinkronban a legújabb biztonsági mentési feladatokkal, indexelésre és katalogizálásra van szükség. Ez az index- és katalógus-újraszkennelési folyamat az összes biztonságimásolat-készlet beolvasását és lekérése a felhőrétegből a helyi eszközszintre eredményezheti. Ez még inkább időigényessé teszi. |
| Helyhiba, amely a biztonsági mentési kiszolgáló és a StorSimple elvesztését eredményezi | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple-t, majd állítsa vissza a Veeam-ot. | Először állítsa vissza a StorSimple-t, majd állítsa vissza a Veeam-ot. Ha az eszköz helyreállítása után kell elvégeznie a visszaállítást, a teljes adatkészletek lekérése a felhőből az új eszközre történik. Minden művelet felhősebességen van. |


## <a name="references"></a>Referencia

A következő dokumentumokra hivatkoztak ebben a cikkben:

- [StorSimple többutas I/O-beállítás](storsimple-configure-mpio-windows-server.md)
- [Tárolási forgatókönyvek: Vékony kiépítés](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT-meghajtók használata](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Árnyékmásolatok beállítása megosztott mappákhoz](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- További információ a [biztonsági másolat készletből történő visszaállításról.](storsimple-restore-from-backup-set-u2.md)
- További információ az [eszközfeladat-átvétel és](storsimple-device-failover-disaster-recovery.md)a vészhelyreállítás végrehajtásáról.
