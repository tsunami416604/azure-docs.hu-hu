---
title: 'Adatok exportálása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja az adatok exportálása modult az Azure Machine Learning szolgáltatás eredmények, a köztes adatok és a működő adatok mentse a kísérletekből felhőalapú tárolási célhely kívül az Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028320"
---
# <a name="export-data-module"></a>Export Data modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával eredmények, a köztes adatok és a működő adatok mentse a kísérletekből felhőalapú tárolási célhely kívül az Azure Machine Learning.

Ez a modul exportálása vagy mentése folyamatban van az adatok az alábbi felhőbeli szolgáltatások támogatja:


- **Az Azure Blob Storage-exportálás**: Menti az adatokat a Blob szolgáltatás az Azure-ban. Nyilvánosan megosztott vagy menteni a biztonságos alkalmazás adattárakban lévő adatokat a Blob service-ben.

  
## <a name="how-to-configure-export-data"></a>Adatok exportálása konfigurálása

1. Adja hozzá a **adatok exportálása** modult a kísérletvászonra a felületen. Ez a modul a annak a **bemeneti és kimeneti** kategória.

2. Csatlakozás **adatok exportálása** , a modul a adatait tartalmazó exportálni szeretné.

3. Kattintson duplán a **adatok exportálása** megnyitásához a **tulajdonságok** ablaktáblán.

4. A **adat célhelyét**, válassza ki a felhőalapú tárolást, amelyen menteni fogja az adatokat. Ha módosításokat hajt végre ezt a beállítást, az összes többi tulajdonság állnak vissza. Ezért ügyeljen arra, hogy először válassza ezt a lehetőséget!

5. Adjon meg egy fiók nevét és hitelesítési módszer a megadott tárfiók eléréséhez szükséges.

    **Az Azure Blob Storage exportálása** az egyetlen lehetőség van a privát előzetes verzióban érhető el. Alábbi bemutatja, hogyan állíthatja be a modult.
    1. Az Azure blob szolgáltatás nem nagy mennyiségű adatot, beleértve a bináris adatok tárolására. A blob Storage két típusa van: nyilvános blobok és a blobokat, amelyekre a bejelentkezési hitelesítő adatok szükségesek.

    2. A **hitelesítési típus**, válassza a **nyilvános (SAS)** Ha tudja, hogy a storage támogatja-e a hozzáférést egy SAS URL-CÍMEN keresztül.

          Egy SAS URL-címet egy olyan speciális típusú URL-címet, amely egy Azure storage-segédprogram használatával hozhat létre, és csak korlátozott ideig áll rendelkezésre.  A hitelesítéshez és a letöltés szükséges összes információt tartalmazza.

        A **URI**írja be vagy illessze be a teljes URI-t, amely meghatározza a fiók és a nyilvános blob.

        Fájlformátum a megosztott Fürtkötettel és TSV támogatottak.

    3. Személyes fiókok esetében válassza a **fiók**, és adja meg a fiók nevét és a fiókkulcsot, hogy a kísérlet lehet írni a storage-fiókot.

         - **Fiók neve**: Írja be vagy illessze be a fiók neve, ahol az adatokat menteni szeretné. Például, ha a tárfiók teljes URL-címe `http://myshared.blob.core.windows.net`, írja be `myshared`.

        - **Fiókkulcs**: Illessze be a tárelérési kulcs, amely a fiókhoz van hozzárendelve.

        -  **Tároló, a könyvtár vagy a blob elérési útja**: Adja meg az exportált adatok tárolására szolgáló BLOB nevét. Például mentse az eredményeket a kísérlet egy új blob nevű **results01.csv** a tárolóban **előrejelzéseket** nevű fiókban **mymldata**, teljes URL-CÍMÉT a BLOB lenne `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Ezért a mezőben **tároló, a könyvtár vagy a blob elérési útja**, kell megadni a tároló és blobnév, a következő: `predictions/results01.csv`

        - Ha a nevet, egy blobot, amely még nem létezik, az Azure létrehozza a blobot, az Ön számára.

       -  Meglévő blobokhoz írásakor is megadhat, hogy a blob tartalmának aktuális felül a tulajdonság beállítása **az Azure blob-tároló írási mód**. Alapértelmezés szerint ez a tulajdonság értéke **hiba**, ami azt jelenti, hogy hiba akkor következik be, amikor az azonos nevű meglévő blob fájl található.


    4. A **blobfájlra fájlformátum**, válassza ki a formátumot, ahol adatokat kell tárolni.

        - **CSV**: Vesszővel tagolt (CSV) a rendszer az alapértelmezett tárolási formátum. Oszlopfejlécek az adatokkal együtt exportálja, jelölje be a jelölőnégyzetet, **írási blob fejlécsor**.  A vesszővel tagolt formátumú használja az Azure Machine Learning kapcsolatos további információkért lásd: [átalakítás fürt megosztott Kötetévé](./convert-to-csv.md).

        - **TSV**: Tabulátorral tagolt értékeket (TSV) formátuma kompatibilis számos machine learning-eszközökkel. Oszlopfejlécek az adatokkal együtt exportálja, jelölje be a jelölőnégyzetet, **írási blob fejlécsor**.  

 
    5. **Gyorsítótárazott eredményeket használja**: Akkor válassza ezt a beállítást, ha el szeretné kerülni az eredményeket a blobfájlba újraírását minden alkalommal, amikor a kísérlet futtatásához. Ha nem változtak más modul paraméterekhez, a kísérlet ír az eredmények csak az első alkalommal fut, a modul, vagy az adatok változásakor.

    6. Futtassa a kísérletet.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 