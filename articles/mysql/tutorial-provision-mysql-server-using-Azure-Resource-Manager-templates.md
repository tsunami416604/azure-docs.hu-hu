---
title: 'Oktatóanyag: Üzembe helyezése egy Azure Database for MySQL-kiszolgálóhoz az Azure Resource Manager-sablon használatával'
description: Ez az oktatóanyag azt ismerteti, hogyan építse ki és automatizálhatja Azure Database for MySQL-kiszolgálói konfigurációk mellett az Azure Resource Manager-sablon használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: bb7a48b08fde07380276d33393225c3f5220b93f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880699"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Oktatóanyag: Üzembe helyezése egy Azure Database for MySQL-kiszolgálóhoz az Azure Resource Manager-sablon használatával

A [, Azure Database for MySQL REST API-val](https://docs.microsoft.com/rest/api/mysql/) lehetővé teszi, hogy a fejlesztő és üzemeltető mérnököknek automatizálása és integrálása a kiépítés, konfigurációs és műveletek felügyelt MySQL-kiszolgálók és adatbázisok az Azure-ban.  Az API lehetővé teszi a létrehozási, enumerálás, felügyeleti és MySQL-kiszolgálók és adatbázisok törlése az Azure Database for MySQL-szolgáltatás.

Az Azure Resource Manager használhatja az alapul szolgáló REST API-t deklarálja és az Azure-erőforrások az infrastruktúra mint kód fogalma igazodó méretű központi telepítéséhez szükséges programot. A sablon az Azure-erőforrás nevét, SKU, hálózati, tűzfal-konfiguráció felparaméterezi és beállítások, lehetővé téve, hogy hozható létre egy időben, és több alkalommal használva.  Az Azure Resource Manager-sablonok egyszerűen hozható létre [az Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) vagy [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). A használatukkal az alkalmazások csomagolása, szabványügyi szervezet és üzembe helyezési automatizálást, amely integrálható a fejlesztési és üzemeltetési CI/CD-folyamat.  Például ha szeretne gyorsan üzembe helyezhet egy webalkalmazást az Azure Database for MySQL háttér, is végezhet a teljes körű üzembe helyezési ez [gyorsindítási sablon](https://azure.microsoft.com/en-us/resources/templates/101-webapp-managed-mysql/) a GitHub-katalógusból.

Ebben az oktatóanyagban használja az Azure Resource Manager-sablon és más segédprogramok megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Hozzon létre egy Azure Database for MySQL-kiszolgálóhoz az Azure Resource Manager-sablon használatával szolgáltatásvégpont
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Hozzon létre egy Azure Database for MySQL-kiszolgálóhoz az Azure Resource Manager-sablon használatával szolgáltatásvégpont

A JSON-sablon hivatkozás egy Azure Database for MySQL-kiszolgáló esetében, keresse fel [Microsoft.DBforMySQL kiszolgálók](/azure/templates/microsoft.dbformysql/servers) tárfióksablonok referenciáját. Alább a minta egy JSON-sablon, amely segítségével hozzon létre egy új kiszolgálót, Azure Database for MySQL-szolgáltatásvégpont futtatása van.
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
Ezt a kérelmet az értékek, amelyek testre kell szabni a következők:
+   `name` -Adja meg a MySQL-kiszolgáló (nélkül tartománynév) nevét.
+   `location` -Adjon meg egy érvényes Azure adatközponti régió a MySQL-kiszolgálóhoz. Ha például westus2.
+   `properties/version` -Adja meg a MySQL-kiszolgáló verzióját üzembe helyezéséhez. Ha például 5.6-os vagy 5.7.
+   `properties/administratorLogin` -Adja meg a kiszolgálóhoz a MySQL rendszergazdai bejelentkezési nevét. A rendszergazdai bejelentkezési név nem lehet azure_superuser, a rendszergazdai, a rendszergazda, a legfelső szintű, a Vendég vagy a nyilvános.
+   `properties/administratorLoginPassword` -Adja meg a fent megadott, a MySQL rendszergazdai felhasználó jelszavát.
+   `properties/sslEnforcement` -Adjon meg engedélyezési/letiltási sslEnforcement engedélyezése vagy letiltása.
+   `storageProfile/storageMB` -Adja meg a maximális kiépített tárhely méretére (MB) a kiszolgáló szükséges. Ha például 5120.
+   `storageProfile/backupRetentionDays` -Adja meg a kívánt biztonsági másolat megőrzési időszak napban. Ha például a 7. 
+   `storageProfile/geoRedundantBackup` -Adjon meg engedélyezési/letiltási Geo-DR-követelményeitől függően.
+   `sku/tier` -Adja meg az üzembe helyezés alap-, GeneralPurpose vagy MemoryOptimized szint.
+   `sku/capacity` -Adja meg a virtuális mag kapacitását. Lehetséges értékek a következők: 2, 4, 8, 16, 32 vagy 64.
+   `sku/family` -Adja meg a Gen4 vagy Gen5 válassza ki a kiszolgáló központi telepítésének hardver generációja.
+   `sku/name` -Adja meg a TierPrefix_family_capacity. Például B_Gen4_1, GP_Gen5_16, MO_Gen5_32. Tekintse meg a [tarifacsomagok](./concepts-pricing-tiers.md) tudni, hogy az érvényes értékek régiónként és / csomag dokumentációja.
+   `resources/properties/virtualNetworkSubnetId` -Adja meg az alhálózati azonosítóját az Azure virtuális hálózatban, ahol az Azure MySQL-kiszolgálót kell elhelyezni. 
+   `tags(optional)` -Adja meg, nem kötelező a címkék olyan kulcs-érték párok, melyek szeretné használni a számlázási az erőforrások kategorizálása stb.

Hozhat létre az Azure Resource Manager-sablonnal automatizálhatja az Azure Database MySQL központi telepítésekhez a szervezet számára szeretne, ha a javaslat a minta kezdő lenne [Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) az Azure-ban A rövid útmutató GitHub katalógus első épülnek, és. 

Ha nem ismeri az Azure Resource Manager-sablonok, és próbálja ki szeretné, elkezdheti az alábbi lépéseket:
+   Klónozás vagy letöltés a minta [Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) az Azure gyorsindítási galéria.  
+   Módosítsa az azuredeploy.parameters.json frissíti a beállítások alapján paraméter értékét, és mentse a fájlt. 
+   Az alábbi parancsokkal az Azure MySQL-kiszolgáló létrehozása az Azure CLI használatával

Előfordulhat, hogy az Azure Cloud Shellt a böngészőben, vagy az Azure CLI telepítése a saját számítógépén futtatásához használt a kódblokkok ebben az oktatóanyagban.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
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
    "family": "Gen4",
    "name": "GP_Gen4_2",
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
> * Hozzon létre egy Azure Database for MySQL-kiszolgálóhoz az Azure Resource Manager-sablon használatával szolgáltatásvégpont
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése

> [Alkalmazások csatlakoztatása az Azure Database for MySQL-adatbázishoz](./howto-connection-string.md)
