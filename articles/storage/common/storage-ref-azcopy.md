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
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038092"
---
# <a name="azcopy"></a>azcopy

A AzCopy olyan parancssori eszköz, amely az Azure Storage-ba és az-ba helyezi át az adatátvitelt.

## <a name="synopsis"></a>Áttekintés

A parancsok általános formátuma: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Ha problémákat szeretne jelenteni, vagy többet szeretne megtudni az eszközről, tekintse meg a következőt: [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="options"></a>Beállítások

**--Cap-Mbps UInt32**   Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**-h,-– Súgó** Súgó a azcopy
      
**--output-Type**  A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text")

## <a name="see-also"></a>Lásd még

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)
- [azcopy pad](storage-ref-azcopy-bench.md)
- [azcopy másolása](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy-feladatok](storage-ref-azcopy-jobs.md)
- [azcopy-feladatok tisztítása](storage-ref-azcopy-jobs-clean.md)
- [azcopy feladatok listája](storage-ref-azcopy-jobs-list.md)
- [azcopy feladatok eltávolítása](storage-ref-azcopy-jobs-remove.md)
- [azcopy-feladatok folytatása](storage-ref-azcopy-jobs-resume.md)
- [azcopy-feladatok megjelenítése](storage-ref-azcopy-jobs-show.md)
- [azcopy listája](storage-ref-azcopy-list.md)
- [azcopy bejelentkezés](storage-ref-azcopy-login.md)
- [azcopy kijelentkezése](storage-ref-azcopy-logout.md)
- [azcopy](storage-ref-azcopy-make.md)
- [azcopy eltávolítása](storage-ref-azcopy-remove.md)
- [azcopy szinkronizálása](storage-ref-azcopy-sync.md)
