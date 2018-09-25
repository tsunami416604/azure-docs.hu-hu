---
title: A LUIS-alkalmazások verziók kezelése
titleSuffix: Azure Cognitive Services
description: Verzió létrehozása és közzététele a különböző modellek lehetővé teszik. Bevált gyakorlat, hogy az alkalmazás más verzióra az aktuális aktív modell klónozza a modell módosítása előtt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: db461191b70aabc322e570ecc814a076c21206f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033787"
---
# <a name="manage-versions"></a>Verziók kezelése

Verzió létrehozása és közzététele a különböző modellek lehetővé teszik. Bevált gyakorlat, hogy a jelenlegi active modell egy másik klónozza [verzió](luis-concept-version.md) az alkalmazás a modell módosítása előtt. 

Verzióival működnek, nyissa meg az alkalmazás nevére a kiválasztásával **saját alkalmazások** oldalra, és kattintson **kezelés** a felső sávon, majd válassza ki **verziók** a bal oldali navigációs. 

Verziók listáját bemutatják, hogy melyik verzió közzététele, ahol azok közzététele megtörténik, és melyik verziója jelenleg aktív. 

[![](./media/luis-how-to-manage-versions/versions-import.png "Verziók lap szakaszban kezelése")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klónozza a verzió

1. Válassza ki a klónozásához, majd válassza ki a kívánt verziót **Klónozás** az eszköztáron. 

2. Az a **Klónozás verzió** párbeszédpanelen írjon be egy nevet az új verziót, például a "0.2-es".

   ![Klónozás verziójára párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Verzió azonosítója csak karakterek, számjegyek állhat vagy '.', és nem lehet hosszabb 10 karakter hosszúságú.
 
 A megadott nevű új verzióját, és a állítja be aktív verziója.

## <a name="set-active-version"></a>Aktív verzió beállítása

Jelöljön ki egy verziót a listából, majd **aktív győződjön meg arról,** az eszköztáron. 

[![](./media/luis-how-to-manage-versions/versions-other.png "Verziók lap szakaszban kezelése")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importálás verzió

1. Válassza ki **importálás verzió** az eszköztáron. 

2. Az a **importálása új verzió** előugró ablak, írja be az új tíz karakter verzió neve. Csak egy beállítani, ha a verzió a JSON-fájl már létezik az alkalmazásban kell.

    ![Verziók lap szakaszban kezelése](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    -Verziót importált, miután az új verzió válik az aktív verziót.

<a name = "export-version"></a>

## <a name="other-actions"></a>Egyéb műveletek

* A **törlése** egy verzió-verzió kiválasztása a listából, majd jelölje ki **törlése** az eszköztáron. Kattintson az **OK** gombra. 
* A **átnevezése** egy verzió-verzió kiválasztása a listából, majd jelölje ki **átnevezése** az eszköztáron. Adjon meg új nevet, és válassza ki **kész**. 
* A **exportálása** egy verzió-verzió kiválasztása a listából, majd jelölje ki **exportálási alkalmazás** az eszköztáron. A fájl letöltése a helyi gépen. 

