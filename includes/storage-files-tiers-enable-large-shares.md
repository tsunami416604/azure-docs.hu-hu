---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011731"
---
Alapértelmezés szerint a standard fájlmegosztás legfeljebb 5 TiB-ra terjedhet, bár a megosztási korlát a 100 TiB-ra is növelhető. Ehhez a Storage-fiók szintjén engedélyeznie kell a *nagyméretű fájlmegosztás* szolgáltatást. A Premium Storage-fiókok (*FileStorage* Storage-fiókok) nem rendelkeznek a nagyméretű fájlmegosztás-szolgáltatás jelzőjével, mivel az összes prémium fájlmegosztás már engedélyezve van a teljes 100 TiB-kapacitás kiépítése érdekében.

A nagyméretű fájlmegosztást csak a helyileg redundáns vagy a zónában redundáns szabványos Storage-fiókok esetében lehet engedélyezni. Ha engedélyezte a nagyméretű fájlmegosztás funkció jelzőjét, a redundancia nem módosítható a Geo-redundáns vagy a Geo-Zone-redundáns tárolóra.

Ha a nagyméretű fájlmegosztást egy meglévő Storage-fiókon szeretné engedélyezni, navigáljon a Storage-fiók tartalomjegyzékében a **konfiguráció** nézetre, és állítsa be a nagyméretű fájlmegosztás rocker-kapcsolóját, hogy engedélyezve legyen:

![Képernyőkép a nagyméretű fájlmegosztás engedélyezése a Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Az 100 TiB-fájlmegosztás a [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell-parancsmagon és az [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI-parancson keresztül is engedélyezhető. A nagyméretű fájlok megosztásának engedélyezésével kapcsolatos részletes utasításokért lásd: [nagyméretű fájlmegosztás engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Ha többet szeretne megtudni arról, hogyan hozhatók létre fájlmegosztás az új Storage-fiókokban, tekintse meg [Az Azure-fájlmegosztás létrehozását](../articles/storage/files/storage-how-to-create-file-share.md)ismertető részt.