---
title: "Logic Apps B2B edifact dekódolása megoldásához UNH2.5 - Azure Logic Apps |} Microsoft Docs"
description: "Az Azure Logic Apps B2B edifact dekódolása UNH2.5 feloldása"
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
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>EDIFACT dokumentumokon UNH2.5 szegmens kezelésének módját
UNH2.5 megtalálható-e a EDIFACT dokumentumot, ha használatban van séma kereséshez. 

Példa: A UNH mezője **EAN008** EDIFACT üzenetben  
UNH + SSDD1 + RENDELÉSEK: D: 03B: UN:**EAN008**"  

Az üzenet kezelni a követendő lépések 
1. A séma frissítése
2. A szerződés beállításainak ellenőrzése  

## <a name="update-the-schema"></a>A séma frissítése
Az üzenet feldolgozására is telepíteni kell a sémát UNH2.5 gyökércsomópontjának neve.  A megadott példa, a séma legfelső szintű név lesz **EFACT_D03B_ORDERS_EAN008**  

Az egyes D03B_ORDERS az egy másik UNH2.5 szegmens kellene az egyéni séma telepítéséhez.  

## <a name="add-schema-to-the-edifact-agreement"></a>Adja hozzá a séma a EDIFACT-megállapodás
### <a name="edifact-decode"></a>EDIFACT dekódolása
A bejövő üzenet dekódolni, konfigurálja a séma a EDIFACT megállapodás kap beállításai
1. A séma az integráció fiók hozzáadása    
2. A séma konfigurálása a EDIFACT a szerződés megkapja a beállításokat. 
3. Jelöljön ki EDIFACT szerződést, majd **módosítsa a JSON**.  A fogadási szerződés UNH2.5 előnyei **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT kódolása
A bejövő üzenet kódolására, konfigurálja a séma EDIFACT megállapodás küldési beállításai
1. A séma az integráció fiók hozzáadása    
2. Adja meg a séma a EDIFACT megállapodás küldés beállításait. 
3. Jelöljön ki EDIFACT szerződést, majd **módosítsa a JSON**.  A Küldés szerződés UNH2.5 előnyei **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Következő lépések
* [További információ az integrációs fiók megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  