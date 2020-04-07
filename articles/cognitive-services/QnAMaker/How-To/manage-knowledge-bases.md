---
title: Tudásbázisok kezelése - QnA Maker
description: A QnA Maker lehetővé teszi tudásbázisai kezelését a tudásbázis beállításaihoz és tartalmához való hozzáférés biztosításával.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4eb07b30b10826c361f4f9b805b517e372fc5ea1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756801"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Tudásbázis létrehozása és beállítások kezelése

A QnA Maker lehetővé teszi tudásbázisai kezelését a tudásbázis beállításaihoz és adatforrásaihoz való hozzáférés biztosításával.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
> * Az Azure Portalon létrehozott [QnA Maker-erőforrás.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) Ne feledje, hogy az Azure Active Directory-azonosító, előfizetés, QnA erőforrás nevét, amikor létrehozta az erőforrást.

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

1. Jelentkezzen be a [QnAMaker.ai](https://QnAMaker.ai) portálra az Azure-hitelesítő adatokkal.

1. A QnA Maker portálon válassza **a Tudásbázis létrehozása**lehetőséget.

1. A **Létrehozás** lapon ugorja ki **az 1.**

    Ha még nem hozta létre az **erőforrást, válassza a QnA szolgáltatás létrehozása lehetőséget.** A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Ne feledje, hogy az Azure Active Directory-azonosító, előfizetés, QnA erőforrás nevét, amikor létrehozta az erőforrást.

    Ha végzett az erőforrás létrehozásával az Azure Portalon, térjen vissza a QnA Maker portálra, frissítse a böngészőlapot, és folytassa a **2.**

1. **Lépésben**válassza ki az Active directoryt, az előfizetést, a szolgáltatást (erőforrást) és a szolgáltatásban létrehozott összes tudásbázis nyelvét.

   ![Képernyőkép a QnA Maker szolgáltatás tudásbázisának kiválasztásáról](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. **Lépésben**nevezze meg tudásbázisát. `My Sample QnA KB`

1. **A 4.**

    |Beállítás|Érték|
    |--|--|
    |**Többfordulatos kinyerés engedélyezése URL-címekből, .pdf vagy .docx fájlokból.**|Bejelölve|
    |**Alapértelmezett válaszszöveg**| `Quickstart - default answer not found.`|
    |**+ URL hozzáadása**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Válassza a **Professional** lehetőséget|

1. **Az 5.** **Create your KB**

    A kivonási folyamat néhány percet vesz igénybe a dokumentum elolvasásához és a kérdések és válaszok azonosításához.

    Miután a QnA Maker sikeresen létrehozza a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen az oldalon módosíthatja a tudásbázis tartalmát.

## <a name="edit-knowledge-base"></a>Tudásbázis szerkesztése

1.  A felső navigációs sávon válassza a **Saját tudásbázis lehetőséget.**

       Az önnel létrehozott vagy megosztott összes szolgáltatást az **utolsó módosítás** dátumának csökkenő sorrendjében láthatja.

       ![Saját tudásbázisok](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Válasszon ki egy adott tudásbázist a szerkesztéshez.

1.  Válassza a **Beállítások lehetőséget.** Az alábbi lista módosítható mezőket tartalmaz.

       |Cél|Műveletek|
       |--|--|
       |URL hozzáadása|Új URL-címeket adhat hozzá, hogy új GYIK-tartalmat adjon hozzá a tudásbázishoz a **Tudásbázis kezelése -> "+ ADD URL"** hivatkozásra kattintva.|
       |URL törlése|A meglévő URL-eket a törlés ikon, a kuka kiválasztásával törölheti.|
       |Tartalom frissítése|Ha azt szeretné, hogy a tudásbázis feltérképezje a meglévő URL-címek legújabb tartalmát, jelölje be a **Frissítés** jelölőnégyzetet. Ez egyszer frissíti a tudásbázist a legújabb URL-tartalommal. Ez nem a frissítések rendszeres ütemezését jelenti.|
       |Fájl hozzáadása|A támogatott fájldokumentumot hozzáadhatja a tudásbázis hoz, ha a Tudásbázis kezelése lehetőséget **választja,** majd a **+ Fájl hozzáadása lehetőséget** választja.|
    |Importálás|Bármely meglévő tudásbázist importálhatja a **Tudásbázis importálása** gombra kattintva. |
    |Frissítés|A tudásbázis frissítése a tudásbázishoz társított QnA Maker szolgáltatás létrehozása során használt **felügyeleti tarifacsomagtól** függ. Szükség esetén frissítheti a felügyeleti szintet az Azure Portalról is.

  1. Miután végzett a tudásbázis módosításával, válassza a **Mentés és betanítás** lehetőséget a lap jobb felső sarkában a módosítások megőrzése érdekében.

       ![Mentés és vonat](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Ha elhagyja az oldalt, mielőtt a **Mentés és betanítás**lehetőséget választja, minden módosítás elvész.



## <a name="manage-large-knowledge-bases"></a>Nagy tudásbázisok kezelése

* **Adatforráscsoportok**: A qna-k az adatforrás szerint vannak csoportosítva, amelyből kinyerték őket. Az adatforrás kibontható vagy összecsukható.

    ![Adatforrásokkal kapcsolatos kérdések és válaszok összecsukása és kibontása a QnA Maker adatforrássávjával](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Tudásbázis**keresése : A tudásbázisban a Tudásbázis tábla tetején található szövegmezőbe beírva kereshet. Kattintson az Enter gombra a kérdés-, válasz- vagy metaadat-tartalomkereséshez. A keresési szűrő eltávolításához kattintson az X ikonra.

    ![A kérdések és válaszok feletti QnA Maker keresőmező használatával a nézetet csak szűrési elemekre csökkentheti](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Tördelés:** Gyorsan mozoghat az adatforrások között a nagy tudásbázisok kezeléséhez

    ![Használja a QnA Maker tördelési funkciók at fenti kérdések és válaszok mozogni oldalak kérdések és válaszok](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Tudásbázisok törlése

A tudásbázis (KB) törlése állandó művelet. Nem lehet visszacsinálni. A tudásbázis törlése előtt exportálja a tudásbázist a QnA Maker portál **Beállítások** lapjáról.

Ha megosztja tudásbázisát a közreműködőkkel,](együttműködés-tudás-base.md), majd törli, mindenki elveszíti a hozzáférést a tudásbázishoz.

## <a name="next-steps"></a>További lépések

További információ az erőforrások összes tudásbázisának [nyelvének kezeléséről.](language-knowledge-base.md)

* QnA-párok szerkesztése
* A QnA Maker által használt Azure-erőforrások kezelése