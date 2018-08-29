---
title: Dekódolandó X12 üzeneteket – Azure Logic Apps |} A Microsoft Docs
description: EDI ellenőrzése és nyugtázását X12 az Azure Logic Apps Enterprise Integration Pack-üzenet dekódoló
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: e3d2a458c2cece5e3f01fdb9e3d403b3fb78dd2b
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121645"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekódolandó X12 üzeneteket az Azure Logic Appsben és Enterprise Integration Pack

A dekódolási X12 üzenet connector is elleni kereskedelmipartner-egyezmény boríték érvényesítése, EDI és partneri jellemző tulajdonságok ellenőrzése, tranzakciók csoportokba adatcsere felosztása vagy a teljes adatcsere megőrzése, és nyugták készítése feldolgozott tranzakciók. Az összekötő használatához hozzá kell adnia az összekötő egy meglévő eseményindítót a logikai alkalmazásban.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókkal](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéséhez társított. A dekódolási X12 üzenet connector használatához egy integrációs fiókhoz kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integrációs fiók
* Egy [X12 szerződés](logic-apps-enterprise-integration-x12.md) , amely már definiálva van az integrációs fiók

## <a name="decode-x12-messages"></a>Dekódolandó X12 üzenetek

1. [Hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

2. A dekódolási X12 üzenet connector nincs eseményindítók, ezért hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása. A Logic App Designerben az eseményindító hozzáadása, és adja hozzá a művelet a logikai alkalmazáshoz.

3.  A keresőmezőbe adja meg a szűrőnek "x12". Válassza ki **X12-dekódolási X12 üzenet**.
   
    ![Keressen a "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Korábban létrehozott kapcsolatokat az integrációs fiókba, most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, és válassza ki az integrációs fiók, amely kapcsolódni szeretne. 

    ![Adja meg az integrációs fiók kapcsolat részletei](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adja meg a kapcsolat bármilyen nevet. |
    | Integrációs fiók * |Adja meg az integrációs fiók nevét. Győződjön meg arról, hogy az integrációs fiók és a logikai alkalmazás ugyanazon Azure-helyen. |

5.  Ha elkészült, a kapcsolat adatait példához hasonlóan kell kinéznie. A kapcsolat létrehozásának befejezéséhez válasszon **létrehozás**.
   
    ![integrációs fiók kapcsolat részletei](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. A csatlakozás után jön létre, ahogyan az ebben a példában válassza ki a X12 való dekódolandó egybesimított fájlos-üzenet.

    ![integrációs fiók kapcsolat létrehozása](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Példa:

    ![Válassza X12 egybesimított fájl dekódolása üzenet](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > A tényleges üzenet tartalma vagy a üzenet tömbből, jó, vagy hibás, hasznos adat a base64-kódolású. Tehát meg kell adnia egy kifejezés, amely feldolgozza a tartalmat.
   > Íme egy példa, amely feldolgozza a tartalmat, amely a kód nézetben vagy a tervezőben Kifejezésszerkesztő használatával adhat meg XML-fájlként.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Tartalom példa](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 részletek-dekódolást.

A X12 dekódolási összekötő az alábbi feladatokat hajtja végre:

* Ellenőrzi a elleni kereskedelmipartner-egyezmény boríték
* EDI- és partner jellemző tulajdonságok ellenőrzése
  * EDI-ellenőrzés szerkezeti és a kiterjesztett séma érvényesítése
  * Az adatcsere-boríték szerkezete érvényesítése.
  * Séma érvényesítése a boríték a vezérlő sémának.
  * Séma érvényesítése a üzenet sémának tranzakciókészlet adatelemet.
  * A tranzakciókészlet adatelem végrehajtott EDI-ellenőrzés 
* Ellenőrzi, hogy az adatcsere, a csoport és a tranzakciós set ellenőrzőszámok nem azonosak
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
* A műszaki és/vagy funkcionális visszaigazolás állít elő, (Ha be van állítva).
  * A technikai visszaigazolás fejléc érvényesítési eredményeként létrehozott. A technikai nyugtázást jelentések egy adatcsere fejlécet és a cím fogadó bemutató feldolgozásának állapotát.
  * A működési visszaigazolás törzs érvényesítési eredményeként létrehozott. A működési nyugtázást jelentések minden egyes hibához a kapott dokumentumot feldolgozásakor

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/x12/). 

## <a name="next-steps"></a>További lépések
[További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag") 

