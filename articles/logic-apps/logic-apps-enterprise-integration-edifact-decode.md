---
title: EDIFACT-üzenetek – Azure Logic Apps-dekódolást |} A Microsoft Docs
description: EDI ellenőrzése és nyugtázását az EDIFACT-üzenet dekódoló az Azure Logic Apps Enterprise Integration Pack-a
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.date: 01/27/2017
ms.openlocfilehash: b101922d15a3f90c29eff51c223d2ea7dc30ddf2
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125352"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Az Azure Logic Apps és az Enterprise Integration Pack EDIFACT-üzenetek dekódolása

Az EDIFACT-dekódolást üzenet Connector EDI és partneri jellemző tulajdonságok ellenőrzése, tranzakciók csoportokba adatcsere felosztása vagy a teljes adatcsere megőrzése és feldolgozott tranzakciók visszaigazoló üzenetet létrehozni. Az összekötő használatához hozzá kell adnia az összekötő egy meglévő eseményindítót a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított. Az EDIFACT-dekódolást üzenet connector használatához egy integrációs fiókhoz kell rendelkeznie. 
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók
* Egy [EDIFACT-egyezmény](logic-apps-enterprise-integration-edifact.md) , amely már definiálva van az integrációs fiók

## <a name="decode-edifact-messages"></a>EDIFACT-üzenetek dekódolása

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. Az EDIFACT-dekódolást üzenet connector eseményindítók, nem rendelkezik, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása. A Logic App Designerben az eseményindító hozzáadása, és adja hozzá a művelet a logikai alkalmazáshoz.

3. A Keresés mezőbe írja be szűrőként "EDIFACT". Válassza ki **EDIFACT-üzenet dekódolása**.
   
    ![EDIFACT keresése](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Korábban létrehozott kapcsolatokat az integrációs fiókba, most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, és válassza ki az integrációs fiók, amely kapcsolódni szeretne.
   
    ![integrációs fiók létrehozása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat bármilyen nevet. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon Azure-helyen. |

4. Ha elkészült, a a kapcsolat létrehozásának befejezéséhez válasszon **létrehozás**. A kapcsolat adatait példához hasonlóan kell kinéznie:

    ![integrációs fiók adatai](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Ha a kapcsolat létrejött, ebben a példában látható módon, válassza ki az egybesimított fájlos EDIFACT-üzenet dekódolása.

    ![integrációs fiók kapcsolat létrehozása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Példa:

    ![Válassza ki az egybesimított fájlos EDIFACT-üzenet dekódolása számára](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT-dekódoló részletei

Az EDIFACT-dekódolást-összekötő az alábbi feladatokat hajtja végre: 

* Ellenőrzi a boríték kereskedelmi partneri szerződés ellen.
* A megállapodás feloldása egyeztetése a feladó minősítője & azonosító és a címzett minősítője & azonosítója alapján.
* Oszt fel egy adatcsere több tranzakcióra, ha az adatcsere fogadni a konfigurációs beállítások több mint egy-egy tranzakció a szerződés alapján.
* Az adatcsere visszafejti.
* Ellenőrzi, EDI és a partner-specifikus tulajdonságokat, többek között:
  * Az adatcsere-boríték struktúra érvényesítése
  * A vezérlő sémának a boríték séma érvényesítése
  * Az üzenet séma tranzakciókészlet adatelemet séma érvényesítése
  * A tranzakciókészlet adatelem végrehajtott EDI-ellenőrzés
* Ellenőrzi, hogy az adatcsere, a csoport és a tranzakciós set ellenőrzőszámok nem azonosak (Ha be van állítva) 
  * Ellenőrzi, az adatcsere-ellenőrzőszám korábban fogadott adatcsere ellen. 
  * A csoport-ellenőrzőszám szemben a többi csoport ellenőrzőszámok az adatcsere ellenőrzi. 
  * Ellenőrzi, hogy a tranzakciókészlet ellenőrzőszáma egyéb tranzakció beállítása, hogy a csoportban ellenőrzőszámok ellen.
* A tranzakció csoportokba adatcsere felosztása, vagy megőrzi a teljes adatcsere:
  * Adatcsere felosztása tranzakciókészletekre – tranzakciókészletek felfüggesztése hiba esetén: tranzakció az elágazást adatcsere állítja be, és minden egyes tranzakciókészlet elemzi. 
  A X12 dekódolási műveleti kimenetek csak azokat a tranzakció állítja be, amelyek sikertelen érvényesítést `badMessages`, és beállítja a fennmaradó tranzakciók kimenetek `goodMessages`.
  * Adatcsere felosztása tranzakciókészletekre – adatcsere felfüggesztése hiba esetén: tranzakció az elágazást adatcsere állítja be, és minden egyes tranzakciókészlet elemzi. 
  Ha egy vagy több tranzakció beállítja az adatcsere sikertelen ellenőrzést követően az dekódolási műveleti kimenetek összes tranzakció beállítja, hogy az adatcsere X12 `badMessages`.
  * Adatcsere megőrzése – tranzakciókészletek felfüggesztése hiba esetén: az adatcsere, és a teljes kötegelt adatcsere feldolgozása. 
  A X12 dekódolási műveleti kimenetek csak azokat a tranzakció állítja be, amelyek sikertelen érvényesítést `badMessages`, és beállítja a fennmaradó tranzakciók kimenetek `goodMessages`.
  * Adatcsere megőrzése – adatcsere felfüggesztése hiba esetén: az adatcsere, és a teljes kötegelt adatcsere feldolgozása. 
  Ha egy vagy több tranzakció beállítja az adatcsere sikertelen ellenőrzést követően az dekódolási műveleti kimenetek összes tranzakció beállítja, hogy az adatcsere X12 `badMessages`.
* Állít elő egy műszaki (vezérlő) és/vagy működési nyugtázása (Ha be van állítva).
  * A műszaki visszaigazolás vagy a CONTRL ACK-jelentések egy teljes kapott adatcsere szintaktikai ellenőrzés eredményeit.
  * A működési visszaigazolás nyugtázza elfogadása vagy elutasítása kapott adatcsere vagy felhasználói csoport

## <a name="view-swagger-file"></a>A Swagger-fájl megtekintése
Az EDIFACT-összekötő Swagger részleteinek megtekintéséhez: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag") 

