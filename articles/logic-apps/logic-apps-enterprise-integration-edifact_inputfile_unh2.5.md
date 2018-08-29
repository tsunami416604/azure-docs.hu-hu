---
title: EDIFACT-üzenetek UNH 2,5 segements – Azure Logic Apps kezelésére |} A Microsoft Docs
description: EDIFACT-dokumentumok kezelése UNH2.5 szegmens az Azure Logic Appsben és Enterprise Integration Pack-megoldásához
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 9c8b8611347840dcf49759dac51fb506815cd782
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122008"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Az Azure Logic Apps UNH2.5 szegmens EDIFACT-dokumentumok

UNH2.5 szerepel az EDIFACT-dokumentumok, ha azt használja a keresési séma. 

Példa: A UNH mező kitöltése **EAN008** az EDIFACT-üzenet  
UNH + SSDD1 + RENDELÉSEK: D: 03B: TÖRLÉSEKOR:**EAN008**"  

Teendők, az üzenet kezeléséhez 
1. A séma frissítése
2. A szerződés beállításainak ellenőrzése  

## <a name="update-the-schema"></a>A séma frissítése
Az üzenet feldolgozására is telepíteni kell egy séma UNH2.5 neve.  A példaként megadott, a legfelső szintű sémanév lenne **EFACT_D03B_ORDERS_EAN008**  

Az egyes D03B_ORDERS egy másik kezelése UNH2.5 szegmens használatával meg kellene egy egyedi séma üzembe helyezése.  

## <a name="add-schema-to-the-edifact-agreement"></a>Az EDIFACT-egyezmény séma hozzáadása
### <a name="edifact-decode"></a>EDIFACT-dekódolást.
A bejövő üzenet dekódolása, konfigurálja a séma az EDIFACT szerződés kap beállításai
1. Adja hozzá a sémát az integrációs fiókba    
2. A séma konfigurálásához az EDIFACT szerződés kap beállításait. 
3. EDIFACT-egyezmény válassza ki, és kattintson a **Szerkesztés JSON-ként**.  UNH2.5 érték hozzáadása a kap szerződés **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT-kódolást
A bejövő üzenet kódolására, konfigurálja a séma az EDIFACT-megállapodás küldési beállításaiban
1. Adja hozzá a sémát az integrációs fiókba    
2. Adja meg a sémában az EDIFACT-megállapodás küldési beállításai. 
3. EDIFACT-egyezmény válassza ki, és kattintson a **Szerkesztés JSON-ként**.  UNH2.5 érték hozzáadása a Küldés szerződés **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>További lépések
* [További információ az integrációs fiók megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "megismerheti a vállalati integrációs szerződések")  