---
title: Virtuális gépek SAS-meghibásodási üzenetei – Azure Marketplace
description: Gyakori kérdések közös hozzáférési aláírások (SAS) használata esetén.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 7b91cf3d49d88501fb908d157892ec3eb24bee6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284022"
---
# <a name="virtual-machine-sas-failure-messages"></a>Virtuális gépek SAS-meghibásodási üzenetei

A következő gyakori problémák merültek fel a közös hozzáférési aláírások használatakor (amelyek a megoldáshoz feltöltött virtuális merevlemezek azonosítására és megosztására használhatók), valamint a javasolt felbontásokkal együtt.

| Probléma | Sikertelen üzenet | Javítás |
| --------- | ------------------- | ------- |
| *Hiba történt a lemezképek másolásakor* |  |  |
| a "?" nem található az SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Az SAS URI frissítése a javasolt eszközök használatával. |
| a "St" és az "se" paraméterek nem szerepelnek az SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a megfelelő **kezdési** és **befejezési dátum** értékekkel. |
| "SP = RL" nem az SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse az SAS URI-t a és a értékkel beállított engedélyekkel `Read` `List` . |
| A SAS URI-ja szóközöket tartalmaz a VHD-névben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a szóközök eltávolításához. |
| SAS URI-hitelesítési hiba | `Failure: Copying Images. Not able to download blob due to authorization error.` | Tekintse át és javítsa ki az SAS URI formátumát. Szükség esetén újragenerált. |
| A SAS URI-ja (St) és az "se" paraméterek nem rendelkeznek teljes dátum-idő specifikációval | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | A SAS URI **kezdő** és **záró dátumának** ( `st` és `se` alkarakterláncának) teljes dátum-idő formátumúnak kell lennie, például: `11-02-2017T00:00:00Z` . A lerövidített verziók érvénytelenek (az Azure CLI egyes parancsai alapértelmezés szerint lerövidítik az értékeket). |
|  |  |  |

Részletekért lásd: [közös hozzáférésű aláírások (SAS) használata](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="next-steps"></a>Következő lépések

- [SAS URI-azonosítójának előállítása](azure-vm-get-sas-uri.md)
