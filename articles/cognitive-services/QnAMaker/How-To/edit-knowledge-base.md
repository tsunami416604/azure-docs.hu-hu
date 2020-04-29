---
title: Tudásbázis szerkesztése – QnA Maker
description: A QnA Maker lehetővé teszi, hogy egy könnyen használható szerkesztési élményt biztosítson a Tudásbázis tartalmának kezeléséhez.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756721"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>QnA-párok szerkesztése a Tudásbázisban

A QnA Maker lehetővé teszi, hogy egy könnyen használható szerkesztési élményt biztosítson a Tudásbázis tartalmának kezeléséhez.

A QnA párokat egy adatforrásból, például egy fájlból vagy URL-címről, vagy szerkesztői forrásként kell hozzáadni. A szerkesztői forrás azt jelzi, hogy a QnA pár kézzel lett hozzáadva a QnA-portálhoz. Az összes QnA-pár szerkesztésre elérhető.

## <a name="add-an-editorial-qna-pair"></a>Szerkesztői QnA pár hozzáadása
1. Jelentkezzen be a [QnA-portálra](https://www.qnamaker.ai/), majd válassza ki azt a tudásbázist, amelyhez hozzá szeretné adni a QnA-párosítást.
1. A Tudásbázis **Szerkesztés** lapján válassza a **QnA-pár hozzáadása** lehetőséget egy új QnA-pár hozzáadásához.

1. Az új QnA-pár sorban adja hozzá a szükséges **kérdések** és **válaszok** mezőket. A többi mező megadása nem kötelező. Az összes mező bármikor módosítható.

1. Szükség esetén **alternatív megfogalmazást**is hozzáadhat. Az alternatív megfogalmazás a kérdés bármilyen formája, amely jelentősen eltér az eredeti kérdéstől, de ugyanazt a választ kell biztosítania.

    Ha a Tudásbázis közzé van téve, és az [aktív tanulás](use-active-learning.md) be van kapcsolva, a QnA Maker összegyűjti az Ön által elfogadott alternatív megfogalmazás-beállításokat. Ezek a beállítások az előrejelzés pontosságának növeléséhez vannak kiválasztva.

1. Szükség esetén **metaadatokat**is hozzáadhat. A metaadatok megtekintéséhez válassza a helyi menü **beállítások megtekintése** elemét. A metaadatok olyan szűrőket biztosítanak a válaszokhoz, amelyeket az ügyfélalkalmazás, például egy csevegési robot biztosít.

1. Igény szerint hozzáadhat **követő utasításokat**is. A követő kérések további beszélgetési útvonalakat biztosítanak az ügyfélalkalmazás számára a felhasználónak való megjelenítéshez.

1. Válassza a **Mentés és a betanítás** lehetőséget az új QnA-párokat tartalmazó előrejelzések megjelenítéséhez.

## <a name="edit-a-qna-pair"></a>QnA pár szerkesztése

Bármely QnA pár mező szerkeszthető, az eredeti adatforrástól függetlenül. Előfordulhat, hogy egyes mezők nem láthatók a környezeti eszköztáron található aktuális **nézet** beállításai miatt.

## <a name="delete-a-qna-pair"></a>QnA pár törlése

A QnA törléséhez kattintson a QnA sor jobb szélén található **Törlés** ikonra. Ez egy állandó művelet. Nem vonható vissza. A készletek törlése előtt érdemes lehet a TUDÁSBÁZISt a **közzétételi** lapról exportálni.

![QnA pár törlése](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>A QnA pár AZONOSÍTÓjának megkeresése

Ha meg kell találnia a QnA pár AZONOSÍTÓját, két helyen is megtalálhatja:

* Vigye a kurzort a Delete (Törlés) ikonra a QnA pár sorában, amelyre kíváncsi. A hover szövege magában foglalja a QnA párosítási AZONOSÍTÓját.
* Exportálja a tudásbázist. A Tudásbázisban minden QnA pár tartalmazza a QnA-párt AZONOSÍTÓját.

## <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Alternatív kérdéseket adhat hozzá egy meglévő QnA-párosításhoz, így növelheti annak valószínűségét, hogy a felhasználó lekérdezése megegyezzen.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA-párok összekapcsolása

A QnA párok összekapcsolása a [követő kérésekkel](multiturn-conversation.md)történik. Ez egy logikai kapcsolat a QnA-párok között, a Tudásbázis szintjén felügyelve. A következő utasításokat szerkesztheti a QnA Maker-portálon.

A válasz metaadataiban nem lehet összekapcsolni a QnA párokat.

## <a name="add-metadata"></a>Metaadatok hozzáadása

Adja hozzá a metaadatokat úgy, hogy először kiválasztja a **megtekintési beállításokat**, majd kiválasztja a **metaadatok megjelenítése**lehetőséget. Ekkor megjelenik a metaadatok oszlop. Ezután válassza ki a **+** jelet a metaadat-készlet hozzáadásához. Ez a készlet egy kulcsból és egy értékből áll.

## <a name="save-changes-to-the-qna-pairs"></a>QnA-párok módosításainak mentése

A módosítások elvesztésének elkerülése érdekében időnként válassza a **Mentés és a betanítás** lehetőséget.

![Metaadatok hozzáadása](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Együttműködés a tudásbázison](./collaborate-knowledge-base.md)

* [Az QnA Maker által használt Azure-erőforrások kezelése](set-up-qnamaker-service-azure.md)