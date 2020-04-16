---
title: Az Azure-SSIS-integrációs futásidejű üzembe helyezett ség kiépítése
description: Ismerje meg, hogyan helyezheti üzembe az Azure SSIS integrációs modult az Azure Data Factoryben, hogy SSIS-csomagokat telepíthessen és futtathasson az Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418626"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Az Azure-SSIS-integrációs futásidő kiépítése az Azure Data Factoryban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez az oktatóanyag az Azure-portál használatával az Azure-SQL Server Integration Services (SSIS) integrációs futásidejű (IR) az Azure Data Factory használatával ismerteti a lépéseket. Az Azure-SSIS ir a következőket támogatja:

- Az Azure SQL Database-kiszolgáló vagy egy felügyelt példány (Project Deployment Model) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása.
- Fájlrendszerekbe, fájlmegosztásokba vagy Azure Files (csomagtelepítési modell) telepített csomagok futtatása. 

Az Azure-SSIS IR kiépítése után ismerős eszközöket használhat a csomagok azure-beli üzembe helyezéséhez és futtatásához. Ezek az eszközök közé tartozik az SQL Server Data Tools (SSDT), `dtinstall`az `dtutil`SQL `dtexec`Server Management Studio (SSMS) és a parancssori eszközök, például a , és a .

Elméleti információk az Azure-SSIS integrációs modulokról: [Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Azure SSIS integrációs modul üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
- **Azure SQL Database server (nem kötelező)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. A Data Factory viszont létrehoz egy SSISDB példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs futásidejű végrehajtási naplók az SSISDB-be anélkül, hogy az Azure-régiók keresztezése.

  Tartsa szem előtt a következő pontokat:

  - A kijelölt adatbázis-kiszolgáló alapján az SSISDB-példány egyetlen adatbázisként, rugalmas készlet részeként vagy felügyelt példányban is létrehozható az Ön nevében egyetlen adatbázisként. Nyilvános hálózatban vagy virtuális hálózathoz való csatlakozással érhető el. Az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló típusának kiválasztásához az [Azure SQL Database egyetlen adatbázis, a rugalmas készlet és a felügyelt példány összehasonlítása című témakörben](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)talál útmutatást. 
  
    Ha egy Azure SQL Database-kiszolgálót használ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatásvégpontokkal, vagy egy felügyelt példányt magánvégpontdal az SSISDB üzemeltetéséhez, vagy ha saját üzemeltetésű infravörös szolgáltatás konfigurálása nélkül van szüksége a helyszíni adatokhoz való hozzáférésre, csatlakoznia kell az Azure-SSIS ir-hez egy virtuális hálózathoz. További információ: [Azure-SSIS ir létrehozása virtuális hálózatban.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

  - Ellenőrizze, hogy az **Azure-szolgáltatások hoz való hozzáférés engedélyezése** beállítás engedélyezve van-e az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha egy Azure SQL Database-kiszolgáló ip-tűzfal szabályok/virtuális hálózati szolgáltatás végpontok vagy egy felügyelt példány magánvégpontgal ssisdb üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha engedélyezni szeretné ezt a beállítást a PowerShell használatával, olvassa el a [New-AzSqlServerFirewallRule című témakört.](/powershell/module/az.sql/new-azsqlserverfirewallrule)

  - Adja hozzá az ügyfélgép IP-címét vagy az ügyfélgép IP-címét tartalmazó IP-címtartományt az adatbázis-kiszolgáló tűzfalbeállításaiban szereplő ügyfél IP-címlistájához. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).

  - Csatlakozhat az adatbázis-kiszolgálóhoz sql-hitelesítés használatával a kiszolgáló felügyeleti hitelesítő adataival, vagy az Azure AD-hitelesítés használatával az adat-előállító felügyelt identitásával. Az utóbbi hoz az adatgyár felügyelt identitását egy Azure AD-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure AD-hitelesítéssel.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

  - Ellenőrizze, hogy az adatbázis-kiszolgálónak nincs-e már SSISDB példánya. Az Azure-SSIS IR kiépítése nem támogatja egy meglévő SSISDB-példány használatát.


> [!NOTE]
> Az Azure-régiók listáját, ahol a Data Factory és az Azure-SSIS IR jelenleg elérhető, lásd: [Data Factory és SSIS ir elérhetősége régiónként.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) 

## <a name="create-a-data-factory"></a>Data factory létrehozása

Ha az Azure Portalon keresztül szeretné létrehozni az adatgyárat, kövesse az [Adatgyár létrehozása a felhasználói felületen keresztül](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)című lépés részletes útmutatóját. Ha így szeretne gyorsan hozzáférni, válassza **a Rögzítés az irányítópultra** lehetőséget. 

Az adatgyár létrehozása után nyissa meg az áttekintő lapot az Azure Portalon. A **Szerző & figyelése** csempével nyissa meg a **Let's Get started (Kezdésnek)** lapot egy külön lapon. Itt folytathatja az Azure-SSIS IR létrehozását.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása

### <a name="from-the-data-factory-overview"></a>A Data Factory áttekintése

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét. 

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>A szerzői felhasználói felületről

1. Az Azure Data Factory felhasználói felületén váltson a **Szerkesztés** lapra, és válassza a **Kapcsolatok lehetőséget.** Ezután váltson az **Integrációs futtatási** lapra az adat-előállító meglévő integrációs futásidők megtekintéséhez. 

   ![Meglévő integrációs modulok megtekintésére szolgáló kiválasztások](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Válassza az **Új** lehetőséget egy Azure-SSIS integrációs modul létrehozásához. 

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Az **Integrációs futásidejű beállítás** panelen válassza ki a **meglévő SSIS-csomagok felemelése és eltolását** az Azure csempén végrehajtandó, majd a **Tovább**gombot. 

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS integrációs modul beállításához szükséges további lépésekkel kapcsolatos információ: [Azure SSIS integrációs modul üzembe helyezése](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az **Integrációs futásidejű beállítás** panel **Általános beállítások** szakaszában hajtsa végre az alábbi lépéseket. 

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. A **Név** mezőben adja meg az integrációs modul nevét. 

   1. A **Leírás** mezőben adja meg az integrációs modul leírását. 

   1. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon. 

   1. A **Csomópontméret** mezőben válassza ki az integrációsmodul-fürtön lévő csomópont méretét. Csak a támogatott csomópontméretek jelennek meg. Válasszon ki egy nagy csomópontméretet (felskálázás), ha sok nagy számítási igényű vagy memóriaigényes csomagot szeretne futtatni. 

   1. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Select a large cluster with many nodes (scale out) if you want to run many packages in parallel. 

   1. A **Edition/License**esetében válassza ki az SQL Server kiadást az integrációs futásidejű: Standard vagy Enterprise. Válassza a Nagyvállalati lehetőséget, ha speciális funkciókat szeretne használni az integrációs futásidőben. 

   1. A **Pénzmegtakarítás**lehetőséghez válassza az Azure Hybrid Benefit lehetőséget az integrációs futásidejű: **Igen** vagy **Nem**. Válassza az **Igen** lehetőséget, ha saját SQL Server-licencet szeretne a Frissítési Garanciával, hogy a hibrid használat tal kapcsolatos költségmegtakarításelőnyeit élvezhesse. 

   1. Kattintson a **Tovább** gombra. 

1. Az **SQL-beállítások** szakaszban hajtsa végre az alábbi lépéseket. 

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Jelölje be az **Azure SQL Database server/Managed Instance által üzemeltetett SSIS-katalógus létrehozása (SSISDB) a projektek/csomagok/környezetek/végrehajtási naplók tárolásához** jelölőnégyzetet az Azure-SSIS IR-en futtatandó csomagok telepítési modelljének kiválasztásához. Válassza ki a projekttelepítési modellt, ahol a csomagok az adatbázis-kiszolgáló által üzemeltetett SSISDB-be kerülnek, vagy a csomagtelepítési modellt, ahol a csomagok fájlrendszerekbe, fájlmegosztásokba vagy Azure-fájlokba vannak telepítve.
   
      Ha bejelöli a jelölőnégyzetet, saját adatbázis-kiszolgálót kell hoznia az Ön nevében létrehozott és kezelt SSISDB üzemeltetéséhez.
   
      1. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

      1. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon.

      1. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
   
         A kijelölt adatbázis-kiszolgáló alapján az SSISDB-példány egyetlen adatbázisként, rugalmas készlet részeként vagy felügyelt példányban is létrehozható az Ön nevében egyetlen adatbázisként. Nyilvános hálózatban vagy virtuális hálózathoz való csatlakozással érhető el. Az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló típusának kiválasztásához az [Azure SQL Database egyetlen adatbázis, a rugalmas készlet és a felügyelt példány összehasonlítása című témakörben](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)talál útmutatást.   

         Ha egy Azure SQL Database-kiszolgálót választ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatásvégpontokkal, vagy egy felügyelt példányt az SSISDB üzemeltetéséhez, vagy ha saját üzemeltetésű infravörös szolgáltatás konfigurálása nélkül van szüksége a helyszíni adatokhoz való hozzáférésre, csatlakoznia kell az Azure-SSIS ir-hez egy virtuális hálózathoz. További információ: [Azure-SSIS ir létrehozása virtuális hálózatban.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

      1. Jelölje be az **AAD-hitelesítés használata az ADF felügyelt identitásával** jelölőnégyzetet, ha ki szeretné választani az adatbázis-kiszolgáló SSISDB-t tároló hitelesítési módját. Az SQL-hitelesítést vagy az Azure AD-hitelesítést az adat-előállító felügyelt identitásával választhatja.

         Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Azure-SSIS IR létrehozása Azure AD-hitelesítéssel.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
   
      1. A **Rendszergazdai felhasználónév**mezőbe írja be az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét. 

      1. A **Rendszergazdai jelszó**mezőbe írja be az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló SQL hitelesítési jelszavát. 

      1. A **Katalógus-adatbázis szolgáltatási szint**esetében válassza ki az SSISDB-t üzemeltetni kívánt adatbázis-kiszolgáló szolgáltatási szintjét. Válassza ki az alapszintű, standard vagy prémium szintű, vagy válasszon egy rugalmas készlet nevét.

      1. Válassza **a Kapcsolat tesztelése**lehetőséget. Ha a teszt sikeres, válassza a **Tovább**gombot. 

1. A **Speciális beállítások** szakaszban hajtsa végre az alábbi lépéseket. 

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. A **csomópontonkénti párhuzamos végrehajtások maximális**száma mezőben válassza ki az integrációs futásidejű fürt csomópontonkénti futtatásához. Csak a támogatott csomagszámok jelennek meg. Válasszon alacsony számot, ha egynél több maggal szeretne egyetlen nagy csomagot futtatni, amely számítás- vagy memóriaigényes. Ha egy vagy több kis csomagot szeretne egyetlen magban futtatni, válasszon ki egy nagy számot. 

   1. Jelölje be az **Azure-SSIS-integrációs futásidő testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet, és válassza ki, hogy hozzá szeretne-e adni szabványos/expressz egyéni beállításokat az Azure-SSIS IR-hez. További információ: [Egyéni beállítás azure-SSIS ir.For](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)more information, see Custom setup for a Azure-SSIS IR .
   
   1. Jelölje be a **Virtuális hálózat kiválasztása az Azure-SSIS-integrációs futásidejű csatlakozásához, engedélyezze az ADF számára bizonyos hálózati erőforrások létrehozását, és adott esetben hozza a saját statikus nyilvános IP-címeit** jelölőnégyzetből, és válassza ki, hogy szeretne-e csatlakozni az Azure-SSIS IR-hez egy virtuális hálózathoz.

      Akkor válassza ki, ha egy Azure SQL Database-kiszolgálót használ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatásvégpontokkal, vagy egy felügyelt példányt magánvégfelhasználóval az SSISDB üzemeltetéséhez, vagy ha saját üzemeltetésű infravörös szolgáltatás konfigurálása nélkül kell hozzáférnie a helyszíni adatokhoz. További információ: [Azure-SSIS ir létrehozása virtuális hálózatban.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) 
   
   1. Jelölje be a **Saját üzemeltetésű integrációs futásidő beállítása az Azure-SSIS-integrációs futásidejű proxyként** jelölőnégyzetet, és válassza ki, hogy az Azure-SSIS IR-hez saját üzemeltetésű infravörös hitelesítést kíván-e beállítani proxyként. További információ: [Saját üzemeltetésű infravörös kapcsolat beállítása proxyként.](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)   

   1. Válassza a **Folytatás** elemet. 

1. Az **Összegzés** szakaszban tekintse át az összes kiépítési beállítást, jelölje be az ajánlott dokumentációs hivatkozásokat, és válassza a **Befejezés** lehetőséget az integrációs futásidő létrehozásának megkezdéséhez. 

   > [!NOTE]
   > Az egyéni beállítási idő kizárásával a folyamatnak 5 percen belül be kell fejeződnie.
   >
   > Ha SSISDB-t használ, a Data Factory szolgáltatás csatlakozik az adatbázis-kiszolgálóhoz az SSISDB előkészítéséhez. 
   > 
   > Azure-SSIS IR kiépítésekor az Access Redistributable és az SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők a beépített összetevők által már támogatott adatforrásokon kívül biztosítják az Excel-fájlokhoz, az Access-fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást. A telepíthető egyéb összetevőkről az [Azure-SSIS ir egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)című témakörben talál.

1. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra. 

   ![Létrehozási állapot a Frissítés gombbal](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva. 

   ![A Műveletek oszlopban szereplő hivatkozások](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha SSISDB-t használ, telepítheti benne a csomagokat, és futtathatja őket az Azure-SSIS IR-en az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) eszközök használatával. Ezek az eszközök a kiszolgáló végpontján keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Azure SQL Database-kiszolgáló esetén a kiszolgálóvégpont formátuma `<server name>.database.windows.net`.
- Privát végpontot kezelő felügyelt példány esetén a `<server name>.<dns prefix>.database.windows.net`kiszolgálóvégpont formátuma .
- Nyilvános végponttal rendelkező felügyelt példány esetén a `<server name>.public.<dns prefix>.database.windows.net,3342`kiszolgálóvégpont formátuma . 

Ha nem használja az SSISDB-t, telepítheti a csomagokat fájlrendszerekbe, fájlmegosztásokba vagy Azure Files-ba, `dtutil`és `dtexec` futtathatja őket az Azure-SSIS ir-en a `dtinstall`, és parancssori eszközökhasználatával. További információt az [SSIS-csomagok telepítése című](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)témakörben talál. 

Mindkét esetben futtathatja a telepített csomagokat az Azure-SSIS IR-en az SSIS-csomag végrehajtása tevékenység a Data Factory folyamatokban használatával. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

Lásd még a következő SSIS dokumentációt: 

- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Csatlakozás az SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Csomagvégrehajtás ütemezése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure-SSIS integrációs modul testreszabásáról, folytassa a következő cikkel: 

> [!div class="nextstepaction"]
> [Azure-SSIS ir testreszabása](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)