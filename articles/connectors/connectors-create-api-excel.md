---
title: Csatlakozhat az Excel online-hoz – Azure Logic Apps |} A Microsoft Docs
description: Az Excel online-hoz – REST API-k és az Azure Logic Apps-adatok kezelése
ms.service: logic-apps
services: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 94960b95e6de30159ec34b3f97bb5119cac42c35
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818099"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Az Azure Logic Apps Excel Online-adatok kezelése

Az Azure Logic Apps és az Excel Online-összekötő automatikus feladatokkal és munkafolyamatokkal, vagy a onedrive-on az Excel online-ban adatai alapján is létrehozhat. A munkafolyamatok hajthatja végre ezeket a műveleteket és más adatait, például:

* Hozzon létre új munkalapokat és táblákat.
* GET, és kezelheti a munkalapok, táblák és sorok.
* Egyetlen sorok hozzáadása és kulcsoszlopot.

A logic Apps, az Excel Online műveletek kimenete használó műveleteket is felvehet. Ez az összekötő biztosít csak műveletek, így a logikai alkalmazás indítására használjon egy külön eseményindító, például egy **ismétlődési** eseményindító. Például ha munkalapok létre minden héten, elküldheti ezen új munkalapok az Office 365 Outlook-összekötővel e-mailt.

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

Tekintse meg ezen összekötő referenciaoldalait technikai részleteit, műveletek és -korlátok, leírtak szerint az összekötők Swagger-fájlok, például:

* [A vállalati Excel online-ban](/connectors/excelonlinebusiness/) 
* [Excel online-ban a onedrive vállalati verzióhoz](/connectors/excelonline/) 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
