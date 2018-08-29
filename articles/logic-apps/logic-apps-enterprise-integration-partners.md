---
title: Adja hozzá a kereskedelmi partnerek B2B Integrációk – Azure Logic Apps |} A Microsoft Docs
description: Kereskedelmi partnerek az integrációs fiók létrehozása az Azure Logic Apps és Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.date: 07/08/2016
ms.openlocfilehash: 20ca5e06cd1cd0d0abfe6d31f622cd6b61b4178f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125261"
---
# <a name="add-trading-partners-for-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az Azure Logic Apps Enterprise Integration Pack a kereskedelmi partnerek integrációs fiókok hozzáadása

Partnerek olyan entitások, amelyek részt vesznek-vállalatközi (B2B) tranzakciók és az exchange-üzenetek egymással. Hozhat létre, és a egy másik szervezetben ezek a tranzakciók képviselő partnerekkel, előtt mindkettő, amely azonosítja, és érvényesíti a egymással által küldött üzenetek információk megosztása. Miután ezeket a részleteket tárgyalják, és az üzleti kapcsolat készen áll, jelölésére, akkor mindkét az integrációs fiókban lévő partner hozhat létre.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Milyen szerepkörök az partnerek, lejátszása az integrációs fiókban lévő?

Adja meg a partnerek között cserélt üzeneteket részleteit, ezek a partnerek között létrejött megállapodások hozzon létre. Azonban mielőtt létrehozna egy szerződést, hozzá kellett adnia legalább két partnerek az integrációs fiókba. A szervezet része az egyezménynek, kell lennie a **gazdagéppartner**. A másik partnert vagy **vendégpartner** a szervezet, amely az üzenetek a szervezeténél jelöli. A vendégpartner lehet egy másik vállalat, vagy akár egy saját cégen belüli részleg.

Ezek a partnerek hozzáadta, egy szerződést is létrehozhat.

Értesítések fogadásához és beállításokat az üzemeltetett partneri szempontból kiszolgálófrissítési küldéséhez. Például egy szerződést Receive beállításait határozza meg, hogyan az üzemeltetett partneri egy vendégpartner küldött üzeneteket fogadja-e. Hasonlóképpen a szerződés küldési beállításai azt jelzik, az üzemeltetett partneri hogyan üzeneteket küld a vendégpartner.

## <a name="create-partner"></a>Partner létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be a "integráció" névre, és válassza ki **integrációs fiókok**.

   ![Integrációs fiók található](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. A **integrációs fiókok**, válassza ki az integrációs fiók, ahol szeretne hozzáadni a partnerekkel.

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partneri"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. A **partnerek**, válassza a **Hozzáadás**.

   ![Válassza az "Add"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Adjon meg egy nevet a partner, majd válassza ki a **minősítő**. Adjon meg egy **érték** dokumentum, amely az alkalmazás fogadhat azonosításához. Ha elkészült, válassza ki a **OK**.

   ![Partner adatainak hozzáadása](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Válassza ki a **partnerek** újra a csempét.

   ![Válassza a "Partneri" csempe](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Ekkor megjelenik az új partner. 

   ![Új partner megtekintése](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Partner szerkesztése

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki az integrációs fiókot. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partneri" csempe](./media/logic-apps-enterprise-integration-partners/edit.png)

2. A **partnerek**, válassza ki a szerkeszteni kívánt partner.

   ![Jelölje be a partner törlése](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. A **frissítés Partner**, hajtsa végre a módosításokat.
Miután elkészült, válassza ki **mentése**. 

   ![Győződjön meg arról, és mentse a módosításokat](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   A módosítások visszavonásához válassza **elveti**.

## <a name="delete-partner"></a>Partner törlése

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki az integrációs fiókot. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partneri" csempe](./media/logic-apps-enterprise-integration-partners/delete.png)

2. A **partnerek**, válassza ki a törölni kívánt partner.
Válasszon **törlése**.

   ![Partner törlése](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>További lépések

* [További tudnivalók a szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md "megismerheti a vállalati integrációs szerződések")  

