---
title: Gyakori SAS URI-problémák és javítások – Azure Marketplace
description: Gyakori problémák merültek fel és javasolták a közös hozzáférési aláírások használatakor.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 4eb3c3e893a276aed10807a13a0f2d6d3bc4e71d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87316838"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Gyakori SAS URI-problémák és javítások

A következő gyakori problémák merültek fel a közös hozzáférési aláírások használatakor (amelyek a megoldáshoz feltöltött virtuális merevlemezek azonosítására és megosztására használhatók), valamint a javasolt felbontásokkal együtt.

| **Probléma** | **Sikertelen üzenet** | **Javítás** |
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
