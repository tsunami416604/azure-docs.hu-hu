---
title: További adatok, cikkek és tördelés – Azure Logic Apps-rekordok beolvasása
description: Tördelés beállítása túllépni az alapértelmezett oldal mérete legfeljebb az Azure Logic Apps összekötő-műveletek
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64476541"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>További adatok, cikkek és rekordok első tördelés Azure Logic Apps használatával

Amikor kérheti le az adatok, cikkek és rekordok egy összekötő művelet használatával [Azure Logic Apps](../logic-apps/logic-apps-overview.md), rendkívül nagy méretűek, hogy a művelet nem ad vissza az összes eredmény egy időben kaphat eredményhalmazt. Az egyes műveletek az eredmények száma meghaladhatja az összekötő alapértelmezett lapméret. Ebben az esetben a művelet csak az első oldal eredményeket adja vissza. Ha például oldalméret alapértelmezés szerint az SQL Server-összekötő **sorok beolvasása** művelet 2048, de más beállítások alapján változhat.

Bizonyos műveletek lehetővé teszik, hogy egy *tördelés* beállítást, hogy a logikai alkalmazás a tördelés korlátig további eredmények lekéréséhez, de eredmények visszaadása, egy üzenet, a művelet végeztével. Tördelés használatakor meg kell adnia egy *küszöbérték* érték, amely a cél azt szeretné, hogy a művelet visszaadandó eredmények száma. A művelet eredménye a megadott küszöbértéket eléréséig kérdezi le. Ha az elemek száma kisebb a megadott küszöbértéknél, a művelet az összes eredmény kérdezi le.

Az eredmények alapján egy összekötő oldalméret tördelés beállítás beolvasása oldalak bekapcsolásával. Ez a viselkedés, az azt jelenti, hogy egyes esetekben előfordulhat, hogy további eredményeket kap a megadott küszöbértéknél. Ha az SQL Server használata esetén például **sorok beolvasása** művelet, amely támogatja a tördelés beállítás:

* A művelet alapértelmezett oldal mérete 2048 rekordok száma oldalanként.
* Tegyük fel, hogy 10 000 rekordot rendelkezik, és adja meg a minimális 5000 rekordokat.
* Tördelés lekéri a rekordokat úgy, hogy legalább a megadott minimális érték első oldalát, a művelet 6144 rekordjait (3 oldalak x 2048 rekord), nem 5000 rekordokat adja vissza.

Ez egy lista csupán néhány az összekötők, ahol túllépheti az adott műveletek oldalméret alapértelmezés szerint:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* A logikai alkalmazás és az elvégzendő ahol tördelés bekapcsolása. Ha a logikai alkalmazás nem rendelkezik, tekintse meg [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Tördelés bekapcsolása

Annak megállapításához, hogy művelet támogatja-e a Logic App Designerben tördelés, ellenőrizze a művelet beállításait a **tördelés** beállítás. Ez a példa bemutatja, hogyan kapcsolja be az SQL Server tördelés **sorok beolvasása** művelet.

1. A művelet jobb felső sarokban, válassza a három pontra ( **...** ) gombra, és válassza **beállítások**.

   ![Nyissa meg a művelet beállításai](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Ha a művelet tördelés támogatja, a művelet mutatja a **tördelés** beállítás.

1. Módosítsa a **tördelés** beállítást **ki** való **a**. Az a **küszöbérték** tulajdonság, adjon meg egy egész számot, amely azt szeretné, hogy a művelet visszaadandó eredmények cél számát.

   ![Adja meg a visszaadandó eredmények minimális száma](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Amikor elkészült, válassza ki a **kész**.

## <a name="workflow-definition---pagination"></a>Munkafolyamat-definíció - tördelési

Egy műveletet, amely támogatja ezt a lehetőséget a tördelés bekapcsolásakor tartalmazza-e a logikai alkalmazás munkafolyamat-definíció a `"paginationPolicy"` a tulajdonság a `"minimumItemCount"` tulajdonság frissítése az adott művelet `"runtimeConfiguration"` tulajdonságához; például:

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
