---
title: Nyelvi modell testreszabása a Video Indexer webhelyével
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a nyelvi modellt a Video Indexer webhelyén.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128073"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Nyelvi modell testreszabása a Video Indexer webhelyével

A Video Indexer lehetővé teszi, hogy egyéni nyelvi modelleket hozzon létre a beszédfelismerés testreszabásához adaptációs szöveg feltöltésével, nevezetesen olyan tartományból származó szöveget, amelynek szókincséhez alkalmazkodni szeretne. A modell betanítása után a program felismeri az adaptációs szövegben megjelenő új szavakat.

Az egyéni nyelvi modellek részletes áttekintését és gyakorlati tanácsait a [Nyelvi modell testreszabása videoindexelővel](customize-language-model-overview.md)című témakörben találja.

A Video Indexer webhely segítségével egyéni nyelvi modelleket hozhat létre és szerkeszthet a fiókjában, a jelen témakörben leírtak szerint. Az API-t is használhatja, ahogy azt a [Nyelvi modell testreszabása API-k használatával című ismertetése ismerteti.](customize-language-model-with-api.md)

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. A fiókban lévő modell testreszabásához kattintson a lap jobb felső sarkában található **Tartalommodell testreszabása** gombra.

   ![Tartalommodell testreszabása a Video Indexelőben](./media/content-model-customization/content-model-customization.png)

3. Válassza a **Nyelv** lapot.

    Megjelenik a támogatott nyelvek listája.

    ![Nyelvek modellek listája a Video Indexerben](./media/customize-language-model/customize-language-model.png)

4. A kívánt nyelv alatt válassza a **Modell hozzáadása**lehetőséget.
5. Írja be a nyelvi modell nevét, és írja be a beírást.

    Ez a lépés létrehozza a modellt, és lehetőséget ad szöveges fájlok feltöltésére a modellbe.

6. Szövegfájl hozzáadásához válassza **a Fájl hozzáadása**lehetőséget. Megnyílik a fájlkezelő.

7. Keresse meg és jelölje ki a szövegfájlt. Egy nyelvi modellhez több szövegfájlt is hozzáadhat.

    Szövegfájlt úgy is hozzáadhat, hogy a **nyelvmodell** jobb oldalán található ... gombot, a **Fájl hozzáadása**lehetőséget választja.

8. Miután befejezte a szöveges fájlok feltöltését, válassza a zöld **Train** lehetőséget.

    ![Betanítási nyelvi modell a Video Indexerben](./media/customize-language-model/train-model.png)

A képzési folyamat eltarthat néhány percig. Miután a betanítás befejeződött, a modell mellett **betanított** ként láthatja. Megtekintheti, letöltheti és törölheti a fájlt a modellből.

![Betanított nyelvi modell a Video Indexerben](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Nyelvi modell használata új videón

Ha a nyelvi modellt új videón szeretné használni, tegye az alábbi műveletek egyikét:

* Válassza a **Feltöltés** gombot a lap tetején.

    ![Feltöltés gomb Video Indexer](./media/customize-language-model/upload.png)

* Dobja a hang- vagy videofájlt a körbe, vagy keresse meg a fájlt.

    ![Médiafájl feltöltése Video Indexer](./media/customize-language-model/upload2.png)

Kiválaszthatja a **Videó forrásnyelvét.** Válassza ki a legördülő listát, és válassza ki a listából létrehozott nyelvi modellt. Meg kell mondani a nyelvet a nyelvi modell és a nevet, amit adott zárójelben.

Válassza a **Feltöltés** lehetőséget az oldal alján, és az új videó indexelése a Nyelvi modell használatával történik.

### <a name="using-a-language-model-to-reindex"></a>Nyelvi modell használata újraindexeléshez

Ha a nyelvi modellt szeretné használni a gyűjteményben lévő videók újraindexeléséhez, nyissa meg a **Fiókvideóit** a [Video Indexer](https://www.videoindexer.ai/) kezdőlapján, és mutasson az újraindexelni kívánt videó nevére.

Megjelennek a videó szerkesztési, a videó törlésére és a videó újraindexelésének lehetőségei. Válaszd ki a videó újraindexelésének lehetőségét.

![Újraindexelés videoindexelővel](./media/customize-language-model/reindex1.png)

Lehetőséged van arra, hogy kiválaszd azt a **Videó forrásnyelvet,** amivel újraindexeled a videódat. Válassza ki a legördülő listát, és válassza ki a listából létrehozott nyelvi modellt. Meg kell mondani a nyelvet a nyelvi modell és a nevet, amit adott zárójelben.

![Videoforrás nyelvének kiválasztása – Videó újraindexelése videoindexelővel](./media/customize-language-model/reindex.png)

Válaszd az **Újraindex** gombot, és a videód újraindexelésre kerül a Nyelvi modell használatával.

## <a name="edit-a-language-model"></a>Nyelvi modell szerkesztése

A nyelvi modelleket úgy szerkesztheti, hogy módosítja a nevét, fájlokat ad hozzá, és fájlokat távolít belőle.

Ha fájlokat ad hozzá vagy töröl a Nyelvi modellből, a zöld **Vonat** lehetőség kiválasztásával újra be kell tanítania a modellt.

### <a name="rename-the-language-model"></a>A nyelvi modell átnevezése

A nyelvi modell nevét úgy módosíthatja, hogy a nyelvi modell jobb oldalán a három pont (**...**) gombot, majd az Átnevezés lehetőséget **választja.**

Írja be az új nevet, és nyomja meg a beírást.

### <a name="add-files"></a>Fájlok hozzáadása

Szövegfájl hozzáadásához válassza **a Fájl hozzáadása**lehetőséget. Megnyílik a fájlkezelő.

Keresse meg és jelölje ki a szövegfájlt. Egy nyelvi modellhez több szövegfájlt is hozzáadhat.

Szövegfájlt úgy is hozzáadhat, hogy a nyelvi modell jobb oldalán a három pont (**...**) gombot, a **Fájl hozzáadása**lehetőséget választja.

### <a name="delete-files"></a>Fájlok törlése

Ha törölni szeretne egy fájlt a Nyelvi modellből, kattintson a szövegfájl jobb oldalán található három pont (**...**) gombra, és válassza a **Törlés**lehetőséget. Megjelenik egy új ablak, amely azt mondja, hogy a törlés nem szakítható meg. Válassza a **Törlés** lehetőséget az új ablakban.

Ez a művelet teljesen eltávolítja a fájlt a Nyelvi modellből.

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

Ha törölni szeretne egy nyelvi modellt a fiókjából, kattintson a nyelvi modell jobb oldalán található három pont (**...**) gombra, majd a **Törlés gombra.**

Megjelenik egy új ablak, amely azt mondja, hogy a törlés nem szakítható meg. Válassza a **Törlés** lehetőséget az új ablakban.

Ez a művelet teljesen eltávolítja a nyelvi modellt a fiókjából. A törölt nyelvi modellt használó videók ugyanazt az indexet megőrzik, amíg újra indexeli a videót. Ha újraindexeli a videót, új nyelvi modellt rendelhet a videóhoz. Ellenkező esetben a Video Indexer az alapértelmezett modellt fogja használni a videó újraindexeléséhez.

## <a name="customize-language-models-by-correcting-transcripts"></a>Nyelvi modellek testreszabása az átiratok javításával

A Video Indexer támogatja a nyelvi modellek automatikus testreszabását a felhasználók által a videóik átiratain végrehajtandó tényleges korrekciók alapján.

1. Ha ki szeretné javítani az átiratot, nyissa meg a fiókjából szerkesztni kívánt videót. Válassza az **Ütemterv** lapot.

    ![A nyelvi modell idővonalának testreszabása lap – Videoindexelő](./media/customize-language-model/timeline.png)

1. Az átirat átiratának szerkesztéséhez válassza a ceruza ikont.

    ![Nyelvi modell szerkesztési átírásának testreszabása – Videoindexelő](./media/customize-language-model/edits.png)

    A Video Indexer rögzíti az összes olyan sort, amelyet ön javított ki a videó átírásában, és automatikusan hozzáadja őket a "From transcript edits" nevű szövegfájlhoz. Ezek a szerkesztések a videó indexeléséhez használt nyelvi modell újratanítására szolgálnak.
    
    Ha a videó indexelésekén nem adott meg nyelvi modellt, a videó összes szerkesztése a videó észlelt nyelvén belül tárolja a "Fiókadaptációk" nevű alapértelmezett nyelvi modellt.
    
    Abban az esetben, ha ugyanazon a vonalon több módosítás történt, a program csak a javított sor utolsó verzióját használja a nyelvi modell frissítéséhez.  
    
    > [!NOTE]
    > A testreszabáshoz csak szöveges javításokat használ. A tényleges szavakat nem tartalmazó javításokat (például írásjeleket vagy szóközöket) nem tartalmazza.
    
1. Az átiratjavítások a Tartalommodell testreszabási lapjának Nyelv lapján jelennek meg.

    ![Nyelvi modell testreszabása – Videoindexelő](./media/customize-language-model/customize.png)

   Ha meg szeretné tekinteni az egyes nyelvi modellek "From transcript edits" fájlját, válassza ki azt a megnyitásához.

    ![Átiratszerkesztésekből – Videoindexelő](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>További lépések

[Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)
