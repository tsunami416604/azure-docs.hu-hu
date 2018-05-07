---
title: SQL-adatbázisok használata Azure veremben |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az erőforrás-szolgáltató SQL eltávolításához elengedhetetlen, először távolítsa el az összes függőséget:

1. Győződjön meg arról, hogy rendelkezik-e az eredeti központi telepítési csomag ezen verzióját az SQL erőforrás-szolgáltató adapter letöltött.

2. Összes felhasználói adatbázis törölni kell az erőforrás-szolgáltató. (A felhasználói adatbázis törlése nem törli az adatokat.) Ez a feladat a felhasználók maguk kell elvégezni.

3. A rendszergazda az üzemeltetési kiszolgáló törölni kell az SQL erőforrás-szolgáltató adapter.

4. A rendszergazda törölnie kell az SQL erőforrás-szolgáltató adapter hivatkozó bármely tervek.

5. A rendszergazda minden termékváltozatok és az SQL erőforrás-szolgáltató adapter társított kvóták kell törölnie.

6. Futtassa újra a telepítési parancsprogram a következő elemeket:
    - A - paraméter eltávolítása
    - Az Azure Resource Manager-végpontok
    - A DirectoryTenantID
    - A szolgáltatás-rendszergazdai fiók hitelesítő adatait

