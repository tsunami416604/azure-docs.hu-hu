---
title: Az Azure Data Explorer fürtkapcsolati hibáinak elhárítása
description: Ez a cikk ismerteti a fürthöz való csatlakozás hibaelhárítási lépéseit az Azure Data Explorerben.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827036"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Hibaelhárítás: Nem sikerült csatlakozni egy fürthöz az Azure Data Explorerben

Ha nem tud csatlakozni egy fürthöz az Azure Data Explorerben, kövesse az alábbi lépéseket.

1. Ellenőrizze a kapcsolati sztring helyességét. Ennek a következő formában `https://<ClusterName>.<Region>.kusto.windows.net`kell lennie: , `https://docscluster.westus.kusto.windows.net`például a következő példa: .

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Ha nem, akkor a *Jogosulatlan* választ kapja.

    További információk a jogosultságokról: [Adatbázis-engedélyek kezelése](manage-database-permissions.md). Ha szükséges, forduljon a fürt rendszergazdájához, aki hozzáadhatja Önt a megfelelő szerepkörhöz.

1. Ellenőrizze, hogy a fürt nem lett-e törölve: tekintse át az előfizetése tevékenységnaplóját.

1. Ellenőrizze az [Azure Service Health irányítópultját](https://azure.microsoft.com/status/). Keresse meg az Azure Data Explorer állapotát abban a régióban, ahol csatlakozni próbál a fürthöz.

    Ha az állapot nem **jó** (zöld pipa), próbáljon meg csatlakozni a fürthöz, miután az állapot javult.

1. Ha továbbra is segítségre van szüksége a probléma megoldásához, nyisson meg egy támogatási kérelmet az [Azure Portalon.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)