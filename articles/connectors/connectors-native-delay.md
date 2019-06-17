---
title: Az Azure Logic Apps-munkafolyamatok – a következő művelet késleltetése
description: Várjon a következő művelet futtatása a logikai alkalmazások munkafolyamataiba a késleltetés vagy késleltetés amíg műveleteket az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297626"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Késleltetés a következő művelet futtatása az Azure Logic Appsben

A logikai alkalmazás mennyi idő a következő művelet futtatása előtt várjon rendelkezik, adja hozzá a beépített **késleltetés – ütemezése** művelet a logikai alkalmazás munkafolyamat-művelet előtt. Vagy adhat hozzá a beépített **késleltetés eddig – ütemezése** művelet megvárnia, amíg egy adott dátumot és időpontot a következő művelet futtatása előtt. A beépített ütemezési műveleteket és eseményindítókat kapcsolatos további információkért lásd: [ütemezés és Futtatás ismétlődő, automatizált, feladatok és az Azure Logic Apps munkafolyamat](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **késleltetés**: Várjon, amíg a megadott számú alkalommal egységek, mint a másodperc, perc, óra, nap, hét vagy hónapban, a következő művelet futtatása előtt.

* **Késleltetés eddig**: Várjon, amíg a megadott dátum és idő a következő művelet futtatása előtt.

Íme néhány példa módszer, ezek a műveletek használatára:

* Várjon, amíg az állapot frissítéséhez küldése e-mailben egy hét napja.

* Késleltetés a munkafolyamat, mielőtt folytatása, és az adatok beolvasása egy HTTP-hívás befejezéséig.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, akkor az [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* Alapvető ismeretek szerezhetők [a logic apps](../logic-apps/logic-apps-overview.md). Mielőtt használhatná egy műveletet, a logikai alkalmazás kell először egy trigger indít el. Használhat bármely trigger szeretne, majd adja hozzá más műveletek, a késleltetési műveletet hozzáadása előtt. Ez a témakör egy Office 365 Outlook-triggert használja. Ha most ismerkedik a logic apps, [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Adja hozzá a késleltetési műveletet

1. Válassza ki a Logic App Designerben kívánja hozzáadni, a késleltetési műveletet, feladatütemezésekben **új lépés**.

   Adja hozzá a késleltetési műveletet között lépéseket, vigye a mutatót a nyíl felett, amely kapcsolódik a lépéseket. Válassza a plusz jelre (+), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A keresőmezőbe írja be szűrőként "késleltetés". A műveletek listából válassza a következő műveletet: **Delay**

   !["Késleltetés" művelet hozzáadása](./media/connectors-native-delay/add-delay-action.png)

1. Adja meg, mennyi ideig várjon a következő művelet futtatása előtt.

   ![Adja meg a késleltetés ideje](./media/connectors-native-delay/delay-time-intervals.png)

   | Tulajdonság | JSON-név | Szükséges | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | Count | count | Igen | Egész szám | Késleltetési idő egységek száma |
   | Unit (Egység) | Egység | Igen | String | Az időegység, például: `Second`, `Minute`, `Hour`, `Day`, `Week`, vagy `Month` |
   ||||||

1. Adja hozzá a munkafolyamatot futtatni kívánt műveletek.

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>A késleltetés hozzáadása – amíg a művelet

1. Válassza ki a Logic App Designerben kívánja hozzáadni, a késleltetési műveletet, feladatütemezésekben **új lépés**.

   Adja hozzá a késleltetési műveletet között lépéseket, vigye a mutatót a nyíl felett, amely kapcsolódik a lépéseket. Válassza a plusz jelre (+), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A keresőmezőbe írja be szűrőként "késleltetés". A műveletek listából válassza a következő műveletet: **Késleltetés eddig**

   !["Késleltetés eddig" művelet hozzáadása](./media/connectors-native-delay/add-delay-until-action.png)

1. Adja meg a záró dátum és idő szeretné, hogy a munkafolyamat folytatásához.

   ![Adja meg, amikor befejezi a késleltetés időbélyeg](./media/connectors-native-delay/delay-until-timestamp.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | Időbélyeg | timestamp | Igen | String | A záró dátum és idő folytatásához a munkafolyamatot, ebben a formátumban: <p>ÉÉÉÉ-hh-DDThh:mm:ssZ <p>Így például, ha azt szeretné, 2017. szeptember 18., 2:00-kor, adja meg "2017-09-18T14:00:00Z". <p>**Megjegyzés:** Ez idő formátumot kell követnie a [ISO 8601 dátum-idő specifikáció](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC idő dátumformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Nélkül időzónát hozzá kell adnia a levél "Z" végén szóközök nélkül. A "Z" hivatkozik az azzal egyenértékű [hajózási idő](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Adja hozzá a munkafolyamatot futtatni kívánt műveletek.

1. Ha elkészült, mentse a logikai alkalmazást.

## <a name="next-steps"></a>További lépések

* [Létrehozhatja, ütemezheti és ismétlődő feladatok és munkafolyamatok futtatása a ismétlődési trigger](../connectors/connectors-native-recurrence.md)
* [A Logic Apps összekötői](../connectors/apis-list.md)