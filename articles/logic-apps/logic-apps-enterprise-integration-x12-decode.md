---
title: "Dekódolás X12 üzenetek - Azure Logic Apps |} Microsoft Docs"
description: "EDI érvényesítése és a X12 rendelkező nyugtázását üzenet dekóder Azure Logic Apps alkalmazásokat a vállalati integrációs csomagban"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 18719a8f49c74973947517161f7306c233a9323f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekódolás X12 üzenetek az Azure Logic Apps a vállalati integrációs csomaggal

Dekódolási X12 üzenet-összekötőn keresztül akkor is érvényesíteni a boríték elleni kereskedelmipartner-egyezmény, EDI és a partner jellemző tulajdonságok ellenőrzése, vágási kereszteződéseket tranzakciók be vagy teljes kereszteződéseket megőrzése és feldolgozott tranzakciók visszaigazoló üzenetet létrehozni. Ez az összekötő használatához fel kell vennie az összekötő egy meglévő elindítani a Logic Apps alkalmazást.

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társítva. A dekódolási X12 üzenet összekötő használatához integrációs fiókkal kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban
* Egy [X12 megállapodás](logic-apps-enterprise-integration-x12.md) , amely már definiálva van az integráció-fiókban

## <a name="decode-x12-messages"></a>Dekódolás X12 üzenetek

1. [Logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md).

2. A dekódolási X12 üzenet összekötő eseményindítókat, nem rendelkezik, hozzá kell adni egy eseményindító indítása el a logikai alkalmazás, például egy kérelem eseményindító. A Logic App-tervezőben, vegye fel egy eseményindítót, majd egy műveletet a logikai alkalmazáshoz.

3.  A keresési mezőbe írja be a "x12" a szűrőhöz. Válassza ki **X12-dekódolási X12 üzenet**.
   
    ![Keressen a "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Integráció fiókjába korábban a kapcsolatokat nem hozott létre, ha a program kéri, most, hogy a kapcsolat létrehozásához. A kapcsolat neve, és válassza ki a integrációs fiókot, amely csatlakozni szeretne. 

    ![Adja meg az integrációs fiók kapcsolódási adatait.](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
    | Integráció fiók * |Adja meg a integrációs fiók nevét. Ellenőrizze, hogy az integrációs fiók és a logikai alkalmazást az Azure ugyanazon a helyen. |

5.  Amikor elkészült, a kapcsolódási adatait. Ez a példa hasonlóan kell kinéznie. Válassza ki a kapcsolat létrehozásának befejezéséhez **létrehozása**.
   
    ![integráció fiók kapcsolódási adatait.](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. A csatlakozás után jön létre, ahogy az ebben a példában válassza ki a X12 egybesimított fájl üzenet dekódolására.

    ![integráció fiók kapcsolat létrehozása](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Példa:

    ![Jelölje be X12 lapos fájl üzenet dekódolását](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>X12 dekódolása részletei

A X12 dekódolási összekötő az alábbi feladatokat hajtja végre:

* Ellenőrzi a boríték kereskedelmipartner-egyezmény ellen
* Érvényesíti EDI- és erőforráspartner jellemző tulajdonságok
  * EDI strukturális érvényesítése és a bővített sémaérvényesítése
  * Az adatcsere boríték szerkezete érvényesítése.
  * A sémaérvényesítés a boríték a vezérlő sémának.
  * A sémaérvényesítés a tranzakció-set adatelemek az üzenet sémának.
  * Tranzakció-set adatelemek végre EDI érvényesítése 
* Ellenőrzi, hogy a csomópont, a csoport és a tranzakció set vezérlő számok nem ismétlődő
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
* Létrehozza a műszaki és/vagy funkcionális visszaigazolás (Ha be van állítva).
  * A műszaki visszaigazolás miatt érvényesítési fejléc állít elő. A műszaki nyugtázást jelentések tartozó az adatcsere fejléc és a cím címzett feldolgozási állapotát.
  * A működési visszaigazolás miatt törzs érvényesítési állít elő. A működési nyugtázást jelentést minden egyes hiba történt a kapott dokumentumot

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/x12/). 

## <a name="next-steps"></a>Következő lépések
[További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag") 

