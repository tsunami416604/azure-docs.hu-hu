---
title: "AS2-üzenetek - Azure Logic Apps kódolása |} Microsoft Docs"
description: "Az AS2 kódoló a vállalati integrációs csomagot az Azure Logic Apps használata"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 7889bf9e4e02143b6bb4c797531afa54f8647ce5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Az Azure Logic Apps a vállalati integrációs csomaggal AS2 üzenetek kódolása

Biztonsága és megbízhatósága közben üzenetek továbbítására létrehozásához az AS2 kódolása üzenet összekötő használatára. Ez az összekötő biztosít digitális aláírására, a titkosítás és a nyugtázásokhoz keresztül üzenet törlése értesítések (MDN), amely még nem megtagadás támogatása.

## <a name="before-you-start"></a>Előkészületek

A szükséges elemeket itt található:

* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , amely már definiált és az Azure-előfizetéshez társítva. Az AS2 kódolása üzenet összekötő használatára integrációs fiókkal kell rendelkeznie.
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva vannak az integráció-fiókban
* Egy [AS2 megállapodás](logic-apps-enterprise-integration-as2.md) , amely már definiálva van az integráció-fiókban

## <a name="encode-as2-messages"></a>AS2-üzenetek kódolása

1. [Logikai alkalmazás létrehozása](logic-apps-create-a-logic-app.md).

2. Az AS2 kódolása üzenet összekötő eseményindítókat, nem rendelkezik, hozzá kell adni egy eseményindító indítása el a logikai alkalmazás, például egy kérelem eseményindító. A Logic App-tervezőben, vegye fel egy eseményindítót, majd egy műveletet a logikai alkalmazáshoz.

3.  A keresési mezőbe írja be a "AS2" a szűrőhöz. Válassza ki **AS2 - Encode AS2 üzenet**.
   
    ![Keresse meg a "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Integráció fiókjába korábban a kapcsolatokat nem hozott létre, ha a program kéri, most, hogy a kapcsolat létrehozásához. A kapcsolat neve, és válassza ki a integrációs fiókot, amely csatlakozni szeretne. 
   
    ![integráció fiókhoz kapcsolat létrehozása](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Tulajdonságok csillaggal szükség.

    | Tulajdonság | Részletek |
    | --- | --- |
    | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
    | Integráció fiók * |Adja meg a integrációs fiók nevét. Ellenőrizze, hogy az integrációs fiók és a logikai alkalmazást az Azure ugyanazon a helyen. |

5.  Amikor elkészült, a kapcsolódási adatait. Ez a példa hasonlóan kell kinéznie. Válassza ki a kapcsolat létrehozásának befejezéséhez **létrehozása**.
   
    ![integráció kapcsolódási adatait.](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. A kapcsolat létrehozása után ebben a példában látható módon, adja meg az adatokat **AS2-a**, **AS2-azonosítók a** be a szerződés és **törzs**, vagyis a üzenetadatokat.
   
    ![Adja meg a kötelező mezők](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2-kódoló részletei

A kódolási AS2-összekötő az alábbi feladatokat hajtja végre: 

* AS2/HTTP-fejlécek vonatkozik
* A jelek kimenő üzenetek (Ha be van állítva)
* Kimenő üzenetek titkosítja (Ha be van állítva)
* Az üzenet tömöríti (Ha be van állítva)

## <a name="try-this-sample"></a>Ismételje meg ezt a mintát

Próbálja meg egy teljesen működőképes logic app és a minta AS2 forgatókönyv üzembe helyezését, tekintse meg a [AS2 logic app-sablon és a forgatókönyv](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/as2/). 

## <a name="next-steps"></a>Következő lépések
[További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag") 

