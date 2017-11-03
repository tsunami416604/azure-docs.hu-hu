---
title: "Data Lake Store-ból adatokat regisztrálni kell az Azure Data Catalog |} Microsoft Docs"
description: "Data Lake Store-ból adatokat regisztrálni kell az Azure Data Catalog"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 90e29a9ab522031db8feb934a218d4b671561417
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Data Lake Store-ból adatokat regisztrálni kell az Azure Data Catalog
Ebben a cikkben, megtudhatja, hogyan integrálható az Azure Data Lake Store az Azure Data Catalog, hogy az adatok felderíthető egy szervezeten belül a Data Catalog integrálásával. Az adatok katalogizálása további információkért lásd: [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Forgatókönyvek, amelyben használhatja a Data Catalog ismertetése: [Azure Data Catalog gyakori forgatókönyvei](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure-előfizetés engedélyezése** a Data Lake Store nyilvános előzetes verziójához. Lásd az [utasításokat](data-lake-store-get-started-portal.md).
* **Azure Data Lake Store-fiók**. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait. Ebben az oktatóanyagban ossza meg velünk hozzon létre egy Data Lake Store-fiókot nevű **datacatalogstore**.

    Egyszer létrehozta a fiókot, a minta adatkészletet feltölteni. Ebben az oktatóanyagban található összes .csv fájl feltöltéséhez írja meg nekünk a **AmbulanceData** mappájában a [Azure Data Lake Git-tárház](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Használhatja például a különböző ügyfelek [Azure Tártallózó](http://storageexplorer.com/), feltölteni az adatokat egy blob-tárolóba.
* **Az Azure Data Catalog**. A szervezet már rendelkeznie kell egy Azure Data Catalog létrehozni a szervezet számára. Egy szervezet csak egy katalógus esetén engedélyezett.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Register Data Lake Store a Data Catalog forrásként

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Ugrás a `https://azure.microsoft.com/services/data-catalog`, és kattintson a **Ismerkedés**.
2. Jelentkezzen be az Azure Data Catalog-portált, majd kattintson **adatok közzététele**.

    ![Egy adatforrás regisztrálása](./media/data-lake-store-with-data-catalog/register-data-source.png "egy adatforrás regisztrálása")
3. A következő lapon kattintson a **alkalmazás indítása**. Az Alkalmazásjegyzék-fájl a számítógép letöltése. Kattintson duplán a jegyzékfájl az alkalmazás indításához.
4. Az üdvözlőoldalon kattintson **bejelentkezés**, és adja meg a hitelesítő adatokat.

    ![Üdvözlőképernyő](./media/data-lake-store-with-data-catalog/welcome.screen.png "üdvözlőképernyője")
5. A Select egy adatforrás lap, válassza ki a **Azure Data Lake**, és kattintson a **következő**.

    ![Adatforrás kiválasztása](./media/data-lake-store-with-data-catalog/select-source.png "adatforrás kiválasztása")
6. A következő oldalon adja meg a Data Catalog regisztrálni kívánt Data Lake Store-fiók nevét. Az egyéb beállításokat hagyja az alapértelmezett, és kattintson a **Connect**.

    ![Az adatforráshoz történő csatlakozás](./media/data-lake-store-with-data-catalog/connect-to-source.png "az adatforráshoz történő csatlakozás")
7. A következő oldalon a következő szegmensek lehet osztani.

    a. A **kiszolgálói hierarchia** mezőben a Data Lake Store-fiók mappaszerkezet jelöli. **$Root** Data Lake Store-fiók gyökerét jelző és **AmbulanceData** jelenti. a mappa létrehozása a Data Lake Store-fiók gyökérkönyvtárában.

    b. A **rendelkezésre álló objektumok** mezőben sorolja fel a fájlokat és mappákat a **AmbulanceData** mappa.

    c. **Objektumok regisztrált mezőben** a fájlokat és mappákat, amelyet szeretne regisztrálni kell az Azure Data Catalog sorolja fel.

    ![Adatszerkezet megtekintése](./media/data-lake-store-with-data-catalog/view-data-structure.png "adatszerkezet megtekintése")
8. Ebben az oktatóanyagban regisztrálnia kell az összes fájl a könyvtárban. Ehhez kattintson a (![-objektumainak áthelyezése](./media/data-lake-store-with-data-catalog/move-objects.png "-objektumainak áthelyezése")) gombra kattintva helyezze át az összes fájlt **regisztrálandó objektumok** mezőbe.

    Mivel az adatok egy szervezeti adatkatalógus lesz regisztrálva, bizonyos metaadatait, amelyet később felhasználhat kereshetők meg gyorsan az adatok hozzáadása egy ajánlott maszkolandó módszert is. Például az adatok tulajdonosa (például egy tölti fel az adatokat, akik) hozzáadása egy e-mail címet, vagy az adatok azonosítására címke hozzáadása. Az alábbi képernyőfelvétel-készítés jeleníti meg egy címkét, hogy azt adja hozzá az adatokhoz.

    ![Adatszerkezet megtekintése](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "adatszerkezet megtekintése")

    Kattintson a **regisztrálása**.
9. A következő képernyőfelvétel-készítés azt jelzi, hogy az adatok sikeresen regisztrálva van a Data Catalog.

    ![Regisztráció kész](./media/data-lake-store-with-data-catalog/registration-complete.png "adatszerkezet megtekintése")
10. Kattintson a **View Portal** lépjen vissza a Data Catalog-portált, és győződjön meg arról, hogy érhetők el a regisztrált adategységeket a portálról. Keresse meg az adatokat, az adatok regisztrálása során használt címke is használhat.

     ![Adatok keresése a katalógus](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "keresés adatokat keressen a katalógusban")
11. Most már például való hozzáadását jegyzeteket és dokumentáció található adatok műveleteket hajthat végre. További információkért tekintse meg a következő hivatkozásokat.

    * [A Data Catalog adatforrások ellátása megjegyzésekkel](../data-catalog/data-catalog-how-to-annotate.md)
    * [A Data Catalog dokumentum adatforrások](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Lásd még:
* [A Data Catalog adatforrások ellátása megjegyzésekkel](../data-catalog/data-catalog-how-to-annotate.md)
* [A Data Catalog dokumentum adatforrások](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Store más Azure-szolgáltatásokkal integrálja](data-lake-store-integrate-with-other-services.md)
