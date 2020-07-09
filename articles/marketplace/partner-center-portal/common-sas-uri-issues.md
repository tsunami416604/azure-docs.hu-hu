---
title: Gyakori SAS URI-problémák és javítások – Azure Marketplace
description: Gyakori problémák merültek fel és javasolták a közös hozzáférési aláírások használatakor.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: anbene
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: 95ce37d92adc3d09c5a09944b094df7971831198
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110725"
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
