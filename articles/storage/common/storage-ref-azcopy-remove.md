---
title: azcopy eltávolítása | Microsoft dokumentumok
description: Ez a cikk az azcopy remove parancs ra vonatkozó referenciainformációkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033995"
---
# <a name="azcopy-remove"></a>azcopy remove

Blobok vagy fájlok törlése egy Azure-tárfiókból.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

Egyetlen blob eltávolítása a SAS-sel:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Teljes virtuális könyvtár eltávolítása SAS-sel:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Csak a virtuális könyvtáron belüli legfelső blobokat távolítsa el, az alkönyvtárakat azonban nem:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

A blobok egy részhalmazának eltávolítása egy virtuális könyvtárban (például: csak jpg és pdf fájlok, vagy ha a blob neve "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Egy teljes virtuális könyvtár eltávolítása, de bizonyos blobok kizárása a hatókörből (például: minden foo-val vagy sávval kezdődő blob):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Adott blobok és virtuális könyvtárak eltávolításával a relatív elérési utak (NEM URL-kódolású) fájlba:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Egyetlen fájlt távolítson el egy hierarchikus névtérrel (include/exclude) tartalmazó/kizárt fájlból.

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Egyetlen könyvtár eltávolítása hierarchikus névtérrel (include/exclude not supported):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Beállítások

**--kizárás-elérési út karakterlánc**      Eltávolításkor zárja ki ezeket az elérési utakat. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagot. Például: myFolder;myFolder/subDirName/file.pdf.

**--exclude-pattern** string Exclude fájlokat, ahol a név megegyezik a minta lista. Például: *.jpg;*. pdf;exactName

**-h, --segítség** az eltávolításhoz

**--include-path** string: Csak ezeket az elérési utakat tartalmazza eltávolításkor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagot. Például: myFolder;myFolder/subDirName/file.pdf

**--include-pattern** string: Csak azokat a fájlokat tartalmazza, amelyek neve megegyezik a mintalistával. Például: *.jpg;*. pdf;exactName

**--files-list-of-files** string Meghatározza a fájl helyét, amely tartalmazza a törölni a fájlok és könyvtárak listáját. A relatív elérési utakat sortörésekkel kell körülhaolni, és az elérési utakat NEM szabad URL-kódolással kódolni.

**--log-level** string Adja meg a napló részletességét a naplófájlhoz. A rendelkezésre álló szintek a következők: INFO(minden kérés/válasz), FIGYELEM(lassú válaszok), ERROR (csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett "INFO") (alapértelmezett "INFO")

**--rekurzív**                Vizsgálja meg az alkönyvtárak rekurzívan szinkronizálás a könyvtárak között.

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
