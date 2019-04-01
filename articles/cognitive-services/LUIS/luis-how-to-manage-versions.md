---
title: Verziók kezelése
titleSuffix: Language Understanding - Azure Cognitive Services
description: Verzió létrehozása és közzététele a különböző modellek lehetővé teszik. Bevált gyakorlat, hogy az alkalmazás más verzióra az aktuális aktív modell klónozza a modell módosítása előtt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: dfe23baa67c87b04a65630611ef71758beda268d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754911"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verziók segítségével módosíthatja, és átmeneti és éles alkalmazások befolyásolása nélkül tesztelése

Verzió létrehozása és közzététele a különböző modellek lehetővé teszik. Bevált gyakorlat, hogy a jelenlegi active modell egy másik klónozza [verzió](luis-concept-version.md) az alkalmazás a modell módosítása előtt. 

Verzióival működnek, nyissa meg az alkalmazás nevére a kiválasztásával **saját alkalmazások** oldalra, és kattintson **kezelés** a felső sávon, majd válassza ki **verziók** a bal oldali navigációs. 

Verziók listáját bemutatják, hogy melyik verzió közzététele, ahol azok közzététele megtörténik, és melyik verziója jelenleg aktív. 

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

[![Kezelés szakaszhoz, verziók lap, győződjön meg arról, egy verzió művelet](./media/luis-how-to-manage-versions/versions-other.png "kezelés szakaszhoz, verziók lap, győződjön meg arról, egy verzió művelet")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importálás verzió

1. Válassza ki **importálás verzió** az eszköztáron. 

2. Az a **importálása új verzió** előugró ablak, írja be az új tíz karakter verzió neve. Csak egy beállítani, ha a verzió a JSON-fájl már létezik az alkalmazásban kell.

    ![Verziók lap, új verzió importálása a szakaszban kezelése](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    -Verziót importált, miután az új verzió válik az aktív verziót.

### <a name="import-errors"></a>Importálási hiba

* Jogkivonatokat létrehozó hibák: Ha egy **jogkivonatokat létrehozó hiba** importálásakor, egy olyanra, amely használja egy másik importálni próbált [jogkivonatokat létrehozó](luis-language-support.md#custom-tokenizer-versions) , mint az alkalmazás jelenleg használ. A probléma megoldásához tekintse meg [jogkivonatokat létrehozó verziók közötti Migrálás](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Egyéb műveletek

* A **törlése** egy verzió-verzió kiválasztása a listából, majd jelölje ki **törlése** az eszköztáron. Kattintson az **OK** gombra. 
* A **átnevezése** egy verzió-verzió kiválasztása a listából, majd jelölje ki **átnevezése** az eszköztáron. Adjon meg új nevet, és válassza ki **kész**. 
* A **exportálása** egy verzió-verzió kiválasztása a listából, majd jelölje ki **exportálási alkalmazás** az eszköztáron. A fájl letöltése a helyi gépen. 

