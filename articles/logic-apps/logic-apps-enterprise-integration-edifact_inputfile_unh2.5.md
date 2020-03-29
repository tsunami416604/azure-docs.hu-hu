---
title: UNH 2.5 szegmensek EDIFACT üzenetekben
description: EdIFACT-üzenetek feloldása UNH2.5 szegmensekkel az Azure Logic Apps alkalmazásban enterprise integration pack csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792539"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>EdIFACT-dokumentumok kezelése UNH2.5 szegmensekkel az Azure Logic Apps alkalmazásban

Ha egy UNH2.5 szegmens létezik egy EDIFACT dokumentumban, a szegmens séma-vizsgálatra lesz használva. Ebben a minta EDIFACT üzenetben például `EAN008`az UNH mező a következő:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Az üzenet kezeléséhez kövesse az alábbi lépéseket:

1. Frissítse a sémát.

1. Ellenőrizze a szerződés beállításait.

## <a name="update-the-schema"></a>A séma frissítése

Az üzenet feldolgozásához telepítenie kell egy sémát, amely rendelkezik az UNH2.5 gyökércsomópont nevével. Például a minta UNH mező sémájának `EFACT_D03B_ORDERS_EAN008`neve . Minden `D03B_ORDERS` egyes, amely egy másik UNH2.5 szegmens, telepítenie kell egy egyéni séma.

## <a name="add-schema-to-edifact-agreement"></a>Séma hozzáadása az EDIFACT-szerződéshez

### <a name="edifact-decode"></a>EDIFACT dekódolni

A bejövő üzenet dekódolásához állítsa be a sémát az EDIFACT-szerződés fogadási beállításaiközött:

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az integrációs fiókot.

1. Adja hozzá a sémát az integrációs fiókhoz.

1. Konfigurálja a sémát az EDIFACT-szerződés fogadási beállításaiban.

1. Válassza ki az EDIFACT-szerződést, és válassza **a Szerkesztés JSON-ként**lehetőséget. Adja hozzá az UNH2.5 értéket `schemaReferences` a fogadási megállapodás szakaszához:

   ![Add UNH2.5 a megállapodás fogadásához](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT kódolás

A bejövő üzenet kódolásához konfigurálja a sémát az EDIFACT szerződés küldési beállításaiközött

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az integrációs fiókot.

1. Adja hozzá a sémát az integrációs fiókhoz.

1. Konfigurálja a sémát az EDIFACT-szerződés küldési beállításaiközött.

1. Válassza az EDIFACT-szerződést, és kattintson **a Szerkesztés JSON-ként gombra.**  UNH2.5 érték hozzáadása a Megállapodás küldése **sémahivatkozásokhoz**

1. Válassza ki az EDIFACT-szerződést, és válassza **a Szerkesztés JSON-ként**lehetőséget. Adja hozzá az UNH2.5 értéket `schemaReferences` a Küldési megállapodás szakaszához:

   ![Add UNH2.5 küldeni megállapodás](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>További lépések

* További információ az [integrációs fiókmegállapodásokról](../logic-apps/logic-apps-enterprise-integration-agreements.md)