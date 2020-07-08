---
title: Gyakori SAS URI-problémák és javítások – Azure Marketplace
description: Gyakori problémák merültek fel és javasolták a közös hozzáférési aláírások használatakor.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83724598"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Gyakori SAS URI-problémák és javítások

A következő gyakori problémák merültek fel a közös hozzáférési aláírások használatakor (amelyek a megoldáshoz feltöltött virtuális merevlemezek azonosítására és megosztására használhatók), valamint a javasolt felbontásokkal együtt.

| **Probléma** | **Sikertelen üzenet** | **Hibajavítás** |
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
