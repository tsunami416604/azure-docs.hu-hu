---
title: "EDIFACT üzenetek - Azure Logic Apps dekódolása |} Microsoft Docs"
description: "EDI érvényesítése, és a vállalati integrációs csomag a EDIFACT üzenet dekóder a nyugtázásokhoz létrehozni az Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: e3787b48037360bf6066ddce2bacba6842213b2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Az Azure Logic Apps a vállalati integrációs csomaggal EDIFACT üzenetek dekódolása

Dekódolás EDIFACT üzenet összekötő EDI és a partner jellemző tulajdonságok ellenőrzése, felosztott kereszteződéseket tranzakciók be vagy teljes kereszteződéseket megőrzése és feldolgozott tranzakciók visszaigazoló üzenetet létrehozni. Ez az összekötő használatához fel kell vennie az összekötő egy meglévő elindítani a Logic Apps alkalmazást.

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társítva. A dekódolási EDIFACT üzenet összekötő használatához integrációs fiókkal kell rendelkeznie. 
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban
* Egy [EDIFACT megállapodás](logic-apps-enterprise-integration-edifact.md) , amely már definiálva van az integráció-fiókban

## <a name="decode-edifact-messages"></a>EDIFACT üzenetek dekódolása

1. [Logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md).

2. A dekódolási EDIFACT üzenet összekötő nincs eseményindítók, hozzá kell adni egy eseményindító indítása el a logikai alkalmazás, például a kérelem eseményindítót. A Logic App-tervezőben, vegye fel egy eseményindítót, majd egy műveletet a logikai alkalmazáshoz.

3. A keresési mezőbe írja be a "EDIFACT" szűrőként. Válassza ki **EDIFACT üzenet dekódolása**.
   
    ![EDIFACT keresése](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Integráció fiókjába korábban a kapcsolatokat nem hozott létre, ha a program kéri, most, hogy a kapcsolat létrehozásához. A kapcsolat neve, és válassza ki a integrációs fiókot, amely csatlakozni szeretne.
   
    ![integráció-fiók létrehozása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
    | Integráció fiók * |Adja meg a integrációs fiók nevét. Ellenőrizze, hogy az integrációs fiók és a logikai alkalmazást az Azure ugyanazon a helyen. |

4. Amikor elkészült, a hozta létre a kapcsolat válasszon **létrehozása**. A kapcsolódási adatait. Ez a példa hasonlóan kell kinéznie:

    ![integráció fiókadatok](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Miután létrehozta a kapcsolatot, ebben a példában látható módon, válassza ki a EDIFACT egybesimított fájl üzenetet dekódolására.

    ![integráció fiók kapcsolat létrehozása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Példa:

    ![EDIFACT egybesimított fájl üzenet dekódolását kiválasztása](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT dekóder részletei

Az dekódolása EDIFACT-összekötő az alábbi feladatokat hajtja végre: 

* A boríték kereskedelmipartner-egyezmény szemben érvényesíti.
* A szerződés megszünteti a küldő minősítő & azonosítója és a fogadó minősítő & azonosítója egyeztetve.
* Ha az adatcsere fogadni a konfigurációs beállítások több tranzakció a szerződés alapján felosztja a több tranzakcióra cseréje.
* Visszafejti a adatcserét.
* Ellenőrzi az EDI és a partner jellemző tulajdonságokat, beleértve:
  * Az adatcsere boríték struktúra érvényesítése
  * A vezérlő sémának a boríték séma érvényesítése
  * A tranzakció-set adatelemek az üzenet sémán elvégzett séma érvényesítése
  * Tranzakció-set adatelemek végre EDI érvényesítése
* Ellenőrzi, hogy a csomópont, a csoport és a tranzakció set vezérlő számok nem ismétlődő (Ha be van állítva) 
  * Ellenőrzi a interchange vezérlő korábban fogadott kereszteződéseket ellen. 
  * A vezérlő csoportszám más cseréje a csoport vezérlő számoknak ellenőrzi. 
  * Ellenőrzi, hogy a tranzakció beállítása vezérlő számoknak más tranzakció set vezérlő csoport.
* Felosztja a cseréje a tranzakció be, vagy a teljes adatcsere megőrzi:
  * Vegyes Interchange, tranzakció készletek - tranzakció készletek felfüggeszteni hiba: elágazást interchange tranzakcióban állítja be, és minden tranzakció set elemzi. 
  A X12 dekódolási művelet kimenetében csak azokat a tranzakciót, amely beállítja a érvényesítése sikertelen `badMessages`, és beállítja a fennmaradó tranzakciók kimenetek `goodMessages`.
  * Vegyes Interchange, tranzakció készletek - interchange felfüggeszteni hiba: elágazást interchange tranzakcióban állítja be, és minden tranzakció set elemzi. 
  Ha egy vagy több tranzakció állítja, az adatcsere ellenőrzésen, a X12 dekódolási művelet kimenetében, a tranzakció beállítja, hogy a csomópont nem `badMessages`.
  * Megőrizheti a adatcsere - tranzakció készletek felfüggeszteni hiba: az adatcsere megőrzése, és a teljes kötegelt interchange feldolgozni. 
  A X12 dekódolási művelet kimenetében csak azokat a tranzakciót, amely beállítja a érvényesítése sikertelen `badMessages`, és beállítja a fennmaradó tranzakciók kimenetek `goodMessages`.
  * Megőrizheti a adatcsere - interchange felfüggeszteni hiba: az adatcsere megőrzése, és a teljes kötegelt interchange feldolgozni. 
  Ha egy vagy több tranzakció állítja, az adatcsere ellenőrzésen, a X12 dekódolási művelet kimenetében, a tranzakció beállítja, hogy a csomópont nem `badMessages`.
* (Ha be van állítva) hoz létre egy műszaki (vezérlő) és/vagy a működési visszaigazolás.
  * A műszaki visszaigazolás vagy a CONTRL nyugtát KAPNI a jelentés teljes fogadott adatcsere szintaktikai ellenőrzés eredményeit.
  * A működési visszaigazolás elismeri elfogadására vagy elutasítására fogadott interchange vagy felhasználói csoport

## <a name="view-swagger-file"></a>A Swagger-fájl megtekintése
A Swagger adatai a EDIFACT-összekötő megtekintése: [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Következő lépések
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag") 

