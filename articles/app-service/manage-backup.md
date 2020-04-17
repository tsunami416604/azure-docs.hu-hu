---
title: Alkalmazás biztonsági mentése
description: Ismerje meg, hogyan hozhat létre biztonsági másolatokat az alkalmazásokról az Azure App Service-ben. Futtassa a manuális vagy ütemezett biztonsági mentéseket. A biztonsági másolatok testreszabása a csatolt adatbázissal.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: b812ae10b3462dbeff05c8a67e7ebb725281e7e8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535757"
---
# <a name="back-up-your-app-in-azure"></a>Adatok biztonsági mentése az Azure-ban
Az Azure App [Service](overview.md) Biztonsági mentés és visszaállítás funkciója lehetővé teszi, hogy manuálisan vagy ütemezés szerint hozzon létre alkalmazásbiztonsági mentéseket. Beállíthatja, hogy a biztonsági mentések meghatározatlan ideig őrizhetők legyenek. Az alkalmazás visszaállíthatja egy korábbi állapot pillanatképét a meglévő alkalmazás felülírásával vagy egy másik alkalmazásra való visszaállításával.

Az alkalmazás biztonsági mentésből történő visszaállításáról az [Alkalmazás visszaállítása az Azure-ban](web-sites-restore.md)című témakörben talál további információt.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Mi lesz biztonsági másolatot
Az App Service biztonsági másolatot tud a következő információkról egy Azure-tárfiókba és tárolóba, amelyet az alkalmazás használatára konfigurált. 

* Alkalmazáskonfiguráció
* Fájl tartalma
* Az alkalmazáshoz csatlakoztatott adatbázis

A biztonsági mentési szolgáltatás a következő adatbázis-megoldásokat támogatja: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL alkalmazáson belüli](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Minden biztonsági mentés az alkalmazás teljes offline másolata, nem pedig növekményes frissítés.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Követelmények és korlátozások
* A Biztonsági mentés és visszaállítás funkció megköveteli, hogy az App Service-csomag a **standard** szintű vagy **prémium szintű** legyen. Az App Service-csomag magasabb szintű használatra történő méretezéséről az [Alkalmazás felskálázása az Azure-ban](manage-scale-up.md)című témakörben talál további információt. **A prémium** szint nagyobb számú napi biztonsági másolatot tesz lehetővé, mint **a standard** szintű.
* Szüksége van egy Azure storage-fiók és a tároló ugyanabban az előfizetésben, mint az alkalmazás, amely szeretne biztonsági másolatot. Az Azure storage-fiókokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)
* A biztonsági mentések legfeljebb 10 GB alkalmazás- és adatbázis-tartalom lehet. Ha a biztonsági mentés mérete meghaladja ezt a korlátot, hibaüzenetet kap.
* A TLS-kompatibilis Azure Database for MySQL biztonsági mentései nem támogatottak. Ha egy biztonsági másolat van konfigurálva, sikertelen biztonsági mentést kap.
* A TLS-kompatibilis Azure Database for PostgreSQL biztonsági mentései nem támogatottak. Ha egy biztonsági másolat van konfigurálva, sikertelen biztonsági mentést kap.
* Az alkalmazáson belüli MySQL adatbázisokról automatikusan, konfiguráció nélkül készül biztonsági másolatot. Ha manuálisan adja meg az alkalmazáson belüli MySQL-adatbázisok beállításait, például a kapcsolati karakterláncok hozzáadását, előfordulhat, hogy a biztonsági mentések nem működnek megfelelően.
* A tűzfalengedélyezett tárfiók használata a biztonsági mentések célhelyeként nem támogatott. Ha egy biztonsági másolat van konfigurálva, sikertelen biztonsági mentést kap.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
1. Az [Azure Portalon](https://portal.azure.com)keresse meg az alkalmazás lapját, és válassza a **Biztonsági mentések**lehetőséget. Megjelenik **a Biztonsági mentések** lap.

    ![Biztonsági mentések lap](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Ha a következő üzenet jelenik meg, kattintson rá az App Service-csomag frissítéséhez, mielőtt folytatná a biztonsági mentéseket.
    > További információt az [Alkalmazás felskálázása az Azure-ban című témakörben talál.](manage-scale-up.md)
    > ![Tárfiók kiválasztása](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. A **Biztonsági másolat** lapon válassza a **Biztonsági másolat nincs konfigurálva jelölőnégyzetet. Kattintson ide az alkalmazás biztonsági mentésének konfigurálásához.**

    ![Kattintson a Konfigurálás gombra](./media/manage-backup/configure-start.png)

3. A **Biztonsági másolat konfigurációja** lapon kattintson a Tárfiók konfigurálásához **nem konfigurált tárterület** elemre.

    ![Tárfiók kiválasztása](./media/manage-backup/configure-storage.png)

4. Válassza ki a biztonsági mentési célt a **tárfiók** és **a tároló**kiválasztásával. A tárfióknak ugyanahhoz az előfizetéshez kell tartoznia, mint a biztonsági másolatot kíván tenni az alkalmazásnak. Ha szeretné, létrehozhat egy új tárfiókot vagy egy új tárolót a megfelelő oldalakon. Ha elkészült, kattintson a **Kijelölés gombra.**

5. A továbbra is nyitva hagyott **Biztonsági másolat konfigurációja** lapon konfigurálhatja a **Biztonsági másolat adatbázisát**, majd kiválaszthatja a biztonsági mentések (SQL-adatbázis vagy a MySQL) által felvenni kívánt adatbázisokat, majd kattintson az **OK**gombra.

    ![Tárfiók kiválasztása](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > Ahhoz, hogy egy adatbázis megjelenjen a listában, a kapcsolati karakterláncnak meg kell lennie az **alkalmazás alkalmazásbeállítások** lapjának **Kapcsolati karakterláncok** szakaszában. 
    >
    > Az alkalmazáson belüli MySQL adatbázisokról automatikusan, konfiguráció nélkül készül biztonsági másolatot. Ha manuálisan adja meg az alkalmazáson belüli MySQL-adatbázisok beállításait, például a kapcsolati karakterláncok hozzáadását, előfordulhat, hogy a biztonsági mentések nem működnek megfelelően.
    > 
    > 

6. A **Biztonsági másolat beállítása** lapon kattintson a **Mentés gombra.**
7. A **Biztonsági másolatok** lapon kattintson a **Biztonsági másolat gombra.**

    ![VisszaupNow gomb](./media/manage-backup/manual-backup.png)

    A biztonsági mentési folyamat során folyamatjelző jelenik meg.

A tárfiók és a tároló konfigurálása után bármikor kezdeményezhet manuális biztonsági mentést.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Automatikus biztonsági mentések konfigurálása
1. A **Biztonsági másolat beállítása lapon** állítsa az **Ütemezett biztonsági mentés** t **.** 

    ![Automatikus biztonsági mentések engedélyezése](./media/manage-backup/scheduled-backup.png)

2. Konfigurálja a biztonsági mentés ütemezését a kívánt módon, és válassza **az OK gombot.**

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Részleges biztonsági mentések konfigurálása
Néha nem szeretne mindent biztonsági másolatot tenni az alkalmazásban. Íme, néhány példa:

* Heti [biztonsági mentéseket állít be](#configure-automated-backups) az alkalmazásról, amelyek soha nem változnak statikus tartalmat, például régi blogbejegyzéseket vagy képeket.
* Az alkalmazás több mint 10 GB tartalommal rendelkezik (ez az a maximális összeg, amelyről egyszerre biztonsági másolatot lehet kapni).
* Nem szeretne biztonsági másolatot tenni a naplófájlokról.

A részleges biztonsági mentések lehetővé teszik annak kiválasztását, hogy pontosan mely fájlokról szeretne biztonsági másolatot készíteni.

> [!NOTE]
> Az egyes adatbázisok a biztonsági mentés lehet 4GB max, de a teljes maximális mérete a biztonsági mentés 10GB

### <a name="exclude-files-from-your-backup"></a>Fájlok kizárása a biztonsági másolatból
Tegyük fel, hogy olyan alkalmazással rendelkezik, amely naplófájlokat és statikus képeket tartalmaz, amelyekről egyszer biztonsági mentés készült, és nem fognak megváltozni. Ilyen esetekben kizárhatja ezeket a mappákat és fájlokat a későbbi biztonsági mentések tárolásából. Ha ki szeretné zárni a fájlokat `_backup.filter` és mappákat a biztonsági másolatokból, hozzon létre egy fájlt az `D:\home\site\wwwroot` alkalmazás mappájában. Adja meg a fájlban kizárni kívánt fájlok és mappák listáját. 

A fájlokat a rendszerre navigálva érheti el. `https://<app-name>.scm.azurewebsites.net/DebugConsole` Ha a rendszer kéri, jelentkezzen be az Azure-fiókjába.

Azonosítsa azokat a mappákat, amelyeket ki szeretne zárni a biztonsági mentésből. Ki szeretné szűrni például a kiemelt mappát és a fájlokat.

![Képek mappa](./media/manage-backup/kudu-images.png)

Hozzon létre `_backup.filter` egy hívott fájlt, és helyezze `D:\home`az előző listát a fájlba, de távolítsa el a programot. Soronként egy könyvtár vagy fájl felsorolása. Tehát a fájl tartalmának a következőnek kell lennie:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Töltsön `_backup.filter` fel `D:\home\site\wwwroot\` fájlt a webhely könyvtárába [ftp](deploy-ftp.md) vagy más módszerrel. Ha szeretné, akkor létrehozhatja a fájlt `DebugConsole` közvetlenül kudu és helyezze be a tartalmat ott.

Futtassa a biztonsági másolatokat ugyanúgy, ahogy azt általában tenné, [manuálisan](#create-a-manual-backup) vagy [automatikusan.](#configure-automated-backups) Mostantól a megadott `_backup.filter` fájlok és mappák ki vannak zárva a jövőbeli biztonsági mentések ütemezéséből vagy manuálisan történő kezdeményezéséből. 

> [!NOTE]
> A webhely részleges biztonsági mentését ugyanúgy állítja vissza, mint a [rendszeres biztonsági másolatot](web-sites-restore.md). A visszaállítási folyamat helyesen cselekszik.
> 
> A teljes biztonsági mentés visszaállításakor a webhely összes tartalma a biztonsági mentésben található tartalomra cserélődik. Ha egy fájl a webhelyen található, de a biztonsági másolatban nem, akkor törlődik. Ha azonban a részleges biztonsági mentés tarol, a feketelistán szereplő könyvtárakban vagy a feketelistán szereplő fájlokban található tartalmak a megfelelő módon maradnak.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>A biztonsági mentések tárolásának
Miután egy vagy több biztonsági mentést készített az alkalmazáshoz, a biztonsági mentések a tárfiók **Tárolók** lapján és az alkalmazáson láthatók. A tárfiókban minden biztonsági másolat`.zip` egy fájlból áll, `.xml` amely tartalmazza a `.zip` biztonsági mentési adatokat, és egy olyan fájlból, amely a fájl tartalmának jegyzékét tartalmazza. Ezeket a fájlokat kicsomagolhatja és böngészheti, ha az alkalmazás visszaállítása nélkül szeretné elérni a biztonsági másolatokat.

Az alkalmazás adatbázis-biztonsági másolata a .zip fájl gyökerében tárolódik. SQL adatbázis esetén ez egy BACPAC-fájl (nincs fájlkiterjesztés), és importálható. Ha a BACPAC-exportáláson alapuló SQL-adatbázist szeretne létrehozni, olvassa el a [BACPAC-fájl importálása új felhasználói adatbázis létrehozásához című témakört.](https://technet.microsoft.com/library/hh710052.aspx)

> [!WARNING]
> A **webhelybiztonsági mentési** tárolójában lévő fájlok bármelyikének módosítása a biztonsági mentés érvénytelenné, ezért nem állítható helyre.
> 
> 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az [Azure CLI](/cli/azure/install-azure-cli) vagy az [Azure PowerShell](/powershell/azure/overview)használatával parancsfájlokkal automatizálhatja a biztonsági mentés kezelését.

A mintákat lásd:

- [Azure CLI-minták](samples-cli.md)
- [Azure PowerShell-minták](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Következő lépések
Az alkalmazások biztonsági másolatból történő visszaállításáról az [Alkalmazás visszaállítása az Azure-ban](web-sites-restore.md)című témakörben talál további információt. 
