---
title: Tudásbázis szerkesztése - QnA Maker
description: A QnA Maker lehetővé teszi a tudásbázis tartalmának kezelését egy könnyen használható szerkesztési élmény biztosításával.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131657"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>QnA-készletek szerkesztése a tudásbázisban

A QnA Maker lehetővé teszi a tudásbázis tartalmának kezelését egy könnyen használható szerkesztési élmény biztosításával.

A QnA-készletek adatforrásból, például fájlból vagy URL-címből kerülnek hozzáadásra, vagy szerkesztői forrásként kerülnek hozzáadásra. Egy szerkesztői forrás azt jelzi, hogy a QnA-készlet manuálisan lett hozzáadva a QnA portálhoz. Minden QnA készlet szerkeszthető.

## <a name="add-an-editorial-qna-set"></a>Szerkesztői QnA-készlet hozzáadása
1. Jelentkezzen be a [QnA portálra,](https://www.qnamaker.ai/)majd válassza ki azt a tudásbázist, amelyhez hozzá szeretné adni a QnA-készletet.
1. A tudásbázis **EDIT** lapján válassza a **QnA hozzáadása készlet** lehetőséget egy új QnA-készlet hozzáadásához.

1. Az új QnA-készlet sorában adja hozzá a szükséges **Kérdések** és **válasz** mezőket. A többi mező nem kötelező. Minden mező bármikor módosítható.

1. Ha tetszés szerint **alternatív kifejezéseket**is hozzáadhat. Az alternatív megfogalmazás a kérdés bármely formája, amely jelentősen eltér az eredeti kérdéstől, de ugyanazt a választ kell adnia.

    Amikor a tudásbázis tava van, és be van kapcsolva az aktív tanulás, a QnA Maker alternatív kifejezéseket gyűjt, amelyeket el kell fogadnia. Ezek a választási lehetőségek az előrejelzés pontosságának növelése érdekében vannak kiválasztva.

1. Tetszés szerint adja hozzá **a metaadatokat**. A metaadatok megtekintéséhez válassza a helyi menü **Nézet beállításai parancsát.** A metaadatok szűrőket adnak az ügyfélalkalmazás válaszaihoz, például egy csevegőrobothoz.

1. Szükség esetén adja hozzá a **nyomon követési utasításokat.** A nyomon követési utasítások további beszélgetési útvonalakat biztosítanak az ügyfélalkalmazásszámára, amelyet a felhasználó rendelkezésére bocsát.

1. Válassza a **Mentés és betanítás** lehetőséget az előrejelzések megtekintéséhez, beleértve az új QnA-készletet is.

## <a name="edit-a-qna-set"></a>QnA-készlet szerkesztése

Bármely QnA-készlet bármely mezője szerkeszthető, függetlenül az eredeti adatforrástól. Előfordulhat, hogy egyes mezők nem láthatók a környezeti eszköztáron található jelenlegi **nézetbeállítások** miatt.

## <a name="delete-a-qna-set"></a>QnA-készlet törlése

QnA törléséhez kattintson a **QnA** sor jobb szélén található törlés ikonra. Ez egy állandó művelet. Nem lehet visszacsinálni. A készletek törlése előtt exportálja a tudásbázist a **Közzététel** lapról.

![QnA-készlet törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>A QnA-készlet azonosítójának megkeresése

Ha meg kell találnia a QnA set ID-t, két helyen megtalálhatja:

* Mutasson a törlés ikonra az Önt érdeklő QnA-beállítássoron. A rámutatás szövege tartalmazza a QnA-készlet azonosítót.
* Exportálja a tudásbázist. A tudásbázisban beállított minden qna tartalmazza a QnA-készlet azonosítót.

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő QnA-készlethez a felhasználói lekérdezések egyezésének valószínűségének növelése érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>QnA-készletek összekapcsolása

A QnA-készletek összekapcsolása nyomon követési utasításokat is [mellékel.](multiturn-conversation.md) Ez egy logikai kapcsolat a tudásbázis szintjén kezelt QnA-készletek között. A nyomon követési utasításokat a QnA Maker portálon szerkesztheti.

A qna-készletek nem kapcsolhatók össze a válasz metaadataiban.

## <a name="add-metadata"></a>Metaadatok hozzáadása

Metaadat-halmazok hozzáadásához először a **Nézet beállítások**, majd a Metaadatok megjelenítése lehetőséget választva adja hozzá a **metaadat-készleteket.** Ez megjeleníti a metaadat oszlopot. Ezután jelölje **+** ki a jelet egy metaadatkészlet hozzáadásához. Ez a készlet egy kulcsból és egy értékből áll.

## <a name="save-changes-to-the-qna-sets"></a>A QnA-készletek módosításának mentése

A módosítások elvesztésének elkerülése érdekében a módosítások elkerülése érdekében a Módosítások **és a Betanítás** lehetőséget rendszeresen válassza a Mentés és betanítás gombra.

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Együttműködés a tudásbázison](./collaborate-knowledge-base.md)

* [A QnA Maker által használt Azure-erőforrások kezelése](set-up-qnamaker-service-azure.md)