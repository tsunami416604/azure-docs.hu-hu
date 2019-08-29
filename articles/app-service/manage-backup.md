---
title: Alkalmazás biztonsági mentése – Azure App Service
description: Ismerje meg, hogyan hozhat létre biztonsági másolatokat az alkalmazásairól Azure App Serviceban.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d153238c58d72874ddbf7979e68423e21ea7c020
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073158"
---
# <a name="back-up-your-app-in-azure"></a>Adatok biztonsági mentése az Azure-ban
A [Azure app Service](overview.md) biztonsági mentési és visszaállítási funkciója lehetővé teszi, hogy egyszerűen hozza létre az alkalmazások biztonsági másolatait manuálisan vagy ütemezés szerint.  A biztonsági mentések úgy konfigurálhatók, hogy határozatlan ideig legyenek megőrizve. Az alkalmazást visszaállíthatja egy korábbi állapot pillanatképére a meglévő alkalmazás felülírásával vagy egy másik alkalmazásra való visszaállítással.

Az alkalmazások biztonsági mentésből való visszaállításáról az [alkalmazás visszaállítása az Azure-ban](web-sites-restore.md)című témakörben olvashat bővebben.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Mi történik a biztonsági mentéssel
App Service a következő információk biztonsági mentését végezheti egy Azure Storage-fiókba és-tárolóba, amelyet az alkalmazás használatára konfigurált. 

* Alkalmazás-beállítások
* Fájl tartalma
* Az alkalmazáshoz csatlakoztatott adatbázis

A biztonsági mentési funkció a következő adatbázis-megoldásokat támogatja: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [beépített mySQL](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Minden biztonsági mentés az alkalmazás teljes offline példánya, nem Növekményes frissítés.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Követelmények és korlátozások
* A biztonsági mentési és visszaállítási funkció használatához a App Service tervnek a **standard** vagy a **prémium** szinten kell lennie. További információ a App Service terv magasabb szintű használatára való méretezéséről: alkalmazás felskálázása [Az Azure-ban](manage-scale-up.md).  
  A **prémium** szint nagyobb számú napi biztonsági mentést tesz lehetővé, mint a **standard** szint.
* Szüksége lesz egy Azure Storage-fiókra és-tárolóra abban az előfizetésben, amelyben a biztonsági mentéshez használni kívánt alkalmazás található. Az Azure Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* A biztonsági másolatok akár 10 GB-nyi alkalmazás-és adatbázis-tartalommal is rendelkezhetnek. Ha a biztonsági másolat mérete meghaladja ezt a korlátot, hibaüzenet jelenik meg.
* Az SSL-kompatibilis Azure Database for MySQL biztonsági mentése nem támogatott. Ha a biztonsági mentés be van állítva, a rendszer sikertelen biztonsági másolatokat fog kapni.
* Az SSL-kompatibilis Azure Database for PostgreSQL biztonsági mentése nem támogatott. Ha a biztonsági mentés be van állítva, a rendszer sikertelen biztonsági másolatokat fog kapni.
* Az alkalmazáson belüli MySQL-adatbázisokat konfiguráció nélkül automatikusan biztonsági másolat készül. Ha az alkalmazáson belüli MySQL-adatbázisok esetében manuálisan állítja be a beállításokat, például a kapcsolatok karakterláncok hozzáadását, előfordulhat, hogy a biztonsági másolatok nem működnek megfelelően.
* Ha tűzfallal védett Storage-fiókot használ, a biztonsági másolatok célhelye nem támogatott. Ha a biztonsági mentés be van állítva, a rendszer sikertelen biztonsági másolatokat fog kapni.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
1. A [Azure Portal](https://portal.azure.com)navigáljon az alkalmazás lapjára, majd válassza a **biztonsági mentések**lehetőséget. Megjelenik a **biztonsági másolatok** lap.
   
    ![Biztonsági másolatok oldal][ChooseBackupsPage]
   
   > [!NOTE]
   > Ha a következő üzenet jelenik meg, kattintson rá a App Service terv frissítéséhez, mielőtt folytatná a biztonsági mentéseket.
   > További információ: alkalmazás vertikális [Felskálázása az Azure-ban](manage-scale-up.md).  
   > ![Storage-fiók kiválasztása](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. A **biztonsági mentés** lapon kattintson a **Konfigurálás**
![elemre, majd a konfigurálás lehetőségre.](./media/web-sites-backup/ClickConfigure1.png)
3. A **biztonsági mentési konfiguráció** lapon kattintson a **tárterület elemre: Nincs konfigurálva** a Storage-fiók konfigurálására.
   
    ![Tárfiók kiválasztása][ChooseStorageAccount]
4. Válassza ki a biztonsági mentési célhelyet a **Storage-fiók** és a **tároló**kiválasztásával. A Storage-fióknak ugyanahhoz az előfizetéshez kell tartoznia, mint annak az alkalmazásnak, amelyről biztonsági másolatot szeretne készíteni. Ha szeretné, létrehozhat egy új Storage-fiókot vagy egy új tárolót a megfelelő lapokon. Ha elkészült, kattintson a **kiválasztás**gombra.
   
    ![Tárfiók kiválasztása](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. A **biztonsági mentési konfiguráció** lapon, amely továbbra is nyitva marad, konfigurálhatja a **biztonsági mentési adatbázist**, majd kiválaszthatja a biztonsági másolatokban szerepeltetni kívánt adatbázisokat (az SQL Database vagy a MySQL-t), majd kattintson **az OK**gombra.  
   
    ![Tárfiók kiválasztása](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Ahhoz, hogy egy adatbázis megjelenjen a listában, a kapcsolati karakterláncnak léteznie kell az **alkalmazás Alkalmazásbeállítások** lapjának **kapcsolati karakterláncok** szakaszában. 
   >
   > Az alkalmazáson belüli MySQL-adatbázisokat konfiguráció nélkül automatikusan biztonsági másolat készül. Ha az alkalmazáson belüli MySQL-adatbázisok esetében manuálisan állítja be a beállításokat, például a kapcsolatok karakterláncok hozzáadását, előfordulhat, hogy a biztonsági másolatok nem működnek megfelelően.
   > 
   > 
6. A **biztonsági mentési konfiguráció** lapon kattintson a **Mentés**gombra.    
7. A **biztonsági másolatok** lapon kattintson a **biztonsági mentés**elemre.
   
    ![BackUpNow gomb][BackUpNow]
   
    A biztonsági mentési folyamat során állapotjelző üzenet jelenik meg.

A Storage-fiók és a tároló konfigurálása után bármikor kezdeményezheti a manuális biztonsági mentést.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Automatikus biztonsági mentések konfigurálása
1. A **biztonsági mentési konfiguráció** lapon állítsa be az **ütemezett biztonsági mentést** be értékre. 
   
    ![Tárfiók kiválasztása](./media/web-sites-backup/05ScheduleBackup1.png)
2. A biztonsági mentési ütemezési beállítások megjelennek, az **ütemezett biztonsági mentés** beállítása be értékre, majd szükség szerint konfigurálja a biztonsági mentés ütemezését, majd kattintson **az OK**gombra.
   
    ![Automatikus biztonsági mentések engedélyezése][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Részleges biztonsági másolatok konfigurálása
Előfordulhat, hogy nem szeretne biztonsági másolatot készíteni az alkalmazás minden eleméről. Íme, néhány példa:

* Az alkalmazás olyan [heti biztonsági mentését állíthatja be](#configure-automated-backups) , amely soha nem módosult statikus tartalmat tartalmaz, például a régi blogbejegyzéseket vagy képeket.
* Az alkalmazás több mint 10 GB tartalmat tartalmaz (ez a maximális mennyiség, amelyet egyszerre tud készíteni).
* Nem kíván biztonsági másolatot készíteni a naplófájlokról.

A részleges biztonsági másolatok lehetővé teszik, hogy pontosan melyik fájlokat szeretné biztonsági másolatot készíteni.

> [!NOTE]
> A biztonsági mentésben szereplő egyéni adatbázisok 4 GB-os maximálisak lehetnek, de a biztonsági mentés maximális mérete 10 GB

### <a name="exclude-files-from-your-backup"></a>Fájlok kizárása a biztonsági másolatból
Tegyük fel, hogy rendelkezik egy olyan alkalmazással, amely olyan naplófájlokat és statikus lemezképeket tartalmaz, amelyek nem változnak. Ilyen esetekben kizárhatja ezeket a mappákat és fájlokat a későbbi biztonsági másolatokban való tároláshoz. Ha fájlokat és mappákat szeretne kizárni a biztonsági másolatokból `_backup.filter` , hozzon `D:\home\site\wwwroot` létre egy fájlt az alkalmazás mappájába. Itt adhatja meg a fájlban kizárni kívánt fájlok és mappák listáját. 

A fájlok elérésének egyszerű módja a kudu használata. Kattintson a **speciális eszközök-> go** beállításra a webalkalmazáshoz a kudu eléréséhez.

![Kudu a portál használatával][kudu-portal]

Azonosítsa a biztonsági másolatokból kizárni kívánt mappákat.  Tegyük fel például, hogy ki szeretné szűrni a kijelölt mappát és fájlokat.

![Lemezképek mappa][ImagesFolder]

Hozzon létre egy `_backup.filter` nevű fájlt, és helyezze el az előző listát a fájlban `D:\home`, de távolítsa el a fájlt. Soronként egy könyvtárat vagy fájlt listázhat. Így a fájl tartalmának a következőket kell tennie:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Töltse `_backup.filter` fel a fájlt `D:\home\site\wwwroot\` a webhely könyvtárába [FTP](deploy-ftp.md) használatával vagy bármilyen más módszerrel. Ha szeretné, közvetlenül is létrehozhatja a fájlt a kudu `DebugConsole` használatával, és beszúrhatja a tartalmat.

A biztonsági mentések ugyanúgy működnek, mint a szokásos módon, [manuálisan](#create-a-manual-backup) vagy [automatikusan](#configure-automated-backups). Mostantól a ben `_backup.filter` megadott fájlok és mappák ki vannak zárva a jövőbeli biztonsági mentések ütemezett vagy manuális elindításával. 

> [!NOTE]
> A hely részleges biztonsági mentéseit ugyanúgy állíthatja vissza, mint a [normál biztonsági mentést](web-sites-restore.md). A visszaállítási folyamat a megfelelő dolog.
> 
> A teljes biztonsági mentés visszaállításakor a helyen lévő összes tartalmat lecseréli a biztonsági mentésben szereplő összes tartalomra. Ha egy fájl van a helyen, de a biztonsági másolatban nem szerepel, a rendszer törli. A részleges biztonsági mentés visszaállításakor azonban a rendszer a feketelistán lévő címtárak egyikén vagy bármely feketelistán található tartalomon is marad.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>A biztonsági másolatok tárolása
Miután elvégezte az alkalmazás egy vagy több biztonsági mentését, a biztonsági másolatok a Storage -fiók tárolók lapján és az alkalmazásban láthatók. A Storage-fiókban minden biztonsági másolat egy olyan`.zip` fájlból áll, amely tartalmazza a biztonsági `.xml` mentési és a fájl tartalmának jegyzékfájlját `.zip` tartalmazó fájlt. Ezeket a fájlokat kibonthatja és böngészheti, ha a biztonsági mentéseket nem az alkalmazás-visszaállítás tényleges végrehajtása nélkül szeretné elérni.

Az alkalmazás adatbázisának biztonsági másolatát a. zip fájl gyökerében tárolja a rendszer. SQL Database esetén ez egy BACPAC-fájl (fájlkiterjesztés nélkül), és importálható. Ha a BACPAC-exportálás alapján szeretne SQL-adatbázist létrehozni, tekintse meg a [BACPAC-fájl importálása új felhasználói adatbázis létrehozásához](https://technet.microsoft.com/library/hh710052.aspx)című témakört.

> [!WARNING]
> A **websitebackups** -tárolóban található fájlok bármelyikének módosítása miatt a biztonsági mentés érvénytelenné válik, ezért nem állítható be.
> 
> 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

A parancsfájlok segítségével automatizálhatja a biztonsági mentési felügyeletet az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/overview)használatával.

Példákat a következő témakörben talál:

- [Azure CLI-minták](samples-cli.md)
- [Azure PowerShell-minták](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>További lépések
Az alkalmazások biztonsági másolatból való visszaállításáról az [alkalmazás visszaállítása az Azure-ban](web-sites-restore.md)című témakörben olvashat bővebben. 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

