---
title: Az Azure Data Lake Storage Gen1 származó adatok regisztrálása az Azure Data Catalog |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 származó adatok regisztrálása az Azure Data Catalog
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
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877884"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Az Azure Data Lake Storage Gen1 származó adatok regisztrálása az Azure Data Catalog
Ebből a cikkből megtudhatja, az Azure Data Lake Storage Gen1 integrálása az Azure Data Catalog, hogy az adatok felderíthető egy szervezeten belül a Data Catalog integrálásával. Az adatok katalogizálása további információkért lásd: [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Forgatókönyvek, ahol használhatja a Data Catalog ismertetése: [Azure Data Catalog gyakori forgatókönyvei](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure-előfizetés engedélyezése** a Data Lake Storage Gen1. Lásd az [utasításokat](data-lake-store-get-started-portal.md).
* **A Data Lake Storage Gen1 fiók**. Kövesse az utasításokat, [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md). A jelen oktatóanyag esetében hozzon létre egy Data Lake Storage Gen1 fiókkal, amelynek neve **datacatalogstore**.

    A fiók létrehozása után, és töltse fel egy minta adatkészlet rá. Ebben az oktatóanyagban található összes .csv fájl feltöltéséhez ossza meg velünk az **AmbulanceData** mappájában a [Azure Data Lake Git-tárház](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Használhatja például a különböző ügyfelek részére, [Azure Storage Explorer](https://storageexplorer.com/), az adatok feltöltése a blob-tárolóba.
* **Az Azure Data Catalog**. A szervezet már rendelkeznie kell egy Azure Data Catalog létrehozása a szervezet számára. Csak egy katalógus minden szervezet számára engedélyezett.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Data Lake Storage Gen1 forrásként Regisztráljon a Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Lépjen a `https://azure.microsoft.com/services/data-catalog`, és kattintson a **Ismerkedés**.
1. Jelentkezzen be az Azure Data Catalog portálra, és kattintson a **adatok közzététele**.

    ![Egy adatforrás regisztrálása](./media/data-lake-store-with-data-catalog/register-data-source.png "egy adatforrás regisztrálása")
1. A következő oldalon kattintson a **alkalmazás indítása**. Ez letölti az Alkalmazásjegyzék-fájl a számítógépen. Kattintson duplán a jegyzékfájl az alkalmazás elindításához.
1. Az üdvözlőoldalon kattintson **jelentkezzen be a**, és adja meg hitelesítő adatait.

    ![Üdvözlőképernyőn](./media/data-lake-store-with-data-catalog/welcome.screen.png "üdvözlőképernyője")
1. A Select egy adatforrás lapon, válassza ki a **Azure Data Lake Store**, és kattintson a **tovább**.

    ![Adatforrás kiválasztása](./media/data-lake-store-with-data-catalog/select-source.png "adatforrás kiválasztása")
1. A következő oldalon adja meg a Data Lake Storage Gen1 Data catalogban regisztrálni kívánt fiók nevét. Az egyéb beállításokat hagyja alapértelmezett, és kattintson a **Connect**.

    ![Csatlakozás adatforráshoz](./media/data-lake-store-with-data-catalog/connect-to-source.png "Csatlakozás adatforráshoz")
1. A következő oldalon a következő szakaszok lehet osztani.

    a. A **Kiszolgálóhierarchia** mezőbe a Data Lake Storage Gen1 fiók gyökérmappa-szerkezetében jelöli. **$Root** jelöli a Data Lake Storage Gen1 fiók legfelső szintű és **AmbulanceData** azt a mappát a Data Lake Storage Gen1 fiókjának gyökérmappájában létrejönnek.

    b. A **rendelkezésre álló objektumok** box sorolja fel, a fájlok és mappák mellett a **AmbulanceData** mappát.

    c. **Regisztrálandó objektumok** megjeleníti a fájlok és mappák az Azure Data Catalogban regisztrálni kívánt mezőben.

    ![Megtekintheti az adatok szerkezetét](./media/data-lake-store-with-data-catalog/view-data-structure.png "adatstruktúra megtekintése")
1. A jelen oktatóanyag esetében minden fájl a könyvtárban kell regisztrálni. Ehhez kattintson a (![-objektumainak áthelyezése](./media/data-lake-store-with-data-catalog/move-objects.png "-objektumainak áthelyezése")) gombra kattintva helyezze át az összes fájlt **regisztrálandó objektumok** mezőbe.

    Mivel az adatokat a rendszer a egy szervezeti a data catalog regisztrálja, bizonyos metaadatait, amelyek később segítségével gyorsan megtalálhatja az adatok hozzáadása egy ajánlott módja. Például az adatok tulajdonosa (például egy tölti fel az adatokat, akik) az e-mail cím megadása, vagy adjon hozzá egy címke azonosíthatja az adatokat. Az alábbi képernyőfelvétel-készítés jeleníti meg egy címkét, adja hozzá az adatokhoz.

    ![Megtekintheti az adatok szerkezetét](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "adatstruktúra megtekintése")

    Kattintson a **regisztrálása**.
1. Az alábbi képernyőfelvétel-készítés, az azt jelzi, hogy az adatok sikeresen regisztrálva van a Data Catalog.

    ![Regisztráció kész](./media/data-lake-store-with-data-catalog/registration-complete.png "adatstruktúra megtekintése")
1. Kattintson a **portál megtekintése** lépjen vissza a Data Catalog portálra, és győződjön meg arról, hogy most már elérhető a regisztrált adatforrásokat a portálról. Az adatok keresésére, használhatja a címke az adatok regisztrálása során használt.

     ![Adatok keresése a katalógusban](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "adatok keresése a katalógusban")
1. Most már elvégezheti a műveletek, mint a jegyzetek és dokumentáció ad hozzá az adatokat. További információkért lásd az alábbi hivatkozásokat.

    * [A Data Catalog az adatforrások ellátása megjegyzésekkel](../data-catalog/data-catalog-how-to-annotate.md)
    * [A Data Catalog az adatforrások dokumentálása](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Lásd még
* [A Data Catalog az adatforrások ellátása megjegyzésekkel](../data-catalog/data-catalog-how-to-annotate.md)
* [A Data Catalog az adatforrások dokumentálása](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Storage Gen1 integrálása más Azure-szolgáltatások](data-lake-store-integrate-with-other-services.md)
