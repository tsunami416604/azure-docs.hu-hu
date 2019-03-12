---
title: Hogyan hozzon létre egy projektet? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Projekt létrehozása az egyéni a fordítót hogyan?
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 2e49a8c40396b03c231fa7fd484331b0e28d4cf6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762704"
---
# <a name="create-a-project"></a>Projekt létrehozása

Egy projektet egy tároló-modellek, dokumentumok, és teszteli. Minden projekt automatikusan összes dokumentumot, amely a rendszer feltölti a munkaterülethez, amelyek rendelkeznek a megfelelő nyelvű pár tartalmaz.

Projekt létrehozása akkor jön létre a modell első lépését.

## <a name="create-a-project"></a>Hozzon létre egy projektet:

1.  Az a [egyéni a fordítót](https://portal.customtranslator.azure.ai) portál, kattintson a létrehozás projektre.

    ![Projekt létrehozása](media/how-to/how-to-create-project.png)

2.  A párbeszédpanelen adja meg a projektet a következő információkat:

    a.  Project name (required): Olyan egyedi, jelentéssel bíró nevet adjon a projekthez. Nem kell említik a nyelveket a cím belül.

    b.  Leírás: A projekt rövid összefoglalása. Ez a leírás nincs befolyása az egyéni a fordítót vagy az eredményül kapott egyéni rendszer viselkedésének, de segíthet megkülönböztetni a különböző projektek.

    c.  Nyelvi pár (kötelező): Válassza ki a, hogy Ön fordítása és közül.

    d.  Kategória (kötelező): Válassza ki a projektnek leginkább megfelelő kategóriát. A kategória terminológia és a dokumentumokat szeretne lefordítani stílusát ismerteti.

    e.  Kategória leírása: Ez a mező segítségével jobban ismertetik az adott mező vagy iparági dolgozik, amelyben. Például, ha a kategória orvoslás, akkor előfordulhat, hogy egy adott dokumentum, ilyen műtét vagy hozzáadása pediatrics. A leírás nem befolyásolják a korábbinál egyéni a fordítót vagy az eredményül kapott egyéni rendszer rendelkezik.

    f.  Projekt címkét: A [projekt címke](workspace-and-project.md#project-labels) különbséget tesz a ugyanarra a nyelv két és kategória a projektek között. Ajánlott eljárásként használja a címke *csak* Ha tervezi az azonos nyelv párokból és azonos kategória több projektet és szeretnének hozzáférni e egy másik CategoryID a projektek. Ne használja ezt a mezőt, csak egy kategória rendszerek létrehozásakor. Egy projekt címke nincs kötelező és nem hasznos nyelvi párok között. A címkét több projektet is használhat.

    ![Projekt párbeszédpanel létrehozása](media/how-to/how-to-create-project-dialog.png)

3.  Kattintson a Létrehozás gombra

## <a name="view-project-details"></a>Projekt részleteinek megtekintése

Az egyéni a fordítót kezdőlapja a munkaterületen jeleníti meg az első 10 projektek. A projekt nevét, nyelvi pár, kategória, állapota és BLEU pontszámot jeleníti meg.

Miután egy projektet, az alábbiakat fogja látni a lapot:

- CategoryID: Egy CategoryID elkülönített változó összefűzésével előállítjuk a munkaterület azonosítója, a projekt címke és a kód jön létre. A Translator Text API a CategoryID használata egyéni fordítások beolvasásához.

- Train gombra: Ez a gomb segítségével indítsa el a [a modell tanítása](how-to-train-model.md).

- Adja hozzá a dokumentumok gombra: Ezzel a gombbal való [dokumentumok feltöltése](how-to-upload-document.md).

- Szűrő dokumentumok gombra: A szűrő és keressen az adott dokumentum gomb használatával.

    ![Projekt részleteinek megtekintése](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, [keresés, Szerkesztés, projekt törlése hogyan](how-to-search-edit-delete-projects.md).
- Ismerje meg, [dokumentum feltöltése](how-to-upload-document.md) fordítási modelleket hozhat létre.
