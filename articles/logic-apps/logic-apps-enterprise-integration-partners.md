---
title: Kereskedelmi partnerek hozzáadása a B2B integrációkhoz
description: Hozzon létre kereskedelmi partnereket az integrációs fiókjában az Azure Logic Apps alkalmazásokkal való használatra
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792442"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Kereskedelmi partnerek hozzáadása az Azure Logic Apps integrációs fiókjaihoz

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)alkalmazásban automatikus, vállalkozások közötti (B2B) integrációs munkafolyamatokat hozhat létre egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) használatával a logikai alkalmazásokkal. A szervezet és mások képviseletéhez hozzon létre és adjon hozzá kereskedelmi partnereket összetevőként az integrációs fiókhoz. A partnerek olyan entitások, amelyek részt vesznek a B2B tranzakciókban és üzeneteket váltanak egymással.

A partnerek létrehozása előtt győződjön meg arról, hogy megbeszéli és megosztja a partnerekkel arról, hogyan azonosíthatja és ellenőrizheti a másik által küldött üzeneteket. Miután megállapodott ezekről az adatokról, készen áll arra, hogy partnereket hozzon létre az integrációs fiókjában.

## <a name="partner-roles-in-integration-accounts"></a>Partneri szerepkörök az integrációs fiókokban

A partnerekkel kicserélt üzenetek részleteinek meghatározásához hozzon létre és adjon hozzá [megállapodásokat](../logic-apps/logic-apps-enterprise-integration-agreements.md) az integrációs fiókhoz, és adja hozzá a megállapodásokat. A megállapodásokhoz legalább két partnerszükséges az integrációs fiókban. A szervezet mindig a *megállapodás gazdapartnere.* A szervezet, amely üzeneteket cserél a szervezettel, a *vendégpartner.* A vendégpartner lehet egy másik vállalat, vagy akár egy részleg a saját szervezetében. A partnerek hozzáadása után létrehozhat egy megállapodást.

A megállapodásban megadhatja a bejövő és kimenő üzenetek kezelésének részleteit a gazdapartner szemszögéből. Bejövő üzenetek esetén a **fogadási beállítások** határozzák meg, hogy a fogadó partner hogyan fogadja az üzeneteket a megállapodásban szereplő vendégpartnertől. A kimenő üzenetek esetében a **Küldési beállítások** határozzák meg, hogy a gazdapartner hogyan küldüzeneteket a vendégpartnernek.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Integrációs [fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a partnerek, megállapodások és egyéb B2B-összetevők tárolásához. Ezt az integrációs fiókot az Azure-előfizetéshez kell társtársosan kezelni.

## <a name="create-partner"></a>Partner létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Az Azure főmenüjében válassza a **Minden szolgáltatás lehetőséget.** A keresőmezőbe írja be az "integráció" szót, és válassza **az Integrációs fiókok lehetőséget.**

   ![Válassza az "Integrációs fiókok" lehetőséget](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Az **Integrációs fiókok csoportban**válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a partnereit.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Válassza a **Partnerek csempét.**

   ![Válassza a "Partnerek" csempe](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. A **Partnerek csoportban**válassza a **Hozzáadás**lehetőséget. A **Partner hozzáadása csoportban**adja meg a partner adatait az alábbi táblázatban leírtak szerint.

   ![Válassza a "Hozzáadás" lehetőséget, és adja meg a partner adatait](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | A partner neve |
   | **Selejtező** | Igen | A hitelesítő szervezet, amely egyedi üzleti identitásokat biztosít a szervezetekszámára, például **D-U-N-S (Dun & Bradstreet)**. <p>A partnerek választhatnak egy kölcsönösen meghatározott üzleti identitást. Ezeknél az eseteknél válassza **a Kölcsönösen definiált** EDIFACT vagy **a Kölcsönösen definiált (X12)** lehetőséget az X12-hez. <p>A RosettaNet esetében csak a **DUNS**lehetőséget válassza, amely a szabvány. |
   | **Érték** | Igen | A logikai alkalmazások által kapott dokumentumokat azonosító érték. <p>A RosettaNet esetében ennek az értéknek a DUNS-számnak megfelelő kilencjegyű számnak kell lennie. |
   ||||

   > [!NOTE]
   > A RosettaNet et használó partnerek számára további információkat adhat meg, ha először létrehozza ezeket a partnereket, majd [ezt követően szerkeszti őket.](#edit-partner)

1. Ha elkészült, válassza az **OK** gombot.

   Az új partner most megjelenik a **Partnerek** listán. Emellett a **Partnerek** csempe frissíti a partnerek aktuális számát.

   ![Új partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Partner szerkesztése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókot.
Válassza a **Partnerek csempét.**

   ![Válassza a "Partnerek" csempe](./media/logic-apps-enterprise-integration-partners/edit.png)

1. A **Partnerek csoportban**jelölje ki a szerkesztendő partnert, és válassza a **Szerkesztés gombot.** A **Szerkesztés csoportban**hajtsa végre a módosításokat.

   ![Módosítások végrehajtása és mentése](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   A RosettaNet esetében a **RosettaNet Partner Properties területen**adhatja meg ezt a további információt:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Partner besorolás** | Nem | A partner szervezettípusa |
   | **Ellátásilánc-kód** | Nem | A partner ellátásilánc-kódja, például "Informatika" vagy "Elektronikus alkatrészek" |
   | **Kapcsolattartó neve** | Nem | A partner kapcsolattartójának neve |
   | **E-mail** | Nem | A partner e-mail címe |
   | **Fax** | Nem | A partner faxszáma |
   | **Telefon** | Nem | A partner telefonszáma |
   ||||

1. Ha elkészült, válassza az **OK gombot** a módosítások mentéséhez.

## <a name="delete-partner"></a>Partner törlése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókot. Válassza a **Partnerek csempét.**

   ![Válassza a "Partnerek" csempe](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. A **Partnerek csoportban**válassza ki a törölni kívánt partnert. Válassza a **Törlés** elemet.

   ![Partner törlése](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>További lépések

* További információ a [megállapodásokról](../logic-apps/logic-apps-enterprise-integration-agreements.md)