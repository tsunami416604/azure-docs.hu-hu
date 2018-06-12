---
title: Hozzon létre B2B megoldások - Azure Logic Apps |} Microsoft Docs
description: Adatfogadás logic Apps alkalmazásokat a vállalati integrációs csomag a B2B szolgáltatások segítségével
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a27a413ba9a0d974cf90fe842d5fc325ab308a56
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298117"
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Adatfogadás a logic apps B2B funkcióit a vállalati integrációs csomag

Miután létrehozta a partnerek és megállapodások integrációs fiókkal, készen áll a Logic Apps alkalmazást, a vállalatok számára (B2B) munkafolyamat létrehozása a [vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Az AS2 és X12 műveleteket, rendelkeznie kell egy vállalati integrációs fiók. Ismerje meg, [vállalati integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Hozzon létre egy logic app B2B összekötők

Kövesse az alábbi lépéseket az AS2 és X12 használó B2B logikai alkalmazás létrehozása az adatok fogadására a kereskedelmi partnertől műveletek:

1. Hozzon létre egy logikai alkalmazást, majd [az alkalmazás a integrációs fiókkal összekapcsolni](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Adja hozzá a **kérelem - amikor egy HTTP-kérelem érkezik** eseményindító a logikai alkalmazáshoz.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Hozzáadása a **dekódolása AS2** művelet, jelölje be **művelet hozzáadása**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Ha szeretne szűrni a használni kívánt összes műveleteket, adja meg a word **as2** be a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Válassza ki a **AS2 - dekódolási AS2 üzenet** művelet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adja hozzá a **törzs** bemenetként használni kívánt. Ebben a példában válassza ki, amely elindítja a logikai alkalmazás a HTTP-kérelem törzsét. Adjon meg egy kifejezést, amely a bemeneti fejlécének vagy a **FEJLÉCEK** mező:

    @triggerOutputs(["fejléc"])

7. Adja hozzá a szükséges **fejlécek** az AS2, amelyek a HTTP-kérelmek fejléceinek található. Ebben a példában válassza ki, amelynek hatására a logikai alkalmazást a HTTP-kérés fejlécébe.

8. Most adja hozzá a dekódolási X12 üzenet művelet. Válassza ki **művelet hozzáadása**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Ha szeretne szűrni a használni kívánt összes műveleteket, adja meg a word **x12** be a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Válassza ki a **X12-dekódolási X12 üzenet** művelet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Ez a művelet bemeneti most meg kell adnia. A bemeneti érték az előző AS2 művelet eredményének.

    A tényleges üzenet tartalma egy JSON-objektum és base64-kódolású, ezért meg kell adnia egy kifejezést a bemeneti adatként. 
    Adja meg a következő kifejezésre a **X12 lapos fájl üzenet TO DEKÓDOLÁSI** beviteli mezőt:
    
    @base64ToString(body('Decode_AS2_message')? ["AS2Message']? ["Tartalom"])

    Most vegyen fel olyan lépéseket a X12 adatok kereskedelmi partneren, és egy JSON-objektum elemeinek kimeneti dekódolására. 
    Az értesítés a partner, hogy az adatokat fogadta a program, küldhet vissza egy válaszban az AS2 üzenet törlése értesítés (MDN) a HTTP-válasz művelethez.

12. Hozzáadása a **válasz** művelet, válassza a **művelet hozzáadása**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Ha szeretne szűrni a használni kívánt összes műveleteket, adja meg a word **válasz** be a keresőmezőbe.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Válassza ki a **válasz** művelet.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. A MDN elérje a kimenetét a **dekódolási X12 üzenet** művelet, állítsa be a válasz **törzs** mezőt a ebben a kifejezésben:

    @base64ToString(body('Decode_AS2_message')? ["OutgoingMdn']? ["Tartalom"])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Mentse a munkáját.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Most már befejezte a B2B logikai alkalmazás beállítása. Egy valós alkalmazás, előfordulhat, hogy tárolni szeretné a dekódolt X12 adatok-üzletági (LOB) alkalmazás vagy adatok tárolóban. Kapcsolódás saját LOB-alkalmazások, és ezen API-k használata a Logic Apps alkalmazást, adhat hozzá további műveletek vagy egyéni API-k írása.

## <a name="features-and-use-cases"></a>Szolgáltatások és a használati esetek

* Az AS2 és X12 dekódolni, és műveletek kódolása lehetővé, hogy adatcserében működik közre a logic apps iparági szabványos protokollok segítségével kereskedelmi partnerek között.
* Az exchange-adatok az üzleti partnerekkel való, használhatja AS2 és X12 vagy anélkül egymással.
* A B2B műveletek hogyan hozhat létre partnerek és megállapodások könnyen integrációs fiókjában, és felhasználni azokat a logikai alkalmazás.
* A Logic Apps alkalmazást az egyéb műveletek bővítésekor küldhet és fogadhat adatokat más alkalmazásokat és szolgáltatásokat, mint a SalesForce között.

## <a name="learn-more"></a>Részletek
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md)
