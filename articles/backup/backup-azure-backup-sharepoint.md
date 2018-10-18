---
title: A DPM vagy az Azure Backup server védelme a SharePoint-farmok Azure-bA
description: Ez a cikk a DPM vagy az Azure Backup server védelmi egy SharePoint-farm áttekintést nyújt az Azure-bA
services: backup
author: adigan
manager: Nkolli1
ms.service: backup
ms.topic: conceptual
ms.date: 09/29/2016
ms.author: adigan
ms.openlocfilehash: 7331b1c99425500b58d186cedab1e83dd20e3684
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389819"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>SharePoint-farm biztonsági mentése az Azure-ba
Készítsen biztonsági másolatot egy SharePoint-farm a Microsoft Azure-ba, hogy készítsen biztonsági mentést más adatforrásokhoz ugyanolyan módon a System Center Data Protection Manager (DPM) használatával. Az Azure Backup rugalmasan hozhat létre naponta a biztonsági mentési ütemezés, heti, havi vagy éves biztonsági mentési mutat, és különböző biztonsági mentési pontok megőrzése házirend lehetőséget kínál fel. A DPM lehetővé teszi a helyi lemez másolatot pedig gyors helyreállítási idő célkitűzései (RTO) tárolására, és tárolja a másolatokat az Azure-bA gazdaságos, hosszú távú megőrzésének.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint támogatott verziója, és a kapcsolódó védelmi forgatókönyvek
Az Azure Backup a DPM a következő eseteket támogatja:

| Számítási feladat | Verzió | A SharePoint-környezet | A DPM központi telepítési típus | DPM – System Center 2012 R2 | Védelem és helyreállítás |
| --- | --- | --- | --- | --- | --- |
| SharePoint |A SharePoint 2013, SharePoint 2007, 2010 SharePoint SharePoint 3.0-s |Fizikai kiszolgálóként vagy Hyper-V vagy VMware virtuális gép üzembe helyezett SharePoint <br> -------------- <br> Az SQL AlwaysOn |Fizikai kiszolgáló vagy a helyszíni Hyper-V virtuális gép |Az 5. kumulatív támogatja az Azure backup |Helyreállítási beállítások SharePoint-Farm védelme: helyreállítási farm, adatbázis és a fájl vagy listaelem, lemez-helyreállítási pontokból.  Helyreállítási farm, adatbázis és az Azure helyreállítási pontokból. |

## <a name="before-you-start"></a>Előkészületek
Néhány dolgot meg kell erősítenie előtt készítsen biztonsági másolatot a SharePoint-farmok Azure-bA.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt folytatná, győződjön meg arról, hogy teljesül-e az összes a [a Microsoft Azure Backup előfeltételei](backup-azure-dpm-introduction.md#prerequisites) munkaterhelések védelme érdekében. Egyes feladatok vonatkozó Előfeltételek a következők: backup-tároló létrehozása, a tároló hitelesítő adatainak letöltése, Azure Backup szolgáltatás ügynökének telepítése és a DPM vagy az Azure Backup Server regisztrálja a tárolóban.

### <a name="dpm-agent"></a>DPM-ügynök
A DPM-ügynök telepítve kell lennie a kiszolgálón, amelyen fut a SharePoint, SQL Server szolgáltatást futtató kiszolgálók és egyéb a SharePoint-farm részét képező kiszolgálók. A védelmi ügynök beállítása kapcsolatos további információkért lásd: [telepítő védelmi ügynök](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Az egyetlen kivétel, hogy telepítenie kell az ügynök csak egy egyetlen webes kezelőfelület (WFE) kiszolgálón. DPM-nek a védelem belépési pontként kiszolgálása érdekében csak egy WFE-kiszolgálón az ügynök.

### <a name="sharepoint-farm"></a>SharePoint-farm
A farm minden 10 millió eleme legalább 2 GB területet a köteten, ahol a DPM mappája megtalálható kell lennie. Ez a terület a katalógus előállításához szükség. A DPM helyreállítása elemeit (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) a katalógus-előállítás létrehoz az egyes tartalom-adatbázisokban tárolt URL-címek listáját. Az URL-címek listáját a helyreállítható elemek ablaktábláján tekintheti a **helyreállítási** feladatterületén a DPM felügyeleti konzolon.

### <a name="sql-server"></a>SQL Server
A DPM LocalSystem fiókként fut. Az SQL Server-adatbázisok biztonsági mentéséhez a DPM az SQL Servert futtató kiszolgáló fiók rendszergazdai jogosultságokkal kell rendelkeznie. NT AUTHORITY\SYSTEM beállítása *SysAdmin (rendszergazda)* a kiszolgálón, amelyen fut az SQL Server előtt készítsen biztonsági másolatot.

Ha a SharePoint-farm konfigurált SQL Server-aliasokkal megadott SQL Server-adatbázisok, telepítse az SQL Server ügyféloldali összetevőit a DPM által védendő előtér-webkiszolgálón.

### <a name="sharepoint-server"></a>SharePoint Server
Bár a teljesítmény számos tényezőtől, például a SharePoint-farm mérete attól függ, általános útmutatásként egy DPM-kiszolgáló is 25 TB SharePoint-farm védelméhez.

### <a name="dpm-update-rollup-5"></a>A DPM 5. kumulatív frissítése
Az Azure-bA a SharePoint-farm védelmének megkezdéséhez telepíteni szeretné a DPM-kumulatív frissítés 5-ös vagy újabb. 5. kumulatív frissítése lehetővé teszi az Azure-bA a SharePoint-farm védelméhez, ha a farm SQL AlwaysOn használatára van konfigurálva.
További információkért lásd a következő blogbejegyzésben, amely bemutatja a post [DPM 5. kumulatív](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Nem támogatott műveletek
* A DPM által védett SharePoint-farm nem nyújt védelmet a keresési indexek vagy az alkalmazásszolgáltatás-adatbázisok. Ezeknek az adatbázisoknak a védelmét külön konfigurálni kell.
* A DPM nem nyújt üzemeltetett SharePoint SQL Server-adatbázisok biztonsági mentése a kibővített fájlkiszolgáló (SOFS) kiszolgálói megosztásokon található.

## <a name="configure-sharepoint-protection"></a>A SharePoint védelmének konfigurálása
A DPM védelme a SharePoint használata előtt konfigurálnia kell a SharePoint VSS-író szolgáltatását (WSS-író szolgáltatás) használatával **ConfigureSharePoint.exe**.

Annak **ConfigureSharePoint.exe** az előtér-webkiszolgálón a [DPM telepítési útvonal] \bin mappában. Ezt az eszközt kínál a SharePoint-farm hitelesítő adatait a védelmi ügynököt. Egyetlen ELŐTÉR-webkiszolgálón futtassa. Ha több ELŐTÉR-webkiszolgáló, csak egyet válasszon ki egy védelmi csoport konfigurálásakor.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>A SharePoint VSS-író szolgáltatás konfigurálása
1. Az ELŐTÉR-webkiszolgálón, a parancssort lépjen [DPM telepítési hely] \bin\
2. Adja meg a ConfigureSharePoint - EnableSharePointProtection.
3. Adja meg a farm rendszergazdai hitelesítő adatait. Ez a fiók az ELŐTÉR-webkiszolgálón a helyi Rendszergazdák csoport tagjának kell lennie. Ha a farm rendszergazdája nem helyi rendszergazda, adja meg a következő engedélyeket az ELŐTÉR-webkiszolgálón:
   * A WSS_Admin_WPG csoportnak teljes hozzáférést a DPM-mappa (% Program Files%\Microsoft Data Protection Manager\DPM).
   * A WSS_Admin_WPG csoportnak olvasási engedélyt a DPM beállításkulcsához (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Futtassa a ConfigureSharePoint.exe, amikor megváltozik a SharePoint-farm rendszergazdai hitelesítő adatai kell.
> 
> 

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>A SharePoint-farmok biztonsági mentéséhez a DPM használatával
Miután konfigurálta a DPM és a korábbiakban leírtak a SharePoint-farm, SharePoint DPM által védett is.

### <a name="to-protect-a-sharepoint-farm"></a>Egy SharePoint-farm védelme céljából
1. A a **védelmi** lapon kattintson a DPM felügyeleti konzol **új**.
    ![Védelem új lap](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Az a **védelmi csoport típusának kiválasztása** lapján a **új védelmi csoport létrehozása** varázsló, jelölje be **kiszolgálók**, és kattintson a **tovább**.
   
    ![Válassza ki a védelmi csoport típusa](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Az a **csoporttagok kiválasztása** lapon jelölje be a SharePoint-kiszolgáló védelmére, és kattintson a kívánt **tovább**.
   
    ![Csoporttagok kiválasztása](./media/backup-azure-backup-sharepoint/select-group-members2.png)
   
   > [!NOTE]
   > A DPM-ügynök telepítve a varázsló a kiszolgáló látható. A DPM megjeleníti azok struktúrája is. Futtatta a ConfigureSharePoint.exe, mert a DPM a SharePoint VSS-író szolgáltatás és a megfelelő SQL Server-adatbázisok kommunikál, és, és a SharePoint-farm struktúráját, a kapcsolódó tartalom-adatbázisokhoz és megfelelő elemeket.
   > 
   > 
4. Az a **adatvédelmi módszer kiválasztása** lap, adja meg a nevét a **védelmi csoport**, és válassza ki a kívánt *védelmi módszerek*. Kattintson a **Tovább** gombra.
   
    ![Adatvédelmi módszer kiválasztása](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)
   
   > [!NOTE]
   > A lemez védelmi módszert segítségével rövid idő helyreállítási célok eléréséhez. Azure-ban egy gazdaságos, hosszú távú védelem a célon, mint szalagok. További információkért lásd: [használata az Azure Backup a szalagos infrastruktúra lecseréléséhez](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   > 
   > 
5. Az a **rövid távú célok megadása** lapon, válassza ki a kívánt **megőrzési** és azonosítását, ha azt szeretné, hogy a biztonsági mentések.
   
    ![Rövid távú célok megadása](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)
   
   > [!NOTE]
   > Helyreállítási általában kevesebb mint öt napnál régebbi adatok szükség, mert azt lemezen öt napnyi megőrzési kiválasztva, és biztosítani, hogy történik-e a biztonsági mentés során nem éles óra, ebben a példában.
   > 
   > 
6. Tekintse át a tárolókészletet a védelmi csoport számára lefoglalt lemezterület, majd kattintson **tovább**.
7. Minden védelmi csoport DPM lefoglalja a szükséges lemezterületet tárolhatja és kezelheti a replikákat. A DPM ezen a ponton a kiválasztott adatok másolatának kell létrehoznia. Válassza ki, hogyan és mikor kívánja elvégezni a létrehozott replika, majd kattintson a **tovább**.
   
    ![Replika-létrehozási módszer kiválasztása](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)
   
   > [!NOTE]
   > Győződjön meg arról, hogy a hálózati forgalom nem befolyásolja, válassza ki a megfelelő üzemi óra kívül.
   > 
   > 
8. A DPM konzisztencia-ellenőrzést a replika a végrehajtásával biztosíthatja az adatok integritását. Két elérhető lehetőség van. Meghatározhatja a konzisztencia-ellenőrzések futtatása ütemezés szerint, vagy a DPM futtatható a replikán automatikusan konzisztencia-ellenőrzéseket, amikor inkonzisztenssé válik. Válassza ki a kívánt beállítást, és kattintson a **tovább**.
   
    ![Konzisztencia-ellenőrzés](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Az a **Online védelem adatainak megadása** lapra, jelölje be a SharePoint-farm védelmét, és kattintson a kívánt **tovább**.
   
    ![A DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Az a **Online biztonsági mentési ütemezés megadása** lapon válassza ki a kívánt ütemezést, és kattintson a **tovább**.
    
    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)
    
    > [!NOTE]
    > A DPM az Azure-bA egy legfeljebb két napi biztonsági mentést biztosít különböző időpontokban. Az Azure Backup használatával használható biztonsági másolatok a csúcsidőre és a csúcsidőn a WAN sávszélesség mennyiségét is szabályozhatja [Azure Backup hálózati sávszélesség-szabályozás](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    > 
    > 
11. A kiválasztott, a biztonsági mentési ütemezés függően a **Online adatmegőrzési szabály megadása** lapra, jelölje be a megtartási házirend napi, heti, havi és éves biztonsági mentési ponthoz.
    
    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)
    
    > [!NOTE]
    > A DPM a nagyapa-apa-fiú megőrzési séma, amelyben a különböző adatmegőrzési megadásához használja a különböző biztonsági mentési ponthoz.
    > 
    > 
12. Lemez hasonlóan egy kezdeti hivatkozási pont replika kell létrehozni az Azure-ban. Válassza ki a kívánt beállítást, hozzon létre egy kezdeti biztonsági másolatot az Azure-ba, és kattintson a **tovább**.
    
    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Tekintse át a beállításokat a a **összefoglalás** lapon, és kattintson a **csoport létrehozása**. A védelmi csoport létrehozása után látni fogja a sikert jelző üzenet.
    
    ![Összegzés](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Állítsa vissza a SharePoint-elem lemezről DPM használatával
A következő példában a *helyreállítás SharePoint-elem* véletlenül törölve lett, és kell helyreállítani.
![A DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **DPM felügyeleti konzol**. Minden DPM által védett SharePoint-farmok jelennek meg a **védelmi** fülre.
   
    ![A DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Az elem helyreállítása első lépésként válassza ki a **helyreállítási** fülre.
   
    ![A DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Kereshet a SharePoint *helyreállítás SharePoint-elem* kereséssel egy helyettesítő karakteres alapú belül egy helyreállítási pont a tartományon.
   
    ![A DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pontot a keresési eredmények között, kattintson a jobb gombbal a cikket, és válassza **helyreállítása**.
5. Böngészhet a különböző helyreállítási pontok is, és válasszon ki egy adatbázist vagy elemek helyreállítása. Válassza ki **dátum > helyreállítási idő**, és válassza ki a megfelelő **adatbázis > SharePoint-farm > helyreállítási pont > elem**.
   
    ![A DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson jobb gombbal az elemre, és válassza **helyreállítása** megnyitásához a **helyreállítási varázsló**. Kattintson a **Tovább** gombra.
   
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
9. Adjon meg egy átmeneti SQL Server példány helyet ideiglenesen állítsa helyre az adatbázist, és adja meg a átmeneti fájlmegosztást a DPM-kiszolgáló és a SharePoint, az elem helyreállítása futtató kiszolgálón.
   
    ![Átmeneti Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)
   
    A DPM csatolja a tartalom-adatbázist, amelyen a SharePoint-elem az ideiglenes SQL Server-példányra. A tartalom-adatbázist a DPM-kiszolgálóra állítja helyre az elemet és az átmeneti tárolási helye a DPM-kiszolgálón helyezi. A helyreállított elem, amely az átmeneti helyen, a DPM-kiszolgáló mostantól exportálható a SharePoint-farm átmeneti tárolási helye van szüksége.
   
    ![Átmeneti Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza ki **helyreállítási beállítások megadása**, és a alkalmazni a SharePoint-farm biztonsági beállításokat, vagy a helyreállítási pont biztonsági beállításainak alkalmazása. Kattintson a **Tovább** gombra.
    
    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)
    
    > [!NOTE]
    > Ha szeretné, a hálózati sávszélesség használatának szabályozását. Ez minimalizálja az üzemi kiszolgálót, éles órában.
    > 
    > 
11. Tekintse át az összefoglaló adatokat, és kattintson a **helyreállítása** megkezdéséhez a fájl helyreállítását.
    
    ![A helyreállítási összegzése](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza ki a **figyelés** lapján a **DPM felügyeleti konzol** megtekintéséhez a **állapot** a helyreállítási.
    
    ![Helyreállítás állapota](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)
    
    > [!NOTE]
    > A fájl most helyreáll. A SharePoint-webhelyen ellenőrizheti a visszaállított fájl is frissítheti.
    > 
    > 

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával
1. Egy SharePoint tartalom-adatbázis helyreállításához, böngészhet a különböző helyreállítási pontok (ahogyan korábban), és válassza ki a visszaállítani kívánt helyreállítási pontot.
   
    ![A DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pont megjelenítése a rendelkezésre álló SharePoint-katalógus adatait.
   
   > [!NOTE]
   > A SharePoint-farm védelme az Azure-ban hosszú távú megőrzésének, mert nincs katalógus információkkal (metaadatokkal) nem érhető el a DPM-kiszolgálón. Ennek eredményeképpen minden alkalommal, amikor egy időponthoz – SharePoint tartalom-adatbázist kell állítható helyre, kell újra a katalógus a SharePoint-farm.
   > 
   > 
3. Kattintson a **katalogizálja újra**.
   
    ![A DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)
   
    A **felhőalapú újrakatalogizálása** állapot ablak nyílik meg.
   
    ![A DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)
   
    Katalogizálása befejezése után a állapota *sikeres*. Kattintson a **Bezárás** gombra.
   
    ![A DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kattintson a SharePoint-objektumra, a DPM-ben látható **helyreállítási** fülre, és a tartalom-adatbázist struktúra beolvasása. Kattintson jobb gombbal az elemre, és kattintson a **helyreállítása**.
   
    ![A DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a [helyreállítási lépéseket a cikk korábbi részeiben](#restore-a-sharepoint-item-from-disk-using-dpm) lemezről való helyreállításhoz egy SharePoint tartalom-adatbázist.

## <a name="faqs"></a>Gyakori kérdések
K: a DPM mely verziói támogatják az SQL Server 2014 és az SQL 2012 (SP2)?<br>
V: A DPM 2012 R2 4. kumulatív frissítés a is támogatja.

K: helyreállíthatók a SharePoint-elem, az eredeti helyre, ha a SharePoint SQL AlwaysOn (a védelem a lemezen) használatával van konfigurálva?<br>
V: Igen, az elem visszaállíthatja az eredeti SharePoint-webhelyre.

K: tudok helyreállítani egy SharePoint-adatbázis az eredeti helyre, ha a SharePoint SQL AlwaysOn használatára van konfigurálva?<br>
V:, mert az SQL AlwaysOn konfigurálása a SharePoint-adatbázisok, nem lehet módosítani, ha a rendszer eltávolítja a rendelkezésre állási csoportot. Ennek eredményeképpen az a DPM nem tudja visszaállítani az adatbázis az eredeti helyre. SQL Server-adatbázis egy másik SQL Server-példányra helyreállítható.

## <a name="next-steps"></a>További lépések
* További tudnivalók a SharePoint védelme a DPM - lásd [videók (sorozat) – a DPM védelmi SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Felülvizsgálat [kibocsátási megjegyzések a System Center 2012 – Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Felülvizsgálat [kibocsátási megjegyzések a Data Protection Manager a System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

