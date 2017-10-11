---
title: "Hibák - Azure Logic Apps diagnosztizálása |} Microsoft Docs"
description: "Ha nem működnek a logic apps megismerése gyakori módjai"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 814e6f93088cdd96b0a663d2a7494b5a11470d99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="diagnose-logic-app-failures"></a>Logic app hibák diagnosztizálása
A a logic apps a problémákat vagy hibákat tapasztal, van néhány módszer segítségével könnyebben áttekinthető, ahol a hibák érkező.  

## <a name="azure-portal-tools"></a>Az Azure portál eszközök
Az Azure-portálon az egyes logikai alkalmazás lépésről lépésre diagnosztizálása érdekében számos eszközt kínál.

### <a name="trigger-history"></a>Eseményindító előzmények

Minden logikai alkalmazás legalább egy eseményindító tartozik. Ha azt észleli, hogy alkalmazásokat nem kiváltó, tanulmányozni, az eseményindító előzmények további információt. Az eseményindító előzmények logika app'ss fő panelje érheti el.

![Az eseményindító előzmények keresése][1]

Az eseményindító előzmények összes eseményindító kísérletet a Logic Apps alkalmazást sorolja fel. Kattinthat, és részletesen, kifejezetten eseményindító megkísérel, a bemeneti adatok vagy kimenettel, amelyek a eseményindító kísérlet jön létre. Ha sikertelen eseményindítók, jelölje ki a eseményindító kísérletet, és válassza a **kimenetek** átnézni mutató hivatkozás létrehozott hibaüzeneteket, például a FTP hitelesítő adatokat, amelyek nem érvényes.

Láthatja a különböző állapotok az alábbiak:

* **Kihagyott**. A végpont lett lekérdezett adatok kereséséhez, és hogy adatot nem volt elérhető választ kapott.
* **Sikeres**. Az eseményindító, hogy az adatok volt elérhető választ kapott. Ez az állapot manuális eseményindítót, ismétlődési eseményindító vagy egy lekérdezési eseményindító okozhatja. Ez az állapot általában csatolni a **Fired** állapotát, de előfordulhat, ha rendelkezik olyan feltétel vagy SplitOn parancsot a kód nézetre, amely nem volt megfelelő.
* **Nem sikerült**. Egy hiba történt.

#### <a name="start-a-trigger-manually"></a>Indítsa el manuálisan a eseményindító

A logikai alkalmazást egy rendelkezésre álló eseményindító azonnal vár a következő ismétlődés nélkül kereséséhez, kattintson a **válasszon eseményindító** a fő panelen a ellenőrzés kényszerítése. Például az Dropbox eseményindító a hivatkozásra kattintva okoz a munkafolyamat azonnali lekérdezésére Dropbox új fájlokat.

### <a name="run-history"></a>futtatási előzményei

Minden égetett eseményindító futását eredményezi. Futtatási információkat tartalmaz, amelyek alapján megtudhatja, mi történt a munkafolyamat során számos részletet fő paneljén érheti el.

![A futtatási előzményei keresése][2]

Futtatás a következő állapotok egyikét jeleníti meg:

* **Sikeres**. Minden művelet sikeresen befejeződött. Hiba történt, ha a hiba látta, amely később a munkafolyamatban történt művelet. Ez azt jelenti, hogy a hiba látta el egy műveletet, amely után a sikertelen művelet lett beállítva.
* **Nem sikerült**. Legalább egy műveletet, amely később a munkafolyamatban művelet nem kezelt hiba volt.
* **Megszakítva**. A munkafolyamat működő állapotban volt, de a megszakítási kérelmet kapott.
* **Futó**. A munkafolyamat jelenleg fut. Ez az állapot akkor fordulhat elő, a szabályozottan halmozott munkafolyamatok, vagy a jelenlegi tarifacsomag miatt. További információkért lásd: [művelet vonatkozó korlátozások a tarifákat tartalmazó oldalt](https://azure.microsoft.com/pricing/details/app-service/plans/). (A diagramok futtatási előzményeit alatt megjelenő) diagnosztika konfigurálása is is információt nyújt a késleltetési eseményeket.

Ha éppen megtekintett futtatási előzményei, megtekintheti további részletekért.  

#### <a name="trigger-outputs"></a>Eseményindító kimenete

Eseményindító kimenetének megjelenítése az adatokat, amely innen származik: az eseményindító. A kimenetek segítségével meghatározhatja, hogy az összes tulajdonság adott vissza a várt módon.

> [!NOTE]
> Ha megjelenik a telepített tartalmak nem világos, ismerje meg az Azure Logic Apps [kezeli a különböző típusú tartalmakat](../logic-apps/logic-apps-content-type.md).
> 

![Eseményindító kimeneti példák][3]

#### <a name="action-inputs-and-outputs"></a>A művelet bemenetekhez és kimenetekhez

A be- és kimenetekkel, művelet fogadó részletezhető. Ezek az adatok akkor hasznos, méretének és a kimenetek alakját megértéséhez, valamint a létrehozott előfordulhat, hogy hibaüzeneteket kereséséhez.

![A művelet bemenetekhez és kimenetekhez][4]

## <a name="debug-workflow-runtime"></a>A munkafolyamat futásidejű hibakeresése

A bemenetek, kimenetek és eseményindítók futtató a figyelés, valamint néhány lépést sikerült hozzá a munkafolyamathoz, amelyek segítenek a hibakeresés. 
[RequestBin](http://requestb.in) hatékony eszköz, amely a munkafolyamat lépésben adhat hozzá. RequestBin használatával állíthat be egy HTTP-kérelem inspector a pontos méretét, alakzat és HTTP-kérések formátum meghatározása. Hozzon létre egy RequestBin, és illessze be az URL-cím a logikai alkalmazás HTTP POST műveletet. a szervezet szeretné tesztelni, például tartalom, kifejezés vagy egy másik lépés kimeneti. Futtatása után a logikai alkalmazást hogy hogyan jött létre a kérelmet a Logic Apps motor előállítja a RequestBin is frissítheti.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png
