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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597812"
---
Alapértelmezés szerint a szabványos fájlmegosztások legfeljebb 5 TiB-re terjedhetnek ki, bár a megosztási korlát 100 TiB-re növelhető. Ehhez engedélyezni kell a *nagy fájlmegosztási* szolgáltatást a tárfiók szintjén. Prémium szintű tárfiókok *(FileStorage* storage fiókok) nem rendelkeznek a nagy fájlmegosztási szolgáltatás jelzője, mivel az összes prémium szintű fájlmegosztások már engedélyezve vannak a teljes 100 TiB-kapacitás kiépítése.

Csak helyileg redundáns vagy zónaredundáns szabványos tárfiókokon engedélyezhet nagy fájlmegosztásokat. Miután engedélyezte a nagy fájlmegosztási szolgáltatásjelzőt, nem módosíthatja a redundanciaszintet georedundáns vagy geozónaredundáns tárolóra.

Ha nagy fájlmegosztásokat szeretne engedélyezni egy meglévő tárfiókban, keresse meg a tárfiók tartalomjegyzékének **Konfiguráció nézetét,** és kapcsolja be a nagy fájlmegosztás-szabályváltót engedélyezve:

![A nagy fájlmegosztás-szabálykezelő váltás engedélyezése az Azure Portalon](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

100 TiB-fájlmegosztást is [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) engedélyezhet a PowerShell-parancsmagés az [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI parancs segítségével.

Ha többet szeretne tudni arról, hogy miként engedélyezheti a nagy fájlmegosztásokat az új tárfiókokon, olvassa el [az Azure-fájlmegosztás létrehozása.](../articles/storage/files/storage-how-to-create-file-share.md)