---
title: B2B-Integrációk – Azure Logic Apps kereskedelmi partnerek felvétele
description: Az Azure Logic Apps használatára az integrációs fiókban lévő kereskedelmi partnerek létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330134"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Kereskedelmi partnerek az Azure Logic Apps integrációs fiókok hozzáadása

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md),-vállalatközi (B2B) integrációs automatizált munkafolyamatok használatával is létrehozhat egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a logic apps szolgáltatással. A szervezet és mások, hozzon létre, és adja hozzá a kereskedelmi partnerek mint összetevőket az integrációs fiókba. Partnerek olyan entitás, amely részt vesz a B2B-tranzakciók, és az egyes üzeneteket.

Ezek a partnerek létrehozása, előtt ügyeljen arra, hogy megvitatása és az információk megosztása a partnerekkel azonosításához, és ellenőrizze, hogy a másik küld üzeneteket. Miután elfogadja ezeket a részleteket, készen áll az integrációs fiókban lévő partner létrehozása.

## <a name="partner-roles-in-integration-accounts"></a>Partner szerepkörök az integrációs fiókok

Adja meg a partnerekkel cserélt üzeneteket részleteit, hozzon létre, majd adja hozzá [szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md) , összetevőket az integrációs fiókba. Szerződések legalább két partnerek az integrációs fiók szükséges. A szervezete nem mindig a *gazdagéppartner* a szerződés. A szervezet, amely az üzenetek a szervezeténél a *vendégpartner*. A vendégpartner lehet egy másik vállalat, vagy akár egy saját cégen belüli részleg. Ezek a partnerek hozzáadta, egy szerződést is létrehozhat.

A szerződésben adja meg a fogadó partner szempontjából a bejövő és kimenő üzenetek kezelése adatait. Bejövő üzenetek a **fogadási beállítások** adja meg, hogyan a fogadó partner üzeneteket fogad a vendégpartner a szerződés. Kimenő üzenetek a **küldési beállítások** adja meg, hogy a fogadó partner üzeneteket küld a vendégpartner.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) más B2B összetevők, a partnerek és szerződések tárolásához. Az integrációs fiók az Azure-előfizetése társítva kell lennie.

## <a name="create-partner"></a>Partner létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure fő menüjéből válassza **minden szolgáltatás**. A keresőmezőbe, írja be a "integráció" névre, és válassza ki **integrációs fiókok**.

   ![Válassza ki a "Integrációs fiók"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. A **integrációs fiókok**, válassza ki az integrációs fiók, ahol szeretne hozzáadni a partnerekkel.

   ![Válassza ki az integrációs fiók](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partneri" csempe](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. A **partnerek**, válassza a **Hozzáadás**. A **Partner hozzáadása**, adja meg a partner adatait, az alábbi táblázat szerint.

   !["A Hozzáadás" gombra, majd adja meg partner adatait](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Name (Név)** | Igen | A partner neve |
   | **Qualifier** | Igen | A hitelesítő szervezet, amely egyedi üzleti identitások szervezetekhez, például biztosít **D-U-N-S (Dun & Bradstreettől)** . <p>Partnerek egy kölcsönösen meghatározott üzleti identitás kikapcsolhatja az újat. Ebben az esetben válassza ki a **kölcsönösen definiált** az EDIFACT vagy **kölcsönösen definiált (X12)** X12 számára. <p>RosettaNet, válassza ki csak **DUNS**, azaz a standard. |
   | **Érték** | Igen | Egy érték, amely azonosítja a fogadása a logic apps dokumentumokat. <p>RosettaNet, az ezt az értéket, amely megfelel a DUNS számának kilenc számjegyű számnak kell lennie. |
   ||||

   > [!NOTE]
   > RosettaNet használó partnerek, hoz létre, ezek a partnerek további beállításokat is megadhat, majd [szerkeszti őket később](#edit-partner).

1. Ha elkészült, válassza ki a **OK**.

   Most már megjelenik az új partnert a **partnerek** listája. Emellett a **partnerek** csempe frissíti a partnerek száma.

   ![Új partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Partner szerkesztése

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki az integrációs fiókot.
Válassza ki a **partnerek** csempére.

   ![Válassza a "Partneri" csempe](./media/logic-apps-enterprise-integration-partners/edit.png)

1. A **partnerek**, jelölje be a partner szerkesztése, és válassza a kívánt **szerkesztése**. A **szerkesztése**, hajtsa végre a módosításokat.

   ![Győződjön meg arról, és mentse a módosításokat](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   A RosettaNet alatt **RosettaNet Partner tulajdonságainak**, megadhatja, hogy ez az információ:

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Partner besorolás** | Nem | A partner szervezet típusa |
   | **Ellátási lánc kód** | Nem | A partner ellátási lánc kód, például az "Informatika" vagy "Elektronikai alkatrészek" |
   | **Kapcsolattartó neve** | Nem | A partner kapcsolattartó neve |
   | **E-mail** | Nem | A partner e-mail-címe |
   | **Fax** | Nem | A partner faxszám |
   | **Telefon** | Nem | A partner telefonszám |
   ||||

1. Ha elkészült, válassza ki a **OK** a módosítások mentéséhez.

## <a name="delete-partner"></a>Partner törlése

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki az integrációs fiókot. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partneri" csempe](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. A **partnerek**, válassza ki a törölni kívánt partner. Válasszon **törlése**.

   ![Partner törlése](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md)