---
title: azcopy eltávolítása | Microsoft Docs
description: Ez a cikk a azcopy Remove parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195905"
---
# <a name="azcopy-remove"></a>azcopy eltávolítása

Entitások törlése Azure Storage Blob, fájlból és Azure Data Lake Storage Gen2ból.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Példák

Egyetlen blob eltávolítása SAS-val:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Teljes virtuális könyvtár eltávolítása SAS-val:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Csak a legfelső blobokat távolítsa el egy virtuális könyvtárban, az alkönyvtárain kívül:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Blobok egy részhalmazának eltávolítása egy virtuális könyvtárban (például: csak jpg-és PDF-fájlok, illetve ha a blob neve "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Távolítson el egy teljes virtuális könyvtárat, de zárja ki bizonyos blobokat a hatókörből (például: minden olyan blob, amely a foo vagy a Bar kifejezéssel végződik):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Egyetlen fájl eltávolítása a Data Lake Storage Gen2ból (belefoglalása és kizárása nem támogatott):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Egyetlen könyvtár eltávolítása a Data Lake Storage Gen2ról (belefoglalása és kizárása nem támogatott):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|– karakterlánc kizárása|zárja ki azokat a fájlokat, amelyeknek a neve megegyezik a minta listával. Például: *. jpg;* . PDF; exactName|
|-h,-– Súgó|Súgótartalom megjelenítése az eltávolítási parancshoz.|
|--karakterlánc belefoglalása|Csak olyan fájlokat adjon meg, amelyeknek a neve megegyezik a minta listával. Például: *. jpg;* . PDF; exactName|
|--log szintű sztring|Adja meg a naplófájl részletességét. A rendelkezésre álló szintek a következők: INFO (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett "információ")|
|– rekurzív|A címtárak közötti szinkronizáláskor a rendszer rekurzív módon vizsgálja az alkönyvtárakat.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
