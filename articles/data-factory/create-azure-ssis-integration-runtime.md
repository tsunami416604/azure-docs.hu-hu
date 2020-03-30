---
title: Hozzon létre egy Azure-SSIS-integrációs futásidőt az Azure Data Factoryban
description: Megtudhatja, hogyan hozhat létre Azure-SSIS-integrációs futásidőt az Azure Data Factoryban, hogy ssis-csomagokat telepíthessen és futtathataz Azure-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336232"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Hozzon létre egy Azure-SSIS-integrációs futásidőt az Azure Data Factoryban

Ez a cikk az Azure-SQL Server Integration Services (SSIS) integrációs futásidejű (IR) azure Data Factory kiépítésének lépéseit ismerteti. Az Azure-SSIS ir a következőket támogatja:

- Az Azure SQL Database-kiszolgáló vagy egy felügyelt példány (Project Deployment Model) által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása.
- Fájlrendszerekbe, fájlmegosztásokba vagy Azure Files (csomagtelepítési modell) telepített csomagok futtatása. 

Az Azure-SSIS IR kiépítése után ismerős eszközöket használhat a csomagok azure-beli üzembe helyezéséhez és futtatásához. Ezek az eszközök közé tartozik az SQL Server Data Tools (SSDT), `dtinstall`az `dtutil`SQL `dtexec`Server Management Studio (SSMS) és a parancssori eszközök, például a , és a .

Az [Azure-SSIS ir-oktatóanyag bemutatja,](tutorial-create-azure-ssis-runtime-portal.md) hogyan hozhat létre egy Azure-SSIS-ir az Azure portalon vagy a Data Factory alkalmazáson keresztül. Az oktatóanyag azt is bemutatja, hogyan használhatja az Azure SQL Database-kiszolgálót vagy az SSISDB üzemeltetéséhez felügyelt példányt. Ez a cikk kibővíti az oktatóanyagot, és ismerteti az alábbi választható feladatok elvégzését:

- Használjon egy Azure SQL Database-kiszolgálót IP-tűzfalszabályokkal/virtuális hálózati szolgáltatás végpontokkal, vagy egy felügyelt példányt magánvégűs ssisdb üzemeltetéséhez. Előfeltételként meg kell konfigurálnia a virtuális hálózati engedélyeket és beállításokat az Azure-SSIS ir-hez a virtuális hálózathoz való csatlakozáshoz.

- Azure Active Directory (Azure AD) hitelesítés tanusítani az adat-előállító egy Azure SQL Database-kiszolgálóhoz vagy felügyelt példányhoz való csatlakozáshoz. Előfeltételként hozzá kell adnia az adat-előállító felügyelt identitását adatbázis-felhasználóként, aki ssisdb-példányt hozhat létre.

- Csatlakozzon az Azure-SSIS ir-hez egy virtuális hálózathoz, vagy konfiguráljon egy saját üzemeltetésű infravörös hitelesítést az Azure-SSIS ir proxyjaként a helyszíni adatok eléréséhez.

Ez a cikk bemutatja, hogyan azure-SSIS IR az Azure Portalon, az Azure PowerShell és egy Azure Resource Manager-sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**. Ha még nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes próbafiókot.](https://azure.microsoft.com/pricing/free-trial/)

- **Azure SQL Database-kiszolgáló vagy felügyelt példány (nem kötelező)**. Ha még nem rendelkezik adatbázis-kiszolgálóval, először hozzon létre egyet az Azure Portalon. A Data Factory viszont létrehoz egy SSISDB példányt ezen az adatbázis-kiszolgálón. 

  Javasoljuk, hogy az adatbáziskiszolgálót az integrációs modullal megegyező Azure-régióban hozza létre. Ez a konfiguráció lehetővé teszi, hogy az integrációs futásidejű végrehajtási naplók az SSISDB-be anélkül, hogy az Azure-régiók keresztezése.

  Tartsa szem előtt a következő pontokat:

  - A kijelölt adatbázis-kiszolgáló alapján az SSISDB-példány egyetlen adatbázisként, rugalmas készlet részeként vagy felügyelt példányban is létrehozható az Ön nevében egyetlen adatbázisként. Nyilvános hálózatban vagy virtuális hálózathoz való csatlakozással érhető el. Az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló típusának kiválasztásához ebben a cikkben az [Azure SQL Database egyetlen adatbázis, a rugalmas készlet és](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) a felügyelt példány összehasonlítása című szakaszban található útmutatást. 
  
    Ha egy Azure SQL Database-kiszolgálót használ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatásvégpontokkal, vagy egy felügyelt példányt magánvégpontdal az SSISDB üzemeltetéséhez, vagy ha saját üzemeltetésű infravörös szolgáltatás konfigurálása nélkül van szüksége a helyszíni adatokhoz való hozzáférésre, csatlakoznia kell az Azure-SSIS ir-hez egy virtuális hálózathoz. További információ: [Csatlakozás az Azure-SSIS infravörös hálózathoz virtuális hálózathoz című témakörben.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

  - Ellenőrizze, hogy az **Azure-szolgáltatások hoz való hozzáférés engedélyezése** beállítás engedélyezve van-e az adatbázis-kiszolgálón. Ez a beállítás nem alkalmazható, ha egy Azure SQL Database-kiszolgáló ip-tűzfal szabályok/virtuális hálózati szolgáltatás végpontok vagy egy felügyelt példány magánvégpontgal ssisdb üzemeltetéséhez. További információkért lásd: [Az Azure SQL-adatbázis védelme](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Ha engedélyezni szeretné ezt a beállítást a PowerShell használatával, olvassa el a [New-AzSqlServerFirewallRule című témakört.](/powershell/module/az.sql/new-azsqlserverfirewallrule)

  - Adja hozzá az ügyfélgép IP-címét vagy az ügyfélgép IP-címét tartalmazó IP-címtartományt az adatbázis-kiszolgáló tűzfalbeállításaiban szereplő ügyfél IP-címlistájához. További információkért lásd: [Kiszolgáló- és adatbázisszintű Azure SQL Database-tűzfalszabályok](../sql-database/sql-database-firewall-configure.md).

  - Csatlakozhat az adatbázis-kiszolgálóhoz sql-hitelesítés használatával a kiszolgáló felügyeleti hitelesítő adataival, vagy az Azure AD-hitelesítés használatával az adat-előállító felügyelt identitásával. Az utóbbi hoz az adatgyár felügyelt identitását egy Azure AD-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Az Azure AD-hitelesítés engedélyezése Azure-SSIS IR esetén.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

  - Ellenőrizze, hogy az adatbázis-kiszolgálónak nincs-e már SSISDB példánya. Az Azure-SSIS IR kiépítése nem támogatja egy meglévő SSISDB-példány használatát.

- **Az Azure Resource Manager virtuális hálózat (nem kötelező)**. Az Azure Resource Manager virtuális hálózattal kell rendelkeznie, ha az alábbi feltételek közül legalább egy teljesül:
  - Az SSISDB-t egy Azure SQL Database-kiszolgálón üzemelteti, amely IP-tűzfalszabályokat/virtuális hálózati szolgáltatásvégpontokat vagy magánvégpontot tartalmazó felügyelt példányt üzemeltet.
  - Az Azure-SSIS-alapú szolgáltatáson futó SSIS-csomagokból helyszíni adattárakhoz szeretne csatlakozni anélkül, hogy saját üzemeltetésű infravörös szolgáltatást konfigurálna.

- **Azure PowerShell (nem kötelező)**. Kövesse az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps)című témakör utasításait, ha PowerShell-parancsfájlt szeretne futtatni az Azure-SSIS IR kiépítéséhez.

### <a name="regional-support"></a>Regionális támogatás

Az Azure-régiók listáját, ahol a Data Factory és az Azure-SSIS IR érhető el, lásd: [Data Factory és SSIS ir elérhetősége régiónként.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Egyetlen SQL-adatbázis, rugalmas készlet és felügyelt példány összehasonlítása

Az alábbi táblázat az Azure SQL Database-kiszolgáló egyes szolgáltatásait hasonlítja össze, és az Azure-SSIR IR-hez kapcsolódó felügyelt példányokat hasonlítössze:

| Szolgáltatás | Egyetlen adatbázis/rugalmas készlet| Felügyelt példány |
|---------|--------------|------------------|
| **Ütemezés** | Az SQL Server Agent nem érhető el.<br/><br/>Lásd: [Csomag végrehajtásának ütemezése data factory folyamatban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)| A felügyelt példányügynök elérhető. |
| **Hitelesítés** | SSISDB-példányt hozhat létre egy olyan foglalt adatbázis-felhasználóval, aki bármely Azure AD-csoportot képvisel, és az adat-előállító felügyelt identitását a **db_owner** szerepkör tagjaként képviseli.<br/><br/>Az [Azure AD-hitelesítés engedélyezése ssisdb-példány azure-beli SQL Database-kiszolgálón való létrehozásáról.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database) | SSISDB-példányt létrehozhat egy olyan foglalt adatbázis-felhasználóval, aki az adat-előállító felügyelt identitását képviseli. <br/><br/>Az [Azure AD-hitelesítés engedélyezése ssisdb-példány létrehozásához az Azure SQL Database által felügyelt példányban című témakörben.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance) |
| **Szolgáltatásszint** | Amikor létrehoz egy Azure-SSIS IR-t az Azure SQL Database-kiszolgálóval, kiválaszthatja az SSISDB szolgáltatási szintjét. Több szolgáltatási szint van. | Amikor létrehoz egy Azure-SSIS-ir-t a felügyelt példányral, nem választhatja ki az SSISDB szolgáltatási szintjét. A felügyelt példány összes adatbázisa ugyanazt az erőforrást osztja meg, amelyet az adott példányhoz rendelt. |
| **Virtuális hálózat** | Az Azure-SSIS IR csatlakozhat egy Azure Resource Manager virtuális hálózathoz, ha egy Azure SQL Database-kiszolgálót használ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatás végpontokkal. | Az Azure-SSIS IR csatlakozhat egy Azure Resource Manager virtuális hálózathoz, ha felügyelt példányt használ a magánvégpontkal. A virtuális hálózat ra van szükség, ha nem engedélyezi a nyilvános végpontot a felügyelt példány.<br/><br/>Ha csatlakozik az Azure-SSIS IR-hez ugyanahhoz a virtuális hálózathoz, mint a felügyelt példány, győződjön meg arról, hogy az Azure-SSIS-ir a felügyelt példánytól eltérő alhálózatban van. Ha az Azure-SSIS-ir-t a felügyelt példánytól eltérő virtuális hálózathoz csatlakozik, javasoljuk, hogy virtuális hálózati társviszony-létesítést vagy hálózatközi kapcsolatot. Lásd: [Az alkalmazás csatlakoztatása egy Azure SQL Database által felügyelt példányhoz.](../sql-database/sql-database-managed-instance-connect-app.md) |
| **Elosztott tranzakciók** | Ez a funkció rugalmas tranzakciók kal támogatott. A Microsoft Distributed Transaction Coordinator (MSDTC) tranzakciói nem támogatottak. Ha az SSIS-csomagok az MSDTC használatával koordinálják az elosztott tranzakciókat, fontolja meg az Azure SQL Database rugalmas tranzakcióira való áttelepítést. További információ: [Distributed transactions across cloud databases](../sql-database/sql-database-elastic-transactions-overview.md). | Nem támogatott. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Integrációs futásidejű létrehozása az Azure Portalon

Ebben a szakaszban az Azure Portalon, különösen a Data Factory felhasználói felület (UI) vagy az alkalmazás, egy Azure-SSIS IR létrehozásához.

### <a name="create-a-data-factory"></a>Data factory létrehozása

Ha az Azure Portalon keresztül szeretné létrehozni az adatgyárat, kövesse az [Adatgyár létrehozása a felhasználói felületen keresztül](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)című lépés részletes útmutatóját. Ha így szeretne gyorsan hozzáférni, válassza **a Rögzítés az irányítópultra** lehetőséget. 

Az adatgyár létrehozása után nyissa meg az áttekintő lapot az Azure Portalon. A **Szerző & figyelése** csempével külön lapon nyissa meg a **Let's get started (Elévülhet)** lapot. Itt folytathatja az Azure-SSIS IR létrehozását.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul üzembe helyezése

1. Az **Első lépések** lapon válassza ki az **SSIS integrációs modul konfigurálása** csempét.

   ![SSIS integrációs modul konfigurálása csempe](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Az **Integrációs futásidejű beállítás** panel **Általános beállítások** szakaszában hajtsa végre az alábbi lépéseket.

   ![Általános beállítások](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. A **Név** mezőben adja meg az integrációs modul nevét.

   1. A **Leírás** mezőben adja meg az integrációs modul leírását.

   1. A **Hely** mezőben válassza ki az integrációs modul helyét. Csak a támogatott helyek jelennek meg. Javasoljuk, hogy az SSISDB-t üzemeltető adatbázis-kiszolgálóval megegyező helyet válasszon.

   1. A **Csomópont mérete,** válassza ki a csomópont méretét az integrációs futásidejű fürtben. Csak a támogatott csomópontméretek jelennek meg. Válasszon ki egy nagy csomópontméretet (felskálázás), ha sok nagy számítási igényű vagy memóriaigényes csomagot szeretne futtatni.

   1. A **Csomópontszám** mezőben adja meg az integrációsmodul-fürtben található csomópontok számát. Csak a támogatott csomópontszámok jelennek meg. Select a large cluster with many nodes (scale out) if you want to run many packages in parallel.

   1. A **Edition/License**esetében válassza ki az SQL Server kiadást az integrációs futásidejű: Standard vagy Enterprise. Válassza a Nagyvállalati lehetőséget, ha speciális funkciókat szeretne használni az integrációs futásidőben.

   1. A **Pénzmegtakarítás**lehetőséghez válassza az Azure Hybrid Benefit lehetőséget az integrációs futásidejű: **Igen** vagy **Nem**. Válassza az **Igen** lehetőséget, ha saját SQL Server-licencet szeretne a Frissítési Garanciával, hogy a hibrid használat tal kapcsolatos költségmegtakarításelőnyeit élvezhesse.

   1. Válassza a **Tovább lehetőséget.**

1. Az **SQL-beállítások** szakaszban hajtsa végre az alábbi lépéseket.

   ![SQL-beállítások](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Jelölje be az **Azure SQL Database server/Managed Instance által üzemeltetett SSIS-katalógus létrehozása (SSISDB) a projektek/csomagok/környezetek/végrehajtási naplók tárolásához** jelölőnégyzetet az Azure-SSIS IR-en futtatandó csomagok telepítési modelljének kiválasztásához. Válassza ki a projekttelepítési modellt, ahol a csomagok az adatbázis-kiszolgáló által üzemeltetett SSISDB-be kerülnek, vagy a csomagtelepítési modellt, ahol a csomagok fájlrendszerekbe, fájlmegosztásokba vagy Azure-fájlokba vannak telepítve. 
    
      Ha bejelöli a jelölőnégyzetet, saját adatbázis-kiszolgálót kell hoznia az Ön nevében létrehozott és kezelt SSISDB-példány üzemeltetéséhez.
   
      1. Az **Előfizetés** mezőben válassza azt az Azure-előfizetést, amelyikkel az adatbázis-kiszolgáló üzemelteti az SSISDB-t. 

      1. A **Hely** mezőben válassza ki az SSISDB-t üzemeltető adatbázis-kiszolgáló helyét. Javasoljuk, hogy az integrációs modullal megegyező helyet válasszon. 

      1. A **Katalógus adatbázis-kiszolgáló végpontja** mezőben válassza az SSISDB-t üzemeltető adatbázis-kiszolgáló végpontját. 
    
         A kijelölt adatbázis-kiszolgáló alapján az SSISDB-példány egyetlen adatbázisként, rugalmas készlet részeként vagy felügyelt példányban is létrehozható az Ön nevében egyetlen adatbázisként. Nyilvános hálózatban vagy virtuális hálózathoz való csatlakozással érhető el. Az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló típusának kiválasztásához ebben a cikkben az [Azure SQL Database egyetlen adatbázis, a rugalmas készlet és](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) a felügyelt példány összehasonlítása című szakaszban található útmutatást. 
    
         Ha egy Azure SQL Database-kiszolgálót választ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatásvégpontokkal, vagy egy felügyelt példányt az SSISDB üzemeltetéséhez, vagy ha saját üzemeltetésű infravörös szolgáltatás konfigurálása nélkül van szüksége a helyszíni adatokhoz való hozzáférésre, csatlakoznia kell az Azure-SSIS ir-hez egy virtuális hálózathoz. További információ: [Csatlakozás az Azure-SSIS infravörös hálózathoz virtuális hálózathoz című témakörben.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 

      1. Jelölje be az **AAD-hitelesítés használata az ADF felügyelt identitásával** jelölőnégyzetet, ha ki szeretné választani az adatbázis-kiszolgáló SSISDB-t tároló hitelesítési módját. Az SQL-hitelesítést vagy az Azure AD-hitelesítést az adat-előállító felügyelt identitásával választhatja. 
    
         Ha bejelöli a jelölőnégyzetet, hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoporthoz, amely hozzáférési engedélyekkel rendelkezik az adatbázis-kiszolgálóhoz. További információ: [Az Azure AD-hitelesítés engedélyezése Azure-SSIS IR esetén.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) 

      1. A **Rendszergazdai felhasználónév**mezőbe írja be az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló SQL-hitelesítési felhasználónevét. 

      1. A **Rendszergazdai jelszó**mezőbe írja be az SSISDB üzemeltetéséhez szükséges adatbázis-kiszolgáló SQL hitelesítési jelszavát. 

      1. A **Katalógus-adatbázis szolgáltatási szint**esetében válassza ki az SSISDB-t üzemeltetni kívánt adatbázis-kiszolgáló szolgáltatási szintjét. Válassza ki az alapszintű, standard vagy prémium szintű, vagy válasszon egy rugalmas készlet nevét. 

      1. Válassza **a Kapcsolat tesztelése**lehetőséget. Ha a teszt sikeres, válassza a **Tovább**gombot. 

1. A **Speciális beállítások** szakaszban hajtsa végre az alábbi lépéseket.

   ![Speciális beállítások](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. A **csomópontonkénti párhuzamos végrehajtások maximális**száma mezőben válassza ki az integrációs futásidejű fürt csomópontonkénti futtatásához. Csak a támogatott csomagszámok jelennek meg. Válasszon alacsony számot, ha egynél több maggal szeretne egyetlen nagy csomagot futtatni, amely számítás- vagy memóriaigényes. Ha egy vagy több kis csomagot szeretne egyetlen magban futtatni, válasszon ki egy nagy számot.

   1. Jelölje be az **Azure-SSIS-integrációs futásidő testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet, és válassza ki, hogy hozzá szeretne-e adni szabványos/expressz egyéni beállításokat az Azure-SSIS IR-hez. További információ: [Egyéni beállítás azure-SSIS ir.For](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)more information, see Custom setup for a Azure-SSIS IR .

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      ![Speciális beállítások egyéni beállításokkal](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. **Egyéni beállítási tároló SAS URI,** adja meg a SAS URI-tároló, ahol tárolja a parancsfájlok és a kapcsolódó fájlokat a szabványos egyéni beállítások.

      1. Az **Express egyéni beállításhoz**válassza az **Új** lehetőséget az Express hozzáadása **egyéni beállításpanel** megnyitásához, majd az **Express egyéni beállítástípus** legördülő menüjében válassza ki a típusokat, például a **cmdkey futtatása parancsot,** **a Környezeti változó hozzáadása**, A **licencelt összetevő telepítése**stb.

         Ha a **Licencelt összetevő telepítése** típust választja, a **Komponensnév** legördülő menüben kiválaszthatja a szoftverszoftver-partnereink től ük integrált összetevőit, és szükség esetén a **Licenckulcs** mezőben adhatja meg a tőlük vásárolt terméklicenckulcsot.
  
         A hozzáadott expressz egyéni beállítások megjelennek a **Speciális beállítások** szakaszban. Az eltávolításukhoz jelölje be a jelölőnégyzeteket, majd kattintson a **Törlés gombra.**

   1. Jelölje be a **Virtuális hálózat kiválasztása az Azure-SSIS-integrációs futásidejű csatlakozásához, engedélyezze az ADF számára bizonyos hálózati erőforrások létrehozását, és szükség esetén hozza meg saját statikus nyilvános IP-címeit** jelölőnégyzetet, és válassza ki, hogy szeretne-e csatlakozni az integrációs futásórához egy virtuális hálózathoz. 

      Akkor válassza ki, ha egy Azure SQL Database-kiszolgálót használ IP-tűzfalszabályokkal/virtuális hálózati szolgáltatás végpontokkal, vagy egy felügyelt példányt magánvégfelhasználói ssisdb üzemeltetéséhez, vagy ha helyszíni adatokhoz (azaz helyszíni adatforrásokhoz vagy célhelyekhez van szüksége az SSIS-csomagokban) saját üzemeltetésű infravörös szolgáltatások konfigurálása nélkül. További információ: [Join Azure-SSIS IR to a virtual network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. **Előfizetés esetén**válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

      1. A **Hely**beállításnál az integrációs futásidő ugyanazon helye van kiválasztva.

      1. A **Típus mezőben**válassza ki a virtuális hálózat típusát: klasszikus vagy Az Azure Resource Manager. Azt javasoljuk, hogy válasszon egy Azure Resource Manager virtuális hálózat, mert a klasszikus virtuális hálózatok hamarosan elavult.

      1. A **Virtuálishálózat neve mezőbe**válassza ki a virtuális hálózat nevét. Az Azure SQL Database-kiszolgálóvirtuális hálózati szolgáltatás végpontjaival vagy az SSISDB üzemeltetéséhez saját végponttal rendelkező felügyelt példányhoz használt példánynak kell lennie. Vagy a helyszíni hálózathoz csatlakoztatott nak kell lennie. Ellenkező esetben bármilyen virtuális hálózat, hogy a saját statikus nyilvános IP-címeket az Azure-SSIS IR.

      1. Az **Alhálózat neve csoportban**válassza ki a virtuális hálózat alhálózatának nevét. Az SSISDB üzemeltetéséhez az Azure SQL Database-kiszolgáló virtuális hálózati szolgáltatásvégpontokkal használt nak kell lennie. Vagy egy másik alhálózat, mint a felügyelt példány a saját végpont ssisdb üzemeltetéséhez használt. Ellenkező esetben bármelyik alhálózat, hogy a saját statikus nyilvános IP-címeket az Azure-SSIS IR.

      1. Jelölje be a **Statikus nyilvános IP-címek kigyűjtése az Azure-SSIS-integrációs futásidejű** höz jelölőnégyzetet, és válassza ki, hogy szeretné-e saját statikus nyilvános IP-címeket hozni az Azure-SSIS IR-hez, hogy engedélyezhesse őket a tűzfalon az adatforrások számára.

         Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

         1. Az **első statikus nyilvános IP-cím,** válassza ki az első statikus nyilvános IP-címet, amely megfelel az Azure-SSIS IR követelményeinek. Ha nem rendelkezik ilyennel, kattintson **az Új** hivatkozás létrehozása statikus nyilvános IP-címek létrehozásához az Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.
      
         1. A **második statikus nyilvános IP-cím,** válassza ki a második statikus nyilvános IP-címet, amely megfelel az Azure-SSIS IR követelményeinek. Ha nem rendelkezik ilyennel, kattintson **az Új** hivatkozás létrehozása statikus nyilvános IP-címek létrehozásához az Azure Portalon, majd kattintson a frissítés gombra itt, így kiválaszthatja őket.

   1. Jelölje be a **Saját üzemeltetésű integrációs futásidő beállítása az Azure-SSIS-integrációs futásidejű proxyként** jelölőnégyzetet, és válassza ki, hogy az Azure-SSIS IR-hez saját üzemeltetésű infravörös hitelesítést kíván-e beállítani proxyként. További információ: [Saját üzemeltetésű infravörös kapcsolat beállítása proxyként.](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) 

      Ha bejelöli a jelölőnégyzetet, hajtsa végre a következő lépéseket.

      ![Speciális beállítások saját üzemeltetésű infravörös kapcsolattal](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. A **saját üzemeltetésű integrációs futásidejű,** válassza ki a meglévő saját üzemeltetésű ir az Azure-SSIS IR proxyjaként.

      1. **Átmeneti tárhoz csatolt szolgáltatás,** válassza ki a meglévő Azure Blob storage kapcsolt szolgáltatás, vagy hozzon létre egy újat az átmeneti.

      1. **Az Átmeneti elérési út,** adjon meg egy blob tárolót a kiválasztott Azure Blob storage-fiókban, vagy hagyja üresen az alapértelmezett et az átmeneti.

   1. Válassza **a Virtuálishálózat-érvényesítés** > **folytatása lehetőséget.** 

1. Az **Összegzés** szakaszban tekintse át az összes kiépítési beállítást, jelölje be az ajánlott dokumentációs hivatkozásokat, és válassza a **Befejezés** lehetőséget az integrációs futásidő létrehozásának megkezdéséhez.

   > [!NOTE]
   > Az egyéni beállítási idő kizárásával a folyamatnak 5 percen belül be kell fejeződnie. De 20-30 percet is igénybe vehet, amíg az Azure-SSIS ir csatlakozik egy virtuális hálózathoz.
   >
   > Ha SSISDB-t használ, a Data Factory szolgáltatás csatlakozik az adatbázis-kiszolgálóhoz az SSISDB előkészítéséhez. A virtuális hálózat engedélyeit és beállításait is konfigurálja, ha meg van adva, és csatlakozik az Azure-SSIS ir-hez a virtuális hálózathoz.
   > 
   > Azure-SSIS IR kiépítésekor az Access Redistributable és az SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők a beépített összetevők által már támogatott adatforrásokon kívül biztosítják az Excel-fájlokhoz, az Access-fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást. A telepíthető egyéb összetevőkről az [Azure-SSIS ir egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)című témakörben talál.

1. A **Kapcsolatok** lapon váltson az **Integrációs modulok** lapra, ha szükséges. Az állapot frissítéséhez kattintson a **Frissítés** gombra.

   ![Létrehozás állapota](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Az integrációs modul leállításához/elindításához, szerkesztéséhez vagy törléséhez használja a **Műveletek** oszlopban található hivatkozásokat. Az integrációs modulhoz tartozó JSON-kód megtekintéséhez használja a legutolsó hivatkozást. A szerkesztés és törlés gombok csak akkor használhatók, ha az integrációs modul le van állítva.

   ![Azure SSIS ir-műveletek](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS integrációs modulok a portálon

1. Az Azure Data Factory felhasználói felületén váltson a **Szerkesztés** lapra, és válassza a **Kapcsolatok lehetőséget.** Ezután váltson az **Integrációs futtatási** lapra az adat-előállító meglévő integrációs futásidők megtekintéséhez.

   ![Létező integrációs modulok megtekintése](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Új Azure-SSIS-ir létrehozásához válassza az **Új** lehetőséget.

   ![Integrációs modul elérése a menüből](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Az **Integrációs futásidejű beállítás** panelen válassza ki a **meglévő SSIS-csomagok felemelése és eltolását** az Azure csempén végrehajtandó, majd a **Tovább**gombot.

   ![Integrációs modulok típusának megadása](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Az Azure-SSIS IR beállításának további lépéseit az [Azure SSIS-integrációs futásidejű szolgáltatás kiépítése](#provision-an-azure-ssis-integration-runtime) című szakaszban.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Integrációs futásidejű létrehozása az Azure PowerShell használatával

Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Azure-SSIS IR.In this section, you use Azure PowerShell to create an Azure-SSIS IR.

### <a name="create-variables"></a>Változók létrehozása

Másolja és illessze be a következő parancsfájlt. Adja meg a változók értékeit. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Bejelentkezés és előfizetés kiválasztása

Adja hozzá a következő parancsfájlt a bejelentkezéshez, és válassza ki az Azure-előfizetést.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Az adatbázis-kiszolgálóval való kapcsolat ellenőrzése

Adja hozzá a következő parancsfájlt az Azure SQL Database-kiszolgáló vagy a felügyelt példány érvényesítéséhez.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>A virtuális hálózat konfigurálása

Adja hozzá a következő parancsfájlt a virtuális hálózati engedélyek és beállítások automatikus konfigurálásához az Azure-SSIS-integrációs futásidejű csatlakozáshoz.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

Ha az erőforráscsoport már létezik, ne másolja ezt a kódot a szkriptbe. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data factory létrehozása

Futtassa a következő parancsot egy adat-előállító létrehozásához.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integrációs modul létrehozása

A következő parancsokkal hozzon létre egy Azure-SSIS-integrációs futásidejűt, amely SSIS-csomagokat futtat az Azure-ban.

Ha nem használja az SSISDB-t, `CatalogServerEndpoint`kihagyhatja a `CatalogPricingTier`, és `CatalogAdminCredential` a paramétereket.

Ha nem használ egy Azure SQL Database-kiszolgálót IP-tűzfalszabályokkal/virtuális hálózati szolgáltatásvégpontokkal, vagy egy felügyelt példányt magánvégfelhasználói ponttal az SSISDB üzemeltetéséhez, vagy nem igényel hozzáférést a helyszíni adatokhoz, kihagyhatja a `VNetId` paramétereket, `Subnet` vagy átadhatja az üres értékeket. Azt is kihagyhatja őket, ha konfigurálja a saját üzemeltetésű ir proxyként az Azure-SSIS ir az adatok helyszíni eléréséhez. Ellenkező esetben nem hagyhatja ki őket, és érvényes értékeket kell átadnia a virtuális hálózati konfigurációból. További információ: [Csatlakozás az Azure-SSIS infravörös hálózathoz virtuális hálózathoz című témakörben.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Ha felügyelt példányt használ az SSISDB üzemeltetéséhez, kihagyhatja a `CatalogPricingTier` paramétert, vagy átadhat egy üres értéket. Ellenkező esetben nem hagyhatja ki, és át kell adnia egy érvényes értéket az Azure SQL Database támogatott tarifacsomagjainak listájából. További információt az [SQL Database erőforráskorlátai](../sql-database/sql-database-resource-limits.md)című témakörben talál.

Ha az Azure AD-hitelesítést az adat-előállító felügyelt identitásával az adatbázis-kiszolgálóhoz való csatlakozáshoz használja, kihagyhatja a `CatalogAdminCredential` paramétert. De hozzá kell adnia az adatgyár felügyelt identitását egy Azure AD-csoporthoz hozzáférési engedélyekkel az adatbázis-kiszolgálóhoz. További információ: [Az Azure AD-hitelesítés engedélyezése Azure-SSIS IR esetén.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) Ellenkező esetben nem hagyhatja ki, és át kell adnia egy érvényes objektumot, amely a kiszolgáló rendszergazdai felhasználónevéből és jelszavából alakult az SQL-hitelesítéshez.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Az integrációs futásidő indítása

Futtassa a következő parancsokat az Azure-SSIS-integrációs futásidő elindításához.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Az egyéni beállítási idő kizárásával a folyamatnak 5 percen belül be kell fejeződnie. De 20-30 percet is igénybe vehet, amíg az Azure-SSIS ir csatlakozik egy virtuális hálózathoz.
>
> Ha SSISDB-t használ, a Data Factory szolgáltatás csatlakozik az adatbázis-kiszolgálóhoz az SSISDB előkészítéséhez. A virtuális hálózat engedélyeit és beállításait is konfigurálja, ha meg van adva, és csatlakozik az Azure-SSIS ir-hez a virtuális hálózathoz.
> 
> Azure-SSIS IR kiépítésekor az Access Redistributable és az SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők a beépített összetevők által már támogatott adatforrásokon kívül biztosítják az Excel-fájlokhoz, az Access-fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást. A telepíthető egyéb összetevőkről az [Azure-SSIS ir egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)című témakörben talál.

### <a name="full-script"></a>Teljes szkript

Az alábbiakban a teljes parancsfájl, amely létrehozza az Azure-SSIS-integrációs futásidejű.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Integrációs futásidejű létrehozása Azure Resource Manager-sablon használatával

Ebben a szakaszban egy Azure Resource Manager-sablon használatával hozza létre az Azure-SSIS-integrációs futásidejű. Íme egy példa forgatókönyv:

1. Hozzon létre egy JSON-fájlt a következő Azure Resource Manager sablonnal. Cserélje le a szögtartók (helyőrzők) értékeit a saját értékeire.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Az Azure Resource Manager-sablon `New-AzResourceGroupDeployment` üzembe helyezéséhez futtassa a parancsot az alábbi példában látható módon. A példában `ADFTutorialResourceGroup` az erőforráscsoport neve. `ADFTutorialARM.json`Az a fájl, amely az adat-előállító JSON-definícióját és az Azure-SSIS IR-t tartalmazza.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Ez a parancs létrehozza az adat-előállító és az Azure-SSIS IR benne, de nem indul el az infravörös.

3. Az Azure-SSIS IR elindításához `Start-AzDataFactoryV2IntegrationRuntime` futtassa a következő parancsot:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Az egyéni beállítási idő kizárásával a folyamatnak 5 percen belül be kell fejeződnie. De 20-30 percet is igénybe vehet, amíg az Azure-SSIS ir csatlakozik egy virtuális hálózathoz.
>
> Ha SSISDB-t használ, a Data Factory szolgáltatás csatlakozik az adatbázis-kiszolgálóhoz az SSISDB előkészítéséhez. A virtuális hálózat engedélyeit és beállításait is konfigurálja, ha meg van adva, és csatlakozik az Azure-SSIS ir-hez a virtuális hálózathoz.
> 
> Azure-SSIS IR kiépítésekor az Access Redistributable és az SSIS-hez készült Azure Feature Pack is telepítve lesz. Ezek az összetevők a beépített összetevők által már támogatott adatforrásokon kívül biztosítják az Excel-fájlokhoz, az Access-fájlokhoz és a különböző Azure-adatforrásokhoz való kapcsolódást. A telepíthető egyéb összetevőkről az [Azure-SSIS ir egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)című témakörben talál.

## <a name="deploy-ssis-packages"></a>SSIS-csomagok üzembe helyezése

Ha SSISDB-t használ, telepítheti benne a csomagokat, és futtathatja őket az Azure-SSIS ir-en az SQL Server Data Tools (SSDT) vagy az SQL Server Management Studio (SSMS) eszközeivel. Ezek az eszközök a kiszolgáló végpontján keresztül csatlakoznak az adatbázis-kiszolgálóhoz: 

- Azure SQL Database-kiszolgáló esetén a kiszolgálóvégpont formátuma `<server name>.database.windows.net`.
- Privát végpontot kezelő felügyelt példány esetén a `<server name>.<dns prefix>.database.windows.net`kiszolgálóvégpont formátuma .
- Nyilvános végponttal rendelkező felügyelt példány esetén a `<server name>.public.<dns prefix>.database.windows.net,3342`kiszolgálóvégpont formátuma . 

Ha nem használja az SSISDB-t, telepítheti csomagjait fájlrendszerekbe, fájlmegosztásokba vagy Azure Files-ba, `dtinstall` `dtutil`és `dtexec` futtathatja őket az Azure-SSIS ir-en a , és parancssori eszközökhasználatával. További információt az [SSIS-csomagok telepítése című](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)témakörben talál. 

Mindkét esetben futtathatja a telepített csomagokat az Azure-SSIS IR-en az SSIS-csomag végrehajtása tevékenység a Data Factory folyamatokban használatával. További információ: [SSIS-csomag végrehajtásának meghívása első osztályú Data Factory tevékenységként.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure-SSIS infravörös témaköreit ebben a dokumentációban:

- [Az Azure-SSIS integrációs futásideje.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Ez a cikk általában az integrációs futásidőkről, például az Azure-SSIS IR-ről nyújt tájékoztatást.
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan lehet letölteni és megérteni az Azure-SSIS IR kapcsolatos információkat.
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan állíthatja le, indíthatja el vagy törölheti az Azure-SSIS IR.This article shows you how to stop, start, or delete your Azure-SSIS IR. Azt is bemutatja, hogyan skálázhatja ki az Azure-SSIS IR további csomópontok hozzáadásával.
- [SSIS-csomagok üzembe helyezése, futtatása és figyelése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Csatlakozás az SSISDB-hez az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Csatlakozás helyszíni adatforrásokhoz Windows-hitelesítéssel](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Csomagvégrehajtás ütemezése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)