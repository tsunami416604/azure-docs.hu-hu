---
title: "Azure Backup-kiszolgáló SharePoint-farm mentésére az Azure-bA |} Microsoft Docs"
description: "Azure Backup Server használatával készítsen biztonsági másolatot, és a SharePoint-adatok helyreállítása. A cikkben az adatokat a SharePoint-farm konfigurálásához, hogy a kívánt adatokat tárolhatja az Azure-ban. Védett SharePoint-adatok visszaállíthatja a lemezről, vagy az Azure-ból."
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
ms.assetid: 34ba87a4-91f1-4054-a4a1-272af1e15496
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 3ed000affd326eb1bd7c99773ec021ad6e03cc3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>SharePoint-farm biztonsági mentése az Azure-ba
Akkor készítsen biztonsági másolatot egy SharePoint-farm Microsoft Azure hasonlóan, amely a biztonsági mentést más adatforrások a Microsoft Azure Backup Server (MABS) használatával. Azure biztonsági mentés naponta létrehozásához a biztonsági mentés ütemezése rugalmasságot biztosít, heti, havi vagy éves biztonsági mentést mutat, és lehetővé teszi az adatmegőrzési házirend-beállítások a különféle biztonsági mentési pontok. Helyi lemez másolat gyors helyreállítási idő célkitűzés (RTO) tárolására és gazdaságos, hosszú távú megőrzési Azure másolat tárolására is tartalmazza.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint támogatott verziója, és a kapcsolódó védelmi forgatókönyvek
Azure biztonsági mentés a DPM a következő szituációkat ismerteti:

| Számítási feladat | Verzió | A SharePoint központi telepítés | Védelem és helyreállítás |
| --- | --- | --- | --- | --- | --- |
| SharePoint |A SharePoint 2013, SharePoint 2007, 2010 SharePoint SharePoint 3.0 |A fizikai kiszolgálóként vagy Hyper-V vagy VMware virtuális gépeket telepített SharePoint <br> -------------- <br> Az SQL AlwaysOn | Helyreállítási beállítások SharePoint-Farm védelme: a helyreállítási farm, adatbázis és a lemezes helyreállítási pontok a fájl vagy listaelem.  Adatbázis és a farm helyreállítási Azure helyreállítási pontokból. |

## <a name="before-you-start"></a>Előkészületek
Néhány dolgot győződjön meg arról, előtt készítsen biztonsági másolatot az Azure-bA SharePoint-farm.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik [telepítve, és az Azure Backup Server előkészített](backup-azure-microsoft-azure-backup.md) munkaterhelések védelme érdekében.

### <a name="protection-agent"></a>Védelmi ügynök
A védelmi ügynököt telepíteni kell a SharePoint, az SQL Server rendszert futtató kiszolgálók és más a SharePoint-farm részét képező kiszolgálók futtató kiszolgálón. A védelmi ügynök beállítása kapcsolatos további információkért lásd: [telepítő védelmi ügynök](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Az egyetlen kivétel ez alól, hogy az ügynököt telepít csak egy egyetlen (WFE) előtér-webkiszolgálóján. DPM-nek az ügynök egy ELŐTÉR-webkiszolgálón csak a védelem belépési pontként szolgál.

### <a name="sharepoint-farm"></a>SharePoint-farm
A farm minden 10 millió eleme, a legalább 2 GB lemezterületet a köteten, ahol a MABS mappában kell lennie. Ez a terület szükség a katalógus előállításához. Katalógus-előállítás MABS elemeket (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) helyreállítása, létrehoz az egyes tartalom-adatbázisokban tárolt URL-címek listáját. A helyreállítható elemek ablaktáblán lévő URL-címek listáját megtekintheti a **helyreállítási** MABS felügyeleti konzol feladatterületén.

### <a name="sql-server"></a>SQL Server
MABS rendszerfiókként fut. SQL Server-adatbázisok biztonsági mentéséhez MABS kell, hogy a fiók rendszergazdai jogosultságokkal az SQL Servert futtató kiszolgáló. NT AUTHORITY\SYSTEM beállítása *sysadmin* a kiszolgálón, amelyen SQL Server előtt készítsen biztonsági másolatot.

Ha a SharePoint-farm SQL Server-aliasokkal használt konfigurált SQL Server-adatbázisok, telepítse az SQL Server ügyfél összetevőit MABS által védendő előtér-webkiszolgáló.

### <a name="sharepoint-server"></a>SharePoint Server
Amíg teljesítmény például a SharePoint-farm méret számos tényezőtől függ, általános útmutatásként egy MABS is 25 TB SharePoint-farm védelméhez.

### <a name="whats-not-supported"></a>Nem támogatott műveletek
* Egy SharePoint-farm védelme MABS nem nyújt védelmet a keresési indexek vagy szolgáltatás adatbázisai. Ezeknek az adatbázisoknak a védelmét külön-külön konfigurálni kell.
* MABS ad üzemeltetett SharePoint SQL Server-adatbázisok biztonsági mentése (SOFS) kibővíthető fájlkiszolgáló-megosztásokat.

## <a name="configure-sharepoint-protection"></a>SharePoint-védelem beállítása
MABS védelme a SharePoint használata előtt konfigurálnia kell a SharePoint VSS-író szolgáltatás (WSS-író szolgáltatás) használatával **ConfigureSharePoint.exe**.

Található **ConfigureSharePoint.exe** előtér-webkiszolgálón [MABS telepítési elérési út] \bin mappában. Ezt az eszközt biztosít a SharePoint-farm hitelesítő adataival, a védelmi ügynök. Futtatja egy WFE-kiszolgálón. Ha több ELŐTÉR-webkiszolgáló, csak egyet válasszon ki egy védelmi csoport konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS-író szolgáltatás konfigurálása
1. Az ELŐTÉR-webkiszolgálón, a parancssorban navigáljon a [MABS telepítés helye] \bin\
2. Adja meg a ConfigureSharePoint - EnableSharePointProtection.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ez a fiók a WFE-kiszolgálón a helyi Rendszergazdák csoport tagjának kell lennie. Ha a farm rendszergazdája nem helyi rendszergazda adja meg a következő engedélyeket az ELŐTÉR-webkiszolgálón:
   * A WSS_Admin_WPG csoportnak teljes hozzáférést a DPM mappát (% Program Files%\Microsoft Azure Backup\DPM).
   * A WSS_Admin_WPG csoportnak olvasási engedélyt a DPM beállításkulcsot (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Futtassa újra a ConfigureSharePoint.exe, amikor megváltozik a SharePoint-farm rendszergazdai hitelesítő adatait a lesz szüksége.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Készítsen biztonsági másolatot egy SharePoint-farm MABS
Miután konfigurálta a MABS és a SharePoint-farm, amint azt korábban, a SharePoint védi őket MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Egy SharePoint-farm védelme
1. A a **védelmi** lapon kattintson a MABS felügyeleti konzol **új**.
    ![Új védelem lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az a **védelmi csoport típusának kiválasztása** oldalán a **új védelmi csoport létrehozása** varázsló, jelölje be **kiszolgálók**, és kattintson a **következő**.

    ![Válassza ki a védelmi csoport típusa](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Az a **csoporttagok kiválasztása** jelölje be a SharePoint-kiszolgáló védelmére, és kattintson a jobb **következő**.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > A védelmi ügynök telepítve tekintse meg a kiszolgáló, a varázsló. MABS látható struktúrája. ConfigureSharePoint.exe futtatta, mert a MABS kommunikál a SharePoint VSS-író szolgáltatás és a megfelelő SQL Server-adatbázisok, és felismeri a SharePoint-farm struktúráját, a kapcsolódó tartalom-adatbázisok és a megfelelő elemeket.
   >
   >
4. Az a **adatvédelmi módszer kiválasztása** lapján adja meg a **védelmi csoport**, és válassza ki a kívánt *védelmi módszert*. Kattintson a **Tovább** gombra.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > A lemez adatvédelmi módszer segítségével rövid helyreállítási idő céljai.
   >
   >
5. Az a **rövid távú célok megadása** lapon, válassza ki a kívánt **megőrzési időtartam** , és azonosíthatja, ha azt szeretné, hogy a biztonsági mentések.

    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Általában szükség, amely öt napnál régebbi adatok, mert jelenleg kijelölt lemezen öt napnyi megőrzési időtartamot, és biztosítani, hogy a biztonsági mentés nem éles órában, ehhez a példához történik.
   >
   >
6. Tekintse át a tárolókészletben lefoglalt lemezterület a védelmi csoport, és majd **következő**.
7. Az összes védelmi csoportra MABS foglal le a hely a lemezen tárolja, és a replikák kezelése. Ezen a ponton MABS létre kell hoznia a kijelölt adatok másolatát. Válassza ki, hogyan és mikor szeretne létrehozni a replikát, és kattintson **következő**.

    ![A replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Győződjön meg arról, hogy a hálózati forgalom nem történik, válassza ki a megfelelő üzemi a munkaidőn kívül.
   >
   >
8. MABS érdekében végezzen konzisztencia-ellenőrzést a replika az adatok integritásának biztosítja. Kétféleképpen érhető el. Végezzen konzisztencia-ellenőrzést a kívánt ütemezést adhat meg, vagy a DPM is végezzen konzisztencia-ellenőrzést a replikán automatikusan, amikor inkonzisztenssé válik. Válassza ki a kívánt beállítást, és kattintson a **következő**.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az a **Online védelem adatainak megadása** lapon, válassza ki a SharePoint-farm védelmét, és kattintson a kívánt **következő**.

    ![A DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az a **Online biztonsági mentési ütemezés megadása** lapon válassza ki a kívánt ütemezést, és kattintson a **következő**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS biztosít két napi biztonsági mentések maximum Azure majd elérhető legújabb lemez biztonsági mentési pont számára. Azure biztonsági mentés WAN sávszélességet, amely alkalmas a biztonsági másolatok maximális és kevesen használatával is szabályozhatja [Azure biztonsági mentési hálózati sávszélesség-szabályozás](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Attól függően, hogy a kiválasztott, a biztonsági mentés ütemezése a **Online adatmegőrzési szabály megadása** lapon, válassza ki az adatmegőrzési napi, heti, havi vagy éves biztonsági mentési pontok.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS egy szerzett-édesapja-fia megőrzési séma, amelyben a különböző adatmegőrzési választható ki a másik biztonsági mentési pontok használja.
    >
    >
12. Hasonló lemezre, kezdeti hivatkozás pont replikájának kell létrehozni az Azure-ban. Válassza ki a kívánt beállítást, hozzon létre egy kezdeti biztonsági másolatot az Azure-ba, és kattintson **következő**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a beállításokat a a **összegzés** lapon, majd **csoport létrehozása**. Egy sikeres üzenet jelenik meg a védelmi csoport létrehozása után.

    ![Összefoglalás](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Egy SharePoint-elem visszaállítása a lemezről MABS használatával
A következő példában a *helyreállítás SharePoint-elem* véletlenül törölve lett, és helyre szeretné állítani.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzol**. Minden DPM által védett SharePoint-farmok megjelennek-e a **védelmi** fülre.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Első lépéseként állítsa helyre az elemet, válassza ki a **helyreállítási** fülre.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. A SharePoint kereshet *helyreállítás SharePoint-elem* kereséssel egy helyettesítő karakteres alapú belül egy helyreállítási pontot a tartományon.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pont a keresési eredmények, kattintson a jobb gombbal az elemet, és válassza **helyreállítása**.
5. Tallózzon a különböző helyreállítási pontokat is, és válasszon egy adatbázist vagy elem helyreállítása. Válassza ki **dátum > helyreállításkor**, és válassza ki a megfelelő **adatbázis > SharePoint-farm > helyreállítási pont > elem**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson a jobb gombbal az elemet, majd válassza ki **helyreállítása** megnyitásához a **helyreállítási varázsló**. Kattintson a **Tovább** gombra.

    ![Helyreállítási beállítások áttekintése](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Válassza ki a végrehajtani, és kattintson a kívánt helyreállítási **következő**.

    ![Helyreállítási típus](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A kijelölt **visszaállítás az eredeti** példában helyreállítja a cikk az eredeti SharePoint-webhelyre.
   >
   >
8. Válassza ki a **helyreállítási folyamat** használni kívánt.

   * Válassza ki **helyreállítás helyreállítási farm nélkül** Ha a SharePoint-farm nem változott, és ugyanaz, mint a helyreállítási pont, amely visszaállítása folyamatban van.
   * Válassza ki **helyreállításához a helyreállítási farm** Ha a SharePoint-farm megváltozott a helyreállítási pont létrehozása óta.

     ![A helyreállítási folyamat](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Állítsa helyre az adatbázist ideiglenesen egy SQL Server átmeneti helyét, és átmeneti fájlmegosztás MABS és a SharePoint-elem helyreállítása futtató kiszolgálón.

    ![Átmeneti Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS csatolja a tartalom-adatbázist, amelyen a SharePoint-elem az ideiglenes SQL Server-példányhoz. A tartalom-adatbázist azt állítja helyre az elemet, és az átmeneti tárolási helye a MABS helyezi. A helyreállított elem, amely az ideiglenes helyet most az ideiglenes helyet azon a SharePoint-farmon exportálni kell.

    ![Átmeneti Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza ki **helyreállítási beállítások megadása**, és a SharePoint-farm biztonsági beállítások alkalmazása, vagy a helyreállítási pont biztonsági beállításainak alkalmazása. Kattintson a **Tovább** gombra.

    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Ha szeretné, a hálózati sávszélesség használatának szabályozását. Ez minimalizálja az üzemi kiszolgálón történő éles órában.
    >
    >
11. Ellenőrizze az összefoglaló információkat, és kattintson a **helyreállítása** fájl helyreállítási megkezdéséhez.

    ![A helyreállítási összefoglaló](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza ki a **figyelés** lapra a **MABS felügyeleti konzol** megtekintéséhez a **állapot** a helyreállítási.

    ![Helyreállítási állapota](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A fájl már vissza. A SharePoint-webhelyre a visszaállított fájl is frissítheti.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Egy SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával
1. Egy SharePoint tartalom-adatbázis helyreállításához tallózzon a különböző helyreállítási pontokat (ahogy korábban), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pont megjelenítése a elérhető SharePoint-katalógus adatait.

   > [!NOTE]
   > A SharePoint-farm hosszú távú megőrzési az Azure-ban a védett, mert a MABS nincs katalógus információkkal (metaadatokkal) érhető el. Ennek eredményeképpen időpontban a SharePoint tartalom-adatbázist kell a helyre kell állítani, amikor kell a SharePoint-farm katalógus újra.
   >
   >
3. Kattintson a **újrakatalogizáláshoz**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A **felhő Újrakatalogizálni** állapotkezelő ablak nyílik meg.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Katalogizálni befejezése után a állapota *sikeres*. Kattintson a **Bezárás** gombra.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kattintson a SharePoint-objektum a MABS látható **helyreállítási** lapot a tartalom-adatbázist-struktúrában. Kattintson a jobb gombbal az elemet, és kattintson **helyreállítása**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton, kövesse a [helyreállítási lépések az ebben a cikkben](#restore-a-sharepoint-item-from-disk-using-dpm) a lemezről egy SharePoint tartalom-adatbázis helyreállításához.

## <a name="faqs"></a>Gyakori kérdések
K: helyreállíthatók a SharePoint-elem az eredeti helyre, ha SharePoint (a védelem a lemezen) az SQL AlwaysOn használatára van konfigurálva?<br>
V: Igen, az elem állíthatók helyre az eredeti SharePoint-webhelyre.

K: helyreállíthatók a SharePoint-adatbázist az eredeti helyre, ha a SharePoint SQL AlwaysOn használatára van konfigurálva?<br>
V:, mert a SharePoint-adatbázisok vannak konfigurálva az SQL AlwaysOn, ezeket nem lehet módosítani kivéve, ha a rendelkezésre állási csoport eltávolítása. Ennek eredményeképpen az MABS nem tudja visszaállítani az adatbázis az eredeti helyre. Helyreállíthatja az SQL Server-adatbázis egy másik SQL Server-példányhoz.

## <a name="next-steps"></a>Következő lépések
* További tudnivalók a SharePoint védelme MABS – lásd [videó sorozat - a SharePoint védelme a DPM](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
