---
title: Kapcsolódás a Salesforce-hez Azure Logic Appsról | Microsoft Docs
description: Salesforce-rekordok és-feladatok figyelésére, létrehozására és kezelésére szolgáló feladatok és munkafolyamatok automatizálása Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: e3e9f7c841d25fa988ae7e0c97adf64a51d8ef87
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050823"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Salesforce-erőforrások figyelése, létrehozása és kezelése Azure Logic Apps használatával

A Azure Logic Apps és a Salesforce-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre a Salesforce-erőforrásokhoz, például a rekordokhoz, a feladatokhoz és az objektumokhoz, például a következő módon:

* A rekordok létrehozásának vagy módosításának figyelése. 
* Feladatok és rekordok létrehozása, beolvasása és kezelése, beleértve a beszúrási, frissítési és törlési műveleteket.

Használhat olyan Salesforce eseményindítókat, amelyek válaszokat kapnak a Salesforce, és elérhetővé teszik a kimenetet más műveletek számára. A logikai alkalmazásokban lévő műveletek használatával Salesforce-erőforrásokkal kapcsolatos feladatokat végezhet el. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Egy [Salesforce-fiók](https://salesforce.com/)

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Salesforce-fiókját. Egy Salesforce triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha Salesforce műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-salesforce"></a>Kapcsolódás a Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "Salesforce" kifejezést. 
   Válassza ki a kívánt eseményindítót az eseményindítók listából. 

     – vagy –

   * A meglévő Logic apps esetében azon a lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. A keresőmezőbe írja be szűrőként a "Salesforce" kifejezést. A műveletek listában válassza ki a kívánt műveletet.

1. Ha a rendszer felszólítja, hogy jelentkezzen be a Salesforce-be, jelentkezzen be most, és engedélyezze a hozzáférést.

   A hitelesítő adataival engedélyezheti a logikai alkalmazásnak, hogy kapcsolatot hozzon létre a Salesforce, és hozzáférjen az adataihoz.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/salesforce/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése