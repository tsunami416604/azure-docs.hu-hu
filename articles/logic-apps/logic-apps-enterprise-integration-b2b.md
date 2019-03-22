---
title: Vállalati Integrációk B2B - létrehozása az Azure Logic Apps |} A Microsoft Docs
description: Az Azure Logic Appsben az Enterprise Integration Pack csomag B2B adatfogadás
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074720"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Az Azure Logic Apps és az Enterprise Integration Pack csomag B2B adatfogadás

Miután létrehozott egy integrációs fiókhoz, amelynek a partnerek és szerződések, készen áll a logikai alkalmazás a vállalatközi (B2B) munkafolyamat létrehozása a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Az AS2-és X12 műveleteket, rendelkeznie kell egy vállalati integrációs fiókban. Ismerje meg, [vállalati integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>B2B-összekötők a logikai alkalmazás létrehozása

Az AS2- és X12 használó B2B logikai alkalmazás létrehozása a következő lépésekkel adatokat fogadni a kereskedelmi partnerek a műveletek:

1. Hozzon létre egy logikai alkalmazást, majd [kapcsolja össze alkalmazását a az integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Adjon hozzá egy **kérelem – Ha egy HTTP-kérelem érkezett** eseményindítót a logikai alkalmazáshoz.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Hozzáadása a **AS2-dekódolást** műveletet, válassza **művelet hozzáadása**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Szűrés a használni kívánt összes műveleteket, adja meg a word **as2** kifejezést a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Válassza ki a **AS2 - dekódolást AS2-üzenet** művelet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adja hozzá a **törzs** bemeneteként használni kívánt. 
   Ebben a példában válassza ki, amely elindítja a logikai alkalmazás a HTTP-kérelem törzse. Adjon meg egy kifejezést, amely a fejlécek, a bemeneti vagy a **FEJLÉCEK** mező:

    @triggerOutputs(["headers"])

7. Adja hozzá a szükséges **fejlécek** az AS2, amely találhatja meg a HTTP-kérelemfejlécek. 
   Ebben a példában válassza ki azt a logikai alkalmazás a HTTP-kérelem fejlécét.

8. Most adja hozzá a dekódolási X12 üzenet műveletet. Válassza ki **művelet hozzáadása**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Szűrés a használni kívánt összes műveleteket, adja meg a word **x12** kifejezést a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Válassza ki a **X12-dekódolási X12 üzenet** művelet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Most meg kell adnia a bemeneti ezt a műveletet. 
    A bemeneti adatok az előző AS2 művelet kimenete.

    A tényleges üzenettartalom JSON-objektum és base64-kódolású, ezért meg kell adnia egy kifejezést a bemenetként. 
    Adja meg a következő kifejezésre a **X12 EGYBESIMÍTOTT fájl üzenet TO DEKÓDOLÁSI** beviteli mező:
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Most adja hozzá a data policies kapott, és a kimeneti JSON-objektum elemeinek X12 dekódolandó lépéseket. 
    Értesítse a partnert, hogy érkezett-e az adatok, küldhet vissza egy választ, amely tartalmazza az AS2 üzenet törlése értesítési (MDN) a HTTP-válasz művelethez.

12. Hozzáadása a **válasz** műveletet, válassza a **művelet hozzáadása**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Szűrés a használni kívánt összes műveleteket, adja meg a word **válasz** kifejezést a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Válassza ki a **válasz** művelet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Az MDN kimenetéből származó eléréséhez a **dekódolási X12 üzenet** műveletben állítsa be a válasz **törzs** mezőt a következő kifejezést:

    @base64ToString(body('Decode_AS2_message')? ["OutgoingMdn']? ["Content"])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Mentse a munkáját.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Ezzel a B2B logikai alkalmazás beállításával. Egy valós alkalmazás esetében előfordulhat, hogy tárolni szeretné a dekódolt X12 egy – üzletági (LOB) alkalmazás vagy adattár adatait. Való csatlakozáshoz a saját ÜZLETÁGI alkalmazások és API-k a logikai alkalmazásban, hozzáadhat további műveletek vagy egyéni API-k.

## <a name="features-and-use-cases"></a>Funkciók és alkalmazási helyzetek

* Az AS2 X12 dekódolása és kódolása műveletek tegye lehetővé, exchange-adatok között a kereskedelmi partnerekkel, a logic appsben az iparági szabványos protokollok használatával.
* Exchange-adatok az üzleti partnerekkel való, használható AS2 és X12 vagy anélkül egymással.
* A B2B-műveletek segítséget nyújt a partnerek és egyezmények az integrációs fiókban lévő egyszerű létrehozását és felhasználását őket egy logikai alkalmazásban.
* Ha kiterjeszti a logikai alkalmazás más műveletekkel, küldhet és fogadhat adatokat más alkalmazások és szolgáltatások, mint például a SalesForce között.

## <a name="learn-more"></a>Részletek
[További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
