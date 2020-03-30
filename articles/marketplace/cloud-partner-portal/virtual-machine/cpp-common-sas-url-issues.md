---
title: Gyakori SAS-URL-címproblémák és -javítások az Azure Piactérhez
description: Sorolja fel a közös hozzáférésű uri-k és a lehetséges megoldások használatával kapcsolatos gyakori problémákat.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 47702959474a352a8e13710ec850f789dee4d517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278161"
---
# <a name="common-sas-url-issues-and-fixes"></a>Gyakori SAS-URL-problémák és -javítások

Az alábbi táblázat a megosztott hozzáférésű aláírások (amelyek a megoldáshoz feltöltött virtuális gépek azonosítására és megosztására) való munka során felmerülő gyakori problémákat, valamint a javasolt megoldásokat sorolja fel.

| **Probléma** | **Hibaüzenet** | **Hibajavítás** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Hiba a képek másolásakor* |  |  |
| "?" nem található a SAS URL-címében | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címét az ajánlott eszközökkel. |
| "st" és "se" paraméterek nem a SAS URL-ben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címét a megfelelő **kezdési dátum** és **záró dátum** értékekkel. | 
| Az "sp=rl" nem a SAS URL-címében | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Frissítse a SAS URL-címét `List`a készletével, és. `Read` | 
| A SAS URL-cím virtuális merevlemez-névvel rendelkezik szóközzel | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címét az üres helyek eltávolításához. |
| SAS URL-engedélyezési hiba | `Failure: Copying Images. Not able to download blob due to authorization error` | Tekintse át és javítsa ki a SAS URI formátumot. Szükség esetén regenerálja. |
| A SAS URL "st" és "se" paraméterei nem rendelkeznek teljes dátum-idő specifikációval | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | A SAS URL **kezdő dátuma** `se` és záró `11-02-2017T00:00:00Z` **dátumparaméterei** (`st` és részkarakterláncai) teljes datetime formátummal rendelkeznek, például . A rövidített verziók érvénytelenek. (Az Azure CLI egyes parancsai alapértelmezés szerint rövidített értékeket generálhatnak.) | 
|  |  |  |

További információ: [A megosztott hozzáférésű aláírások (SAS) használata.](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
