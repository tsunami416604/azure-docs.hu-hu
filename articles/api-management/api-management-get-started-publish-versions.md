---
title: API-verziók közzététele az Azure API Management szolgáltatással | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan tehet közzé több verziót az API Management szolgáltatásban.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a7e5051248a579b0943fa69620215b060bd1e235
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092693"
---
# <a name="publish-multiple-versions-of-your-api"></a>Az API több verziójának közzététele 

Előfordulhat, hogy nem célszerű, ha az összes hívó ugyanazt az API-verziót használja. Ha a hívók újabb verzióra szeretnének frissíteni, azt egy könnyen érthető megközelítéssel szeretnék megtenni. Ez az Azure API Management **verziók** szolgáltatásával valósítható meg. További információkért lásd: [Verziók és változatok](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új verzió hozzáadása meglévő API-hoz
> * Verzióséma kiválasztása
> * A verzió hozzáadása egy termékhez
> * A fejlesztői portál tallózása a verzió megtekintéséhez

![A fejlesztői portálon látható verzió](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Előfeltételek

* Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
* Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="add-a-new-version"></a>Új verzió hozzáadása

![Az API helyi menüje – Verzió hozzáadása](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Válassza a **Demo Conference API** lehetőséget az API-k listájából.
2. Válassza a mellette lévő helyi menüt (**...**).
3. Válassza a **+ Verzió hozzáadása** lehetőséget.

    > [!TIP]
    > A verziók az új API-k létrehozásakor is engedélyezhetők – válassza az **Új verziót készít az API-ról?** lehetőséget az **API hozzáadása** képernyőn.

## <a name="choose-a-versioning-scheme"></a>Verziókezelési séma kiválasztása

Az Azure API Management segítségével meghatározhatja, hogy a hívók hogyan adhassák meg, hogy az API melyik verzióját szeretnék használni. A használandó API-verziót egy **verziókezelési séma** kiválasztásával határozhatja meg. Ez a séma lehet **elérési út, fejléc vagy lekérdezési sztring**. Az alábbi példában az elérési utat használjuk a verziókezelési séma kiválasztására.

![Verzió hozzáadása képernyő](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. A **verziókezelési sémánál** hagyja bejelölve az **elérési út** beállítást.
2. Adja hozzá a **v1** tagot **névként** és **verzióazonosítóként**.

    > [!TIP]
    > Ha a **fejléc** vagy a **lekérdezési sztring** lehetőséget választja verziókezelési sémaként, meg kell adnia egy további értéket is – a fejléc vagy a lekérdezési sztring paraméterének nevét.

3. Válassza a **Létrehozás** lehetőséget az új verzió beállításához.
4. Az API listában a **Demo Conference API** alatt most két különböző API látható – az **Eredeti** és a **v1**.

    ![Az API alatt listázott verziók az Azure Portalon](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Ha egy verzióval nem rendelkező API-hoz ad hozzá egy verziót, automatikusan létrejön egy **Eredeti** verzió – ez az alapértelmezett URL-címen válaszol. Ez biztosítja, hogy a meglévő hívók kapcsolata ne szakadjon meg az új verzió hozzáadásával. Ha egy új API létrehozásakor engedélyezi a verziókat, nem jön létre Eredeti verzió.

5. A **v1** most az **Eredeti** API-tól eltérő API-ként szerkeszthető és konfigurálható. Az egyik verzió módosítása nem érinti a másikat.

## <a name="add-the-version-to-a-product"></a>A verzió hozzáadása egy termékhez

Ahhoz, hogy a hívók láthassák az új verziót, hozzá kell adni azt egy **termékhez**.

1. Válassza a **Termékek** lehetőséget a klasszikus üzemi modell oldalán.

    ![API Management-termékek](media/api-management-getstarted-publish-versions/Products.png)

2. Válassza a **Korlátlan** lehetőséget.
3. Válassza az **API-k** lehetőséget.
4. Válassza a **Hozzáadás** lehetőséget.
5. Válassza a **Demo Conference API, v1 verzió** elemet.
6. Lépjen a szolgáltatásfelügyeleti oldalra, és válassza az **API-k** elemet.

## <a name="browse-the-developer-portal-to-see-the-version"></a>A fejlesztői portál tallózása a verzió megtekintéséhez

1. A felső menüben kattintson a **Fejlesztői portál** elemre.
2. Válassza az **API-k** lehetőséget, ahol láthatja, hogy a **Demo Conference API** alatt az **Eredeti** és a **v1** verziók láthatók.
3. Válassza a **v1** lehetőséget.
4. Figyelje meg a lista első műveletének **Kérés URL-címe** értékét. Azt mutatja, hogy az API URL-címe tartalmazza a **v1** tagot.

    ![Az API helyi menüje – Verzió hozzáadása](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>További lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új verzió hozzáadása meglévő API-hoz
> * Verzióséma kiválasztása 
> * A verzió hozzáadása egy termékhez
> * A fejlesztői portál tallózása a verzió megtekintéséhez

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [A fejlesztői portál oldalstílusának testreszabása](api-management-customize-styles.md)