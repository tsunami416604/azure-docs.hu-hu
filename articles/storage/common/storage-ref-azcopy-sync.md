---
title: azcopy sync | Microsoft dokumentumok
description: Ez a cikk az azcopy sync parancs ra vonatkozó referenciainformációkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988243"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikálja a forráshelyet a célhelyre.

## <a name="synopsis"></a>Áttekintés

Az utolsó módosított időpontok összehasonlításra szolgálnak. A fájl kimarad, ha a célutolsó módosított időpontja újabb.

A támogatott párok a következők:

- helyi < > Azure Blob (SAS- vagy OAuth-hitelesítés használható)
- Az Azure Blob < > Azure Blob (a forrásnak Tartalmaznia kell egy SAS-t, vagy nyilvánosan elérhető; sas- vagy OAuth-hitelesítés használható a célhoz)
- Az Azure File < > Azure-fájl (a forrásnak Tartalmaznia kell egy SAS-t, vagy nyilvánosan elérhető; SAS-hitelesítést kell használni a célhoz)

A szinkronizálási parancs több szempontból is eltér a másolás parancstól:

1. Alapértelmezés szerint a rekurzív jelző igaz, és a szinkronizálás az összes alkönyvtárat másolja. A Szinkronizálás csak akkor másolja át a könyvtárlegfelső szintű fájljait, ha a rekurzív jelző hamis.
2. Virtuális könyvtárak közötti szinkronizáláskor adjon hozzá egy záró perjelet az elérési úthoz (lásd a példákat), ha van egy blob, amelynek neve megegyezik a virtuális könyvtárak egyikével.
3. Ha a "deleteDestination" jelző igaz vagy gyors, majd a szinkronizálás törli a fájlokat és blobokat a cél, amelyek nem szerepelnek a forrásnál.

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Speciális

Ha nem ad meg fájlkiterjesztést, az AzCopy automatikusan felismeri a fájlok tartalomtípusát a helyi lemezről való feltöltéskor a fájlkiterjesztés vagy -tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresendő tábla kicsi, de unix esetén a helyi rendszer mime.types fájljai bővítik, ha egy vagy több ilyen név alatt elérhető:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszerben a MIME-típusok kibontása a beállításjegyzékből lesz kivonva.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Példák

Egyetlen fájl szinkronizálása:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Ugyanaz, mint fent, de ezúttal is számítsa ki a fájltartalom MD5 kivonatát, és mentse a blob Content-MD5 tulajdonságaként:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

A teljes könyvtár szinkronizálása az alkönyvtárakkal együtt (vegye figyelembe, hogy a rekurzív alapértelmezés szerint be van kapcsolva):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

vagy

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Csak a könyvtárban lévő legfelső fájlokszinkronizálása, az alkönyvtárak nem:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Fájlok egy részhalmazának szinkronizálása egy könyvtárban (Például: csak jpg és pdf fájlok, vagy ha a fájlnév "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

A teljes könyvtár szinkronizálása, de bizonyos fájlok kizárása a hatókörből (például: minden foo-val vagy sávval kezdődő fájl):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Egyetlen blob szinkronizálása:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Virtuális könyvtár szinkronizálása:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Szinkronizáljon egy virtuális könyvtárat, amelynek neve megegyezik a blob nevével (adjon hozzá egy záró perjelet az elérési úthoz, hogy félreérthető:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure-fájlkönyvtár szinkronizálása (ugyanaz a szintaxis, mint a Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Ha a include/exclude jelzőket együtt használja, csak az belefoglalási mintáknak megfelelő fájlokat kell megvizsgálni, de a kizárási mintáknak megfelelő fájlokat a rendszer mindig figyelmen kívül hagyja.

## <a name="options"></a>Beállítások

**--block-size-mb** úszó: Használja ezt a blokkméretet (mib-ben megadva), amikor feltölti az Azure Storage-ba vagy letölti az Azure Storage-ból. Az alapértelmezett értéket a fájlméret alapján automatikusan kiszámítja a program. Tizedes törttörtek megengedettek (például: 0,25).

**--check-md5** string Itt adhatja meg, hogy a szigorúan MD5-hash-okat hogyan kell érvényesíteni a letöltéskor. Ez a beállítás csak letöltéskor érhető el. A rendelkezésre álló értékek a következők: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (alapértelmezett 'FailIfDifferent'). (alapértelmezett "FailIfDifferent")

**--delete-destination** string: Azt határozza meg, hogy töröljön-e további fájlokat a célból, amelyek nincsenek jelen a forrásnál. Lehet, hogy igaz, hamis vagy gyors. Ha a rendszer azt szeretné, hogy a rendszer rákérdezzen, a rendszer kérdéseket tesz fel a felhasználónak a fájlok és blobok törlésre való ütemezése előtt. (alapértelmezett "false"). (alapértelmezett "hamis")

**--exclude-attributes** string (csak Windows) Kizárhatja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumlistával. Például: A; S; R

**--exclude-path** string Kizárni ezeket az elérési utakat másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagot(Például: myFolder;myFolder/subDirName/file.pdf). Ha a fiók sokszögelésével együtt használja, az elérési utak nem tartalmazzák a tároló nevét.

**--exclude-pattern** string Exclude fájlokat, ahol a név megegyezik a minta lista. Például: \*.jpg; \*.pdf;exactName

**-h, --súgó** a szinkronizáláshoz

**--include-attributes** string (csak Windows) Csak azokat a fájlokat tartalmazza, amelyek attribútumai megfelelnek az attribútumlistának. Például: A; S; R

**--include-pattern** string: Csak azokat a fájlokat tartalmazza, amelyek neve megegyezik a mintalistával. Például: \*.jpg; \*.pdf;exactName

**--log-level** string Adja meg a napló részletességét a naplófájlhoz, elérhető szintek: INFO(minden kérés és válasz), FIGYELEM(lassú válaszok), HIBA(csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett INFO). (alapértelmezett "INFO")

**--put-md5**                     Hozzon létre egy MD5-kivonatot minden fájlból, és mentse a kivonatot a célblob vagy -fájl Content-MD5 tulajdonságaként. (Alapértelmezés szerint a kivonat NEM jön létre.) Csak feltöltéskor érhető el.

**--rekurzív**                   Alapértelmezés szerint igaz, a könyvtárak közötti szinkronizáláskor rekurzívmódon tekintse meg az alkönyvtárakat. (alapértelmezett igaz). (alapértelmezett igaz)

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
