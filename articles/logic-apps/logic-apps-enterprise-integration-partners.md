---
title: "Az üzleti vállalatközi (B2B) üzenetek - Azure Logic Apps partnerek létrehozása |} Microsoft Docs"
description: "Útmutató a vállalati integrációs csomag és a Logic Apps integrációs fiókjába partnerek hozzáadása"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Hozzáadásakor vagy módosításakor a partnerek a munkafolyamat vállalatok szerződések

A partnerek olyan entitásokat, üzleti vállalatközi (B2B) tranzakciók részt, és az exchange-üzenetek között. Létrehozhat, és ezek a tranzakciók a másik szervezet képviselő partnerekkel, előtt is, amely azonosítja, és ellenőrzi az egyes küldött üzenetek az információkat. Miután vitassa meg ezeket az adatokat, és az üzleti kapcsolat készen áll, az integráció fiókban partnerek számára, hogy megfelelnek az Ön is hozhat létre.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>Milyen szerepkörök rendelkeznek partnerek integrációs fiókja?

Adja meg a partnerek között váltott üzenetek részleteit, ezeket a partnerek között létrejött megállapodások kell létrehozni. Azonban létrehozhat egy szerződést, mielőtt hozzá kellett adnia legalább két partner integrációs fiókjába. A szervezet, a szerződés részét kell képeznie a **fogadó partner**. A másik partnert vagy **Vendég partner** azt a szervezetet, amely az üzeneteket a szervezet jelöli. A Vendég partner lehet egy másik vállalat, vagy akár egy részleget a saját szervezetében.

Miután hozzáadta a partnerek, létrehozhat egy szerződést.

Értesítések fogadásához és küldéséhez a beállítások a üzemeltetett Partner szempontból irányulnak. Például egy szerződést a fogadási beállítások határozzák meg, hogyan az üzemeltetett partner kap egy Vendég partner küldött üzenetek. Hasonlóképpen a Küldés beállításai a szerződésben azt jelzik, hogyan az üzemeltetett partner üzeneteket küld a Vendég partner.

## <a name="how-to-create-a-partner"></a>Hogyan hozhat létre a partner?

1. Válassza ki az Azure-portálon **Tallózás**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. A szűrő a keresőmezőbe írja be **integrációs**, majd jelölje be **integrációs fiókok** az eredménylistában.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Válassza ki az integráció fiókra, amelyhez a partnerek hozzáadni.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Válassza ki a **partnerek** csempére.

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. A partnerek paneljén válassza **Hozzáadás**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Adja meg a partner nevét, majd válasszon egy **minősítő**. Végül adja meg a **érték** dokumentumok, amelyek az alkalmazások azonosítása.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. A partner-létrehozási folyamat előrehaladásának megtekintéséhez válassza ki a *harang* értesítési ikon.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. Győződjön meg arról, hogy az új partnerek sikerült hozzáadni, jelölje be a **partnerek** csempére.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    Miután kiválasztotta a partnerek csempe, azt is megtudhatja újonnan hozzáadott partnerek a partnerek a panelen.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Az integráció fiókban meglévő partnerek szerkesztése

1. Válassza ki a **partnerek** csempére.
2. Panel megnyitása után a partnerek, válassza ki a szerkeszteni kívánt partnert.
3. Az a **frissítés Partner** csempére, hajtsa végre a módosításokat.
4. Miután elkészült, válassza ki azt **mentése**, vagy a Mégse gombra a módosítások, jelöljön ki **elvetése**.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>Egy partner törlése

1. Válassza ki a **partnerek** csempére.
2. A Partner után panel nyílik meg, válassza ki a törölni kívánt partnert.
3. Válasszon **törlése**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Következő lépések
* [További információ a megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  

