---
title: "Az Azure Logic Apps XML - érvényesítése |} Microsoft Docs"
description: "Ellenőrizze az XML a sémák Azure Logic Apps és B2B forgatókönyvek a vállalati integrációs csomag segítségével"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 8558efffa354cc4bb93820c837077ee997924c95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>Vállalati integrációs XML érvényesítése

Gyakran a B2B esetben megállapodást a partnerek kell ellenőrizze, hogy az üzeneteket, azok az exchange érvényes adatok feldolgozásának megkezdése előtt. Az XML-érvényesítés összekötő használatát a vállalati integrációs csomag használatával ellenőrizheti a dokumentumok egy előre definiált sémának.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>A dokumentum XML-érvényesítés összekötő ellenőrzése

1. A logikai alkalmazás létrehozása és [az alkalmazás az integráció fiókkal összekapcsolni](../logic-apps/logic-apps-enterprise-integration-accounts.md "integrációs fiók csatolása logikai alkalmazás további") , amely rendelkezik a séma XML-adatok érvényesítése a használni kívánt.

2. Adja hozzá a **kérelem - amikor egy HTTP-kérelem érkezik** eseményindító a logikai alkalmazáshoz.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. Hozzáadása a **XML-érvényesítés** művelet, válassza a **művelet hozzáadása**.

4. Adja meg azt, amelyik azt szeretné, hogy a műveletek szűrése, *xml* be a keresőmezőbe. Válasszon **XML-érvényesítés**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. Az XML-tartalom az érvényesíteni kívánt megadásához válassza **tartalom**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. A szervezet címke jelöli ki a tartalmát érvényesíteni szeretné.

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. A séma érvényesítése a korábbi használandó meg *tartalom* adjon meg, válassza a **SÉMANÉV**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. Mentse a munkáját  

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

Most befejezése a érvényesítési connector beállítása. Egy valós alkalmazás érdemes az ellenőrzött adatok tárolásához például a SalesForce-üzletági (LOB) alkalmazásban. Az ellenőrzött kimenetként a Salesforce, venni egy műveletet.

Az ellenőrzési művelet teszteléséhez indítson egy lekérdezést a HTTP-végpont.

## <a name="next-steps"></a>Következő lépések
[További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")   

