---
title: A Video Indexer webhely segítségével testre szabhatja a nyelvi modell – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a nyelvi modell a Video Indexer webhelyet.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 7b81b53c03104023823bef75beb4ac6077feede7
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918688"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>A Video Indexer webhelyet nyelvi modell testreszabása

A video Indexer segítségével hozhat létre egyéni nyelvi modelljeit, testre szabható beszédfelismerés betanítás szövegben, nevezetesen a tartomány, amelynek Szójegyzék a motor mértékének megfelelően szeretné feltölteni. Ha a modell betanításához a betanítás szövegben szereplő szavakat ismerhető fel. 

A részletes áttekintése és ajánlott eljárások a testreszabott nyelvi modell: [Video Indexer nyelvi modell testreszabása](customize-language-model-overview.md).

Használhatja a Video Indexer webhelyet hozhat létre és szerkeszthet egyéni nyelvi modelljeit a fiókban, ebben a témakörben leírtak szerint. Is használhatja az API-t, a leírtak szerint [testreszabása nyelvi modell API-k használatával](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Testre szabhatja egy modellt a fiókjában, kattintson a a **modell testreszabása tartalom** gombra az oldal jobb felső sarkában.

   ![Tartalom modell testreszabása](./media/content-model-customization/content-model-customization.png)

3. Válassza ki a **nyelvi** fülre.

    Támogatott nyelvek listáját láthatja. 

    ![Nyelvi modell testreszabása](./media/customize-language-model/customize-language-model.png)

4. Kattintson a kívánt nyelv **Hozzáadás modell**.
5. Írja be a nevét, a nyelvi modell, és nyomja le meg.

    Ez a modell létrehozása, és teszi a szöveges fájlok feltöltését a modellbe.
6. Adjon hozzá egy szöveges fájlt, kattintson a **Add file**. Megnyílik a Fájlkezelőben.

7. Keresse meg és jelölje be a szövegfájl. Nyelvi modell is hozzáadhat több szöveges fájlok.

    Szövegfájl kattintva is hozzáadhat a **...**  a nyelvi modell jobb oldali gombjával, és kiválasztja **Add file**.
8. Ha elkészült a szöveges fájlok feltöltése, kattintson a zöld a **Train** lehetőséget.

    ![Train nyelvi modell](./media/customize-language-model/train-model.png)

A betanítási folyamat néhány percet is igénybe vehet. Miután megtörtént a képzés, **Trained** mellett a modell. Előzetes verzió, töltse le és törölje a fájlt a modellből.

![Betanított nyelvi modell](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Az új videó nyelvi modell használatával

A nyelvi modellt használ egy új videót, tegye a következők egyikét:

* Kattintson a **feltöltése** gombra az oldal felső részén 

    ![Feltöltés](./media/customize-language-model/upload.png)
* A körre a dobja el a hang- vagy fájl, vagy a fájl keresése tallózással

    ![Feltöltés](./media/customize-language-model/upload2.png)

Ekkor kap arra, hogy válassza ki a **videó Forrásnyelv**. Kattintson a legördülő, és válassza ki a nyelvi modell, amely létrehozta a listából. Üzenetnek kell megjelennie, a nyelvi modell és a nevét, amely rendelte hozzá zárójelben nyelvét.

Kattintson a **feltöltése** az oldalon, és az új videó alján található a beállítás a nyelvi modell indexelését.

### <a name="using-a-language-model-to-reindex"></a>Nyelvi modell használatával újraindexelése

A nyelvi modell használatával egy videót a gyűjteményt a újraindexelés, lépjen a **videók fiók** a a [Video Indexer](https://www.videoindexer.ai/) kezdőlapjára, és vigye a kurzort a videó újraindexelése kívánt nevét.

A videó szerkesztése, törlése, videó és a videó újraindexelése beállításokat láthatja. Kattintson a beállítás újraindexelni a videót.

![Reindex](./media/customize-language-model/reindex1.png)

Ez lehetővé teszi a kívánt a **videó Forrásnyelv** újraindexelni a videót. Kattintson a legördülő, és válassza ki a nyelvi modell, amely létrehozta a listából. Üzenetnek kell megjelennie, a nyelvi modell és a nevét, amely rendelte hozzá zárójelben nyelvét.

![Reindex](./media/customize-language-model/reindex.png)

Kattintson a **újraindexelése** gomb, valamint a videó lesz újra kell indexelni, nyelvi modell használatával.

## <a name="edit-a-language-model"></a>Nyelvi modell szerkesztése

Nyelvi modell szerkesztéséhez névváltoztatás fájlok felvételével és törli a fájlokat belőle.

Hozzáadásakor, vagy törölje a fájlokat a a nyelvi modell, el újra a modell betanítását kattintva. a zöld **betanításához** lehetőséget.

### <a name="rename-the-language-model"></a>Nevezze át a nyelvi modell

Módosíthatja a nyelvi modell nevére kattintva **...**  jobb oldalán a nyelvi modell, és kiválasztja **átnevezése**. 

Adja meg a nevét, majd kattintson az új típust.

### <a name="add-files"></a>Fájlok hozzáadása

Adjon hozzá egy szöveges fájlt, kattintson a **Add file**. Megnyílik a Fájlkezelőben.

Keresse meg és jelölje be a szövegfájl. Nyelvi modell is hozzáadhat több szöveges fájlok.

Szövegfájl kattintva is hozzáadhat a **...**  a nyelvi modell jobb oldali gombjával, és kiválasztja **Add file**.

### <a name="delete-files"></a>Fájlok törlése

A nyelvi modell egy fájl törléséhez kattintson a **...**  jobb oldalán a szövegfájl gombra, majd **törlése**. Ekkor megjelenik egy új ablakot, amely arról tájékoztat, hogy a törlés nem vonható vissza. Kattintson a **törlése** lehetőséget az új ablakban.

Ez a művelet a fájl teljes mértékben eltávolítja a nyelvi modell alapján.

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

Nyelvi modell, a fiók törléséhez kattintson a **...**  jobb oldalán található a nyelvi modell gombra, majd **törlése**.

Ekkor megjelenik egy új ablakot, amely arról tájékoztat, hogy a törlés nem vonható vissza. Kattintson a **törlése** lehetőséget az új ablakban.

Ez a művelet a nyelvi modell teljesen eltávolítja a fiókból. Bármely videó használta a szolgáltatást a törölt nyelvi modell fogja megőrizni a ugyanazt az indexet, mindaddig, amíg újra indexeli a videót. Ha újraindexelni a videót, egy új nyelvi modellbe rendelhet a videót. Ellenkező esetben a Video Indexer használja az alapértelmezett modell újraindexelni a videót. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Nyelvi modell testreszabásával szövegekben javítása

A video Indexer nyelvi modelleket, a tényleges javításokat felhasználók alapján győződjön meg arról, a beszédátírás videók megtekintése az automatikus testre szabható.

1. Ahhoz, hogy egy szöveges javításokat, nyissa meg a videót, amely a szerkeszteni kívánt a fiók videókkal. Válassza ki a **ütemterv** fülre.

    ![Nyelvi modell testreszabása](./media/customize-language-model/timeline.png)
1. Kattintson a ceruza ikonra a beszédátírási szövegének szerkesztését. 

    ![Nyelvi modell testreszabása](./media/customize-language-model/edits.png)

    A video Indexer rögzíti az összes sort tartalmazza az Ön által a videóra vonatkozó szabálymezők, és hozzáadja őket automatikusan ", átirat módosításokat" nevű szövegfájlba. Ezeket a szerkesztéseket a megadott nyelvi modell, amellyel ez a videó index újbóli betanítása szolgálnak. 
    
    Ebben a videóban indexelésekor nyelvi modell nem adta meg, ha, majd a videó minden módosításokat fog tárolt nevű fiók betanítások belül a felismert nyelv a videó alapértelmezett nyelvi modell. 
    
    Abban az esetben, ha több módosítások történtek-e ugyanabban a sorban, csak a javított sor utolsó verziójának frissítéséhez a nyelvi modell használható.  
    
    > [!NOTE]
    > A testreszabás csak szöveges korrekciók szolgálnak. Ez azt jelenti, hogy, amelyek nem tényleges szavak (például írásjelek vagy tárolóhelyek) javítások nem szerepelnek. 
    
1. Látni fogja a nyelvi lapja tartalommodell testreszabási megjelennek a szövegben javításokat.

    ![Nyelvi modell testreszabása](./media/customize-language-model/customize.png)

   Keresse meg a ", átirat módosításokat" fájlban az egyes nyelvi modelljeit, kattintson rá a megnyitásához. 

    ![Átiratmódosításokból származó](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>További lépések

[Testre szabhatja a nyelvi modell API-k használatával](customize-language-model-with-api.md)
