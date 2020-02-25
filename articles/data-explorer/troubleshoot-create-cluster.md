---
title: Az Azure Adatkezelő-fürt létrehozási hibáinak hibaelhárítása
description: Ez a cikk a fürt Azure-beli Adatkezelő történő létrehozásával kapcsolatos hibaelhárítási lépéseket ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562411"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Hibakeresés: az Azure Adatkezelő nem sikerült létrehozni a fürtöt

Ha nem valószínű, hogy a fürt létrehozása meghiúsul az Azure Adatkezelőban, kövesse az alábbi lépéseket.

1. Győződjön meg arról, hogy rendelkezik a szükséges jogosultságokkal. Fürt létrehozásához az Azure-előfizetéshez tartozó *közreműködő* vagy *tulajdonos* szerepkör tagjának kell lennie. Ha szükséges, működjön együtt az előfizetés rendszergazdájával, hogy hozzá lehessen adni a megfelelő szerepkörhöz.

1. Győződjön meg arról, hogy nincsenek olyan érvényesítési hibák, amelyek a fürt **létrehozása** a Azure Portalben megadott fürt nevével kapcsolatosak.

1. Ellenőrizze az [Azure Service Health irányítópultját](https://azure.microsoft.com/status/). Keresse meg az Azure-Adatkezelő állapotát abban a régióban, ahol létre szeretné hozni a fürtöt.

    Ha az állapot nem **jó** (zöld pipa), próbálja meg létrehozni a fürtöt az állapot javulása után.

1. Ha továbbra is segítségre van szüksége a probléma megoldásához, nyisson meg egy támogatási kérést a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
