---
title: A munkafolyamatok következő műveletének késleltetése
description: Várakozás a következő művelet futtatására a logikai alkalmazás munkafolyamataiban a Késleltetés vagy késleltetés az Azure Logic Apps-műveletekhez használatával
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787336"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>A következő művelet futtatásának késleltetése az Azure Logic Apps alkalmazásban

Ha azt szeretné, hogy a logikai alkalmazás várjon egy ideig a következő művelet futtatása előtt, hozzáadhatja a beépített **késleltetés – művelet ütemezése** művelet előtt a logikai alkalmazás munkafolyamatában. Vagy hozzáadhatja a beépített **Késleltetés amíg - A** művelet ütemezése egy adott dátumig és időpontig, mielőtt a következő műveletet futtatna. A beépített ütemezési műveletekről és eseményindítókról az [Ismétlődő automatizált, feladatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps alkalmazásokkal című témakörben](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)talál további információt.

* **Késleltetés**: A következő művelet futtatása előtt várja meg a megadott számú időegységet, például másodperceket, perceket, órákat, napokat, heteket vagy hónapokat.

* **Késleltetés a következő**művelet futtatása előtt a megadott dátumig és időpontig.

Íme néhány példa a műveletek használatára:

* Várjon egy hétköznapig, hogy e-mailben küldjön állapotfrissítést.

* Az adatok folytatása és beolvasása előtt a http-hívás befejezéséig késleltetheti a munkafolyamatot.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra.](https://azure.microsoft.com/free/)

* Alapvető ismeretek a [logikai alkalmazásokról.](../logic-apps/logic-apps-overview.md) A művelet használata előtt a logikai alkalmazás először egy eseményindítóval kell kezdődnie. A késleltetési művelet hozzáadása előtt bármilyen eseményindítót használhat, és további műveleteket adhat hozzá. Ez a témakör az Office 365 Outlook-eseményindítót használja. Ha most ismerkedik a logikai alkalmazásokkal, ismerje meg, [hogyan hozhat létre az első logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>A Késleltetés művelet hozzáadása

1. A Logic App Designer ben, a késleltetési művelet hozzáadásának lépésében válassza az **Új lépés lehetőséget.**

   A késleltetési művelet lépések közé való hozzáadásához vigye az egérmutatót a lépéseket összekötő nyíl fölé. Válassza ki a megjelenő pluszjelet (+), majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "késleltetés" szót. A műveletek listájából válassza a következő műveletet: **Késleltetés**

   !["Késleltetés" művelet hozzáadása](./media/connectors-native-delay/add-delay-action.png)

1. Adja meg, hogy mennyi ideig kell várni a következő művelet futtatása előtt.

   ![A késleltetéshez való idő beállítása](./media/connectors-native-delay/delay-time-intervals.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | Darabszám | count | Igen | Egész szám | A késleltetni múlva számra számot kell |
   | Unit (Egység) | egység | Igen | Sztring | Az időmérték, `Second`például: `Minute` `Hour`, `Day` `Week`, , , vagy`Month` |
   ||||||

1. Adja hozzá a munkafolyamatban futtatni kívánt egyéb műveleteket.

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>A Késleltetés a míg a művelet hozzáadása

1. A Logic App Designer ben, a késleltetési művelet hozzáadásának lépésében válassza az **Új lépés lehetőséget.**

   A késleltetési művelet lépések közé való hozzáadásához vigye az egérmutatót a lépéseket összekötő nyíl fölé. Válassza ki a megjelenő pluszjelet (+), majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "késleltetés" szót. A műveletek listájában válassza a következő műveletet: **Késleltetés**

   !["Késleltetés-ig" művelet hozzáadása](./media/connectors-native-delay/add-delay-until-action.png)

1. Adja meg a befejezési dátumot és az időpontot, amikor folytatni szeretné a munkafolyamatot.

   ![Időbélyegző megadása a késleltetés befejezésének időpontjához](./media/connectors-native-delay/delay-until-timestamp.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | Időbélyeg | időbélyeg | Igen | Sztring | A munkafolyamat folytatásának záró dátuma és időpontja a következő formátumban: <p>ÉÉÉÉ-HH-DDThh:mm:ssZ <p>Ha például 2017. <p>**Megjegyzés:** Ennek az időformátumnak [utc dátumidő-formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kell követnie az [ISO 8601 dátumidő-specifikációt](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) , de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Időzóna nélkül a végén szóközök nélkül hozzá kell adnia a "Z" betűt. Ez a "Z" az egyenértékű [hajózási időre](https://en.wikipedia.org/wiki/Nautical_time)vonatkozik . |
   ||||||

1. Adja hozzá a munkafolyamatban futtatni kívánt egyéb műveleteket.

1. Ha elkészült, mentse a logikai alkalmazást.

## <a name="next-steps"></a>További lépések

* [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az Ismétlődés eseményindítóval](../connectors/connectors-native-recurrence.md)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)