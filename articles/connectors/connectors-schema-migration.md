---
title: Legújabb séma – Azure Logic Apps alkalmazások migrálása |} A Microsoft Docs
description: A logic Apps alkalmazások áttelepítése a legújabb sémaverzióra
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 8a6925d79b225a34d980472d4fb3241ab9eb1017
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127411"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Logic Apps alkalmazások áttelepítése a legújabb sémaverzióra

A legújabb sémát a meglévő logic apps át, kövesse az alábbi lépéseket: 

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben.

2. A logikai alkalmazás menüjében válassza **áttekintése**. Az eszköztáron válassza **Update Schema**.

   > [!NOTE]
   > Ha Ön következő **Update Schema**, Azure Logic Apps automatikusan futtatja az áttelepítési lépéseket, és Önnek a kódkimenetet biztosít. Ez a kimenet a logikai alkalmazás definíciójának frissítéséhez használható. Ellenőrizze, hogy az alábbiakban leírt ajánlott eljárást követi **ajánlott eljárások** szakaszban.

   ![Séma frissítése](./media/connectors-schema-migration/update-schema.png)

   Az Update Schema oldal jelenik meg, és megjelenít egy hivatkozást, amely leírja a az új sémában található fejlesztések dokumentumhoz.

## <a name="best-practices"></a>Ajánlott eljárások

Az alábbiakban néhány gyakorlati tanácsot a logic Apps alkalmazások áttelepítése a legújabb sémaverzióra:

* Másolja az áttelepített parancsfájlt új logikai alkalmazás. Ne írja felül a régi verziót, amíg a tesztelés befejezését, és győződjön meg arról, hogy az áttelepített alkalmazás a várt módon működik-e.

* A logikai alkalmazás teszteléséhez **előtt** éles környezetben használná.

* Ha befejezte az áttelepítést, a logic apps használatára frissítésének megkezdése a [felügyelt API-k](../connectors/apis-list.md) ahol csak lehetséges. Ha például Dropbox 2 mindenhol DropBox 1-es használt használatának megkezdéséhez.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [a Logic Apps alkalmazások manuális áttelepítésével](../logic-apps/logic-apps-schema-2015-08-01.md)
