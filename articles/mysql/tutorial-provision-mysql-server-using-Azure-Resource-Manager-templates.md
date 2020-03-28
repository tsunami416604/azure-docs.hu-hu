---
title: 'Oktatóanyag: Azure-adatbázis létrehozása a MySQL-hez – Azure Resource Manager sablon'
description: Ez az oktatóanyag bemutatja, hogyan építheti ki és automatizálhatja az Azure Database for MySQL-kiszolgálók központi telepítéseit az Azure Resource Manager-sablon használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: f4960482c88bf9768be1c1c9dbb3652409a8f1b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771088"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Oktatóanyag: Azure-adatbázis kiépítése a MySQL-kiszolgálóhoz az Azure Resource Manager-sablon használatával

Az [Azure Database for MySQL REST API](https://docs.microsoft.com/rest/api/mysql/) lehetővé teszi a DevOps-mérnökök számára a felügyelt MySQL-kiszolgálók és adatbázisok kiépítésének, konfigurálásának és műveleteinek automatizálását és integrálását az Azure-ban.  Az API lehetővé teszi a MySQL-kiszolgálók és adatbázisok létrehozását, számbavételét, felügyeletét és törlését az Azure Database for MySQL szolgáltatásban.

Az Azure Resource Manager kihasználja az alapul szolgáló REST API-t a nagy méretű üzembe helyezésekhez szükséges Azure-erőforrások deklarálásához és programozásához, kódkoncepcióként való összehangolása érdekében. A sablon paraméterezi az Azure-erőforrás nevét, termékváltozatát, hálózatát, tűzfal-konfigurációját és beállításait, lehetővé téve, hogy egyszer létre lehessen hozni, és többször is használható legyen.  Az Azure Resource Manager-sablonok könnyen létrehozhatók az [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) vagy a Visual Studio [Code használatával.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI) Lehetővé teszik az alkalmazások csomagolását, szabványosítását és üzembe helyezését, amelyek integrálhatók a DevOps CI/CD-folyamatba.  Ha például egy Web App ot szeretne gyorsan telepíteni az Azure Database for MySQL háttérrendszerhez, akkor a Teljes körű üzembe helyezést a GitHub-katalógusból származó [QuickStart sablon](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) használatával hajthatja végre.

Ebben az oktatóanyagban az Azure Resource Manager-sablon t és más segédprogramokat használhatja a következők megismeréséhez:

> [!div class="checklist"]
> * Azure-adatbázis létrehozása a MySQL-kiszolgálóhoz a VNet-szolgáltatásvégponthasználatával az Azure Resource Manager-sablon használatával
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Azure-adatbázis létrehozása a MySQL-kiszolgálóhoz a VNet-szolgáltatásvégponthasználatával az Azure Resource Manager-sablon használatával

A MySQL-kiszolgálóhoz készült Azure Database JSON-sablonhivatkozásának beírásához látogasson el a [Microsoft.DBforMySQL-kiszolgálók](/azure/templates/microsoft.dbformysql/servers) sablonhivatkozási hivatkozására. Az alábbiakban a minta JSON sablon, amely egy új kiszolgáló azure-adatbázis t fut a MySQL a VNet Service Endpoint.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
Ebben a kérésben a testre szabandó értékek a következők:
+   `name`- Adja meg a mySQL szerver nevét (tartománynév nélkül).
+   `location`- Adjon meg egy érvényes Azure-adatközpont-régiót a MySQL-kiszolgálóhoz. Például westus2.
+   `properties/version`- Adja meg a MySQL szerver verzióját telepíteni. Például 5.6 vagy 5.7.
+   `properties/administratorLogin`- Adja meg a MySQL admin bejelentkezésa a szerver. A rendszergazdai bejelentkezési név nem lehet azure_superuser, admin, administrator, root, guest vagy public.
+   `properties/administratorLoginPassword`- Adja meg a jelszót a MySQL admin felhasználó fent megadott.
+   `properties/sslEnforcement`- Az sslEnforcement engedélyezéséhez/letiltásához adja meg az engedélyezve/letiltva.
+   `storageProfile/storageMB`- Adja meg a kiszolgálóhoz szükséges maximális kiépített tárolási méretet megabájtban. Például 5120.
+   `storageProfile/backupRetentionDays`- Adja meg a kívánt biztonsági mentési megőrzési időszakot napokban. Például, 7. 
+   `storageProfile/geoRedundantBackup`- Adja meg az engedélyezve /letiltva a Geo-DR követelményektől függően.
+   `sku/tier`- Basic, GeneralPurpose vagy MemoryOptimized szint megadása a telepítéshez.
+   `sku/capacity`- Adja meg a virtuálismag-kapacitást. A lehetséges értékek a következők: 2, 4, 8, 16, 32 vagy 64.
+   `sku/family`- Adja meg a Gen5 gombot a hardvergenerálás kiválasztásához a kiszolgáló telepítéséhez.
+   `sku/name`- Adja meg TierPrefix_family_capacity. Például B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Tekintse meg a [tarifacsomagok dokumentációját](./concepts-pricing-tiers.md) a régiónkéntés rétegenként érvényes értékek megértéséhez.
+   `resources/properties/virtualNetworkSubnetId`- Adja meg az alhálózat Azure-azonosítóját a virtuális hálózatban, ahol az Azure MySQL-kiszolgálót el kell helyezni. 
+   `tags(optional)`- Adja meg a választható címkék kulcs érték párok, amelyek segítségével kategorizálni az erőforrásokat a számlázás, stb

Ha egy Azure Resource Manager-sablont szeretne készíteni a mySQL-telepítésekhez készült Azure Database-környezetek automatizálásához, a javaslat az azure-beli Rövid útmutató GitHub-galériában található minta [Azure Resource Manager-sablonból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) való kiindulása, és annak tetejére való összeállítása. 

Ha most jön az Azure Resource Manager-sablonok, és szeretné kipróbálni, az alábbi lépésekkel kezdheti:
+   Klónozza vagy töltse le a [Minta Azure Resource Manager sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) az Azure gyorsútmutató-galériából.  
+   Módosítsa az azuredeploy.parameters.json t a kívánt paraméterértékek frissítéséhez és a fájl mentéséhez. 
+   Az Azure CLI használatával hozhassa létre az Azure MySQL-kiszolgálót a következő parancsokkal

Egy böngészőben az Azure Cloud Shell használatával vagy a számítógépen az Azure CLI telepítésével futtathatja az oktatóanyag kódblokkjait.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése
A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
A [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) használatával építsen ki egy kapcsolatot az Azure Database for MySQL-kiszolgálóhoz. Példánkban a parancs a következő:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist.
```sql
mysql> CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot a kapcsolat az újonnan létrehozott adatbázisra történő átállítására:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy megtanulta, hogyan csatlakozhat az Azure Database for MySQL-adatbázishoz, végezzünk el néhány alapvető feladatot.

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzunk létre egy táblát leltáradatok tárolásához.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy már rendelkezünk egy táblával, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most két mintaadatsorral rendelkezünk a korábban létrehozott táblában.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést az adatbázistáblában lévő információk lekéréséhez.
```sql
SELECT * FROM inventory;
```

A táblákban lévő adatokat frissítheti is.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor az adatok lekérésekor megfelelően frissül.
```sql
SELECT * FROM inventory;
```

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag a következőket mutatta be:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása a MySQL-kiszolgálóhoz a VNet-szolgáltatásvégponthasználatával az Azure Resource Manager-sablon használatával
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> 
> [Alkalmazások csatlakoztatása az Azure Database for MySQL-adatbázishoz](./howto-connection-string.md)
