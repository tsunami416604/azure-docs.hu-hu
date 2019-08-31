---
title: azcopy szinkronizálása | Microsoft Docs
description: Ez a cikk a azcopy Sync paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195762"
---
# <a name="azcopy-sync"></a>azcopy szinkronizálása

A forrás helyét replikálja a célhelyre.

## <a name="synopsis"></a>Áttekintés

A rendszer az utolsó módosítás időpontját használja az összehasonlításhoz. A rendszer kihagyja a fájlt, ha a cél utolsó módosításának ideje újabb.

A támogatott párok a következők:

- helyi < – > Azure-Blob (SAS-vagy OAuth-hitelesítés is használható)

A szinkronizálási parancs többféle módon eltér a másolási parancstól:

  1. A rekurzív jelző alapértelmezés szerint be van kapcsolva.
  2. A forrás és a cél nem tartalmazhat mintázatot (például * vagy?).
  3. A belefoglalási és kizárási jelzők a fájlneveknek megfelelő mintázatok listája lehet. Tekintse meg az ábrán látható példa szakaszt.
  4. Ha a célhelyen olyan fájlok vagy Blobok találhatók, amelyek nem szerepelnek a forrásban, a rendszer felszólítja a felhasználót a törlésre.

     Ezt a kérést a megfelelő jelzők használatával lehet meghallgatni a törlési kérdés automatikus megválaszolása érdekében.

### <a name="advanced"></a>Speciális

A AzCopy automatikusan észleli a fájlok tartalomtípusát a helyi lemezről történő feltöltéskor a fájlkiterjesztés vagy a tartalom alapján (ha nincs megadva kiterjesztés).

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

or

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

> [!NOTE]
> Ha a belefoglalási/kizárási jelzők együtt vannak használatban, csak a belefoglalási mintáknak megfelelő fájlok fognak megjelenni, a kizárási mintáknak megfelelők azonban mindig figyelmen kívül lesznek hagyva.

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|--Block-Size-MB lebegőpontos|Ezt a blokk-méretet (a MiB-ben megadott) használja az Azure Storage-ba való feltöltéshez vagy az Azure Storage-ból való letöltéshez A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. A tizedes törtek engedélyezettek (például: 0,25).|
|--ellenőrzési-MD5 karakterlánc|Azt határozza meg, hogy a letöltéskor a szigorúan MD5-kivonatok legyenek érvényesítve. Ez a lehetőség csak a letöltéskor érhető el. Az elérhető értékek a következők: Nincs vizsgálat, bejelentkezés, FailIfDifferent, FailIfDifferentOrMissing. (alapértelmezett "FailIfDifferent").|
|--delete-Destination sztring|meghatározza, hogy a rendszer törli-e a forrásban nem szereplő további fájlokat a célhelyről. Értéke true, false vagy prompt lehet. Ha a beállítás értéke prompt, a rendszer megkérdezi a felhasználót, mielőtt ütemezi a fájlok és a Blobok törlését. (alapértelmezés szerint "false").|
|– karakterlánc kizárása|Zárja ki azokat a fájlokat, amelyeknek a neve megegyezik a minta listával. Például: *. jpg;* . PDF; exactName.|
|-h,-– Súgó|A szinkronizálási parancshoz tartozó súgótartalom megjelenítése.|
|--karakterlánc belefoglalása|Csak olyan fájlokat adjon meg, amelyeknek a neve megegyezik a minta listával. Például: *. jpg;* . PDF; exactName.|
|--log szintű sztring|Adja meg a naplófájl részletességét, a rendelkezésre álló szinteket: INFO (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett "információ").|
|--Put-MD5|Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob vagy fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Csak feltöltéskor érhető el.|
|– rekurzív|Alapértelmezés szerint a címtárak közötti szinkronizáláskor a rendszer rekurzív módon vizsgálja meg az alkönyvtárakat. (alapértelmezés szerint igaz).|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
