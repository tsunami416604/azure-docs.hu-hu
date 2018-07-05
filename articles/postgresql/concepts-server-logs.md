---
title: Kiszolgálói naplók az Azure Database for postgresql-hez
description: Ez a cikk bemutatja, hogyan Azure-adatbázis PostgreSQL állít elő, lekérdezés és a hiba naplókat, és hogyan jelentkezzen az adatmegőrzési van konfigurálva.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bcca8ce8d11482dd8517992297b7e8a5b94ac8b1
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435490"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Kiszolgálói naplók az Azure Database for postgresql-hez 
Azure Database for PostgreSQL állít elő, lekérdezés és a hiba naplókat. Azonban a tranzakciós naplókhoz való hozzáférést nem támogatott. Lekérdezés és a hiba a naplók segítségével azonosítása, elhárítása és konfigurációs hibák és az optimálisnál rosszabb teljesítmény javításához. További információkért lásd: [hibajelentés és a naplózás](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Hozzáférés a kiszolgálói naplókhoz
A listában, és az Azure Portallal, Azure PostgreSQL server hibanaplójában le [Azure CLI-vel](howto-configure-server-logs-using-cli.md), és az Azure REST API-k.

## <a name="log-retention"></a>Napló megőrzése
Rendszer naplókat használatával beállíthatja a megőrzési időszak a **log\_megőrzési\_időszak** a kiszolgálóhoz társított paraméter. Ez a paraméter mértékegysége a nap. Az alapértelmezett érték 3 nap. A maximális értéke 7 nap. A kiszolgáló kell van-e elegendő lefoglalt tárhely a megőrzött naplófájlok tartalmaznak.
A naplófájlok elforgatása minden egy óra vagy 100 MB méretű, amelyiket hamarabb.

## <a name="configure-logging-for-azure-postgresql-server"></a>Az Azure PostgreSQL server-naplózás beállítása
A kiszolgáló engedélyezheti a lekérdezések naplózása és a hibanaplózás. Hibanaplók automatikus vákuumszivattyú és a kapcsolat és az ellenőrzőpontok információkat is tartalmazhat.

Lekérdezés naplózás engedélyezésével a PostgreSQL adatbázis-példány két kiszolgáló paraméterek beállításával: `log_statement` és `log_min_duration_statement`.

A **log\_utasítás** paraméter határozza meg, melyik SQL-utasítások a rendszer naplózza. Javasoljuk, hogy ez a paraméter beállítása ***összes*** való bejelentkezéshez az összes utasítást; az alapértelmezett értéke none.

A **log\_min\_időtartama\_utasítás** paraméter a korlátot állít be kell jelentkeznie egy utasítás ezredmásodpercben. Az összes SQL-utasítások futtatása hosszabb, mint a paraméter értéke a rendszer naplózza. Ez a paraméter le van tiltva, alapértelmezés szerint a mínuszjel (-1) 1 értékre. Ez a paraméter engedélyezi a nem optimalizált lekérdezések az alkalmazásokban nyomon hasznos lehet.

A **log\_min\_üzenetek** lehetővé teszi, hogy szabályozhatja, hogy mely szintek üzenet naplóba írja a program a kiszolgáló. Az alapértelmezett figyelmeztetés. 

Ezek a beállítások további információkért lásd: [hibajelentés és a naplózás](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) dokumentációját. Különösen konfigurálása az Azure Database for PostgreSQL-kiszolgálói paramétert, lásd: [Azure CLI-vel a kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>További lépések
- Hozzáférhet az Azure CLI parancssori felület használatával a naplókat, lásd: [Azure CLI-vel kiszolgálónaplók konfigurálása és a hozzáférés](howto-configure-server-logs-using-cli.md).
- A kiszolgáló paramétereinek további információkért lásd: [Azure CLI-vel a kiszolgáló konfigurációs paramétereinek testreszabása](howto-configure-server-parameters-using-cli.md).
