---
title: Tartalom-ellenőrzések használata a Véleményezés eszköz segítségével – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a Felülvizsgálati eszköz hogyan teszi lehetővé az emberi moderátorok számára a képek áttekintését egy internetes portálon.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044116"
---
# <a name="create-human-reviews"></a>Emberi vélemények létrehozása

Ebből az útmutatóból megtudhatja, hogyan állíthatja be az [értékeléseket](../review-api.md#reviews) a Véleményezés eszköz webhelyén. Vélemények tárolja és megjeleníti a tartalmat az emberi moderátorok, hogy értékelje. A moderátorok módosíthatják az alkalmazott címkéket, és szükség szerint alkalmazhatják saját egyéni címkéiket. Amikor a felhasználó befejezi az áttekintést, az eredményeket a rendszer elküldi egy megadott visszahívási végpontnak, és eltávolítja a tartalmat a webhelyről.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be vagy hozzon létre fiókot a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.

## <a name="image-reviews"></a>Képekre vonatkozó vélemények

1. Nyissa meg a [Véleményezés eszközt,](https://contentmoderator.cognitive.microsoft.com/)válassza a **Kipróbálás** lapot, és töltsön fel néhány képet, amelyeket át szeretne tekinteni.
1. Miután a feltöltött képek feldolgozása befejeződött, lépjen a **Véleményezés** lapra, és válassza a **Kép**lehetőséget.

    ![Chrome böngésző, amely a felülvizsgálati eszközt mutatja a Kép áttekintése lehetőséggel kiemelve](images/review-images-1.png)

    A képek az automatikus moderálási folyamat által hozzárendelt címkékkel jelennek meg. A Véleményezés eszköz segítségével elküldött képek nem láthatók a többi véleményező számára.

1. A Vélemények mozgatása a csúszka (1) **megjelenítéséhez** módosíthatja a képernyőn megjelenő képek számát. Kattintson a **címkézett** vagy **címkézetlen** gombokra (2) a képek megfelelő rendezéséhez. Kattintson a címke panel (3) váltani, hogy be-vagy kikapcsolása.

    ![Chrome böngésző, amely a Véleményezés eszközt jeleníti meg címkézett képekkel véleményezésre](images/review-images-2.png)

1. Ha további információt szeretne látni egy képről, kattintson a miniatűrben a három pontra, és válassza a **Részletek megtekintése lehetőséget.** A kép hez az **Áthelyezés** beállítással rendelhet egy képet (az alcsapatokról további információért tekintse meg a [csapatok](./configure.md#manage-team-and-subteams) szakaszt).

    ![Kiemelt: A Részletek megtekintése beállítás kiemelve](images/review-images-3.png)

1. Böngésszen a képmoderálási információk között a részletek oldalon.

    ![Külön ablaktáblában felsorolt moderálási részletekkel rendelkező kép](images/review-images-4.png)

1. Miután szükség szerint áttekintette és frissítette a címkehozzárendeléseket, kattintson a **Tovább** gombra az értékelések elküldéséhez. Miután elküldte, körülbelül öt másodperce van, hogy kattintson a **Prev** gombra, hogy visszatérjen az előző képernyőre, és újra áttekinti a képeket. Ezt követően a képek már nem szerepelnek a Küldés várólistában, és a **Prev** gomb már nem érhető el.

## <a name="text-reviews"></a>Szövegekre vonatkozó vélemények

A szöveges értékelések a képértékelésekhez hasonlóan működnek. A tartalom feltöltése helyett egyszerűen írhat vagy illesztheti be a szöveget (legfeljebb 1024 karakter). Ezután a Tartalommoderátor elemzi a szöveget, és címkéket alkalmaz (az egyéb moderálási információkon, például a káromkodáson és a személyes adatokon kívül). A szöveges ellenőrzésekben az alkalmazott címkék et és/vagy egyéni címkéket alkalmazhat az értékelés elküldése előtt.

![Képernyőkép a Chrome böngészőablakában megjelölt szöveget megjelenítő ellenőrző eszközről](../images/reviewresults_text.png)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan állíthatja be és használhatja az értékeléseket a [Tartalommoderátor-ellenőrző eszközből.](https://contentmoderator.cognitive.microsoft.com) Ezután tekintse meg a [REST API-útmutatót](../try-review-api-review.md) vagy a [.NET SDK rövid útmutatót,](../dotnet-sdk-quickstart.md) amelyből megtudhatja, hogyan hozhat létre értékeléseket programozott módon.