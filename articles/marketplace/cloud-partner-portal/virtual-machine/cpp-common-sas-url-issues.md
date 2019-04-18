---
title: Általános SAS URL-cím problémákat, és javítja az Azure Marketplace-en |} A Microsoft Docs
description: Közös hozzáférésű jogosultságkód URI-k és a lehetséges megoldások szolgáltatással kapcsolatos gyakori problémák listája.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/27/2018
ms.author: pbutlerm
ms.openlocfilehash: abb29cd0d31288ba7bfab7024cf7657ab6b9a3d3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879216"
---
# <a name="common-sas-url-issues-and-fixes"></a>Általános SAS URL-cím problémákat és javítások

A következő táblázat felsorolja (amely használata esetén azonosíthatja és a megoldás a feltöltött virtuális merevlemezek megosztása) közös hozzáférésű jogosultságkódok használata során jelentkező gyakori problémák és megoldásuk javasolt.

| **A probléma** | **Hibaüzenet** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Hiba történt a lemezképek másolása* |  |  |
| "?" nem található az SAS URL-címe | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítés az SAS URL-címet használó eszközök ajánlott. |
| "st" és "se" paraméterek nem az SAS URL-címe | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse az SAS URL-cím megfelelő **Kezdődátum** és **Záródátum** értékeket. | 
| "sp = rl" nem az SAS URL-címe | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Az SAS URL-cím frissítése engedéllyel rendelkező `Read` és `List`. | 
| SAS URL-címnek szóközöket a virtuális merevlemez neve | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a szóközöket eltávolítja a SAS URL-címet. |
| SAS URL-engedélyezési hiba | `Failure: Copying Images. Not able to download blob due to authorization error` | Tekintse át, és javítsa ki a SAS URI-formátum. Hozza létre újra, ha szükséges. |
| SAS URL-címe "st" és "se" paraméter nem rendelkezik teljes dátum-idő specifikáció | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL-címet **Kezdődátum** és **záró dátum** paraméterek (`st` és `se` karakterláncrész) teljes dátum és idő formátumban, például kell `11-02-2017T00:00:00Z`. Akkor használhatja rövidített verziók nem érvényesek. (Egyes parancsok az Azure CLI előállíthat akkor használhatja rövidített értékek alapértelmezés szerint.) | 
|  |  |  |

További információkért lásd: [a közös hozzáférésű jogosultságkód (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
