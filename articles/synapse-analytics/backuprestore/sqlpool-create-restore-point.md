---
title: Felhasználó által definiált visszaállítási pont létrehozása egy dedikált SQL-készlethez
description: Visszaállítási pont létrehozása a dedikált SQL-készlethez.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332131"
---
# <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ebből a cikkből megtudhatja, hogyan hozhat létre egy új, felhasználó által definiált visszaállítási pontot egy dedikált SQL-készlethez az Azure szinapszis Analyticsben a Azure Portal használatával.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Felhasználó által definiált visszaállítási pontok létrehozása a Azure Portal

A felhasználó által definiált visszaállítási pontok a Azure Portal használatával is létrehozhatók.

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) -fiókjába.

2. Navigáljon arra a dedikált SQL-készletre, amelyhez visszaállítási pontot kíván létrehozni.

3. Válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblán, majd válassza az **+ új visszaállítási pont** lehetőséget. Ha az új visszaállítási pont gomb nincs engedélyezve, győződjön meg arról, hogy a dedikált SQL-készlet nincs szüneteltetve.

    ![Új visszaállítási pont](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Adja meg a felhasználó által definiált visszaállítási pont nevét, majd kattintson az **alkalmaz** gombra. A felhasználó által definiált visszaállítási pontok alapértelmezett megőrzési időtartama hét nap.

    ![A visszaállítási pont neve](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>További lépések

- [Meglévő dedikált SQL-készlet visszaállítása](restore-sql-pool.md)

