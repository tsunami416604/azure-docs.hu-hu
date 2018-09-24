---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003819"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Tárfiókok és virtuális gépek engedélyezett termékváltozatai](../articles/governance/policy/samples/allowed-skus-storage.md) | Ehhez a tárfiókoknak és a virtuális gépeknek engedélyezett termékváltozatokat kell használniuk. Beépített szabályzatok használatával biztosítja az engedélyezett termékváltozatok alkalmazását. Megadhatja az engedélyezett virtuálisgép-termékváltozatok és az engedélyezett tárfiók-termékváltozatok tömbjét. |
| [Engedélyezett tárfiók-termékváltozatok](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | Ehhez a tárfiókoknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [A ritkán használt adatok hozzáférési szintje használatának tiltása a tárfiókok számára](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Megtiltja a ritkán használt adatok hozzáférési szintjének használatát a Blob Storage-fiókokban.  |
| [HTTPS-adatforgalom kényszerítése a tárfiókok számára](../articles/governance/policy/samples/ensure-https-stor-acct.md) | Ehhez a tárfiókoknak HTTPS-forgalmat kell használniuk.  |
| [Fájltitkosítás megkövetelése a tárfiókokban](../articles/governance/policy/samples/ensure-store-file-enc.md) | Megköveteli a fájltitkosítás engedélyezését a tárfiókokban.  |
| [Tárfiók-titkosítás megkövetelése](../articles/governance/policy/samples/req-store-acct-enc.md) | Megköveteli, hogy a tárfiók blobtitkosítást használjon.  |