---
title: Az SAS URL-címével kapcsolatos gyakori problémák és javítások az Azure Marketplace-en
description: Gyakori problémák listázása a közös hozzáférésű aláírási URI-k és a lehetséges megoldások használatával.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147042"
---
# <a name="common-sas-url-issues-and-fixes"></a>Az SAS URL-címével kapcsolatos gyakori problémák és javítások

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [sas URL-címekkel kapcsolatos gyakori kérdések és javítások](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) című témakör utasításait.

A következő táblázat a közös hozzáférésű aláírások (amelyek a megoldáshoz feltöltött virtuális merevlemezek azonosítására és megosztására használatos), valamint a javasolt megoldások mellett előforduló gyakori problémákat ismertetik.

| **Probléma** | **Sikertelen üzenet** | **Hibajavítás** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Hiba történt a lemezképek másolásakor* |  |  |
| a "?" nem található az SAS URL-címben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | A SAS URL-cím frissítése a javasolt eszközök használatával. |
| a "St" és az "se" paraméterek nem az SAS URL-címben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címet a megfelelő **kezdő** és **befejező dátum** értékekkel. | 
| "SP = RL" not in SAS URL-cím | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Frissítse a SAS URL-címet az engedélyek `Read` beállításával és `List`. | 
| Az SAS URL-címe szóközökkel rendelkezik a VHD-névben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címét a szóközök eltávolításához. |
| SAS URL-hitelesítési hiba | `Failure: Copying Images. Not able to download blob due to authorization error` | Tekintse át és javítsa ki az SAS URI formátumát. Szükség esetén újragenerált. |
| A SAS URL-cím (St) és az "se" paraméterek nem rendelkeznek teljes dátum-idő specifikációval | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Az SAS URL-cím **kezdő** és **záró dátumának** paraméterei (`st` és `se` alkarakterláncai) teljes datetime formátumot kell megadni, `11-02-2017T00:00:00Z`például:. A rövidített verziók nem érvényesek. (Egyes parancsok az Azure CLI-ben lerövidített értékeket hozhatnak alapértelmezésben.) | 
|  |  |  |

További információ: [Shared Access Signatures (SAS) használata](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
