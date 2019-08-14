---
title: Tudásbázis – QnA Maker szerkesztése
titleSuffix: Azure Cognitive Services
description: A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1342616a0d32d8ac68965bc04f1fd3ca60146369
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966768"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>A QnA Maker Tudásbázis szerkesztése

A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.

<a name="add-datasource"></a>

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

A **Beállítások** lapon válassza a **QnA pár hozzáadása** lehetőséget egy új sor hozzáadásához a Tudásbázis-táblához.

![A QnA pár hozzáadása](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Egy kérdés-válasz párt törlése

A QnA törléséhez kattintson a **törlése** ikonra a jobb szélen a QnA sor. Ez a végleges művelet. Nem lehet visszavonni. Vegye figyelembe a Tudásbázissal való exportálása a **közzététel** lap párok törlése előtt. 

![Kérdés-válasz párt törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő QnA pár annak lehetőségét, hogy egy felhasználó lekérdezésnek megfelelő javítása érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>metaadatok hozzáadása

Adja hozzá a metaadatokat úgy, hogy először kiválasztja a **megtekintési beállításokat**, majd kiválasztja a **metaadatok megjelenítése**lehetőséget. Ekkor megjelenik a metaadatok oszlop. Ezután válassza ki a **+** jelet egy metaadat-pár hozzáadásához. Ez a pár egy kulcsból és egy értékből áll.

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ellenőrizze, hogy rendszeres időközönként mentéséhez és a módosításokat az adatvesztés elkerülése érdekében a módosítások elvégzése után a Tudásbázis betanításához.

## <a name="manage-large-knowledge-bases"></a>Nagy tudásbázisok kezelése

* **Adatforrás-csoportok**: A QnAs azon adatforrás szerint vannak csoportosítva, amelyből kinyerték őket. Bontsa ki, vagy az adatforrás összecsukása.

    ![Az adatforrással kapcsolatos kérdések és válaszok összecsukása és kibontása a QnA Maker adatforrás-sávjának használatával](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Keresés**az Tudásbázisban: A Tudásbázisban a Tudásbázis tábla tetején található szövegmezőbe írja be a keresést. Kattintson a kérdés válasz metaadat-tartalom, vagy a kereséshez adja meg. Kattintson az X ikont a keresési szűrő eltávolításához.

    ![A kérdések és válaszok fölötti QnA Maker keresőmező használatával csökkentse a nézetet csak a szűrővel egyező elemek](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Tördelés**: Az adatforrások gyors áthelyezése a nagyméretű tudásbázisok kezelésére

    ![A kérdések és válaszok lapokon való áthelyezéséhez használja a QnA Maker tördelési funkcióit.](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Tudásbázisok törlése

(KB) Tudásbázis törlése nem visszavonható művelet. Nem lehet visszavonni. Mielőtt törölné a Tudásbázis, exportálnia kell a Tudásbázis következő, a **beállítások** a QnA Maker portal oldalán. 

Ha a KB, a megosztott [közreműködők](collaborate-knowledge-base.md) törölje azt, mindenki elveszítette a hozzáférését a KB. 

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése 

Ha törli a QnA Maker tudásbázisok használt Azure-erőforrások, a tudásbázisok többé nem fognak működni. Összes erőforrás törlése, előtt győződjön meg arról, a tudásbázisok az Exportálás a **beállítások** lapot. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztésében](./collaborate-knowledge-base.md)
