---
title: Az Azure Data Explorer-fürt létrehozásának hibája
description: Ez a cikk a fürt azure Data Explorerben való létrehozásának lépéseit ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562411"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Hibaelhárítás: Az Azure Data Explorer fürtlétrehozása sikertelen

Abban a valószínűtlen esetben, ha a fürt létrehozása sikertelen az Azure Data Explorerben, kövesse az alábbi lépéseket.

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Fürt létrehozásához az Azure-előfizetés *közreműködői* vagy *tulajdonosi* szerepkörének tagjának kell lennie. Szükség esetén működjön együtt az előfizetés rendszergazdájával, hogy hozzávehessenek a megfelelő szerepkörhöz.

1. Győződjön meg arról, hogy nincsenek érvényesítési hibák az Azure Portalon a **Fürt létrehozása** csoportban megadott fürtnevével kapcsolatban.

1. Ellenőrizze az [Azure Service Health irányítópultját](https://azure.microsoft.com/status/). Keresse meg az Azure Data Explorer állapotát abban a régióban, ahol a fürtöt próbálja létrehozni.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg létrehozni a fürtöt az állapot javítása után.

1. Ha továbbra is segítségre van szüksége a probléma megoldásához, nyisson meg egy támogatási kérelmet az [Azure Portalon.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
