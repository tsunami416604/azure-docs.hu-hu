---
title: azcopy | Microsoft Docs
description: Ez a cikk a azcopy parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513381"
---
# <a name="azcopy"></a>azcopy

A AzCopy olyan parancssori eszköz, amely az Azure Storage-ba és az-ba helyezi át az adatátvitelt.

## <a name="synopsis"></a>Áttekintés

A parancsok általános formátuma: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Ha problémákat szeretne jelenteni, vagy többet szeretne megtudni az eszközről, tekintse meg a következőt: [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Beállítások

**--Cap-Mbps UInt32**   Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**-h,-– Súgó** Súgó a azcopy
      
**--output-Type**  A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text")

## <a name="see-also"></a>Lásd még:

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)
- [azcopy másolása](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
- [azcopy listája](storage-ref-azcopy-list.md)
- [azcopy bejelentkezés](storage-ref-azcopy-login.md)
- [azcopy kijelentkezése](storage-ref-azcopy-logout.md)
- [azcopy](storage-ref-azcopy-make.md)
- [azcopy eltávolítása](storage-ref-azcopy-remove.md)
- [azcopy szinkronizálása](storage-ref-azcopy-sync.md)
