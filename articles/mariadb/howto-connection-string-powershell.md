---
title: Kapcsolatok karakterláncának létrehozása a PowerShell-Azure Database for MariaDB
description: Ez a cikk egy Azure PowerShell példát mutat be a Azure Database for MariaDBhoz való kapcsolódáshoz használt kapcsolati karakterlánc létrehozásához.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: b08a1c60b1225b2786ea869f96ac40cc651c82f3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88185604"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Azure Database for MariaDB kapcsolódási karakterlánc létrehozása a PowerShell-lel

Ez a cikk bemutatja, hogyan lehet létrehozni egy Azure Database for MariaDB-kiszolgáló kapcsolódási karakterláncát. A kapcsolati karakterlánc használatával számos különböző alkalmazásból csatlakozhat egy Azure Database for MariaDBhoz.

## <a name="requirements"></a>Követelmények

Ez a cikk kiindulási pontként a következő útmutatóban létrehozott erőforrásokat használja:

* [Rövid útmutató: Azure Database for MariaDB-kiszolgáló létrehozása a PowerShell használatával](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A `Get-AzMariaDbConnectionString` parancsmag egy kapcsolati sztring létrehozásához használható az alkalmazások Azure Database for MariaDBhoz való csatlakoztatásához. A következő példa a **mydemoserver**-ból származó php-ügyfélhez tartozó kapcsolatok karakterláncát adja vissza.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

A paraméter érvényes értékei a `Client` következők:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Database for MariaDB kiszolgáló paramétereinek testreszabása a PowerShell használatával](howto-configure-server-parameters-using-powershell.md)
