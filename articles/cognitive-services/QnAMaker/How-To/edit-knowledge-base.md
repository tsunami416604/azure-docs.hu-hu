---
title: Tudásbázis – QnA Maker szerkesztése
titleSuffix: Azure Cognitive Services
description: A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: f927e5b7ff65b82aef9d4224d22296e0fa48ad59
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451884"
---
# <a name="edit-a-knowledge-base"></a>Tudásbázis szerkesztése

A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.

## <a name="edit-your-knowledge-base-content"></a>A Tudásbázis-tartalmat szerkesztése

1.  Válassza ki **saját tudásbázisok** a felső navigációs sávban. 

    Megtekintheti a létrehozott vagy a csökkenő sorrendben rendezi az Önnel megosztott összes szolgáltatást a **utolsó módosítás** dátum.

    ![Saját tudásbázisok](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Válassza ki az adott Tudásbázis szerkesztenie azt.
 
3. Kattintson a **Settings** (Beállítások) lehetőségre.

   Itt szerkesztheti a szolgáltatás neve kötelező mező.
  
   Új GYIK-tartalom hozzáadása a tudásbázissal kattintva új URL-címeket adhat hozzá **cikkének kezelés -> "+ URL-cím hozzáadása"** hivatkozásra.
   
   Kattintva törölheti a meglévő URL-címei **Törlés ikon**.
   
   Ha azt szeretné, hogy a legfrissebb tartalmát, a meglévő URL-címei feltérképezése cikkének, a jelölőnégyzet-name osztásjelek **"Frissítés"**, ez a rendszer ekkor frissíti a tudásbázissal legfrissebb URL tartalmát.
   
Támogatott fájl dokumentum cikkének, részének kell kattintva adhat hozzá **cikkének kezelés -> "+ -fájl hozzáadása"**

Minden olyan meglévő cikkének kattintva is importálhat **Ímport cikkének** gombra. 
   
Frissítése az cikkének függ **felügyeleti tarifacsomag** QnA Maker szolgáltatást a knowledgbase társított létrehozása során használt. Ha szükséges, a felügyeleti csomag az Azure Portalról is frissítheti.

4. Ha elkészült a Tudásbázis módosítását, kattintson a **mentéséhez és a vonat** annak érdekében, hogy a változtatások megőrzéséhez az oldal jobb felső sarkában található.    

    ![Mentse és tanítása](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    A Mentés előtt kattintson az oldal elhagyása és train nem menti a módosításokat.

## <a name="add-a-qna-pair"></a>A QnA pár hozzáadása

Válassza ki **hozzáadása kérdés-válasz párt** hozzáadása egy új sort a Tudásbázis tábla.

![A QnA pár hozzáadása](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Egy kérdés-válasz párt törlése

A QnA törléséhez kattintson a **törlése** ikonra a jobb szélen a QnA sor.

![Kérdés-válasz párt törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő QnA pár annak lehetőségét, hogy egy felhasználó lekérdezésnek megfelelő javítása érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>metaadatok hozzáadása


A szűrő ikon kiválasztásával metaadatok pár hozzáadása

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ellenőrizze, hogy rendszeres időközönként mentéséhez és a módosításokat az adatvesztés elkerülése érdekében a módosítások elvégzése után a Tudásbázis betanításához.

## <a name="manage-large-knowledge-bases"></a>Nagy tudásbázisok kezelése

1. A QnA-tudásbázisok vannak **csoportosítva** az adatforrás, amelyből könyvtárban találhatók. Bontsa ki, vagy az adatforrás összecsukása.
2. Is **keresési** a Tudásbázis tábla tetején a szövegmezőbe írja be a Tudásbázis. Kattintson a kérdés válasz metaadat-tartalom, vagy a kereséshez adja meg. Kattintson az X ikont a keresési szűrő eltávolításához.
3. **Tördelés** lehetővé teszi, hogy nagy tudásbázisok kezelése

    ![Keresés, oldalakra bontása csoport](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztésében](./collaborate-knowledge-base.md)
