---
title: StorSimple 8000 sorozat biztonsági mentési cél a Backup Exec | Microsoft dokumentumok
description: A StorSimple biztonsági mentési célkonfigurációját ismerteti a Veritas Backup Exec alkalmazással.
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
ms.author: matd
ms.openlocfilehash: 4dcda65384190050e11f1bf9b15c706b0e38c6b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561643"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple, mint egy biztonsági mentési cél Backup Exec

## <a name="overview"></a>Áttekintés

Az Azure StorSimple a Microsoft hibrid felhőalapú tárolási megoldása. A StorSimple az exponenciális adatnövekedés összetettségét kezeli azáltal, hogy egy Azure-tárfiókot használ a helyszíni megoldás kiterjesztéseként, és automatikusan rétegezi az adatokat a helyszíni és a felhőalapú tárolás között.

Ebben a cikkben a StorSimple integrációt tárgyaljuk a Veritas Backup Exec-el, és a két megoldás integrálásának ajánlott eljárásait. Javaslatokat teszünk arra vonatkozóan is, hogyan állíthatja be a Backup Exec-et a StorSimple legjobb integrálására. A Veritas ajánlott eljárásokat, biztonsági mentési építészeket és rendszergazdákat elhalasztjuk a Biztonsági mentési exec beállításának legjobb módja érdekében, hogy megfeleljen az egyéni biztonsági mentési követelményeknek és a szolgáltatásiszint-szerződéseknek (SLA).

Bár bemutatjuk a konfigurációs lépéseket és a legfontosabb fogalmakat, ez a cikk semmiképpen sem lépésenként konfigurációs vagy telepítési útmutató. Feltételezzük, hogy az alapvető összetevők és az infrastruktúra üzemképes, és készen áll, hogy támogassa az általunk leírt fogalmakat.

### <a name="who-should-read-this"></a>Kinek kellene ezt elolvasnia?

A cikkben található információk a tárolást, a Windows Server 2012 R2, az Ethernet, a felhőszolgáltatások és a Biztonsági másolat készítő rendszergazdái, a tárolási rendszergazdák és a biztonsági mentési rendszergazdák számára lesznek a leghasznosabbak.

## <a name="supported-versions"></a>Támogatott verziók

-   [Exec 16-os és újabb verziók biztonsági mentése](https://www.veritas.com/content/support/en_US/article.100040087)
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
![StorSimple rétegezési diagram](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

| Tárkapacitás       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple elsődleges biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Backup Exec telepítése.

Minden lépést részletesen tárgyalunk a következő szakaszokban.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple az Azure-felhővel integrált megoldás, a StorSimple aktív és működő kapcsolatot igényel az Azure-felhővel. Ez a kapcsolat olyan műveletekhez használatos, mint a felhőbeli pillanatképek, a felügyelet és a metaadat-átvitel, valamint a régebbi, kevésbé elérhető adatok azure-beli felhőalapú tárhelyre való rétegezése.

A megoldás optimális végrehajtásához azt javasoljuk, hogy kövesse az alábbi ajánlott hálózati eljárásokat:

-   A StorSimple rétegezést az Azure-hoz csatlakoztató hivatkozásnak meg kell felelnie a sávszélesség-követelményeknek. Ennek elérése érdekében alkalmazza a szükséges Szolgáltatásminőségi (QoS) szintet az infrastruktúra-kapcsolókra, hogy megfeleljen az RPO és a helyreállítási idő célkitűzésének (RTO) SLA-inak.
-   Az Azure Blob-tároló késése iményének körülbelül 80 ms-nak kell lennie.

### <a name="deploy-storsimple"></a>StorSimple telepítése

A StorSimple központi telepítési útmutatóját a [helyszíni StorSimple-eszköz üzembe helyezése.](storsimple-deployment-walkthrough-u2.md)

### <a name="deploy-backup-exec"></a>Backup Exec telepítése

A Backup Exec telepítésével kapcsolatos gyakorlati tanácsok a [Biztonsági másolat készítéssel kapcsolatos gyakorlati tanácsok című](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464)témakörben található.

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

- Ne használjon átnyúló köteteket (amelyeket a Windows Lemezkezelés hozott létre). A átnyúló lemezek nem támogatottak.
- A kötetek 64 KB-os lefoglalási mérettel rendelkező NTFS-sel formázzák.
- A StorSimple kötetek hozzárendelése közvetlenül a Backup Exec kiszolgálóra.
    - Használja az iSCSI-t fizikai kiszolgálókhoz.
    - A virtuális kiszolgálók átmenő lemezeit használja.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Gyakorlati tanácsok a StorSimple és a Backup Exec számára

Állítsa be a megoldást a következő szakaszokban található irányelveknek megfelelően.

### <a name="operating-system-best-practices"></a>Az operációs rendszerrel kapcsolatos gyakorlati tanácsok

- Tiltsa le a Windows Server titkosítást és a deduplikációt az NTFS fájlrendszerben.
- Tiltsa le a Windows Server töredezettségmentesítését a StorSimple köteteken.
- Tiltsa le a Windows Server indexelését a StorSimple köteteken.
- Futtasson víruskereső vizsgálatot a forrásállomáson (ne a StorSimple köteteken).
- Kapcsolja ki a Windows Server alapértelmezett [karbantartását](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) a Feladatkezelőben. Ezt az alábbi módokon tegye:
  - Kapcsolja ki a Karbantartás konfigurátort a Windows Feladatütemezőben.
  - Töltse le a [PsExec-et](https://technet.microsoft.com/sysinternals/bb897553.aspx) a Windows Sysinternals rendszerről. A PsExec letöltése után futtassa az Azure PowerShellt rendszergazdaként, és írja be a következőt:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple gyakorlati tanácsok

  -   Győződjön meg arról, hogy a StorSimple eszköz [3-as vagy újabb frissítésre](storsimple-install-update-3.md)frissül.
  -   Az iSCSI és a felhőforgalom elkülönítése. A StorSimple és a biztonsági másolat-kiszolgáló közötti forgalomhoz használjon dedikált iSCSI-kapcsolatokat.
  -   Győződjön meg arról, hogy a StorSimple-eszköz egy dedikált biztonsági mentési cél. A vegyes számítási feladatok nem támogatottak, mert hatással vannak az RTO-ra és az RPO-ra.

### <a name="backup-exec-best-practices"></a>A Backup Exec gyakorlati tanácsok

-   A Backup Exec-et a kiszolgáló helyi meghajtójára kell telepíteni, nem pedig StorSimple kötetre.
-   Állítsa be a Backup Exec storage **egyidejű írási műveleteit** a megengedett maximális értékre.
    -   Állítsa a Biztonsági másolat exec **tárolóblokkját és pufferméretét** 512 KB-ra.
    -   Kapcsolja be a Backup Exec tároló **pufferelt olvasási és írási**.
-   A StorSimple támogatja a Backup Exec teljes és növekményes biztonsági mentéseket. Azt javasoljuk, hogy ne használjon szintetikus és különbözeti biztonsági mentéseket.
-   A biztonsági másolat adatfájljai csak egy adott feladatadatait tartalmazhatják. Például nem média hozzáfűzések különböző feladatok között engedélyezettek.
-   A feladatellenőrzés letiltása. Szükség esetén az ellenőrzést a legutóbbi biztonsági mentési feladat után kell ütemezni. Fontos megérteni, hogy ez a feladat hatással van a biztonsági mentési ablak.
-   Válassza **a Tároló** > **a lemez** > **részleteinek** > **tulajdonságai lehetőséget.** Kapcsolja ki **a lemezterület lefoglalása előtti kifoglalást.**

A biztonsági mentési exec legújabb beállításait és a követelmények megvalósításával kapcsolatos gyakorlati tanácsokat [a Veritas webhelyén találja.](https://www.veritas.com)

## <a name="retention-policies"></a>Adatmegőrzési házirendek

Az egyik leggyakoribb biztonsági mentési adatmegőrzési házirend-típusok egy nagyapa, apa és a fiú (GFS) házirend. A GFS-házirendben a növekményes biztonsági mentés naponta történik, és a teljes biztonsági mentések hetente és havonta történik. Ez a házirend hat StorSimple rétegzett kötetet eredményez. Egy kötet tartalmazza a heti, havi és éves teljes biztonsági mentéseket. A másik öt kötet tárolja a napi növekményes biztonsági mentéseket.

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

## <a name="set-up-backup-exec-storage"></a>Backup Exec storage beállítása

### <a name="to-set-up-backup-exec-storage"></a>A Backup Exec storage beállítása

1.  A Backup Exec felügyeleti konzolon válassza a **Storage** > **Configure Storage** > **Disk-Based Storage** > **Next**lehetőséget.

    ![Backup Exec felügyeleti konzol, tárolólap konfigurálása](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Válassza **a Lemeztárolás**lehetőséget, majd a **Tovább**gombot.

    ![Backup Exec felügyeleti konzol, válassza tárolási lap](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Adjon meg egy képviselő nevet, például **a Szombat teljes**és a Leírást. Válassza a **Tovább lehetőséget.**

    ![Backup Exec felügyeleti konzol, név és leírás lap](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Jelölje ki azt a lemezt, amelyen létre szeretné hozni a lemeztároló eszközt, majd válassza a **Tovább**gombot.

    ![Backup Exec felügyeleti konzol, a tárolólemez kiválasztása lap](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Az írási műveletek számát **16-ra**kell számozni, majd válassza a **Tovább**gombot.

    ![Backup Exec felügyeleti konzol, egyidejű írási műveletek beállításai lap](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Tekintse át a beállításokat, majd válassza a **Befejezés gombot.**

    ![Backup Exec felügyeleti konzol, tárolási konfiguráció összegzési lap](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Minden kötet-hozzárendelés végén módosítsa a tárolóeszköz beállításait úgy, hogy azok megfeleljenek a [StorSimple és a Backup Exec ajánlott eljárásoknak.](#best-practices-for-storsimple-and-backup-exec)

    ![Backup Exec felügyeleti konzol, tárolóeszköz-beállítások lap](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Ismételje meg az 1-7.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>A StorSimple beállítása elsődleges biztonsági mentési célként

> [!NOTE]
> A felhőbe rétegzett biztonsági másolatból származó adat-visszaállítás felhősebességen történik.

Az alábbi ábra egy tipikus kötet biztonsági mentési feladathoz való hozzárendelését mutatja be. Ebben az esetben az összes heti biztonsági mentések leképezése a szombati teljes lemezre, és a növekményes biztonsági mentések leképezése hétfőtől péntekig növekményes lemezek. Az összes biztonsági mentések és visszaállítások egy StorSimple rétegzett kötetről.

![Elsődleges biztonsági mentési cél konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple elsődleges tartalék cél GFS ütemezés példa

Íme egy példa a GFS rotációs ütemezésnégy hét, havonta és évente:

| Gyakoriság/biztonsági mentés típusa | Összes | Növekményes (1-5. nap)  |   
|---|---|---|
| Heti (1-4. hét) | Szombat | Hétfő-Péntek |
| Havonta  | Szombat  |   |
| Éves | Szombat  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple kötetek hozzárendelése biztonsági mentési exec biztonsági mentési feladathoz

A következő sorozat feltételezi, hogy a Backup Exec és a célállomás a Biztonsági másolat készítő konszernek irányelveinek megfelelően van konfigurálva.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple kötetek hozzárendelése biztonsági mentési exec biztonsági mentési feladathoz

1.  A Backup Exec felügyeleti konzolon válassza a **Host** > **Backup Backup** > to**Disk (Biztonsági másolat lemezre) lehetőséget.**

    ![Exec felügyeleti konzol biztonsági mentése, állomás, biztonsági mentés és lemezre való biztonsági mentés kiválasztása](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  A **Biztonsági másolat definíciójának tulajdonságai** párbeszédpanel **Biztonsági másolat csoportban**válassza a **Szerkesztés**lehetőséget.

    ![Backup Exec felügyeleti konzol, Biztonsági másolat definíciójának tulajdonságai párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Állítsa be a teljes és növekményes biztonsági mentéseket úgy, hogy azok megfeleljenek az RPO és RTO követelményeinek, és megfeleljenek a Veritas ajánlott eljárásoknak.

4.  A **Biztonsági másolat beállításai** párbeszédpanelen válassza a **Tárolás**lehetőséget.

    ![Backup Exec felügyeleti konzol, Biztonsági másolat beállításai tároló párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Rendelje hozzá a megfelelő StorSimple köteteket a biztonsági mentési ütemezéshez.

    > [!NOTE]
    > **A tömörítés** és **a titkosítás típusa** **Nincs.**

6.  **Az Ellenőrzés csoportban**jelölje be a **Feladat adatainak ellenőrzésének nem ellenőrzése** jelölőnégyzetet. Ezzel a beállítással hatással lehet a StorSimple rétegezés.

    > [!NOTE]
    > Töredezettségmentesítés, indexelés és a háttér ellenőrzése negatívan befolyásolja a StorSimple rétegezés.

    ![Backup Exec felügyeleti konzol, Biztonsági mentési beállítások ellenőrzés beállításai](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Ha a többi biztonsági mentési lehetőséget úgy állította be, hogy megfeleljen a követelményeknek, válassza az **OK** gombot a befejezéshez.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési célként

> [!NOTE]
>A felhőbe rétegzett biztonsági másolatból származó adat-visszaállítások felhősebességen történnek.

Ebben a modellben ideiglenes gyorsítótárként való kiszolgálóadathordozóval (a StorSimple-től eltérő) kell rendelkeznie. Használhatja például a raid-kötetek redundáns tömbjét a hely, a bemeneti/kimeneti (I/O) és a sávszélesség befogadására. Javasoljuk a RAID 5, 50 és 10 használatát.

Az alábbi ábra a tipikus rövid távú megőrzési helyi (a kiszolgálóra) köteteket és a hosszú távú adatmegőrzési archívumok köteteit mutatja be. Ebben az esetben az összes biztonsági mentés a helyi (a kiszolgálóra) RAID-köteten fut. Ezeket a biztonsági másolatokat a rendszer rendszeresen megkettőzi és archiválja egy archívumkötetre. Fontos, hogy méretezze a helyi (a kiszolgálóra) RAID kötetet, hogy képes legyen kezelni a rövid távú megőrzési kapacitást és a teljesítménykövetelményeket.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple másodlagos tartalék célGFS példa

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Az alábbi táblázat bemutatja, hogyan állíthatja be a biztonsági másolatokat a helyi és a StorSimple lemezeken való futtatáshoz. Ez magában foglalja az egyéni és a teljes kapacitásra vonatkozó követelményeket.

### <a name="backup-configuration-and-capacity-requirements"></a>Biztonsági mentés konfigurációs és kapacitási követelményei

| Biztonsági másolat típusa és megőrzése | Konfigurált tároló | Méret (TiB) | GFS szorzó | Teljes\* kapacitás (TiB) |
|---|---|---|---|---|
| 1. hét (teljes és növekményes) |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| StorSimple hét 2-4 |StorSimple lemez (hosszú távú) | 1 | 4 | 4 |
| Havi teljes |StorSimple lemez (hosszú távú) | 1 | 12 | 12 |
| Éves encika |StorSimple lemez (hosszú távú) | 1 | 1 | 1 |
|GFS-mennyiségek méretére vonatkozó követelmény |  |  |  | 18*|

\*A teljes kapacitás 17 TiB StorSimple lemezt és 1 TiB helyi RAID kötetet tartalmaz.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS példa ütemezése: GFS rotációs heti, havi és éves ütemezés

| Hét | Összes | Növekményes nap 1 | 2. növekményes nap | Növekményes nap 3 | 4. nap | Növekményes nap 5 |
|---|---|---|---|---|---|---|
| 1. hét | Helyi RAID-hangerő  | Helyi RAID-hangerő | Helyi RAID-hangerő | Helyi RAID-hangerő | Helyi RAID-hangerő | Helyi RAID-hangerő |
| 2. hét | StorSimple hét 2-4 |   |   |   |   |   |
| 3. hét | StorSimple hét 2-4 |   |   |   |   |   |
| 4. hét | StorSimple hét 2-4 |   |   |   |   |   |
| Havonta | StorSimple havonta |   |   |   |   |   |
| Éves | StorSimple évente  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>StorSimple kötetek hozzárendelése biztonsági mentési exec-archívumhoz és deduplikációs feladathoz

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>StorSimple kötetek hozzárendelése biztonsági mentési exec-archívumhoz és másolási feladathoz

1.  A Backup Exec felügyeleti konzolon kattintson a jobb gombbal arra a feladatra, amelyet archiválni szeretne egy StorSimple kötetre, majd válassza a **Biztonsági másolat definíciója tulajdonságainak** > **szerkesztése parancsot.**

    ![Backup Exec felügyeleti konzol, Biztonsági másolat definíciójának tulajdonságai lap](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Válassza **a Szakaszmásolat** > hozzáadása**a Lemezszerkesztéshez** > **Edit**lehetőséget.

    ![Backup Exec felügyeleti konzol, színpad hozzáadása](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Az **Ismétlődő beállítások** párbeszédpanelen jelölje ki a **Forrás** és **az Ütemezés**mezőben használni kívánt értékeket.

    ![Exec felügyeleti konzol biztonsági mentése, biztonsági mentési definíciók tulajdonságai és duplikált beállítások](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  A **Storage** legördülő listában válassza ki azt a StorSimple kötetet, amelyben az archiválási feladat nak tárolnia kell az adatokat.

    ![Exec felügyeleti konzol biztonsági mentése, biztonsági mentési definíciók tulajdonságai és duplikált beállítások](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Jelölje **be az Ellenőrzés**jelölőnégyzetet, majd jelölje be a Ne ellenőrizze az **adatokat ehhez a feladathoz** jelölőnégyzetet.

    ![Exec felügyeleti konzol biztonsági mentése, biztonsági mentési definíciók tulajdonságai és duplikált beállítások](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Válassza **az OK gombot.**

    ![Exec felügyeleti konzol biztonsági mentése, biztonsági mentési definíciók tulajdonságai és duplikált beállítások](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  A **Biztonsági másolat** oszlopban adjon hozzá egy új szakaszt. A forráshoz használja a **növekményes**. A cél, válassza ki a StorSimple kötet, ahol a növekményes biztonsági mentési feladat archiválva van. Ismételje meg az 1-6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple felhőbeli pillanatképek

A StorSimple felhőbeli pillanatképei védik a StorSimple-eszközben található adatokat. A felhőbeli pillanatkép létrehozása egyenértékű a helyi biztonsági mentési szalagok külső létesítménybe történő szállításával. Ha az Azure georedundáns tárolás, hozzon létre egy felhőbeli pillanatkép egyenértékű a szállítási biztonsági mentési szalagok több helyen. Ha egy katasztrófa után vissza kell állítania egy eszközt, előfordulhat, hogy egy másik StorSimple-eszközt hoz online állapotba, és feladatátvételt végez. A feladatátvétel után a legutóbbi felhőbeli pillanatképből (felhősebességen) hozzáférhet az adatokhoz.

A következő szakasz ismerteti, hogyan hozhat létre egy rövid parancsfájlt a StorSimple felhőbeli pillanatképek indításához és törléséhez a biztonsági mentés utófeldolgozása során.

> [!NOTE]
> Manuálisan vagy programozott módon létrehozott pillanatképek nem követik a StorSimple pillanatkép lejárati szabályzatát. Ezeket a pillanatképeket manuálisan vagy programozott módon törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Felhőbeli pillanatképek indítása és törlése parancsfájl használatával

> [!NOTE]
> A StorSimple-pillanatkép törlése előtt gondosan értékelje a megfelelőségi és adatmegőrzési következményeket. A biztonsági mentés utáni parancsfájlok futtatásáról a Biztonsági másolat dokumentációjában olvashat [bővebben.](https://www.veritas.com/support/en_US/article.100032497.html)

### <a name="backup-lifecycle"></a>Biztonsági mentés életciklusa

![Biztonsági mentés életciklus-diagramja](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Adja hozzá a parancsfájlt a Backup Exec biztonsági mentési feladatához a Backup Exec feladat beállításainak előfeldolgozási és utófeldolgozási parancsainak szerkesztésével.

   ![Backup Exec konzol, biztonsági mentési lehetőségek, elő- és utófeldolgozási parancsok lap](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Azt javasoljuk, hogy futtassa a StorSimple felhőbeli pillanatkép biztonsági mentési szabályzatot utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. Ha további információra van szüksége arról, hogyan készíthet biztonsági mentést és visszaállítást a biztonsági mentési alkalmazáskörnyezetről, hogy könnyebben megfeleljen az RPO-nak és az RTO-nak, kérjük, forduljon a biztonsági mentési tervezőhöz.

## <a name="storsimple-as-a-restore-source"></a>StorSimple mint visszaállítási forrás

A StorSimple-eszközről történő visszaállítás a blokktároló eszközről történő visszaállításhoz hasonlóan működik. A felhőbe rétegzett adatok visszaállítása felhősebességen történik. A helyi adatok visszaállítása az eszköz helyi lemezsebességén. A visszaállítás végrehajtásáról a Biztonsági másolat segédprogram dokumentációjában olvashat. Azt javasoljuk, hogy megfeleljen a Backup Exec visszaállítási gyakorlati tanácsoknak.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvétel és vészhelyreállítás

> [!NOTE]
> A biztonsági mentési célforgatókönyvek, StorSimple Cloud Appliance nem támogatott visszaállítási célként.

A katasztrófát számos tényező okozhatja. Az alábbi táblázat a gyakori vész-helyreállítási forgatókönyveket sorolja fel.

| Forgatókönyv | Hatás | Hogyan lehet visszaállítani | Megjegyzések |
|---|---|---|---|
| StorSimple eszközhiba | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a meghibásodott eszközt, és hajtsa végre a [StorSimple feladatátvételt és a vészhelyreállítást.](storsimple-device-failover-disaster-recovery.md) | Ha az eszköz helyreállítása után kell végrehajtania a visszaállítást, a teljes adatkészletek lekérése a felhőből az új eszközre történik. Minden művelet felhősebességen van. Az indexelési és katalogizálási újraszkennelési folyamat az összes biztonságimásolat-készlet beolvasását és lekérése a felhőrétegből a helyi eszközszintre eredményezheti, ami időigényes folyamat lehet. |
| Exec kiszolgáló biztonsági mentése hiba | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Építse újra a biztonsági másolat kiszolgálóját, és végezze el az adatbázis-visszaállítást a részletesen a [Biztonsági másolat készítése exec (BEDB) adatbázis kézi biztonsági mentése és visszaállítása című részében.](http://www.veritas.com/docs/000041083) | Újra kell építenie vagy vissza kell állítania a Biztonsági másolat készítő kiszolgálót a vész-helyreállítási helyen. Az adatbázis visszaállítása a legutóbbi pontra. Ha a visszaállított Biztonsági másolat exec adatbázisa nincs szinkronban a legújabb biztonsági mentési feladatokkal, indexelésre és katalogizálásra van szükség. Ez az index- és katalógus-újraszkennelési folyamat az összes biztonságimásolat-készlet beolvasását és lekérése a felhőrétegből a helyi eszközszintre eredményezheti. Ez még inkább időigényessé teszi. |
| Helyhiba, amely a biztonsági mentési kiszolgáló és a StorSimple elvesztését eredményezi | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple alkalmazást, majd állítsa vissza a Backup Exec segédprogramot. | Először állítsa vissza a StorSimple alkalmazást, majd állítsa vissza a Backup Exec segédprogramot. Ha az eszköz helyreállítása után kell elvégeznie a visszaállítást, a teljes adatkészletek lekérése a felhőből az új eszközre történik. Minden művelet felhősebességen van. |

## <a name="references"></a>Referencia

A következő dokumentumokra hivatkoztak ebben a cikkben:

- [StorSimple többutas I/O-beállítás](storsimple-configure-mpio-windows-server.md)
- [Tárolási forgatókönyvek: Vékony kiépítés](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT-meghajtók használata](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Árnyékmásolatok beállítása megosztott mappákhoz](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- További információ a [biztonsági másolat készletből történő visszaállításról.](storsimple-restore-from-backup-set-u2.md)
- További információ az [eszközfeladat-átvétel és](storsimple-device-failover-disaster-recovery.md)a vészhelyreállítás végrehajtásáról.
