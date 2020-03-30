---
title: Azure Data Catalog létrehozása
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Azure Data Catalog az Azure Portal használatával.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "68976864"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Rövid útmutató: Azure-adatkatalógus létrehozása

Az Azure Data Catalog teljes körűen felügyelt felhőszolgáltatás, amely vállalati adategységek regisztrációs és felderítőrendszereként szolgál. A szolgáltatás részletes bemutatásáért olvassa el a [Mi az az Azure Data Catalog?](overview.md) című cikket.

Ez a rövid útmutató segít az Azure Data Catalog létrehozásának megkezdésében.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre van szükség:

* [Microsoft Azure-előfizetés.](https://azure.microsoft.com/)
* Saját Azure Active [Directory-bérlővel kell rendelkeznie.](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

A Data Catalog beállításához egy Azure-előfizetés tulajdonosának vagy társtulajdonosának kell lennie.

## <a name="create-a-data-catalog"></a>Adatkatalógus létrehozása

Szervezetenként (Azure Active Directory-tartományonként) mindössze egy adatkatalógust hozhat létre. Ezért ha az Azure Active Directory-tartományhoz tartozó Azure-előfizetés tulajdonosa vagy társtulajdonosa már létrehozott egy katalógust, akkor nem hozhat létre újra katalógust, még akkor sem, ha több Azure-előfizetéssel rendelkezik. Ha szeretné megnézni, hogy az Ön Azure Active Directory-tartományában hozott-e már létre adatkatalógust valamelyik felhasználó, lépjen az [Azure Data Catalog honlapjára](http://azuredatacatalog.com), és ellenőrizze, hogy lát-e katalógust. Ha valaki már létrehozta a katalógust, ugorja át az itt látható eljárást, és folytassa a következő résszel.

1. Nyissa meg az [Azure PortalErőforrás](https://portal.azure.com) > **létrehozása és** az **Adatkatalógus**lehetőséget.

    ![Az Azure Data Catalog létrehozása gomb](media/data-catalog-get-started/data-catalog-create.png)

2. Adja meg az adatkatalógus **nevét,** a használni kívánt **előfizetést,** a katalógus **helyét** és a **tarifacsomagot.** Ezután válassza **a Létrehozás lehetőséget.**

3. Nyissa meg az [Azure Data Catalog kezdőlapját](http://azuredatacatalog.com), és kattintson az **Adatok közzététele** elemre.

   ![Azure Data Catalog – Adatok közzététele gomb](media/data-catalog-get-started/data-catalog-publish-data.png)

   A Data Catalog [szolgáltatás lapjáról](https://azure.microsoft.com/services/data-catalog) az Első lépések lehetőséget választva is megérheti a Data Catalog **kezdőlapját.**

   ![Azure Data Catalog – marketinges kezdőlap](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Nyissa meg a **Beállítások** lapot.

    ![Azure Data Catalog – adatkatalógus létrehozása](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. **Bontsa ki a díjszabást,** és ellenőrizze az Azure Data **Catalog-kiadását** (ingyenes vagy standard).

    ![Azure Data Catalog – kiadás kiválasztása](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Ha a *Standard* kiadást választja tarifacsomagként, kibonthatja a **biztonsági csoportokat,** és engedélyezheti az Active Directory biztonsági csoportok engedélyezését az adatkatalógus eléréséhez, és engedélyezheti a számlázás automatikus módosítását.

    ![Azure-adatkatalógus biztonsági csoportjai](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Bontsa ki a **Katalógus felhasználói** csomópontot, majd kattintson a **Hozzáadás** gombra, és adja hozzá a kívánt felhasználókat az adatkatalógushoz. A rendszer automatikusan hozzáadódik ehhez a csoporthoz.

    ![Azure Data Catalog – felhasználók](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Ha a *Standard* kiadást választja tarifacsomagként, bontsa ki a **Glossary Administrators elemet,** és kattintson a **Hozzáadás** gombra a szószedetrendszergazdai felhasználók hozzáadásához. A rendszer automatikusan hozzáadódik ehhez a csoporthoz.

    ![Az Azure Data Catalog szószedetrendszergazdái](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Bontsa ki a **Katalógus-rendszergazdák** csomópontot, majd kattintson a **Hozzáadás** gombra, így további rendszergazdákat rendelhet hozzá az adatkatalógushoz. A rendszer automatikusan hozzáadódik ehhez a csoporthoz.

    ![Azure Data Catalog – rendszergazdák](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Bontsa ki **a portál címét,** és adjon hozzá további szöveget, amely megjelenik a portál címében.

    ![Az Azure Data Catalog-Portal címe](media/data-catalog-get-started/data-catalog-portal-title.png)

11. A **Beállítások** lap befejezése után keresse meg a **Közzététel** lapot.

    ![Azure Data Catalog – létrehozva](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Adatkatalógus megkeresése az Azure Portalon

1. A böngésző egy másik lapján, illetve egy másik böngésző ablakában nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be ugyanazzal a fiókkal, amelyet az előző lépésnél az adatkatalógus létrehozásához használt.

2. Válassza a **Minden szolgáltatás** lehetőséget, majd kattintson **az Adatkatalógus gombra.**

    ![Azure Data Catalog - böngésszen az Azure-ban](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Megjelenik a létrehozott adatkatalógus.

    ![Azure Data Catalog – katalóguslista megtekintése](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Kattintson a korábban létrehozott katalógusra. Megjelenik a portál **Data Catalog** panele.

   ![Azure Data Catalog – panel a portálon](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Itt megtekintheti az adatkatalógus tulajdonságait, illetve frissítheti is őket. Kattintson például a **Tarifacsomag** elemre, és módosítsa a kiadást.

    ![Azure Data Catalog – tarifacsomag](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Data Catalog-t a szervezet számára. Most már regisztrálhatja az adatforrásokat az adatkatalógusban.

> [!div class="nextstepaction"]
> [Adatforrások regisztrálása az Azure Data Catalogban](data-catalog-how-to-register.md)
