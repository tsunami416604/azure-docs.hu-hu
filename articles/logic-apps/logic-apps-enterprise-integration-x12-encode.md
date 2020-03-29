---
title: X12 üzenetek kódolása
description: Edi ellenőrzése és XML-kódolású üzenetek konvertálása X12-es üzenetkódolóval az Azure Logic Apps enterprise integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792347"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-üzenetek kódolása az Azure Logic Apps alkalmazásban enterprise integration pack csomaggal

Az Encode X12 üzenetösszekötővel ellenőrizheti az EDI- és partnerspecifikus tulajdonságokat, XML-kódolású üzeneteket konvertálhat EDI-tranzakciókészletekké a csomópontban, és technikai nyugtázást, funkcionális nyugtázást vagy mindkettőt kérhet.
Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő eseményindítóhoz a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Itt vannak a szükséges elemek:

* Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók,](logic-apps-enterprise-integration-create-integration-account.md) amely már definiálva van, és az Azure-előfizetéshez társítva van. Az X12 kódolási üzenetösszekötő használatához integrációs fiókkal kell rendelkeznie.
* Legalább két [partner,](logic-apps-enterprise-integration-partners.md) akik már definiálva vannak az integrációs fiókban
* Az integrációs fiókban már definiált [X12-megállapodás](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>X12 üzenetek kódolása

1. [Hozzon létre egy logikai alkalmazást.](quickstart-create-first-logic-app-workflow.md)

2. Az X12-es üzenetösszekötő nem rendelkezik eseményindítókkal, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérelemeseményindítót. A Logic App Designer, adjon hozzá egy eseményindítót, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3.  A keresőmezőbe írja be a szűrő "x12" betűjét. Válassza az **X12 - Kódolás X12-üzenetbe a szerződés neve** vagy **az X12 - Kódoljon X12 üzenetbe identitások szerint.**
   
    ![Keresés az "x12" kifejezésre](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Ha korábban nem hozlétre kapcsolatot az integrációs fiókkal, a rendszer most kéri a kapcsolat létrehozását. Nevezze el a kapcsolatot, és válassza ki a csatlakoztatni kívánt integrációs fiókot. 
   
    ![integrációs fiók kapcsolata](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    A csillaggal rendelkező tulajdonságok szükségesek.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen. |

5.  Ha elkészült, a kapcsolat részleteinek ehhez a példához hasonlóan kell kinézniük. A kapcsolat létrehozásának befejezéséhez válassza a **Létrehozás gombot.**

    ![integrációs fiók kapcsolat a létrehozva](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A kapcsolat létrejött.

    ![integrációs fiók kapcsolatának részletei](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>X12 üzenetek kódolása szerződésnév szerint

Ha úgy döntött, hogy az X12-üzeneteket szerződésnév szerint kódolja, nyissa meg az **X12-szerződés neve** listát, írja be vagy válassza ki a meglévő X12-szerződést. Írja be a kódolni hozandó XML-üzenetet.

![Írja be az X12-szerződés nevét és a kódolni hozandó XML-üzenetet](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>X12 üzenetek kódolása identitások szerint

Ha úgy dönt, hogy az X12-üzeneteket identitások szerint kódolja, adja meg a feladó azonosítóját, a feladói minősítőt, a fogadóazonosítót és a fogadóminősítőt az X12-szerződésben konfigurált módon. Jelölje ki a kódolni kívánt XML-üzenetet.
   
![Identitások biztosítása a feladó és a címzett számára, válassza a kódolni kívánt XML-üzenet lehetőséget](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 Részletek kódolása

Az X12 Kódolás irava a következő feladatokat hajtja végre:

* Megállapodás feloldása a küldő és a fogadó környezet tulajdonságainak egyeztetésével.
* Szerializálja az EDI-csomópontot, és az XML-kódolású üzeneteket EDI tranzakciókészletekké alakítja a csomópontban.
* Tranzakciókészlet fejléc- és utánfutószegmenseinek alkalmazása
* Létrehoz egy csomópont-vezérlőszámot, egy csoportvezérlőszámot és egy tranzakciókészlet-vezérlőszámot minden kimenő csomóponthoz.
* Lecseréli az elválasztókat a hasznos adatban
* Érvényesíti az EDI- és partnerspecifikus tulajdonságokat
  * A tranzakciókészlet-adatelemek sémaellenőrzése a Séma üzenettel szemben
  * EDI-érvényesítés tranzakció-set adatelemeken.
  * Kiterjesztett érvényesítés a tranzakciókészlet-adatelemeken
* Technikai és/vagy funkcionális nyugtázást kér (ha be van állítva).
  * A technikai nyugtázás a fejléc érvényesítése eredményeként jön létre. A műszaki nyugtázás a csomópontfejléc és pótkocsi címfogadó általi feldolgozásának állapotát jelenti
  * A funkcionális nyugtázás a törzs érvényesítése eredményeként jön létre. A funkcionális nyugtázás minden olyan hibát jelent, amely a fogadott dokumentum feldolgozása során történt

## <a name="view-the-swagger"></a>Tekintse meg a swagger
Lásd a [swagger részleteket](/connectors/x12/). 

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack csomagról](logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról") 

