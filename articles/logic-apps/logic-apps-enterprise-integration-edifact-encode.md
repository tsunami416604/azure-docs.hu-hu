---
title: EDIFACT üzenetek kódolása
description: Edi ellenőrzése és XML létrehozása AZ EDIFACT üzenetkódolóval az Azure Logic Apps vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 257cbd0b1a68ddd2b16235e6f8dec5d5b0eb10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790654"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>EDIFACT-üzenetek kódolása az Azure Logic Apps alkalmazáshoz enterprise integration pack csomaggal

Az Encode EDIFACT üzenetösszekötővel érvényesítheti az EDI- és partnerspecifikus tulajdonságokat, xml-dokumentumot hozhat létre minden egyes tranzakciókészlethez, és kérhet technikai nyugtázást, funkcionális nyugtázást vagy mindkettőt.
Az összekötő használatához hozzá kell adnia az összekötőt egy meglévő eseményindítóhoz a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

Itt vannak a szükséges elemek:

* Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók,](logic-apps-enterprise-integration-create-integration-account.md) amely már definiálva van, és az Azure-előfizetéshez társítva van. Az EDIFACT kódoló üzenetösszekötő használatához integrációs fiókkal kell rendelkeznie. 
* Legalább két [partner,](logic-apps-enterprise-integration-partners.md) akik már definiálva vannak az integrációs fiókban
* [EdIFACT-megállapodás,](logic-apps-enterprise-integration-edifact.md) amely már definiálva van az integrációs fiókban

## <a name="encode-edifact-messages"></a>EDIFACT üzenetek kódolása

1. [Hozzon létre egy logikai alkalmazást.](quickstart-create-first-logic-app-workflow.md)

2. Az EDIFACT-üzenetösszekötő nem rendelkezik eseményindítókkal, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérelemeseményindítót. A Logic App Designer, adjon hozzá egy eseményindítót, majd adjon hozzá egy műveletet a logikai alkalmazáshoz.

3.  A keresőmezőbe írja be szűrőként az "EDIFACT" kifejezést. Válassza az **EDIFACT-üzenet kódolása megállapodás név** vagy **az EDIFACT üzenet kódolása identitások szerint**lehetőséget.
   
    ![keresés EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Ha korábban nem hozlétre kapcsolatot az integrációs fiókkal, a rendszer most kéri a kapcsolat létrehozását. Nevezze el a kapcsolatot, és válassza ki a csatlakoztatni kívánt integrációs fiókot.

    ![integrációs fiók kapcsolatának létrehozása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    A csillaggal rendelkező tulajdonságok szükségesek.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat nevét. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon az Azure-helyen. |

5.  Ha elkészült, a kapcsolat részleteinek ehhez a példához hasonlóan kell kinézniük. A kapcsolat létrehozásának befejezéséhez válassza a **Létrehozás gombot.**

    ![integrációs fiók kapcsolatának részletei](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A kapcsolat létrejött.

    ![integrációs fiók kapcsolat a létrehozva](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>EDIFACT-üzenet kódolása szerződésnév szerint

Ha úgy döntött, hogy az EDIFACT üzeneteket a szerződés neve szerint kódolja, nyissa meg az **EDIFACT szerződés** neve listát, írja be vagy válassza ki az EDIFACT szerződés nevét. Írja be a kódolni hozandó XML-üzenetet.

![Kódolandó EDIFACT-szerződés nevének és XML-üzenetének megadása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>EDIFACT-üzenet kódolása identitások szerint

Ha úgy dönt, hogy az EDIFACT-üzeneteket identitások szerint kódolja, adja meg a feladó azonosítóját, a feladói minősítőt, a fogadóazonosítót és a fogadóminősítőt az EDIFACT-szerződésben konfigurált módon. Jelölje ki a kódolni kívánt XML-üzenetet.

![Identitások biztosítása a feladó és a címzett számára, válassza a kódolni kívánt XML-üzenet lehetőséget](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT Kódolás részletei

Az Encode EDIFACT összekötő a következő feladatokat hajtja végre: 

* A megállapodás megoldása a feladói minősítő & azonosítójának, a címzett minősítőjének és azonosítójának egyeztetésével
* Szerializálja az EDI-csomópontot, és az XML-kódolású üzeneteket EDI tranzakciókészletekké alakítja a csomópontban.
* Tranzakciókészlet fejléc- és utánfutószegmenseinek alkalmazása
* Létrehoz egy csomópont-vezérlőszámot, egy csoportvezérlőszámot és egy tranzakciókészlet-vezérlőszámot minden kimenő csomóponthoz.
* Lecseréli az elválasztókat a hasznos adatban
* Érvényesíti az EDI- és partnerspecifikus tulajdonságokat
  * A tranzakciókészlet-adatelemek sémaellenőrzése az üzenetsémával szemben.
  * EDI-érvényesítés tranzakció-set adatelemeken.
  * Kiterjesztett érvényesítés a tranzakciókészlet-adatelemeken
* XML-dokumentumot hoz létre minden tranzakciókészlethez.
* Technikai és/vagy funkcionális nyugtázást kér (ha be van állítva).
  * Technikai nyugtaként a CONTRL közlemény egy csomópont fogadását jelzi.
  * Funkcionális nyugtázásként a CONTRL közlemény a fogadott csomópont, csoport vagy üzenet elfogadását vagy elutasítását jelzi, a hibák vagy a nem támogatott funkciók listájával

## <a name="view-swagger-file"></a>Swagger-fájl megtekintése
Az EDIFACT-csatlakozó Swagger részleteinek megtekintéséhez lásd: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack csomagról](logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról") 

