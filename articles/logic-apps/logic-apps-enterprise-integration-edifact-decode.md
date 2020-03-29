---
title: EDIFACT üzenetek dekódolása
description: Edi ellenőrzése és nyugtázás létrehozása az Azure Logic Apps enterprise integrációs csomaggal rendelkező EDIFACT üzenetdekódolójával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790665"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>EdIFACT üzenetek dekódolása az Azure Logic Apps alkalmazáshoz a vállalati integrációs csomaggal

A Decode EDIFACT üzenetösszekötővel érvényesítheti az EDI- és partnerspecifikus tulajdonságokat, tranzakciókészletekre oszthatja az adatcserecsoportokat, vagy megőrizheti a teljes csomópontokat, és nyugtákat hozhat létre a feldolgozott tranzakciókhoz. Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő eseményindítóhoz a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Itt vannak a szükséges elemek:

* Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók,](logic-apps-enterprise-integration-create-integration-account.md) amely már definiálva van, és az Azure-előfizetéshez társítva van. A Decode EDIFACT üzenetösszekötő használatához integrációs fiókkal kell rendelkeznie. 
* Legalább két [partner,](logic-apps-enterprise-integration-partners.md) akik már definiálva vannak az integrációs fiókban
* [EdIFACT-megállapodás,](logic-apps-enterprise-integration-edifact.md) amely már definiálva van az integrációs fiókban

## <a name="decode-edifact-messages"></a>EDIFACT üzenetek dekódolása

1. [Hozzon létre egy logikai alkalmazást.](quickstart-create-first-logic-app-workflow.md)

2. A Decode EDIFACT üzenetösszekötő nem rendelkezik eseményindítók, ezért hozzá kell adnia egy eseményindító a logikai alkalmazás indításához, például egy kérelem eseményindító. A Logic App Designer, adjon hozzá egy eseményindítót, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3. A keresőmezőbe írja be szűrőként az "EDIFACT" kifejezést. Válassza **az EDIFACT üzenet dekódolása lehetőséget.**
   
    ![keresés EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Ha korábban nem hozlétre kapcsolatot az integrációs fiókkal, a rendszer most kéri a kapcsolat létrehozását. Nevezze el a kapcsolatot, és válassza ki a csatlakoztatni kívánt integrációs fiókot.
   
    ![integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    A csillaggal rendelkező tulajdonságok szükségesek.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen. |

4. Ha végzett a kapcsolat létrehozásának befejezésével, válassza a **Létrehozás gombot.** A kapcsolat részleteinek a következő példához hasonlóan kell kinézniük:

    ![integrációs fiók adatai](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. A kapcsolat létrehozása után, ahogy az ebben a példában látható, válassza ki a dekódolni kívánt EDIFACT egyfájlos üzenetet.

    ![integrációs fiók kapcsolat a létrehozva](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Példa:

    ![Válassza az EDIFACT egyfájlos üzenetet a dekódoláshoz](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT dekóder részletei

A Decode EDIFACT összekötő a következő feladatokat hajtja végre: 

* Érvényesíti a borítékot a kereskedelmi partneri megállapodással szemben.
* A szerződés megoldása a küldő minősítő & azonosítójának és a fogadó azonosító &jának egyeztetésével.
* A csomópontot több tranzakcióra osztja fel, ha a megállapodás fogadási beállítások konfigurációja alapján egynél több tranzakcióval rendelkezik.
* Szétszereli a csomópontot.
* Ellenőrzi az EDI és a partnerspecifikus tulajdonságokat, beleértve a következőket:
  * A cserekeret szerkezetének érvényesítése
  * A boríték sémaellenőrzése a vezérlősémával szemben
  * A tranzakciókészlet-adatelemek sémaellenőrzése az üzenetsémával szemben
  * EDI-érvényesítés tranzakció-set adatelemeken
* Ellenőrzi, hogy az adatcsere-, csoport- és tranzakciókészlet-vezérlőszámok nem ismétlődnek-e (ha konfigurálva van) 
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
* Technikai (vezérlő) és/vagy funkcionális nyugtázást hoz létre (ha be van állítva).
  * A technical acknowledged eded or the CONTRL ACK reports the results of a syntactical check of the complete received interchange.
  * A funkcionális elismervény nyugtázza a fogadott bankközi vagy csoportos

## <a name="view-swagger-file"></a>Swagger-fájl megtekintése
Az EDIFACT-csatlakozó Swagger részleteinek megtekintéséhez lásd: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack csomagról](logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról") 

