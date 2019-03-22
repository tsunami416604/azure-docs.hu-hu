---
title: Fürt létrehozása az Azure Data Explorer hiba
description: Ez a cikk ismerteti a fürt létrehozása az Azure Data Explorer hibaelhárítási lépéseit.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9b8bfe2a4b9b7a8432f14fb53b3e7a4cae49a3b4
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189971"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Hibaelhárítás: Fürt létrehozása az Azure Data Explorer hiba

A fürtlétrehozás meghiúsult az Adatkezelőben az Azure nem túl valószínű esetben kövesse az alábbi lépéseket.

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Hozzon létre egy fürtöt, tagjának kell lennie a *közreműködői* vagy *tulajdonosa* szerepkör esetében az Azure-előfizetést. Ha szükséges, ahol az előfizetés-rendszergazdától, azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy nincs érvényesítési hiba kapcsolódó a fürt neve alatt megadott **fürt létrehozása** az Azure Portalon.

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/). Keresse meg a régióban, ahol próbál a fürt létrehozása az Azure adatkezelő állapotát.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg a fürt létrehozása után az állapot javítja.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).