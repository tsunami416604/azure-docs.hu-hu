---
title: "Logic Apps-alkalmazások áttelepítése a 2015. 08. 01. dátumú előzetes sémaverzióra | Microsoft Docs"
description: "Logic Apps alkalmazásait egyszerűen áttelepítheti a legújabb sémaverzióra. Csak kövesse az alábbi lépéseket."
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.translationtype: Human Translation
ms.sourcegitcommit: 30abc823a72431dfd12f1051434191ea904533f6
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.contentlocale: hu-hu
ms.lasthandoff: 01/20/2017

---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Logic Apps alkalmazások áttelepítése a 2015. 08. 01. dátumú előzetes sémaverzióra
A meglévő Logic Apps alkalmazásoknak az új sémára való áttelepítéséhez tegye az alábbiakat:  

1. Nyissa meg a Logic Apps alkalmazást az Azure Portalon  
2. Kattintson az Update Schema (Séma frissítése) elemre:
   
   ![API-ikon][step1]   
   Megjelenik az Update Schema (Séma frissítése) lap az új sémában található fejlesztések részleteit tartalmazó dokumentumra mutató hivatkozással: ![API-ikon][step2]

> [!NOTE]
> Az **Update Schema** (Séma frissítése) választásakor automatikusan futtatjuk az áttelepítési lépéseket, és átadjuk Önnek a kódkimenetet. Ennek használatával frissítheti a definíciót, azonban mindenképpen kövesse a kódolás ajánlott eljárásit, például az alábbi **Ajánlott eljárások** című szakaszban foglaltakat.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Ajánlott eljárások a Logic Apps alkalmazások áttelepítéséhez a legújabb sémaverzióra:
* Az áttelepített parancsfájlt új Logic Apps alkalmazásba másolja – ne írja felül a régit, amíg el nem végezte a tesztelést, és meg nem győződött róla, hogy az áttelepített alkalmazás a vártnak megfelelően működik.
* Tesztelje a Logic Apps alkalmazást, **mielőtt** éles környezetben használná.
* Az áttelepítés befejezését követően kezdje el frissíteni a Logic Apps alkalmazásokat a [felügyelt API-k](apis-list.md) használatára, ha lehetséges. Megkezdheti például a Dropbox&2;-es verziójának használatát azon alkalmazások esetén, amelyek a DropBox&1;-es verzióját használják.

## <a name="whats-next"></a>A következő lépések
* [Információk a Logic Apps alkalmazások manuális áttelepítésével kapcsolatban](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png







