---
title: Csatlakozás a Facebookhoz
description: A Facebook idővonalát és oldalát kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665802"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>A Facebook idővonalának és oldalának kezelése az Azure Logic Apps használatával

Csatlakozz a Facebookhoz, és tetsztesd meg az idővonalat, kapsz egy oldalfeedet stb. A Facebook segítségével a következőket teheti:

* Építsd meg az üzleti folyamatot a Facebooktól kapott adatok alapján. 
* Új bejegyzés érkezésekor használjon eseményindítót.
* Olyan műveleteket használsz, amelyek közzétesznek az idővonaladon, oldalképet kapnak stb. Ezek a műveletek választ kapnak, majd a kimenetet elérhetővé teszik más műveletek számára. Ha például új bejegyzés van az idővonaladon, akkor a bejegyzést a Twitter-hírcsatornára is eltolhatja. 

A kezdéshez hozzon létre egy logikai alkalmazást most, [lásd: Logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-a-connection-to-facebook"></a>Kapcsolat létrehozása a Facebookkal

Amikor hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, engedélyeznie kell a logikai alkalmazásokat a Facebookhoz való csatlakozáshoz.

1. Jelentkezzen be Facebook-fiókjába.

2. Válaszd **az Engedélyezés**lehetőséget, és engedélyezd a logikai alkalmazásaidnak, hogy csatlakozzanak a Facebookhoz, és használják azokat. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi nevén Swagger) fájljában leírtak szerint technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán talál.](/connectors/facebook/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)