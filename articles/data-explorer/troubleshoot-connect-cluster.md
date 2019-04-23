---
title: Azure Data Explorer fürt csatlakozási hibák elhárítása
description: Ez a cikk ismerteti a hibaelhárítási lépések az Azure Data Explorer fürthöz csatlakozik.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795256"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Hibaelhárítás: Nem sikerült kapcsolódni az Azure Data Explorer egy fürt

Ha Ön nem tud csatlakozni az Azure Data Explorer egy fürt, kövesse az alábbi lépéseket.

1. Győződjön meg arról, a kapcsolati karakterlánc helyességéről. Alakúnak kell lennie: `https://<ClusterName>.<Region>.kusto.windows.net`, például az alábbi példa: `https://docscluster.westus.kusto.windows.net`.

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Ha nem kap választ *jogosulatlan*.

    Engedélyekkel kapcsolatos további információkért lásd: [adatbázis-engedélyek kezeléséhez](manage-database-permissions.md). Ha szükséges, ahol a fürt rendszergazdája így azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy a fürt még nem lett törölve: áttekintheti a tevékenységnaplót az előfizetésében.

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/). Keresse meg az Azure Data Explorer a régióban, ahol próbál csatlakozni a fürthöz állapotát.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg a fürthöz való csatlakozás után az állapot javítja.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).