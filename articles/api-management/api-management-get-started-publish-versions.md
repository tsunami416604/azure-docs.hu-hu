---
title: "Az Azure API Management használata API-verzióit közzététele |} Microsoft Docs"
description: "Ez az oktatóanyag áttekintésével megismerheti, hogyan több verzió közzététele az API Management kövesse."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Az API-t több verziója kiszámítható módon közzététele
Ez az oktatóanyag ismerteti, hogyan állítsa be az API-verzióit, és válassza ki, hogyan API fejlesztők nevezzük.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez szüksége API Management szolgáltatás létrehozása, és egy meglévő API-t (helyett konferencia-API) módosíthatja.

## <a name="about-versions"></a>Verziókról
Egyes esetekben célszerű praktikus, hogy az összes hívó az API-nak ugyanazon verzióját használja. Egyes esetekben közzétenni kívánt API szolgáltatásainak új vagy eltérő szeretne egyes felhasználóknak, míg mások szeretné, hogy az API-t, amely jelenleg működik a számukra odatapadjon. Hívóknak újabb verzióra frissíteni szeretné, ha szeretnék végezhető el ez a módszer megértéséhez egy alkalmazását.  Azt is megteheti **verziók** az Azure API Management.

## <a name="walkthrough"></a>Útmutatás
Ez a forgatókönyv azt hozzá egy új verziója egy meglévő API-t, válassza a verziókezelő rendszer és verzió azonosítója.

## <a name="add-a-new-version"></a>Adja hozzá az új verzió
![API helyi menüből – adja hozzá a verzió](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Keresse meg a **API-k** lap az Azure-portálon az API Management szolgáltatáson belül.
2. Válassza ki **konferencia-API** API, majd válassza ki a listáról a helyi menüt (**...** ) látható.
3. Válassza ki **+ Hozzáadás verzió**.

    > [!TIP]
    > Verziók is engedélyezhető, ha először létre kell hoznia egy új API - kiválasztása **az API verzió?** a a **hozzáadása API** képernyő.

## <a name="choose-a-versioning-scheme"></a>Versioning séma kiválasztása
Az Azure API Management válassza ki, amelyben hívók adhatja meg az API verziójának azok hogyan teszi lehetővé. Ehhez válassza a **verziókezelő rendszer**. Ez a séma lehet **elérési út, fejlécet vagy lekérdezési karakterlánc**. A jelen példában használjuk elérési útja.
![Adja hozzá a verzió képernyő](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. Hagyja **elérési** kijelölt, mint a **verziókezelő rendszer**.
2. Adja hozzá **v1** , a **verzióazonosító**.

    > [!TIP]
    > Ha **fejléc** vagy **lekérdezési karakterlánc** versioning sémába, akkor adjon meg egy további érték – a fejléc neve, vagy a lekérdezési karakterlánc.

3. Ha kívánja, adjon meg egy leírást.
4. Válassza ki **létrehozása** állíthatja be az új verzióra.
5. Underneath **nagy konferencia-API** API listájában ekkor megjelenik az két különböző API - **eredeti**, és **v1**.
![Egy API-t az Azure portálon felsorolt verziók](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Ha egy verziót ad hozzá egy fájlkivonatát API, mindig létrehozhatunk egy **eredeti** - meg az alapértelmezett URL-cím válaszol. Ez biztosítja, hogy minden meglévő hívóknak nem bontásban való hozzáadása egy verzió. Ha egy olyan új API-verziók érhető el a start hoz létre, az eredeti nem jön létre.

6. Most szerkesztését és konfigurálását a **v1** , az API-k, amely teljesen különálló való **eredeti**. Egy verziót módosításai nem befolyásolják egy másikra.

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
![A verzió jelenik meg a fejlesztői portálján](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
