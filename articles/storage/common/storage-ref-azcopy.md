---
title: azcopy | Microsoft Docs
description: Ez a cikk a azcopy parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195892"
---
# <a name="azcopy"></a>azcopy

A AzCopy olyan parancssori eszköz, amely az Azure Storage-ba és az-ba helyezi át az adatátvitelt.

## <a name="synopsis"></a>Áttekintés

A parancsok általános formátuma: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Ha problémákat szeretne jelenteni, vagy többet szeretne megtudni az eszközről [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy), tekintse meg a következő témakört:.

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|-h,-– Súgó|Megjeleníti a azcopy súgójának tartalmát.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

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
