---
title: AS2-üzenetek – Azure Logic Apps-dekódolást |} A Microsoft Docs
description: Az Azure Logic Apps és az Enterprise Integration Pack ÜZENETEKKÉNT-dekódolást.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 08/08/2018
ms.openlocfilehash: 06ffa6bddc1340ad548f9baf30eba65ba503bf73
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128281"
---
# <a name="decode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Az Azure Logic Apps és az Enterprise Integration Pack AS2-üzenetek dekódolása 

Biztonságának és megbízhatóságának közben továbbítására üzenetek létrehozásához használja az AS2-dekódolást üzenet connector. Ez az összekötő biztosít a digitális aláírás, visszafejtési és nyugtázás üzenet törlése értesítések (MDN) keresztül.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított. Az AS2-dekódolást üzenet connector használatához egy integrációs fiókhoz kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók
* Egy [AS2-egyezményt](logic-apps-enterprise-integration-as2.md) , amely már definiálva van az integrációs fiók

## <a name="decode-as2-messages"></a>AS2-üzenetek dekódolása

1. [Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Az AS2-dekódolást üzenet connector eseményindítók, nem rendelkezik, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása. A Logic App Designerben az eseményindító hozzáadása, és adja hozzá a művelet a logikai alkalmazáshoz.

3.  A keresőmezőbe adja meg a szűrőnek "AS2". Válassza ki **AS2 - dekódolást AS2-üzenet**.
   
    ![Keressen a "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Korábban létrehozott kapcsolatokat az integrációs fiókba, most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, és válassza ki az integrációs fiók, amely kapcsolódni szeretne.
   
    ![Integráció kapcsolat létrehozása](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat bármilyen nevet. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon Azure-helyen. |

5.  Ha elkészült, a kapcsolat adatait példához hasonlóan kell kinéznie. A kapcsolat létrehozásának befejezéséhez válasszon **létrehozás**.

    ![integráció a kapcsolat részletei](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. A kapcsolat létrejötte után ebben a példában látható módon, válassza ki **törzs** és **fejlécek** a kérelem-kimeneteket a.
   
    ![integráció kapcsolat létrehozva](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Példa:

    ![Válassza ki a szervezet és a fejlécek a kérelem kimenetek](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 


## <a name="as2-decoder-details"></a>AS2-dekódoló részletei

Az AS2-dekódolást-összekötő az alábbi feladatokat hajtja végre: 

* Dolgozza fel az AS2/HTTP-fejlécek
* Ellenőrzi az aláírást (Ha be van állítva)
* Mindig visszafejti az üzeneteket az (Ha be van állítva)
* Az üzenet kibontja (Ha be van állítva)
* Ellenőrizze és ismétlődésének üzenet azonosítója (Ha be van állítva)
* A fogadott MDN kimenő eredeti üzenettel összehangolja ezzel
* A frissítések letöltésének utal. az letagadhatatlanság adatbázis
* Az AS2-állapotjelentés rekordokat ír
* A kimeneti tartalom tartalma base64-kódolású
* Beállítja, hogy az MDN szükség, e lehet, hogy az MDN szinkron vagy aszinkron eseményadatokra konfigurációtól függően az AS2-megállapodás
* Létrehoz egy szinkron vagy aszinkron MDN (a szerződés konfigurációk alapján)
* Az MDN a korrelációs jogkivonatok és a tulajdonságok beállítása


  > [!NOTE]
  > Ha tanúsítványok kezelése az Azure Key Vault használ, győződjön meg arról, hogy konfigurálnia a kulcsokat, hogy lehetővé tegye a **visszafejtéséhez** műveletet.
  > Ellenkező esetben az AS2-dekódolást sikertelen lesz.
  >
  > ![Keyvault visszafejti](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Ez a minta kipróbálása

Próbálja meg egy teljesen működőképes logic app és a minta AS2 forgatókönyv üzembe helyezéséhez, tekintse meg a [AS2 logikaialkalmazás-sablon és a forgatókönyv](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/as2/). 

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 

