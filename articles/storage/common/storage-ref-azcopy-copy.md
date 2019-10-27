---
title: azcopy másolás | Microsoft Docs
description: Ez a cikk a azcopy másolási parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 78482b5d7013ffa3bbb0a34dd04c8c48626dc77a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926655"
---
# <a name="azcopy-copy"></a>azcopy copy

A forrásadatok másolása a célhelyre.

## <a name="synopsis"></a>Áttekintés

A forrásadatok másolása a célhelyre. A támogatott utasítások a következők:

  - helyi < – > Azure Blob (SAS vagy OAuth Authentication)
  - helyi <-> Azure Files (megosztás/könyvtár SAS-hitelesítése)
  - helyi < – > ADLS Gen 2 (SAS, OAuth vagy SharedKey Authentication)
  - Azure Blob (SAS vagy Public) – > Azure Blob (SAS vagy OAuth Authentication)
  - Azure Blob (SAS vagy Public) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure-Blob (SAS vagy OAuth-hitelesítés)
  - AWS S3 (hozzáférési kulcs) – > Azure Block blob (SAS vagy OAuth Authentication)

További információért tekintse meg a példákat.

## <a name="advanced"></a>Extra szintű

A AzCopy automatikusan észleli a fájlok tartalomtípusát a helyi lemezről történő feltöltéskor a fájlkiterjesztés vagy a tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresési táblázat kicsi, de UNIX rendszeren a helyi rendszer MIME. types fájl (ok), ha az alábbi nevek közül egy vagy több található:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszeren a MIME-típusokat a rendszer kinyeri a beállításjegyzékből. Ez a funkció egy jelző segítségével kapcsolható ki. Tekintse meg a jelző szakaszt.

Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Példák

Töltsön fel egyetlen fájlt a OAuth-hitelesítés használatával. Ha még nem jelentkezett be a AzCopy-ba, futtassa a AzCopy login parancsot a következő parancs futtatása előtt.

- azcopy CP "/Path/to/file.txt" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]"

Ugyanaz, mint a fenti, de ezúttal is számításba kell vennie a fájl MD5-kivonatát, és mentenie kell a blob Content-MD5 tulajdonságának:

- azcopy CP "/Path/to/file.txt" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési út/a blob]"--Put-MD5

Egyetlen fájl feltöltése SAS-token használatával:

- azcopy CP "/Path/to/file.txt" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] "

Egyetlen fájl feltöltése SAS-token és-csővezeték használatával (csak a Blobok blokkolása):
  
- Cat "/Path/to/file.txt" | azcopy CP "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] "

Teljes könyvtár feltöltése SAS-token használatával:
  
- azcopy CP "/Path/to/dir" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "--rekurzív = True

vagy

- azcopy CP "/Path/to/dir" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "--rekurzív = True--Put-MD5

Fájlok készletének feltöltése SAS-token és helyettesítő karakter (*) karakterek használatával:

- azcopy CP "/Path/*foo/* Bar/*. pdf" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "

Fájlok és könyvtárak feltöltése SAS-token és helyettesítő karakter (*) karakterek használatával:

- azcopy CP "/Path/*foo/* Bar *" "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "--rekurzív = True

Töltsön le egyetlen fájlt a OAuth-hitelesítés használatával. Ha még nem jelentkezett be a AzCopy-ba, futtassa a AzCopy login parancsot a következő parancs futtatása előtt.

- azcopy CP "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]" "/Path/to/file.txt"

Egyetlen fájl letöltése SAS-token használatával:

- azcopy CP "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] ""/Path/to/file.txt "

Egyetlen fájl letöltése SAS-token használatával, majd a kimenet fájlba állítása (csak a Blobok blokkolása esetén):
  
- azcopy CP "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] ">"/Path/to/file.txt "

Teljes könyvtár letöltése SAS-token használatával:
  
- azcopy CP "https://[fiók]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] ""/Path/to/dir "--rekurzív = True

Megjegyzés a helyettesítő karakter (*) URL-címekben való használatáról:

Egy URL-címben csak két támogatott módon használható helyettesítő karakter. 

- Egy URL-cím utolsó perjel (/) utáni végét is használhat. Ez egy címtárban lévő összes fájlt átmásolja közvetlenül a célhelyre anélkül, hogy egy alkönyvtárba helyezzük őket.

- A tároló nevében is használhat egyet, ha az URL-cím csak egy tárolóra hivatkozik, és nem a blobra. Ezt a módszert használhatja a tárolók egy részhalmazában lévő fájlok beszerzésére.

Töltse le a könyvtár tartalmát anélkül, hogy magáról a tartalmazó könyvtárat másolja.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[elérési út/mappa]/*? [SAS] ""/Path/to/dir "

Töltse le a teljes Storage-fiókot.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/" "/Path/to/dir"--rekurzív

A Storage-fiókban lévő tárolók egy részhalmazát letöltheti egy helyettesítő karakter (*) használatával a tároló nevében.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[tároló * neve]" "/Path/to/dir"--rekurzív

Egyetlen blob másolása egy másik blobba SAS-token használatával.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] "

Egyetlen blob másolása egy másik blobba SAS-jogkivonat és egy OAuth-jogkivonat használatával. A forrásoldali fiók URL-címének végén egy SAS-tokent kell használnia, de a AzCopy a AzCopy login paranccsal kell bejelentkeznie. 

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[elérési_út/to/blob]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[elérési út/blob] "

Egy blob virtuális könyvtár másolása egy másikba SAS-token használatával:

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "--rekurzív = True

Az összes blob-tároló,-könyvtár és-blob másolása a Storage-fiókból egy másikba SAS-token használatával:

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net? [SAS] "" https://[destaccount]. blob. Core. Windows. net? [SAS] "--rekurzív = True

Egyetlen objektum másolása Amazon Web Services (AWS) S3-Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az AWS S3-forráshoz.
  
- azcopy CP "https://s3.amazonaws.com/ [gyűjtő]/[objektum]" "https://[destaccount]. blob. Core. Windows. net/[Container]/[elérési út/to/blob]? [SAS] "

Egy teljes könyvtár másolása az AWS S3-Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az AWS S3-forráshoz.

- azcopy CP "https://s3.amazonaws.com/ [Bucket]/[mappa]" "https://[destaccount]. blob. Core. Windows. net/[Container]/[elérési út/könyvtár]? [SAS] "--rekurzív = True

A [mappa] helyőrző jobb megismeréséhez tekintse meg a https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html.

Másolja az összes gyűjtőt a Amazon Web Services (AWS) Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az AWS S3-forráshoz.

- azcopy CP "https://s3.amazonaws.com/ " "https://[destaccount]. blob. Core. Windows. net? [SAS] "--rekurzív = True

Másolja az összes gyűjtőt egy Amazon Web Services (AWS) régióból Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót az AWS S3-forráshoz.

- azcopy CP "https://s3- [régió]. Amazonaws. com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--rekurzív = True

Másolja a gyűjtők egy részhalmazát egy helyettesítő karakter (*) szimbólum használatával a gyűjtő nevében. Az előző példákhoz hasonlóan egy hozzáférési kulcsra és egy SAS-jogkivonatra is szüksége lesz. Ügyeljen arra, hogy a AWS_ACCESS_KEY_ID és a AWS_SECRET_ACCESS_KEY környezeti változót AWS S3-forrásként adja meg.

- azcopy CP "https://s3.amazonaws.com/ [Bucket * name]/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--rekurzív = True

## <a name="options"></a>Beállítások

**--a blob-Type** karakterlánc határozza meg a blob típusát a célhelyen. Ez a Blobok feltöltésére és a fiókok közötti másolásra használatos (az alapértelmezett "észlelés"). Az érvényes értékek közé tartozik az "észlelés", a "BlockBlob", a "PageBlob" és a "AppendBlob". A fiókok közötti másoláskor az "észlelés" érték azt eredményezi, hogy a AzCopy a forrás blob típusát használja a cél blob típusának meghatározásához. Egy fájl feltöltésekor az "észlelés" meghatározza, hogy a fájl egy VHD vagy egy VHDX-fájl a fájlkiterjesztés alapján. Ha a fájl az éter VHD-vagy VHDX-fájlja, a AzCopy oldal blobként kezeli a fájlt. (alapértelmezett "észlelés")

**--Block-blob-réteg** karakterlánc feltöltési blokk blob az Azure Storage-ba ezzel a blob réteggel. (alapértelmezett "nincs")

**--Block-Size-MB** lebegőpontos használata esetén ez a blokk mérete (a MIB-ben van megadva) az Azure Storage-ba való feltöltéskor és az Azure Storage-ból való letöltéssel. Az alapértelmezett értéket a rendszer automatikusan kiszámítja a fájl mérete alapján. Tizedes törtek engedélyezettek (például: 0,25).

**--Cache-Control** sztring a Cache-Control fejlécet adja meg. A letöltés után tért vissza.

**--ellenőrzési hossz**                         Az átvitel után a célhelyen lévő fájl hosszának ellenőrzését. Ha eltérés van a forrás és a cél között, az átvitel sikertelenként van megjelölve. (alapértelmezett true)

**--ellenőrzés-MD5** karakterlánc megadja, hogy a letöltéskor milyen szigorúan kell ellenőrizni az MD5-kivonatok érvényességét. Csak a letöltéskor érhető el. Elérhető lehetőségek: nincs vizsgálat, bejelentkezés, FailIfDifferent, FailIfDifferentOrMissing. (alapértelmezett "FailIfDifferent")

**--Content-hajlam** sztring beállítja a Content-hajlam fejlécet. A letöltés után tért vissza.

**--Content-Encoding** string beállítja a Content-Encoding fejlécet. A letöltés után tért vissza.

**--Content-Language** karakterlánc a Content-Language fejlécet adja meg. A letöltés után tért vissza.

**--Content-Type** karakterlánc megadja a fájl tartalomtípusát. A nem-GUESS-MIME-típust jelenti. A letöltés után tért vissza.

**– Kibontás**                           Fájlok automatikus kibontása letöltéskor, ha a tartalmuk kódolása azt jelzi, hogy tömörítve vannak. A támogatott Content-Encoding érték a "gzip" és a "deflate". A ". gz"/". gzip" vagy a ". ZZ" fájlkiterjesztések nem szükségesek, de ha van, el lesznek távolítva.

**– kizárás – attribútumok** karakterlánca (csak Windows) kizárhatja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumok listájával. Például: A; S R

**--kizárás-blob-Type** karakterlánc opcionálisan megadja a blob (BlockBlob/PageBlob/AppendBlob) típusát, amelyet a rendszer a tárolóból vagy a fiókból származó Blobok másolásakor figyelmen kívül hagy. A jelző használata nem alkalmazható az adatok nem Azure-szolgáltatásból szolgáltatásba történő másolására. Több blobot ";" karakterrel kell elválasztani.

**--kizárás – az elérési út** karakterlánca kizárja ezeket az elérési utakat másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: myFolder; myFolder/subDirName/file. pdf). Ha a fiókhoz való bejárással együtt használja, az elérési utak nem tartalmazzák a tároló nevét.

**--kizárás-Pattern** sztring kizárja ezeket a fájlokat másoláskor. Ez a beállítás támogatja a helyettesítő karaktereket (*)

**– követés – symlinks**                      A helyi fájlrendszerből való feltöltéskor kövesse a szimbolikus hivatkozásokat.

**--from-to** karakterlánc opcionálisan megadja a forrás céljának kombinációját. Például: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--Súgó** a másoláshoz

**--include-attributes** sztring (csak Windows) olyan fájlokat tartalmaz, amelyek attribútumai megegyeznek az attribútumok listájával. Például: A; S R

a- **-include-Path** sztring csak a másoláskor tartalmazza ezeket az elérési utakat. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: myFolder; myFolder/subDirName/file. pdf).

**--include-Pattern** sztring csak a másoláskor tartalmazza ezeket a fájlokat. Ez a beállítás támogatja a helyettesítő karaktereket (*). A fájlokat a ";" használatával válassza el.

**--a log szintű** karakterlánc határozza meg a naplófájl részletességét, a rendelkezésre álló szinteket: info (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérések), és nincs (nincs kimeneti napló). (alapértelmezett "információ")

**--a metaadatok** karakterláncának feltöltése az Azure Storage-ba ezekkel a kulcs-érték párokkal.

**--nem-GUESS-MIME-típus**                   Megakadályozza, hogy a AzCopy a fájl kiterjesztése vagy tartalma alapján észlelje a tartalom típusát.

**– a karakterlánc felülírása** felülírja az ütköző fájlokat és blobokat a célhelyen, ha a jelző értéke TRUE (igaz). A lehetséges értékek a következők: "true", "false" és "prompt". (alapértelmezett érték: "true")

**--Page-blob-rétegbeli** karakterlánc feltöltése oldal blob az Azure Storage-ba ezen blob-réteg használatával. (alapértelmezett "nincs")

**--megőrzés-utolsó módosítás időpontja**          Csak akkor érhető el, ha a cél fájlrendszer.

**--put-MD5**                             Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob vagy fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Csak feltöltéskor érhető el.

**– rekurzív**                            A helyi fájlrendszerből való feltöltéskor a rendszer rekurzív módon vizsgálja az alkönyvtárakat.

**--S2S-Detect-Source-changed**           Ellenőrizze, hogy a forrás módosult-e az enumerálás után.

**--S2S-Handle-érvénytelen – a metaadatok** karakterlánca megadja, hogy a rendszer hogyan kezelje az érvénytelen metaadat-kulcsokat. Elérhető lehetőségek: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (alapértelmezett "ExcludeIfInvalid")

**--S2S-megőrzés – hozzáférési réteg**             A hozzáférési szintek megőrzése a szolgáltatás és a szolgáltatás közötti másolás során. Tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési rétegeket](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) , hogy a cél Storage-fiók támogassa a hozzáférési szint beállítását. Abban az esetben, ha a hozzáférési szintet nem támogatja, használja a s2sPreserveAccessTier = false kapcsolót a hozzáférési szintek másolásának mellőzéséhez. (alapértelmezett true)

**--S2S-megőrzése-Properties**              A szolgáltatás és a szolgáltatás közötti másolás során megőrzi a teljes tulajdonságokat. Az AWS S3 és az Azure file nem single file Source esetében a List művelet nem ad vissza objektumok és fájlok teljes tulajdonságait. A teljes tulajdonságok megőrzése érdekében a AzCopy egy további kérelmet kell küldenie egy objektumra vagy fájlra vonatkozóan. (alapértelmezett true)

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

**--Cap-Mbps UInt32**      Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**--** a parancs kimenetének kimeneti típusú karakterlánc-formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text")

## <a name="see-also"></a>Lásd még:

- [azcopy](storage-ref-azcopy.md)
