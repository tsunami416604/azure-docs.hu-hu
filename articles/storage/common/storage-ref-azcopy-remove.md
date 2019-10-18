---
title: azcopy eltávolítása | Microsoft Docs
description: Ez a cikk a azcopy Remove parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fc23afb9a407fc2e6689c5c8766cb4beba868269
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513444"
---
# <a name="azcopy-remove"></a>azcopy remove

Blobok vagy fájlok törlése egy Azure Storage-fiókból.

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

Távolítsa el a megadott blobokat és virtuális könyvtárakat úgy, hogy a relatív elérési utakat (nem pedig URL-kódolású) egy fájlba helyezi:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Egyetlen fájl eltávolítása olyan Blob Storage-fiókból, amely hierarchikus névtérrel rendelkezik (belefoglalása/kizárása nem támogatott).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Egyetlen könyvtár eltávolítása olyan Blob Storage-fiókból, amely hierarchikus névtérrel rendelkezik (belefoglalása/kizárása nem támogatott):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Beállítások

**--kizárás-elérésiút-karakterlánc**      Az elérési utak kizárása a eltávolításakor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját. Például: myFolder; myFolder/subDirName/file. PDF.

**--kizárás-Pattern** karakterlánc zárja ki azokat a fájlokat, amelyeknek a neve megegyezik a minta listával. Például: *. jpg;* . PDF; exactName

**-h,--Súgó** az eltávolításhoz

a- **-include-Path** sztring csak az eltávolításkor megadott elérési utakat tartalmazza. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját. Például: myFolder; myFolder/subDirName/file. pdf

**--include-Pattern** sztring csak olyan fájlokat tartalmazhat, amelyekben a név megegyezik a minta listával. Például: *. jpg;* . PDF; exactName

**--a-Files List-of-** Files karakterlánc meghatározza a törlendő fájlok és könyvtárak listáját tartalmazó fájl helyét. A relatív elérési utakat sortöréssel kell elválasztani, és az elérési utakat nem szabad URL-kódolással ellátni.

**--a log-Level** karakterlánc határozza meg a naplófájl részletességét. Az elérhető szintek a következők: INFO (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérelmek), és nincs (nincs kimeneti napló). (alapértelmezett "INFO") (alapértelmezett "információ")

**– rekurzív**                A címtárak közötti szinkronizáláskor a rendszer rekurzív módon vizsgálja az alkönyvtárakat.

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
