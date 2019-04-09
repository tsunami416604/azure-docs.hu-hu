---
title: Hozzon létre egy Azure Data Catalog
description: A rövid útmutató az Azure Data Catalog létrehozásához.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 66d178497f3403579990340b88a1153905b2a2bf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271033"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Gyors útmutató: Hozzon létre egy Azure Data Catalog

Az Azure Data Catalog teljes körűen felügyelt felhőszolgáltatás, amely vállalati adategységek regisztrációs és felderítőrendszereként szolgál. A szolgáltatás részletes bemutatásáért olvassa el a [Mi az az Azure Data Catalog?](overview.md) című cikket.

A rövid útmutató segítségével első lépésként létrehozhat egy Azure Data Catalog.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez meg kell rendelkeznie:

* A [Microsoft Azure](https://azure.microsoft.com/) előfizetés.
* Rendelkeznie kell a saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

A Data Catalog beállításához az Azure-előfizetés tulajdonosa vagy társtulajdonosa kell lennie.

## <a name="create-a-data-catalog"></a>Hozzon létre egy data catalog

Szervezetenként (Azure Active Directory-tartományonként) mindössze egy adatkatalógust hozhat létre. Ezért ha a tulajdonosa vagy társtulajdonosa Azure-előfizetés az Azure Active Directory-tartományhoz tartozó már létrehozott egy katalógust, majd nem hozhat egy katalógus újra akkor is, ha több Azure-előfizetéssel rendelkezik. Ha szeretné megnézni, hogy az Ön Azure Active Directory-tartományában hozott-e már létre adatkatalógust valamelyik felhasználó, lépjen az [Azure Data Catalog honlapjára](http://azuredatacatalog.com), és ellenőrizze, hogy lát-e katalógust. Ha valaki már létrehozta a katalógust, ugorja át az itt látható eljárást, és folytassa a következő résszel.

1. Nyissa meg a [az Azure portal](https://portal.azure.com) > **erőforrás létrehozása** válassza **a Data Catalog**.

    ![Adatkatalógus létrehozása](media/data-catalog-get-started/data-catalog-create.png)

2. Adjon meg egy **neve** a data CATALOG Az **előfizetés** szeretné használni, a **helye** a katalógus és a **tarifacsomag**. Ezután kattintson a **Létrehozás** elemre.

3. Nyissa meg az [Azure Data Catalog kezdőlapját](http://azuredatacatalog.com), és kattintson az **Adatok közzététele** elemre.

   ![Azure Data Catalog – Adatok közzététele gomb](media/data-catalog-get-started/data-catalog-publish-data.png)

   Emellett érheti el a Data Catalog kezdőlapját, a [Data Catalog szolgáltatás weboldalát](https://azure.microsoft.com/services/data-catalog) kiválasztásával **Ismerkedés**.

   ![Azure Data Catalog – marketinges kezdőlap](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Nyissa meg a **beállítások** lapot.

    ![Azure Data Catalog – adatkatalógus létrehozása](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Bontsa ki a **díjszabási** , és ellenőrizze az Azure Data Catalog **edition** (ingyenes vagy Standard).

    ![Azure Data Catalog – kiadás kiválasztása](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Ha úgy dönt, *Standard* bővítheti tarifacsomag szerint edition **biztonsági csoportok** és engedélyezés engedélyezése az Active Directory biztonsági csoportokat a Data Catalog eléréséhez, és az automatikus módosításának engedélyezése számlázási.

    ![Az Azure Data Catalog biztonsági csoportok](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Bontsa ki a **Katalógus felhasználói** csomópontot, majd kattintson a **Hozzáadás** gombra, és adja hozzá a kívánt felhasználókat az adatkatalógushoz. Ön automatikusan hozzáadja ehhez a csoporthoz.

    ![Azure Data Catalog – felhasználók](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Ha úgy dönt, *Standard* tarifacsomag szerint edition, kibővítheti **szószedet-rendszergazdák** kattintson **Hozzáadás** szószedet-rendszergazda felhasználók hozzáadása. Ön automatikusan hozzáadja ehhez a csoporthoz.

    ![Az Azure Data Catalog szószedet-rendszergazdák](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Bontsa ki a **Katalógus-rendszergazdák** csomópontot, majd kattintson a **Hozzáadás** gombra, így további rendszergazdákat rendelhet hozzá az adatkatalógushoz. Ön automatikusan hozzáadja ehhez a csoporthoz.

    ![Azure Data Catalog – rendszergazdák](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Bontsa ki a **portál címe** , és adja hozzá a további szöveget, amely megjelenik a portál címében.

    ![Az Azure Data Catalog-portál címe](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Miután elvégezte az a **beállítások** lapon, lépjen tovább a **közzététel** lap.

    ![Azure Data Catalog – létrehozva](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Adatkatalógus megkeresése az Azure Portalon

1. A böngésző egy másik lapján, illetve egy másik böngésző ablakában nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be ugyanazzal a fiókkal, amelyet az előző lépésnél az adatkatalógus létrehozásához használt.

2. Válassza ki **minden szolgáltatás** majd **a Data Catalog**.

    ![Az Azure Data Catalog – Tallózás az Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Láthatja, hogy a korábban létrehozott adatkatalógust.

    ![Azure Data Catalog – katalóguslista megtekintése](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Kattintson a korábban létrehozott katalógusra. Megjelenik a portál **Data Catalog** panele.

   ![Azure Data Catalog – panel a portálon](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Itt megtekintheti az adatkatalógus tulajdonságait, illetve frissítheti is őket. Kattintson például a **Tarifacsomag** elemre, és módosítsa a kiadást.

    ![Azure Data Catalog – tarifacsomag](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban bemutattuk, hogyan hozhat létre egy Azure Data Catalog a szervezet számára. Most már regisztrálhatja az adatforrásokat a data catalog.

> [!div class="nextstepaction"]
> [Az Azure Data Catalog az adatforrások regisztrálása](data-catalog-how-to-register.md)
