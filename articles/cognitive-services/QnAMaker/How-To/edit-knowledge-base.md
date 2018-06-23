---
title: A Tudásbázis - Microsoft kognitív szolgáltatások szerkesztése |} Microsoft Docs
titleSuffix: Azure
description: A Tudásbázis szerkesztése
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348679"
---
# <a name="edit-a-knowledge-base"></a>A Tudásbázis szerkesztése

Kérdések és válaszok Maker lehetővé teszi a Tudásbázis tartalmának kezelését, adja meg a könnyen használható szerkesztési élményt.

## <a name="edit-your-knowledge-base-content"></a>A Tudásbázis tartalom szerkesztése

1.  Válassza ki **a Tudásbázis körrel** a felső navigációs sávban. 

    Létrehozott vagy csökkenő sorrendben rendezve Önnel megosztott összes szolgáltatását láthatja a **utolsó módosítás** dátum.

    ![A Tudásbázis körrel](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Válasszon egy adott Tudásbázis szerkesztenie azt.

3. Miután a módosítások a Tudásbázisban, kattintson a **mentéséhez és a vonat** ahhoz, hogy a változtatások megőrzéséhez a lap jobb felső sarkában található.    

    ![Mentse és betanítás](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Kilépés a való kattintás előtt a lap mentésekor és vonat nem a változtatások megőrzéséhez.

## <a name="add-a-qna-pair"></a>Egy kérdés-válasz pár hozzáadása

Válassza ki **hozzáadása kérdés-válasz pár** használatával adhat hozzá új sort a Tudásbázis táblázathoz.

![Kérdés-válasz pár hozzáadása](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Egy kérdés-válasz pár törlése

A kérdések és válaszok törléséhez kattintson a **törlése** ikonra a képernyő jobb szélén a kérdések és válaszok sor.

![Kérdés-válasz pár törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő kérdés-válasz pár egy felhasználó lekérdezésnek megfelelő valószínűségét javítása érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Metaadatok hozzáadása


A szűrő ikon kiválasztásával metaadatok pár hozzáadása

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ügyeljen arra, hogy megadott időközönként és módosítások elvesztésének elkerülése érdekében módosítások elvégzése után a Tudásbázis betanításához.

## <a name="manage-large-knowledge-bases"></a>Nagy Tudásbázis körrel kezelése

1. A QnAs vannak **csoportosítva** által az adatforrást, amelyből könyvtárban találhatók. Bontsa ki, vagy az adatforrás csukja össze.
2. Is **keresési** a Tudásbázis tábla tetején a szövegmezőbe írja be a Tudásbázis. Kattintson a kérdést, válasz vagy metaadatok tartalom kereséséhez írja be. Kattintson a keresési szűrő eltávolítása X ikonra.
3. **Tördelési** nagy Tudásbázis körrel kezelése

    ![Keresés, megjelenítheti a csoport](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis együttműködéshez](./collaborate-knowledge-base.md)
