---
title: Gyakori SAS URI-problémák és javítások – Azure Marketplace
description: Gyakori problémák merültek fel és javasolták a közös hozzáférési aláírások használatakor.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266239"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Gyakori SAS URI-problémák és javítások

> [!IMPORTANT]
> Az Azure-beli virtuálisgép-ajánlatok felügyeletét az Cloud Partner Portalról a partneri központba helyezi át. Amíg az ajánlatokat át nem telepíti, kövesse az [általános sas URI-hibákkal kapcsolatos](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) utasításokat, és javítsa a Cloud Partner Portal az ajánlatok kezeléséhez.

A következő gyakori problémák merültek fel a közös hozzáférési aláírások használatakor (amelyek a megoldáshoz feltöltött virtuális merevlemezek azonosítására és megosztására használhatók), valamint a javasolt felbontásokkal együtt.

| **Probléma** | **Sikertelen üzenet** | **Hibajavítás** |
| --------- | ------------------- | ------- |
| *Hiba történt a lemezképek másolásakor* |  |  |
| a "?" nem található az SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Az SAS URI frissítése a javasolt eszközök használatával. |
| a "St" és az "se" paraméterek nem szerepelnek az SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a megfelelő **kezdési** és **befejezési dátum** értékekkel. |
| "SP = RL" nem az SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse az SAS URI `Read` -t a és `List`a értékkel beállított engedélyekkel. |
| A SAS URI-ja szóközöket tartalmaz a VHD-névben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a szóközök eltávolításához. |
| SAS URI-hitelesítési hiba | `Failure: Copying Images. Not able to download blob due to authorization error.` | Tekintse át és javítsa ki az SAS URI formátumát. Szükség esetén újragenerált. |
| A SAS URI-ja (St) és az "se" paraméterek nem rendelkeznek teljes dátum-idő specifikációval | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | A `11-02-2017T00:00:00Z`sas URI **kezdő** és **záró dátumának** (`st` és `se` alkarakterláncának) teljes dátum-idő formátumúnak kell lennie, például:. A lerövidített verziók érvénytelenek (az Azure CLI egyes parancsai alapértelmezés szerint lerövidítik az értékeket). |
|  |  |  |

Részletekért lásd: [közös hozzáférésű aláírások (SAS) használata](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
