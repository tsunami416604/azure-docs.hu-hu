---
title: Azure soros konzol hibái | Microsoft dokumentumok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060687"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Gyakori hibák az Azure soros konzolon belül
Az Azure serial console-on belül ismert hibák vannak. Ez a lista a hibák at és a kockázatcsökkentési lépéseket.

## <a name="common-errors"></a>Gyakori hibák

Hiba                             |   Kezelés
:---------------------------------|:--------------------------------------------|
"Az Azure Serial Console rendszerindítási diagnosztika engedélyezését igényli. Kattintson ide a virtuális gép rendszerindítási diagnosztikájának konfigurálásához." | Győződjön meg arról, hogy a virtuális gép vagy a virtuális gép méretezési csoport [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. Ha a soros konzol egy virtuálisgép-méretezési készlet példány, győződjön meg arról, hogy a példány a legújabb modell.
"Az Azure Serial Console használatához egy virtuális gép futtatásához szükséges. A fenti Start gombbal indítsa el a virtuális gépet."  | A virtuális gép vagy a virtuálisgép méretezési csoport példánya kell egy elindított állapotban a soros konzol eléréséhez (a virtuális gép nem állítható le, vagy felszabadított). Győződjön meg arról, hogy a virtuális gép vagy a virtuális gép méretezési készlet példánya fut, és próbálkozzon újra.
"Az Azure Serial Console nincs engedélyezve ehhez az előfizetéshez, az engedélyezéshez forduljon az előfizetés rendszergazdájához." | Az Azure soros konzol előfizetési szinten letiltható. Ha Ön előfizetés-rendszergazda, [engedélyezheti és letilthatja az Azure Serial Console konzolt.](./serial-console-enable-disable.md) Ha Ön nem előfizetés-rendszergazda, lépjen kapcsolatba az előfizetés rendszergazdájával a következő lépésekért.
A virtuális gép rendszerindító diagnosztikai tárfiókjának elérésekor "Tiltott" válasz történt. | Győződjön meg arról, hogy a rendszerindítási diagnosztika nem rendelkezik fióktűzfallal. A soros konzol működéséhez egy akadálymentes rendszerindítási diagnosztikai tárfiók szükséges. A soros konzol kialakítása szerint nem működik a rendszerindítási diagnosztikai tárfiókban engedélyezett tárfiók tűzfalai.
Nem rendelkezik a virtuális gép soros konzollal való használatához szükséges engedélyekkel. Győződjön meg arról, hogy legalább a Virtual Machine Közreműködő szerepkör engedélyeit.| A soros konzol-hozzáférés megköveteli, hogy közreműködői szintű hozzáférés vagy a virtuális gép vagy a virtuális gép méretezési csoport. További információt az [áttekintő lapon](serial-console-overview.md)talál.
A virtuális gép rendszerindítási diagnosztikájához használt "" tárfiók nem található. Ellenőrizze, hogy a rendszerindítási diagnosztika engedélyezve van-e ehhez a virtuális géphez, ez a tárfiók nem lett törölve, és hozzáférése van-e ehhez a tárfiókhoz. | Ellenőrizze, hogy nem törölte-e a virtuális gép vagy a virtuálisgép-méretezési csoport rendszerindító diagnosztikai tárfiókját
A virtuális géphez való soros konzolkapcsolat a következő hibát észlelte: "Hibás kérés" (400) | Ez akkor fordulhat elő, ha a rendszerindítási diagnosztikai URI helytelen. A "https://" helyett például a "http://" volt használatban. A rendszerindítási diagnosztika URI-ja a következő paranccsal javítható:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Nem rendelkezik a szükséges engedélyekkel a virtuális gép rendszerindító diagnosztikai tárfiókjához való íráshoz. Kérjük, győződjön meg arról, hogy legalább VM közreműködői engedélyekkel rendelkezik | A soros konzolhoz való hozzáférés közreműködői szintű hozzáférést igényel a rendszerindító diagnosztikai tárfiókhoz. További információt az [áttekintő lapon](serial-console-overview.md)talál.
Nem lehet meghatározni a * &lt;STORAGEACCOUNTNAME&gt;* rendszerindító diagnosztikai tárfiók erőforráscsoportját. Ellenőrizze, hogy a rendszerindítási diagnosztika engedélyezve van-e ehhez a virtuális géphez, és rendelkezik-e hozzáféréssel ehhez a tárfiókhoz. | A soros konzolhoz való hozzáférés közreműködői szintű hozzáférést igényel a rendszerindító diagnosztikai tárfiókhoz. További információt az [áttekintő lapon](serial-console-overview.md)talál.
A virtuális gép kiépítése még nem sikerült. Győződjön meg arról, hogy a virtuális gép teljesen telepítve van, és próbálja meg újra a soros konzolkapcsolatot. | A virtuális gép vagy a virtuális gép méretezési csoport továbbra is kiépítés. Várjon egy kicsit, majd próbálkozzon újra.
A webszoftver-szoftvercsatorna be van zárva, vagy nem nyitható meg. | Előfordulhat, hogy tűzfal-hozzáférést `*.console.azure.com`kell hozzáadnia a hoz. Részletesebb, de hosszabb megközelítés, hogy a tűzfal hozzáférést biztosít a [Microsoft Azure Datacenter IP-tartományok](https://www.microsoft.com/download/details.aspx?id=41653), amelyek viszonylag rendszeresen változnak.
Soros konzol nem működik a tárfiók használatával Azure Data Lake Storage Gen2 hierarchikus névterekkel. | Ez egy ismert probléma a hierarchikus névterekkel. A mérséklés érdekében győződjön meg arról, hogy a virtuális gép rendszerindító diagnosztikai tárfiókja nem az Azure Data Lake Storage Gen2 használatával jön létre. Ez a beállítás csak a tárfiók létrehozásakor állítható be. Előfordulhat, hogy létre kell hoznia egy külön rendszerindítási diagnosztikai tárfiókot anélkül, hogy az Azure Data Lake Storage Gen2 engedélyezve lenne a probléma enyhítése érdekében.
A virtuális géphez való soros konzolkapcsolat a következő hibát észlelte: 'Forbidden'(SubscriptionNotEnabled) - Az előfizetés neve nincs definiálva, \<azonosító előfizetés-azonosító> nincs engedélyezve állapotban van | Ez a probléma akkor fordulhat elő, ha az előfizetés, amelyben a felhasználó létrehozta a Cloud Shell-tárfiókot, le van tiltva. A mérséklés érdekében indítsa el a Cloud Shellt, és [hajtsa végre a szükséges lépéseket a](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) Cloud Shell biztonsági tárfiókjának újbóli üzembe bocsátásához az aktuális előfizetésben.

## <a name="next-steps"></a>További lépések
* További információ az [Azure Serial Console for Linux virtuális gépekről](./serial-console-linux.md)
* További információ az [Azure Serial Console for Windows virtuális gépekről](./serial-console-windows.md)