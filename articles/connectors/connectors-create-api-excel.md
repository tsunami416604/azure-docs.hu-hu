---
title: Csatlakozhat az Excel online-hoz – Azure Logic Apps
description: Az Excel online-hoz – REST API-k és az Azure Logic Apps-adatok kezelése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 28739ad65462acc9f2d2ed7db1e9ed14d19f032c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314519"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Az Azure Logic Apps Excel Online-adatok kezelése

Az Azure Logic Apps és az Excel Online-összekötő automatikus feladatokkal és munkafolyamatokkal, vagy a onedrive-on az Excel online-ban adatai alapján is létrehozhat. Ez az összekötő biztosít műveletek, amelyek segítenek az adatok és a táblázatok, például kezelheti:

* Hozzon létre új munkalapokat és táblákat.
* GET, és kezelheti a munkalapok, táblák és sorok.
* Egyetlen sorok hozzáadása és kulcsoszlopot.

Ezután használhatja ezeket a műveleteket kimenetei műveletekhez más szolgáltatásokhoz. Például ha egy műveletet, amely létrehozza a munkalapok minden héten használja, használhatja egy másik művelet, amely megerősítő e-mailt küld az Office 365 Outlook-összekötő használatával.

Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> A [Excel online-ban a vállalati](/connectors/excelonlinebusiness/) és [az Excel online-hoz a onedrive vállalati verzióhoz](/connectors/excelonline/) összekötők működnek az Azure Logic Apps, és eltér a [PowerApps Excel-összekötő](/connectors/excel/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Egy [Office 365-fiókja](https://www.office.com/) számára a munkahelyi és személyes Microsoft-fiók

  Az Excel-adatok létezhet egy vesszővel tagolt (CSV) fájlként egy storage-mappába, például a onedrive-on. 
  Használhatja az azonos CSV-fájl is a [fájlszintű összekötő](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazást, amelyre az Excel online-ban adatok eléréséhez. Ez az összekötő biztosít csak műveletek, így a logikai alkalmazás indítására jelöljön ki egy külön eseményindítót, például a **ismétlődési** eseményindító.

## <a name="add-excel-action"></a>Az Excel-művelet hozzáadása

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben, ha nincs már nyissa meg.

1. Az eseményindító területén válassza a **új lépés**.

1. A Keresés mezőbe írja be "excel-" szűrőként. Műveletek listája alatt válassza ki a kívánt művelet.

1. Ha a rendszer kéri, jelentkezzen be az Office 365-fiókjával, válassza ki a **jelentkezzen be a**.

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot az Excel online-hoz, és az adatok eléréséhez.

1. Továbbra is biztosítani a szükséges adatokat a kiválasztott művelet, és a logikai alkalmazás munkafolyamat létrehozásához.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő OpenAPI (korábbi nevén Swagger-) fájlok, lásd: e összekötő referenciaoldalait:

* [A vállalati Excel online-ban](/connectors/excelonlinebusiness/)
* [Excel online-ban a onedrive vállalati verzióhoz](/connectors/excelonline/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
