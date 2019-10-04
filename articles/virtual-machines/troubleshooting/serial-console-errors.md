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
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129665"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Gyakori hibák az Azure soros konzolon belül
Az Azure soros konzolon az ismert hibák halmaza található. Ezek a hibák és a hozzájuk tartozó enyhítő lépések listája.

## <a name="common-errors"></a>Gyakori hibák

Hiba                            |   Kezelés
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztikai beállításait  *&lt;VMNAME&gt;* . A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. ![Rendszerindítási diagnosztikai hiba](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Győződjön meg arról, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport számára engedélyezve van a rendszerindítási [diagnosztika](boot-diagnostics.md) . Ha a soros konzolt egy virtuálisgép-méretezési csoport példányán használja, győződjön meg arról, hogy a példánya rendelkezik a legújabb modellel.
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, és ismételje meg a soros konzol kapcsolat. ![Delefoglalt hiba](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | A soros konzol eléréséhez a virtuális gép vagy a virtuálisgép-méretezési csoport példányának elindított állapotban kell lennie. Indítsa el a virtuális gép vagy a virtuálisgép-méretezési csoport példányát, és próbálkozzon újra.
Egy "Tiltott" válasz fordult elő a virtuális gép rendszerindítás-diagnosztikai tárfiók elérésekor. ![Storage-fiók tűzfala – hiba](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Győződjön meg arról, hogy a rendszerindítási diagnosztika nem rendelkezik fiók-tűzfallal. Egy elérhető rendszerindítás-diagnosztikai tárfiók a soros konzol működéséhez szükséges.
Nem rendelkezik a virtuális gép soros konzollal való használatához szükséges engedélyekkel. Győződjön meg arról, hogy a virtuális gépek Közreműködője szerepkör engedélyei.| A soros konzolhoz való hozzáféréshez a virtuális gép vagy a virtuálisgép-méretezési csoport közreműködői szintű hozzáférésének vagy újabb verziójának kell lennie. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Nem található a virtuális gépen a rendszerindítási diagnosztika használatára szolgáló "" Storage-fiók. Ellenőrizze, hogy engedélyezve van-e a rendszerindítási diagnosztika a virtuális gépen, ez a Storage-fiók nem lett törölve, és hozzáfér-e ehhez a Storage-fiókhoz. | Ellenőrizze, hogy nem törölte-e a virtuális gép vagy virtuálisgép-méretezési csoport rendszerindítási diagnosztikai fiókját
A virtuális gép üzembe helyezése még nem sikerült. Győződjön meg arról, hogy a virtuális gép teljesen telepítve van, majd próbálja megismételni a soros konzol kapcsolatát. | Lehetséges, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport továbbra is kiépíthető. Várjon egy kis időt, és próbálkozzon újra.
Nem rendelkezik a virtuális gép rendszerindítási diagnosztikai tárolási fiókjába való íráshoz szükséges engedélyekkel. Győződjön meg arról, hogy legalább a virtuális gép közreműködői engedélyekkel rendelkezik a következőn: "". | Serial console hozzáféréshez közreműködői szintű hozzáférés szükséges a rendszerindítási diagnosztika Storage-fiókhoz. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Nem sikerült meghatározni a rendszerindítási diagnosztika tárfiókja erőforráscsoportjának  *&lt;STORAGEACCOUNTNAME&gt;* . Győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép, és rendelkezik a tárfiókhoz való hozzáférést. | Serial console hozzáféréshez közreműködői szintű hozzáférés szükséges a rendszerindítási diagnosztika Storage-fiókhoz. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Web socket le van zárva, vagy nem nyitható meg. | Előfordulhat, hogy hozzá kell adnia a `*.console.azure.com`tűzfalhoz való hozzáférést. Egy részletesebb, de hosszabb módszer a tűzfal hozzáférésének engedélyezése a [Microsoft Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek meglehetősen rendszeresen változnak.


## <a name="next-steps"></a>További lépések
* További információ a [Linux rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-linux.md)
* További információ a [Windows rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-windows.md)