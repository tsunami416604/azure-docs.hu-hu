---
title: Tudásbázis – QnA Maker szerkesztése
titleSuffix: Azure Cognitive Services
description: A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: b2810a34c8b4b7329568ee8601d296c4eeb263ff
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226400"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>A QnA Maker Tudásbázis szerkesztése

A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.

## <a name="edit-your-knowledge-base-content"></a>A Tudásbázis-tartalmat szerkesztése

1.  Válassza ki **saját tudásbázisok** a felső navigációs sávban. 

    Megtekintheti a létrehozott vagy a csökkenő sorrendben rendezi az Önnel megosztott összes szolgáltatást a **utolsó módosítás** dátum.

    ![Saját tudásbázisok](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Válassza ki az adott Tudásbázis szerkesztenie azt.
 
1. Válassza ki **beállítások**. Itt szerkesztheti a szolgáltatás neve kötelező mező.
  
    |Cél|Műveletek|
    |--|--|
    |URL-cím felvétele|Új GYIK-tartalom hozzáadása a Tudásbázis kattintva új URL-címeket adhat hozzá **Tudásbázis kezelés -> "+ URL-cím hozzáadása"** hivatkozásra.|
    |URL-cím törlése|A Törlés ikonra kattintva törölheti a meglévő URL-címeket, a Kuka is.|
    |Tartalom URL-cím frissítése|Ha azt szeretné, hogy a Tudásbázis feltérképezése a legfrissebb tartalmát, a meglévő URL-címei, válassza ki a **frissítése** jelölőnégyzetet. Ez frissíti a Tudásbázis legújabb URL-cím tartalommal.|
    |Fájl hozzáadása|Egy támogatott dokumentumot, egy Tudásbázis részét kiválasztásával adhat hozzá **Tudásbázis kezelése**, majd válassza **+ fájl hozzáadása**|
    |Importálás|Minden olyan meglévő Tudásbázis kiválasztásával is importálhat **Ímport Tudásbázis** gombra. |
    |Frissítés|Frissítés a Tudásbázis függ **felügyeleti tarifacsomag** QnA Maker szolgáltatást a Tudásbázis társított létrehozásakor használt. Ha szükséges, a felügyeleti csomag az Azure Portalról is frissítheti.

1. Ha elkészült a módosítása a Tudásbázis válassza **mentéséhez és betanítunk** annak érdekében, hogy a változtatások megőrzéséhez az oldal jobb felső sarkában található.    

    ![Mentse és tanítása](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Ha elhagyja a lapot kiválasztása előtt **mentéséhez és a vonat**, minden módosítása elveszik.

## <a name="add-a-qna-pair"></a>Kérdés-válasz pár hozzáadása

Válassza ki **hozzáadása kérdés-válasz párt** hozzáadása egy új sort a Tudásbázis tábla.

![A QnA pár hozzáadása](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Egy kérdés-válasz párt törlése

A QnA törléséhez kattintson a **törlése** ikonra a jobb szélen a QnA sor. Ez a végleges művelet. Nem lehet visszavonni. Vegye figyelembe a Tudásbázissal való exportálása a **közzététel** lap párok törlése előtt. 

![Kérdés-válasz párt törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő QnA pár annak lehetőségét, hogy egy felhasználó lekérdezésnek megfelelő javítása érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>metaadatok hozzáadása


A metaadat ikon kiválasztásával metaadatok pár hozzáadása

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ellenőrizze, hogy rendszeres időközönként mentéséhez és a módosításokat az adatvesztés elkerülése érdekében a módosítások elvégzése után a Tudásbázis betanításához.

## <a name="manage-large-knowledge-bases"></a>Nagy tudásbázisok kezelése

* **Adatforrás-csoportok**: A QnA-tudásbázisok az adatforrás, amelyből könyvtárban találhatók szerint vannak csoportosítva. Bontsa ki, vagy az adatforrás összecsukása.

    ![A QnA Maker adatsáv forrás használatával összecsukása, és bontsa ki a data source kérdések és válaszok](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Tudásbázisban**: A Tudásbázis tábla tetején a szövegmezőbe írja be a Tudásbázis kereshet. Kattintson a kérdés válasz metaadat-tartalom, vagy a kereséshez adja meg. Kattintson az X ikont a keresési szűrő eltávolításához.

    ![A QnA Maker keresőmezőbe írja felett a kérdések és válaszok használatával csökkentheti a szűrő egyező elemek csak a nézet](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Tördelés**: Gyorsan mozoghat adatforrások nagy tudásbázisok kezelése

    ![Váltás a lapok kérdések és válaszok a QnA Maker tördelés funkciókat a fenti kérdések és válaszok használata](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Tudásbázisok törlése

(KB) Tudásbázis törlése nem visszavonható művelet. Nem lehet visszavonni. Mielőtt törölné a Tudásbázis, exportálnia kell a Tudásbázis következő, a **beállítások** a QnA Maker portal oldalán. 

Ha a KB, a megosztott [közreműködők](collaborate-knowledge-base.md) törölje azt, mindenki elveszítette a hozzáférését a KB. 

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése 

Ha törli a QnA Maker tudásbázisok használt Azure-erőforrások, a tudásbázisok többé nem fognak működni. Összes erőforrás törlése, előtt győződjön meg arról, a tudásbázisok az Exportálás a **beállítások** lapot. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztésében](./collaborate-knowledge-base.md)
