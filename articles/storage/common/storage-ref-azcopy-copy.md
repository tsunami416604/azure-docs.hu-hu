---
title: azcopy másolás | Microsoft Docs
description: Ez a cikk a azcopy másolási parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195736"
---
# <a name="azcopy-copy"></a>azcopy másolása

A forrásadatok másolása a célhelyre.

## <a name="synopsis"></a>Áttekintés

A támogatott utasítások a következők:

- helyi < – > Azure Blob (SAS vagy OAuth Authentication)
- helyi < – > Azure-fájl (megosztás/könyvtár SAS-hitelesítése)
- helyi < – > ADLS Gen 2 (SAS, OAuth vagy SharedKey Authentication)
- Azure Blob (SAS vagy Public) <-> Azure Blob (SAS vagy OAuth Authentication)
- Azure-fájl (SAS) – > Azure Block blob (SAS vagy OAuth hitelesítés)
- AWS S3 (hozzáférési kulcs) – > Azure Block blob (SAS vagy OAuth Authentication)

További információért tekintse meg a példákat.

### <a name="advanced"></a>Speciális

A AzCopy automatikusan észleli a fájlok tartalomtípusát a helyi lemezről történő feltöltéskor a fájlkiterjesztés vagy a tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresési táblázat kicsi, de UNIX rendszeren a helyi rendszer MIME. types fájl (ok), ha az alábbi nevek közül egy vagy több található:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszeren a MIME-típusokat a rendszer kinyeri a beállításjegyzékből. Ez a funkció egy jelző segítségével kapcsolható ki. Tekintse meg a jelző szakaszt.

> [!IMPORTANT]
> Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Példák

Töltsön fel egyetlen fájlt a OAuth-hitelesítés használatával.

Ha még nem jelentkezett be a AzCopy-be, használja `azcopy login` a parancsot a következő parancs futtatása előtt.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Ugyanaz, mint a fenti, de ezúttal is számításba kell vennie a fájl MD5-kivonatát, és mentenie kell a blob Content-MD5 tulajdonságának:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Egyetlen fájl feltöltése SAS-val:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS-t használó egyetlen fájl feltöltése a csővezetékek használatával (csak a Blobok blokkolása):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Teljes könyvtár feltöltése SAS-val:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

or

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Fájlok készletének feltöltése SAS használatával helyettesítő karakterekkel:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Fájlok és könyvtárak feltöltése SAS használatával helyettesítő karakterekkel:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Egyetlen fájl letöltése a OAuth-hitelesítés használatával.

Ha még nem jelentkezett be a AzCopy-be, használja `azcopy login` a parancsot a következő parancs futtatása előtt.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Egyetlen fájl letöltése SAS-vel:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Egyetlen fájl letöltése az SAS használatával a csővezetékeken (csak a Blobok blokkolása):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Teljes könyvtár letöltése SAS-val:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Fájlok készletének letöltése SAS használatával helyettesítő karakterekkel:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Fájlok és könyvtárak letöltése a SAS használatával helyettesítő karakterekkel:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Egyetlen blob másolása SAS-vel egy másik blobba SAS használatával:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egyetlen blobot másoljon SAS-vel egy másik blobba az OAuth token használatával.

Ha még nem jelentkezett be a AzCopy-be, használja `azcopy login` a parancsot a következő parancs futtatása előtt. A OAuth jogkivonat a cél Storage-fiók elérésére szolgál.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Teljes könyvtár másolása a blob virtuális könyvtárból SAS-vel egy másik blob virtuális könyvtárba SAS használatával:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Egy teljes fiók adatainak másolása a blob-fiókból az SAS-vel egy másik blob-fiókba SAS használatával:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Egyetlen objektum másolása az S3-ból hozzáférési kulccsal a blobba SAS használatával:

Állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az S3-forráshoz.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

A teljes címtár másolása az S3-ból hozzáférési kulccsal a blob virtuális könyvtárba az SAS használatával:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html További információ az S3 [mappa] eszközről:. Állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az S3-forráshoz.

Az S3 szolgáltatásban található összes gyűjtő másolása a hozzáférési kulccsal a blob-fiókba SAS használatával:

Állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az S3-forráshoz.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Az S3-régióban található összes gyűjtő másolása hozzáférési kulccsal a blob-fiókba SAS használatával:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az S3-forráshoz.

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|--BLOB típusú karakterlánc|Meghatározza a blob típusát a célhelyen. Ez a Blobok feltöltésére és a fiókok közötti másolásra használatos (alapértelmezés szerint "None").|
|--Block-blob-réteg sztringje|Blokkolja a blobokat az Azure Storage-ba a blob-rétegek használatával. (alapértelmezett "nincs").|
|--Block-Size-MB lebegőpontos |Használja ezt a blokk-méretet (a MiB-ben megadva) az Azure Storage-ba való feltöltéskor és az Azure Storage-ból való letöltéssel. Az alapértelmezett értéket a rendszer automatikusan kiszámítja a fájl mérete alapján. A tizedes törtek engedélyezettek (például: 0,25).|
|--Cache-Control sztring|Állítsa be a Cache-Control fejlécet. A letöltés után tért vissza.|
|--ellenőrzési-MD5 karakterlánc|Azt határozza meg, hogy a letöltéskor a szigorúan MD5-kivonatok legyenek érvényesítve. Csak a letöltéskor érhető el. Elérhető lehetőségek: Nincs vizsgálat, bejelentkezés, FailIfDifferent, FailIfDifferentOrMissing. (alapértelmezett "FailIfDifferent")|
|--Content-hajlam sztring|Állítsa be a Content-hajlam fejlécet. A letöltés után tért vissza.|
|--Content-Encoding sztring|Állítsa be a Content-Encoding fejlécet. A letöltés után tért vissza.|
|--Content-Language karakterlánc|A Content-Language fejléc beállítása A letöltés után tért vissza.|
|--Content-Type karakterlánc |A fájl tartalmának típusát adja meg. A nem-GUESS-MIME-típust jelenti. A letöltés után tért vissza.|
|– karakterlánc kizárása|Kizárja ezeket a fájlokat másoláskor. A * használatának támogatása.|
|--kizárás-blob-Type karakterlánc|Opcionálisan meghatározza a Blobok (BlockBlob/PageBlob/AppendBlob) típusát a tárolóból vagy a fiókból származó Blobok másolásakor. A jelző használata nem alkalmazható az adatok nem Azure-szolgáltatásból szolgáltatásba történő másolására. Több blobot ";" karakterrel kell elválasztani.|
|– követés – symlinks|A helyi fájlrendszerből való feltöltéskor kövesse a szimbolikus hivatkozásokat.|
|--a-ból sztring|Opcionálisan megadja a forrás céljának kombinációját. Példa: LocalBlob, BlobLocal, LocalBlobFS.|
|-h,-– Súgó|A másolási parancs súgójának tartalmát jeleníti meg. |
|--log szintű sztring|Adja meg a naplófájl részletességét, a rendelkezésre álló szinteket: INFO (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett "információ").|
|--Metadata karakterlánc|Töltse fel az Azure Storage-ba ezeket a kulcs-érték párokat metaadatokként.|
|--nem-GUESS-MIME-típus|Megakadályozza, hogy a AzCopy a fájl kiterjesztése vagy tartalma alapján észlelje a tartalom típusát.|
|– felülírás|Felülírja az ütköző fájlokat/blobokat a célhelyen, ha a jelző értéke TRUE (igaz). (alapértelmezés szerint igaz).|
|--Page-blob-réteg karakterlánca |Oldal blobjának feltöltése az Azure Storage-ba a blob-rétegek használatával. (alapértelmezett "nincs").|
|--megőrzés-utolsó módosítás időpontja|Csak akkor érhető el, ha a cél fájlrendszer.|
|--Put-MD5|hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob vagy fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Csak feltöltéskor érhető el.|
|– rekurzív|A helyi fájlrendszerből való feltöltéskor a rendszer rekurzív módon vizsgálja az alkönyvtárakat.|
|--S2S-Detect-Source-changed|Ellenőrizze, hogy a forrás módosult-e az enumerálás után. A S2S-példányok esetében a forrás egy távoli erőforrás, és annak ellenőrzése, hogy a forrás megváltozott-e, további kérési költségekkel kell rendelkeznie.|
|--S2S-Handle-érvénytelen – metaadat-karakterlánc |Megadja, hogy a rendszer hogyan kezelje az érvénytelen metaadat-kulcsokat. Elérhető lehetőségek: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (alapértelmezett "ExcludeIfInvalid").|
|--S2S-megőrzés – hozzáférési réteg|A hozzáférési szintek megőrzése a szolgáltatás és a szolgáltatás közötti másolás során. Tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési rétegeket](../blobs/storage-blob-storage-tiers.md) , hogy a cél Storage-fiók támogassa a hozzáférési szint beállítását. Abban az esetben, ha a hozzáférési szintet nem támogatja, használja a s2sPreserveAccessTier = false kapcsolót a hozzáférési szintek másolásának mellőzéséhez.  (alapértelmezés szerint igaz).|
|--S2S-megőrzése-Properties|A szolgáltatás és a szolgáltatás közötti másolás során megőrzi a teljes tulajdonságokat. Az S3 és az Azure file nem single file Source esetében, mivel a List művelet nem ad vissza teljes tulajdonságokat az objektumok és a fájlok számára, a teljes tulajdonságok megőrzése érdekében a AzCopy egy további kérést kell küldenie. (alapértelmezés szerint igaz).|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
