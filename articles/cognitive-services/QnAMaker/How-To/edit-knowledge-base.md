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
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b5ee7f60eab0349378767473c9c80f035a65c9a5
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669139"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>A QnA Maker Tudásbázis szerkesztése

A QnA Maker lehetővé teszi a Tudásbázis tartalmát kezelését azáltal, hogy egy könnyen használható szerkesztési funkciót.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>A Tudásbázis-tartalmat szerkesztése

1.  Válassza a **saját tudásbázisok** lehetőséget a felső navigációs sávon. 

    Az összes Ön által létrehozott vagy megosztott szolgáltatást megtekintheti az **utolsó módosítás** dátuma szerinti csökkenő sorrendben.

    ![Saját tudásbázisok](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Válassza ki az adott Tudásbázis szerkesztenie azt.
 
1. Válassza a **Beállítások**lehetőséget. Itt szerkesztheti a szolgáltatás neve kötelező mező.
  
    |Cél|Műveletek|
    |--|--|
    |URL-cím felvétele|Új URL-címek hozzáadásával új GYIK-tartalmakat adhat hozzá a tudásbázishoz, ha a Tudásbázis kezelése elemre kattint **– > "+ URL-cím hozzáadása"** hivatkozást.|
    |URL-cím törlése|A Törlés ikonra kattintva törölheti a meglévő URL-címeket, a Kuka is.|
    |Tartalom frissítése|Ha azt szeretné, hogy a tudásbázisa a meglévő URL-címek legújabb tartalmát bejárja, jelölje be a **refresh (frissítés** ) jelölőnégyzetet. Ekkor a rendszer egyszer frissíti a tudásbázist a legújabb URL-tartalommal. Ez nem állítja be a frissítések rendszeres ütemtervét.|
    |Fájl hozzáadása|Egy Tudásbázis részét képező támogatott dokumentum hozzáadásához válassza a **Tudásbázis kezelése**lehetőséget, majd a **+ fájl hozzáadása** elemet.|
    |Importálás|A meglévő tudásbázist a **Tudásbázis importálása** gomb kiválasztásával is importálhatja. |
    |Frissítés|A Tudásbázis frissítése a tudásbázishoz kapcsolódó QnA Maker szolgáltatás létrehozásakor használt **felügyeleti díjszabási szinttől** függ. Ha szükséges, a felügyeleti csomag az Azure Portalról is frissítheti.

1. Miután végzett a Tudásbázis módosításával, a módosítások megőrzése érdekében válassza a **Mentés és a betanítás** lehetőséget a lap jobb felső sarkában.    

    ![Mentse és tanítása](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Ha a **Mentés és a betanítás**kiválasztása előtt elhagyja a lapot, az összes módosítás elvész.

## <a name="add-a-qna-pair"></a>Kérdés-válasz pár hozzáadása

Az **Edit (Szerkesztés** ) lapon válassza a **QnA pár hozzáadása** elemet új sor hozzáadásához a Tudásbázis-táblához.

![A QnA pár hozzáadása](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Egy kérdés-válasz párt törlése

A QnA törléséhez kattintson a QnA sor jobb szélén található **Törlés** ikonra. Ez a végleges művelet. Nem lehet visszavonni. A párok törlése előtt érdemes lehet a TUDÁSBÁZISt a **közzétételi** lapról exportálni. 

![Kérdés-válasz párt törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő QnA pár annak lehetőségét, hogy egy felhasználó lekérdezésnek megfelelő javítása érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>metaadatok hozzáadása

Adja hozzá a metaadatokat úgy, hogy először kiválasztja a **megtekintési beállításokat**, majd kiválasztja a **metaadatok megjelenítése**lehetőséget. Ekkor megjelenik a metaadatok oszlop. Ezután válassza ki a **+** aláírást a metaadat-párok hozzáadásához. Ez a pár egy kulcsból és egy értékből áll.

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ellenőrizze, hogy rendszeres időközönként mentéséhez és a módosításokat az adatvesztés elkerülése érdekében a módosítások elvégzése után a Tudásbázis betanításához.

## <a name="manage-large-knowledge-bases"></a>Nagy tudásbázisok kezelése

* **Adatforrás-csoportok**: a QnAs azon adatforrás szerint vannak csoportosítva, amelyből kibontották őket. Bontsa ki, vagy az adatforrás összecsukása.

    ![Az adatforrással kapcsolatos kérdések és válaszok összecsukása és kibontása a QnA Maker adatforrás-sávjának használatával](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Keresés a Tudásbázisban**: a Tudásbázis táblázatának tetején található szövegmező beírásával keresheti meg a tudásbázist. Kattintson a kérdés válasz metaadat-tartalom, vagy a kereséshez adja meg. Kattintson az X ikont a keresési szűrő eltávolításához.

    ![A kérdések és válaszok fölötti QnA Maker keresőmező használatával csökkentse a nézetet csak a szűrővel egyező elemek](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Tördelés**: az adatforrások gyors áthelyezése a nagyméretű tudásbázisok kezelésére

    ![A kérdések és válaszok lapokon való áthelyezéséhez használja a QnA Maker tördelési funkcióit.](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Tudásbázisok törlése

(KB) Tudásbázis törlése nem visszavonható művelet. Nem lehet visszavonni. A Tudásbázis törlése előtt exportálja a tudásbázist a QnA Maker portál **Beállítások** lapjáról. 

Ha megosztja a KB-ban [közreműködőket](collaborate-knowledge-base.md) , majd törli azt, mindenki elveszti a kb-hoz való hozzáférést. 

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése 

Ha törli a QnA Maker tudásbázisok használt Azure-erőforrások, a tudásbázisok többé nem fognak működni. Az erőforrások törlése előtt győződjön meg arról, hogy a tudásbázisokat a **Beállítások** lapról exportálja. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Együttműködés a Tudásbázisban](./collaborate-knowledge-base.md)
