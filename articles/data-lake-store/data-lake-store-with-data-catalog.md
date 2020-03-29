---
title: Adatok regisztrálása az Azure Data Lake Storage Gen1 szolgáltatásból az Azure Data Catalogban | Microsoft dokumentumok
description: Adatok regisztrálása az Azure Data Lake Storage Gen1 szolgáltatásból az Azure Data Catalogban
services: data-lake-store,data-catalog
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd887560c0011fb1ec2141e33f02f7e3d8a39c81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60196535"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Adatok regisztrálása az Azure Data Lake Storage Gen1 szolgáltatásból az Azure Data Catalogban
Ebben a cikkben megtudhatja, hogyan integrálhatja az Azure Data Lake Storage Gen1-et az Azure Data Catalog-tal, hogy az adatok felderíthetők legyenek egy szervezeten belül a Data Catalog-tal való integrálásával. Az adatok katalogizálásáról az Azure Data Catalog című [témakörben](../data-catalog/data-catalog-what-is-data-catalog.md)talál további információt. Az Azure [Data Catalog gyakori forgatókönyveiből.](../data-catalog/data-catalog-common-scenarios.md)

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Engedélyezze azure-előfizetését** a Data Lake Storage Gen1 szolgáltatáshoz. Lásd az [utasításokat](data-lake-store-get-started-portal.md).
* **A Data Lake Storage Gen1 fiók**. Kövesse az [Azure Data Lake Storage Gen1 használatának első](data-lake-store-get-started-portal.md)lépéseit az Azure Portal használatával című útmutatóban. Ebben az oktatóanyagban hozzon létre egy Data Lake Storage Gen1 fiókot, amelyet **datacatalogstore-nak**hívnak.

    Miután létrehozta a fiókot, töltsön fel hozzá egy mintaadatkészletet. Ebben az oktatóanyagban töltsük fel az összes .csv fájlt az [Azure Data Lake Git repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/) **AmbulanceData** mappája alatt. Különböző ügyfelek, például az [Azure Storage Explorer](https://storageexplorer.com/)használatával adatokat tölthet fel egy blobtárolóba.
* **Az Azure-adatkatalógus**. A szervezetnek már rendelkeznie kell a szervezet számára létrehozott Azure Data Catalog-katalógussal. Minden szervezethez csak egy katalógus engedélyezett.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Adattó-tároló gen1 regisztrálása adatkatalógus forrásaként

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Nyissa `https://azure.microsoft.com/services/data-catalog`meg a t, és kattintson **az Első lépések gombra.**
1. Jelentkezzen be az Azure Data Catalog-portálra, majd kattintson a **Publish data** (Adatok közzététele) lehetőségre.

    ![Adatforrás regisztrálása](./media/data-lake-store-with-data-catalog/register-data-source.png "Adatforrás regisztrálása")
1. A következő lapon kattintson az **Alkalmazás indítása gombra.** Ezzel letölti az alkalmazás jegyzékfájlját a számítógépre. Kattintson duplán a jegyzékfájlra az alkalmazás elindításához.
1. Az Üdvözlőlapon kattintson a **Bejelentkezés**gombra, és adja meg a hitelesítő adatait.

    ![Üdvözlőképernyő](./media/data-lake-store-with-data-catalog/welcome.screen.png "Üdvözlőképernyő")
1. Az Adatforrás kiválasztása lapon válassza az **Azure Data Lake Store**lehetőséget, majd kattintson a **Tovább**gombra.

    ![Adatforrás kiválasztása](./media/data-lake-store-with-data-catalog/select-source.png "Adatforrás kiválasztása")
1. A következő lapon adja meg a Data Lake Storage Gen1 fiók nevét, amelyet regisztrálni szeretne a Data Catalog-ban. Hagyja a többi beállítást alapértelmezettként, majd kattintson a **Csatlakozás gombra.**

    ![Kapcsolódás adatforráshoz](./media/data-lake-store-with-data-catalog/connect-to-source.png "Kapcsolódás adatforráshoz")
1. A következő oldal a következő szakaszokra osztható.

    a. A **Kiszolgálói hierarchia** mező a Data Lake Storage Gen1 fiókmappa-struktúrát jelöli. **$Root** a Data Lake Storage Gen1 fiók gyökér, a **AmbulanceData** pedig a Data Lake Storage Gen1 fiók gyökérében létrehozott mappát jelöli.

    b. A **Rendelkezésre álló objektumok** mezőben a AmbulanceData mappa alatt található fájlok és mappák **találhatók.**

    c. **A regisztrálandó objektumok** listája azokat a fájlokat és mappákat, amelyeket regisztrálni szeretne az Azure Data Catalogban.

    ![Adatstruktúra megtekintése](./media/data-lake-store-with-data-catalog/view-data-structure.png "Adatstruktúra megtekintése")
1. Ebben az oktatóanyagban regisztrálnia kell a könyvtárban lévő összes fájlt. Ahhoz, hogy kattintson a (![objektumok áthelyezése](./media/data-lake-store-with-data-catalog/move-objects.png "Objektumok áthelyezése")) gombra, hogy helyezze át az összes fájlt **objektumok regisztrált** mezőbe.

    Mivel az adatok egy szervezeti szintű adatkatalógusban lesznek regisztrálva, ajánlott néhány metaadatot hozzáadni, amelyek segítségével később gyorsan megtalálhatja az adatokat. Hozzáadhat például egy e-mail címet az adattulajdonoshoz (például egy olyanhoz, aki feltölti az adatokat), vagy hozzáadhat egy címkét az adatok azonosításához. Az alábbi képernyőfelvételen látható az adatokhoz adott címke.

    ![Adatstruktúra megtekintése](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Adatstruktúra megtekintése")

    Kattintson a **Regisztráció gombra.**
1. A következő képernyőfelvétel azt jelzi, hogy az adatok sikeresen regisztrálva vannak az adatkatalógusban.

    ![Regisztráció kész](./media/data-lake-store-with-data-catalog/registration-complete.png "Adatstruktúra megtekintése")
1. Kattintson **a Portál megtekintése** gombra a Data Catalog portálra való visszalépéshez, és ellenőrizze, hogy most már hozzáférhet-e a regisztrált adatokhoz a portálról. Az adatokban való kereséshez használhatja az adatok regisztrálása során használt címkét.

     ![Keresés a katalógusban lévő adatok között](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Keresés a katalógusban lévő adatok között")
1. Most már végre műveleteket, mint a megjegyzések hozzáadása és dokumentáció az adatokhoz. További információt az alábbi hivatkozásokon talál.

    * [Adatforrások jegyzetelése az adatkatalógusban](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentumadatforrások az adatkatalógusban](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Lásd még
* [Adatforrások jegyzetelése az adatkatalógusban](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentumadatforrások az adatkatalógusban](../data-catalog/data-catalog-how-to-documentation.md)
* [A Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal](data-lake-store-integrate-with-other-services.md)
