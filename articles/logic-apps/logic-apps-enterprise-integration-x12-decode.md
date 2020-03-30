---
title: X12 üzenetek dekódolása
description: Edi ellenőrzése és nyugtázás létrehozása az X12-es üzenetdekóderrel az Azure Logic Apps vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792367"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>X12-üzenetek dekódolása az Azure Logic Apps alkalmazásban enterprise integration pack csomaggal

A Decode X12 üzenetösszekötővel érvényesítheti a borítékot egy kereskedelmi partneri megállapodás alapján, érvényesítheti az EDI- és partnerspecifikus tulajdonságokat, tranzakciókészletekre oszthatja az adatcserecsoportokat, vagy megőrizheti a teljes csomópontokat, és nyugtákat hozhat létre feldolgozott tranzakciók esetében. Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő eseményindítóhoz a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Itt vannak a szükséges elemek:

* Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók,](logic-apps-enterprise-integration-create-integration-account.md) amely már definiálva van, és az Azure-előfizetéshez társítva van. A Decode X12 üzenetösszekötő használatához integrációs fiókkal kell rendelkeznie.
* Legalább két [partner,](logic-apps-enterprise-integration-partners.md) akik már definiálva vannak az integrációs fiókban
* Az integrációs fiókban már definiált [X12-megállapodás](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>X12 üzenetek dekódolása

1. [Hozzon létre egy logikai alkalmazást.](quickstart-create-first-logic-app-workflow.md)

2. A Decode X12 üzenetösszekötő nem rendelkezik eseményindítók, ezért hozzá kell adnia egy eseményindító a logikai alkalmazás indításához, például egy kérelem eseményindító. A Logic App Designer, adjon hozzá egy eseményindítót, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3.  A keresőmezőbe írja be a szűrő "x12" betűjét. Válassza az **X12 - Decode X12 üzenet**lehetőséget.
   
    ![Keresés az "x12" kifejezésre](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Ha korábban nem hozlétre kapcsolatot az integrációs fiókkal, a rendszer most kéri a kapcsolat létrehozását. Nevezze el a kapcsolatot, és válassza ki a csatlakoztatni kívánt integrációs fiókot. 

    ![Integrációs fiók kapcsolatának részleteinek biztosítása](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    A csillaggal rendelkező tulajdonságok szükségesek.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen. |

5.  Ha elkészült, a kapcsolat részleteinek ehhez a példához hasonlóan kell kinézniük. A kapcsolat létrehozásának befejezéséhez válassza a **Létrehozás gombot.**
   
    ![integrációs fiók kapcsolatának részletei](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. A kapcsolat létrehozása után, ahogy az ebben a példában látható, válassza ki a dekódolni kívánt X12-es egyeníró fájlüzenetet.

    ![integrációs fiók kapcsolat a létrehozva](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Példa:

    ![X12-es lapos fájlüzenet kijelölése dekódoláshoz](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > A tényleges üzenet tartalom vagy hasznos adat az üzenet tömb, jó vagy rossz, base64 kódolt. Ezért meg kell adnia egy kifejezést, amely feldolgozza ezt a tartalmat.
   > Íme egy példa, amely a tartalmat XML-ként dolgozza fel, amelyet kódnézetben vagy a tervező ben a kifejezésszerkesztő használatával adhat meg.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Példa tartalomra](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 Részletek dekódolása

Az X12 Decode összekötő a következő feladatokat hajtja végre:

* Érvényesíti a borítékot a kereskedelmi partneri megállapodással szemben
* Érvényesíti az EDI- és partnerspecifikus tulajdonságokat
  * EDI szerkezeti érvényesítés és kiterjesztett séma-érvényesítés
  * A cserekeret szerkezetének érvényesítése.
  * A boríték séma-ellenőrzése a vezérlősémával szemben.
  * A tranzakciókészlet-adatelemek sémaellenőrzése az üzenetsémával szemben.
  * EDI-érvényesítés tranzakció-set adatelemeken 
* Ellenőrzi, hogy az átcserélési, csoport- és tranzakciókészlet-vezérlőszámok nem ismétlődnek-e
  * Ellenőrzi a bankközi ellenőrzőszámot a korábban fogadott interchange-k alapján.
  * Összeveti a csoportvezérlőszámot a csomópontban lévő többi csoportvezérlőszámmal.
  * Összeveti a tranzakciókészlet vezérlőszámát az adott csoport ban lévő többi tranzakciókészlet-vezérlőszámmal.
* A csomópontot tranzakciókészletekre osztja fel, vagy megőrzi a teljes adatcserét:
  * Az Interchange felosztása tranzakciókészletekként – tranzakciókészletek felfüggesztése hiba esetén: Tranzakciókészletekre bontja a csomópontot, és elemzi az egyes tranzakciókészleteket. 
  Az X12 Dekódolás művelet csak azokat a `badMessages`tranzakciókészleteket adja ki, `goodMessages`amelyek nem érvényesítése sikertelen , és a fennmaradó tranzakciókészleteket adja ki .
  * Az Interchange felosztása tranzakciókészletekként – hiba esetén a csomópont felfüggesztése: Tranzakciókészletekre bontja a csomópontot, és elemzi az egyes tranzakciókészleteket. 
  Ha egy vagy több tranzakciókészlet sikertelen a csomópontban, az X12 Dekódolás művelet az `badMessages`adatcsere összes tranzakciókészletét kimenetelként adja meg.
  * Adatcsere megőrzése – tranzakciókészletek felfüggesztése hiba esetén: A csomópont megőrzése és a teljes kötegelt adatcsere feldolgozása. 
  Az X12 Dekódolás művelet csak azokat a `badMessages`tranzakciókészleteket adja ki, `goodMessages`amelyek nem érvényesítése sikertelen , és a fennmaradó tranzakciókészleteket adja ki .
  * Adatcsere megőrzése - a csomópont felfüggesztése hiba esetén: A csomópont megőrzése és a teljes kötegelt adatcsere feldolgozása. 
  Ha egy vagy több tranzakciókészlet sikertelen a csomópontban, az X12 Dekódolás művelet az `badMessages`adatcsere összes tranzakciókészletét kimenetelként adja meg. 
* Technikai és/vagy funkcionális nyugtát hoz létre (ha be van állítva).
  * A technikai nyugtázás a fejléc érvényesítése eredményeként jön létre. A technikai nyugtázás a csomópontfejléc és pótkocsi címfogadó általi feldolgozásának állapotát jelenti.
  * A funkcionális nyugtázás a törzs érvényesítése eredményeként jön létre. A funkcionális nyugtázás minden olyan hibát jelent, amely a fogadott dokumentum feldolgozása során történt

## <a name="view-the-swagger"></a>Tekintse meg a swagger
Lásd a [swagger részleteket](/connectors/x12/). 

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack csomagról](../logic-apps/logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról") 

