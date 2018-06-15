---
title: Az Azure felhőalapú tárolást strukturálatlan adatok keresése
description: Az Azure search strukturálatlan adatok keresése.
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
ms.locfileid: "23930203"
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Strukturálatlan adatok keresése felhőalapú tárolóban

Ebben az oktatóanyagban elsajátíthatja, hogyan strukturálatlan adatok kereséséhez [Azure Search](../../search/search-what-is-azure-search.md) Azure-blobokat tárolt adatok használatával. Strukturálatlan adatok olyan adat, amelyet nem olyan előre definiált módon vannak rendezve, vagy nem rendelkezik olyan adatmodellt. Erre példa lehet egy .txt fájlt.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot
> * Create a storage account
> * Tároló létrehozása
> * Adatok feltöltése a tárolóba
> * Hozzon létre egy keresési szolgáltatást a portálon keresztül
> * A keresési szolgáltatását használja a tároló keresése

## <a name="download-the-sample"></a>A minta letöltése

Egy minta adatkészlet Önnek készült. **Töltse le [klinikai trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)**  és csomagolja ki azt a saját mappába.

A minta áll nyert szövegfájlok [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Használhatja például szövegfájlként Azure használatával kereséséhez.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure portálra](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Create a storage account

A storage-fiók egy egyedi helyet tárolhatja és érheti el az Azure Storage-adatobjektumok biztosít.

Jelenleg nincsenek storage-fiókok, két típusú **Blob** és **általános célú**. Ebben az oktatóanyagban létrehoz egy **általános célú** storage-fiók.

Ha nem ismeri a egy általános célú tárfiók létrehozásának folyamatán, itt, hogyan hozzon létre egyet:

1. A bal oldali menüben válassza ki a **Tárfiókok**, majd jelölje be **Hozzáadás**.

2. Adjon meg egy egyedi nevet a tárfiók. 

3. Válassza ki **erőforrás-kezelő** a a **telepítési modell** válassza **általános célú** a a **fiók kind** legördülő.

4. Válassza ki **helyileg redundáns tárolás (LRS)** a a **replikációs** legördülő listán.

5. A **erőforráscsoport**, jelölje be **hozzon létre új** és adjon meg egy egyedi nevet.

6. Válassza ki a megfelelő előfizetést.

7. Válassza ki azt a helyet, és válassza ki **létrehozása.**

  ![Strukturálatlan keresése](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Tároló létrehozása

Tárolók mappák hasonló, és a blobok tárolására szolgálnak.

Ebben az oktatóanyagban segítségével egyetlen tárolót clinicaltrials.gov származó szöveget fájlok tárolásához.

1. Nyissa meg a storage-fiókot az Azure-portálon.

2. Válassza ki **keresse meg a blobok** alatt **Blob szolgáltatás.**

3. Adjon hozzá egy új tárolót.

4. A tároló neve "data", és válassza ki **tároló** a nyilvános hozzáférés szint.

5. Válassza ki **OK** létrehozni a tárolót. 

  ![Strukturálatlan keresése](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>A példa adatok feltöltése

Most, hogy a tároló, a példában az adatait feltöltheti azt.

1. Válassza ki a tárolót, majd **feltöltése**.

2. Válassza ki a kék ikonja mellett a fájlok és a Tallózás gombra a helyi mappát, amelyikbe kibontotta a mintaadatok használatosak.

3. Válassza ki a kibontott fájlok mindegyikét, majd **megnyitása**

4. Válassza ki **feltöltése** a feltöltési folyamat megkezdéséhez.

  ![Strukturálatlan keresése](media/storage-unstructured-search/upload.png)

A feltöltési folyamat néhány percet is igénybe vehet.

Miután befejeződött, nyissa meg vissza az adatokat tároló annak ellenőrzéséhez, hogy a szöveg fájlok feltöltése.

  ![Strukturálatlan keresése](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Hozzon létre egy keresési szolgáltatást

Az Azure Search egy keresési,--szolgáltatás felhőalapú megoldás, amely lehetőséget nyújt a fejlesztők API-k, valamint eszközei hozzáadása egy hatékony keresési élményt biztosít az adatok a webes, mobil és vállalati alkalmazások.

Ha nem ismeri a keresési szolgáltatás létrehozásának folyamatán, itt, hogyan hozzon létre egyet:

1. Nyissa meg a storage-fiókot az Azure-portálon.

2. Görgessen le, majd kattintson a **hozzáadása Azure Search** alatt **BLOB szolgáltatás**.

3. A **és adatokat importálhat**, jelölje be **válassza ki a szolgáltatás**.

4. Válassza ki **Ide kattintva hozzon létre egy új keresési szolgáltatást**.

5. Belül **új keresőszolgáltatás** adjon egy egyedi nevet a keresési szolgáltatáshoz a a **URL-cím** mező.

6. A **erőforráscsoport**, jelölje be **meglévő** , és válassza a korábban létrehozott erőforráscsoportot.

7. Az a **tarifacsomag**, jelölje be a **szabad** réteg, majd kattintson a **válasszon**.

8. Válassza ki **létrehozása** létrehozni a keresési szolgáltatást.

  ![Strukturálatlan keresése](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>A keresési szolgáltatáshoz kapcsolódni a tárolóhoz

Most, hogy a keresési szolgáltatást, csatolhat a blob-tároló. Ez a szakasz bemutatja, hogyan adatforrás kiválasztása, az index létrehozása és az indexelő létrehozásának folyamata.

1. Lépjen a tárfiókhoz.

2. Válassza ki **adja hozzá az Azure Search** alatt **BLOB szolgáltatás.**

3. Válassza ki **keresési szolgáltatás** belül **és adatokat importálhat** , majd a keresési szolgáltatás az előző szakaszban létrehozott. Ezzel megnyílik **új adatforrás**.

### <a name="new-data-source"></a>Új adatforrás hozzáadása

  Adatforrás határozza meg, amelyhez az index és az adatok elérése. Egy adatforrás használhatja többször ugyanazt a keresési szolgáltatást.

1. Adja meg az adatforrás nevét. A **adatok kibontásához**, jelölje be **tartalom és metaadatok**. Az adatforrás határozza meg, mely részei a blob indexelt.
    
    a. Saját jövőbeli forgatókönyvek közül is választhat **csak a tároló metaadatainak**. A kijelölés lenne, ha az adatok szabványos blob tulajdonságok vagy tulajdonságok felhasználó által definiált indexelt korlátozni szeretné.
    
    b. Dönthet úgy is **minden** mindkét szabványos blob tulajdonságok beszerzésére és *összes* tartalomtípus adott metaadatokat. 

2. Mivel a blobs használata szövegfájlok, állítsa be **elemzése mód** való **szöveg**.
    
    a. A saját jövőbeli esetben Kezdésként kiválasztása [más elemzési módok](../../search/search-howto-indexing-azure-blob-storage.md) attól függően, hogy a BLOB tartalmát.

  ![Strukturálatlan keresése](media/storage-unstructured-search/datasources.png)

3. Válassza ki **tároló** a rendelkezésre álló tárfiókok listázásához.

4. Válassza ki a tárfiókot, majd válassza ki a korábban létrehozott tároló.

5. Kattintson a **válassza** való visszatéréshez **új adatforrás** válassza **OK** folytatja.

  ![Strukturálatlan keresése](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Az index konfigurálása

  Az index az adatforrásból kereshető mezők gyűjteménye. Az index az, hogy hogyan a keresőszolgáltatása tudja, hogyan lehet keresni az adatok.

1. A **adatimportálás** válasszon **testreszabás cél index**.
 
2. Adjon meg egy nevet az index a **indexnév** mező.

3. Válassza ki a **lekérhető** attribútum jelölőnégyzet alatti **metadata_storage_name**.

  ![Strukturálatlan keresése](media/storage-unstructured-search/valuestoselect.png)

4. Kattintson a **OK**, amely megjelenik **hozzon létre egy indexelőt**.

Az index és ezeket a paramétereket ad az attribútumok paraméterei fontos. A paraméterek meg *mi* adatok tárolására, adja meg, az attribútumok *hogyan* adatok tárolására.

A **mezőnév** oszlop a paramétereket tartalmaz. A következő táblázat felsorolja a rendelkezésre álló attribútumok és azok leírásait tartalmazza.

### <a name="field-attributes"></a>Mezőattribútumok
| Attribútum | Leírás |
| --- | --- |
| *Kulcs* |Dokumentum keresési használt egyes dokumentumok egyedi Azonosítóját megadó karakterlánc. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| *Lekérhető* |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| *Szűrhető* |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| *Rendezhető* |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| *Értékkorlátozó* |Lehetővé teszi, hogy egy felhasználó irányuló szűréshez jellemzőalapú navigációs szerkezetben használandó mezőt. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében). |
| *Kereshető* |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |


### <a name="create-an-indexer"></a>Hozzon létre egy indexelőt
    
  Az indexelő adatforráshoz kapcsolódik egy keresési indexszel, és biztosítja az adatok újraindexelése ütemezésével.

1. Írjon be egy nevet a **neve** mezőben, majd válassza ki **OK**.

  ![Strukturálatlan keresése](media/storage-unstructured-search/exindexer.png)

2. Hivatkozásra vissza **és adatokat importálhat**, jelölje be **OK** a csatlakozási folyamat befejezéséhez.

Most már sikeresen csatlakozott a blob a keresési szolgáltatáshoz. A portálon megjelenítendő van feltöltve, hogy az index néhány percet vesz igénybe. Azonban a keresési szolgáltatás megkezdi az indexelő azonnali elkezdéséhez azonnal keresése.

## <a name="search-your-text-files"></a>Keresés a szövegfájlok

A fájlok megkereséséhez nyissa meg a keresési ablak belül az index az újonnan létrehozott search szolgáltatás.

A következő lépések bemutatják, hol található a keresési ablak, és mintalekérdezéseket biztosít:

1. Keresse meg az összes erőforráshoz, és az újonnan létrehozott search szolgáltatás található.

  ![Strukturálatlan keresése](media/storage-unstructured-search/exampleurl.png)

3. Válassza ki az index a megnyitáshoz. 

  ![Strukturálatlan keresése](media/storage-unstructured-search/overview.png)

4. Válassza ki **keresési ablak** kattintva nyissa meg a keresési ablak teszi lehetővé az élő lekérdezések az adatokon.

  ![Strukturálatlan keresése](media/storage-unstructured-search/indexespane.png)

5. Válassza ki **keresési** lekérdezési karakterlánc mezőneve üres állapotában. Egy üres lekérdezés visszaadja az *összes* blobok adatait.

  ![Strukturálatlan keresése](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Teljes szöveges keresés 

A "Myopia" adja meg a **lekérdezési karakterlánc** mezőben, majd válassza ki **keresési**. A Keresés a fájlok tartalmának kezdeményezése törlését és azok egy részét, amely tartalmazza a word "Myopia."

  ![Strukturálatlan keresése](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Rendszer tulajdonságok keresési

Lekérdezéseiben, amelyeket a rendszer tulajdonságok használatával kereshet is létrehozhat a `$select` paraméter. Adja meg `$select=metadata_storage_name` a lekérdezési karakterláncot, és nyomja le az adja meg, csak adott mező ad vissza.
    
A lekérdezési karakterlánc közvetlenül módosítja az URL-cím, így szóközök nem használhatók. Keresés több mező, használjon vesszőt, például:`$select=metadata_storage_name,metadata_storage_path`
    
A `$select` paraméter csak akkor használható az index definiálásakor megjelölt lekérhető mezőkkel.

  ![Strukturálatlan keresése](media/storage-unstructured-search/metadatasearchunstructured.png) 

Ez az oktatóanyag teljesített és kereshető strukturálatlan adatok rendelkeznek.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte az Azure Search, például hogyan strukturálatlan adatok keresése:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot
> * Create a storage account
> * Tároló létrehozása
> * Adatok feltöltése a tárolóba
> * Hozzon létre egy keresési szolgáltatást
> * A keresési szolgáltatás használatakor a tároló keresése

Kattintson ide további információt az Azure Search dokumentumok indexelő.

> [!div class="nextstepaction"]
> [Az Azure Blob Storage tárolóban az Azure Search dokumentumok indexelő](../../search/search-howto-indexing-azure-blob-storage.md)