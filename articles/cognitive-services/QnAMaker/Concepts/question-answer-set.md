---
title: Tudásbázis tervezése – QnA Maker
description: A QnA Maker Tudásbázis egy kérdés-válasz típusú (QnA) készletből és az egyes QnA-párokhoz tartozó opcionális metaadatokból áll.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756789"
---
# <a name="question-and-answer-set-concepts"></a>Kérdések és válaszok beállítása – fogalmak

A Tudásbázis a kérdések és válaszok (QnA) készletből áll.  Mindegyik készlet egyetlen választ tartalmaz, és egy készlet tartalmazza az adott _válaszhoz_tartozó összes információt. A válasz egy adatbázis-sorra vagy egy adatstruktúra-példányra is lazán hasonlíthat.

## <a name="question-and-answer-sets"></a>Kérdés- és válaszkészletek

A kérdés-válasz (QnA) készlet **kötelező** beállításai a következők:

* a **felhasználói lekérdezésnek a QnA Maker** gépi tanuláshoz használt szövege, amely a felhasználó kérdéséhez igazodik a különböző szóhasználattal, de ugyanaz a válasz
* a **Válasz** – a készlet válasza az a válasz, amelyet a rendszer akkor ad vissza, amikor egy felhasználói lekérdezés megfelel a kapcsolódó kérdésnek

Minden készletet egy **azonosító**képvisel.

A készlet **választható** beállításai a következők:

* **A kérdés alternatív formái** – ez segít QnA Maker a megfelelő választ visszaadni a kérdéses szövegezések széles körére
* **Metaadatok**: a metaadatok egy QnA-párral vannak társítva, és kulcs-érték párokként jelennek meg. A metaadatok címkéi a QnA párok szűrésére és a lekérdezési egyeztetést végző készlet korlátozására használhatók.
* Többfordulatú **kérések**, amelyek a több fordulatos beszélgetés folytatására szolgálnak

![QnA Maker tudásbázisok](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Szerkesztői Hozzáadás a tudásbázishoz

Ha nem rendelkezik már meglévő tartalommal a Tudásbázis feltöltéséhez, QnA-párokat adhat hozzá a QnA Maker portálon. Megtudhatja, hogyan frissítheti a tudásbázist [itt](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>A Tudásbázis helyi szerkesztése

A Tudásbázis létrehozása után javasolt a [QnA Maker-portálon](https://qnamaker.ai)lévő Tudásbázis szövegének szerkesztése a helyi fájlok exportálása és újraimportálása helyett. Előfordulhat azonban, hogy a tudásbázist helyileg kell szerkesztenie.

Exportálja a tudásbázist a **Beállítások** lapról, majd szerkessze a tudásbázist a Microsoft Excelben. Ha egy másik alkalmazás használatával szerkeszti az exportált fájlt, előfordulhat, hogy az alkalmazás szintaktikai hibákat tartalmaz, mert az nem teljes TSV-kompatibilis. A Microsoft Excel TSV-fájljai általában nem vezetnek be formázási hibákat.

Miután végzett a szerkesztéssel, importálja újra a TSV-fájlt a **Beállítások** lapról. Ez teljes mértékben lecseréli az aktuális tudásbázist az importált tudásbázisba.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis életciklusa QnA Maker](./development-lifecycle-knowledge-base.md)