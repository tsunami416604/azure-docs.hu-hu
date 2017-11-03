---
title: "Adatok biztonsági mentése az Azure-ban"
description: "Megtudhatja, hogyan az alkalmazások biztonsági mentéseinek létrehozását az Azure App Service-ben."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: 041847f2f341528c742d127f5d624e60c26e01fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-your-app-in-azure"></a>Adatok biztonsági mentése az Azure-ban
A biztonsági mentése és visszaállítás szolgáltatás [Azure App Service](app-service-web-overview.md) lehetővé teszi, hogy könnyen hozzanak létre alkalmazás biztonsági mentést, manuálisan vagy ütemezés szerint. Az alkalmazás felülírja a meglévő alkalmazás vagy egy másik alkalmazásnak visszaállítása visszaállíthatja egy korábbi állapothoz pillanatképet. 

Az alkalmazás biztonsági másolatból történő visszaállítását információkért lásd: [visszaállítása egy alkalmazást az Azure-ban](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Mi a biztonsági mentés beolvasása
App Service készíthet biztonsági másolatot egy Azure-tárfiók és tároló, amely az alkalmazás használatára konfigurálta a következő információkat. 

* Alkalmazáskonfiguráció
* A fájl
* Az alkalmazáshoz kapcsolódó adatbázis

A következő adatbázis-megoldások biztonsági mentését végző szolgáltatás használata támogatott: 
   - [SQL Database](https://azure.microsoft.com/en-us/services/sql-database/)
   - [A MySQL (előzetes verzió) Azure-adatbázis](https://azure.microsoft.com/en-us/services/mysql)
   - [Azure-adatbázis PostgreSQL (előzetes verzió)](https://azure.microsoft.com/en-us/services/postgres)
   - [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview)
   - [MySQL alkalmazásbeli](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Minden biztonsági mentés az alkalmazás nem növekményes frissítés offline teljes másolata.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Követelmények és korlátozások
* A biztonsági mentését és visszaállítását funkció használatához az App Service-csomag kell lennie a **szabványos** réteg vagy **prémium** réteg. Az alkalmazásszolgáltatási csomag magasabb szintű használható használandó méretezésével kapcsolatos további információkért lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).  
  **Prémium szintű** réteg lehetővé teszi, hogy a napi nagyobb számú biztonsági ups mint **szabványos** réteg.
* Egy Azure storage-fiók és a tároló ugyanazt az előfizetést, mint az alkalmazás kívánt kell biztonsági másolatot készíteni. Az Azure storage-fiókokról további információkért lásd: a [hivatkozások](#moreaboutstorage) Ez a cikk végén.
* Biztonsági mentés az alkalmazás- és a tartalom legfeljebb 10 GB-os lehet. Ha a biztonsági másolat mérete meghaladja ezt a korlátot, hibaüzenetet kap.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
1. Az a [Azure Portal](https://portal.azure.com), keresse meg az alkalmazás panelen, jelölje ki **biztonsági mentések**. A **biztonsági mentések** panel fog megjelenni.
   
    ![Biztonsági mentések lap][ChooseBackupsPage]
   
   > [!NOTE]
   > Ha az alábbi üzenet jelenik meg, kattintson rá az App Service-csomag frissítése előtt nyugodtan folytathatja a biztonsági másolatok.
   > Lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md) további információt.  
   > ![Válassza ki a tárfiók](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. A a **biztonsági mentés** paneljén kattintson **konfigurálása**
![kattintson konfigurálása](./media/web-sites-backup/ClickConfigure1.png)
3. Az a **biztonsági mentési konfigurációhoz** panelen kattintson a **tárolási: nincs konfigurálva** storage-fiókok konfigurálása.
   
    ![Válassza ki a tárfiók][ChooseStorageAccount]
4. A biztonsági mentés célhelyének megadásához jelöljön ki egy **Tárfiók** és **tároló**. A tárfiók ugyanahhoz az előfizetéshez, mint a kívánt alkalmazást, készítsen biztonsági másolatot kell tartoznia. Ha kívánja, létrehozhat egy új tárfiókot vagy egy új tároló a megfelelő panelt a. Amikor elkészült, kattintson a **válasszon**.
   
    ![Válassza ki a tárfiók](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. A a **biztonsági mentési konfigurációhoz** még mindig nyitva marad panelen beállíthatja **adatbázis biztonsági másolata**, majd válassza ki a szerepeljen a biztonsági mentések (SQL-adatbázis vagy MySQL), majd kattintson a kívánt adatbázisokat **OK**.  
   
    ![Válassza ki a tárfiók](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Ebben a listában szerepelnek az adatbázis, a kapcsolati karakterláncában szerepelnie kell a **kapcsolati karakterláncok** szakasza a **Alkalmazásbeállítások** alkalmazás.
   > 
   > 
6. Az a **biztonsági mentési konfigurációhoz** panelen kattintson a **mentése**.    
7. Az a **biztonsági mentések** panelen kattintson a **biztonsági mentés**.
   
    ![BackUpNow gomb][BackUpNow]
   
    A folyamatban lévő üzenet jelenik meg a biztonsági mentési folyamat során.

Miután beállította a tárfiók és tároló manuális biztonsági mentés bármikor kezdeményezhető.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Az automatikus biztonsági mentések konfigurálása
1. Az a **biztonsági mentési konfigurációhoz** panelen állítsa **ütemezett biztonsági mentés** való **a**. 
   
    ![Válassza ki a tárfiók](./media/web-sites-backup/05ScheduleBackup1.png)
2. Beállítások megjelenik, biztonsági mentési ütemezés beállítása **ütemezett biztonsági mentési** való **a**, majd konfigurálja a biztonsági mentés ütemezése tetszés szerint, és kattintson a **OK**.
   
    ![Az automatikus biztonsági mentés engedélyezése][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Részleges biztonsági mentések konfigurálása
Néha nem kívánja minden, az alkalmazás a biztonsági mentés. Íme, néhány példa:

* Ön [beállítása heti biztonsági mentései](web-sites-backup.md#configure-automated-backups) az alkalmazás, amely tartalmazza a statikus tartalom, amely soha nem változik, például a régi blogbejegyzések vagy képeket.
* Az alkalmazás rendelkezik több mint 10 GB-os tartalomtípus (Ez az a maximális időtartam készíthet biztonsági másolatot egy időben).
* Nem szeretné a naplófájlok biztonsági mentését.

Részleges biztonsági másolatok lehetővé teszi, hogy úgy dönt, hogy pontosan amely fájlokat szeretne biztonsági másolatot készíteni.

### <a name="exclude-files-from-your-backup"></a>Fájlok kizárása a biztonsági mentés
Tegyük fel, hogy egy alkalmazás, amely tartalmazza a naplófájlok és a statikus képeket, biztonsági mentési egyszer és nem kívánja módosítani. Ilyen esetekben kizárhatja azokat a fájlokat és mappákat a jövőbeni biztonsági mentések tárolják. Fájlok és mappák kizárása a biztonsági másolatok, hozzon létre egy `_backup.filter` fájlt a `D:\home\site\wwwroot` az alkalmazás mappájában. Megadja azokat a fájlokat és mappákat szeretne kizárni a fájlban található. 

A fájlok eléréséhez egyszerűen, hogy a Kudu használja. Kattintson a **speciális eszközök -> Ugrás** Kudu eléréséhez a webalkalmazás beállítása.

![A kudu portál használatával][kudu-portal]

Azonosítsa a biztonsági másolatok kizárni kívánt mappákat.  Például szeretné a kijelölt mappa és a fájlok szűrik.

![Képek mappához][ImagesFolder]

Hozzon létre egy nevű fájlt `_backup.filter` és a fenti lista be a fájlt, de eltávolítása `D:\home`. Egy soronként fájl vagy könyvtár felsorolása. Ezért a fájl tartalma kell lennie:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Töltse fel `_backup.filter` fájlt a `D:\home\site\wwwroot\` mappában található a webhely használatával [ftp](app-service-deploy-ftp.md) vagy más módszerrel. Ha kívánja, a fájlt közvetlenül a Kudu segítségével létrehozhat `DebugConsole` és szúrja be a hiba a tartalmat.

Biztonsági mentések futtatása a szokásos módon teheti meg, ugyanúgy [manuálisan](#create-a-manual-backup) vagy [automatikusan](#configure-automated-backups). Most, bármely fájlok és mappák megadott `_backup.filter` ki van zárva a jövőbeli biztonsági mentések ütemezett, vagy manuálisan indítják el. 

> [!NOTE]
> A webhely részleges biztonsági másolatok a módon visszaállítása [a rendszeres biztonsági másolat visszaállításával](web-sites-restore.md). A visszaállítási folyamat nem a megfelelő művelet.
> 
> Ha egy teljes biztonsági mentés helyreállítása, a hely összes tartalmat helyére függetlenül a biztonsági mentés van. Ha egy fájl a helyen, de nem a biztonsági mentés az lekérdezi törlődni fog. De részleges biztonsági másolat visszaállításakor egyik Feketelistára tett könyvtárban, vagy bármely Feketelistára tett fájlban található tartalmakhoz marad, mert a.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Biztonsági másolatok tárolási módját
Egy vagy több biztonsági mentés az alkalmazás elkészítése után a biztonsági másolatok jelennek meg a **tárolók** panelen található a tárfiók, és az alkalmazás. A tárfiókban lévő minden egyes biztonsági másolat áll egy`.zip` a biztonsági mentési adatokat tartalmazó fájlt, és egy `.xml` a javítócsomagban adatait tartalmazó fájlt a `.zip` fájl tartalmát. Csomagolja ki, és keresse meg ezeket a fájlokat, ha azt szeretné, hogy egy alkalmazás-visszaállítási végrehajtása nélkül a biztonsági másolatok eléréséhez.

Az adatbázis biztonsági mentése az alkalmazás the.zip fájl tárolja. SQL-adatbázis Ez egy BACPAC-fájl (nincs fájl kiterjesztése) és importálhatók. Egy SQL-adatbázis BACPAC exportálás alapján létrehozásához lásd: [létrehozni egy új felhasználói adatbázis BACPAC fájl importálása](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Módosítása a fájlokat a **websitebackups** tároló okozhat a biztonsági mentés érvénytelen, és ezért nem visszaállítható válik.
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>Következő lépések
A visszaállítása egy alkalmazás olyan biztonsági információ: [visszaállítása egy alkalmazást az Azure-ban](web-sites-restore.md). 


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

