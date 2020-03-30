---
title: Adatok, munkalapok és táblázatok kezelése az Excel Online-ban
description: Adatok kezelése munkalapokon és táblázatokban az Excel Online Vállalati verzióban vagy a OneDrive-hoz készült Excel Online-ban az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445882"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Excel Online-adatok kezelése az Azure Logic Apps alkalmazásokkal

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és az Excel Online for [Business](/connectors/excelonlinebusiness/) összekötő vagy az Excel Online [for OneDrive-összekötő](/connectors/excelonline/) segítségével automatizált feladatokat és munkafolyamatokat hozhat létre az Excel Online vállalati verzióban vagy a OneDrive-on lévő adatai alapján. Ez az összekötő olyan műveleteket biztosít, amelyek segítenek az adatokkal való munkában és a számolótáblák kezelésében, például:

* Új munkalapok és táblázatok létrehozása.
* Munkalapok, táblázatok és sorok beszerezhetése és kezelése.
* Egyetlen sorok és kulcsoszlopok hozzáadása.

Ezután használhatja a kimenetek ezek a műveletek műveletek más szolgáltatásokhoz. Ha például minden héten munkalapot létrehozó műveletet használ, akkor egy másik műveletet is használhat, amely az Office 365 Outlook-összekötő vel küld visszaigazoló e-mailt.

Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Az [Excel Online Vállalati verzió](/connectors/excelonlinebusiness/) és a [OneDrive-hoz készült Excel Online-összekötők](/connectors/excelonline/) együttműködnek az Azure Logic Apps alkalmazásokkal, és különböznek a [PowerApps Excel-összekötőjétől.](/connectors/excel/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Munkahelyi vagy személyes Microsoft-fiókjához készült [Office 365-fiók](https://www.office.com/)

  Az Excel-adatok létezhetnek vesszővel tagolt értékfájlként egy tárolómappában, például a OneDrive-on. 
  Ugyanazt a CSV-fájlt is használhatja a [síkfájlú összekötővel.](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni az Excel Online-adatokhoz. Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazás elindításához válasszon ki egy külön eseményindítót, például az **Ismétlődés eseményindítót.**

## <a name="add-excel-action"></a>Excel hozzáadása művelet

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.**

1. A keresőmezőbe írja be szűrőként az "excel" szót. A műveletek listájában jelölje ki a kívánt műveletet.

   > [!NOTE]
   > A Logic App Designer nem tudja betölteni a 100 vagy több oszlopot tartalmazó táblákat. Ha lehetséges, csökkentse a kijelölt tábla oszlopainak számát, hogy a tervező betölthesse a táblát.

1. Ha a rendszer kéri, jelentkezzen be Office 365-fiókjába.

   A hitelesítő adatok felhatalmazzák a logikai alkalmazást, hogy kapcsolatot hozzon létre az Excel Online-nal, és hozzáférjen az adatokhoz.

1. Továbbra is adja meg a szükséges részleteket a kiválasztott művelethez, és a logikai alkalmazás munkafolyamatának létrehozása.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi nevén Swagger) fájljaiban leírtak szerint technikai részleteket, például eseményindítókat, műveleteket és korlátokat az összekötő referencialapjai ban talál:

* [Excel Online vállalati verzió](/connectors/excelonlinebusiness/)
* [A OneDrive-hoz készült Excel Online](/connectors/excelonline/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
