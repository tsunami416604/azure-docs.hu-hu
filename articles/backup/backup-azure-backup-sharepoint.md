---
title: "A DPM vagy az Azure Backup server védelmének Azure SharePoint-farm |} Microsoft Docs"
description: "Ez a cikk a DPM vagy az Azure Backup server védelmének SharePoint-farm áttekintést nyújt az Azure-bA"
services: backup
documentationcenter: 
author: adigan
manager: Nkolli1
editor: 
ms.assetid: e0c0c252-dc1d-4072-b777-7222c13950b0
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: adigan;giridham;jimpark;trinadhk;markgal
ms.openlocfilehash: 1bbf3233169fa9966e3dd0fac18ee448f26caa6b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>SharePoint-farm biztonsági mentése az Azure-ba
Akkor készítsen biztonsági másolatot egy SharePoint-farm számára a Microsoft Azure hasonlóan, amely a biztonsági mentést más adatforrások a System Center Data Protection Manager (DPM) használatával. Azure biztonsági mentés naponta létrehozásához a biztonsági mentés ütemezése rugalmasságot biztosít, heti, havi vagy éves biztonsági mentést mutat, és lehetővé teszi az adatmegőrzési házirend-beállítások a különféle biztonsági mentési pontok. A DPM lehetővé teszi a helyi lemez másolat gyors helyreállítási idő célkitűzés (RTO) tárolására és gazdaságos, hosszú távú megőrzési Azure másolat tárolja.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint támogatott verziója, és a kapcsolódó védelmi forgatókönyvek
Azure biztonsági mentés a DPM a következő szituációkat ismerteti:

| Számítási feladat | Verzió | A SharePoint központi telepítés | DPM-telepítés típusa | DPM – System Center 2012 R2 | Védelem és helyreállítás |
| --- | --- | --- | --- | --- | --- |
| SharePoint |A SharePoint 2013, SharePoint 2007, 2010 SharePoint SharePoint 3.0 |A fizikai kiszolgálóként vagy Hyper-V vagy VMware virtuális gépeket telepített SharePoint <br> -------------- <br> Az SQL AlwaysOn |Fizikai kiszolgáló vagy a helyszíni Hyper-V virtuális gép |Támogatja az Azure szolgáltatásba mentésének az 5. kumulatív |Helyreállítási beállítások SharePoint-Farm védelme: a helyreállítási farm, adatbázis és a lemezes helyreállítási pontok a fájl vagy listaelem.  Adatbázis és a farm helyreállítási Azure helyreállítási pontokból. |

## <a name="before-you-start"></a>Előkészületek
Néhány dolgot győződjön meg arról, előtt készítsen biztonsági másolatot az Azure-bA SharePoint-farm.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt továbblépne, győződjön meg arról, hogy teljesül összes a [a Microsoft Azure Backup előfeltételei](backup-azure-dpm-introduction.md#prerequisites) munkaterhelések védelme érdekében. Egyes feladatok Előfeltételek a következők: hozzon létre egy mentési tárolót, töltse le a tárolói hitelesítő adatokat, telepítse az Azure Backup szolgáltatás ügynöke és regisztrálja a DPM vagy az Azure Backup Server a tárolóban.

### <a name="dpm-agent"></a>A DPM-ügynök
A DPM-ügynököt telepíteni kell a SharePoint, az SQL Server rendszert futtató kiszolgálók és más a SharePoint-farm részét képező kiszolgálók futtató kiszolgálón. A védelmi ügynök beállítása kapcsolatos további információkért lásd: [telepítő védelmi ügynök](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Az egyetlen kivétel ez alól, hogy az ügynököt telepít csak egy egyetlen (WFE) előtér-webkiszolgálóján. DPM-nek az ügynök egy ELŐTÉR-webkiszolgálón csak a védelem belépési pontként szolgál.

### <a name="sharepoint-farm"></a>SharePoint-farm
A farm minden 10 millió eleme, a legalább 2 GB lemezterületet a köteten, ahol a DPM mappa kell lennie. Ez a terület szükség a katalógus előállításához. Katalógus-előállítás elemeket (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) helyreállításához a DPM létrehoz az egyes tartalom-adatbázisokban tárolt URL-címek listáját. A helyreállítható elemek ablaktáblán lévő URL-címek listáját megtekintheti a **helyreállítási** feladat a DPM felügyeleti konzol felügyelet munkaterületén.

### <a name="sql-server"></a>SQL Server
A DPM fut, a LocalSystem fiókra. SQL Server-adatbázisok biztonsági mentéséhez a DPM-nek fiók rendszergazdai jogosultságokkal az SQL Servert futtató kiszolgáló. NT AUTHORITY\SYSTEM beállítása *sysadmin* a kiszolgálón, amelyen SQL Server előtt készítsen biztonsági másolatot.

Ha a SharePoint-farm SQL Server-aliasokkal használt konfigurált SQL Server-adatbázisok, telepítse az SQL Server ügyféloldali összetevőit az előtér-webkiszolgálón, amely védeni fogja a DPM.

### <a name="sharepoint-server"></a>SharePoint Server
Amíg teljesítmény például a SharePoint-farm méret számos tényezőtől függ, általános útmutatásként egy DPM-kiszolgáló képes 25 TB SharePoint-farm védelméhez.

### <a name="dpm-update-rollup-5"></a>A DPM kumulatív frissítés 5
Az Azure-bA SharePoint-farm védelmének megkezdéséhez szeretne telepíteni a DPM-kumulatív frissítés 5-ös vagy újabb. 5. kumulatív frissítés teszi lehetővé az Azure-bA SharePoint-farm védelméhez, ha a farm SQL AlwaysOn használatára van konfigurálva.
További információkért lásd: a blog bejegyzése, amely bemutatja a [DPM kumulatív frissítés 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Nem támogatott műveletek
* A DPM által védett SharePoint-farm nem nyújt védelmet a keresési indexek vagy szolgáltatás adatbázisai. Ezeknek az adatbázisoknak a védelmét külön-külön konfigurálni kell.
* A DPM nem ad üzemeltetett SharePoint SQL Server-adatbázisok biztonsági mentése (SOFS) kibővíthető fájlkiszolgáló-megosztásokat.

## <a name="configure-sharepoint-protection"></a>SharePoint-védelem beállítása
A DPM védelme a SharePoint használata előtt konfigurálnia kell a SharePoint VSS-író szolgáltatás (WSS-író szolgáltatás) használatával **ConfigureSharePoint.exe**.

Található **ConfigureSharePoint.exe** előtér-webkiszolgálón [DPM telepítési útvonalán] \bin mappában. Ezt az eszközt biztosít a SharePoint-farm hitelesítő adataival, a védelmi ügynök. Futtatja egy WFE-kiszolgálón. Ha több ELŐTÉR-webkiszolgáló, csak egyet válasszon ki egy védelmi csoport konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS-író szolgáltatás konfigurálása
1. Az ELŐTÉR-webkiszolgálón, a parancssorban navigáljon a [DPM telepítési helye] \bin\
2. Adja meg a ConfigureSharePoint - EnableSharePointProtection.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ez a fiók a WFE-kiszolgálón a helyi Rendszergazdák csoport tagjának kell lennie. Ha a farm rendszergazdája nem helyi rendszergazda adja meg a következő engedélyeket az ELŐTÉR-webkiszolgálón:
   * A WSS_Admin_WPG csoportnak teljes hozzáférést a DPM mappát (% Program Files%\Microsoft Data Protection Manager\DPM).
   * A WSS_Admin_WPG csoportnak olvasási engedélyt a DPM beállításkulcsot (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Futtassa újra a ConfigureSharePoint.exe, amikor megváltozik a SharePoint-farm rendszergazdai hitelesítő adatait a lesz szüksége.
> 
> 

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>A DPM biztonsági mentése egy SharePoint-farm
Miután konfigurálta a DPM és a SharePoint-farm, amint azt korábban, SharePoint védelme a DPM által.

### <a name="to-protect-a-sharepoint-farm"></a>Egy SharePoint-farm védelme
1. Az a **védelmi** lapon kattintson a DPM felügyeleti konzol **új**.
    ![Új védelem lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az a **védelmi csoport típusának kiválasztása** oldalán a **új védelmi csoport létrehozása** varázsló, jelölje be **kiszolgálók**, és kattintson a **következő**.
   
    ![Válassza ki a védelmi csoport típusa](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Az a **csoporttagok kiválasztása** jelölje be a SharePoint-kiszolgáló védelmére, és kattintson a jobb **következő**.
   
    ![Csoporttagok kiválasztása](./media/backup-azure-backup-sharepoint/select-group-members2.png)
   
   > [!NOTE]
   > A DPM-ügynököt futtató tekintse meg a kiszolgáló, a varázslóban. A DPM is látható struktúrája. ConfigureSharePoint.exe futtatta, mert a DPM a SharePoint VSS-író szolgáltatás és a megfelelő SQL Server-adatbázisok kommunikál, és felismeri a SharePoint-farm struktúráját, a kapcsolódó tartalom-adatbázisok és a megfelelő elemeket.
   > 
   > 
4. Az a **adatvédelmi módszer kiválasztása** lapján adja meg a **védelmi csoport**, és válassza ki a kívánt *védelmi módszert*. Kattintson a **Tovább** gombra.
   
    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)
   
   > [!NOTE]
   > A lemez adatvédelmi módszer segítségével rövid helyreállítási idő céljai. Azure célja egy gazdaságos, hosszú távú védelem szalagok képest. További információkért lásd: [történő cserélje le a szalag infrastruktúra használata Azure biztonsági mentés](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   > 
   > 
5. Az a **rövid távú célok megadása** lapon, válassza ki a kívánt **megőrzési időtartam** , és azonosíthatja, ha azt szeretné, hogy a biztonsági mentések.
   
    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)
   
   > [!NOTE]
   > Általában szükség, amely öt napnál régebbi adatok, mert jelenleg kijelölt lemezen öt napnyi megőrzési időtartamot, és biztosítani, hogy a biztonsági mentés nem éles órában, ehhez a példához történik.
   > 
   > 
6. Tekintse át a tárolókészletben lefoglalt lemezterület a védelmi csoport, és majd **következő**.
7. Az összes védelmi csoportra a DPM lefoglalja a szükséges lemezterületet történő tárolására és kezelésére a replikákat. A DPM ezen a ponton a kiválasztott adatok másolatának kell létrehoznia. Válassza ki, hogyan és mikor szeretne létrehozni a replikát, és kattintson **következő**.
   
    ![A replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)
   
   > [!NOTE]
   > Győződjön meg arról, hogy a hálózati forgalom nem történik, válassza ki a megfelelő üzemi a munkaidőn kívül.
   > 
   > 
8. A DPM érdekében végezzen konzisztencia-ellenőrzést a replika az adatok integritásának biztosítja. Kétféleképpen érhető el. Végezzen konzisztencia-ellenőrzést a kívánt ütemezést adhat meg, vagy a DPM is végezzen konzisztencia-ellenőrzést a replikán automatikusan, amikor inkonzisztenssé válik. Válassza ki a kívánt beállítást, és kattintson a **következő**.
   
    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az a **Online védelem adatainak megadása** lapon, válassza ki a SharePoint-farm védelmét, és kattintson a kívánt **következő**.
   
    ![A DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az a **Online biztonsági mentési ütemezés megadása** lapon válassza ki a kívánt ütemezést, és kattintson a **következő**.
    
    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)
    
    > [!NOTE]
    > DPM Azure különböző időpontokban egy legfeljebb két napi biztonsági mentést biztosít. Azure biztonsági mentés WAN sávszélességet, amely alkalmas a biztonsági másolatok maximális és kevesen használatával is szabályozhatja [Azure biztonsági mentési hálózati sávszélesség-szabályozás](https://azure.microsoft.com/en-in/documentation/articles/backup-configure-vault/#enable-network-throttling).
    > 
    > 
11. Attól függően, hogy a kiválasztott, a biztonsági mentés ütemezése a **Online adatmegőrzési szabály megadása** lapon, válassza ki az adatmegőrzési napi, heti, havi vagy éves biztonsági mentési pontok.
    
    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)
    
    > [!NOTE]
    > A DPM egy szerzett-édesapja-fia megőrzési séma, amelyben a különböző adatmegőrzési választható ki a másik biztonsági mentési pontok használja.
    > 
    > 
12. Hasonló lemezre, kezdeti hivatkozás pont replikájának kell létrehozni az Azure-ban. Válassza ki a kívánt beállítást, hozzon létre egy kezdeti biztonsági másolatot az Azure-ba, és kattintson **következő**.
    
    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a beállításokat a a **összegzés** lapon, majd **csoport létrehozása**. Egy sikeres üzenet jelenik meg a védelmi csoport létrehozása után.
    
    ![Összefoglalás](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Egy SharePoint-elem visszaállítása a lemezről DPM használatával
A következő példában a *helyreállítás SharePoint-elem* véletlenül törölve lett, és helyre szeretné állítani.
![A DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzol**. Minden DPM által védett SharePoint-farmok megjelennek-e a **védelmi** fülre.
   
    ![A DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Első lépéseként állítsa helyre az elemet, válassza ki a **helyreállítási** fülre.
   
    ![A DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. A SharePoint kereshet *helyreállítás SharePoint-elem* kereséssel egy helyettesítő karakteres alapú belül egy helyreállítási pontot a tartományon.
   
    ![A DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pont a keresési eredmények, kattintson a jobb gombbal az elemet, és válassza **helyreállítása**.
5. Tallózzon a különböző helyreállítási pontokat is, és válasszon egy adatbázist vagy elem helyreállítása. Válassza ki **dátum > helyreállításkor**, és válassza ki a megfelelő **adatbázis > SharePoint-farm > helyreállítási pont > elem**.
   
    ![A DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
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
9. Állítsa helyre az adatbázist ideiglenesen egy SQL Server átmeneti helyét, és átmeneti fájlmegosztást a DPM-kiszolgáló és a SharePoint-elem helyreállítása futtató kiszolgálón.
   
    ![Átmeneti Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)
   
    A DPM csatlakoztatja a tartalom-adatbázist, amelyen a SharePoint-elem az ideiglenes SQL Server-példányhoz. A tartalom-adatbázist a DPM-kiszolgálón az elem helyreállítása és elhelyezése a DPM-kiszolgálón az átmeneti helyet. A helyreállított elem, amely az ideiglenes helyet a DPM-kiszolgáló most az ideiglenes helyet azon a SharePoint-farmon exportálni kell.
   
    ![Átmeneti Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza ki **helyreállítási beállítások megadása**, és a SharePoint-farm biztonsági beállítások alkalmazása, vagy a helyreállítási pont biztonsági beállításainak alkalmazása. Kattintson a **Tovább** gombra.
    
    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)
    
    > [!NOTE]
    > Ha szeretné, a hálózati sávszélesség használatának szabályozását. Ez minimalizálja az üzemi kiszolgálón történő éles órában.
    > 
    > 
11. Ellenőrizze az összefoglaló információkat, és kattintson a **helyreállítása** fájl helyreállítási megkezdéséhez.
    
    ![A helyreállítási összefoglaló](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza ki a **figyelés** lapra a **DPM felügyeleti konzol** megtekintéséhez a **állapot** a helyreállítási.
    
    ![Helyreállítási állapota](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)
    
    > [!NOTE]
    > A fájl már vissza. A SharePoint-webhelyre a visszaállított fájl is frissítheti.
    > 
    > 

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Egy SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával
1. Egy SharePoint tartalom-adatbázis helyreállításához tallózzon a különböző helyreállítási pontokat (ahogy korábban), és válassza ki a visszaállítani kívánt helyreállítási pontot.
   
    ![A DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pont megjelenítése a elérhető SharePoint-katalógus adatait.
   
   > [!NOTE]
   > A SharePoint-farm hosszú távú megőrzési az Azure-ban a védett, mert a DPM-kiszolgálón nincs katalógus információkkal (metaadatokkal) érhető el. Ennek eredményeképpen időpontban a SharePoint tartalom-adatbázist kell a helyre kell állítani, amikor kell a SharePoint-farm katalógus újra.
   > 
   > 
3. Kattintson a **újrakatalogizáláshoz**.
   
    ![A DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)
   
    A **felhő Újrakatalogizálni** állapotkezelő ablak nyílik meg.
   
    ![A DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)
   
    Katalogizálni befejezése után a állapota *sikeres*. Kattintson a **Bezárás** gombra.
   
    ![A DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kattintson a SharePoint-objektumra, a DPM látható **helyreállítási** lapot a tartalom-adatbázist-struktúrában. Kattintson a jobb gombbal az elemet, és kattintson **helyreállítása**.
   
    ![A DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton, kövesse a [helyreállítási lépések az ebben a cikkben](#restore-a-sharepoint-item-from-disk-using-dpm) a lemezről egy SharePoint tartalom-adatbázis helyreállításához.

## <a name="faqs"></a>Gyakori kérdések
K: milyen DPM támogatja az SQL Server 2014 és az SQL 2012 (SP2)?<br>
V: DPM 2012 R2 Update Rollup 4 egyaránt támogat.

K: helyreállíthatók a SharePoint-elem az eredeti helyre, ha SharePoint (a védelem a lemezen) az SQL AlwaysOn használatára van konfigurálva?<br>
V: Igen, az elem állíthatók helyre az eredeti SharePoint-webhelyre.

K: helyreállíthatók a SharePoint-adatbázist az eredeti helyre, ha a SharePoint SQL AlwaysOn használatára van konfigurálva?<br>
V:, mert a SharePoint-adatbázisok vannak konfigurálva az SQL AlwaysOn, ezeket nem lehet módosítani kivéve, ha a rendelkezésre állási csoport eltávolítása. Ennek eredményeképpen az a DPM nem tudja visszaállítani az adatbázis az eredeti helyre. Helyreállíthatja az SQL Server-adatbázis egy másik SQL Server-példányhoz.

## <a name="next-steps"></a>Következő lépések
* További információk a DPM védelme a SharePoint - lásd [videó sorozat - a SharePoint védelme a DPM](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Felülvizsgálati [kibocsátási megjegyzések a System Center 2012 – Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Felülvizsgálati [kibocsátási megjegyzések a Data Protection Manager a System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

