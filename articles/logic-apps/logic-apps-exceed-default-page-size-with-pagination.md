---
title: További elemek vagy rekordok beszerezni tördeléssel
description: A tördelés beállítása az Azure Logic Apps-beli összekötőműveletek alapértelmezett oldalméret-korlátjának túllépése érdekében
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792111"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>További adatok, elemek és rekordok beszereznie tördelés használatával az Azure Logic Apps

Amikor az [Azure Logic Apps-ben](../logic-apps/logic-apps-overview.md)egy összekötő művelet használatával lekéri az adatokat, elemeket vagy rekordokat, előfordulhat, hogy olyan nagy eredményhalmazokat kap, hogy a művelet nem adja vissza az összes eredményt egyszerre. Egyes műveletek esetén az eredmények száma meghaladhatja az összekötő alapértelmezett oldalméretét. Ebben az esetben a művelet csak az eredmények első oldalát adja vissza. Például az SQL Server-összekötő **Sorok betöltése** műveletének alapértelmezett oldalmérete 2048, de más beállításoktól függően változhat.

Egyes műveletek lehetővé teszik, hogy kapcsolja be a *tördelési* beállítást, hogy a logikai alkalmazás képes letölteni több eredményt a tördelési korlátig, de ezeket az eredményeket egyetlen üzenetben adja vissza, amikor a művelet befejeződik. A tördelés használatakor meg kell adnia egy *küszöbértéket,* amely a művelet visszaadására kívánt eredmények célszáma. A művelet lekéri az eredményeket, amíg el nem éri a megadott küszöbértéket. Ha az elemek teljes száma kisebb, mint a megadott küszöbérték, a művelet lekéri az összes eredményt.

A tördelési beállítás bekapcsolása az összekötő oldalméretétől függően olvassa le az eredmények oldalait. Ez a viselkedés azt jelenti, hogy néha előfordulhat, hogy több eredményt kap, mint a megadott küszöbértéket. Például az SQL Server **Sorok bekérdezése** művelet használatakor, amely támogatja a tördelési beállítást:

* A művelet alapértelmezett oldalmérete oldalanként 2048 rekord.
* Tegyük fel, hogy 10 000 rekordja van, és minimum 5000 rekordot ad meg.
* A tördelés rekordok oldalait kapja meg, így a művelet legalább a megadott minimumot (3 oldal x 2048 rekord) adja vissza, nem 5000 rekordot.

Az alábbiakban csak néhány összekötőt tartalmazó lista tartalmazza, ahol meghaladhatja az adott műveletek alapértelmezett oldalméretét:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [Sharepoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* A logikai alkalmazás és az a művelet, amelybe be szeretné kapcsolni a tördelést. Ha nem rendelkezik logikai alkalmazással, olvassa [el a Rövid útmutató: Az első logikai alkalmazás létrehozása című témakört.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="turn-on-pagination"></a>A tördelés bekapcsolása

Annak megállapításához, hogy egy művelet támogatja-e a tördelést a Logic App Designerben, ellenőrizze a művelet beállításait a **Tördelés** beállításhoz. Ez a példa bemutatja, hogyan kapcsolhatja be a tördelést az SQL Server **Sorok beszedése** műveletében.

1. A művelet jobb felső sarkában válassza a három pont (**...**) gombot, majd a **Beállítások lehetőséget.**

   ![A művelet beállításainak megnyitása](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Ha a művelet támogatja a tördelést, a művelet a **Tördelés** beállítást jeleníti meg.

1. Módosítsa a **Tördelés** beállítást Ki beállításról **Be-re.** **Off** A **Threshold** tulajdonságban adjon meg egy egész értéket a művelet által visszaadandó eredmények célszámára.

   ![Adja meg a visszaadandó eredmények minimális számát.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Ha készen áll, válassza a **Kész gombot.**

## <a name="workflow-definition---pagination"></a>Munkafolyamat-definíció - tördelés

Ha bekapcsolja a tördelést egy olyan műveletnél, amely támogatja ezt `"paginationPolicy"` a funkciót, a logikai alkalmazás munkafolyamat-definíciója tartalmazza a tulajdonságot a `"minimumItemCount"` művelet tulajdonságában `"runtimeConfiguration"` lévő tulajdonsággal együtt, például:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Támogatás kérése

A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
