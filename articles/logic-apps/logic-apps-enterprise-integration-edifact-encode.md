---
title: Kódolás EDIFACT-üzenetek – Azure Logic Apps |} A Microsoft Docs
description: EDI ellenőrzése és XML létrehozása az Azure Logic Apps Enterprise Integration Pack-EDIFACT-üzenet Encoder
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.date: 01/27/2017
ms.openlocfilehash: e1c990030a9fa9ad25950ccb24b36b82a8c69f5c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122353"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Az Azure Logic Apps Enterprise Integration Pack az EDIFACT-üzenetek kódolása

A kódolás EDIFACT-üzenet connector az EDI és partneri jellemző tulajdonságok ellenőrzése, hozzon létre egy XML-dokumentumot, az egyes tranzakciók, és technikai nyugtázási vagy működési nyugtázása kérése.
Az összekötő használatához hozzá kell adnia az összekötő egy meglévő eseményindítót a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított. A kódolás EDIFACT-üzenet connector használatához egy integrációs fiókhoz kell rendelkeznie. 
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók
* Egy [EDIFACT-egyezmény](logic-apps-enterprise-integration-edifact.md) , amely már definiálva van az integrációs fiók

## <a name="encode-edifact-messages"></a>EDIFACT-üzenetek kódolása

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. A kódolás EDIFACT-üzenet connector eseményindítók, nem rendelkezik, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása. A Logic App Designerben az eseményindító hozzáadása, és adja hozzá a művelet a logikai alkalmazáshoz.

3.  A Keresés mezőbe írja be szűrőként "EDIFACT". Ezek közül bármelyikre **kódolás EDIFACT-üzenetbe egyezménynév szerint** vagy **EDIFACT-üzenetbe identitások szerint Encode**.
   
    ![EDIFACT keresése](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Korábban létrehozott kapcsolatokat az integrációs fiókba, most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, és válassza ki az integrációs fiók, amely kapcsolódni szeretne.

    ![integrációs fiók kapcsolat létrehozása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat bármilyen nevet. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon Azure-helyen. |

5.  Ha elkészült, a kapcsolat adatait példához hasonlóan kell kinéznie. A kapcsolat létrehozásának befejezéséhez válasszon **létrehozás**.

    ![integrációs fiók kapcsolat részletei](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Ekkor létrejön a kapcsolat.

    ![integrációs fiók kapcsolat létrehozása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Kódolás EDIFACT-üzenetbe egyezménynév szerint

Ha úgy döntött, hogy egyezménynév szerint EDIFACT-üzenetek kódolása, nyissa meg a **nevét az EDIFACT-egyezmény** listában adja meg vagy válassza ki a EDIFACT-egyezmény neve. Adja meg az XML-üzenet kódolása.

![Adja meg az EDIFACT-egyezmény neve és az XML-üzenet kódolása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kódolás EDIFACT-üzenetbe identitások szerint

Ha EDIFACT-üzenetek kódolása identitások szerint választja, adja meg a feladó azonosítója, a feladó minősítője, a címzett azonosítója és a címzett minősítője az EDIFACT-egyezmény konfigurált. Válassza ki a kódolni kívánt XML-üzenet.

![Adja meg a küldő és fogadó identitásainak, válassza ki a kódolni kívánt XML-üzenet](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT-kódolást részletei

A kódolás EDIFACT-összekötő az alábbi feladatokat hajtja végre: 

* A megállapodás feloldása összekapcsolja a feladó minősítője & azonosítója és a címzett minősítője és azonosítója
* Szerializálja a EDI adatcsere, EDI tranzakciókészletek az adatcsere XML-ként kódolt üzenetek történő konvertálásakor.
* Tranzakció set fejléc és bemutató szegmensek vonatkozik
* Létrehoz egy adatcsere-ellenőrzőszám, a csoport-ellenőrzőszám és a tranzakciókészlet ellenőrzőszáma minden kimenő adatcserét
* A hasznos adatok elválasztóként váltja fel
* EDI- és partner jellemző tulajdonságok ellenőrzése
  * Séma érvényesítése a üzenet sémának tranzakciókészlet adatelemet.
  * EDI-ellenőrzés végrehajtani a tranzakciókészlet adatelemeket.
  * A tranzakciókészlet adatelem végrehajtott bővített ellenőrzés
* Létrehoz egy XML-dokumentumot, az egyes tranzakció.
* A műszaki és/vagy funkcionális visszaigazolás-lekérdezések (Ha be van állítva).
  * A technikai visszaigazolás, mint a CONTRL üzenet azt jelzi, az adatcsere kézhezvételét.
  * A működési visszaigazolás, mint a CONTRL az üzenet azt jelzi, elfogadási vagy elutasítási a fogadott adatcsere, csoport vagy üzenet, a hibák vagy nem támogatott funkciók listáját

## <a name="view-swagger-file"></a>A Swagger-fájl megtekintése
Az EDIFACT-összekötő Swagger részleteinek megtekintéséhez: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag") 

