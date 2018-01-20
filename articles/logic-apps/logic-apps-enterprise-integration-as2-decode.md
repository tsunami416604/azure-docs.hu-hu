---
title: "AS2-üzenetek - Azure Logic Apps dekódolása |} Microsoft Docs"
description: "Az AS2 dekóder a vállalati integrációs csomagot az Azure Logic Apps használata"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4acae9f1837069c494985ff1456979490485f609
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Az Azure Logic Apps a vállalati integrációs csomaggal AS2 üzenetek dekódolása 

Biztonsága és megbízhatósága közben üzenetek továbbítására létrehozásához az AS2 dekódolása üzenet összekötő használatára. Ez az összekötő biztosít digitális aláírására, visszafejtéshez és nyugták üzenet törlése értesítések (MDN) keresztül.

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társítva. Az AS2 dekódolása üzenet összekötő használatára integrációs fiókkal kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban
* Egy [AS2 megállapodás](logic-apps-enterprise-integration-as2.md) , amely már definiálva van az integráció-fiókban

## <a name="decode-as2-messages"></a>AS2-üzenetek dekódolása

1. [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Az AS2 dekódolása üzenet összekötő nincs eseményindítók, hozzá kell adni egy eseményindító indítása el a logikai alkalmazás, például a kérelem eseményindítót. A Logic App-tervezőben, vegye fel egy eseményindítót, majd egy műveletet a logikai alkalmazáshoz.

3.  A keresési mezőbe írja be a "AS2" a szűrőhöz. Válassza ki **AS2 - dekódolási AS2 üzenet**.
   
    ![Keresse meg a "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Integráció fiókjába korábban a kapcsolatokat nem hozott létre, ha a program kéri, most, hogy a kapcsolat létrehozásához. A kapcsolat neve, és válassza ki a integrációs fiókot, amely csatlakozni szeretne.
   
    ![Integráció kapcsolat létrehozása](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
    | Integráció fiók * |Adja meg a integrációs fiók nevét. Ellenőrizze, hogy az integrációs fiók és a logikai alkalmazást az Azure ugyanazon a helyen. |

5.  Amikor elkészült, a kapcsolódási adatait. Ez a példa hasonlóan kell kinéznie. Válassza ki a kapcsolat létrehozásának befejezéséhez **létrehozása**.

    ![integráció kapcsolódási adatait.](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. A kapcsolat létrehozása után ebben a példában látható módon, válassza ki a **törzs** és **fejlécek** az a kérelem kimenetek.
   
    ![integráció kapcsolat létrehozása](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Példa:

    ![Válassza ki a szervezet és a fejlécek a kérelem kimenetek](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>AS2 dekóder részletei

A dekódolása AS2-összekötő az alábbi feladatokat hajtja végre: 

* Feldolgozza a AS2/HTTP-fejlécek
* Ellenőrzi az aláírást (Ha be van állítva)
* Az üzenetek visszafejti (Ha be van állítva)
* Az üzenet kibontja (Ha be van állítva)
* Egyezteti a fogadott MDN eredeti kimenő üzenet
* Frissítések és az nem megtagadás adatbázis korrelálja
* Írja a rekordokat az AS2 állapotjelentés
* A kimeneti hasznos tartalma base64-kódolású
* Beállítja, hogy egy MDN szükség, hogy kell, hogy a MDN szinkron vagy aszinkron konfigurációtól függően az AS2-megállapodás
* Létrehoz egy szinkron vagy aszinkron MDN (megállapodás konfigurációk alapján)
* A MDN beállítása a korrelációs jogkivonat és tulajdonságai

## <a name="try-this-sample"></a>Ismételje meg ezt a mintát

Próbálja meg egy teljesen működőképes logic app és a minta AS2 forgatókönyv üzembe helyezését, tekintse meg a [AS2 logic app-sablon és a forgatókönyv](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/as2/). 

## <a name="next-steps"></a>További lépések
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md) 

