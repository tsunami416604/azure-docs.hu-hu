---
title: Alkalmazások áttelepítése a legújabb sémába
description: A logikai alkalmazás munkafolyamatának JSON-definícióinak áttelepítése a munkafolyamat-definíciónyelv legújabb verziójára
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666788"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Logikai alkalmazások áttelepítése a legújabb sémaverzióra

Ha a meglévő logikai alkalmazásokat a legújabb sémába szeretné áthelyezni, kövesse az alábbi lépéseket: 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

2. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. Az eszköztáron válassza a **Séma frissítése**lehetőséget.

   > [!NOTE]
   > Ha a **Séma frissítése lehetőséget választja,** az Azure Logic Apps automatikusan futtatja az áttelepítési lépéseket, és biztosítja a kódkimenetet. Ezt a kimenetet használhatja a logikai alkalmazás definíciójának frissítéséhez. Ügyeljen azonban arra, hogy kövesse az ajánlott eljárásokat az alábbi gyakorlati tanácsok szakaszban **leírtak** szerint.

   ![Séma frissítése](./media/connectors-schema-migration/update-schema.png)

   Megjelenik a Séma frissítése lap, amely egy olyan dokumentumra mutató hivatkozást jelenít meg, amely leírja az új séma fejlesztéseit.

## <a name="best-practices"></a>Ajánlott eljárások

Az alábbiakban néhány gyakorlati tanácst tartalmaz a logikai alkalmazások legújabb sémaverzióra való áttelepítéséhez:

* Másolja az áttelepített parancsfájlt egy új logikai alkalmazásba. Ne írja felül a régi verziót, amíg be nem fejezi a tesztelést, és nem győződjön meg arról, hogy az áttelepített alkalmazás a várt módon működik.

* Tesztelje a logikai alkalmazást az éles környezetbe helyezése **előtt.**

* Miután befejezte az áttelepítést, indítsa el a logikai alkalmazások frissítését a [felügyelt API-k,](../connectors/apis-list.md) ahol lehetséges. Például, elkezd használ Dropbox v2 mindenhol amit használ DropBox v1.

## <a name="next-steps"></a>További lépések

* További információ a [logikai alkalmazások manuális áttelepítése](../logic-apps/logic-apps-schema-2015-08-01.md)
