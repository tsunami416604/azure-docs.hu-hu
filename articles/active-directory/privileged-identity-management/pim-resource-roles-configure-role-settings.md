---
title: Azure szerepkör-beállítások konfigurálása a PIM |} A Microsoft Docs
description: Ismerje meg az Azure-erőforrás szerepkör-beállítások konfigurálása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 901eb5ef43ddb2840ed7a3d83fc08f2f05849461
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189735"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>A PIM Azure szerepkör-beállítások konfigurálása

Szerepkör-beállítások konfigurálásakor adja meg az alapértelmezett beállításokat, amelyek érvényesek a Privileged Identity Management (PIM) környezetben társításának. Ezek az erőforrás beállításainak megadásához válassza ki a **szerepkör-beállítások** lapra a bal oldali ablaktáblán. A jelenlegi beállítások megtekintéséhez a művelet sávon (az összes szerepkör) közül is választhat a szerepkör-beállítások gombra.

## <a name="overview"></a>Áttekintés

Jóváhagyási munkafolyamat Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó a rendszergazdák további védelméhez, vagy kritikus erőforrásokhoz való hozzáférés korlátozása. A rendszergazdák, szerepkör-hozzárendelések az aktiváláshoz jóváhagyásra lehet szükség. 

Az erőforrás hierarchia elképzelés az, csak az Azure-erőforrások szerepköreihez. Ez a hierarchia lehetővé teszi, hogy az öröklési szülőtároló található összes gyermek-erőforrás a szülőobjektum erőforrás lefelé szerepkör-hozzárendelések. 

Például: Bob, egy erőforrás-rendszergazda, használja a PIM módon megadott tagja jogosult Alice hozzárendelése a Contoso az előfizetésben tulajdonosi szerepkör. Ezt a hozzárendelést Alice a Contoso-előfizetésen belüli összes erőforrás csoport tárolók jogosult tulajdonosa. Alice az is az összes erőforrás (például virtuális gépek) jogosult tulajdonosa az előfizetés mindegyik erőforráscsoporton belül. 

Tegyük fel, a Contoso-előfizetés három erőforráscsoportok vannak: a Fabrikam tesztelés, a Fabrikam fejlesztői és a Fabrikam gyárt. Ezeket az erőforráscsoportokat mindegyike tartalmaz egy virtuális gépen.

A PIM beállításai az egyes szerepkörökhöz erőforrás. Hozzárendelések ellentétben ezek a beállítások nem öröklődnek, és feltétlenül vonatkozik az erőforrás-szerepkör. [További információ a jogosult hozzárendelések és láthatósága](pim-resource-roles-eligible-visibility.md).

A példa folytatása: Bob PIM használ, amelyet aktiválnia kell a tulajdonosi szerepkör a Contoso előfizetési kérés jóváhagyási szereplő összes tag igényelnek. A Fabrikam éles erőforráscsoportban az erőforrások védelme érdekében Bob is jóváhagyásra van szüksége ennek az erőforrásnak a tulajdonos szerepkör tagjai. A Fabrikam tesztelési és a Fabrikam fejlesztői tulajdonos szerepkörök nem igényel jóváhagyási az aktiváláshoz.

Amikor Ágnes a Contoso előfizetés tulajdonosi szerepkörét aktiválását, a jóváhagyó musí schválit nebo elutasítja a saját kérelmet, mielőtt ő válik aktívvá a szerepkörben. Ha úgy dönt, hogy Ágnes [saját aktiválási hatókör](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) a Fabrikam éles erőforráscsoporthoz a jóváhagyó jóváhagyása vagy elutasítása túl a ezt a kérelmet kell. De ha Alice úgy dönt, hogy saját aktiválási egyik vagy mindkét Fabrikam teszt vagy a Fabrikam fejlesztői hatókör, jóváhagyás, nem szükséges.

A jóváhagyási munkafolyamat nem feltétlenül szükséges a szerepkör összes tagja számára. Példaként vegyünk egy forgatókönyvet, ahol a szervezet több szerződés hozzárendeli egy Azure-előfizetésben futó alkalmazás fejlesztésének kiküszöbölni hires. Erőforrás-rendszergazdaként azt szeretné, hogy az alkalmazottak jogosult hozzáférése nélkül jóváhagyás szükséges, de a szerződés hozzárendeli engedélyt kell kérnie. Jóváhagyási munkafolyamatok konfigurálása csak a szerződés hozzárendeli, létrehozhat egy egyéni biztonsági szerepkört, az alkalmazottak rendelt szerepkör ugyanazokkal az engedélyekkel. Megkövetelheti, hogy egyéni szerepkör aktiválásához jóváhagyás. [További információ az egyéni szerepkörök](pim-resource-roles-custom-role-policy.md).

A jóváhagyási munkafolyamat konfigurálása, és adja meg, akik jóváhagyhatják vagy megtagadhatják a kérelmek, az alábbi eljárásokkal.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyásra van szükség

1. A PIM az Azure Portalon keresse meg, és válasszon ki egy erőforrást a listából.

   ![A kiválasztott erőforrás "Azure-erőforrások" panel](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. A bal oldali panelen válassza ki a **szerepkör-beállítások**.

3. Keresse meg, és válasszon egy szerepkört, majd válassza ki **szerkesztése** beállításainak módosítására.

   ![Az operátor szerepkör "Szerkesztés" gombra](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. Az a **aktiválási** szakaszban jelölje be a **aktiválásához jóváhagyás szükséges** jelölőnégyzetet.

   ![Szerepkör-beállítások "Aktiválás" szakaszában](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Adja meg a jóváhagyóknak

Kattintson a **jóváhagyók kiválasztása** megnyitásához a **egy felhasználó vagy csoport kiválasztása** ablaktáblán.

>[!NOTE]
>Jelöljön ki legalább egy felhasználó vagy csoport a beállításainak frissítéséhez. Nincsenek nem alapértelmezett jóváhagyónak.

Erőforrás-rendszergazdák felhasználók és csoportok tetszőleges kombinációját is hozzáadhat a listát. 

!["A felhasználó vagy csoport kiválasztása" panelen a kiválasztott felhasználóhoz](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Kérelem jóváhagyása aktiválása

Engedély kérése nem befolyásolja az eljárást, amely a tag aktiválni kell követnie. [Tekintse át a szerepkört aktiváló lépéseit](pim-resource-roles-activate-your-roles.md).

Ha tagja a kért jóváhagyást igénylő szerepkörök aktiválása és a szerepkör már nem szükséges, a tag megszakíthatja a kérelmet az PIM-ben.

Megszakítja, keresse meg a PIM, és válassza a **saját kérések**. Keresse meg a kérelmet, és válasszon **Mégse**.

!["Saját kérések" panel](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrások szerepköreihez tartozó többtényezős hitelesítés megkövetelése](pim-resource-roles-require-mfa.md)
- [Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az PIM-ben](pim-resource-roles-configure-alerts.md)
