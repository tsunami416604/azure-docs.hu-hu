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
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318236"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Tárfiókok és virtuális gépek engedélyezett termékváltozatai](../articles/governance/policy/samples/allowed-skus-storage.md) | Ehhez a tárfiókoknak és a virtuális gépeknek engedélyezett termékváltozatokat kell használniuk. Beépített szabályzatok használatával biztosítja az engedélyezett termékváltozatok alkalmazását. Megadhatja az engedélyezett virtuálisgép-termékváltozatok és az engedélyezett tárfiók-termékváltozatok tömbjét. |
| [Engedélyezett tárfiók-termékváltozatok](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Ehhez a tárfiókoknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [A ritkán használt adatok hozzáférési szintje használatának tiltása a tárfiókok számára](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Megtiltja a ritkán használt adatok hozzáférési szintjének használatát a Blob Storage-fiókokban.  |
| [HTTPS-adatforgalom kényszerítése a tárfiókok számára](../articles/governance/policy/samples/ensure-https-storage-account.md) | Ehhez a tárfiókoknak HTTPS-forgalmat kell használniuk.  |
| [Fájltitkosítás megkövetelése a tárfiókokban](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Megköveteli a fájltitkosítás engedélyezését a tárfiókokban.  |
| [Tárfiók-titkosítás megkövetelése](../articles/governance/policy/samples/require-storage-account-encryption.md) | Megköveteli, hogy a tárfiók blobtitkosítást használjon.  |