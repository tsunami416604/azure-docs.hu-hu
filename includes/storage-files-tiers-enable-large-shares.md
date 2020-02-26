---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597812"
---
Alapértelmezés szerint a standard fájlmegosztás legfeljebb 5 TiB-ra terjedhet, bár a megosztási korlát a 100 TiB-ra is növelhető. Ehhez a Storage-fiók szintjén engedélyeznie kell a *nagyméretű fájlmegosztás* szolgáltatást. A Premium Storage-fiókok (*FileStorage* Storage-fiókok) nem rendelkeznek a nagyméretű fájlmegosztás-szolgáltatás jelzőjével, mivel az összes prémium fájlmegosztás már engedélyezve van a teljes 100 TiB-kapacitás kiépítése érdekében.

A nagyméretű fájlmegosztást csak a helyileg redundáns vagy a zónában redundáns szabványos Storage-fiókok esetében lehet engedélyezni. Ha engedélyezte a nagyméretű fájlmegosztás funkció jelzőjét, a redundancia nem módosítható a Geo-redundáns vagy a Geo-Zone-redundáns tárolóra.

Ha a nagyméretű fájlmegosztást egy meglévő Storage-fiókon szeretné engedélyezni, navigáljon a Storage-fiók tartalomjegyzékében a **konfiguráció** nézetre, és állítsa be a nagyméretű fájlmegosztás rocker-kapcsolóját, hogy engedélyezve legyen:

![Képernyőkép a nagyméretű fájlmegosztás engedélyezése a Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Az 100 TiB-fájlmegosztás a [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell-parancsmagon és az [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI-parancson keresztül is engedélyezhető.

Ha többet szeretne megtudni arról, hogyan engedélyezhető nagyméretű fájlmegosztás az új Storage-fiókoknál, tekintse meg [Az Azure-fájlmegosztás létrehozását](../articles/storage/files/storage-how-to-create-file-share.md)ismertető részt.