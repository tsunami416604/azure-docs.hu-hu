---
title: "EDIFACT üzenetek - Azure Logic Apps kódolása |} Microsoft Docs"
description: "EDI érvényesítése és EDIFACT üzenetkódoló a vállalati integrációs csomagban az XML létrehozása az Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 799d444632b67788520be8a777ec656076022583
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Az Azure Logic Apps a vállalati integrációs csomaggal EDIFACT üzenetek kódolása

EDIFACT kódolása üzenet összekötő EDI és a partner jellemző tulajdonságok ellenőrzése, az XML-dokumentum az egyes tranzakciót létrehozni és műszaki nyugtázási vagy működési visszaigazolás kérése.
Ez az összekötő használatához fel kell vennie az összekötő egy meglévő elindítani a Logic Apps alkalmazást.

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társítva. A kódolás EDIFACT üzenet összekötő használatához integrációs fiókkal kell rendelkeznie. 
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban
* Egy [EDIFACT megállapodás](logic-apps-enterprise-integration-edifact.md) , amely már definiálva van az integráció-fiókban

## <a name="encode-edifact-messages"></a>EDIFACT üzenetek kódolása

1. [Logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md).

2. A kódolás EDIFACT üzenet összekötő nincs eseményindítók, hozzá kell adni egy eseményindító indítása el a logikai alkalmazás, például a kérelem eseményindítót. A Logic App-tervezőben, vegye fel egy eseményindítót, majd egy műveletet a logikai alkalmazáshoz.

3.  A keresési mezőbe írja be a "EDIFACT" szűrőként. Válassza **EDIFACT üzenetből kódolása szerződésnév** vagy **Encode EDIFACT üzenet által identitások**.
   
    ![EDIFACT keresése](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Integráció fiókjába korábban a kapcsolatokat nem hozott létre, ha a program kéri, most, hogy a kapcsolat létrehozásához. A kapcsolat neve, és válassza ki a integrációs fiókot, amely csatlakozni szeretne.

    ![integráció fiók kapcsolat létrehozása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
    | Integráció fiók * |Adja meg a integrációs fiók nevét. Ellenőrizze, hogy az integrációs fiók és a logikai alkalmazást az Azure ugyanazon a helyen. |

5.  Amikor elkészült, a kapcsolódási adatait. Ez a példa hasonlóan kell kinéznie. Válassza ki a kapcsolat létrehozásának befejezéséhez **létrehozása**.

    ![integráció fiók kapcsolódási adatait.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A kapcsolat létrejön.

    ![integráció fiók kapcsolat létrehozása](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>EDIFACT üzenetek kódolása szerződés neve

Ha úgy döntött, hogy EDIFACT üzenetek kódolása szerződés neve, nyissa meg a **nevét a EDIFACT megállapodás** listában, adja meg vagy válassza ki a EDIFACT a szerződés nevét. Adja meg az XML-üzenet kódolására.

![Adja meg a EDIFACT szerződés neve és kódolni XML-üzenet](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>EDIFACT üzenetek által identitások kódolása

Ha EDIFACT üzenetek kódolása által identitások választja, adja meg a küldő azonosítója, a küldő minősítő, a fogadó azonosítója és a fogadó minősítő a EDIFACT megállapodás be. Válassza ki a kódolni XML üzenetet.

![Adja meg a küldő és fogadó identitásainak, válassza ki a kódolni XML-üzenet](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT kódolása részletei

A kódolási EDIFACT-összekötő az alábbi feladatokat hajtja végre: 

* A szerződés feloldani a küldő minősítő & azonosítója és a fogadó minősítő és az azonosítója
* XML-kódolású üzenetekkel konvertálásakor EDI tranzakció halmazok adatcsere EDI adatcsere rendezi sorba.
* Tranzakció set fejléc és pótkocsi szegmensek vonatkozik
* Az adatcsere ellenőrző szám, a vezérlő csoportszámmal és a tranzakció beállítása vezérlő száma minden kimenő adatcserét hoz létre
* A felváltja elválasztók a hasznos adatban
* Érvényesíti EDI- és erőforráspartner jellemző tulajdonságok
  * A sémaérvényesítés a tranzakció-set adatelemek az üzenet sémának.
  * EDI érvényesítési tranzakció-set adatelemek végre.
  * Tranzakció-set adatelemek végre kiterjesztett érvényesítése
* Az XML-dokumentum az egyes tranzakció állít elő.
* A műszaki és/vagy funkcionális visszaigazolás-kérelmek (Ha be van állítva).
  * A műszaki visszaigazolás, mint a CONTRL üzenet azt jelzi, az cseréje fogadását.
  * A működési visszaigazolás, mint a CONTRL az üzenet azt jelzi, elfogadása vagy elutasítása a fogadott interchange, csoport vagy üzenet, hibák vagy nem támogatott funkciók listáját

## <a name="view-swagger-file"></a>A Swagger-fájl megtekintése
A Swagger adatai a EDIFACT-összekötő megtekintése: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>További lépések
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag") 

