---
title: azcopy szinkronizálása | Microsoft Docs
description: Ez a cikk a azcopy Sync paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8b4ab0e44f2432056c9c94061c59c99c89a6407d
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513422"
---
# <a name="azcopy-sync"></a>azcopy sync

A forrás helyét replikálja a célhelyre.

## <a name="synopsis"></a>Áttekintés

A rendszer az utolsó módosítás időpontját használja az összehasonlításhoz. A rendszer kihagyja a fájlt, ha a cél utolsó módosításának ideje újabb.

A támogatott párok a következők:

- helyi < – > Azure-Blob (SAS-vagy OAuth-hitelesítés is használható)
- Azure Blob <-> Azure Blob (a forrásnak tartalmaznia kell SAS-t vagy nyilvánosan elérhetőnek kell lennie, vagy az SAS vagy a OAuth hitelesítés használható a célhelyre)
- Azure file <-> Azure-fájl (a forrásnak tartalmaznia kell SAS vagy nyilvánosan elérhető; Az SAS-hitelesítést a célhelyre kell használni.)

A szinkronizálási parancs többféle módon eltér a másolási parancstól:

1. Alapértelmezés szerint a rekurzív jelző igaz értékű, és a szinkronizálás az összes alkönyvtárat átmásolja. A szinkronizálás csak akkor másolja a legfelső szintű fájlokat egy könyvtárba, ha a rekurzív jelző hamis.
2. A virtuális könyvtárak közötti szinkronizáláskor adjon hozzá egy perjelet az elérési úthoz (példák), ha van olyan blob, amelynek a neve megegyezik a virtuális könyvtárak egyikével.
3. Ha az "deleteDestination" jelző értéke TRUE (igaz) vagy prompt, akkor a Sync törli a célhelyen található fájlokat és blobokat, amelyek nem szerepelnek a forrásban.

### <a name="advanced"></a>Extra szintű

Ha nem ad meg fájlkiterjesztést, a AzCopy automatikusan észleli a fájlok tartalomtípusát a helyi lemezről történő feltöltéskor a fájlkiterjesztés vagy a tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresési táblázat kicsi, de a UNIX rendszeren a helyi rendszer MIME. types fájl (ok), ha az alábbi nevek közül egy vagy több található:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszeren a MIME-típusokat a rendszer kinyeri a beállításjegyzékből.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>Példák

Egyetlen fájl szinkronizálása:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Ugyanaz, mint a fentiekben, de ezúttal is kiszámítja a fájl tartalmának MD5-kivonatát, és a blob Content-MD5 tulajdonságként menti azt:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Egy teljes könyvtár szinkronizálása alkönyvtárakkal együtt (a rekurzív alapértelmezés szerint a rekurzív):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

vagy

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Csak a legfelső fájlokat szinkronizálja egy könyvtáron belül, de nem az alkönyvtáraiban:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Fájlok egy részhalmazának szinkronizálása egy címtárban (például: csak jpg-és PDF-fájlok, illetve ha a fájl neve "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Egy teljes címtár szinkronizálása, de bizonyos fájlok kizárása a hatókörből (például: minden olyan fájl, amely foo-vel kezdődik, vagy a sáv végén végződik):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Egyetlen blob szinkronizálása:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Sync a virtual directory:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Egy blobtal megegyező nevű virtuális könyvtár szinkronizálása (a egyértelműsítse az elérési útra a záró perjelet adja hozzá):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure file Directory szinkronizálása (a Blobtal megegyező szintaxis):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Ha a belefoglalási/kizárási jelzők együtt vannak használatban, csak a belefoglalási mintáknak megfelelő fájlok fognak megjelenni, a kizárási mintáknak megfelelők azonban mindig figyelmen kívül lesznek hagyva.

## <a name="options"></a>Beállítások

**--Block-Size-MB** lebegőpontos használata esetén ez a blokk mérete (a MIB-ben van megadva) az Azure Storage-ba való feltöltéskor vagy az Azure Storage-ból való letöltéskor. A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. Tizedes törtek engedélyezettek (például: 0,25).

**--ellenőrzés-MD5** karakterlánc megadja, hogy a letöltéskor milyen szigorúan kell ellenőrizni az MD5-kivonatok érvényességét. Ez a lehetőség csak a letöltéskor érhető el. Az elérhető értékek a következők: nincs vizsgálat, bejelentkezés, FailIfDifferent, FailIfDifferentOrMissing. (alapértelmezett érték: "FailIfDifferent"). (alapértelmezett "FailIfDifferent")

**--delete-Destination** sztring meghatározza, hogy a rendszer törli-e a forrásban nem szereplő további fájlokat a célhelyről. Értéke true, false vagy prompt lehet. Ha a beállítás értéke prompt, a rendszer megkérdezi a felhasználót, mielőtt ütemezi a fájlok és a Blobok törlését. (alapértelmezett érték: "false"). (alapértelmezett "false")

**– kizárás – attribútumok** karakterlánca (csak Windows) kizárhatja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumok listájával. Például: A; S R

**--kizárás-Pattern** karakterlánc zárja ki azokat a fájlokat, amelyeknek a neve megegyezik a minta listával. Például: *. jpg;* . PDF; exactName

**-h,--Súgó** a szinkronizáláshoz

**– include-attributes** sztring (csak Windows) csak azokat a fájlokat tartalmazza, amelyek attribútumai megegyeznek az attribútumok listájával. Például: A; S R

**--include-Pattern** sztring csak olyan fájlokat tartalmazhat, amelyekben a név megegyezik a minta listával. Például: *. jpg;* . PDF; exactName

**--a log szintű** karakterlánc határozza meg a naplófájl részletességét, a rendelkezésre álló szinteket: info (minden kérelem és válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérések), és nincs (nincs kimeneti napló). (alapértelmezett információ). (alapértelmezett "információ")

**--put-MD5**                     Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob vagy fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Csak feltöltéskor érhető el.

**– rekurzív**                   Alapértelmezés szerint a címtárak közötti szinkronizáláskor a rendszer rekurzív módon vizsgálja meg az alkönyvtárakat. (alapértelmezés szerint igaz). (alapértelmezett true)

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Lehetőség|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
