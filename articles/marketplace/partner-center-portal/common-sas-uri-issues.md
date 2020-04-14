---
title: Gyakori SAS URI-problémák és -javítások – Azure Piactér
description: Gyakori problémák merültek fel, és a közös hozzáférésű aláírásokkal végzett munka során javasolt megoldások.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266239"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Gyakori SAS URI-problémák és -javítások

> [!IMPORTANT]
> Az Azure virtuálisgép-ajánlatok kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a [Közös SAS URI-problémák és -javítások](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) a Cloud Partner Portal utasításait az ajánlatok kezeléséhez.

A következőkben a közös hozzáférésű aláírások (amelyek a megoldáshoz feltöltött virtuális gépek azonosítására és megosztására szolgáló) közös problémák, valamint a javasolt megoldások közösen előforduló gyakori problémák.

| **Probléma** | **Hibaüzenet** | **Hibajavítás** |
| --------- | ------------------- | ------- |
| *Hiba a képek másolásakor* |  |  |
| "?" nem található a SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t az ajánlott eszközökkel. |
| "st" és "se" paraméterek nem a SAS URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a megfelelő **kezdési dátum** és **záró dátum** értékekkel. |
| "sp=rl" nem sas URI-ban | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a `Read` készletével. `List` |
| A SAS URI virtuális merevlemez-névvel rendelkezik szóközzel | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URI-t a szóközök eltávolításához. |
| SAS URI-engedélyezési hiba | `Failure: Copying Images. Not able to download blob due to authorization error.` | Tekintse át és javítsa ki a SAS URI formátumot. Szükség esetén regenerálja. |
| A SAS URI "st" és "se" paraméterei nem rendelkeznek teljes dátum-idő specifikációval | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | A SAS URI **kezdő dátumának** és **záró dátumának** paramétereinek (és`st` `se` részkarakterláncainak) teljes dátum-idő formátummal kell rendelkezniük, például `11-02-2017T00:00:00Z`. A rövidített verziók érvénytelenek (az Azure CLI egyes parancsai alapértelmezés szerint rövidített értékeket generálhatnak). |
|  |  |  |

További információt a [Megosztott hozzáférésű aláírások (SAS) használata](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)című témakörben talál.
