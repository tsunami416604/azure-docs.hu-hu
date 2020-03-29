---
title: Verziók kezelése - LUIS
titleSuffix: Azure Cognitive Services
description: A verziók lehetővé teszik különböző modellek készítését és közzétételét. Egy jó gyakorlat, hogy klónozza az aktuális aktív modell egy másik változata az alkalmazás, mielőtt a modell módosítása.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220896"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verziók használata az átmeneti vagy éles alkalmazások befolyásolása nélkül történő szerkesztéshez és teszteléshez

A verziók lehetővé teszik különböző modellek készítését és közzétételét. Egy jó gyakorlat, hogy klónozza az aktuális aktív modell egy másik [változata](luis-concept-version.md) az alkalmazás, mielőtt a modell módosítása. 

A verziókkal való munkához nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **Saját alkalmazások** lapon, majd válassza a **Kezelés** lehetőséget a felső sávon, majd válassza a **Verziók** lehetőséget a bal oldali navigációs sávon. 

A verziólista megmutatja, hogy mely verziók vannak közzétéve, hol vannak közzétéve, és mely verziók aktívak. 

> [!div class="mx-imgBorder"]
> [![Szakasz kezelése, verziók lap](./media/luis-how-to-manage-versions/versions-import.png "Szakasz kezelése, verziók lap")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Verzió klónozása

1. Jelölje ki a klónozni kívánt verziót, majd válassza a **Klónozás** lehetőséget az eszköztáron. 

2. A **Klónozás verziója** párbeszédpanelen írja be az új verzió nevét, például "0.2".

   ![Klónozó verzió párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > A verzióazonosító csak karakterekből, számjegyekből vagy ""-ból állhat, és nem lehet hosszabb 10 karakternél.
 
   A megadott nevű új verzió jön létre, és aktív verzióként van beállítva.

## <a name="set-active-version"></a>Aktív verzió beállítása

Jelöljön ki egy verziót a listából, majd válassza az **eszköztár Aktiválás parancsát.** 

> [!div class="mx-imgBorder"]
> [![Szakasz, verziók lap kezelése, verzióművelet](./media/luis-how-to-manage-versions/versions-other.png "Szakasz, verziók lap kezelése, verzióművelet")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Verzió importálása

Importálhatja az `.json` alkalmazás `.lu` egy vagy verzióját.

1. Válassza az **eszköztár Importálás parancsát,** majd válassza ki a formátumot. 

2. Az **Új verzió importálása** előugró ablakban adja meg az új tíz karakteres verziónevet. Csak akkor kell beállítania egy verzióazonosítót, ha a fájlban lévő verzió már létezik az alkalmazásban.

    ![Szakasz, verziók lap kezelése, új verzió importálása](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    A verzió importálása után az új verzió lesz az aktív verzió.

### <a name="import-errors"></a>Importálási hibák

* Tokenizer hibák: Ha egy **tokenizer hiba** importálásakor, próbál importálni egy verziót, amely egy másik [tokenizer,](luis-language-support.md#custom-tokenizer-versions) mint az alkalmazás jelenleg használ. A probléma megoldásához olvassa el a [Tokenizer verziók közötti áttelepítés témakört.](luis-language-support.md#migrating-between-tokenizer-versions)

<a name = "export-version"></a>

## <a name="other-actions"></a>Egyéb műveletek

* Verzió **törléséhez** jelöljön ki egy verziót a listából, majd válassza a **Törlés** gombot az eszköztáron. Válassza az **Ok gombot.** 
* Verzió **átnevezéséhez** jelöljön ki egy verziót a listából, majd válassza az **Átnevezés** lehetőséget az eszköztáron. Írja be az új nevet, és válassza **a Kész**lehetőséget. 
* Verzió **exportálásához** válasszon ki egy verziót a listából, majd válassza az **Alkalmazás exportálása lehetőséget** az eszköztáron. Válassza a JSON-t a biztonsági mentéshez való exportáláshoz, és válassza **az Exportálás tárolóhoz** lehetőséget az [alkalmazás LUIS-tárolóban való használatához.](luis-container-howto.md)  

