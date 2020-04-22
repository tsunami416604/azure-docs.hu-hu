---
title: Adattárak és fájlmegosztások elérése Windows-hitelesítéssel
description: Megtudhatja, hogy miként konfigurálhatja az SSIS-katalógust az Azure SQL Database-ben és az Azure-SSIS-integrációs futásidejű az Azure Data Factoryban az adattárolókhoz és fájlmegosztásokhoz kapcsolódó csomagok Windows-hitelesítéssel való futtatásához.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760185"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Adattárak és fájlmegosztások elérése Windows-hitelesítéssel, Azure-beli SSIS-csomagokból

A Windows-hitelesítés segítségével hozzáférhet az adattárakhoz, például az SQL-kiszolgálókhoz, a fájlmegosztásokhoz, az Azure Files-fájlokhoz stb. Az adattárak lehetnek a helyszínen, üzemeltetett Azure virtuális gépek (VM- ek), vagy az Azure-ban futó felügyelt szolgáltatások. Ha a helyszínen vannak, csatlakoznia kell az Azure-SSIS ir-hez egy virtuális hálózathoz (Microsoft Azure Virtuális hálózat), amely a helyszíni hálózathoz csatlakozik, olvassa el az [Azure-SSIS ir csatlakozása microsoft Azure virtuális hálózathoz](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)című témakört. Az Azure-SSIS ir-en futó SSIS-csomagokból négy módszer létezik a Windows-hitelesítéssel rendelkező adattárak elérésére:

| Kapcsolati módszer | Hatékony hatály | Beállítási lépés | Hozzáférési mód csomagokban | Hitelesítő adatok és csatlakoztatott erőforrások száma | A csatlakoztatott erőforrások típusa | 
|---|---|---|---|---|---|
| Tevékenységszintű végrehajtási környezet beállítása | SSIS-csomagtevékenység végrehajtása szerint | Állítsa be úgy a **Windows hitelesítési** tulajdonságot, hogy "Végrehajtás/Futtatás másként" környezetet állítson be SSIS-csomagok futtatásakor SSIS-csomagtevékenységek végrehajtásaként Az ADF-folyamatokban.<br/><br/> További információ: [Configure Execute SSIS Package activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). | Az erőforrásokat közvetlenül a csomagokban érheti el unci elérési `\\YourFileShareServerName\YourFolderName` úton keresztül, például ha fájlmegosztást vagy Azure-fájlokat használ: vagy`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Csak egy hitelesítő adatkészlet támogatása az összes csatlakoztatott erőforráshoz | - Fájlmegosztások a helyszínen/Azure virtuális gépeken<br/><br/> - Azure Files, lásd: [Azure-fájlmegosztás használata](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL-kiszolgálók a helyszínen/ Azure virtuális gépek Windows-hitelesítéssel<br/><br/> - Egyéb források Windows-hitelesítéssel |
| Katalógusszintű végrehajtási környezet beállítása | Azure-SSIS-alapú végrehajtás szerint, de felülvan bírálva egy tevékenységszintű végrehajtási környezet beállításakor (lásd fent) | Az SSISDB `catalog.set_execution_credential` tárolt eljárásának végrehajtása a "Végrehajtás/Futtatás másként" környezet beállításához.<br/><br/> További információ: a cikk további részében. | Az erőforrásokat közvetlenül a csomagokban érheti el unci elérési `\\YourFileShareServerName\YourFolderName` úton keresztül, például ha fájlmegosztást vagy Azure-fájlokat használ: vagy`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Csak egy hitelesítő adatkészlet támogatása az összes csatlakoztatott erőforráshoz | - Fájlmegosztások a helyszínen/Azure virtuális gépeken<br/><br/> - Azure Files, lásd: [Azure-fájlmegosztás használata](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL-kiszolgálók a helyszínen/ Azure virtuális gépek Windows-hitelesítéssel<br/><br/> - Egyéb források Windows-hitelesítéssel |
| Hitelesítő adatok megőrzése a parancson keresztül `cmdkey` | Azure-SSIS-alapú végrehajtás szerint, de felülvan bírálva egy tevékenység/katalógus szintű végrehajtási környezet beállításakor (lásd fent) | Az `cmdkey` Azure-SSIS`main.cmd`IR kiépítésekor hajtsa végre a parancsot egyéni beállítási parancsfájlban ( `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` ), például fájlmegosztások vagy Azure Files használata esetén: vagy `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`.<br/><br/> További információ: [A beállítás testreszabása az Azure-SSIS ir-hez.](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) | Az erőforrásokat közvetlenül a csomagokban érheti el unci elérési `\\YourFileShareServerName\YourFolderName` úton keresztül, például ha fájlmegosztást vagy Azure-fájlokat használ: vagy`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Több hitelesítő adathalmaz támogatása különböző csatlakoztatott erőforrásokhoz | - Fájlmegosztások a helyszínen/Azure virtuális gépeken<br/><br/> - Azure Files, lásd: [Azure-fájlmegosztás használata](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SQL-kiszolgálók a helyszínen/ Azure virtuális gépek Windows-hitelesítéssel<br/><br/> - Egyéb források Windows-hitelesítéssel |
| Meghajtók felszerelése a csomag végrehajtási idejében (nem állandó) | Csomagonként | Végrehajtás `net use` parancs a Folyamat végrehajtása feladat, amely hozzá az elején a vezérlő folyamat a csomagokban, például,`net use D: \\YourFileShareServerName\YourFolderName` | Fájlmegosztások elérése leképezett meghajtókon keresztül | Több meghajtó támogatása különböző fájlmegosztásokhoz | - Fájlmegosztások a helyszínen/Azure virtuális gépeken<br/><br/> - Azure Files, lásd: [Azure-fájlmegosztás használata](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Ha a fenti módszerek egyikét sem használja a Windows-hitelesítéssel rendelkező adattárak eléréséhez, a Windows-hitelesítéstől függő csomagok nem férnek hozzá, és futásidőben nem működnek. 

A cikk további cikk ismerteti, hogyan konfigurálhatja az Azure SQL Database-kiszolgálóban/felügyelt példányban tárolt SSIS-katalógust (SSISDB) az Azure-SSIS infravörös szolgáltatáson, amely Windows-hitelesítést használ az adattárak eléréséhez. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Csak egy hitelesítő adathalmazt használhat

Ha Windows-hitelesítést használ egy SSIS-csomagban, csak egy hitelesítő adatot használhat. A tartomány hitelesítő adatait, amelyeket a cikkben leírt lépések végrehajtásakor ad meg, az Azure-SSIS ir-en lévő összes – interaktív vagy ütemezett – csomagvégrehajtás-végrehajtásra vonatkoznak, amíg meg nem változtatja vagy el nem távolítja őket. Ha a csomagnak több adattárhoz kell csatlakoznia különböző hitelesítő adatokkal, vegye figyelembe a fenti alternatív módszereket.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Tartományi hitelesítő adatok megadása a Windows-hitelesítéshez

Ha olyan tartományi hitelesítő adatokat szeretne megadni, amelyek lehetővé teszik, hogy a csomagok Windows-hitelesítést használjanak a helyszíni adattárak eléréséhez, tegye a következőket:

1. Az SQL Server Management Studio (SSMS) vagy más eszközzel csatlakozzon az SSISDB-t tároló Azure SQL Database server/Managed Instance szolgáltatáshoz. További információ: [Csatlakozás az SSISDB-hez az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)

2. Ha az SSISDB az aktuális adatbázis, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és adja meg a megfelelő tartományi hitelesítő adatokat:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Futtassa az SSIS-csomagokat. A csomagok a megadott hitelesítő adatokat használják a Windows-hitelesítéssel a helyszíni adattárak eléréséhez.

### <a name="view-domain-credentials"></a>Tartományhitelesítő adatok megtekintése

Az aktív tartomány hitelesítő adatainak megtekintéséhez tegye a következőket:

1. Az SSMS vagy más eszközzel csatlakozzon az Azure SQL Database server/Managed Instance, amely az SSISDB-t üzemelteti. További információ: [Csatlakozás az SSISDB-hez az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)

2. Ha az SSISDB az aktuális adatbázis, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és ellenőrizze a kimenetet:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Tartományhitelesítő adatok törlése
A jelen cikkben ismertetett hitelesítő adatok törléséhez és eltávolításához tegye a következőket:

1. Az SSMS vagy más eszközzel csatlakozzon az Azure SQL Database server/Managed Instance, amely az SSISDB-t üzemelteti. További információ: [Csatlakozás az SSISDB-hez az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)

2. Ha az SSISDB az aktuális adatbázis, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Csatlakozás helyszíni SQL Server kiszolgálóhoz

Annak ellenőrzéséhez, hogy a helyszínen csatlakozhat-e SQL Server kiszolgálóhoz, tegye a következőket:

1. A teszt futtatásához keressen egy nem tartományhoz csatlakozó számítógépet.

2. A tartományhoz nem csatlakozó számítógépen futtassa a következő parancsot az SSMS elindításához a használni kívánt tartományi hitelesítő adatokkal:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Az SSMS-ből ellenőrizze, hogy tud-e csatlakozni az SQL Server hez a helyszínen.

### <a name="prerequisites"></a>Előfeltételek

Ha az Azure-ban futó csomagokból szeretne helyszíni SQL Servert elérni, tegye a következőket:

1.  Az SQL Server Configuration Manager programban engedélyezze a TCP/IP protokollt.

2. Hozzáférés engedélyezése a Windows tűzfalon keresztül. További információt a [Windows tűzfal konfigurálása az SQL Server eléréséhez című témakörben talál.](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access)

3. Csatlakozzon az Azure-SSIS ir-hez egy Microsoft Azure virtuális hálózathoz, amely a helyszínen csatlakozik az SQL Serverhez.  További információ: [Join Azure-SSIS IR to a Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Az SSISDB `catalog.set_execution_credential` tárolt eljárásával a jelen cikkben ismertetett hitelesítő adatokat biztosíthatja.

## <a name="connect-to-a-file-share-on-premises"></a>Csatlakozás helyszíni fájlmegosztáshoz

Annak ellenőrzéséhez, hogy a helyszínen tud-e fájlmegosztáshoz csatlakozni, tegye a következőket:

1. A teszt futtatásához keressen egy nem tartományhoz csatlakozó számítógépet.

2. A nem tartományhoz csatlakozó számítógépen futtassa a következő parancsokat. Ezek a parancsok megnyitnak egy parancssorablakot a használni kívánt tartományi hitelesítő adatokkal, majd egy címtárlista beszerzésével teszteljék a helyszíni fájlmegosztáshoz való kapcsolódást.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Ellenőrizze, hogy a rendszer visszaadja-e a címtárlistát a helyszíni fájlmegosztáshoz.

### <a name="prerequisites"></a>Előfeltételek

Ha az Azure-ban futó csomagokból szeretne helyszíni fájlmegosztást elérni, tegye a következőket:

1. Hozzáférés engedélyezése a Windows tűzfalon keresztül.

2. Csatlakozzon az Azure-SSIS ir-hez egy Microsoft Azure virtuális hálózathoz, amely a helyszíni fájlmegosztáshoz csatlakozik.  További információ: [Join Azure-SSIS IR to a Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

3. Az SSISDB `catalog.set_execution_credential` tárolt eljárásával a jelen cikkben ismertetett hitelesítő adatokat biztosíthatja.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Csatlakozás fájlmegosztáshoz az Azure VM-en

Ha az Azure virtuális gépen lévő fájlmegosztást az Azure-ban futó csomagokból szeretné elérni, tegye a következőket:

1. Az SSMS vagy más eszközzel csatlakozzon az Azure SQL Database server/Managed Instance, amely az SSISDB-t üzemelteti. További információ: [Csatlakozás az SSISDB-hez az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)

2. Ha az SSISDB az aktuális adatbázis, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és adja meg a megfelelő tartományi hitelesítő adatokat:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Csatlakozás fájlmegosztáshoz az Azure Files-ban

Az Azure Files szolgáltatásról további információt az [Azure Files ( Azure Files ) (Azure Files) (Azure Files) (További](https://azure.microsoft.com/services/storage/files/)információ: Azure Files) (További információ: Azure Files

Ha az Azure Files ban szeretne fájlmegosztást elérni az Azure-ban futó csomagokból, tegye a következőket:

1. Az SSMS vagy más eszközzel csatlakozzon az Azure SQL Database server/Managed Instance, amely az SSISDB-t üzemelteti. További információ: [Csatlakozás az SSISDB-hez az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)

2. Ha az SSISDB az aktuális adatbázis, nyisson meg egy lekérdezési ablakot.

3. Futtassa a következő tárolt eljárást, és adja meg a megfelelő tartományi hitelesítő adatokat:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>További lépések

- Telepítse a csomagokat. További információ: [SSIS-projekt telepítése az Azure-ban az SSMS segítségével.](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- Futtassa a csomagokat. További információ: [SSIS-csomagok futtatása az Azure-ban SSMS-sel.](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)
- Ütemezze be a csomagokat. További információ: [SSIS-csomagok ütemezése az Azure-ban.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)