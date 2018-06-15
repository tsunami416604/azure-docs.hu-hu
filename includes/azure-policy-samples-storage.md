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
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664631"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [Tárfiókok és virtuális gépek engedélyezett termékváltozatai](../articles/azure-policy/scripts/allowed-skus-storage.md) | Ehhez a tárfiókoknak és a virtuális gépeknek engedélyezett termékváltozatokat kell használniuk. Beépített szabályzatok használatával biztosítja az engedélyezett termékváltozatok alkalmazását. Megadhatja az engedélyezett virtuálisgép-termékváltozatok és az engedélyezett tárfiók-termékváltozatok tömbjét. |
| [Engedélyezett tárfiók-termékváltozatok](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Ehhez a tárfiókoknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [A ritkán használt adatok hozzáférési szintje használatának tiltása a tárfiókok számára](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Megtiltja a ritkán használt adatok hozzáférési szintjének használatát a Blob Storage-fiókokban.  |
| [HTTPS-adatforgalom kényszerítése a tárfiókok számára](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Ehhez a tárfiókoknak HTTPS-forgalmat kell használniuk.  |
| [Fájltitkosítás megkövetelése a tárfiókokban](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Megköveteli a fájltitkosítás engedélyezését a tárfiókokban.  |
| [Tárfiók-titkosítás megkövetelése](../articles/azure-policy/scripts/req-store-acct-enc.md) | Megköveteli, hogy a tárfiók blobtitkosítást használjon.  |