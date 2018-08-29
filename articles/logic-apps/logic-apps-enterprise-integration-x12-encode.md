---
title: Kódolás X12 üzeneteket – Azure Logic Apps |} A Microsoft Docs
description: EDI ellenőrzése és átalakítása XML-ként kódolt üzenetek X12 az üzenetet az Azure Logic Apps Enterprise Integration Pack-kódoló
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 3ed5cb61fef5f07913f11c4e4df309d720d5b901
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123505"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kódolás X12 üzeneteket az Azure Logic Appsben és Enterprise Integration Pack

A Encode X12 üzenet Connector EDI és partneri jellemző tulajdonságok ellenőrzése, EDI tranzakciókészletek az adatcsere XML-ként kódolt üzenetek alakíthatja, és technikai nyugtázási vagy működési nyugtázása kérése.
Az összekötő használatához hozzá kell adnia az összekötő egy meglévő eseményindítót a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított. A Encode X12 üzenet connector használatához egy integrációs fiókhoz kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók
* Egy [X12 szerződés](logic-apps-enterprise-integration-x12.md) , amely már definiálva van az integrációs fiók

## <a name="encode-x12-messages"></a>Kódolás X12 üzenetek

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. A Encode X12 üzenet connector nincs eseményindítók, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása. A Logic App Designerben az eseményindító hozzáadása, és adja hozzá a művelet a logikai alkalmazáshoz.

3.  A keresőmezőbe adja meg a szűrőnek "x12". A következők közül választhat **X12 kódolás X12-üzenetbe egyezménynév szerint** vagy **X12 kódolás X12-üzenetbe identitások szerint**.
   
    ![Keressen a "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Korábban létrehozott kapcsolatokat az integrációs fiókba, most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, és válassza ki az integrációs fiók, amely kapcsolódni szeretne. 
   
    ![integrációs fiók kapcsolat](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat bármilyen nevet. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon Azure-helyen. |

5.  Ha elkészült, a kapcsolat adatait példához hasonlóan kell kinéznie. A kapcsolat létrehozásának befejezéséhez válasszon **létrehozás**.

    ![integrációs fiók kapcsolat létrehozása](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Ekkor létrejön a kapcsolat.

    ![integrációs fiók kapcsolat részletei](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kódolás X12 üzenetek egyezménynév szerint

Ha úgy döntött, hogy X12 kódolása üzenetek szerződés neve, nyissa meg a **X12 nevére szerződés** listában adja meg vagy válassza ki a meglévő X12 szerződés. Adja meg az XML-üzenet kódolása.

![Adja meg a X12 szerződés neve és az XML-üzenet kódolása](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>X12 kódolása identitások szerint üzenetek

Ha úgy dönt, hogy X12 kódolása identitások, szerint üzeneteket adja meg a feladó azonosítója, a feladó minősítője, a címzett azonosítója és a címzett minősítője a X12 konfigurált szerződés. Válassza ki a kódolni kívánt XML-üzenet.
   
![Adja meg a küldő és fogadó identitásainak, válassza ki a kódolni kívánt XML-üzenet](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 kódolása részletei

A X12 Encode összekötő az alábbi feladatokat hajtja végre:

* Egyezményt egyeztetése küldői és fogadói környezeti tulajdonsága alapján.
* Szerializálja a EDI adatcsere, EDI tranzakciókészletek az adatcsere XML-ként kódolt üzenetek történő konvertálásakor.
* Tranzakció set fejléc és bemutató szegmensek vonatkozik
* Létrehoz egy adatcsere-ellenőrzőszám, a csoport-ellenőrzőszám és a tranzakciókészlet ellenőrzőszáma minden kimenő adatcserét
* A hasznos adatok elválasztóként váltja fel
* EDI- és partner jellemző tulajdonságok ellenőrzése
  * Az üzenet séma elleni tranzakciókészlet adatelemet séma érvényesítése
  * EDI-ellenőrzés végrehajtani a tranzakciókészlet adatelemeket.
  * A tranzakciókészlet adatelem végrehajtott bővített ellenőrzés
* A műszaki és/vagy funkcionális visszaigazolás-lekérdezések (Ha be van állítva).
  * A technikai visszaigazolás fejléc érvényesítési eredményeként létrehozott. A technikai nyugtázást jelentések egy adatcsere fejlécet és a cím fogadó bemutató feldolgozását állapota
  * A működési visszaigazolás törzs érvényesítési eredményeként létrehozott. A működési nyugtázást jelentések minden egyes hibához a kapott dokumentumot feldolgozásakor

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/x12/). 

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag") 

