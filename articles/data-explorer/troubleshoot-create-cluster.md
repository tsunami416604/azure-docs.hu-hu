---
title: A fürt létrehozásakor az Azure Data Explorer hiba elhárítása
description: Ez a cikk ismerteti a fürt létrehozása az Azure Data Explorer hibaelhárítási lépéseit.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791687"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Hibaelhárítás: Sikertelen fürt létrehozása az Azure Data Explorer

A fürtlétrehozás meghiúsult az Adatkezelőben az Azure nem túl valószínű esetben kövesse az alábbi lépéseket.

1. Győződjön meg arról, hogy a megfelelő engedélyekkel. Hozzon létre egy fürtöt, tagjának kell lennie a *közreműködői* vagy *tulajdonosa* szerepkör esetében az Azure-előfizetést. Ha szükséges, ahol az előfizetés-rendszergazdától, azokat is hozzáadhatja a megfelelő szerepkört.

1. Győződjön meg arról, hogy nincs érvényesítési hiba kapcsolódó a fürt neve alatt megadott **fürt létrehozása** az Azure Portalon.

1. Ellenőrizze a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/). Keresse meg a régióban, ahol próbál a fürt létrehozása az Azure adatkezelő állapotát.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg a fürt létrehozása után az állapot javítja.

1. Ha továbbra is a probléma megoldásához segítségre van szüksége, nyisson egy támogatási kérelmét a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
