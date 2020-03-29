---
title: azcopy másolata| Microsoft dokumentumok
description: Ez a cikk az azcopy copy parancs ra vonatkozó információkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 431372b930269c3dfa6bdc6e8b2fe4d291a8162e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933786"
---
# <a name="azcopy-copy"></a>azcopy copy

Forrásadatok másolása a célhelyre.

## <a name="synopsis"></a>Áttekintés

Forrásadatok másolása a célhelyre. A támogatott irányok a következők:

  - helyi < > Azure Blob (SAS vagy OAuth hitelesítés)
  - helyi < > Azure-fájlok (Megosztás/címtár SAS-hitelesítés)
  - helyi < > ADLS Gen 2 (SAS, OAuth vagy SharedKey hitelesítés)
  - Azure Blob (SAS vagy nyilvános) -> Azure Blob (SAS vagy OAuth hitelesítés)
  - Azure Blob (SAS vagy nyilvános) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Blob (SAS vagy OAuth hitelesítés)
  - AWS S3 (access key) –> Azure Block Blob (SAS vagy OAuth hitelesítés)

További információkért olvassa el a példákat.

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Speciális

AzAzCopy automatikusan felismeri a fájlok tartalomtípusát a helyi lemezről való feltöltéskor a fájlkiterjesztés vagy -tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresendő tábla kicsi, de unix esetén a helyi rendszer mime.types fájljai egészítik ki, ha egy vagy több ilyen név alatt elérhető:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszerben a MIME-típusok kibontása a beállításjegyzékből lesz kivonva. Ez a funkció egy zászló segítségével kikapcsolható. Kérjük, olvassa el a zászló részt.

Ha egy környezeti változót a parancssorból állít be, az a változó olvasható lesz a parancssori előzményekben. Fontolja meg a parancssori előzmények hitelesítő adatait tartalmazó változók törlését. Ha meg szeretné tartani, hogy a változók ne jelenjenek meg az előzményekben, parancsfájl segítségével kérheti a felhasználótól a hitelesítő adatait, és beállíthatja a környezeti változót.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Példák

Töltsön fel egyetlen fájlt OAuth-hitelesítéssel. Ha még nem jelentkezett be az AzCopy programba, a következő parancs futtatása előtt futtassa az azcopy bejelentkezési parancsot.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Ugyanaz, mint fent, de ezúttal is kiszámítja MD5 kivonat a fájl tartalmát, és mentse el a blob Content-MD5 tulajdonság:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

Töltsön fel egyetlen fájlt SAS-jogkivonat használatával:

- azcopy cp "/path/to/file.txt" "https://[account]blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Töltsön fel egyetlen fájlt SAS-jogkivonat és -csővezeték használatával (csak blokkblobok esetén):
  
- cat "/path/to/file.txt" | azcopy cp "https://[[account]blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Töltsön fel egy teljes könyvtárat SAS-jogkivonat használatával:
  
- azcopy cp "/path/to/dir" "https://[account]blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurzív=igaz

vagy

- azcopy cp "/path/to/dir" "https://[account]blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurzív=igaz --put-md5

Fájlok készletének feltöltése SAS-jogkivonat és helyettesítő karakterek (*) használatával:

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account]blob.core.windows.net/[container]/[path/to/directory]? [SAS]"

Fájlok és könyvtárak feltöltése SAS-jogkivonat és helyettesítő karakterek használatával:

- azcopy cp "/path/*foo/* bar*" "https://[account]blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurzív=igaz

Egyetlen fájl letöltése OAuth-hitelesítéssel. Ha még nem jelentkezett be az AzCopy programba, a következő parancs futtatása előtt futtassa az azcopy bejelentkezési parancsot.

- azcopy cp "https://[[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Egyetlen fájl letöltése SAS-jogkivonat használatával:

- azcopy cp "https://[[account]blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/path/to/file.txt"

Egyetlen fájl letöltése SAS-jogkivonat használatával, majd a kimenet fájlba való letiltása (csak a blokkblobok esetén):
  
- azcopy cp "https://[[account]blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/path/to/file.txt"

Töltsön le egy teljes könyvtárat Egy SAS-jogkivonat használatával:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "/path/to/dir" --rekurzív=true

Megjegyzés a helyettesítő karakter (*) URL-ekben való használatáról:

Az URL-címekben csak két támogatott módszer létezik a helyettesítő karakterek használatában. 

- Használhatja az egyik közvetlenül az URL végső perjel (/) után. Ez a könyvtárban lévő összes fájlt közvetlenül a célkönyvtárba másolja anélkül, hogy alkönyvtárba helyezne.

- Egy tároló nevében is használhat egyet, feltéve, hogy az URL-cím csak egy tárolóra vonatkozik, és nem egy blobra. Ezzel a módszerrel fájlokat szerezhet be a tárolók egy részhalmazából.

Töltse le a könyvtár tartalmát anélkül, hogy magához a könyvtárat másolna.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*? [SAS]" "/elérési út//dir"

Töltsön le egy teljes tárfiókot.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --rekurzív

Töltse le a tárolók egy tárfiókon belüli egy részhalmazát egy helyettesítő szimbólum (*) használatával a tároló nevében.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --rekurzív

Egyetlen blob másolása egy másik blob egy SAS-jogkivonat használatával.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Egyetlen blob másolása egy másik blob egy SAS-jogkivonat és egy OAuth-jogkivonat használatával. A forrásfiók URL-címének végén sas-jogkivonatot kell használnia, de a célfióknak nincs szüksége erre, ha az azcopy bejelentkezési paranccsal jelentkezik be az AzCopy-ba. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

Egy blob virtuális könyvtár másolása a másikra egy SAS-jogkivonat használatával:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "https://[destaccount]blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurzív=igaz

Másolja az összes blobtárolót, könyvtárat és blobot a tárfiókból egy másikba egy SAS-jogkivonat használatával:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --rekurzív=igaz

Egyetlen objektum másolása a Blob Storage-ba az Amazon Web Services (AWS) S3 szolgáltatásból egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a AWS_ACCESS_KEY_ID környezeti változót, és AWS_SECRET_ACCESS_KEY az AWS S3 forráshoz.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[object]" "https://[destaccount]blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Egy teljes könyvtár másolása a Blob Storage-ból AWS S3 egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a AWS_ACCESS_KEY_ID környezeti változót, és AWS_SECRET_ACCESS_KEY az AWS S3 forráshoz.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[folder]" "https://[destaccount]blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekurzív=igaz

A [mappa] helyőrzőjének jobb https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html megértéséhez olvassa el a [mappa] helyőrzőt.

Másolja az összes gyűjtőt a Blob Storage-ba az Amazon Web Services (AWS) egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a AWS_ACCESS_KEY_ID környezeti változót, és AWS_SECRET_ACCESS_KEY az AWS S3 forráshoz.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount]blob.core.windows.net? [SAS]" --rekurzív=igaz

Másolja az összes gyűjtőt a Blob Storage-ba egy Amazon Web Services (AWS) régióból egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a AWS_ACCESS_KEY_ID környezeti változót, és AWS_SECRET_ACCESS_KEY az AWS S3 forráshoz.

- azcopy cphttps://s3-" [region].amazonaws.com/" "https://[destaccount]blob.core.windows.net? [SAS]" --rekurzív=igaz

Gyűjtők egy részhalmazának másolása helyettesítő szimbólummal (*) a gyűjtő névben. Az előző példákhoz hasonlóan egy hozzáférési kulcsra és egy SAS-jogkivonatra is szüksége lesz. Ügyeljen arra, hogy az AWS S3 forráshoz AWS_ACCESS_KEY_ID és AWS_SECRET_ACCESS_KEY állítsa be.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount]blob.core.windows.net? [SAS]" --rekurzív=igaz

## <a name="options"></a>Beállítások

**--blob-típusú** karakterlánc Határozza meg a blob típusát a cél. Ez blobok feltöltésére és fiókok közötti másolásra szolgál (alapértelmezett "Detect"). Az érvényes értékek a következők: "Detect", "BlockBlob", "PageBlob" és "AppendBlob". A fiókok közötti másoláskor az "Észlelés" érték hatására az AzCopy a forrásblob típusát használja a célblob típusának meghatározásához. Fájl feltöltésekor a "Detect" határozza meg, hogy a fájl vhd vagy VHDX fájl-e a fájlkiterjesztés alapján. Ha a fájl ether egy VHD vagy VHDX fájl, az AzCopy a fájlt lapblobként kezeli. (alapértelmezett "Észlelés")

**--block-blob-tier** string Feltöltésblokk blobok közvetlenül a [hozzáférési szint](../blobs/storage-blob-storage-tiers.md) az Ön által kiválasztott. (alapértelmezett "Nincs"). Az érvényes értékek a következők: "Nincs", "Forró", "Hűvös" és "Archívum". Ha "Nincs" vagy nincs réteg kerül átadásra, a blob örökli a tárfiók rétegét.

**--block-size-mb** úszó: Használja ezt a blokkméretet (mib-ben megadva) az Azure Storage-ba való feltöltéséskor és az Azure Storage-ból való letöltéskor. Az alapértelmezett értéket a program automatikusan kiszámítja a fájlméret alapján. Tizedes törttörtek megengedettek (például: 0,25).

**--cache-control** string Állítsa be a cache-control fejlécet. Letöltéskor visszaküldve.

**--ellenőrzés hossza**                         Ellenőrizze a fájl hosszát a célállomáson az átvitel után. Ha eltérés van a forrás és a cél között, az átvitel sikertelenként lesz megjelölve. (alapértelmezett igaz)

**--check-md5** string Itt adhatja meg, hogy a szigorúan MD5-hash-okat hogyan kell érvényesíteni a letöltéskor. Csak letöltéskor érhető el. Elérhető lehetőségek: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (alapértelmezett "FailIfDifferent")

**--content-disposition** string Állítsa be a content-disposition fejlécet. Letöltéskor visszaküldve.

**--content-encoding** string Állítsa be a tartalomkódolási fejlécet. Letöltéskor visszaküldve.

**--content-language** string Állítsa be a tartalom-nyelvi fejlécet. Letöltéskor visszaküldve.

**--content-type** string A fájl tartalomtípusát adja meg. Azt jelenti, nem-találgatás-pantomim-típus. Letöltéskor visszaküldve.

**--kicsomagolás**                           A fájlok automatikus kibontása letöltéskor, ha azok tartalomkódolása azt jelzi, hogy tömörítettek. A támogatott tartalomkódolási értékek a "gzip" és a "deflate". A '.gz'/'.gzip' vagy a '.zz' fájlkiterjesztések nem szükségesek, de ha vannak ilyenek.

**--exclude-attributes** string (csak Windows) Kizárhatja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumlistával. Például: A; S; R

**--exclude-blob-type** string Optionally adja meg a blob típusát (BlockBlob/ PageBlob/ AppendBlob), hogy kizárja a blobok másolásakor a tárolóból vagy a fiókból. A jelző használata nem alkalmazható adatok másolása nem azure-szolgáltatás szolgáltatás. Egynél több blobot kell elválasztani a ";"

**--exclude-path** string Kizárni ezeket az elérési utakat másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagot(Például: myFolder;myFolder/subDirName/file.pdf). Ha a fiók sokszögelésével együtt használja, az elérési utak nem tartalmazzák a tároló nevét.

**--exclude-pattern** string Kizárhatja ezeket a fájlokat másolás közben. Ez a beállítás támogatja a helyettesítő karaktereket (*)

**---follow-symlinks**                      Kövesse a szimbolikus hivatkozásokat, amikor helyi fájlrendszerből töltődik fel.

**--from-to** string Opcionálisan megadja a forrás cél kombinációt. Például: LocalBlob, BlobLocal, LocalBlobFs.

**-h, --segítség** a másoláshoz

**--include-attributes** string (csak Windows) Olyan fájlokat is tartalmaz, amelyek attribútumai megfelelnek az attribútumlistának. Például: A; S; R

**--include-path** string Csak ezeket az elérési utakat tartalmazza másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagot (például: myFolder;myFolder/subDirName/file.pdf).

**--include-pattern** string Csak ezeket a fájlokat tartalmazza másoláskor. Ez a beállítás támogatja a helyettesítő karaktereket (*). A fájlokat a ";" használatával kell elválasztani.

**--log-level** string Adja meg a napló részletességét a naplófájlhoz, elérhető szintek: INFO(minden kérés/válasz), FIGYELEM(lassú válaszok), HIBA(csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett "INFO")

**--metaadat-karakterlánc** feltöltése az Azure Storage-ba ezekkel a kulcs-érték pármetaadatokkal.

**--no-guess-mime-típus**                   Megakadályozza, hogy az AzCopy észlelje a tartalomtípust a fájl kiterjesztése vagy tartalma alapján.

**--felülírja** a karakterláncot Felülírja az ütköző fájlokat és blobokat a célon, ha a jelző értéke igaz. A lehetséges értékek a következők: "true", "false", "ifSourceNewer" és "prompt". (alapértelmezett "igaz")

**--page-blob-tier** string Oldalblob feltöltése az Azure Storage-ba ezzel a blobréteghasználatával. (alapértelmezett "Nincs")

**--megőrzés-utolsó-módosított-idő**          Csak akkor érhető el, ha a cél fájlrendszer.

**--put-md5**                             Hozzon létre egy MD5-kivonatot minden fájlból, és mentse a kivonatot a célblob vagy -fájl Content-MD5 tulajdonságaként. (Alapértelmezés szerint a kivonat NEM jön létre.) Csak feltöltéskor érhető el.

**--rekurzív**                            Vizsgálja meg az alkönyvtárakre rekurzívan, amikor feltölti a helyi fájlrendszerből.

**--s2s-detect-source-megváltozott**           Ellenőrizze, hogy a forrás megváltozott-e a számbavétel után.

**--s2s-handle-invalid-metadata** karakterlánc: Az érvénytelen metaadatkulcsok kezelésének módját adja meg. Elérhető lehetőségek: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (alapértelmezett "ExcludeIfInvalid")

**--s2s-preserve-access-tier**             A hozzáférési szint megőrzése a szolgáltatás-szolgáltatás másolása során. Tekintse meg az [Azure Blob storage: gyakori elérésű és archív hozzáférési szintek](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) győződjön meg arról, céltárfiók támogatja a hozzáférési szint beállítása. Abban az esetben, ha a hozzáférési szint beállítása nem támogatott, használja s2sPreserveAccessTier=false a másolási hozzáférési szint megkerüléséhez. (alapértelmezett igaz)

**--s2s-megőrzési tulajdonságok**              A teljes tulajdonságok megőrzése a szolgáltatás másolása során. Az AWS S3 és az Azure File nem egyfájlos forrás, a lista művelet nem adja vissza az objektumok és fájlok teljes tulajdonságait. A teljes tulajdonságok megőrzése érdekében az AzCopy-nak objektumonként vagy fájlonként egy további kérelmet kell küldenie. (alapértelmezett igaz)

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

**--sapka-mbps uint32**      Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.

**--output-type** string A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg". (alapértelmezett "szöveg")

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
