---
title: Nem sikerült kapcsolódni az Azure Data Explorer egy fürt
description: Ez a cikk ismerteti a hibaelhárítási lépések Azure.Data Explorer fürthöz csatlakozik.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 89ae8bd4139623cfafe811b7c82433cfb8400611
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189665"
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