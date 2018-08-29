---
title: XML érvényességének ellenőrzése B2B vállalati integráció – Azure Logic Apps |} A Microsoft Docs
description: XML érvényességének ellenőrzése az Azure Logic Apps Enterprise Integration Pack a B2B-megoldásoktól sémákkal
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: e25f4e633b8cf9030bb0ce395f093f630db59f38
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121113"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack for XML érvényességének ellenőrzése

Gyakran a B2B-forgatókönyvekhez, egy megállapodást a partnerek kell ellenőrizze, hogy az üzenetek váltanak érvényes adatfeldolgozási megkezdése előtt. Dokumentumok egy előre definiált séma az XML-érvényesítés összekötő használatát az Enterprise Integration Pack használatával ellenőrizheti.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>A dokumentum az XML-érvényesítés összekötővel ellenőrzése

1. Hozzon létre egy logikai alkalmazást, és [az alkalmazás összekapcsolása az integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md "Ismerkedjen meg az integrációs fiók összekapcsolása egy logikai alkalmazást") , amely rendelkezik a séma használandó XML-adatok érvényesítéséhez.

2. Adjon hozzá egy **kérelem – Ha egy HTTP-kérelem érkezett** eseményindítót a logikai alkalmazáshoz.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Hozzáadása a **XML-érvényesítés** műveletet, válassza a **művelet hozzáadása**.

4. Adja meg a használni kívánt összes a műveletek szűrése, *xml* kifejezést a keresőmezőbe. Válasszon **XML-érvényesítés**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Válassza ki az érvényesíteni kívánt XML-tartalom megadásához **tartalom**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Válassza ki a törzscímkét tartalmat szeretne érvényesíteni.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Adja meg a séma érvényesítése az előző használni szeretné a *tartalom* adjon meg, válassza a **SÉMANÉV**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Mentse a munkáját  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Most már elkészült, az érvényesítési összekötő beállításához. Egy valós alkalmazás esetében érdemes lehet, hogy az ellenőrzött adatok tárolása egy – üzletági (LOB) alkalmazásokat, mint például a SalesForce. Az ellenőrzött kimenetként a Salesforce-hoz, adjon hozzá egy műveletet.

Az érvényesítés művelet teszteléséhez indítson egy a HTTP-végpontot.

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")   

