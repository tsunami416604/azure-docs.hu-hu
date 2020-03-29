---
title: SharePoint-farm biztonsági és biztonsági másolatot az Azure Stacken
description: Az Azure Backup Server segítségével biztonsági másolatot készíthet a SharePoint-adatokról az Azure Stacken. Ez a cikk a SharePoint-farm konfigurálásához tartalmaz információkat, hogy a kívánt adatok at tárolhassa az Azure-ban. A védett SharePoint-adatok at lemezről vagy az Azure-ból állíthatja vissza.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d080605022cadf121fa6be99c9758fe9c0d878ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673042"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>SharePoint-farm biztonsági és biztonsági másolatot az Azure Stacken

Az Azure Stacken lévő SharePoint-farmról a Microsoft Azure-ba készíthet biztonsági másolatot a Microsoft Azure Backup Server (MABS) használatával, ugyanúgy, mint más adatforrásokról. Az Azure Backup rugalmasságot biztosít a biztonsági mentésütemezésben napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és különböző biztonsági mentési pontok megőrzési házirend-beállításait biztosítja. Emellett lehetővé teszi a helyi lemezmásolatok gyors helyreállítási idejű célok (RTO) tárolására, valamint az Azure-ba való másolatok tárolására a gazdaságos, hosszú távú megőrzés érdekében.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek

Az Azure Backup for MABS a következő forgatókönyveket támogatja:

| Számítási feladat | Verzió | SharePoint-telepítés | Védelem és helyreállítás |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |Az Azure Stack virtuális gépként telepített SharePoint <br> -------------- <br> SQL AlwaysOn | SharePoint Farm helyreállítási beállítások védelme: Helyreállítási farm, adatbázis és fájl- vagy listaelem lemezhelyreállítási pontokról.  Farm és adatbázis-helyreállítás az Azure helyreállítási pontokról. |

## <a name="before-you-start"></a>Előkészületek

Néhány dolgot meg kell erősítenie, mielőtt biztonsági másolatot szeretne a SharePoint-farmról az Azure-ba.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt folytatná, győződjön meg arról, hogy [telepítette és előkészítette az Azure Backup Server](backup-mabs-install-azure-stack.md) a számítási feladatok védelme.

### <a name="protection-agent"></a>Védelmi ügynök

Az Azure Backup-ügynököt telepíteni kell a SharePointot futtató kiszolgálóra, az SQL Servert futtató kiszolgálókra és a SharePoint-farm részét használó összes többi kiszolgálóra. A védelmi ügynök beállításáról a Setup [Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)című témakörben talál további információt.  Az egyetlen kivétel, hogy az ügynököt csak egyetlen webes előtér-kiszolgálóra telepíti. Az Azure Backup Server szüksége van az ügynök egy WFE-kiszolgálón csak szolgál a belépési pont a védelem.

### <a name="sharepoint-farm"></a>SharePoint-farm

A farmminden 10 millió eleméhez legalább 2 GB-os területnek kell lennie azon a köteten, amelyben a MABS mappa található. Ez a terület a katalógus előállításához szükséges. A MABS számára bizonyos elemek (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyedi dokumentumok és listaelemek) helyreállításához a katalógusgenerálás létrehozza az egyes tartalom-adatbázisokban található URL-címek listáját. Az URL-címek listáját a MABS felügyeleti konzol **helyreállítási** feladatterületéntalálható helyreállítható elem ablaktáblában tekintheti meg.

### <a name="sql-server"></a>SQL Server

Az Azure Backup Server localsystem fiókként fut. Az SQL Server-adatbázisok biztonsági mentén a MABS-nek rendszergazdai jogosultságokra van szüksége az SQL Servert futtató kiszolgálóhoz. Állítsa az NT AUTHORITY\SYSTEM szolgáltatást az SQL Servert futtató kiszolgáló *nysadmin* szolgáltatására, mielőtt biztonsági másolatot állítana róla.

Ha a SharePoint-farm SQL Server-aliasokkal konfigurált SQL Server-adatbázisokkal rendelkezik, telepítse az SQL Server ügyfélösszetevőit a MABS által védett előtér-webkiszolgálóra.

### <a name="whats-not-supported"></a>Nem támogatott műveletek

* A SharePoint-farmot védő MABS nem védi a keresési indexeket és az alkalmazásszolgáltatás-adatbázisokat. Az adatbázisok védelmét külön kell konfigurálnia.
* A MABS nem nyújt biztonsági másolatot a kibővített fájlkiszolgáló (SOFS) megosztásokon tárolt SharePoint SQL Server-adatbázisokról.

## <a name="configure-sharepoint-protection"></a>SharePoint-védelem konfigurálása

Mielőtt a MABS segítségével megvédheti a SharePointot, konfigurálnia kell a SharePoint VSS Writer szolgáltatást (WSS Writer szolgáltatás) a **ConfigureSharePoint.exe**használatával.

A **ConfigureSharePoint.exe** fájl az előtér-webkiszolgáló [MABS telepítési útvonal]\bin mappájában található. Ez az eszköz biztosítja a védelmi ügynök számára a SharePoint-farm hitelesítő adatait. Egyetlen WFE-kiszolgálón futtatja. Ha több WFE-kiszolgálóval rendelkezik, csak egyet jelöljön ki a védelmi csoport konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS Writer szolgáltatás konfigurálása

1. A WFE-kiszolgálón a parancssorban nyissa meg a [MABS telepítési hely]\bin\
2. Írja be a ConfigureSharePoint -EnableSharePointProtection értéket.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ennek a fióknak a helyi rendszergazdák csoportja tagjának kell lennie a WFE-kiszolgálón. Ha a farm rendszergazdája nem helyi rendszergazda, adja meg a következő engedélyeket a WFE-kiszolgálón:
   * Adja meg a WSS_Admin_WPG csoport teljes hozzáférését a DPM mappának (%Program Files%\Microsoft Azure Backup\DPM).
   * Adjon hozzáférést a WSS_Admin_WPG csoport olvasási hozzáférésének a DPM rendszerleíró kulcshoz (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> A SharePoint-farm rendszergazdai hitelesítő adatainak változásakor újra kell futtatnia a ConfigureSharePoint.exe parancsot.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>SharePoint-farm biztonsági és biztonsági másolatot készül a MABS használatával

Miután konfigurálta a MABS-t és a SharePoint-farmot a korábbiaknak megfelelően, a SharePoint ot a MABS megvédheti.

### <a name="to-protect-a-sharepoint-farm"></a>SharePoint-farm védelme

1. A MABS felügyeleti konzol **Védelem** lapján kattintson az **Új**gombra.
    ![Új védelem lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az **Új védelmi csoport létrehozása** varázsló Védelmi **csoporttípus kiválasztása** lapján válassza a **Kiszolgálók**lehetőséget, majd kattintson a **Tovább**gombra.

    ![Védelmi csoport típusának kiválasztása](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. A **Csoporttagok kiválasztása** képernyőn jelölje be a védeni kívánt SharePoint-kiszolgáló **jelölőnégyzetét,** és kattintson a Tovább gombra.

    ![Csoporttagok kijelölése](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Ha a védelmi ügynök telepítve van, a kiszolgáló látható a varázslóban. MABS is mutatja a szerkezetét. A ConfigureSharePoint.exe fájl használata miatt a MABS kommunikál a SharePoint VSS Writer szolgáltatással és a hozzá tartozó SQL Server-adatbázisokkal, és felismeri a SharePoint farmstruktúrát, a kapcsolódó tartalom-adatbázisokat és a hozzájuk tartozó elemeket.
   >
   >
4. Az **Adatvédelmi módszer kiválasztása** lapon adja meg a **Védelmi csoport**nevét, és válassza ki a kívánt *védelmi módszereket.* Kattintson a **Tovább** gombra.

    ![Az adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > A lemezvédelmi módszer segít a rövid helyreállítási idő célok elérésében.
   >
   >
5. A **Rövid távú célok megadása** lapon válassza ki a kívánt **megőrzési tartományt,** és azonosítsa, ha biztonsági mentést szeretne készíteni.

    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Mivel a helyreállítás leggyakrabban öt napnál rövidebb adatok esetén szükséges, öt napos megőrzési tartományt választottunk ki a lemezen, és biztosítottuk, hogy a biztonsági mentés nem éles órákban történjen, ebben a példában.
   >
   >
6. Tekintse át a védelmi csoport számára lefoglalt tárterület-lemezterületet, majd kattintson a **Tovább**gombra.
7. A MABS minden védelmi csoport számára lemezterületet foglal le a replikák tárolásához és kezeléséhez. Ezen a ponton a MABS-nek létre kell hoznia a kijelölt adatok másolatát. Adja meg, hogyan és mikor szeretné létrehozni a kópiát, majd kattintson a **Tovább**gombra.

    ![A replika létrehozási módszerének kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Annak érdekében, hogy a hálózati forgalom ne legyen végrehajtva, válasszon ki egy időpontot a gyártási órákon kívül.
   >
   >
8. A MABS biztosítja az adatok integritását azáltal, hogy konzisztencia-ellenőrzéseket hajt végre a kópián. Két lehetőség áll rendelkezésre. Megadhat egy ütemezést a konzisztencia-ellenőrzések futtatásához, vagy a DPM automatikusan futtathatja a konzisztencia-ellenőrzéseket a replikán, amikor az inkonzisztenssé válik. Válassza ki a kívánt beállítást, majd kattintson a **Tovább gombra.**

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az **Online védelem adatainak megadása** lapon jelölje ki a védeni kívánt SharePoint-farmot, majd kattintson a **Tovább**gombra.

    ![DPM SharePoint-védelem1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az **Online biztonsági másolat ütemezésének megadása** lapon jelölje ki az előnyben részesített ütemezést, majd kattintson a **Tovább**gombra.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > A MABS legfeljebb két napi biztonsági mentést biztosít az Azure-ba az akkor elérhető legújabb lemezbiztonsági pontról. Az Azure Backup biztonsági mentési biztonsági másolat az [Azure Backup Network Throttling](backup-windows-with-mars-agent.md#enable-network-throttling)használatával is szabályozhatja a biztonsági mentések készítéséhez használható WAN-sávszélesség mennyiségét.
    >
    >
11. A kiválasztott biztonsági mentési ütemezéstől függően az **Online adatmegőrzési házirend megadása** lapon válassza ki a napi, heti, havi és éves biztonsági mentési pontok adatmegőrzési házirendjét.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > A MABS egy nagyapa-apa-fia megőrzési sémát használ, amelyben különböző adatmegőrzési házirend választható a különböző biztonsági mentési pontokhoz.
    >
    >
12. A lemezhez hasonlóan létre kell hozni egy kezdeti referenciapont-replika az Azure-ban. Válassza ki a kívánt beállítást, ha kezdeti biztonsági másolatot szeretne készíteni az Azure-ba, majd kattintson a **Tovább**gombra.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a kijelölt beállításokat az **Összegzés** lapon, majd kattintson a **Csoport létrehozása gombra.** A védelmi csoport létrehozása után sikeres üzenet jelenik meg.

    ![Összefoglalás](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>SharePoint-elem visszaállítása lemezről a MABS használatával

A következő példában a *SharePoint helyreállítása elemet* véletlenül törölték, és helyre kell állítani.
![MABS SharePoint-védelem4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzolt**. A DPM által védett összes SharePoint-farm a **Védelem** lapon látható.

    ![MABS SharePoint-védelem3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Az elem helyreállításának megkezdéséhez válassza a **Helyreállítás** lapot.

    ![MABS SharePoint-védelem5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. A *SharePoint-elem helyreállítása a SharePoint-elem helyreállítására* helyettesítő karakteralapú kereséssel kereshet a helyreállítási ponttartományon belül.

    ![MABS SharePoint-védelem6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pontot a keresési eredmények közül, kattintson a jobb gombbal az elemre, majd válassza a **Helyreállítás parancsot.**
5. A különböző helyreállítási pontok között is böngészhet, és kiválaszthat egy adatbázist vagy elemet a helyreállításhoz. Válassza **a Dátum > helyreállítási idő**lehetőséget, majd válassza ki a megfelelő **Adatbázis-> SharePoint-farmot > helyreállítási pont > elem**.

    ![MABS SharePoint-védelem7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson a jobb gombbal az elemre, majd a Helyreállítás **parancsra** kattintva nyissa meg a **Helyreállítási varázslót**. Kattintson a **Tovább** gombra.

    ![A helyreállítási beállítások áttekintése](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Jelölje ki a végrehajtani kívánt helyreállítási típust, majd kattintson a **Tovább**gombra.

    ![Helyreállítási típus](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A példában az **eredeti helyreállító** beállítás helyreállítja az elemet az eredeti SharePoint-webhelyre.
   >
   >
8. Jelölje ki a használni kívánt **helyreállítási folyamatot.**

   * Válassza **a Helyreállítás helyreállítási farm használata nélkül lehetőséget,** ha a SharePoint-farm nem változott, és megegyezik a visszaállításalatt lévő helyreállítási ponttal.
   * Válassza **a Helyreállítás helyreállítási farm használatával lehetőséget,** ha a SharePoint-farm megváltozott a helyreállítási pont létrehozása óta.

     ![Helyreállítási folyamat](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Adjon meg egy átmeneti SQL Server-példány helyet az adatbázis ideiglenes helyreállításához, és adjon átmeneti fájlmegosztást a MABS-en és a SharePointot futtató kiszolgálón az elem helyreállításához.

    ![Átmeneti hely1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    A MABS a SharePoint-elemet tároló tartalom-adatbázist az ideiglenes SQL Server-példányhoz csatolja. A tartalom-adatbázisból helyreállítja az elemet, és a MABS átmeneti fájlhelyére helyezi. Az átmeneti helyen lévő helyreállított elemet most exportálni kell a SharePoint-farm átmeneti helyére.

    ![Átmeneti hely2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza **a Helyreállítási beállítások megadása**lehetőséget, és alkalmazza a biztonsági beállításokat a SharePoint-farmra, vagy alkalmazza a helyreállítási pont biztonsági beállításait. Kattintson a **Tovább** gombra.

    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Dönthet úgy, hogy szabályozza a hálózati sávszélesség-használatot. Ez minimálisra csökkenti az éles kiszolgálóra gyakorolt hatást az üzemi órákban.
    >
    >
11. Tekintse át az összefoglaló információkat, majd kattintson a **Helyreállítás** gombra a fájl helyreállításának megkezdéséhez.

    ![Helyreállítási összefoglaló](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza a **Figyelés** lapot a **MABS felügyeleti konzolon** a helyreállítás **állapotának** megtekintéséhez.

    ![Helyreállítás állapota](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A fájl visszaállítása. A SharePoint-webhely frissítéséhez ellenőrizheti a visszaállított fájlt.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával

1. SharePoint-tartalomadatbázis helyreállításához tallózzon a különböző helyreállítási pontok között (ahogy azt korábban is látható), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![MABS SharePoint-védelem8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pontjára a SharePoint-katalógusra vonatkozó rendelkezésre álló információk megjelenítéséhez.

   > [!NOTE]
   > Mivel a SharePoint-farm hosszú távú megőrzése az Azure-ban védett, a MABS-en nem érhetők el katalógusadatok (metaadatok). Ennek eredményeképpen, amikor egy adott sharepoint-tartalom-adatbázist helyre kell állítotton, újra katalogizálnia kell a SharePoint-farmot.
   >
   >
3. Kattintson **az Újrakatalógus gombra.**

    ![MABS SharePoint-védelem10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik **a Felhőrekra katalogizálás** állapotablaka.

    ![MABS SharePoint-védelem11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot *sikeresre*változik. Kattintson a **Bezárás** gombra.

    ![MABS SharePoint-védelem12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. A tartalom-adatbázis szerkezetének leszámításához kattintson a **MABS-helyreállítás** lapon megjelenő SharePoint-objektumra. Kattintson a jobb gombbal az elemre, majd kattintson a **Helyreállítás parancsra.**

    ![MABS SharePoint-védelem13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikk korábbi helyreállítási lépéseit a SharePoint-tartalom-adatbázis lemezről való helyreállításához.

## <a name="faqs"></a>Gyakori kérdések

K: Helyreállítható kontavan egy SharePoint-elem az eredeti helyre, ha a SharePoint az SQL AlwaysOn (lemezen védelemmel) használatával van konfigurálva?<br>
A: Igen, az elem visszavezethető az eredeti SharePoint-webhelyre.

K: Helyreállítható kontami sharepoint-adatbázis az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva?<br>
V: Mivel a SharePoint-adatbázisok SQL AlwaysOn-ban vannak konfigurálva, csak akkor módosíthatók, ha a rendelkezésre állási csoportot eltávolították. Ennek eredményeképpen a MABS nem tudja visszaállítani az adatbázist az eredeti helyre. Az SQL Server-adatbázis egy másik SQL Server-példányba állítható helyre.

## <a name="next-steps"></a>További lépések

Tekintse meg a [Biztonsági másolat fájlok és alkalmazások](backup-mabs-files-applications-azure-stack.md) cikket.
Tekintse meg a [Backup SQL Server az Azure Stack](backup-mabs-sql-azure-stack.md) cikket.
