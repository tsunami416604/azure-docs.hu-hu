---
title: 'Importálás az Azure Blob Storage-ból: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, ez a témakör ismerteti, hogyan használható az importálás az Azure Blob Storage-modul az Azure Machine Learning szolgáltatásban adatokat olvasni az Azure blob storage-ban, hogy az adatokat a machine learning-kísérletből is használhatja.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029685"
---
# <a name="import-from-azure-blob-storage-module"></a>Azure Blob Storage-modul importálása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használható adatokat olvasni az Azure blob storage-ban, hogy használhatja az adatokat a machine learning-kísérlet.  

Az Azure Blob szolgáltatás nem nagy mennyiségű adatot, beleértve a bináris adatok tárolására. Azure-blobok bárhonnan, HTTP vagy HTTPS használatával is elérhetők. Hitelesítés a blob storage típusától függően szükség lehet. 

- Bárki, vagy egy SAS URL-cím rendelkező felhasználók számára érhető el nyilvános blobok.
- Privát blobok szükséges bejelentkezési adatokat és hitelesítő adatokat.

Blob storage-ból importálása van szükség, hogy adatokat használó blobok tárolja a **blokkblob** formátumban. A blob található fájlok vesszővel tagolt (CSV) vagy tabulátorral tagolt (TSV) formátumot kell használnia. Ha a fájl elolvasásához, a rekordokat és bármely alkalmazandó attribútum fejlécek vannak a memóriába sorok adatkészletként.


Javasoljuk, hogy profilját, az adatok az importálás előtt, győződjön meg arról, hogy a séma az elvárt módon. Az importálási folyamat megvizsgálja az egyes határozza meg a séma fő sorok száma, de újabb sort tartalmazhat felesleges oszlopok vagy hibákat okozó adatokhoz.



## <a name="manually-set-properties-in-the-import-data-module"></a>Manuálisan állítsa be a tulajdonságokat a az adatok importálása modullal

A következő lépések bemutatják, hogyan manuális konfigurálásához az importálás forrás.

1. Adja hozzá a **adatok importálása** modult a kísérletvászonra. Ez a modul az található a felületen, a a **adatok bemeneti és kimeneti**

2. A **adatforrás**válassza **Azure Blob Storage**.

3. A **hitelesítési típus**, válassza a **nyilvános (SAS URL-cím)** Ha tudja, hogy az információk nyilvános adatforrásként lett megadva. A SAS URL-címet az időhöz kötött URL nyilvánosan elérhető, amely egy Azure storage-segédprogram használatával is létrehozhat.

    Ellenkező esetben válasszon **fiók**.

4. Ha az adatok egy **nyilvános** blobot, amely a SAS URL-cím használatával érhető el, mert az URL-karakterlánc letöltése és a hitelesítéshez szükséges összes információt tartalmazza, nem kell további hitelesítő adatokat.

    Az a **URI** mezőbe írja be vagy illessze be a teljes URI-t, amely meghatározza a fiók és a nyilvános blob.



5. Ha az adatok egy **privát** fiók, meg kell adnia hitelesítő adatait, beleértve a fiók nevét és a kulcsot.

    - A **fióknév**írja be vagy illessze be a fiók, amely tartalmazza a használni kívánt blob neve.

        Például, ha a tárfiók teljes URL-címe `http://myshared.blob.core.windows.net`, írja be `myshared`.

    - A **fiókkulcs**, illessze be a tárelérési kulcs, amely a fiókhoz van hozzárendelve.

        Ha nem ismeri a hozzáférési kulcsot, című témakör, "Az Azure storage-fiókok kezelése" Ebben a cikkben: [Az Azure Storage-fiókokkal kapcsolatos](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. A **tároló, a könyvtár vagy a blob elérési útja**, írja be a blobra, a lekérdezni kívánt nevét.

    Például, ha egy fájlt a feltöltött **data01.csv** a tárolóhoz **trainingdata** nevű fiókban **mymldata**, a fájl teljes URL-cím lesz: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Ezért a mezőben **tároló, a könyvtár vagy a blob elérési útja**, írja be: `trainingdata/data01.csv`

    Több fájlt importál, használhatja a helyettesítő karakterek `*` (csillag) vagy `?` (kérdőjel).

    Például, ha a tároló `trainingdata` több fájl tartalmaz egy kompatibilis formátumra, használhatja a következő specifikáció kezdődő összes fájl olvasása `data`, és fűzze össze őket egy adatkészletbe:

    `trainingdata/data*.csv`

    A tároló neve nem használhat helyettesítő karaktereket. Importálja a fájlokat a több tároló van szüksége, ha egy külön példányát használja a **adatok importálása** modul minden egyes tároló és az adatkészletek használatával majd egyesítési a [sorok hozzáadása](./add-rows.md) modul.

    > [!NOTE]
    > Ha a megoldás, kiválasztotta **használja a gyorsítótárazott eredményeket**, módosításokat hajt végre a tárolóban található fájlokat nem aktiválhatja az adatok a kísérletben.

7. A **Blob fájlformátum**, válasszon egy beállítást, amely azt jelzi, hogy a blobban tárolt adatok formátumát, hogy az Azure Machine Learning képes megfelelően feldolgozni az adatokat. A következő formátumokban támogatja:

    - **CSV**: Vesszővel tagolt (CSV) exportálása és importálása az Azure Machine Learning fájlok alapértelmezett tárolási formátumát. Ha az adatok már tartalmaz egy fejléc sorra, ügyeljen arra, hogy a beállítást, **fájl rendelkezik fejlécsor**, vagy a fejlécet, adatsorrá lesznek kezelve.

       

    - **TSV**: Tabulátorral tagolt értékeket (TSV) számos machine learning-eszközök által használt formátumban. Ha az adatok már tartalmaz egy fejléc sorra, ügyeljen arra, hogy a beállítást, **fájl rendelkezik fejlécsor**, vagy a fejlécet, adatsorrá lesznek kezelve.

       

    - **ARFF**: Ehhez a formátumhoz a Weka eszközkészlet által használt formátumú fájlok importálásával. 

   

8. Futtassa a kísérletet.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 