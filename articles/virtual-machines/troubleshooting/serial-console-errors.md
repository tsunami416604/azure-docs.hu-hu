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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060687"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Gyakori hibák az Azure soros konzolon belül
Az Azure soros konzolon az ismert hibák halmaza található. Ezek a hibák és a hozzájuk tartozó enyhítő lépések listája.

## <a name="common-errors"></a>Gyakori hibák

Hiba                             |   Kezelés
:---------------------------------|:--------------------------------------------|
"Az Azure soros konzoljának engedélyezni kell a rendszerindítási diagnosztika használatát. Kattintson ide a virtuális gép rendszerindítási diagnosztika konfigurálásához. " | Győződjön meg arról, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport számára engedélyezve van a [rendszerindítási diagnosztika](boot-diagnostics.md) . Ha a soros konzolt egy virtuálisgép-méretezési csoport példányán használja, győződjön meg arról, hogy a példánya rendelkezik a legújabb modellel.
"Az Azure soros konzoljának működéséhez virtuális gépnek kell futnia. A virtuális gép elindításához használja a fenti Start gombot. "  | A virtuális gép vagy virtuálisgép-méretezési csoport példányának elindított állapotban kell lennie a soros konzol eléréséhez (a virtuális gép nem állítható le vagy nem foglalható le). Győződjön meg arról, hogy a virtuális gép vagy virtuálisgép-méretezési csoport példánya fut, és próbálkozzon újra.
"Az Azure soros konzol nincs engedélyezve ehhez az előfizetéshez, forduljon az előfizetés rendszergazdájához az engedélyezéshez." | Az Azure soros konzol előfizetési szinten letiltható. Ha Ön előfizetés-rendszergazda, engedélyezheti [és letilthatja az Azure soros konzolját](./serial-console-enable-disable.md). Ha Ön nem előfizetés-rendszergazda, a további lépésekért forduljon az előfizetés rendszergazdájához.
A virtuális gép rendszerindítási diagnosztikai tárolási fiókjához való hozzáférés "tiltott" választ észlelt. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nem rendelkezik fiók-tűzfallal. A soros konzol működéséhez elérhető rendszerindítási diagnosztikai Storage-fiók szükséges. A Serial console by design nem tud működni a rendszerindítási diagnosztika Storage-fiókjában engedélyezve lévő Storage-fiók tűzfalakkal.
Nem rendelkezik a virtuális gép soros konzollal való használatához szükséges engedélyekkel. Győződjön meg arról, hogy legalább a virtuális gép közreműködői szerepkörének engedélyei vannak.| A soros konzolhoz való hozzáféréshez a virtuális gép vagy a virtuálisgép-méretezési csoport közreműködői szintű hozzáférésének vagy újabb verziójának kell lennie. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Nem található a virtuális gépen a rendszerindítási diagnosztika használatára szolgáló "" Storage-fiók. Ellenőrizze, hogy engedélyezve van-e a rendszerindítási diagnosztika a virtuális gépen, ez a Storage-fiók nem lett törölve, és hozzáfér-e ehhez a Storage-fiókhoz. | Ellenőrizze, hogy nem törölte-e a virtuális gép vagy virtuálisgép-méretezési csoport rendszerindítási diagnosztikai fiókját
A soros konzol a virtuális géphez való kapcsolata hibát észlelt: "hibás kérelem" (400) | Ez akkor fordulhat elő, ha a rendszerindítási diagnosztikai URI-ja helytelen. A "https://" helyett például "http://" volt használatban. A rendszerindítási diagnosztikai URI-t a következő paranccsal lehet megjavítani:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Nem rendelkezik a virtuális gép rendszerindítási diagnosztikai tárolási fiókjába való íráshoz szükséges engedélyekkel. Győződjön meg arról, hogy legalább a virtuális gép közreműködői engedélyekkel rendelkezik | Serial console hozzáféréshez közreműködői szintű hozzáférés szükséges a rendszerindítási diagnosztika Storage-fiókhoz. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
Nem határozható meg a rendszerindítási diagnosztikai fiók * &lt;STORAGEACCOUNTNAME&gt;* tartozó erőforráscsoport. Ellenőrizze, hogy engedélyezve van-e a rendszerindítási diagnosztika ehhez a virtuális géphez, és hozzáfér-e ehhez a Storage-fiókhoz. | Serial console hozzáféréshez közreműködői szintű hozzáférés szükséges a rendszerindítási diagnosztika Storage-fiókhoz. További információkért tekintse meg az [Áttekintés oldalt](serial-console-overview.md).
A virtuális gép üzembe helyezése még nem sikerült. Győződjön meg arról, hogy a virtuális gép teljesen telepítve van, majd próbálja megismételni a soros konzol kapcsolatát. | Lehetséges, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport továbbra is kiépíthető. Várjon egy kis időt, és próbálkozzon újra.
A webes szoftvercsatorna be van zárva, vagy nem nyitható meg. | Előfordulhat, hogy hozzá kell adnia a `*.console.azure.com`tűzfalhoz való hozzáférést. Egy részletesebb, de hosszabb módszer a tűzfal hozzáférésének engedélyezése a [Microsoft Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek meglehetősen rendszeresen változnak.
A Serial console a Azure Data Lake Storage Gen2 hierarchikus névtereket használó Storage-fiókkal nem működik. | Ez egy ismert probléma a hierarchikus névterek esetében. A megoldáshoz győződjön meg arról, hogy a virtuális gép rendszerindítási diagnosztikai tárolási fiókja nem Azure Data Lake Storage Gen2 használatával jön létre. Ez a beállítás csak a Storage-fiók létrehozásakor állítható be. Előfordulhat, hogy létre kell hoznia egy különálló rendszerindítási diagnosztikai Storage-fiókot anélkül, hogy Azure Data Lake Storage Gen2 engedélyezve lenne a probléma enyhítése érdekében.
A soros konzol kapcsolata a virtuális géppel hibát észlelt: "tiltott" (SubscriptionNotEnabled) – az előfizetés neve nem definiált \<, azonosító előfizetés-azonosító> nem engedélyezett állapotú, nem definiált | Ez a probléma akkor fordulhat elő, ha az előfizetés, amellyel a felhasználó létrehozta Cloud Shell Storage-fiókját, le van tiltva. A megoldás elindításához indítsa el Cloud Shell, és [hajtsa végre a szükséges lépéseket](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) ahhoz, hogy a jelenlegi előfizetésben Cloud Shell a biztonsági mentést végző fiók újralétesítéséhez.

## <a name="next-steps"></a>További lépések
* További információ a [Linux rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-linux.md)
* További információ a [Windows rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-windows.md)