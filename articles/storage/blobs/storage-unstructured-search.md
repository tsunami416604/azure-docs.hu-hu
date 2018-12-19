---
title: 'Oktatóanyag: Az Azure Blob storage a strukturálatlan adatok keresése'
description: 'Oktatóanyag: Strukturálatlan adatok keresése az Azure search használata a Blob storage-ban.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 42c67d73ee776488fbe932676f61cb7166c2984b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599838"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Oktatóanyag: Strukturálatlan adatok keresése felhőalapú tárolóban

Ebben az oktatóanyagban elsajátíthatja, hogyan kereshet strukturálatlan adatokat használatával [Azure Search](../../search/search-what-is-azure-search.md), az Azure Blob storage-ban tárolt adatok használatával. A strukturálatlan adatok olyan adatok, amelyek nem előre definiáltan vannak rendezve, vagy nem rendelkeznek adatmodellel. Ilyen például, egy .txt fájlt.

Ebben az oktatóanyagban megköveteli, hogy Ön egy Azure-előfizetést. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot
> * Tárfiók létrehozása
> * Tároló létrehozása
> * Adatok feltöltése a tárolóba
> * Keresési szolgáltatás létrehozása a portálon
> * Egy keresési szolgáltatás csatlakoztatása tárfiókhoz
> * Adatforrás létrehozása
> * Az index konfigurálása
> * Hozzon létre egy indexelőt
> * A keresési szolgáltatás használata tároló keresésére

## <a name="prerequisites"></a>Előfeltételek

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. Amikor létrehoz egy tárfiókot, lehetősége van, vagy hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot. Ebben az oktatóanyagban létrehoz egy új erőforráscsoportot.

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

Egy minta adatkészlet elő van készítve az Ön számára, hogy akkor is győződjön meg arról, ebben az oktatóanyagban azt használja. Töltse le [klinikai trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) és bontsa ki azt a saját mappájába.

A minta a [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) webhelyről származó szöveges fájlokat tartalmaz. Ebben az oktatóanyagban használja őket, például szöveges fájlok, amelyek használatával az Azure Search-szolgáltatás a keresés történik.

## <a name="create-a-container"></a>Tároló létrehozása

A tárolók hasonlók a mappákhoz, és a blobok tárolására szolgálnak.

Ebben az oktatóanyagban egyetlen tárolót használunk a clinicaltrials.gov webhelyről származó szöveges fájlok tárolására.

1. Nyissa meg a storage-fiókot az Azure Portalon.

2. Válassza ki **blobok tallózása** alatt **Blob Service**.

3. Vegyen fel egy új tárolót.

4. A tároló neve **adatok** válassza **tároló** számára a nyilvános hozzáférés szintje.

5. A tároló létrehozásához válassza az **OK** gombot.

  ![Strukturálatlan keresés](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>A példaadatok feltöltése

Most, hogy már rendelkezik tárolóval, feltöltheti abba a példaadatokat.

1. Válassza ki a tárolót, majd válassza a **Feltöltés** elemet.

2. Mellett válassza a kék mappa ikont a **fájlok** mezőben, és keresse meg a helyi mappát, amelyikbe kibontotta a mintaadatokat.

3. Válassza ki az összes a kibontott fájlokat, és válassza ki **nyílt**.

4. Válassza a **Feltöltés** elemet a feltöltési folyamat megkezdéséhez.

  ![Strukturálatlan keresés](media/storage-unstructured-search/upload.png)

A feltöltési folyamat néhány percet is igénybe vehet.

Befejezését követően váltson vissza annak megerősítéséhez, szöveges fájlok feltöltése az adatok tárolóba.

  ![Strukturálatlan keresés](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Keresési szolgáltatás létrehozása

Az Azure Search keresési--szolgáltatásként felhőmegoldás, amely biztosítja a fejlesztők számára az API-k és eszközök hozzáadásának keresési funkciókat az adatok egy.

Ebben az oktatóanyagban a keresési szolgáltatás clinicaltrials.gov származó szöveges fájlok keresése használhatja.

1. Nyissa meg a storage-fiókot az Azure Portalon.

2. Görgessen lefelé, és válassza **Azure Search hozzáadása** alatt **BLOB SERVICE**.

3. Az **Adatok importálása** szakaszban használja a **Válasszon szolgáltatást** lehetőséget.

4. Válassza a **Kattintson ide egy új keresési szolgáltatás létrehozásához** lehetőséget.

5. Az **Új keresési szolgáltatás** szakaszban adjon meg egy egyedi nevet a keresési szolgáltatásnak az **URL** mezőben.

6. Alatt **erőforráscsoport**válassza **meglévő** , és válassza ki a korábban létrehozott erőforráscsoportot.

7. A **tarifacsomag**, jelölje be a **ingyenes** csomagot, és kattintson a **kiválasztása**.

8. A keresési szolgáltatás létrehozásához kattintson a **Létrehozás** elemre.

  ![Strukturálatlan keresés](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Keresési szolgáltatás csatlakoztatása a tárolóhoz

Most, hogy már rendelkezik keresési szolgáltatással, csatlakoztathatja azt a Blob Storage szolgáltatáshoz. Ez a szakasz végigvezeti az adatforrás kiválasztásának, az index létrehozásának és az indexelő létrehozásának folyamatán.

1. Lépjen a tárfiókhoz.

2. Válassza ki **hozzáadása az Azure Search** alatt **BLOB szolgáltatás**.

3. Válassza ki **keresőszolgáltatás** belül **adatok importálása**, majd kattintson a keresési szolgáltatás, amely az előző szakaszban létrehozott. Ez megnyitja **új adatforrás**.

### <a name="create-a-data-source"></a>Adatforrás létrehozása

  Az adatforrás az indexelendő adatokat és az adatok elérési módját határozza meg. Ugyanaz a keresési szolgáltatás többször is használhat egy adott adatforrást.

1. Adjon meg egy nevet az adatforrásnak. A **Kivonandó adatok** legördülő listából válassza ki a **Tartalom és metaadatok** lehetőséget. Az adatforrás a blob indexelendő részeit határozza meg.

2. Mivel a blobokat használ szöveges fájlok, állítsa be **elemzés mód** való **szöveg**.

  ![Strukturálatlan keresés](media/storage-unstructured-search/datasources.png)

3. A **Tároló** kiválasztásával listázhatja a rendelkezésre álló tárfiókokat.

4. Válassza ki a tárfiókját, és válassza ki a korábban létrehozott tároló.

  ![Strukturálatlan keresés](media/storage-unstructured-search/datacontainer.png)

5. Kattintson a **kiválasztása** való visszatéréshez **új adatforrás**, és válassza ki **OK** folytatásához.

### <a name="configure-the-index"></a>Az index konfigurálása

  Az index az adatforrásból származó kereshető mezők gyűjteménye. Állítsa be, és a paraméterek konfigurálása ezen mezők, így a keresési szolgáltatás felismeri, hogy milyen módon az adatokat kell keresni.

1. A **adatimportálás**válassza **célindex testreszabása**.

2. Írja be az index nevét az **Index neve** mezőbe.

3. Válassza ki a **lekérhető** attribútum jelölőnégyzet jelölését a **metadata_storage_name**.

  ![Strukturálatlan keresés](media/storage-unstructured-search/valuestoselect.png)

4. Válassza ki **OK**, amely kimenetei **hozzon létre egy indexelőt**.

Az index paraméterei és az ezekhez a paraméterekhez rendelt attribútumok fontosak. A paraméterek megadása *mi* adatok tárolására, és az attribútumok megadása *hogyan* tárolja az adatokat.

A **MEZŐ NEVE** oszlop tartalmazza a paramétereket. Az alábbi táblázat a rendelkezésre álló attribútumokat és a hozzájuk tartozó leírást listázza.

#### <a name="field-attributes"></a>Mezőattribútumok

| Attribútum | Leírás |
| --- | --- |
| *Kulcs* |Az egyes dokumentumok egyedi azonosítóját megadó sztring, amelyet a dokumentumok keresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| *Lekérhető* |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| *Szűrhető* |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| *Rendezhető* |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| *Értékkorlátozó* |Lehetővé teszi az adott mező értékkorlátozott navigációs szerkezetben történő használatát a felhasználó által önállóan irányított szűrések során. Általában együtt használhatja csoport dokumentumok ismétlődő értékeket tartalmazó mezők (például több dokumentumot, amelyek adott márkához vagy szolgáltatási kategóriához) működnek a legjobban, metszettel. |
| *Kereshető* |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |

### <a name="create-an-indexer"></a>Indexelő létrehozása

  Az indexelő az adatforrást kapcsolja össze a keresési indexszel, és ütemezést biztosít az adatok újraindexeléséhez.

1. Írjon be egy nevet a **Név** mezőbe, majd válassza az **OK** elemet.

  ![Strukturálatlan keresés](media/storage-unstructured-search/exindexer.png)

2. Hogy rendszer milyen közeli állapotba **adatok importálása**. Válassza ki **OK** a kapcsolódási folyamat befejezéséhez.

Most már sikeresen csatlakoztatta a blobot a keresési szolgáltatáshoz. Az index feltöltési állapotának megjelenítése néhány percet vesz igénybe a portálon. Azonban a keresési szolgáltatás azonnal megkezdi az indexelést, így Ön is azonnal megkezdheti a keresést.

## <a name="search-your-text-files"></a>Keresés a szöveges fájlokban

A fájlokban történő kereséshez nyissa meg az újonnan létrehozott keresési szolgáltatás indexének keresési ablakát.

Az alábbi lépések megmutatják a keresési ablak helyét, és tartalmaznak néhány példát a lekérdezésekre:

1. Nyissa meg az összes erőforrás, és keresse meg az újonnan létrehozott keresési szolgáltatás.

  ![Strukturálatlan keresés](media/storage-unstructured-search/exampleurl.png)

2. Válassza ki a megnyitásához az index.

  ![Strukturálatlan keresés](media/storage-unstructured-search/overview.png)

3. Válassza ki **keresési ablak** teszi lehetővé az élő lekérdezésekre az adatokon, a keresési ablak megnyitásához.

  ![Strukturálatlan keresés](media/storage-unstructured-search/indexespane.png)

4. Válassza a **Keresés** elemet úgy, hogy a lekérdezési karakterlánc mezője üres legyen. Az üres lekérdezés a blobok *összes* adatát adja vissza.

  ![Strukturálatlan keresés](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>A teljes szöveges keresés végrehajtása

Adja meg **Myopia** a a **lekérdezési karakterlánc** mezőt, és válassza **keresési**. Ebben a lépésben megkezdi a keresést, a fájlok tartalmát, és a egy részhalmazát adja vissza, amely tartalmazza a word "Myopia."

  ![Strukturálatlan keresés](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>A rendszer tulajdonságai keresés végrehajtása

A teljes szöveges keresés mellett létrehozhat lekérdezéseket, amelyek a keresés használatával Rendszertulajdonságok szerint a `$select` paraméter.

Adjon meg `$select=metadata_storage_name` a lekérdezési karakterláncot, majd nyomja le az **Enter**. Ez azt eredményezi, hogy csak adott mező való visszatéréshez.

A lekérdezési karakterlánc közvetlenül módosítja az URL-t, így a szóközök használata nem engedélyezett. Több mező kereséséhez használjon vesszőt, például: `$select=metadata_storage_name,metadata_storage_path`

A `$select` paraméter csak olyan mezőkkel használható, amelyek lekérdezhetőként lettek megjelölve az index definiálásakor.

  ![Strukturálatlan keresés](media/storage-unstructured-search/metadatasearchunstructured.png)

Ezennel befejezte ezt az oktatóanyagot, és most már rendelkezik a strukturálatlan adatok kereshető halmazával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A legegyszerűbben létrehozott erőforrások eltávolításához úgy, hogy törölje az erőforráscsoportot. Az erőforráscsoport törlésével a csoportban található összes erőforrást is törli. A következő példában az erőforráscsoport eltávolítása eltávolítja a tárfiókot és magát az erőforráscsoportot is.

1. Az Azure Portalon nyissa meg a azon erőforráscsoportok listája az előfizetésében.
2. Válassza ki a törölni kívánt erőforráscsoportot.
3. Válassza ki a **erőforráscsoport törlése** gombra, és adja meg az erőforráscsoport nevét a törlés mezőben.
4. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Tudjon meg többet az Azure Search dokumentumok indexelése erre a hivatkozásra:

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban az Azure Search használatával](../../search/search-howto-indexing-azure-blob-storage.md)
