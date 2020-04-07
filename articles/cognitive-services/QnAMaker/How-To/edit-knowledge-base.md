---
title: Tudásbázis szerkesztése - QnA Maker
description: A QnA Maker lehetővé teszi a tudásbázis tartalmának kezelését egy könnyen használható szerkesztési élmény biztosításával.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756721"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>QnA-párok szerkesztése a tudásbázisban

A QnA Maker lehetővé teszi a tudásbázis tartalmának kezelését egy könnyen használható szerkesztési élmény biztosításával.

A QnA-párok adatforrásból, például fájlból vagy URL-címből kerülnek hozzáadásra, vagy szerkesztői forrásként kerülnek hozzáadásra. Egy szerkesztői forrás azt jelzi, hogy a QnA-párt manuálisan adták hozzá a QnA portálhoz. Minden QnA pár szerkeszthető.

## <a name="add-an-editorial-qna-pair"></a>Szerkesztői QnA-pár hozzáadása
1. Jelentkezzen be a [QnA portálra,](https://www.qnamaker.ai/)majd válassza ki azt a tudásbázist, amelyhez hozzá szeretné adni a QnA-párt.
1. A tudásbázis **EDIT** lapján válassza a **QnA-pár hozzáadása** lehetőséget egy új QnA-pár hozzáadásához.

1. Az új QnA pársorban adja hozzá a szükséges **Kérdések** és **válasz** mezőket. A többi mező nem kötelező. Minden mező bármikor módosítható.

1. Ha tetszés szerint **alternatív kifejezéseket**is hozzáadhat. Az alternatív megfogalmazás a kérdés bármely formája, amely jelentősen eltér az eredeti kérdéstől, de ugyanazt a választ kell adnia.

    Amikor a tudásbázis tava van, és be van kapcsolva az [aktív tanulás,](use-active-learning.md) a QnA Maker alternatív kifejezéseket gyűjt, amelyeket el kell fogadnia. Ezek a választási lehetőségek az előrejelzés pontosságának növelése érdekében vannak kiválasztva.

1. Tetszés szerint adja hozzá **a metaadatokat**. A metaadatok megtekintéséhez válassza a helyi menü **Nézet beállításai parancsát.** A metaadatok szűrőket adnak az ügyfélalkalmazás válaszaihoz, például egy csevegőrobothoz.

1. Szükség esetén adja hozzá a **nyomon követési utasításokat.** A nyomon követési utasítások további beszélgetési útvonalakat biztosítanak az ügyfélalkalmazásszámára, amelyet a felhasználó rendelkezésére bocsát.

1. Válassza a **Mentés és betanítás** lehetőséget az előrejelzések megtekintéséhez, beleértve az új QnA-párt is.

## <a name="edit-a-qna-pair"></a>QnA-pár szerkesztése

Bármely QnA-pár bármely mezője szerkeszthető, függetlenül az eredeti adatforrástól. Előfordulhat, hogy egyes mezők nem láthatók a környezeti eszköztáron található jelenlegi **nézetbeállítások** miatt.

## <a name="delete-a-qna-pair"></a>QnA-pár törlése

QnA törléséhez kattintson a **QnA** sor jobb szélén található törlés ikonra. Ez egy állandó művelet. Nem lehet visszacsinálni. A készletek törlése előtt exportálja a tudásbázist a **Közzététel** lapról.

![QnA-pár törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>A QnA-pár azonosítójának megkeresése

Ha meg kell találnia a QnA-pár azonosítót, két helyen megtalálhatja:

* Mutass on the delete icon on the QnA pair sorban you are interested in. A rámutatás szövege tartalmazza a QnA-pár azonosítót.
* Exportálja a tudásbázist. A tudásbázis minden QnA-párja tartalmazza a QnA-pár azonosítót.

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdések hozzáadása egy meglévő QnA-párhoz a felhasználói lekérdezések egyezésének valószínűségének növelése érdekében.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA-párok összekapcsolása

A QnA-párok összekapcsolása nyomon követési utasításokat is [mellékel.](multiturn-conversation.md) Ez egy logikai kapcsolat a Tudásbázis szintjén kezelt QnA-párok között. A nyomon követési utasításokat a QnA Maker portálon szerkesztheti.

A qna-párok nem kapcsolhatók össze a válasz metaadataiban.

## <a name="add-metadata"></a>Metaadatok hozzáadása

Metaadat-halmazok hozzáadásához először a **Nézet beállítások**, majd a Metaadatok megjelenítése lehetőséget választva adja hozzá a **metaadat-készleteket.** Ez megjeleníti a metaadat oszlopot. Ezután jelölje **+** ki a jelet egy metaadatkészlet hozzáadásához. Ez a készlet egy kulcsból és egy értékből áll.

## <a name="save-changes-to-the-qna-pairs"></a>A QnA-párok módosításának mentése

A módosítások elvesztésének elkerülése érdekében a módosítások elkerülése érdekében a Módosítások **és a Betanítás** lehetőséget rendszeresen válassza a Mentés és betanítás gombra.

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Együttműködés a tudásbázison](./collaborate-knowledge-base.md)

* [A QnA Maker által használt Azure-erőforrások kezelése](set-up-qnamaker-service-azure.md)