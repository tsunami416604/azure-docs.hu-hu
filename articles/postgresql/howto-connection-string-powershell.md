---
title: Kapcsolatok karakterláncának létrehozása a PowerShell-Azure Database for PostgreSQL
description: Ez a cikk egy Azure PowerShell példát mutat be a Azure Database for PostgreSQLhoz való kapcsolódáshoz használt kapcsolati karakterlánc létrehozásához.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909901"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Azure Database for PostgreSQL kapcsolódási karakterlánc létrehozása a PowerShell-lel

Ez a cikk bemutatja, hogyan lehet létrehozni egy Azure Database for PostgreSQL-kiszolgáló kapcsolódási karakterláncát. A kapcsolati karakterlánc használatával számos különböző alkalmazásból csatlakozhat egy Azure Database for PostgreSQLhoz.

## <a name="requirements"></a>Követelmények

Ez a cikk kiindulási pontként a következő útmutatóban létrehozott erőforrásokat használja:

* [Rövid útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása a PowerShell használatával](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A `Get-AzPostgreSqlConnectionString` parancsmag egy kapcsolati sztring létrehozásához használható az alkalmazások Azure Database for PostgreSQLhoz való csatlakoztatásához. A következő példa a **mydemoserver**-ból származó php-ügyfélhez tartozó kapcsolatok karakterláncát adja vissza.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

A paraméter érvényes értékei a `Client` következők:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL kiszolgáló paramétereinek testreszabása a PowerShell használatával](howto-configure-server-parameters-using-powershell.md)
