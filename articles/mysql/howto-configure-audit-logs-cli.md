---
title: Hozzáférés a naplókhoz – Azure CLI – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja és érheti el a Azure Database for MySQL naplóit az Azure CLI-vel.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 568f49565c6e6d8062f8869566cf3879b7c97eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503327"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Eseménynaplók konfigurálása és elérése az Azure CLI-ben

Az [Azure Database for MySQL naplókat](concepts-audit-logs.md) az Azure CLI-ből is konfigurálhatja.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ehhez az útmutatóhoz az Azure CLI 2,0-es vagy újabb verzióját kell használnia. A verzió megerősítéséhez az Azure CLI parancssorában adja meg a következőt: `az --version` . Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

A naplózást a következő lépésekkel engedélyezheti és konfigurálhatja:

1. A naplók bekapcsolásához állítsa a **audit_logs_enabled** paramétert "on" értékre. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Válassza ki a naplózni kívánt [események típusát](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. A **audit_log_exclude_users** paraméter frissítésével adja hozzá a kizárni kívánt MySQL-felhasználókat a naplózásból. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adja hozzá a naplózáshoz hozzáadni kívánt MySQL-felhasználókat a **audit_log_include_users** paraméter frissítésével. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>További lépések
- További információ a Azure Database for MySQL [naplózási naplóiról](concepts-audit-logs.md)
- Ismerje meg, hogyan konfigurálhatja a naplókat a [Azure Portal](howto-configure-audit-logs-portal.md)
