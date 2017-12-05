---
title: "Az Azure API Management használata API-verzióit közzététele |} Microsoft Docs"
description: "Ez az oktatóanyag áttekintésével megismerheti, hogyan több verzió közzététele az API Management kövesse."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: d63bdd3110f5c5db3e7bfec424644fdbc8d8d90c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="publish-multiple-versions-of-your-api"></a>Az API-t több verziójának közzététele 

Nincsenek időpontokat, amikor nem kivitelezhető, hogy az összes hívó számára az API-t használja pontosan azonos verzióját. Egyes esetekben közzétenni kívánt API szolgáltatásainak új vagy eltérő szeretne egyes felhasználóknak, míg mások szeretné, hogy az API-t, amely jelenleg működik a számukra odatapadjon. Hívóknak újabb verzióra frissíteni szeretné, ha szeretnék végezhető el ez a módszer megértéséhez egy alkalmazását.  Azt is megteheti **verziók** az Azure API Management. További információkért lásd: [változatok & verzió](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új verzió hozzáadása egy meglévő API
> * Verzió séma kiválasztása
> * A verzió hozzáadása egy termék
> * Keresse meg a fejlesztői portálhoz, és a verzió

![A verzió jelenik meg a fejlesztői portálján](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-a-new-version"></a>Adja hozzá az új verzió

![API helyi menüből – adja hozzá a verzió](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Válassza ki **konferencia-API** API listájából.
2. Válassza ki a helyi menüt (**...** ) látható.
3. Válassza ki **+ Hozzáadás verzió**.

    > [!TIP]
    > Verziók is engedélyezhető, ha először létre kell hoznia egy új API - kiválasztása **az API verzió?** a a **hozzáadása API** képernyő.

## <a name="choose-a-versioning-scheme"></a>Versioning séma kiválasztása

Az Azure API Management válassza ki, amelyben hívók adhatja meg az API verziójának azok hogyan teszi lehetővé. Ehhez válassza a **verziókezelő rendszer**. Ez a séma lehet **elérési út, fejlécet vagy lekérdezési karakterlánc**. A jelen példában használjuk elérési útja.

![Adja hozzá a verzió képernyő](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Hagyja **elérési** kijelölt, mint a **verziókezelő rendszer**.
2. Adja hozzá **v1** , a **verzióazonosító**.

    > [!TIP]
    > Ha **fejléc** vagy **lekérdezési karakterlánc** egy versioning sémát kell adjon meg egy további érték – a fejléc neve, vagy a lekérdezési karakterlánc.

3. Ha kívánja, adjon meg egy leírást.
4. Válassza ki **létrehozása** állíthatja be az új verzióra.
5. Underneath **nagy konferencia-API** API listájában ekkor megjelenik az két különböző API - **eredeti**, és **v1**.

    ![Egy API-t az Azure portálon felsorolt verziók](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Ha egy verziót ad hozzá egy fájlkivonatát API, mindig létrehozhatunk egy **eredeti** - meg az alapértelmezett URL-cím válaszol. Ez biztosítja, hogy minden meglévő hívóknak nem bontásban való hozzáadása egy verzió. Ha egy olyan új API-verziók érhető el a start hoz létre, az eredeti nem jön létre.

6. Most szerkesztését és konfigurálását a **v1** , az API-k, amely nem csatlakozik a **eredeti**. Egy verziót módosításai nem befolyásolják egy másikra.

## <a name="add-the-version-to-a-product"></a>A verzió hozzáadása egy termék

A hívó számára jelenik meg az új verzió, azt kell adni egy **termék** (termékek nem örökli a szülő verziók).

1. Válassza ki **termékek** szolgáltatás kezelése lapján.
2. Válassza ki **korlátlan**.
3. Válassza ki **API-k**.
4. Válassza a **Hozzáadás** lehetőséget.
5. Válassza ki **konferencia API-verzió v1**.
6. Térjen vissza a szolgáltatás felügyeleti lapra, és válassza ki **API-k**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Keresse meg a fejlesztői portálhoz, és a verzió

1. Válassza ki **fejlesztői portálján** a felső menüben.
2. Válassza ki **API-k**, figyelje meg, hogy **konferencia-API** látható **eredeti** és **v1** verziók.
3. Válassza ki **v1**.
4. Figyelje meg a **URL-cím kérése** az első művelet a listában. Azt mutatja, hogy API URL-címet tartalmaz **v1**.

    ![API helyi menüből – adja hozzá a verzió](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új verzió hozzáadása egy meglévő API
> * Verzió séma kiválasztása 
> * A verzió hozzáadása egy termék
> * Keresse meg a fejlesztői portálhoz, és a verzió

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Frissítés és skálázás](upgrade-and-scale.md)