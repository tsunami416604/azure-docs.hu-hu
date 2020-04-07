---
title: 'Rövid útmutató: Kérdések és válasz adása a QnA Maker portálon'
description: Ez a rövid útmutató bemutatja, hogyan adhat hozzá kérdés- és válaszkészleteket metaadatokkal, hogy a felhasználók megtalálják a megfelelő választ a kérdésükre.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f067bae55c38fc783c12bf9d0bc6fbcdf881e4e4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756683"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Rövid útmutató: Kérdések és válaszadás a QnA Maker portállal

A tudásbázis létrehozása után adja hozzá a kérdés-válasz (QnA) készletekmetaadatokkal a válasz szűréséhez. Az alábbi táblázatban szereplő kérdések az Azure-szolgáltatáskorlátokról szólnak, de mindegyiknek más Azure-szolgáltatással kell kapcsolatosak.

<a name="qna-table"></a>

|Beállítás|Kérdések|Válasz|Metaadatok|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Miután metaadatokat adott hozzá egy QnA-párhoz, az ügyfélalkalmazás:

* Olyan válaszok kérése, amelyek csak bizonyos metaadatoknak felelnek meg.
* Az egyes válaszok metaadataitól függően megkapja az összes választ, de a feldolgozás után.


## <a name="prerequisites"></a>Előfeltételek

* Az [előző rövid útmutató befejezése](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Bejelentkezés a QnA Maker portálra

1. Jelentkezzen be a [QnA Maker portálra.](https://www.qnamaker.ai)

1. Válassza ki a meglévő tudásbázist az [előző rövid útmutatóból.](../how-to/create-knowledge-base.md)

## <a name="add-additional-alternatively-phrased-questions"></a>További, alternatív kifejezésű kérdések hozzáadása

Az aktuális tudásbázis rendelkezik a QnA Maker hibaelhárítási QnA pár. Ezek a készletek akkor jöttek létre, amikor az URL-t hozzáadták a tudásbázishoz a létrehozási folyamat során.

Az URL-cím importálásakor csak egy kérdés jött létre egy válaszal. Ebben az eljárásban adjon hozzá további kérdéseket.

1. A **Szerkesztés** oldalon használja a kérdés- és válaszkészletek feletti keresőszöveget, hogy megtalálja a kérdést`How large a knowledge base can I create?`

1. A **Kérdés** oszlopban válassza a **+ Alternatív kifejezések hozzáadása lehetőséget,** majd adjon hozzá minden új megfogalmazást az alábbi táblázatban.

    |Alternatív megfogalmazás|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Válassza a **Mentés és betanítás** lehetőséget a tudásbázis újratanításához.

1. Válassza **a Teszt**lehetőséget, majd adjon meg egy kérdést, amely közel áll az új alternatív kifejezések egyikéhez, de nem pontosan ugyanaz a megfogalmazás:

    `What GB size can a knowledge base be?`

    A helyes választ markdown formátumban adja vissza:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Ha a visszaadott válasz alatt az Ellenőrzés lehetőséget **választja,** akkor több választ láthat a kérdésben, de nem ugyanolyan magas szintű bizalommal.

    Ne adjon hozzá minden lehetséges kombinációt az alternatív kifejezésekhez. Ha bekapcsolja a QnA Maker [aktív tanulását,](../how-to/improve-knowledge-base.md)ez megtalálja azokat az alternatív kifejezéseket, amelyek a legjobban segítik a tudásbázist a felhasználók igényeinek kielégítésében.

1. A tesztablak bezárásához válassza a **Teszt** ismét lehetőséget.

## <a name="add-metadata-to-filter-the-answers"></a>Metaadatok hozzáadása a válaszok szűréséhez

Metaadatok hozzáadása a kérdés-válasz készlet lehetővé teszi, hogy az ügyfélalkalmazás küldszűrt válaszokat. Ez a szűrő az első és a [második rangsoroló](../concepts/query-knowledge-base.md#ranker-process) alkalmazása előtt kerül alkalmazásra.

1. Adja hozzá a második kérdés-válasz készletet a metaadatok nélkül, a [rövid útmutató első táblájából,](#qna-table)majd folytassa a következő lépésekkel.

1. Válassza **a Nézet beállításai lehetőséget,** majd a **Metaadatok megjelenítése**lehetőséget.

1. Az imént hozzáadott QnA-páresetében válassza a **Metaadat-címkék hozzáadása**lehetőséget, majd adja hozzá a nevét `service` és `search`értékét. Úgy néz ki, mint ez: `service:search`.

1. Adjon hozzá egy másik metaadat-címkét a `link_in_answer` nevével és értékével. `false` Úgy néz ki, mint ez: `link_in_answer:false`.

1. Keresse meg az első választ `How large a knowledge base can I create?`a táblázatban, .

1. Metaadatpárok hozzáadása ugyanahhoz a két metaadat-címkéhez:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Most már két kérdése van ugyanazzal a metaadat-címkével, különböző értékekkel.

1. Válassza a **Mentés és betanítás** lehetőséget a tudásbázis újratanításához.

1. A közzétételi lap ra való ugráshoz válassza a legfelső menü **Közzététel** parancsát.
1. A **Közzététel gombra** kattintva tegye közzé az aktuális tudásbázist a végponton.
1. A tudásbázis közzététele után folytassa a következő rövid útmutatóval, és ismerje meg, hogyan hozhat létre választ a tudásbázisból.

## <a name="what-did-you-accomplish"></a>Mit ért el?

A tudásbázist úgy szerkesztette, hogy további kérdéseket támogasson, és név-/értékpárokat adott meg a szűrés támogatásához a legjobb válasz vagy utófeldolgozás keresése során, miután a választ vagy a válaszokat visszaadta.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a következő rövid útmutatót, törölje a QnA Maker és a Robot keretrendszer erőforrásait az Azure Portalon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Válasz kérése Postman vagy cURL használatával](get-answer-from-knowledge-base-using-url-tool.md)