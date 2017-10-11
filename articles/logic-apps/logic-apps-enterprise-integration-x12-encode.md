---
title: "Kódolja X12 üzenetek - Azure Logic Apps |} Microsoft Docs"
description: "EDI érvényesítése és átalakítani az XML-kódolású X12 helyű üzenet a vállalati integrációs csomag a kódoló Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 29d19364b9a98e351c95f13e68a2e63b9f6439f8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kódolja X12 üzenetek az Azure Logic Apps a vállalati integrációs csomaggal

Encode X12 üzenet összekötő EDI és a partner jellemző tulajdonságok ellenőrzése, XML-kódolású üzenetekkel átalakítása EDI tranzakció halmazok cseréje és műszaki nyugtázási vagy működési visszaigazolás kérése.
Ez az összekötő használatához fel kell vennie az összekötő egy meglévő elindítani a Logic Apps alkalmazást.

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társítva. A Encode X12 üzenet összekötő használatához integrációs fiókkal kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban
* Egy [X12 megállapodás](logic-apps-enterprise-integration-x12.md) , amely már definiálva van az integráció-fiókban

## <a name="encode-x12-messages"></a>Kódolja X12 üzenetek

1. [Logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md).

2. A Encode X12 üzenet összekötő nincs eseményindítók, hozzá kell adni egy eseményindító indítása el a logikai alkalmazás, például a kérelem eseményindítót. A Logic App-tervezőben, vegye fel egy eseményindítót, majd egy műveletet a logikai alkalmazáshoz.

3.  A keresési mezőbe írja be a "x12" a szűrőhöz. Válassza **X12-kódolás X12 szerződésnév üzenetre** vagy **X12-kódolás X12 identitások üzenetből**.
   
    ![Keressen a "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Integráció fiókjába korábban a kapcsolatokat nem hozott létre, ha a program kéri, most, hogy a kapcsolat létrehozásához. A kapcsolat neve, és válassza ki a integrációs fiókot, amely csatlakozni szeretne. 
   
    ![integráció fiók kapcsolat](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
    | Integráció fiók * |Adja meg a integrációs fiók nevét. Ellenőrizze, hogy az integrációs fiók és a logikai alkalmazást az Azure ugyanazon a helyen. |

5.  Amikor elkészült, a kapcsolódási adatait. Ez a példa hasonlóan kell kinéznie. Válassza ki a kapcsolat létrehozásának befejezéséhez **létrehozása**.

    ![integráció fiók kapcsolat létrehozása](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A kapcsolat létrejön.

    ![integráció fiók kapcsolódási adatait.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kódolja X12 szerződés neve üzeneteket

Ha úgy döntött, hogy X12 kódolása üzenetek szerződés neve, nyissa meg a **X12 neve megállapodás** listában, adja meg vagy válassza ki a meglévő X12 szerződést. Adja meg az XML-üzenet kódolására.

![Adja meg a X12 szerződésnév és kódolni XML-üzenet](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kódolja X12 identitások üzenetek

Ha úgy dönt, hogy X12 kódolása üzenetek identitások, adja meg a küldő azonosítója, a küldő minősítő, a fogadó azonosítója és a fogadó minősítő a X12 a szerződést. Válassza ki a kódolni XML üzenetet.
   
![Adja meg a küldő és fogadó identitásainak, válassza ki a kódolni XML-üzenet](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 részletek kódolása

A X12 Encode összekötő az alábbi feladatokat hajtja végre:

* A szerződés feloldási összekapcsolja a küldő és fogadó környezeti tulajdonságok.
* XML-kódolású üzenetekkel konvertálásakor EDI tranzakció halmazok adatcsere EDI adatcsere rendezi sorba.
* Tranzakció set fejléc és pótkocsi szegmensek vonatkozik
* Az adatcsere ellenőrző szám, a vezérlő csoportszámmal és a tranzakció beállítása vezérlő száma minden kimenő adatcserét hoz létre
* A felváltja elválasztók a hasznos adatban
* Érvényesíti EDI- és erőforráspartner jellemző tulajdonságok
  * A tranzakció-set adatelemek szemben az üzenet séma séma érvényesítése
  * EDI érvényesítési tranzakció-set adatelemek végre.
  * Tranzakció-set adatelemek végre kiterjesztett érvényesítése
* A műszaki és/vagy funkcionális visszaigazolás-kérelmek (Ha be van állítva).
  * A műszaki visszaigazolás miatt érvényesítési fejléc állít elő. A műszaki nyugtázást jelentések tartozó az adatcsere fejléc és a cím címzett feldolgozási állapota
  * A működési visszaigazolás miatt törzs érvényesítési állít elő. A működési nyugtázást jelentést minden egyes hiba történt a kapott dokumentumot

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/x12/). 

## <a name="next-steps"></a>Következő lépések
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag") 

