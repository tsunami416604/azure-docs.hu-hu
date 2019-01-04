---
title: Alkalmazás – az Azure App Service biztonsági mentése
description: Megtudhatja, hogyan hozhat létre az alkalmazások biztonsági mentését az Azure App Service-ben.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1491068248d20a917e28db5a75dee3d4b3753f5c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731058"
---
# <a name="back-up-your-app-in-azure"></a>Adatok biztonsági mentése az Azure-ban
A biztonsági mentési és visszaállítási funkciójának [Azure App Service](overview.md) könnyedén hozhat létre alkalmazást biztonsági mentések manuális vagy ütemezett teszi lehetővé. Az alkalmazás írja felül a meglévő alkalmazás vagy egy másik alkalmazásba való visszaállítása visszaállíthatja egy korábbi állapotáról pillanatképet. 

Az alkalmazás biztonsági másolatból történő visszaállítását információkért lásd: [alkalmazás visszaállítása az Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Mi a mentendő
App Service-ben is biztonsági másolatot a következő adatokat egy Azure storage-fiókot és egy tároló, amely az alkalmazások a konfigurált. 

* Alkalmazás-beállítások
* Fájl tartalma
* A kapcsolódó adatbázis

A következő adatbázis-megoldások biztonsági mentési szolgáltatás támogatottak: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [– Alkalmazáson belüli MySQL](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Minden biztonsági mentés: az alkalmazás nem növekményes frissítés offline teljes másolata.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Követelmények és korlátozások
* A biztonsági mentési és visszaállítási funkció igényel az App Service-csomagot kell lennie a **Standard** szint vagy **prémium** szint. Az App Service-csomag magasabb szintre használandó méretezésével kapcsolatos további információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md).  
  **Prémium szintű** csomaggal naponta nagyobb számú ups, mint biztonsági **Standard** szint.
* Az Azure storage-fiók és tároló ugyanabban az előfizetésben, mint az alkalmazás biztonsági mentéséhez használni kívánt van szüksége. Az Azure storage-fiókokról további információért lásd a [hivatkozások](#moreaboutstorage) Ez a cikk végén található.
* Biztonsági másolatok az alkalmazás- és tartalom legfeljebb 10 GB is lehet. Ha a biztonsági másolat mérete túllépi ezt a korlátot, hibaüzenetet kap.
* Az SSL biztonsági mentések engedélyezve Azure Database MySQL nem támogatott. Ha egy biztonsági mentés van beállítva, a sikertelen biztonsági mentések kap.
* SSL biztonsági másolatait engedélyezve az Azure-adatbázis PostgreSQL nem támogatott. Ha egy biztonsági mentés van beállítva, a sikertelen biztonsági mentések kap.
* Alkalmazáson belüli MySQL-adatbázisok készül automatikusan biztonsági másolat konfigurálása nélkül. Ha manuálisan beállításait az alkalmazáson belüli MySQL-adatbázisok, például a kapcsolati karakterláncokhoz, előfordulhat, hogy a biztonsági mentések nem működik megfelelően.
* Tárfiók tűzfal használata engedélyezve van, a biztonsági másolatok a cél nem támogatott. Ha egy biztonsági mentés van beállítva, a sikertelen biztonsági mentések kap.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
1. Az a [az Azure portal](https://portal.azure.com), nyissa meg az alkalmazás lapot, válassza ki **biztonsági mentések**. A **biztonsági mentések** lap jelenik meg.
   
    ![Biztonsági másolatok lap][ChooseBackupsPage]
   
   > [!NOTE]
   > Ha a következő üzenetet látja, kattintson rá az App Service-csomag frissítése a biztonsági mentések a folytatás előtt.
   > További információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md).  
   > ![Tárfiók kiválasztása](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Az a **Backup** lapon kattintson **konfigurálása**
![kattintson konfigurálása](./media/web-sites-backup/ClickConfigure1.png)
3. Az a **biztonsági mentés konfigurációja** kattintson **tároló: Nincs konfigurálva** tárfiók konfigurálásához.
   
    ![Tárfiók kiválasztása][ChooseStorageAccount]
4. Válassza ki a biztonsági mentés célhelye kiválasztásával egy **Tárfiók** és **tároló**. A tárfiók ugyanahhoz az előfizetéshez, a kívánt alkalmazást, készítsen biztonsági másolatot kell tartoznia. Ha szeretné, létrehozhat egy új tárfiókot vagy egy új tárolót a megfelelő oldalain. Ha elkészült, kattintson a **kiválasztása**.
   
    ![Tárfiók kiválasztása](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Az a **biztonsági mentés konfigurációja** oldalon, továbbra is nyitva marad, konfigurálhat **adatbázis biztonsági másolata**, majd válassza ki a biztonsági mentések (SQL-adatbázishoz vagy MySQL) szerepeltetni kívánt adatbázisokat, majd kattintson a **OK**.  
   
    ![Tárfiók kiválasztása](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Egy adatbázis megjelenik a listában, a kapcsolati karakterlánc szerepelniük kell a **kapcsolati karakterláncok** szakaszában a **Alkalmazásbeállítások** az alkalmazás lapját. 
   >
   > Alkalmazáson belüli MySQL-adatbázisok készül automatikusan biztonsági másolat konfigurálása nélkül. Ha manuálisan beállításait az alkalmazáson belüli MySQL-adatbázisok, például a kapcsolati karakterláncokhoz, előfordulhat, hogy a biztonsági mentések nem működik megfelelően.
   > 
   > 
6. Az a **biztonsági mentés konfigurációja** kattintson **mentése**.    
7. Az a **biztonsági mentések** kattintson **Backup**.
   
    ![BackUpNow gomb][BackUpNow]
   
    Folyamat üzenet jelenik meg a biztonsági mentési folyamat során.

A tárfiók és tároló konfigurálása után bármikor kezdeményezhető manuális biztonsági mentés.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Automatizált biztonsági mentések konfigurálása
1. Az a **biztonsági mentési konfiguráció** lap **ütemezett biztonsági mentés** való **a**. 
   
    ![Tárfiók kiválasztása](./media/web-sites-backup/05ScheduleBackup1.png)
2. Biztonsági mentés ütemezése beállítások jelennek meg, állítsa be **ütemezett biztonsági mentési** való **a**, majd igény szerint a biztonsági mentési ütemezés konfigurálása, és kattintson a **OK**.
   
    ![Automatizált biztonsági mentésének engedélyezéséhez][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Részleges biztonsági mentések konfigurálása
Néha nem szeretne biztonsági mentést mindent az alkalmazást. Íme, néhány példa:

* Ön [állítsa be a heti biztonsági mentések](#configure-automated-backups) az alkalmazás, amely tartalmazza a statikus tartalmat, amely soha nem változik, mint a régi blogbejegyzések vagy képeket.
* Az alkalmazás több mint 10 GB-nyi tartalom (a biztonsági másolatot készíthet egy időben maximális összeg) rendelkezik.
* Nem szeretne biztonsági másolatot készíteni a rendszernapló fájljaiban.

Részleges biztonsági mentések engedélyezése úgy dönt, hogy pontosan mely fájlokat szeretné készíteni.

### <a name="exclude-files-from-your-backup"></a>Fájlok kizárása a biztonsági mentés
Tegyük fel, hogy egy alkalmazás, amely tartalmazza a naplófájlok és a biztonsági mentés után és a módosítása nem fog statikus képeket. Ezekben az esetekben kizárhatja azokat a fájlokat és mappákat való tárolása a későbbi biztonsági mentések. Fájlok és mappák kizárása a biztonsági mentések, hozzon létre egy `_backup.filter` fájlt a `D:\home\site\wwwroot` az alkalmazás mappájában. Az ebben a fájlban kizárni kívánt mappák és fájlok listájának megadása. 

Könnyedén hozzáférhet a fájljaihoz, hogy Kudu. Kattintson a **speciális eszközök -> Go** Kudu eléréséhez a webalkalmazás beállítása.

![A kudu portál használatával][kudu-portal]

Azonosíthatja a biztonsági mentések kizárni kívánt mappákat.  Ha például szeretné szűrje ki a kijelölt mappa és fájlok.

![Képek mappa][ImagesFolder]

Hozzon létre egy fájlt nevű `_backup.filter` és az előzőekben felsorolt helyezni a fájlt, de eltávolítása `D:\home`. Egy fájl minden sorában vagy könyvtár listázása. Ezért a fájl tartalmának kell lennie:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Töltse fel `_backup.filter` fájlt a `D:\home\site\wwwroot\` könyvtárát a webhely használatával [ftp](deploy-ftp.md) vagy más módszerrel. Ha szeretné, a fájlt közvetlenül a Kudu használatával hozhat létre `DebugConsole` , és szúrja be a hiba a tartalmat.

Az azonos, ahogyan általában, biztonsági mentések futtatása [manuálisan](#create-a-manual-backup) vagy [automatikusan](#configure-automated-backups). Most, bármely fájlok és mappák, a megadott `_backup.filter` ki van zárva a a jövőbeni biztonsági mentések ütemezett, vagy manuálisan indítják el. 

> [!NOTE]
> A webhely részleges biztonsági másolatok ugyanúgy ugyanúgy visszaállításához [rendszeres biztonsági másolatának visszaállítása](web-sites-restore.md). A visszaállítási folyamat hajtja végre a megfelelő dolgokat teszi.
> 
> Ha egy teljes biztonsági mentés visszaállítása a hely összes tartalma helyére függetlenül a biztonsági mentés van. Ha egy fájl a helyen, de nem a biztonsági mentés a beolvasása törlése. De ha részleges biztonsági másolat visszaállítása a Feketelistára tett könyvtárak egyikét, vagy bármely Feketelistára tett fájl található tartalmak marad, mivel.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Biztonsági másolatok tárolási módját
Az alkalmazás egy vagy több biztonsági mentések végrehajtása után a biztonsági mentések láthatók a a **tárolók** a tárfiók és az alkalmazás oldalán. A storage-fiókban lévő minden egyes biztonsági másolat áll egy`.zip` fájlt, amely tartalmazza a biztonsági mentési adatok és a egy `.xml` fájlt, amely tartalmazza a jegyzék a `.zip` fájl tartalma. Csomagolja ki, és keresse meg ezeket a fájlokat, ha azt szeretné, a biztonsági másolatok eléréséhez egy alkalmazás visszaállítás végrehajtása nélkül.

Az adatbázis biztonsági mentése az alkalmazás a legfelső szintű .zip fájl tárolja. SQL-adatbázishoz Ez egy BACPAC-fájlt (fájlkiterjesztés nélkül), amely importálható. Az Exportálás BACPAC-alapú SQL-adatbázis létrehozásához lásd: [hozhat létre egy új felhasználói adatbázis BACPAC-fájl importálása](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> A fájlokat a módosítása a **websitebackups** tároló okozhat a biztonsági mentés érvénytelen, ezért nem visszaállítható válik.
> 
> 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Automatizálhat biztonsági másolatokat kezelő szkriptekkel, a a [Azure CLI-vel](/cli/azure/install-azure-cli) vagy [Azure PowerShell-lel](/powershell/azure/overview).

Minták lásd:

- [Azure CLI-minták](samples-cli.md)
- [Azure PowerShell-minták](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>További lépések
Az alkalmazás egy biztonsági másolatból történő visszaállítását információkért lásd: [alkalmazás visszaállítása az Azure](web-sites-restore.md). 


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

