---
title: Fürt létrehozása az Azure Data Explorer hiba
description: Ez a cikk ismerteti a fürt létrehozása az Azure Data Explorer hibaelhárítási lépéseit.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b95dabbdecd98902da3bf8217a14f41019c31e82
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757698"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Hibaelhárítás: Fürt létrehozása az Azure Data Explorer hiba

A fürtlétrehozás meghiúsult az Adatkezelőben az Azure nem túl valószínű esetben kövesse az alábbi lépéseket.

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Hozzon létre egy fürtöt, tagjának kell lennie a *közreműködői* vagy *tulajdonosa* szerepkör esetében az Azure-előfizetést. Ha szükséges, ahol az előfizetés-rendszergazdától, azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy nincs érvényesítési hiba kapcsolódó a fürt neve alatt megadott **fürt létrehozása** az Azure Portalon.

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/). Keresse meg a régióban, ahol próbál a fürt létrehozása az Azure adatkezelő állapotát.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg a fürt létrehozása után az állapot javítja.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).