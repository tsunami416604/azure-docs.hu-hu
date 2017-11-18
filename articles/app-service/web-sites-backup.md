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
ms.openlocfilehash: 435370a8758d439a5fcce2e04efd11b4aaaf0357
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="back-up-your-app-in-azure"></a>Adatok biztonsági mentése az Azure-ban
A biztonsági mentési és visszaállítási funkciót [Azure App Service](app-service-web-overview.md) lehetővé teszi, hogy könnyen hozzanak létre alkalmazás biztonsági mentést, manuálisan vagy ütemezés szerint. Az alkalmazás felülírja a meglévő alkalmazás vagy egy másik alkalmazásnak visszaállítása visszaállíthatja egy korábbi állapothoz pillanatképet. 

Az alkalmazás biztonsági másolatból történő visszaállítását információkért lásd: [visszaállítása egy alkalmazást az Azure-ban](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Mi a biztonsági mentés beolvasása
App Service is biztonsági másolatot a következő információkat az Azure-tárfiók és tároló, amely az alkalmazás használatára konfigurált. 

* Alkalmazáskonfiguráció
* Fájl tartalma
* Az alkalmazáshoz kapcsolódó adatbázis

A következő adatbázis-megoldások biztonsági mentését végző szolgáltatás használata támogatott: 
   - [SQL Database](https://azure.microsoft.com/en-us/services/sql-database/)
   - [A MySQL (előzetes verzió) Azure-adatbázis](https://azure.microsoft.com/en-us/services/mysql)
   - [Azure-adatbázis PostgreSQL (előzetes verzió)](https://azure.microsoft.com/en-us/services/postgres)
   - [MySQL alkalmazásbeli](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Minden biztonsági mentés az alkalmazás nem növekményes frissítés offline teljes másolata.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Követelmények és korlátozások
* A biztonsági mentés és visszaállítás használatához kell lennie az App Service-csomag a **szabványos** réteg vagy **prémium** réteg. Az alkalmazásszolgáltatási csomag magasabb szintű használható használandó méretezésével kapcsolatos további információkért lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).  
  **Prémium szintű** réteg lehetővé teszi, hogy a napi nagyobb számú biztonsági ups mint **szabványos** réteg.
* Azure-tárfiók és tároló ugyanazt az előfizetést, mint az alkalmazás, amelyet szeretne biztonsági másolatot készíteni van szükség. Az Azure storage-fiókokról további információkért lásd: a [hivatkozások](#moreaboutstorage) Ez a cikk végén.
* Biztonsági mentés az alkalmazás- és a tartalom legfeljebb 10 GB-os lehet. Ha a biztonsági másolat mérete meghaladja ezt a korlátot, hibaüzenetet kap.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az alkalmazás lapját, válassza ki **biztonsági mentések**. A **biztonsági mentések** lap is megjelenik.
   
    ![Biztonsági mentések lap][ChooseBackupsPage]
   
   > [!NOTE]
   > Ha a következő üzenet jelenik meg, kattintson rá az App Service-csomag frissítése előtt nyugodtan folytathatja a biztonsági másolatok.
   > További információkért lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).  
   > ![Válassza ki a tárfiók](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Az a **biztonsági mentés** lapon, kattintson **konfigurálása**
![kattintson konfigurálása](./media/web-sites-backup/ClickConfigure1.png)
3. Az a **biztonsági mentési konfigurációhoz** kattintson **tárolási: nincs konfigurálva** storage-fiókok konfigurálása.
   
    ![Válassza ki a tárfiók][ChooseStorageAccount]
4. A biztonsági mentés célhelyének megadásához jelöljön ki egy **Tárfiók** és **tároló**. A tárfiók ugyanahhoz az előfizetéshez, mint a kívánt alkalmazást, készítsen biztonsági másolatot kell tartoznia. Ha kívánja, létrehozhat egy új tárfiókot vagy egy új tároló megfelelő lapján. Amikor elkészült, kattintson a **válasszon**.
   
    ![Válassza ki a tárfiók](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Az a **biztonsági mentési konfigurációhoz** még mindig nyitva marad lapján konfigurálhatja **adatbázis biztonsági másolata**, majd válassza ki a biztonsági mentés (SQL-adatbázis vagy MySQL) szerepeltetni kívánt adatbázisokat, majd kattintson a **OK**.  
   
    ![Válassza ki a tárfiók](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Ebben a listában szerepelnek az adatbázis, a kapcsolati karakterláncában szerepelnie kell a **kapcsolati karakterláncok** szakasza a **Alkalmazásbeállítások** az alkalmazás lapját.
   > 
   > 
6. Az a **biztonsági mentési konfigurációhoz** kattintson **mentése**.    
7. Az a **biztonsági mentések** kattintson **biztonsági mentés**.
   
    ![BackUpNow gomb][BackUpNow]
   
    A folyamatban lévő üzenet jelenik meg a biztonsági mentési folyamat során.

A tárfiók és tároló konfigurálása után bármikor kezdeményezhető manuális biztonsági mentés.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Az automatikus biztonsági mentések konfigurálása
1. Az a **biztonsági mentési konfigurációhoz** lap **ütemezett biztonsági mentés** való **a**. 
   
    ![Válassza ki a tárfiók](./media/web-sites-backup/05ScheduleBackup1.png)
2. Beállítások megjelenik, biztonsági mentési ütemezés beállítása **ütemezett biztonsági mentési** való **a**, majd konfigurálja a biztonsági mentés ütemezése tetszés szerint, és kattintson a **OK**.
   
    ![Az automatikus biztonsági mentés engedélyezése][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Részleges biztonsági mentések konfigurálása
Néha nem szeretnénk mindenről az alkalmazást. Íme, néhány példa:

* Ön [beállítása heti biztonsági mentései](web-sites-backup.md#configure-automated-backups) az alkalmazás, amely tartalmazza a statikus tartalom, amely soha nem változik, például a régi blogbejegyzések vagy képeket.
* Az alkalmazás rendelkezik több mint 10 GB-os tartalomtípus (Ez az a maximális időtartam, biztonsági másolatot készíthet egy időben).
* Nem szeretnénk készítsen biztonsági másolatot a rendszernapló fájljaiban.

Részleges biztonsági mentések engedélyezése úgy dönt, hogy pontosan mely kívánt fájlt, készítsen biztonsági másolatot.

### <a name="exclude-files-from-your-backup"></a>Fájlok kizárása a biztonsági mentés
Tegyük fel, hogy egy alkalmazás, amely tartalmazza a naplófájlok és a statikus képeket, biztonsági mentési egyszer és nem kívánja módosítani. Ebben az esetben kizárhatja azokat a fájlokat és mappákat a jövőbeni biztonsági mentések tárolják. Fájlok és mappák kizárása a biztonsági másolatok, hozzon létre egy `_backup.filter` fájlt a `D:\home\site\wwwroot` az alkalmazás mappájában. Megadja azokat a fájlokat és mappákat szeretne kizárni a fájlban található. 

A fájlok eléréséhez egyszerűen, hogy a Kudu használja. Kattintson a **speciális eszközök -> Ugrás** Kudu eléréséhez a webalkalmazás beállítása.

![A kudu portál használatával][kudu-portal]

Azonosítsa a biztonsági másolatok kizárni kívánt mappákat.  Például szeretné a kijelölt mappa és a fájlok szűrik.

![Képek mappához][ImagesFolder]

Hozzon létre egy nevű fájlt `_backup.filter` és az előző listában be a fájlt, de eltávolítása `D:\home`. Egy soronként fájl vagy könyvtár felsorolása. Ezért a fájl tartalma kell lennie:
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
Egy vagy több biztonsági mentés az alkalmazás elkészítése után a biztonsági másolatok jelennek meg a **tárolók** a tárfiók, és az alkalmazás lapján. A tárfiókban lévő minden egyes biztonsági másolat áll egy`.zip` a biztonsági mentési adatokat tartalmazó fájlt, és egy `.xml` a javítócsomagban adatait tartalmazó fájlt a `.zip` fájl tartalmát. Csomagolja ki, és keresse meg ezeket a fájlokat, ha azt szeretné, hogy egy alkalmazás-visszaállítási végrehajtása nélkül a biztonsági másolatok eléréséhez.

Az adatbázis biztonsági mentése az alkalmazás a .zip fájl tárolja. SQL-adatbázis Ez egy BACPAC-fájl (nincs fájl kiterjesztése) és importálhatók. Egy SQL-adatbázis BACPAC exportálás alapján létrehozásához lásd: [létrehozni egy új felhasználói adatbázis BACPAC fájl importálása](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Módosítása a fájlokat a **websitebackups** tároló okozhat a biztonsági mentés érvénytelen, és ezért nem visszaállítható válik.
> 
> 

## <a name="automate-with-scripts"></a>Parancsfájlok automatizálásához

Automatizálható biztonságimásolat-felügyeleti parancsfájlok, használja a [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/overview).

Minták lásd:

- [Azure CLI-minták](app-service-cli-samples.md)
- [Az Azure PowerShell-példák](app-service-powershell-samples.md)

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

