---
title: SharePoint-farm Azure-ba való DPM/Azure Backupának védelme
description: Ez a cikk áttekintést nyújt a SharePoint-farmok Azure-ba való DPM/Azure Backup kiszolgáló védelméről
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: f17cc71e0666ccf08d90fcef8f18552c09761cbc
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689403"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>SharePoint-farm biztonsági mentése az Azure-ba
A SharePoint-farmról biztonsági mentést készíthet a System Center Data Protection Manager (DPM) szolgáltatással való Microsoft Azureére, ugyanúgy, mint az egyéb adatforrások biztonsági mentése. Azure Backup rugalmasságot biztosít a biztonsági mentési ütemezésben napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és adatmegőrzési házirend-beállításokat biztosít a különböző biztonsági mentési pontokhoz. A DPM lehetővé teszi a helyi lemezek másolását a gyors helyreállítási idejű célkitűzésekhez (RTO), valamint a másolatok Azure-ba történő tárolását gazdaságos, hosszú távú adatmegőrzésre.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek
A DPM Azure Backup a következő forgatókönyveket támogatja:

| Számítási feladat | Version | SharePoint-telepítés | DPM központi telepítési típus | DPM - System Center 2012 R2 | Védelem és helyreállítás |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3,0 |Fizikai kiszolgálóként vagy Hyper-V/VMware virtuális gépen üzembe helyezett SharePoint <br> -------------- <br> SQL AlwaysOn |Fizikai kiszolgáló vagy helyszíni Hyper-V virtuális gép |Támogatja az Azure-ba történő biztonsági mentést az 5. kumulatív frissítéssel |A SharePoint-farm helyreállítási lehetőségeinek biztosítása: Helyreállítási Farm, adatbázis és fájl vagy listaelem a lemezes helyreállítási pontokból.  Farm-és adatbázis-helyreállítás az Azure helyreállítási pontjairól. |

## <a name="before-you-start"></a>Előkészületek
A SharePoint-farmok Azure-ba történő biztonsági mentése előtt néhány dolgot meg kell erősítenie.

### <a name="prerequisites"></a>Előfeltételek
A folytatás előtt győződjön meg arról, hogy teljesítette az [Microsoft Azure Backup használatára vonatkozó](backup-azure-dpm-introduction.md#prerequisites-and-limitations) összes előfeltételt a munkaterhelések elleni védelemhez. Az előfeltételek egyes feladatai közé tartozik a Backup-tároló létrehozása, a tár hitelesítő adatainak letöltése, Azure Backup ügynök telepítése, valamint a DPM/Azure Backup Server regisztrálása a tárolóval.

### <a name="dpm-agent"></a>DPM-ügynök
A DPM-ügynököt a SharePointot futtató kiszolgálóra, a SQL Server rendszert futtató kiszolgálókra és a SharePoint-farm részét képező összes többi kiszolgálóra kell telepíteni. A védelmi ügynök beállításával kapcsolatos további információkért lásd: a [védelmi ügynök](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx)beállítása.  Az egyetlen kivétel az, hogy csak egyetlen webes előtér-(WFE-) kiszolgálón telepíti az ügynököt. A DPM csak egy WFE-kiszolgálón lévő ügynökre van szüksége, hogy a védelemhez belépési pontként szolgáljon.

### <a name="sharepoint-farm"></a>SharePoint-farm
A farm minden 10 000 000 eleméhez legalább 2 GB szabad területnek kell lennie azon a köteten, ahol a DPM mappa található. Ez a terület a katalógus létrehozásához szükséges. Az adott elemek (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) helyreállításához a DPM létrehoz egy listát az egyes tartalom-adatbázisokban található URL-címekről. Az URL-címek listáját a helyreállítható elemek ablaktábláján tekintheti meg DPM Felügyeleti konzol a **helyreállítási** feladat területén.

### <a name="sql-server"></a>SQL Server
A DPM LocalSystem fiókként fut. SQL Server adatbázisok biztonsági mentéséhez a DPM rendszergazdai jogosultságokkal kell rendelkeznie az adott fiókhoz a SQL Server rendszert futtató kiszolgálón. A biztonsági mentés előtt állítsa be az NT AUTHORITY\SYSTEM-t a *sysadmin* értékre azon a kiszolgálón, amelyen a SQL Server fut.

Ha a SharePoint-farm SQL Server SQL Server aliasokkal konfigurált adatbázisokkal rendelkezik, akkor telepítse a SQL Server ügyféloldali összetevőit azon az előtér-webkiszolgálón, amelyet a DPM véd.

### <a name="sharepoint-server"></a>SharePoint Server
Míg a teljesítmény számos tényezőtől függ, például a SharePoint-farm méretétől, általános útmutatásként az egyik DPM-kiszolgáló egy 25 TB-os SharePoint-farmot is képes védelemmel ellátni.

### <a name="dpm-update-rollup-5"></a>5\. kumulatív frissítés DPM
A SharePoint-farmok Azure-ba való védelmének megkezdéséhez telepítenie kell az 5. vagy újabb kumulatív frissítés DPM. Az 5. kumulatív frissítés lehetővé teszi a SharePoint-farmok Azure-ba való védelmének lehetőségét, ha a Farm az SQL AlwaysOn használatával van konfigurálva.
További információ: a [DPM 5. kumulatív frissítését](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx) bevezető blogbejegyzés

### <a name="whats-not-supported"></a>Nem támogatott műveletek
* A SharePoint-farmokat védő DPM nem védik a keresési indexeket vagy az Application Service-adatbázisokat. Ezen adatbázisok védelmét külön kell konfigurálnia.
* A DPM nem biztosít biztonsági mentést a SharePoint SQL Server-adatbázisokról, amelyek a kibővített fájlkiszolgáló (SOFS) megosztásokon futnak.

## <a name="configure-sharepoint-protection"></a>A SharePoint-védelem konfigurálása
Ahhoz, hogy a DPM-t a SharePoint számára is használhassa, konfigurálnia kell a SharePoint VSS-író szolgáltatást (WSS-író szolgáltatás) a **ConfigureSharePoint. exe**használatával.

A **ConfigureSharePoint. exe** a [DPM telepítési útvonala] \bin mappában található az előtér-webkiszolgálón. Ez az eszköz biztosítja a védelmi ügynököt a SharePoint-farm hitelesítő adataival. Egyetlen WFE-kiszolgálón futtatja azt. Ha több WFE-kiszolgálóval rendelkezik, válassza a csak egyet a védelmi csoportok konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS-író szolgáltatás konfigurálása
1. A WFE-kiszolgálón a parancssorban lépjen a [DPM telepítési hely] \bin\ mappát
2. Adja meg a ConfigureSharePoint-EnableSharePointProtection értéket.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ennek a fióknak a helyi rendszergazda csoport tagjának kell lennie a WFE-kiszolgálón. Ha a farm rendszergazdája nem helyi rendszergazda, a következő engedélyeket kell megadnia a WFE-kiszolgálón:
   * Adja meg a WSS_Admin_WPG csoport teljes hozzáférését a DPM mappához (% Program Files%\Microsoft adatvédelem Manager\DPM).
   * Adjon olvasási hozzáférést a WSS_Admin_WPG csoportnak a DPM beállításkulcs (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager) számára.

> [!NOTE]
> Ha a SharePoint-farm rendszergazdai hitelesítő adatai módosulnak, újra kell futtatnia a ConfigureSharePoint. exe fájlt.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>SharePoint-farm biztonsági mentése a DPM használatával
Miután a korábban ismertetett módon konfigurálta a DPM-t és a SharePoint-farmot, a SharePoint védelme a DPM használatával lehetséges.

### <a name="to-protect-a-sharepoint-farm"></a>SharePoint-farmok megóvása
1. A DPM Felügyeleti konzol **védelem** lapján kattintson az **új**elemre.
    ![Új védelem lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az **új védelmi csoport létrehozása** varázsló **védelmi csoport típusának kiválasztása** lapján válassza a **kiszolgálók**elemet, majd kattintson a **tovább**gombra.

    ![Védelmi csoport típusának kiválasztása](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. A **csoporttagok kiválasztása** képernyőn jelölje be a védelemmel ellátni kívánt SharePoint-kiszolgáló jelölőnégyzetét, majd kattintson a **tovább**gombra.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Ha a DPM-ügynök telepítve van, akkor a kiszolgáló a varázslóban látható. A DPM a szerkezetét is megjeleníti. Mivel a ConfigureSharePoint. exe fájlt futtatta, a DPM kommunikál a SharePoint VSS-író szolgáltatással és a hozzá tartozó SQL Server adatbázisokkal, és felismeri a SharePoint-farm struktúráját, a társított tartalom-adatbázisokat és a hozzájuk tartozó elemeket.
   >
   >
4. Az **adatvédelmi módszer kiválasztása** lapon adja meg a **védelmi csoport**nevét, és válassza ki az előnyben részesített *védelmi módszereket*. Kattintson a **Tovább** gombra.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > A lemezes védelmi módszer segíti a rövid helyreállítási idejű célkitűzések teljesítését. Az Azure egy gazdaságos, hosszú távú védelmi cél a szalagokhoz képest. További információ: [a Azure Backup használata a szalagos infrastruktúra lecseréléséhez](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   >
   >
5. A **rövid távú célok megadása** oldalon válassza ki az előnyben részesített **megőrzési tartományt** , és határozza meg, hogy mikor történjen a biztonsági mentés.

    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Mivel a helyreállítás leggyakrabban az öt napnál régebbi adatok esetében szükséges, a lemezen lévő megőrzési időtartamot választottuk, és gondoskodunk arról, hogy a biztonsági mentés a nem üzemi időszakban történjen, ebben a példában.
   >
   >
6. Tekintse át a védelmi csoport számára lefoglalt tárterület-lemezterületet, majd kattintson a **tovább**gombra.
7. Minden védelmi csoport esetében a DPM lemezterületet foglal le a replikák tárolásához és kezeléséhez. Ekkor a DPM-nek létre kell hoznia a kijelölt adatmennyiség másolatát. Válassza ki, hogyan és mikor szeretné létrehozni a replikát, majd kattintson a **tovább**gombra.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Annak érdekében, hogy a hálózati forgalom ne legyen hatással, válasszon ki egy időpontot az üzemi órákon kívül.
   >
   >
8. A DPM biztosítja az adatok integritását, ha konzisztencia-ellenőrzéseket végez a replikán. Két lehetőség közül választhat. Megadhat egy ütemtervet a konzisztencia-ellenőrzések futtatásához, vagy a DPM automatikusan futtathat konzisztencia-ellenőrzéseket a replikán, ha az inkonzisztens lesz. Válassza ki a kívánt lehetőséget, majd kattintson a **tovább**gombra.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az **online védelem adatainak megadása** lapon válassza ki a védelemmel ellátni kívánt SharePoint-farmot, majd kattintson a **tovább**gombra.

    ![DPM SharePoint-Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az **online biztonsági mentési ütemterv megadása** lapon válassza ki az előnyben részesített ütemtervet, majd kattintson a **tovább**gombra.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > A DPM legfeljebb két napi biztonsági mentést biztosít az Azure-ba különböző időpontokban. A Azure Backup az [Azure Backup hálózati sávszélesség-szabályozás](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling)használatával is szabályozhatja azt a WAN-sávszélességet, amelyet a biztonsági mentéshez használhat a csúcs-és a távolsági órákban.
    >
    >
11. A kiválasztott biztonsági mentési ütemezéstől függően az online adatmegőrzési **szabály megadása** lapon válassza ki a napi, heti, havi és éves biztonsági mentési pontok megőrzési szabályát.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > A DPM egy nagyapa-apa-Son adatmegőrzési sémát használ, amelyben eltérő adatmegőrzési szabályt lehet kiválasztani a különböző biztonsági mentési pontokhoz.
    >
    >
12. A lemezhez hasonlóan a kezdeti hivatkozási pont replikáját is létre kell hozni az Azure-ban. Válassza ki a kívánt lehetőséget egy kezdeti biztonsági másolat Azure-ba való létrehozásához, majd kattintson a **tovább**gombra.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a kiválasztott beállításokat az **Összefoglalás** lapon, majd kattintson a **csoport létrehozása**elemre. A védelmi csoport létrehozása után sikert jelző üzenet jelenik meg.

    ![Összegzés](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>SharePoint-elem visszaállítása a lemezről a DPM használatával
A következő példában a SharePoint- *elem helyreállítása* véletlenül törölve lett, és vissza kell állítani.
![DPM SharePoint-Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzol**. A DPM által védett SharePoint-farmok a **védelem** lapon jelennek meg.

    ![DPM SharePoint-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Az elem helyreállításának megkezdéséhez válassza a **helyreállítás** lapot.

    ![DPM SharePoint-Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. A SharePointban a *SharePoint-elem* helyreállítása a helyreállítási pont tartományán belüli helyettesítő karakteres keresés használatával végezhető el.

    ![DPM SharePoint-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pontot a keresési eredmények közül, kattintson a jobb gombbal az elemre, majd válassza a **helyreállítás**lehetőséget.
5. A helyreállításhoz különböző helyreállítási pontokat is megkereshet, és kiválaszthatja a helyreállítani kívánt adatbázist vagy elemet. Válassza a **dátum > a helyreállítási idő**lehetőséget, majd válassza ki a megfelelő **adatbázist > SharePoint-farm > helyreállítási pont > elem**.

    ![DPM SharePoint-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson a jobb gombbal az elemre, majd **válassza a** helyreállítás elemet a **helyreállítási varázsló**megnyitásához. Kattintson a **Tovább** gombra.

    ![Visszaállítási kijelölés áttekintése](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Válassza ki a végrehajtani kívánt helyreállítás típusát, majd kattintson a **tovább**gombra.

    ![Helyreállítási típus](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A példában az **eredeti helyre történő helyreállítás** a következő helyre visszaállítja az elemet az eredeti SharePoint-webhelyre.
   >
   >
8. Válassza ki a használni kívánt **helyreállítási folyamatot** .

   * Ha a SharePoint-farm nem módosult, és megegyezik a visszaállítani kívánt helyreállítási ponttal, válassza a helyreállítás a **helyreállítási Farm használata nélkül** lehetőséget.
   * Ha a SharePoint-farm megváltozott a helyreállítási pont létrehozása óta, válassza **a helyreállítás a helyreállítási Farm használatával** lehetőséget.

     ![Helyreállítási folyamat](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Adja meg az átmeneti SQL Server példány helyét az adatbázis ideiglenes helyreállításához, és adjon meg egy átmeneti fájlmegosztást a DPM-kiszolgálón és a SharePoint rendszert futtató kiszolgálón az elem helyreállításához.

    ![Előkészítési Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    A DPM csatolja a SharePoint-elemeket futtató tartalom-adatbázist az ideiglenes SQL Server példányhoz. A tartalom-adatbázisból a DPM-kiszolgáló helyreállítja az elemet, és a DPM-kiszolgálón elhelyezi az átmeneti fájl helyét. A DPM-kiszolgáló átmeneti helyén található helyreállított elemnek most exportálva kell lennie az átmeneti helyre a SharePoint-farmon.

    ![Előkészítési Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza a **helyreállítási beállítások megadása lehetőséget**, majd alkalmazza a biztonsági beállításokat a SharePoint-farmra, vagy alkalmazza a helyreállítási pont biztonsági beállításait. Kattintson a **Tovább** gombra.

    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Megadhatja a hálózati sávszélesség használatának szabályozását. Ez a művelet a termelési idő alatt a termelési kiszolgálóra is hatással van.
    >
    >
11. Tekintse át az összegző információkat, majd kattintson a **helyreállítás** gombra a fájl helyreállításának megkezdéséhez.

    ![Helyreállítási összefoglalás](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza a **figyelés** fület a **DPM felügyeleti konzol** a helyreállítás **állapotának** megtekintéséhez.

    ![Helyreállítási állapot](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A fájl most vissza lett állítva. Frissítheti a SharePoint-webhelyet a visszaállított fájl vizsgálatához.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával
1. SharePoint tartalom-adatbázis helyreállításához tallózzon a különböző helyreállítási pontok között (ahogy az előzőekben látható), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![DPM SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. A SharePoint-katalógus elérhető adatainak megjelenítéséhez kattintson duplán a SharePoint helyreállítási pontra.

   > [!NOTE]
   > Mivel a SharePoint-farm védelme hosszú távú adatmegőrzést biztosít az Azure-ban, a DPM-kiszolgálón nem érhető el katalógus-információ (metaadatok). Ennek eredményeképpen, amikor egy időponthoz tartozó SharePoint tartalmi adatbázist helyre kell állítani, újra kell katalogizálni a SharePoint-farmot.
   >
   >
3. Kattintsonaz újrakatalogizálás elemre.

    ![DPM SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik a **felhő** -újrakatalogizálás állapota ablak.

    ![DPM SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot Sikeresre változik. Kattintson a **Bezárás** gombra.

    ![DPM SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kattintson a DPM- **helyreállítás** lapon látható SharePoint-objektumra a tartalom-adatbázis struktúrájának lekéréséhez. Kattintson a jobb gombbal az elemre, majd kattintson a **helyreállítás**elemre.

    ![DPM SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikkben korábban ismertetett helyreállítási lépéseket a SharePoint tartalmi adatbázis lemezről történő helyreállításához.

## <a name="next-steps"></a>További lépések
* További tudnivalók a SharePoint DPM-védelméről – lásd: [videó sorozat – DPM védelme](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Tekintse át [a System Center 2012 – Data Protection Manager kibocsátási megjegyzéseit](https://technet.microsoft.com/library/jj860415.aspx)
* A [System Center 2012 SP1 Data Protection Manager kiadási megjegyzései](https://technet.microsoft.com/library/jj860394.aspx)
