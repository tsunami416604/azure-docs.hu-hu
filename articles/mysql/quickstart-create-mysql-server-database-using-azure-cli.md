---
title: "Első lépések: Azure-adatbázis létrehozása MySQL-kiszolgálóhoz - Azure CLI | Microsoft Docs"
description: "Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 574299dd64120d75a1a36cb2ded0fdd269292570
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre öt perc alatt egy Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.

A rövid útmutató elvégzéséhez az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) legújabb verziójával kell rendelkeznie. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```
Kövesse a parancssor utasításait a https://aka.ms/devicelog oldal megnyitásához a böngészőben, majd adja meg a **parancssorban** generált kódot.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) az [az group create](https://docs.microsoft.com/cli/azure/group#create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `mycliresource` helyen.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Hozzon létre egy Azure-adatbázist MySQL-kiszolgálóhoz az **az mysql server create** paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

A következő példában létrehozunk egy `mycliserver` nevű Azure-adatbázist MySQL-kiszolgálóhoz a `mycliresource` erőforráscsoportban a `westus`-ben. A kiszolgáló rendelkezik egy `myadmin` nevű rendszergazdai fiókkal, amelyhez a jelszó `Password01!`. A kiszolgáló **Alapszintű** teljesítményszinttel van létrehozva, valamint **50** számítási egység van megosztva a kiszolgálón lévő összes adatbázis között. Az alkalmazás szükségleteitől függően csökkentheti vagy növelheti a számítási egységeket és a tárterületet.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure-adatbázis MySQL-kiszolgálóhoz-szintű tűzfalszabályt az **az mysql server firewall-rule create** parancs használatával. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások használatát, mint a **mysql.exe** parancssori eszköz vagy a MySQL Workbench, amelyekkel kapcsolódhat a kiszolgálóhoz az Azure MySQL szolgáltatás tűzfalán keresztül. 

A következő példában létrehozunk egy tűzfalszabályt egy előre meghatározott címtartományhoz, amely ebben a példában az IP-címek teljes lehetséges tartományát lefedi.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása
Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak.  Ez biztosítja a „mozgó” adatok biztonságát az adatfolyam interneten keresztüli titkosításával.  A rövid útmutató megkönnyítésének érdekében letiltjuk a kiszolgáló SSL-kapcsolatait.  Ez nem ajánlott üzemi kiszolgálók esetében.  További részletekért, lásd az [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md) témakört.

A következő példában letiltjuk az SSL kényszerítését a MySQL-kiszolgálón.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Csatlakozás a kiszolgálóhoz a mysql.exe parancssori eszköz használatával
Ha a **mysql.exe** parancssori eszköz használatával kapcsolódik a kiszolgálóhoz, ellenőrizze, hogy telepítve van-e a MySQL a számítógépen.  A MySQL-t [innen](https://dev.mysql.com/downloads/) töltheti le.

Nyissa meg a parancssort, és adja meg a következőt: 

1. Csatlakozás a kiszolgálóhoz a **mysql** parancssori eszköz használatával:
```dos
 mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

2. Kiszolgáló állapotának megtekintése:
```dos
 mysql> status
```
Ha minden megfelelően működik, a parancssori eszköznek a következő kimenettel kell visszatérnie:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mycliserver.database.windows.net via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> További parancsokért, lásd [MySQL 5.6 Reference Manual - 4.5.1 fejezetet](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench GUI eszköz használatával
1.    Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench-et [innen](https://dev.mysql.com/downloads/workbench/) töltheti le és telepítheti.

2.    Az **Új kapcsolat létrehozása** párbeszédpanelen adja meg a következő információkat a **Paraméterek** lapon:

| **Paraméterek** | **Leírás** |
|----------------|-----------------|
|    *Kapcsolat neve* | adja meg a kapcsolat nevét (ez bármi lehet) |
| *Csatlakozási módszer* | válassza a Standard (TCP/IP) lehetőséget |
| *Állomásnév* | mycliserver.database.windows.net (a korábban lejegyzett kiszolgálónév) |
| *Port* | 3306 |
| *Felhasználónév* | myadmin@mycliserver (a korábban lejegyzett kiszolgáló-rendszergazdai felhasználónév) |
| *Jelszó* | a rendszergazdafiók jelszavát a tárolóban tárolhatja |

![új kapcsolat beállítása](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

3.    Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva.

4.    Kattintson az imént létrehozott kapcsolatra, ezzel sikeresen kapcsolódhat a kiszolgálóhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más útmutatókhoz/oktatóanyagokhoz, az alábbiak szerint törölheti azokat: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis megtervezése az Azure CLI-vel](./tutorial-design-database-using-cli.md).

