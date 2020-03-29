---
title: Tervezési tudásbázis - QnA Maker
description: A QnA Maker tudásbázis a qna-párokhoz tartozó kérdés-válasz (QnA) készletekből és választható metaadatokból áll.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844318"
---
# <a name="question-and-answer-set-concepts"></a>A kérdések és kérdések halmazának fogalmai

A tudásbázis kérdések és válasz (QnA) készletekből áll.  Minden készletnek egy válasza van, és egy készlet tartalmazza a _válaszhoz_kapcsolódó összes információt. A válasz lazán hasonlíthat egy adatbázissorra vagy egy adatstruktúra-példányra.

## <a name="question-and-answer-sets"></a>Kérdés- és válaszkészletek

A kérdések és válasz (QnA) készletben **szükséges** beállítások a következők:

* egy **kérdés** - a felhasználói lekérdezés szövege, a QnA Maker gépi tanulásához, a felhasználó kérdésének szövegéhez való igazítás különböző megfogalmazással, de ugyanaz a válasz
* a **válasz** - a készlet válasza az a válasz, amelyakkor ad vissza, amikor egy felhasználói lekérdezés tegyezik meg a kapcsolódó kérdéssel

Minden készletet egy **azonosító**jelöl.

A **készlet választható** beállításai a következők:

* **Alternatív formái a kérdés** - ez segít QnA Maker vissza a helyes választ a szélesebb körű kérdés kifejezések
* **Metaadatok**: A metaadatok egy QnA-párhoz társított címkék, amelyek kulcsérték-párokként jelennek meg. A metaadat-címkék a QnA-párok szűrésére szolgálnak, és korlátozzák azt a készletet, amely en a lekérdezésegyeztetést végzik.
* **Többfordulatos rákérdezések**, amelyek a többfordulatos beszélgetés folytatására szolgálnak

![QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Szerkesztői módon hozzá tudásbázis

Ha nem rendelkezik már meglévő tartalommal a tudásbázis feltöltéséhez, hozzáadhat QnA-készleteket szerkesztői módon a QnA Maker portálon. Itt megtudhatja, hogyan [here](../How-To/edit-knowledge-base.md)frissítheti tudásbázisát.

## <a name="editing-your-knowledge-base-locally"></a>A tudásbázis helyi szerkesztése

A tudásbázis létrehozása után a tudásbázis szövegét a [QnA Maker portálon](https://qnamaker.ai)ajánlott a tudásbázis szövegének szerkesztése, nem pedig a helyi fájlokon keresztüli exportálás és újraimportálás. Előfordulhat azonban, hogy helyileg kell szerkeszteni egy tudásbázist.

Exportálja a **tudásbázist** a Beállítások lapról, majd szerkesztse a tudásbázist a Microsoft Excel programmal. Ha úgy dönt, hogy egy másik alkalmazást használ az exportált fájl szerkesztéséhez, az alkalmazás szintaktikai hibákat okozhat, mert nem teljesen kompatibilis a TSV-vel. A Microsoft Excel TSV-fájljai általában nem okoznak formázási hibákat.

Miután végzett a szerkesztésekkel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljesen felváltja az aktuális tudásbázist az importált tudásbázissal.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis életciklusa a QnA Maker programban](./development-lifecycle-knowledge-base.md)