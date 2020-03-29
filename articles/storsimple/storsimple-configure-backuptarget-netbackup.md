---
title: StorSimple 8000 sorozat biztonsági mentési célként a NetBackup segítségével | Microsoft dokumentumok
description: A StorSimple biztonsági mentési célkonfigurációját ismerteti a Veritas NetBackup szolgáltatással.
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
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876208"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple tartalék célként a NetBackup segítségével

## <a name="overview"></a>Áttekintés

Az Azure StorSimple a Microsoft hibrid felhőalapú tárolási megoldása. A StorSimple az exponenciális adatnövekedés összetettségét kezeli azáltal, hogy egy Azure-tárfiókot használ a helyszíni megoldás kiterjesztéseként, és automatikusan rétegezi az adatokat a helyszíni és a felhőalapú tárolás között.

Ebben a cikkben a Veritas NetBackup storSimple-integrációját, valamint a két megoldás integrálásának ajánlott eljárásait tárgyaljuk. Javaslatokat teszünk arra vonatkozóan is, hogyan állíthatja be a Veritas NetBackup-ot a StorSimple legjobb integrálására. A Veritas ajánlott eljárásokat, biztonsági mentési építészeket és rendszergazdákat elhalasztjuk, hogy a Veritas NetBackup az egyéni biztonsági mentési követelményeknek és szolgáltatásiszintű szerződéseknek (SLA) megfeleljen.

Bár bemutatjuk a konfigurációs lépéseket és a legfontosabb fogalmakat, ez a cikk semmiképpen sem lépésenként konfigurációs vagy telepítési útmutató. Feltételezzük, hogy az alapvető összetevők és az infrastruktúra üzemképes, és készen áll, hogy támogassa az általunk leírt fogalmakat.

### <a name="who-should-read-this"></a>Kinek kellene ezt elolvasnia?

A cikkben található információk a tárolást, a Windows Server 2012 R2, az Ethernet, a felhőszolgáltatások és a Veritas NetBackup szolgáltatást ismerő biztonsági mentési rendszergazdák, tárolási rendszergazdák és tárolótervezők számára lesznek a leghasznosabbak.

### <a name="supported-versions"></a>Támogatott verziók

-   NetBackup 7.7.x és újabb verziók
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
![StorSimple rétegezési diagram](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

![StorSimple elsődleges biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Elsődleges célbiztonsági mentés logikai lépései

1.  A biztonsági másolat kiszolgálója kapcsolatba lép a célbiztonsági másolat ügynökével, és a biztonságimásolat-készítő ügynök adatokat továbbít a biztonsági másolat kiszolgálójára.
2.  A biztonsági másolat kiszolgáló adatokat ír a StorSimple rétegzett kötetek.
3.  A biztonságimásolat-kiszolgáló frissíti a katalógusadatbázist, majd befejezi a biztonsági mentési feladatot.
4.  A pillanatkép-parancsfájl elindítja a StorSimple pillanatkép-kezelő (start vagy törlés).
5.  A biztonsági másolat kiszolgáló adatmegőrzési házirend alapján törli a lejárt biztonsági mentéseket.

### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállítási logikai lépései

1.  A biztonsági másolat kiszolgálója elindítja a megfelelő adatok visszaállítását a tárolótárból.
2.  A biztonságimásolat-készítő ügynök fogadja az adatokat a biztonsági másolat kiszolgálójáról.
3.  A biztonsági másolat kiszolgálója befejezi a visszaállítási feladatot.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple másodlagos biztonsági mentési célként

Ebben a forgatókönyvben a StorSimple kötetek elsősorban hosszú távú megőrzési vagy archiválási.

Az alábbi ábrán egy architektúra látható, amelyben a kezdeti biztonsági mentések és visszaállítások nagy teljesítményű kötetet céloznak meg. Ezek a biztonsági mentések másolása és archiválása egy StorSimple rétegzett kötet egy meghatározott ütemezés szerint.

Fontos, hogy méretezze a nagy teljesítményű kötetet, hogy kezelni tudja az adatmegőrzési házirend kapacitását és teljesítménykövetelményeit.

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Másodlagos célbiztonsági mentés logikai lépései

1.  A biztonsági másolat kiszolgálója kapcsolatba lép a célbiztonsági másolat ügynökével, és a biztonságimásolat-készítő ügynök adatokat továbbít a biztonsági másolat kiszolgálójára.
2.  A biztonsági másolat kiszolgálója adatokat ír a nagy teljesítményű tárolóba.
3.  A biztonságimásolat-kiszolgáló frissíti a katalógusadatbázist, majd befejezi a biztonsági mentési feladatot.
4.  A biztonsági másolat kiszolgáló adatmásolatokat a StorSimple egy adatmegőrzési házirend alapján.
5.  A pillanatkép-parancsfájl elindítja a StorSimple pillanatkép-kezelő (start vagy törlés).
6.  A biztonsági másolat kiszolgáló adatmegőrzési házirend alapján törli a lejárt biztonsági másolatokat.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél-visszaállítási logikai lépések

1.  A biztonsági másolat kiszolgálója elindítja a megfelelő adatok visszaállítását a tárolótárból.
2.  A biztonságimásolat-készítő ügynök fogadja az adatokat a biztonsági másolat kiszolgálójáról.
3.  A biztonsági másolat kiszolgálója befejezi a visszaállítási feladatot.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A megoldás üzembe helyezése három lépést igényel:
1. Készítse elő a hálózati infrastruktúrát.
2. Telepítse a StorSimple-eszközt biztonsági mentési célként.
3. Telepítse a Veritas NetBackup-ot.

Minden lépést részletesen tárgyalunk a következő szakaszokban.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple az Azure-felhővel integrált megoldás, a StorSimple aktív és működő kapcsolatot igényel az Azure-felhővel. Ez a kapcsolat olyan műveletekhez használatos, mint a felhőbeli pillanatképek, az adatkezelés és a metaadat-átvitel, valamint a régebbi, kevésbé elérhető adatok azure-beli felhőalapú tárhelyre való rétegezése.

A megoldás optimális végrehajtásához azt javasoljuk, hogy kövesse az alábbi ajánlott hálózati eljárásokat:

-   A StorSimple rétegezést az Azure-hoz csatlakoztató hivatkozásnak meg kell felelnie a sávszélesség-követelményeknek. Ennek elérése érdekében alkalmazza a megfelelő szolgáltatásminőségi (QoS) szintet az infrastruktúra-kapcsolókra, hogy megfeleljen az RPO és a helyreállítási idő célkitűzésének (RTO) SLA-knak.

-   Az Azure Blob-tároló késése iményének körülbelül 80 ms-nak kell lennie.

### <a name="deploy-storsimple"></a>StorSimple telepítése

A StorSimple központi telepítési útmutatója a [helyszíni StorSimple-eszköz üzembe helyezése.](storsimple-deployment-walkthrough-u2.md)

### <a name="deploy-netbackup"></a>NetBackup telepítése

A NetBackup 7.7.x telepítési útmutatóját a [NetBackup 7.7.x dokumentációjában találja.](http://www.veritas.com/docs/000094423)

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

- Ne használjon átnyúló köteteket (amelyeket a Windows Lemezkezelés hozott létre); átnyúló kötetek nem támogatottak.
- A kötetek 64 KB-os lefoglalási mérettel rendelkező NTFS-sel formázzák.
- Rendelje hozzá a StorSimple köteteket közvetlenül a NetBackup kiszolgálóhoz.
    - Használja az iSCSI-t fizikai kiszolgálókhoz.
    - A virtuális kiszolgálók átmenő lemezeit használja.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Gyakorlati tanácsok a StorSimple és a NetBackup alkalmazáshoz

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

### <a name="netbackup-best-practices"></a>Gyakorlati tanácsok a NetBackup biztonsági mentése

-   A NetBackup-adatbázisnak a kiszolgálón kell lennie, és nem StorSimple-köteten kell lennie.
-   Vészhelyreállításhoz készítsen biztonsági másolatot a NetBackup adatbázisról egy StorSimple köteten.
-   Ehhez a megoldáshoz támogatjuk a NetBackup teljes és növekményes biztonsági mentését (más néven különbözeti növekményes biztonsági mentéseket a NetBackup-ban). Azt javasoljuk, hogy ne használjon szintetikus és kumulatív növekményes biztonsági mentések.
-   A biztonsági másolat adatfájljai csak egy adott feladat adatait tartalmazhatják. Például nem média hozzáfűzések különböző feladatok között engedélyezettek.

A NetBackup legújabb beállításait és a követelmények megvalósításával kapcsolatos gyakorlati tanácsokat a Www.veritas.com NetBackup dokumentációjában [találja.](https://www.veritas.com)


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

## <a name="set-up-netbackup-storage"></a>A NetBackup tároló beállítása

### <a name="to-set-up-netbackup-storage"></a>A NetBackup tároló beállítása

1.  A NetBackup felügyeleti konzolon válassza a **Média- és eszközkezelő** > **eszközök** > **lemezkészleteit**. A Lemezkészlet konfigurálása varázslóban jelölje ki az **AdvancedDisk**tárolókiszolgáló típust, majd kattintson a **Tovább**gombra.

    ![NetBackup felügyeleti konzol, lemezkészlet-konfigurációs varázsló](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Jelölje ki a kiszolgálót, majd kattintson a **Tovább gombra.**

    ![NetBackup felügyeleti konzol, válassza ki a kiszolgálót](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Válassza ki a StorSimple kötetet.

    ![NetBackup felügyeleti konzol, válassza ki a StorSimple kötetlemezt](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Adja meg a biztonsági másolat céljának nevét, majd **a** > következő**gombra** a varázsló befejezéséhez.

5.  Tekintse át a beállításokat, majd válassza a **Befejezés gombot.**

6.  Minden kötet-hozzárendelés végén módosítsa a tárolóeszköz beállításait a [StorSimple és a NetBackup ajánlott eljárásokban](#best-practices-for-storsimple-and-netbackup)ajánlott módon.

7. Ismételje meg az 1-6.

    ![NetBackup felügyeleti konzol, lemezkonfiguráció](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>A StorSimple beállítása elsődleges biztonsági mentési célként

> [!NOTE]
> A felhőbe rétegzett biztonsági másolatból származó adat-visszaállítások felhősebességen történnek.

Az alábbi ábra egy tipikus kötet biztonsági mentési feladathoz való hozzárendelését mutatja be. Ebben az esetben az összes heti biztonsági mentések leképezése a szombati teljes lemezre, és a növekményes biztonsági mentések leképezése hétfőtől péntekig növekményes lemezek. Az összes biztonsági mentések és visszaállítások egy StorSimple rétegzett kötetről.

![Elsődleges biztonsági mentési cél konfigurációs logikai diagramja](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple elsődleges tartalék cél GFS ütemezés példa

Íme egy példa a GFS rotációs ütemezésnégy hét, havonta és évente:

| Gyakoriság/biztonsági mentés típusa | Összes | Növekményes (1-5. nap)  |   
|---|---|---|
| Heti (1-4. hét) | Szombat | Hétfő-Péntek |
| Havonta  | Szombat  |   |
| Éves | Szombat  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-kötetek hozzárendelése NetBackup biztonságimentési feladathoz

A következő sorozat feltételezi, hogy a NetBackup és a célállomás a NetBackup ügynök irányelveinek megfelelően van konfigurálva.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple kötetek hozzárendelése NetBackup biztonsági mentési feladathoz

1. A NetBackup felügyeleti konzolon válassza a **NetBackup-kezelés**lehetőséget, kattintson a jobb gombbal **a Házirendek**elemre, majd válassza az **Új házirend parancsot.**

   ![NetBackup felügyeleti konzol, új házirend létrehozása](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Az **Új házirend hozzáadása** párbeszédpanelen adja meg a házirend nevét, majd jelölje be a **Házirend-konfiguráció varázsló használata** jelölőnégyzetet. Válassza **az OK gombot.**

   ![NetBackup felügyeleti konzol, Új házirend hozzáadása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. A Biztonsági másolat készítése varázslóban válassza ki a kívánt biztonsági másolat típusát, majd kattintson a **Tovább**gombra.

   ![NetBackup felügyeleti konzol, válassza ki a biztonsági másolat típusát](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. A házirend típusának beállításához válassza a **Normál**lehetőséget, majd a **Tovább**gombot.

   ![NetBackup felügyeleti konzol, házirendtípus kiválasztása](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Jelölje be a gazdagépet, jelölje be az **Ügyfél operációs rendszerének észlelése** jelölőnégyzetet, majd a **Hozzáadás lehetőséget.** Válassza a **Tovább lehetőséget.**

   ![NetBackup felügyeleti konzol, ügyfelek listázása új házirendben](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Jelölje ki azokat a meghajtókat, amelyekről biztonsági másolatot szeretne tartani.

   ![NetBackup felügyeleti konzol, biztonsági mentés ia új házirend esetén](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Válassza ki a biztonsági mentési elforgatási követelményeknek megfelelő gyakorisági és megőrzési értékeket.

   ![NetBackup felügyeleti konzol, biztonsági mentés gyakorisága és rotációja egy új házirendhez](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Válassza a **Következő** > **befejezés** > **lehetőséget**.  Az ütemezést a házirend létrehozása után módosíthatja.

9. Jelölje be, ha ki szeretné bontani az imént létrehozott házirendet, majd válassza **az Ütemezések**lehetőséget.

   ![NetBackup felügyeleti konzol, új házirend ütemezése](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Kattintson a jobb gombbal **a Differential-Inc**elemre, válassza **a Másolás újra**parancsot, majd kattintson **az OK gombra.**

    ![NetBackup felügyeleti konzol, ütemezés másolása új házirendbe](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Kattintson a jobb gombbal az újonnan létrehozott ütemezésre, és válassza a **Módosítás parancsot.**

12. Az **Attribútumok** lapon jelölje be a **Házirend-tárhely kijelölésének felülbírálása** jelölőnégyzetet, majd jelölje be azt a kötetet, ahová a hétfői növekményes biztonsági mentések kerül.

    ![NetBackup felügyeleti konzol, ütemezés módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. A **Start ablak** lapon válassza ki a biztonsági mentések időablakát.

    ![NetBackup felügyeleti konzol, a kezdőablak módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Válassza **az OK gombot.**

15. Ismételje meg a 10-14. Válassza ki a megfelelő kötetet, és ütemezze az egyes létrehozott biztonsági mentéseket.

16. Kattintson a jobb gombbal a **Differential-Inc** ütemezésre, majd törölje azt.

17. Módosítsa a teljes ütemezést, hogy megfeleljen a biztonsági mentési igényeknek.

    ![NetBackup felügyeleti konzol, a teljes ütemezés módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Módosítsa a kezdőablakot.

    ![NetBackup felügyeleti konzol, a kezdőablak módosítása](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. A végleges menetrend így néz ki:

    ![NetBackup felügyeleti konzol, végleges ütemezés](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési célként

> [!NOTE]
>A felhőbe rétegzett biztonsági másolatból származó adat-visszaállítások felhősebességen történnek.

Ebben a modellben ideiglenes gyorsítótárként való kiszolgálóadathordozóval (a StorSimple-től eltérő) kell rendelkeznie. Használhatja például a raid-kötetek redundáns tömbjét a hely, a bemeneti/kimeneti (I/O) és a sávszélesség befogadására. Javasoljuk a RAID 5, 50 és 10 használatát.

Az alábbi ábra a tipikus rövid távú megőrzési helyi (a kiszolgálóra) köteteket és a hosszú távú adatmegőrzési archívumok köteteit mutatja be. Ebben az esetben az összes biztonsági mentés a helyi (a kiszolgálóra) RAID-köteten fut. Ezeket a biztonsági másolatokat a rendszer rendszeresen megkettőzi és archiválja egy archívumkötetre. Fontos, hogy méretezze a helyi (a kiszolgálóra) RAID kötetet, hogy képes legyen kezelni a rövid távú megőrzési kapacitást és a teljesítménykövetelményeket.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple másodlagos tartalék célGFS példa

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

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


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple kötetek hozzárendelése NetBackup archívumhoz és másolási feladathoz

Mivel a NetBackup a tárolási és médiakezelési lehetőségek széles skáláját kínálja, javasoljuk, hogy a tárolási életciklus-szabályzat (SLP) követelményeinek megfelelő felmérése érdekében konzultáljon a Veritas-szal vagy a NetBackup tervezőjével.

Miután definiálta a kezdeti lemezkészleteket, három további tárolási életciklus-szabályzatot kell definiálnia, összesen négy szabályzathoz:
* LocalRAIDKötet
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple kötetek hozzárendelése NetBackup archívumhoz és másolási feladathoz

1. A NetBackup felügyeleti konzolon válassza **a Tárolási** > **tárolási életciklus-házirendek** > **Új tárolási életciklus-szabályzat lehetőséget.**

   ![NetBackup felügyeleti konzol, új tárolási életciklus-házirend](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Adja meg a pillanatkép nevét, majd válassza a **Hozzáadás lehetőséget.**

3. Az **Új művelet** párbeszédpanel **Tulajdonságok** lapján a **Művelet**csoportban válassza a Biztonsági **másolat lehetőséget.** Válassza ki a **Céltároló**, **Megőrzési típus**és Megőrzési időszak kívánt **értékeit.** Válassza **az OK gombot.**

   ![NetBackup felügyeleti konzol, Új művelet párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Ez határozza meg az első biztonsági mentési műveletet és tárházat.

4. Jelölje be az előző művelet kiemeléséhez, majd válassza a **Hozzáadás lehetőséget.** A **Tárolási művelet módosítása** párbeszédpanelen jelölje ki a **Céltároló**, **Megőrzési típus**és Megőrzési időszak kívánt **értékeit.**

   ![NetBackup felügyeleti konzol,Tárolási művelet módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Jelölje be az előző művelet kiemeléséhez, majd válassza a **Hozzáadás lehetőséget.** Az **Új tárolási életciklus-házirend** párbeszédpanelen adjon hozzá havi biztonsági mentéseket egy évre.

   ![NetBackup felügyeleti konzol, Új tárolási életciklus-házirend párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Ismételje meg a 4-5.

   ![NetBackup felügyeleti konzol, Házirendek hozzáadása az Új tárolási életciklus-házirend párbeszédpanelen](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Ha befejezte az SLP-adatmegőrzési házirend definiálását, a **Házirend**csoportban definiáljon egy biztonsági mentési házirendet a [StorSimple-kötetek hozzárendelése a NetBackup biztonsági mentési feladathoz](#assigning-storsimple-volumes-to-a-netbackup-backup-job)című lépések végrehajtásával.

8. Az **Ütemezések csoportÜtemezés** **csoportban** kattintson a jobb gombbal a **Teljes**gombra, majd válassza a **Módosítás parancsot.**

   ![NetBackup felügyeleti konzol, Ütemezés módosítása párbeszédpanel](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Jelölje be a **Házirend-tárhely kijelölésének felülbírálása** jelölőnégyzetet, majd jelölje be az 1-6.

   ![NetBackup felügyeleti konzol, házirend-tárolókiválasztásának felülbírálása](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Válassza **az OK**gombot, majd ismételje meg a műveletet a növekményes biztonsági mentési ütemezésnél.

    ![NetBackup felügyeleti konzol, Ütemezés módosítása párbeszédpanel növekményes biztonsági mentések esetén](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Biztonsági másolat típusának megőrzése | Méret (TiB) | GFS szorzó\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Heti teljes |  1  |  4 | 4  |
| Napi növekményes  | 0,5  | 20 (a ciklusok megegyeznek a havi hetek számával) | 12 (2 kiegészítő kontingens esetén) |
| Havi teljes  | 1 | 12 | 12 |
| Éves encika | 1  | 10 | 10 |
| GFS-követelmény  |     |     | 38 |
| További kontingens  | 4  |    | 42 teljes GFS követelmény |

\*A GFS szorzó a biztonsági mentési házirend követelményeinek teljesítéséhez szükséges másolatok száma.

## <a name="storsimple-cloud-snapshots"></a>StorSimple felhőbeli pillanatképek

A StorSimple felhőbeli pillanatképei védik a StorSimple-eszközben található adatokat. A felhőbeli pillanatkép létrehozása egyenértékű a helyi biztonsági mentési szalagok külső létesítménybe történő szállításával. Ha az Azure georedundáns tárolás, hozzon létre egy felhőbeli pillanatkép egyenértékű a szállítási biztonsági mentési szalagok több helyen. Ha egy katasztrófa után vissza kell állítania egy eszközt, előfordulhat, hogy egy másik StorSimple-eszközt hoz online állapotba, és feladatátvételt végez. A feladatátvétel után a legutóbbi felhőbeli pillanatképből (felhősebességen) hozzáférhet az adatokhoz.

A következő szakasz ismerteti, hogyan hozhat létre egy rövid parancsfájlt a StorSimple felhőbeli pillanatképek indításához és törléséhez a biztonsági mentés utófeldolgozása során.

> [!NOTE]
> Manuálisan vagy programozott módon létrehozott pillanatképek nem követik a StorSimple pillanatkép lejárati szabályzatát. Ezeket a pillanatképeket manuálisan vagy programozott módon törölni kell.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Felhőbeli pillanatképek indítása és törlése parancsfájl használatával

> [!NOTE]
> A StorSimple-pillanatkép törlése előtt gondosan értékelje a megfelelőségi és adatmegőrzési következményeket. A biztonsági mentés utáni parancsfájlok futtatásáról a [NetBackup dokumentációjában](http://www.veritas.com/docs/000094423)olvashat bővebben.

### <a name="backup-lifecycle"></a>Biztonsági mentés életciklusa

![Biztonsági mentés életciklus-diagramja](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. Adja hozzá a parancsfájlt a biztonsági mentési feladathoz a NetBackup szolgáltatásban. Ehhez szerkesztheti a NetBackup feladatbeállítások előfeldolgozási és utófeldolgozási parancsait.

> [!NOTE]
> Azt javasoljuk, hogy futtassa a StorSimple felhőbeli pillanatkép biztonsági mentési szabályzatot utófeldolgozási parancsfájlként a napi biztonsági mentési feladat végén. Ha további információra van szüksége arról, hogyan készíthet biztonsági mentést és visszaállítást a biztonsági mentési alkalmazáskörnyezetről, hogy könnyebben megfeleljen az RPO-nak és az RTO-nak, kérjük, forduljon a biztonsági mentési tervezőhöz.

## <a name="storsimple-as-a-restore-source"></a>StorSimple mint visszaállítási forrás

A StorSimple-eszközről történő visszaállítás a blokktároló eszközről történő visszaállításhoz hasonlóan működik. A felhőbe rétegzett adatok visszaállítása felhősebességen történik. A helyi adatok visszaállítása az eszköz helyi lemezsebességén. A visszaállítás végrehajtásáról a [NetBackup dokumentációjában](http://www.veritas.com/docs/000094423)olvashat. Javasoljuk, hogy feleljen meg a NetBackup-visszaállítás ajánlott eljárásainak.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvétel és vészhelyreállítás

> [!NOTE]
> A biztonsági mentési célforgatókönyvek, StorSimple Cloud Appliance nem támogatott visszaállítási célként.

A katasztrófát számos tényező okozhatja. Az alábbi táblázat a gyakori vész-helyreállítási forgatókönyveket sorolja fel.

| Forgatókönyv | Hatás | Hogyan lehet visszaállítani | Megjegyzések |
|---|---|---|---|
| StorSimple eszközhiba | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a meghibásodott eszközt, és hajtsa végre a [StorSimple feladatátvételt és a vészhelyreállítást.](storsimple-device-failover-disaster-recovery.md) | Ha az eszköz helyreállítása után kell végrehajtania a visszaállítást, a teljes adatkészletek lekérése a felhőből az új eszközre történik. Minden művelet felhősebességen van. Az index- és katalógus-újraszkennelési folyamat az összes biztonságimásolat-készlet beolvasását és lekérése a felhőrétegből a helyi eszközszintre eredményezheti, ami időigényes folyamat lehet. |
| NetBackup kiszolgálóhibája | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Építse újra a biztonsági másolat kiszolgálóját, és hajtsa végre az adatbázis-visszaállítást. | Újra kell építenie vagy vissza kell állítania a NetBackup kiszolgálót a vész-helyreállítási helyen. Az adatbázis visszaállítása a legutóbbi pontra. Ha a visszaállított NetBackup adatbázis nincs szinkronban a legújabb biztonsági mentési feladatokkal, indexelésre és katalogizálásra van szükség. Ez az index- és katalógus-újraszkennelési folyamat az összes biztonságimásolat-készlet beolvasását és lekérése a felhőrétegből a helyi eszközszintre eredményezheti. Ez még inkább időigényessé teszi. |
| Helyhiba, amely a biztonsági mentési kiszolgáló és a StorSimple elvesztését eredményezi | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple alkalmazást, majd állítsa vissza a NetBackup segédprogramot. | Először állítsa vissza a StorSimple alkalmazást, majd állítsa vissza a NetBackup segédprogramot. Ha az eszköz helyreállítása után kell elvégeznie a visszaállítást, a teljes adatkészletek lekérése a felhőből az új eszközre történik. Minden művelet felhősebességen van. |

## <a name="references"></a>Referencia

A következő dokumentumokra hivatkoztak ebben a cikkben:

- [StorSimple többutas I/O-beállítás](storsimple-configure-mpio-windows-server.md)
- [Tárolási forgatókönyvek: Vékony kiépítés](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT-meghajtók használata](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Árnyékmásolatok beállítása megosztott mappákhoz](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>További lépések

- További információ a [biztonsági másolat készletből történő visszaállításról.](storsimple-restore-from-backup-set-u2.md)
- További információ az [eszközfeladat-átvétel és](storsimple-device-failover-disaster-recovery.md)a vészhelyreállítás végrehajtásáról.
