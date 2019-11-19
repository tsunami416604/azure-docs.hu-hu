---
title: SharePoint-farm biztonsági mentése Azure Stack
description: A Azure Backup Server használatával biztonsági mentést készíthet a SharePoint-adatairól, és visszaállíthatja azokat Azure Stackeken. Ez a cikk ismerteti a SharePoint-farm konfigurálásához szükséges információkat, hogy a kívánt adatok az Azure-ban is tárolhatók legyenek. A védett SharePoint-adatok a lemezről vagy az Azure-ból is visszaállíthatók.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 06d64be4f09c6fb6ed9dee34a0c7ba0b1bd785e6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172496"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>SharePoint-farm biztonsági mentése Azure Stack

Egy SharePoint-farmról biztonsági mentést készíthet Azure Stack, hogy a Microsoft Azure Backup Server (MABS) használatával Microsoft Azure a többi adatforrásról származó biztonsági mentéssel. Azure Backup rugalmasságot biztosít a biztonsági mentési ütemezésben napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és adatmegőrzési házirend-beállításokat biztosít a különböző biztonsági mentési pontokhoz. Lehetővé teszi továbbá a helyi lemezek másolását a gyors helyreállítási idejű célkitűzések (RTO) számára, valamint a másolatok Azure-ba történő tárolását gazdaságos, hosszú távú adatmegőrzés céljából.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek

A MABS Azure Backup a következő forgatókönyveket támogatja:

| Számítási feladat | Verzió | SharePoint-telepítés | Védelem és helyreállítás |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |Azure Stack virtuális gépként üzembe helyezett SharePoint <br> -------------- <br> SQL AlwaysOn | A SharePoint-farm helyreállítási lehetőségeinek biztosítása: helyreállítási Farm, adatbázis, valamint fájl-vagy listaelemek a lemezes helyreállítási pontokból.  Farm-és adatbázis-helyreállítás az Azure helyreállítási pontjairól. |

## <a name="before-you-start"></a>Előkészületek

A SharePoint-farmok Azure-ba történő biztonsági mentése előtt néhány dolgot meg kell erősítenie.

### <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy [telepítette és felkészítette a Azure Backup Server](backup-mabs-install-azure-stack.md) a munkaterhelések elleni védelem érdekében.

### <a name="protection-agent"></a>Védelmi ügynök

A Azure Backup ügynöknek telepítve kell lennie a SharePoint rendszert futtató kiszolgálón, a SQL Server futtató kiszolgálókon, valamint a SharePoint-farm részét képező összes többi kiszolgálón. A védelmi ügynök beállításával kapcsolatos további információkért lásd: a [védelmi ügynök](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx)beállítása.  Az egyetlen kivétel az, hogy csak egyetlen webes előtér-(WFE-) kiszolgálón telepíti az ügynököt. Azure Backup Server csak egy WFE-kiszolgálón lévő ügynökre van szüksége, hogy a védelemhez belépési pontként szolgáljon.

### <a name="sharepoint-farm"></a>SharePoint-farm

A farm minden 10 000 000 eleméhez legalább 2 GB szabad területnek kell lennie azon a köteten, ahol a MABS mappa található. Ez a terület a katalógus létrehozásához szükséges. Az adott elemek (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) helyreállításához a MABS létrehoz egy listát az egyes tartalom-adatbázisokban található URL-címekről. Az URL-címek listáját a **helyreállítható** elem ablaktáblán tekintheti meg a MABS felügyeleti konzol helyreállítási feladat területén.

### <a name="sql-server"></a>SQL Server

Azure Backup Server LocalSystem fiókként fut. SQL Server adatbázisok biztonsági mentéséhez a MABS rendszergazdai jogosultságokkal kell rendelkeznie az adott fiókhoz a SQL Server rendszert futtató kiszolgálón. A biztonsági mentés előtt állítsa be az NT AUTHORITY\SYSTEM-t a *sysadmin* értékre azon a kiszolgálón, amelyen a SQL Server fut.

Ha a SharePoint-farm SQL Server SQL Server aliasokkal konfigurált adatbázisokkal rendelkezik, akkor telepítse a SQL Server ügyféloldali összetevőit azon az előtér-webkiszolgálón, amelyet a MABS véd.

### <a name="whats-not-supported"></a>Nem támogatott műveletek

* A SharePoint-farmokat védő MABS nem védik a keresési indexeket vagy az Application Service-adatbázisokat. Ezen adatbázisok védelmét külön kell konfigurálnia.
* A MABS nem biztosít biztonsági mentést a SharePoint SQL Server-adatbázisokról, amelyek a kibővített fájlkiszolgáló (SOFS) megosztásokon futnak.

## <a name="configure-sharepoint-protection"></a>A SharePoint-védelem konfigurálása

Ahhoz, hogy a MABS-t a SharePoint számára is használhassa, konfigurálnia kell a SharePoint VSS-író szolgáltatást (WSS-író szolgáltatás) a **ConfigureSharePoint. exe**használatával.

A **ConfigureSharePoint. exe** a [MABS telepítési útvonala] \bin mappában található az előtér-webkiszolgálón. Ez az eszköz biztosítja a védelmi ügynököt a SharePoint-farm hitelesítő adataival. Egyetlen WFE-kiszolgálón futtatja azt. Ha több WFE-kiszolgálóval rendelkezik, válassza a csak egyet a védelmi csoportok konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS-író szolgáltatás konfigurálása

1. A WFE-kiszolgálón a parancssorban lépjen a [MABS telepítési hely] \bin\ mappát
2. Adja meg a ConfigureSharePoint-EnableSharePointProtection értéket.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ennek a fióknak a helyi rendszergazda csoport tagjának kell lennie a WFE-kiszolgálón. Ha a farm rendszergazdája nem helyi rendszergazda, a következő engedélyeket kell megadnia a WFE-kiszolgálón:
   * Adja meg az WSS_Admin_WPG csoport teljes hozzáférését a DPM mappához (% Program Files%\Microsoft Azure Backup\DPM).
   * Adjon olvasási hozzáférést a WSS_Admin_WPG csoportnak a DPM beállításkulcs (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager) számára.

> [!NOTE]
> Ha a SharePoint-farm rendszergazdai hitelesítő adatai módosulnak, újra kell futtatnia a ConfigureSharePoint. exe fájlt.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>SharePoint-farm biztonsági mentése a MABS használatával

Miután a korábban ismertetett módon konfigurálta a MABS-t és a SharePoint-farmot, a SharePoint védelme a MABS használatával lehetséges.

### <a name="to-protect-a-sharepoint-farm"></a>SharePoint-farmok megóvása

1. A MABS felügyeleti konzol **védelem** lapján kattintson az **új**elemre.
    ![új védelem lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az **új védelmi csoport létrehozása** varázsló **védelmi csoport típusának kiválasztása** lapján válassza a **kiszolgálók**elemet, majd kattintson a **tovább**gombra.

    ![Védelmi csoport típusának kiválasztása](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. A **csoporttagok kiválasztása** képernyőn jelölje be a védelemmel ellátni kívánt SharePoint-kiszolgáló jelölőnégyzetét, majd kattintson a **tovább**gombra.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Ha telepítve van a védelmi ügynök, a kiszolgáló megjelenik a varázslóban. A MABS a szerkezetét is megjeleníti. Mivel a ConfigureSharePoint. exe fájlt futtatta, a MABS kommunikál a SharePoint VSS-író szolgáltatással és a hozzá tartozó SQL Server adatbázisokkal, és felismeri a SharePoint-farm struktúráját, a társított tartalom-adatbázisokat és a hozzájuk tartozó elemeket.
   >
   >
4. Az **adatvédelmi módszer kiválasztása** lapon adja meg a **védelmi csoport**nevét, és válassza ki az előnyben részesített *védelmi módszereket*. Kattintson a **Tovább**gombra.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > A lemezes védelmi módszer segíti a rövid helyreállítási idejű célkitűzések teljesítését.
   >
   >
5. A **rövid távú célok megadása** oldalon válassza ki az előnyben részesített **megőrzési tartományt**, és határozza meg, hogy mikor történjen a biztonsági mentés.

    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Mivel a helyreállítás leggyakrabban az öt napnál régebbi adatok esetében szükséges, a lemezen lévő megőrzési időtartamot választottuk, és gondoskodunk arról, hogy a biztonsági mentés a nem üzemi időszakban történjen, ebben a példában.
   >
   >
6. Tekintse át a védelmi csoport számára lefoglalt tárterület-lemezterületet, majd kattintson a **tovább**gombra.
7. Minden védelmi csoport esetében a MABS lemezterületet foglal le a replikák tárolásához és kezeléséhez. Ekkor a MABS-nek létre kell hoznia a kijelölt adatmennyiség másolatát. Válassza ki, hogyan és mikor szeretné létrehozni a replikát, majd kattintson a **tovább**gombra.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Annak érdekében, hogy a hálózati forgalom ne legyen hatással, válasszon ki egy időpontot az üzemi órákon kívül.
   >
   >
8. A MABS biztosítja az adatok integritását, ha konzisztencia-ellenőrzéseket végez a replikán. Két lehetőség közül választhat. Megadhat egy ütemtervet a konzisztencia-ellenőrzések futtatásához, vagy a DPM automatikusan futtathat konzisztencia-ellenőrzéseket a replikán, ha az inkonzisztens lesz. Válassza ki a kívánt lehetőséget, majd kattintson a **tovább**gombra.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az **online védelem adatainak megadása** lapon válassza ki a védelemmel ellátni kívánt SharePoint-farmot, majd kattintson a **tovább**gombra.

    ![DPM SharePoint-Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az **online biztonsági mentési ütemterv megadása** lapon válassza ki az előnyben részesített ütemtervet, majd kattintson a **tovább**gombra.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > A MABS legfeljebb két napi biztonsági mentést biztosít az Azure-hoz a később elérhető legújabb lemezes biztonsági mentési pontról. A Azure Backup az [Azure Backup hálózati sávszélesség-szabályozás](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling)használatával is szabályozhatja azt a WAN-sávszélességet, amelyet a biztonsági mentéshez használhat a csúcs-és a távolsági órákban.
    >
    >
11. A kiválasztott biztonsági mentési ütemezéstől függően az **online adatmegőrzési szabály megadása** lapon válassza ki a napi, heti, havi és éves biztonsági mentési pontok megőrzési szabályát.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > A MABS egy nagyapa-apa-Son adatmegőrzési sémát használ, amelyben eltérő adatmegőrzési szabályt lehet kiválasztani a különböző biztonsági mentési pontokhoz.
    >
    >
12. A lemezhez hasonlóan a kezdeti hivatkozási pont replikáját is létre kell hozni az Azure-ban. Válassza ki a kívánt lehetőséget egy kezdeti biztonsági másolat Azure-ba való létrehozásához, majd kattintson a **tovább**gombra.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a kiválasztott beállításokat az **Összefoglalás** lapon, majd kattintson a **csoport létrehozása**elemre. A védelmi csoport létrehozása után sikert jelző üzenet jelenik meg.

    ![Összegzés](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>SharePoint-elem visszaállítása a lemezről a MABS használatával

A következő példában a SharePoint- *elem helyreállítása* véletlenül törölve lett, és vissza kell állítani.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzol**. A DPM által védett SharePoint-farmok a **védelem** lapon jelennek meg.

    ![MABS SharePoint-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Az elem helyreállításának megkezdéséhez válassza a **helyreállítás** lapot.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. A SharePointban a *SharePoint-elem* helyreállítása a helyreállítási pont tartományán belüli helyettesítő karakteres keresés használatával végezhető el.

    ![MABS SharePoint-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pontot a keresési eredmények közül, kattintson a jobb gombbal az elemre, majd válassza a **helyreállítás**lehetőséget.
5. A helyreállításhoz különböző helyreállítási pontokat is megkereshet, és kiválaszthatja a helyreállítani kívánt adatbázist vagy elemet. Válassza a **dátum > a helyreállítási idő**lehetőséget, majd válassza ki a megfelelő **adatbázist > SharePoint-farm > helyreállítási pont > elem**.

    ![MABS SharePoint-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson a jobb gombbal az elemre, majd **válassza a helyreállítás elemet** a **helyreállítási varázsló**megnyitásához. Kattintson a **Tovább**gombra.

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
9. Adja meg az átmeneti SQL Server példány helyét az adatbázis ideiglenes helyreállításához, és adjon meg egy átmeneti fájlmegosztást a MABS és a SharePoint rendszert futtató kiszolgálón az elem helyreállításához.

    ![Előkészítési Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    A MABS csatolja a SharePoint-elemeket futtató tartalom-adatbázist az ideiglenes SQL Server példányhoz. A tartalom-adatbázisból helyreállítja az elemet, és áthelyezi az átmeneti fájl helyére a MABS. Az átmeneti helyen található helyreállított elem most exportálva lesz a SharePoint-farm átmeneti helyére.

    ![Előkészítési Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza a **helyreállítási beállítások megadása lehetőséget**, majd alkalmazza a biztonsági beállításokat a SharePoint-farmra, vagy alkalmazza a helyreállítási pont biztonsági beállításait. Kattintson a **Tovább**gombra.

    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Megadhatja a hálózati sávszélesség használatának szabályozását. Ez a művelet a termelési idő alatt a termelési kiszolgálóra is hatással van.
    >
    >
11. Tekintse át az összegző információkat, majd kattintson a **helyreállítás** gombra a fájl helyreállításának megkezdéséhez.

    ![Helyreállítási összefoglalás](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza a **figyelés** fület a **MABS felügyeleti konzol** a helyreállítás **állapotának** megtekintéséhez.

    ![Helyreállítási állapot](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A fájl most vissza lett állítva. Frissítheti a SharePoint-webhelyet a visszaállított fájl vizsgálatához.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával

1. SharePoint tartalom-adatbázis helyreállításához tallózzon a különböző helyreállítási pontok között (ahogy az előzőekben látható), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![MABS SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. A SharePoint-katalógus elérhető adatainak megjelenítéséhez kattintson duplán a SharePoint helyreállítási pontra.

   > [!NOTE]
   > Mivel a SharePoint-farm védelme hosszú távú adatmegőrzést biztosít az Azure-ban, a MABS nem érhető el katalógus-információ (metadata). Ennek eredményeképpen, amikor egy időponthoz tartozó SharePoint tartalmi adatbázist helyre kell állítani, újra kell katalogizálni a SharePoint-farmot.
   >
   >
3. Kattintson az **újrakatalogizálás**elemre.

    ![MABS SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik a **felhő-újrakatalogizálás** állapota ablak.

    ![MABS SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot *sikeresre*változik. Kattintson a **Bezárás** gombra.

    ![MABS SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kattintson a MABS- **helyreállítás** lapon látható SharePoint-objektumra a tartalom-adatbázis struktúrájának lekéréséhez. Kattintson a jobb gombbal az elemre, majd kattintson a **helyreállítás**elemre.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikkben korábban ismertetett helyreállítási lépéseket a SharePoint tartalmi adatbázis lemezről történő helyreállításához.

## <a name="faqs"></a>Gyakori kérdések

K: helyreállítható egy SharePoint-elem az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva (a lemezes védelemmel)?<br>
Válasz: igen, az elem visszaállítható az eredeti SharePoint-webhelyre.

K: lehet helyreállítani egy SharePoint-adatbázist az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva?<br>
A: mivel a SharePoint-adatbázisok konfigurálva vannak az SQL-AlwaysOn, csak akkor módosíthatók, ha a rendelkezésre állási csoport el lett távolítva. Ennek eredményeképpen a MABS nem tudja visszaállítani az adatbázist az eredeti helyére. SQL Server-adatbázist egy másik SQL Server-példányra is helyreállíthatja.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [biztonsági másolat fájljait és az alkalmazásról](backup-mabs-files-applications-azure-stack.md) szóló cikket.
A [biztonsági mentési SQL Server Azure stack](backup-mabs-sql-azure-stack.md) cikkben talál.
