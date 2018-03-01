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
ms.openlocfilehash: 89066ba062c2b243136a03a52144fd99ae87eddc
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Hozzáadásakor vagy módosításakor a partnerek a munkafolyamat vállalatok szerződések

A partnerek olyan entitásokat, üzleti vállalatközi (B2B) tranzakciók részt, és az exchange-üzenetek között. Létrehozhat, és ezek a tranzakciók a másik szervezet képviselő partnerekkel, előtt is, amely azonosítja, és ellenőrzi az egyes küldött üzenetek az információkat. Miután vitassa meg ezeket az adatokat, és az üzleti kapcsolat készen áll, az integráció fiókban partnerek számára, hogy megfelelnek az Ön is hozhat létre.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Milyen szerepkörök az partnerek, játszani az integráció fiókját?

Adja meg a partnerek között váltott üzenetek részleteit, ezeket a partnerek között létrejött megállapodások kell létrehozni. Azonban létrehozhat egy szerződést, mielőtt hozzá kellett adnia legalább két partner integrációs fiókjába. A szervezet, a szerződés részét kell képeznie a **fogadó partner**. A másik partnert vagy **Vendég partner** azt a szervezetet, amely az üzeneteket a szervezet jelöli. A Vendég partner lehet egy másik vállalat, vagy akár egy részleget a saját szervezetében.

Miután hozzáadta a partnerek, létrehozhat egy szerződést.

Értesítések fogadásához és küldéséhez a beállítások a üzemeltetett Partner szempontból irányulnak. Például egy szerződést a fogadási beállítások határozzák meg, hogyan az üzemeltetett partner kap egy Vendég partner küldött üzenetek. Hasonlóképpen a Küldés beállításai a szerződésben azt jelzik, hogyan az üzemeltetett partner üzeneteket küld a Vendég partner.

## <a name="create-partner"></a>Partner létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki a fő Azure menü **minden szolgáltatás**. A keresési mezőbe, írja be a "integrációt", és válassza ki **integrációs fiókok**.

   ![Integráció fiók](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. A **integrációs fiókok**, ahol a partnerek hozzáadni kívánt integrációs fiók kijelölése.

   ![Integráció fiók kiválasztása](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Válassza ki a **partnerek** csempére.

   ![Válassza ki a "Partners"](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. A **partnerek**, válassza a **Hozzáadás**.

   ![Válassza a "Hozzáadás"](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Adja meg a partner nevét, majd válasszon egy **minősítő**. Adjon meg egy **érték** dokumentumok, az alkalmazások fogadó azonosításához. Amikor elkészült, válassza ki a **OK**.

   ![Partner részletek megadása](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Válassza ki a **partnerek** újra csempére.

   ![Válassza a "Partners" csempe](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Ekkor megjelenik az új partner. 

   ![Új partner megtekintése](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Partner szerkesztése

1. Az a [Azure-portálon](https://portal.azure.com), található, és válassza ki a integrációs fiókját. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partners" csempe](./media/logic-apps-enterprise-integration-partners/edit.png)

2. A **partnerek**, válassza ki a szerkeszteni kívánt partnert.

   ![Válassza ki a törölni kívánt partnert](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. A **frissítés Partner**, hajtsa végre a módosításokat.
Miután elkészült, válassza ki azt **mentése**. 

   ![Ellenőrizze, és mentse a módosításokat](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   A módosítások visszavonásához jelölje ki a **elvetése**.

## <a name="delete-partner"></a>Partner törlése

1. Az a [Azure-portálon](https://portal.azure.com), található, és válassza ki a integrációs fiókját. Válassza ki a **partnerek** csempére.

   ![Válassza a "Partners" csempe](./media/logic-apps-enterprise-integration-partners/delete.png)

2. A **partnerek**, válassza ki a törölni kívánt partnert.
Válasszon **törlése**.

   ![Partner törlése](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>További lépések

* [További információ a megállapodások](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")  

