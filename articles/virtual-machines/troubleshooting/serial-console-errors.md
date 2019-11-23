---
title: Az Azure soros konzol hibái | Microsoft Docs
description: Gyakori hibák az Azure soros konzolon belül
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e4596ae2f92e5dfd99dc7c83857e0c9874358fd4
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949704"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Gyakori hibák az Azure soros konzolon belül
Az Azure soros konzolon az ismert hibák halmaza található. Ezek a hibák és a hozzájuk tartozó enyhítő lépések listája.

## <a name="common-errors"></a>Gyakori hibák

Hiba                            |   Kezelés
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait  *&lt;VMNAME&gt;* . A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. rendszerindítási diagnosztikai hiba ![](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Győződjön meg arról, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport számára engedélyezve van a [rendszerindítási diagnosztika](boot-diagnostics.md) . Ha a soros konzolt egy virtuálisgép-méretezési csoport példányán használja, győződjön meg arról, hogy a példánya rendelkezik a legújabb modellel.
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. ![Delefoglalt hiba](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | A soros konzol eléréséhez a virtuális gép vagy a virtuálisgép-méretezési csoport példányának elindított állapotban kell lennie. Indítsa el a virtuális gép vagy a virtuálisgép-méretezési csoport példányát, és próbálkozzon újra.
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. ![Storage-fiók tűzfala – hiba](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Győződjön meg arról, hogy a rendszerindítási diagnosztika nem rendelkezik fiók-tűzfallal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges. A Serial console by design nem tud működni a rendszerindítási diagnosztika Storage-fiókjában engedélyezve lévő Storage-fiók tűzfalakkal.
Nem rendelkezik a virtuális gép soros konzollal való használatához szükséges engedélyekkel. Győződjön meg arról, hogy a virtuális gépek Közreműködője szerepkör engedélyei.| A soros konzolhoz való hozzáféréshez a virtuális gép vagy a virtuálisgép-méretezési csoport közreműködői szintű hozzáférésének vagy újabb verziójának kell lennie. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Nem található a virtuális gépen a rendszerindítási diagnosztika használatára szolgáló "" Storage-fiók. Ellenőrizze, hogy engedélyezve van-e a rendszerindítási diagnosztika a virtuális gépen, ez a Storage-fiók nem lett törölve, és hozzáfér-e ehhez a Storage-fiókhoz. | Ellenőrizze, hogy nem törölte-e a virtuális gép vagy virtuálisgép-méretezési csoport rendszerindítási diagnosztikai fiókját
A virtuális gép üzembe helyezése még nem sikerült. Győződjön meg arról, hogy a virtuális gép teljesen telepítve van, majd próbálja megismételni a soros konzol kapcsolatát. | Lehetséges, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport továbbra is kiépíthető. Várjon egy kis időt, és próbálkozzon újra.
Nem rendelkezik a virtuális gép rendszerindítási diagnosztikai tárolási fiókjába való íráshoz szükséges engedélyekkel. Győződjön meg arról, hogy legalább a virtuális gép közreműködői engedélyekkel rendelkezik a következőn: "". | Serial console hozzáféréshez közreműködői szintű hozzáférés szükséges a rendszerindítási diagnosztika Storage-fiókhoz. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának  *&lt;STORAGEACCOUNTNAME&gt;* . Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Serial console hozzáféréshez közreműködői szintű hozzáférés szükséges a rendszerindítási diagnosztika Storage-fiókhoz. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Web socket le van zárva, vagy nem nyitható meg. | Előfordulhat, hogy tűzfal-hozzáférést kell hozzáadnia `*.console.azure.com`hoz. Egy részletesebb, de hosszabb módszer a tűzfal hozzáférésének engedélyezése a [Microsoft Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek meglehetősen rendszeresen változnak.
A Serial console a Azure Data Lake Storage Gen2 hierarchikus névtereket használó Storage-fiókkal nem működik. | Ez egy ismert probléma a hierarchikus névterek esetében. A megoldáshoz győződjön meg arról, hogy a virtuális gép rendszerindítási diagnosztikai tárolási fiókja nem Azure Data Lake Storage Gen2 használatával jön létre. Ez a beállítás csak a Storage-fiók létrehozásakor állítható be. Előfordulhat, hogy létre kell hoznia egy különálló rendszerindítási diagnosztikai Storage-fiókot anélkül, hogy Azure Data Lake Storage Gen2 engedélyezve lenne a probléma enyhítése érdekében.


## <a name="next-steps"></a>Következő lépések
* További információ a [Linux rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-linux.md)
* További információ a [Windows rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-windows.md)