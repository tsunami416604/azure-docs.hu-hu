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
ms.openlocfilehash: f66dcc55b01b407c59c65ea300757ab4ee1002ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965058"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Elhárítása: Hiba az Adatkezelőben az Azure-fürthöz való csatlakozáshoz

Ha Ön nem tud csatlakozni az Azure Data Explorer egy fürt, kövesse az alábbi lépéseket.

1. Győződjön meg arról, a kapcsolati karakterlánc helyességéről. Alakúnak kell lennie: `https://<ClusterName>.<Region>.kusto.windows.net`, például az alábbi példa: `https://docscluster.westus.kusto.windows.net`.

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Ha nem kap választ *jogosulatlan*.

    Engedélyekkel kapcsolatos további információkért lásd: [adatbázis-engedélyek kezeléséhez](manage-database-permissions.md). Ha szükséges, ahol a fürt rendszergazdája így azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy a fürt még nem lett törölve: áttekintheti a tevékenységnaplót az előfizetésében.

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/>). Keresse meg az Azure Data Explorer a régióban, ahol próbál csatlakozni a fürthöz állapotát.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg a fürthöz való csatlakozás után az állapot javítja.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com).