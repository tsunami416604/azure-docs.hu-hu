---
title: Verziók kezelése – LUIS
titleSuffix: Azure Cognitive Services
description: Verzió létrehozása és közzététele a különböző modellek lehetővé teszik. Bevált gyakorlat, hogy az alkalmazás más verzióra az aktuális aktív modell klónozza a modell módosítása előtt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 1169af85c35f04a302816f564caeb49eaf455d0e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563670"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verziók használata az átmeneti vagy éles alkalmazások befolyásolása nélkül

Verzió létrehozása és közzététele a különböző modellek lehetővé teszik. Bevált gyakorlat, hogy a jelenlegi active modell egy másik klónozza [verzió](luis-concept-version.md) az alkalmazás a modell módosítása előtt. 

Verzióival működnek, nyissa meg az alkalmazás nevére a kiválasztásával **saját alkalmazások** oldalra, és kattintson **kezelés** a felső sávon, majd válassza ki **verziók** a bal oldali navigációs. 

A verziók listája megjeleníti a közzétett verziókat, a közzétételük helyét, valamint a jelenleg aktív verziót. 

[![Kezelés szakaszhoz, verziók lap](./media/luis-how-to-manage-versions/versions-import.png "kezelés szakaszhoz, verziók lap")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klónozza a verzió

1. Válassza ki a klónozásához, majd válassza ki a kívánt verziót **Klónozás** az eszköztáron. 

2. Az a **Klónozás verzió** párbeszédpanelen írjon be egy nevet az új verziót, például a "0.2-es".

   ![Klónozás verziójára párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Verzió azonosítója csak karakterek, számjegyek állhat vagy '.', és nem lehet hosszabb 10 karakter hosszúságú.
 
   A megadott nevű új verzióját, és a állítja be aktív verziója.

## <a name="set-active-version"></a>Aktív verzió beállítása

Jelöljön ki egy verziót a listából, majd **aktív győződjön meg arról,** az eszköztáron. 

[![A szakasz, a verziók lap, a verzióra vonatkozó művelet kezelése](./media/luis-how-to-manage-versions/versions-other.png "A szakasz, a verziók lap, a verzióra vonatkozó művelet kezelése")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importálás verzió

1. Válassza ki **importálás verzió** az eszköztáron. 

2. Az a **importálása új verzió** előugró ablak, írja be az új tíz karakter verzió neve. Csak egy beállítani, ha a verzió a JSON-fájl már létezik az alkalmazásban kell.

    ![Szakasz, verziók lap, új verzió importálása](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    -Verziót importált, miután az új verzió válik az aktív verziót.

### <a name="import-errors"></a>Importálási hibák

* Tokenizer hibák: Ha az importálás során **tokenizer hibaüzenetet** kap, egy olyan verziót próbál importálni, amely a jelenleg használt alkalmazástól eltérő [tokenizer](luis-language-support.md#custom-tokenizer-versions) használ. A probléma megoldásához tekintse meg az [áttelepítés a tokenizer-verziók között](luis-language-support.md#migrating-between-tokenizer-versions)című témakört.

<a name = "export-version"></a>

## <a name="other-actions"></a>Egyéb műveletek

* A **törlése** egy verzió-verzió kiválasztása a listából, majd jelölje ki **törlése** az eszköztáron. Kattintson az **OK** gombra. 
* A **átnevezése** egy verzió-verzió kiválasztása a listából, majd jelölje ki **átnevezése** az eszköztáron. Adjon meg új nevet, és válassza ki **kész**. 
* A **exportálása** egy verzió-verzió kiválasztása a listából, majd jelölje ki **exportálási alkalmazás** az eszköztáron. Válassza ki a JSON-t a biztonsági mentéshez való exportáláshoz, majd válassza az **Exportálás tárolóként** lehetőséget az [alkalmazás egy Luis](luis-container-howto.md)-tárolóban való használatához.  

