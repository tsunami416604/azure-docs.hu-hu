---
title: Csatlakozhat a Boxhoz – Azure Logic Apps |} A Microsoft Docs
description: Fájlok a Box REST API-k és az Azure Logic Apps létrehozásáról és kezeléséről
author: ecfan
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 971d38fa0fbd47f0deb815577033bbe684aac32f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310676"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Az Azure Logic Apps a boxban fájlok létrehozásáról és kezeléséről

Ez a cikk bemutatja, hogyan létrehozása és kezelése a fájlokat a boxban belül egy logikai alkalmazást, a Box-összekötővel. Ezzel a módszerrel hozhat létre, amely a feladatok és kezeléséhez a fájlok és más műveletek, például a munkafolyamatok automatizálása a logic apps:

* A kapott boxból adatok az üzleti folyamatot hozhat létre.

* Indítható automatizált feladatokat és a munkafolyamat, ha egy fájl létrehozásakor vagy frissítésekor.

* Futtassa a másol egy fájlt, töröl egy fájlt, és egyéb műveleteket.

  Ezek a műveletek válasz érkezik, amikor azok elérhetővé a kimeneti más műveletekhez. 
  Például ha egy fájl módosul, a Box, elküldheti a fájlt használja az Office 365 e-mailben.

## <a name="prerequisites"></a>Előfeltételek

* A [fiók párbeszédpanel](https://www.box.com/home)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A logikai alkalmazás, ahol szeretné a Box-fiók eléréséhez. Egy Box-trigger indít el a logikai alkalmazást, szüksége egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő OpenAPI (korábbi nevén Swagger) fájl, tekintse meg a [összekötő referenciájának oldalát](/connectors/box/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)