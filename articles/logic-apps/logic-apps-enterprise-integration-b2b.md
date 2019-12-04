---
title: B2B vállalati integrációk létrehozása
description: B2B-adatAzure Logic Appsek fogadása Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 39966b8171296a8608b9436485f7682d114c8410
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793100"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B-adatfogadás Azure Logic Apps és Enterprise Integration Pack

A partnerekkel és szerződésekkel rendelkező integrációs fiók létrehozása után készen áll a Business to Business (B2B) munkafolyamat létrehozására a logikai alkalmazáshoz a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Az AS2 és a X12 műveletek használatához Vállalati integráció fiókkal kell rendelkeznie. Megtudhatja [, hogyan hozhat létre vállalati integráció fiókot](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Logikai alkalmazás létrehozása B2B-összekötővel

Kövesse az alábbi lépéseket egy B2B logikai alkalmazás létrehozásához, amely az AS2 és a X12 műveletet használja az adatok kereskedelmi partnertől való fogadásához:

1. Hozzon létre egy logikai alkalmazást, majd [kapcsolja össze az alkalmazást az integrációs fiókkal](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Kérelem hozzáadása **– Ha egy HTTP-kérést kap** a logikai alkalmazáshoz, a rendszer elindít egy HTTP-kérelmet.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. A **dekódolási AS2** művelet hozzáadásához válassza a **művelet hozzáadása**lehetőséget.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Ha az összes műveletet át szeretné szűrni, írja be az **AS2** szót a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Válassza ki az **AS2-dekódolási AS2-üzenet** műveletet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adja hozzá a bemenetként használni kívánt **törzset** . 
   Ebben a példában válassza ki a logikai alkalmazást indító HTTP-kérelem törzsét. Vagy adjon meg egy kifejezést, amely beírja a fejléceket a **fejlécek** mezőbe:

    @triggerOutputs() [' headers ']

7. Adja hozzá az AS2 számára szükséges **fejléceket** , amelyeket a HTTP-kérések fejlécében talál. 
   Ebben a példában válassza ki a logikai alkalmazást indító HTTP-kérelem fejléceit.

8. Most adja hozzá a dekódolási X12-üzenet műveletet. Válassza **a művelet hozzáadása**lehetőséget.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Ha az összes műveletet át szeretné szűrni, írja be a **x12** szót a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Válassza ki a **X12-dekódolás X12 üzenet** műveletet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Most meg kell adnia a bemenetet ehhez a művelethez. 
    Ez a bemenet az előző AS2 művelet kimenete.

    Az üzenet tényleges tartalma egy JSON-objektumban van, és Base64 kódolású, ezért meg kell adnia egy kifejezést bemenetként. 
    Adja meg a következő kifejezést az **X12 Flat file üzenetben a beviteli mező DEkódolásához** :
    
    @base64ToString(törzs ("Decode_AS2_message")? [" AS2Message']? ["Content"])

    Most adja hozzá a lépéseket a kereskedelmi partnertől kapott X12 adatok dekódolásához és egy JSON-objektum kimeneti elemeihez. 
    Ha értesíteni szeretné a partnert arról, hogy az adatfogadásra került, küldjön vissza egy választ a HTTP-válasz műveletben az AS2 üzenet-törlési értesítést (MDN) tartalmazó válasz.

12. A **Válasz** művelet hozzáadásához válassza a **művelet hozzáadása**lehetőséget.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Ha az összes műveletet át szeretné szűrni, a keresőmezőbe írja be a **Válasz** szót.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Válassza ki a **Válasz** műveletet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Ha a MDN az **X12 dekódolása** művelet kimenetében szeretné elérni, állítsa a válasz **törzse** mezőt a következő kifejezésre:

    @base64ToString(törzs ("Decode_AS2_message")? [" OutgoingMdn']? ["Content"])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Mentse a munkáját.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Ezzel befejezte a B2B logikai alkalmazás beállítását. Egy valós alkalmazásban érdemes lehet egy üzletági (LOB) alkalmazásban vagy adattárban tárolni a dekódolású X12-adatmennyiséget. A saját LOB-alkalmazások összekapcsolásához és a logikai alkalmazásban található API-k használatához további műveleteket adhat hozzá, vagy egyéni API-kat írhat.

## <a name="features-and-use-cases"></a>Funkciók és használati esetek

* Az AS2-és X12-dekódolási és-kódolási műveletek lehetővé teszik az adatcserét a kereskedelmi partnerek között az iparági szabványoknak megfelelő protokollok használatával a Logic Appsben.
* A kereskedelmi partnerekkel való adatcseréhez az AS2 és a X12 is használható egymással vagy anélkül.
* A B2B-műveletek segítségével egyszerűen hozhat létre partnereket és szerződéseket az integrációs fiókjában, és felhasználhatja őket egy logikai alkalmazásban.
* Ha más műveletekkel bővíti a logikai alkalmazást, az egyéb alkalmazások és szolgáltatások, például a SalesForce között küldhet és fogadhat.

## <a name="learn-more"></a>További információ
[További információ a Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
