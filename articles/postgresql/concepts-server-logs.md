---
title: "-Kiszolgáló naplóit Azure-adatbázisban PostgreSQL |} Microsoft Docs"
description: "PostgreSQL az Azure-adatbázis lekérdezés és a hiba naplókat hoz létre."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 696af85cd5609171a719a7e77efbfcdeba0aaaaa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>-Kiszolgáló naplóit Azure-adatbázisban PostgreSQL 
Azure PostgreSQL-adatbázishoz lekérdezés és a hiba generál naplókat. Tranzakciós naplók elérése azonban nem támogatott. Lekérdezés és a hiba naplók segítségével azonosíthatja, hibaelhárításához és konfigurációs hibák és optimálisnál teljesítményének javítása. További információkért lásd: [jelentéskészítési és naplózási hiba](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Hozzáférés a kiszolgálói naplókhoz
Listáról, és töltse le az Azure PostgreSQL server hibanaplójában talál az Azure portál használatával [Azure CLI](howto-configure-server-logs-using-cli.md), és az Azure REST API-k.

## <a name="log-retention"></a>Napló megőrzési
A megőrzési időtartam állíthatja be, a rendszer naplóz, használja a **napló\_megőrzési\_időszak** a kiszolgálóhoz társított paraméter. Ez a paraméter mértékegysége a nap. Az alapértelmezett érték 3 nap. A maximális értéke a 7 nap. A kiszolgáló kell van-e elegendő lefoglalt megtartott naplófájlokat tartalmazó tároló.
A naplófájlok elforgatása minden egy óra vagy 100 MB-os méretet, amelyik előbb következik be.

## <a name="configure-logging-for-azure-postgresql-server"></a>Azure PostgreSQL-kiszolgáló naplózásának konfigurálása
A kiszolgáló használatával engedélyezheti a lekérdezések naplózása és a hibanaplózás. A hibanaplókban automatikus vákuumszivattyú és a kapcsolat és az ellenőrzőpontok információkat is tartalmazhat.

Lekérdezés naplózása engedélyezhető a PostgreSQL DB példány úgy, hogy két kiszolgáló paraméterek: `log\_statement` és `log\_min\_duration\_statement`.

A **napló\_utasítás** paraméter határozza meg, melyik SQL-utasítások naplózza. Javasoljuk, hogy ha a paramétert a ***összes*** összes utasítást; bejelentkezni az alapértelmezett érték: nincs.

A **napló\_min\_időtartam\_utasítás** paraméter értéke ezredmásodpercet fejez be kell jelentkezniük utasítás állítja be a korlátot. Összes SQL-utasítások futtatása hosszabb, mint a paraméter értéke a rendszer naplózza. Ez a paraméter le van tiltva, és alapértelmezés szerint a mínuszjel (-1) 1 értékre. Ez a paraméter engedélyezése az alkalmazások nem optimalizált lekérdezéseket nyomkövetési hasznos lehet.

A **napló\_min\_üzenetek** lehetővé teszi, hogy a kiszolgáló naplóját írt szabályozhatja, hogy mely szintek jelenik meg. Az alapértelmezett figyelmet IGÉNYEL. 

A beállításokkal további információkért lásd: [jelentéskészítési és naplózási hiba](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) dokumentációját. Azure-adatbázis különösen konfigurálása PostgreSQL-kiszolgáló adatait, tekintse meg [testre szabhatja a kiszolgáló konfigurációs paraméterek Azure parancssori felület használatával](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Következő lépések
- Hozzáférés az Azure CLI parancssori felület használatával a naplókat, lásd: [beállítása és hozzáférést kiszolgálónaplókban olvashatók Azure parancssori felület használatával](howto-configure-server-logs-using-cli.md).
- A kiszolgáló paraméterek további információkért lásd: [testre szabhatja a kiszolgáló konfigurációs paraméterek Azure parancssori felület használatával](howto-configure-server-parameters-using-cli.md).
