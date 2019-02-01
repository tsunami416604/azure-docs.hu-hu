---
title: A SharePoint-farmok biztonsági mentéséhez az Azure Stackben
description: Az Azure Backup Server használatával biztonsági mentése és visszaállítása a SharePoint-adatok az Azure Stacken. Ez a cikk az információkat a SharePoint-farm konfigurálásához, hogy a kívánt adatokat tárolhatja az Azure-ban. Visszaállíthatja a védett SharePoint-adatok a lemezről, vagy az Azure-ból.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: 84b3808e56ad318165eeec973a622c2e8747c633
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488332"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>A SharePoint-farmok biztonsági mentéséhez az Azure Stackben
Biztonsági mentést a SharePoint-farmok Azure Stack a Microsoft Azure-ba, hogy készítsen biztonsági mentést más adatforrásokhoz ugyanolyan módon a Microsoft Azure Backup Server (MABS) használatával. Az Azure Backup rugalmasan hozhat létre naponta a biztonsági mentési ütemezés, heti, havi vagy éves biztonsági mentési mutat, és különböző biztonsági mentési pontok megőrzése házirend lehetőséget kínál fel. Azt is lehetővé teszi a helyi lemez másolatot pedig gyors helyreállítási idő célkitűzései (RTO) tárolására, és tárolja a másolatokat az Azure-bA gazdaságos, hosszú távú megőrzésének.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint támogatott verziója, és a kapcsolódó védelmi forgatókönyvek
A MABS az Azure Backup a következő eseteket támogatja:

| Számítási feladat | Verzió | A SharePoint-környezet | Védelem és helyreállítás |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |Azure Stack virtuális gépként üzembe helyezett SharePoint <br> -------------- <br> SQL AlwaysOn | Helyreállítási beállítások SharePoint-Farm védelme: Helyreállítási farm, adatbázis és a lemez-helyreállítási pontokból fájl vagy listaelem.  Helyreállítási farm, adatbázis és az Azure helyreállítási pontokból. |

## <a name="before-you-start"></a>Előkészületek
Néhány dolgot meg kell erősítenie előtt készítsen biztonsági másolatot a SharePoint-farmok Azure-bA.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt folytatná, győződjön meg arról, hogy rendelkezik-e [telepítve van, és az Azure Backup Server készített](backup-mabs-install-azure-stack.md) munkaterhelések védelme érdekében.

### <a name="protection-agent"></a>Védelmi ügynök
Az Azure Backup ügynököt telepíteni kell a kiszolgálón, amelyen fut a SharePoint, SQL Server szolgáltatást futtató kiszolgálók és egyéb a SharePoint-farm részét képező kiszolgálók. A védelmi ügynök beállítása kapcsolatos további információkért lásd: [telepítő védelmi ügynök](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Az egyetlen kivétel, hogy telepítenie kell az ügynök csak egy egyetlen webes kezelőfelület (WFE) kiszolgálón. Az Azure Backup Server van szüksége az ügynök egy ELŐTÉR-webkiszolgálón csak a védelem belépési pontként szolgál.

### <a name="sharepoint-farm"></a>SharePoint-farm
A farm minden 10 millió eleme legalább 2 GB területet a köteten, ahol megtalálható a MABS mappában kell lennie. Ez a terület a katalógus előállításához szükség. A MABS helyreállítása elemeit (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) a katalógus-előállítás létrehoz az egyes tartalom-adatbázisokban tárolt URL-címek listáját. Az URL-címek listáját a helyreállítható elemek ablaktábláján tekintheti a **helyreállítási** MABS felügyeleti konzol feladatterületén.

### <a name="sql-server"></a>SQL Server
Az Azure Backup Server LocalSystem fiókként fut. Az SQL Server-adatbázisok biztonsági mentéséhez a MABS az SQL Servert futtató kiszolgáló fiók rendszergazdai jogosultságokkal kell rendelkeznie. NT AUTHORITY\SYSTEM beállítása *SysAdmin (rendszergazda)* a kiszolgálón, amelyen fut az SQL Server előtt készítsen biztonsági másolatot.

Ha a SharePoint-farm konfigurált SQL Server-aliasokkal megadott SQL Server-adatbázisok, telepítse az SQL Server ügyféloldali összetevőit az előtér-webkiszolgálón, amely védeni fogja a MABS.

### <a name="whats-not-supported"></a>Nem támogatott műveletek
* MABS, amely védelmet nyújt a SharePoint-farm nem nyújt védelmet a keresési indexek vagy az alkalmazásszolgáltatás-adatbázisok. Ezeknek az adatbázisoknak a védelmét külön konfigurálni kell.
* MABS-fájlmegosztásokon kibővíthető fájlkiszolgálófürt (SOFS) nem biztosít üzemeltetett SharePoint SQL Server-adatbázisok biztonsági mentése.

## <a name="configure-sharepoint-protection"></a>A SharePoint védelmének konfigurálása
MABS védelme a SharePoint használata előtt konfigurálnia kell a SharePoint VSS-író szolgáltatását (WSS-író szolgáltatás) használatával **ConfigureSharePoint.exe**.

Annak **ConfigureSharePoint.exe** az előtér-webkiszolgálón a [MABS telepítési elérési út] \bin mappában. Ezt az eszközt kínál a SharePoint-farm hitelesítő adatait a védelmi ügynököt. Egyetlen ELŐTÉR-webkiszolgálón futtassa. Ha több ELŐTÉR-webkiszolgáló, csak egyet válasszon ki egy védelmi csoport konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS-író szolgáltatás konfigurálása
1. Az ELŐTÉR-webkiszolgálón, a parancssort lépjen [MABS telepítési hely] \bin\
2. Adja meg a ConfigureSharePoint - EnableSharePointProtection.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ez a fiók az ELŐTÉR-webkiszolgálón a helyi Rendszergazdák csoport tagjának kell lennie. Ha a farm rendszergazdája nem helyi rendszergazda, adja meg a következő engedélyeket az ELŐTÉR-webkiszolgálón:
   * A WSS_Admin_WPG csoportnak teljes hozzáférést a DPM-mappa (% Program Files%\Microsoft Azure Backup\DPM).
   * A WSS_Admin_WPG csoportnak olvasási engedélyt a DPM beállításkulcsához (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Futtassa a ConfigureSharePoint.exe, amikor megváltozik a SharePoint-farm rendszergazdai hitelesítő adatai kell.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Biztonsági mentése egy SharePoint-farm MABS használatával
Miután konfigurálta a MABS és a korábbiakban leírtak a SharePoint-farm, SharePoint MABS is védi.

### <a name="to-protect-a-sharepoint-farm"></a>Egy SharePoint-farm védelme céljából
1. Az a **védelmi** fülre a MABS felügyeleti konzol, kattintson a **új**.
    ![Védelem új lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az a **védelmi csoport típusának kiválasztása** lapján a **új védelmi csoport létrehozása** varázsló, jelölje be **kiszolgálók**, és kattintson a **tovább**.

    ![Válassza ki a védelmi csoport típusa](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Az a **csoporttagok kiválasztása** lapon jelölje be a SharePoint-kiszolgáló védelmére, és kattintson a kívánt **tovább**.

    ![Csoporttagok kiválasztása](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > A védelmi ügynök telepítve a varázsló a kiszolgáló látható. MABS mutatja a struktúráját is. Futtatta a ConfigureSharePoint.exe, mert a MABS kommunikál a SharePoint VSS-író szolgáltatás és a megfelelő SQL Server-adatbázisok és, és a SharePoint-farm struktúráját, a kapcsolódó tartalom-adatbázisokhoz és megfelelő elemeket.
   >
   >
4. Az a **adatvédelmi módszer kiválasztása** lap, adja meg a nevét a **védelmi csoport**, és válassza ki a kívánt *védelmi módszerek*. Kattintson a **tovább**.

    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > A lemez védelmi módszert segítségével rövid idő helyreállítási célok eléréséhez.
   >
   >
5. Az a **rövid távú célok megadása** lapon, válassza ki a kívánt **megőrzési** és azonosítását, ha azt szeretné, hogy a biztonsági mentések.

    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Helyreállítási általában kevesebb mint öt napnál régebbi adatok szükség, mert azt lemezen öt napnyi megőrzési kiválasztva, és biztosítani, hogy történik-e a biztonsági mentés során nem éles óra, ebben a példában.
   >
   >
6. Tekintse át a tárolókészletet a védelmi csoport számára lefoglalt lemezterület, majd kattintson **tovább**.
7. Minden egyes védelmi csoporthoz tartozó MABS tárolhatja és kezelheti a replikák lemezterületet foglal le. Ezen a ponton MABS létre kell hoznia egy másolatot a kijelölt adatokat. Válassza ki, hogyan és mikor kívánja elvégezni a létrehozott replika, majd kattintson a **tovább**.

    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Győződjön meg arról, hogy a hálózati forgalom nem befolyásolja, válassza ki a megfelelő üzemi óra kívül.
   >
   >
8. MABS a replika konzisztencia-ellenőrzések végrehajtásával biztosíthatja az adatok integritását. Két elérhető lehetőség van. Meghatározhatja a konzisztencia-ellenőrzések futtatása ütemezés szerint, vagy a DPM futtatható a replikán automatikusan konzisztencia-ellenőrzéseket, amikor inkonzisztenssé válik. Válassza ki a kívánt beállítást, és kattintson a **tovább**.

    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az a **Online védelem adatainak megadása** lapra, jelölje be a SharePoint-farm védelmét, és kattintson a kívánt **tovább**.

    ![A DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az a **Online biztonsági mentési ütemezés megadása** lapon válassza ki a kívánt ütemezést, és kattintson a **tovább**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS egy legfeljebb két napi biztonsági mentést biztosít majd elérhető legújabb lemez biztonsági mentési pont az Azure-bA. Az Azure Backup használatával használható biztonsági másolatok a csúcsidőre és a csúcsidőn a WAN sávszélesség mennyiségét is szabályozhatja [Azure Backup hálózati sávszélesség-szabályozás](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. A kiválasztott, a biztonsági mentési ütemezés függően a **Online adatmegőrzési szabály megadása** lapra, jelölje be a megtartási házirend napi, heti, havi és éves biztonsági mentési ponthoz.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS a nagyapa-apa-fiú megőrzési séma, amelyben a különböző adatmegőrzési megadásához használja a különböző biztonsági mentési pontok.
    >
    >
12. Lemez hasonlóan egy kezdeti hivatkozási pont replika kell létrehozni az Azure-ban. Válassza ki a kívánt beállítást, hozzon létre egy kezdeti biztonsági másolatot az Azure-ba, és kattintson a **tovább**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a beállításokat a a **összefoglalás** lapon, és kattintson a **csoport létrehozása**. A védelmi csoport létrehozása után látni fogja a sikert jelző üzenet.

    ![Összegzés](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Állítsa vissza a SharePoint-elem lemezről MABS használatával
A következő példában a *helyreállítás SharePoint-elem* véletlenül törölve lett, és kell helyreállítani.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzol**. Minden DPM által védett SharePoint-farmok jelennek meg a **védelmi** fülre.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Az elem helyreállítása első lépésként válassza ki a **helyreállítási** fülre.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Kereshet a SharePoint *helyreállítás SharePoint-elem* kereséssel egy helyettesítő karakteres alapú belül egy helyreállítási pont a tartományon.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pontot a keresési eredmények között, kattintson a jobb gombbal a cikket, és válassza **helyreállítása**.
5. Böngészhet a különböző helyreállítási pontok is, és válasszon ki egy adatbázist vagy elemek helyreállítása. Válassza ki **dátum > helyreállítási idő**, és válassza ki a megfelelő **adatbázis > SharePoint-farm > helyreállítási pont > elem**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson jobb gombbal az elemre, és válassza **helyreállítása** megnyitásához a **helyreállítási varázsló**. Kattintson a **tovább**.

    ![Helyreállítási beállítások áttekintése](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Válasszon végrehajtani, és kattintson a kívánt helyreállítási **tovább**.

    ![Helyreállítási típus](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A kijelölt **helyreállítás az eredeti** példában állítja helyre az elemet az eredeti SharePoint-webhelyre.
   >
   >
8. Válassza ki a **helyreállítási folyamat** , amelyet használni szeretne.

   * Válassza ki **helyreállítás helyreállítási farm nélkül** Ha a SharePoint-farm nem változott, és ugyanaz, mint a helyreállítási pont kerül.
   * Válassza ki **helyreállításához a helyreállítási farm** Ha a SharePoint-farm megváltozott, a helyreállítási pont létrehozása óta.

     ![A helyreállítási folyamat](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Adjon meg egy átmeneti SQL Server példány helyet ideiglenesen állítsa helyre az adatbázist, és adja meg a átmeneti fájlmegosztást a MABS és a SharePoint, az elem helyreállítása futtató kiszolgálón.

    ![Átmeneti Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS csatolja a tartalom-adatbázist, amelyen a SharePoint-elem az ideiglenes SQL Server-példányra. A tartalom-adatbázist, az azt állítja helyre az elemet, és helyezi az átmeneti tárolási helye a MABS. A helyreállított elem, amely az átmeneti helyen mostantól exportálható a SharePoint-farm átmeneti tárolási helye van szüksége.

    ![Átmeneti Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza ki **helyreállítási beállítások megadása**, és a alkalmazni a SharePoint-farm biztonsági beállításokat, vagy a helyreállítási pont biztonsági beállításainak alkalmazása. Kattintson a **tovább**.

    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Ha szeretné, a hálózati sávszélesség használatának szabályozását. Ez minimalizálja az üzemi kiszolgálót, éles órában.
    >
    >
11. Tekintse át az összefoglaló adatokat, és kattintson a **helyreállítása** megkezdéséhez a fájl helyreállítását.

    ![A helyreállítási összegzése](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza ki a **figyelés** lapján a **MABS felügyeleti konzol** megtekintéséhez a **állapot** a helyreállítási.

    ![Helyreállítás állapota](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A fájl most helyreáll. A SharePoint-webhelyen ellenőrizheti a visszaállított fájl is frissítheti.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával
1. Egy SharePoint tartalom-adatbázis helyreállításához, böngészhet a különböző helyreállítási pontok (ahogyan korábban), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pont megjelenítése a rendelkezésre álló SharePoint-katalógus adatait.

   > [!NOTE]
   > A SharePoint-farm védelme az Azure-ban hosszú távú megőrzésének, mert nem katalógus (metadata) áll rendelkezésre információ a MABS. Ennek eredményeképpen minden alkalommal, amikor egy időponthoz – SharePoint tartalom-adatbázist kell állítható helyre, kell újra a katalógus a SharePoint-farm.
   >
   >
3. Kattintson a **katalogizálja újra**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A **felhőalapú újrakatalogizálása** állapot ablak nyílik meg.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Katalogizálása befejezése után a állapota *sikeres*. Kattintson a **Bezárás** gombra.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kattintson a SharePoint-objektum látható a MABS **helyreállítási** fülre, és a tartalom-adatbázist struktúra beolvasása. Kattintson jobb gombbal az elemre, és kattintson a **helyreállítása**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a [helyreállítási lépéseket a cikk korábbi részeiben](#restore-a-sharepoint-item-from-disk-using-dpm) lemezről való helyreállításhoz egy SharePoint tartalom-adatbázist.

## <a name="faqs"></a>Gyakori kérdések
KÉRDÉS: Tudok helyreállítani egy SharePoint-elem az eredeti helyre, ha a SharePoint SQL AlwaysOn (a védelem a lemezen) használatával van konfigurálva?<br>
V: Igen, az elem visszaállíthatja az eredeti SharePoint-webhelyre.

KÉRDÉS: Tudok helyreállítani egy SharePoint-adatbázis az eredeti helyre, ha a SharePoint SQL AlwaysOn használatára van konfigurálva?<br>
V: A SharePoint-adatbázisok az SQL AlwaysOn vannak konfigurálva, mivel azok nem lehet módosítani, ha a rendszer eltávolítja a rendelkezésre állási csoport. Ennek eredményeképpen az MABS nem állítható vissza az adatbázis az eredeti helyre. SQL Server-adatbázis egy másik SQL Server-példányra helyreállítható.

## <a name="next-steps"></a>További lépések

Tekintse meg a [fájlok és alkalmazások biztonsági másolatának](backup-mabs-files-applications-azure-stack.md) cikk.
Tekintse meg a [biztonsági mentés SQL Server az Azure Stacken](backup-mabs-sql-azure-stack.md) cikk.
