---
title: Hozzáférés naplóihoz – Azure CLI – Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a naplózási naplók az Azure Database for MySQL az Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384167"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Naplózási naplók konfigurálása és elérése az Azure CLI-ben

Konfigurálhatja az [Azure Database for MySQL naplózási naplók](concepts-audit-logs.md) az Azure CLI.

> [!IMPORTANT]
> A napló funkciója jelenleg előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató útmutatón való átlépéshez a következőkre van szükség:

- [Azure Database for MySQL szerver](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ez az útmutató megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját használja. A verzió megerősítéséhez írja be az Azure `az --version`CLI parancssorába a . A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése]( /cli/azure/install-azure-cli)című témakört.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

A naplózás engedélyezése és konfigurálása a következő lépésekkel:

1. Kapcsolja be a naplókat úgy, hogy a **audit_logs_enabled** paramétert "BE" -re állítja. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Válassza ki a naplózni kívánt [eseménytípusokat](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adja hozzá azokat a MySQL-felhasználókat, akiket ki szeretne zárni a naplózásból a **audit_log_exclude_users** paraméter frissítésével. Adja meg a felhasználók at a MySQL felhasználónév megadásával.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adja hozzá a naplózáshoz a **naplóba** kerülő mySQL-felhasználókat a audit_log_include_users paraméter frissítésével. Adja meg a felhasználók at a MySQL felhasználónév megadásával.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>További lépések
- További információ a MySQL-alapú Azure Database [naplóiról](concepts-audit-logs.md)
- A naplók konfigurálása az [Azure Portalon](howto-configure-audit-logs-portal.md)