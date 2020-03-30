---
title: Csatlakozás a dobozhoz
description: Az Azure Logic Apps használatával fájlokat létrehozó és kezelő feladatok és munkafolyamatok automatizálása a Boxban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666771"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Fájlok létrehozása és kezelése a Box ban az Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet fájlokat a Box-ban egy logikai alkalmazáson belülről a Box-összekötővel. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a fájlok és egyéb műveletek kezeléséhez szükséges feladatokat és munkafolyamatokat, például:

* A Boxtól kapott adatok alapján építheti ki üzleti folyamatait.

* Automatikus feladatok és munkafolyamat oka a fájl létrehozásakor vagy frissítésekor.

* Futtasson egy fájlt másoló műveletet, vagy töröljön egy fájlt.

  Amikor ezek a műveletek választ kapnak, a kimenetet más műveletek számára is elérhetővé teszik. 
  Ha például egy fájlt módosítanak a Box alkalmazásban, a fájlt elküldheti e-mailben az Office 365-tel.

## <a name="prerequisites"></a>Előfeltételek

* [A Box fiók](https://www.box.com/home)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A logikai alkalmazás, ahol szeretné elérni a Box-fiók. A logikai alkalmazás Box eseményindítóval való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md)
Ha most kezdi a logikai alkalmazásokat, tekintse át [az Azure Logic Apps.](../logic-apps/logic-apps-overview.md)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi nevén Swagger) fájljában leírtak szerint technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán talál.](/connectors/box/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)