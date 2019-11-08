---
title: Az SAS URL-címével kapcsolatos gyakori problémák és javítások az Azure Marketplace-en
description: Gyakori problémák listázása a közös hozzáférésű aláírási URI-k és a lehetséges megoldások használatával.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813316"
---
# <a name="common-sas-url-issues-and-fixes"></a>Az SAS URL-címével kapcsolatos gyakori problémák és javítások

A következő táblázat a közös hozzáférésű aláírások (amelyek a megoldáshoz feltöltött virtuális merevlemezek azonosítására és megosztására használatos), valamint a javasolt megoldások mellett előforduló gyakori problémákat ismertetik.

| **Kérdés** | **Sikertelen üzenet** | **Javítsa ki** | 
| --------- | ------------------- | ------- | 
| &emsp;*hiba a képek másolásakor* |  |  |
| a "?" nem található az SAS URL-címben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | A SAS URL-cím frissítése a javasolt eszközök használatával. |
| a "St" és az "se" paraméterek nem az SAS URL-címben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címet a megfelelő **kezdő** és **befejező dátum** értékekkel. | 
| "SP = RL" not in SAS URL-cím | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Frissítse a SAS URL-címet `Read` és `List`engedélyekkel. | 
| Az SAS URL-címe szóközökkel rendelkezik a VHD-névben | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Frissítse a SAS URL-címét a szóközök eltávolításához. |
| SAS URL-hitelesítési hiba | `Failure: Copying Images. Not able to download blob due to authorization error` | Tekintse át és javítsa ki az SAS URI formátumát. Szükség esetén újragenerált. |
| A SAS URL-cím (St) és az "se" paraméterek nem rendelkeznek teljes dátum-idő specifikációval | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Az SAS URL-cím **kezdő dátumának** és **befejezési dátumának** paraméterei (`st` és `se` alkarakterláncok) teljes datetime formátumot kell megadni, például `11-02-2017T00:00:00Z`. A rövidített verziók nem érvényesek. (Egyes parancsok az Azure CLI-ben lerövidített értékeket hozhatnak alapértelmezésben.) | 
|  |  |  |

További információ: [Shared Access Signatures (SAS) használata](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
