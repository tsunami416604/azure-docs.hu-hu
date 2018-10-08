---
title: Strukturálatlan adatok keresése az Azure felhőalapú tárolójában
description: Strukturálatlan adatok keresése az Azure Search használatával.
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 902009d7807b1ce340000c271350af1c37231d77
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181192"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Oktatóanyag: Strukturálatlan adatok keresése felhőalapú tárolóban

Ebben az oktatóanyagban megismerheti, hogy miként lehet a strukturálatlan adatokban keresni az [Azure Search](../../search/search-what-is-azure-search.md) és az Azure-blobokban tárolt adatok használatával. A strukturálatlan adatok olyan adatok, amelyek nem előre definiáltan vannak rendezve, vagy nem rendelkeznek adatmodellel. Jó példa erre egy szöveges (.txt) fájl.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot
> * Tárfiók létrehozása
> * Tároló létrehozása
> * Adatok feltöltése a tárolóba
> * Keresési szolgáltatás létrehozása a portálon
> * A keresési szolgáltatás használata tároló keresésére

## <a name="download-the-sample"></a>A minta letöltése

Előkészítettünk Önnek egy mintaadatkészletet. **Töltse le a [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) fájlt**, és bontsa ki a saját mappájába.

A minta a [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) webhelyről származó szöveges fájlokat tartalmaz. Használhatja őket szöveges példafájlokként az Azure-ral történő kereséskor.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A tárfiók egy egyedi helyet biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez.

Jelenleg kétféle típusú tárfiók létezik: **blob** és **általános célú**. Ebben az oktatóanyagban egy **általános célú** tárfiókot hozunk létre.

Ha nem ismeri az általános célú tárfiókok létrehozásának folyamatát, az alábbiakban ismertetjük azt:

1. A bal oldali menüben válassza a **Tárfiókok**, majd a **Hozzáadás** lehetőséget.

2. Adja meg a tárfiók egyedi nevét. 

3. Válassza ki az **Üzemi modell** lehetőséget a **Resource Manager** szakaszban, majd válassza az **Általános célú** lehetőséget a **Fióktípus** legördülő listájában.

4. A **Replikáció** legördülő listából válassza a **Helyileg redundáns tárolás (LRS)** lehetőséget.

5. Az **Erőforráscsoport** menüben kattintson az **Új létrehozása** elemre, és adjon meg egy egyedi nevet.

6. Válassza ki a megfelelő előfizetést.

7. Válasszon egy helyet, majd válassza a **Létrehozás** elemet.

  ![Strukturálatlan keresés](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Tároló létrehozása

A tárolók hasonlók a mappákhoz, és a blobok tárolására szolgálnak.

Ebben az oktatóanyagban egyetlen tárolót használunk a clinicaltrials.gov webhelyről származó szöveges fájlok tárolására.

1. Az Azure Portalon nyissa meg a tárfiókot.

2. A **Blob Service** szakaszban válassza a **Blobok tallózása** lehetőséget.

3. Vegyen fel egy új tárolót.

4. Adjon meg egy nevet a tárolónak („adatok”), és a nyilvános hozzáférés szintjénél válassza a **Tároló** lehetőséget.

5. A tároló létrehozásához válassza az **OK** gombot. 

  ![Strukturálatlan keresés](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>A példaadatok feltöltése

Most, hogy már rendelkezik tárolóval, feltöltheti abba a példaadatokat.

1. Válassza ki a tárolót, majd válassza a **Feltöltés** elemet.

2. Válassza ki a Fájlok melletti kék mappaikont, és tallózással nyissa meg azt a helyi mappát, amelyikbe kibontotta a mintaadatokat.

3. Jelölje ki az összes kibontott fájlt, és válassza a **Megnyitás** elemet

4. Válassza a **Feltöltés** elemet a feltöltési folyamat megkezdéséhez.

  ![Strukturálatlan keresés](media/storage-unstructured-search/upload.png)

A feltöltési folyamat eltarthat egy kis ideig.

Amint a folyamat befejeződik, lépjen vissza az adattárolóba, és ellenőrizze, hogy megtörtént-e a szöveges fájlok feltöltése.

  ![Strukturálatlan keresés](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Keresési szolgáltatás létrehozása

Az Azure Search egy felhőalapú keresőszolgáltatás, amely olyan API-kat és eszközöket nyújt a fejlesztőknek, amelyek segítségével fejlett adatkeresési funkciók adhatók hozzá a webalkalmazásokhoz, a mobilalkalmazásokhoz és a vállalati alkalmazásokhoz.

Ha nem ismeri a keresési szolgáltatások létrehozásának folyamatát, az alábbiakban ismertetjük azt:

1. Az Azure Portalon nyissa meg a tárfiókot.

2. Görgessen lefelé, és kattintson **Az Azure Search felvétele** elemre a **BLOB SERVICE** szakaszban.

3. Az **Adatok importálása** szakaszban használja a **Válasszon szolgáltatást** lehetőséget.

4. Válassza a **Kattintson ide egy új keresési szolgáltatás létrehozásához** lehetőséget.

5. Az **Új keresési szolgáltatás** szakaszban adjon meg egy egyedi nevet a keresési szolgáltatásnak az **URL** mezőben.

6. Az **Erőforráscsoport** listában válassza a **Meglévő használata** elemet, és válassza ki a korábban létrehozott erőforráscsoportot.

7. A **Tarifacsomag** szakaszban válassza az **Ingyenes** csomagot, és kattintson a **Kiválasztás** elemre.

8. A keresési szolgáltatás létrehozásához kattintson a **Létrehozás** elemre.

  ![Strukturálatlan keresés](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Keresési szolgáltatás csatlakoztatása a tárolóhoz

Most, hogy már rendelkezik keresési szolgáltatással, csatlakoztathatja azt a Blob Storage szolgáltatáshoz. Ez a szakasz végigvezeti az adatforrás kiválasztásának, az index létrehozásának és az indexelő létrehozásának folyamatán.

1. Nyissa meg a tárfiókot.

2. Válassza **Az Azure Search felvétele** elemet a **BLOB SERVICE** szakaszban.

3. Válassza a **Keresési szolgáltatás** elemet az **Adatok importálása** lapon, és kattintson az előző szakaszban létrehozott keresési szolgáltatásra. Megnyílik az **Új adatforrás** lap.

### <a name="new-data-source"></a>Új adatforrás

  Az adatforrás az indexelendő adatokat és az adatok elérési módját határozza meg. Ugyanaz a keresési szolgáltatás többször is használhat egy adott adatforrást.

1. Adjon meg egy nevet az adatforrásnak. A **Kivonandó adatok** legördülő listából válassza ki a **Tartalom és metaadatok** lehetőséget. Az adatforrás a blob indexelendő részeit határozza meg.
    
    a. Saját jövőbeli forgatókönyvek esetén a **Csak tárolási metaadatok** lehetőséget is kiválaszthatja. Akkor válassza ezt, ha a blob szokásos tulajdonságaira vagy felhasználó által definiált tulajdonságokra szeretné korlátozni az indexelt adatok körét.
    
    b. Választhatja az **Összes metaadat** lehetőséget mindkét szokásos blobtulajdonság és az *összes* tartalomtípusú, konkrét metaadat lekéréséhez. 

2. Mivel a használandó blobok szöveges fájlok, állítsa az **Elemzési mód** beállítást **Szöveg** értékre.
    
    a. Saját jövőbeli forgatókönyvek esetén [más elemzési módot](../../search/search-howto-indexing-azure-blob-storage.md) is kiválaszthat a blobok tartalmától függően.

  ![Strukturálatlan keresés](media/storage-unstructured-search/datasources.png)

3. A **Tároló** kiválasztásával listázhatja a rendelkezésre álló tárfiókokat.

4. Válassza ki a tárfiókot, majd válassza ki a korábban létrehozott tárolót.

5. A **Kiválasztás** elemre kattintva visszatérhet az **Új adatforrás** lapra, majd válassza az **OK** gombot folytatáshoz.

  ![Strukturálatlan keresés](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Az index konfigurálása

  Az index az adatforrásból származó kereshető mezők gyűjteménye. Az index segítségével tudja a keresési szolgáltatás megállapítani, hogy az adatok milyen módon kereshetők.

1. Az **Adatok importálása** lapon válassza a **Célindex testreszabása** lehetőséget.
 
2. Írja be az index nevét az **Index neve** mezőbe.

3. Jelölje be a **Lekérdezhető** attribútum jelölőnégyzetét a **metadata_storage_name** mezőnél.

  ![Strukturálatlan keresés](media/storage-unstructured-search/valuestoselect.png)

4. Kattintson a **OK** gombra, és megnyílik az **Indexelő létrehozása** lap.

Az index paraméterei és az ezekhez a paraméterekhez rendelt attribútumok fontosak. A paraméterek azt határozzák meg, hogy a rendszer *milyen* adatokat tároljon, az attribútumok pedig azt, hogy a rendszer *hogyan* tárolja ezeket az adatokat.

A **MEZŐ NEVE** oszlop tartalmazza a paramétereket. Az alábbi táblázat a rendelkezésre álló attribútumokat és a hozzájuk tartozó leírást listázza.

### <a name="field-attributes"></a>Mezőattribútumok
| Attribútum | Leírás |
| --- | --- |
| *Kulcs* |Az egyes dokumentumok egyedi azonosítóját megadó sztring, amelyet a dokumentumok keresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| *Lekérhető* |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| *Szűrhető* |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| *Rendezhető* |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| *Értékkorlátozó* |Lehetővé teszi az adott mező értékkorlátozott navigációs szerkezetben történő használatát a felhasználó által önállóan irányított szűrések során. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében). |
| *Kereshető* |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |


### <a name="create-an-indexer"></a>Indexelő létrehozása
    
  Az indexelő az adatforrást kapcsolja össze a keresési indexszel, és ütemezést biztosít az adatok újraindexeléséhez.

1. Írjon be egy nevet a **Név** mezőbe, majd válassza az **OK** elemet.

  ![Strukturálatlan keresés](media/storage-unstructured-search/exindexer.png)

2. A rendszer visszairányítja az **Adatok importálása** lapra, ahol válassza az **OK** elemet a kapcsolódási folyamat befejezéséhez.

Most már sikeresen csatlakoztatta a blobot a keresési szolgáltatáshoz. Az index feltöltési állapotának megjelenítése néhány percet vesz igénybe a portálon. Azonban a keresési szolgáltatás azonnal megkezdi az indexelést, így Ön is azonnal megkezdheti a keresést.

## <a name="search-your-text-files"></a>Keresés a szöveges fájlokban

A fájlokban történő kereséshez nyissa meg az újonnan létrehozott keresési szolgáltatás indexének keresési ablakát.

Az alábbi lépések megmutatják a keresési ablak helyét, és tartalmaznak néhány példát a lekérdezésekre:

1. Az összes erőforrásnál keresse meg az újonnan létrehozott keresési szolgáltatást.

  ![Strukturálatlan keresés](media/storage-unstructured-search/exampleurl.png)

3. Válassza ki az indexet a megnyitáshoz. 

  ![Strukturálatlan keresés](media/storage-unstructured-search/overview.png)

4. A **Keresési ablak** elem kiválasztásával nyissa meg a keresési ablakot, ahol élő lekérdezéseket végezhet az adatokon.

  ![Strukturálatlan keresés](media/storage-unstructured-search/indexespane.png)

5. Válassza a **Keresés** elemet úgy, hogy a lekérdezési karakterlánc mezője üres legyen. Az üres lekérdezés a blobok *összes* adatát adja vissza.

  ![Strukturálatlan keresés](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Teljes szöveges keresés 

Írja be a „Myopia” kifejezést a **Lekérdezési karakterlánc** mezőbe, majd válassza a **Keresés** elemet. Fájltartalom keresésének kezdeményezése és az adatok egy olyan részhalmazának visszaadása, amely tartalmazza a „Myopia” szót.

  ![Strukturálatlan keresés](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Rendszertulajdonságok alapján történő keresés

A `$select` paraméterrel olyan lekérdezéseket is létrehozhat, amelyek rendszertulajdonságok alapján keresnek. Adja meg a `$select=metadata_storage_name` lekérdezési karakterláncot, nyomja meg az Enter billentyűt, és a rendszer csak az adott mezőt adja vissza.
    
A lekérdezési karakterlánc közvetlenül módosítja az URL-t, így a szóközök használata nem engedélyezett. Több mező kereséséhez használjon vesszőt, például: `$select=metadata_storage_name,metadata_storage_path`
    
A `$select` paraméter csak olyan mezőkkel használható, amelyek lekérdezhetőként lettek megjelölve az index definiálásakor.

  ![Strukturálatlan keresés](media/storage-unstructured-search/metadatasearchunstructured.png) 

Ezennel befejezte ezt az oktatóanyagot, és most már rendelkezik a strukturálatlan adatok kereshető halmazával.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta a strukturálatlan adatok Azure Search használatával történő keresését, például a következő feladatokra:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot
> * Tárfiók létrehozása
> * Tároló létrehozása
> * Adatok feltöltése a tárolóba
> * Keresési szolgáltatás létrehozása
> * A keresési szolgáltatás használata tároló keresésére

Erre a hivatkozásra kattintva többet tudhat meg a dokumentumok Azure Search használatával történő indexeléséről.

> [!div class="nextstepaction"]
> [Dokumentumok indexelése az Azure Blob Storage-ban az Azure Search használatával](../../search/search-howto-indexing-azure-blob-storage.md)