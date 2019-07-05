---
title: A PIM - Azure Active Directory saját Azure AD-szerepkörök aktiválása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet aktiválni az Azure AD Privileged Identity Management (PIM) az Azure AD-szerepkörök.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b3e02222580da67a4eeb4159e4fc5dc3b9a98f
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501673"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>A PIM saját Azure AD-szerepkörök aktiválása

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) egyszerűbbé teszi, hogy hogyan kezelhetik a vállalatok számára az erőforrások az Azure AD és más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune emelt szintű hozzáférés.  

Ha Ön történtek egy rendszergazdai szerepkör jogosult, ez azt jelenti, is aktiválhatja a szerepkört, amikor szüksége van privilegizált műveletek elvégzéséhez. Például alkalmanként kezelheti az Office 365-funkciókat, ha a szervezet a kiemelt szerepkörű rendszergazdák nem teheti elérhetővé, állandó globális rendszergazda, mivel az adott szerepkör milyen hatással van más szolgáltatások túl. Ehelyett, győződjön meg arról, hogy jogosult az Azure AD-szerepkörök, például az Exchange Online rendszergazdai. Kérheti, hogy a szerepkör aktiválása, ha a jogosultságok szükségesek, és ezután lesz rendszergazdai felügyelete egy előre meghatározott időtartamra.

Ez a cikk a rendszergazdák számára, akik az Azure AD PIM szerepük aktiválnia kell.

## <a name="activate-a-role"></a>A szerepkör aktiválása

Amikor szüksége van egy Azure AD-szerepkör, aktiválás segítségével kérheti a **saját szerepkörök** navigációs lehetőség az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**. A PIM-csempét az irányítópulton adásával kapcsolatos információkért lásd: [használatához a PIM](pim-getting-started.md).

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **saját szerepkörök** megjeleníthetők a jogosult az Azure AD-szerepkörökhöz.

    ![Az Azure AD-szerepkörök – saját szerepkörök jogosultak vagy aktív szerepkörök lista megjelenítése](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Keresse meg egy szerepkör, amely aktiválni szeretné.

    ![Az Azure AD-szerepkörök – a jogosult szerepkörök listáját megjelenítő hivatkozás aktiválása](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kattintson a **aktiválás** a szerepkör aktiválási részletei ablak megnyitásához.

1. Ha a szerepkör megköveteli a multi-factor authentication (MFA), kattintson a **a folytatás előtt azonosítsa magát**. Csak egyszer munkamenetenként hitelesítésre van.

    ![Ellenőrizze a szerepkör aktiválása előtt MFA-val saját identitás panel](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Kattintson a **saját identitás ellenőrzése** és kövesse az utasításokat a további biztonsági ellenőrzést nyújtanak.

    ![További biztonsági ellenőrzési lapot kapcsolatba lépni Önnel, hogy miként kérése](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Kattintson a **aktiválás** az aktiválási panel megnyitásához.

    ![Aktiválási ablak kezdő időpontja, időtartama, jegy és ok megadása](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Ha szükséges, adja meg egy egyéni Aktiválás kezdete.

1. Adja meg az aktiválási időtartamát.

1. Az a **aktiválási OK** adja meg az aktiválási kérés okát. Egyes szerepkörök szükséges egy probléma jegyszám fogja tartalmazni.

    ![Egy egyéni kezdő időpontja, időtartama, jegy és OK befejezett aktiválási panelről](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Kattintson a **aktiválása**.

    Ha a szerepkör nem igényel jóváhagyási, egy **aktiválási állapota** panelen megjelennek az aktiválási állapotát jeleníti meg.

    ![Aktiválási állapot oldalról az aktiválás három fázisa](./media/pim-how-to-activate-role/activation-status.png)

    Ha minden szakasz befejeződött, kattintson a **Kijelentkezés** jelentkezzen ki az Azure Portalon mutató hivatkozást. Amikor bejelentkezik vissza a portálra, most már használhatja a szerepkört.

    Ha a [a szerepkör megköveteli a jóváhagyási](./azure-ad-pim-approval-workflow.md) aktiválni, egy értesítés jelenik meg a böngészőben arról értesíti, a kérelem van függőben lévő jóváhagyási jobb felső sarkában található.

    ![Aktiválási kérelem függőben lévő jóváhagyási értesítés](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti a függőben lévő aktiválási kéréseit állapotát.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **saját kérések** a kérések listájának megtekintéséhez.

    ![Az Azure AD-szerepkörök – saját kérések listája](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>A szerepkör inaktiválása

Miután aktiválta már a szerepkör, azt automatikusan inaktiválja az időkorlátot (jogosult időtartam) elérésekor.

Ha a rendszergazdai feladatok korai végrehajtani, egy szerepkörhöz manuálisan az Azure AD Privileged Identity Management is inaktiválhatók.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **saját szerepkörök**.

1. Kattintson a **aktív szerepkörök** az aktív szerepkörök listájának megtekintéséhez.

1. A szerepkör végzett keresés használatával, és kattintson a **inaktiválás**.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Jóváhagyást igénylő szerepkörök aktiválási nincs szükség, ha egy függőben lévő kérelem bármikor visszavonhatja.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **Azure AD-szerepkörök**.

1. Kattintson a **saját kérések**.

1. A szerepkör, amely megszakítja, kattintson a **Mégse** gombra.

    Ha a Mégse gombra kattint, a kérelem meg lesz szakítva. A szerepkör aktiválását ismét el küldjön új aktiválási kérelmet.

   ![Saját kérések listája, a Mégse gomb kiemelésével](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nem engedélyekkel a szerepkör aktiválása után

A PIM szerepkör aktiválásakor az aktiválás előfordulhat, hogy nem azonnal továbbítódik az összes portálok, a kiemelt szerepkörű igénylő. Előfordul akkor is, ha propagálja a módosítást, webes gyorsítótárazás a portál az eredményezhet a módosítás nem hatállyal. Ha az aktiválás késik, Íme, mit kell tennie.

1. Jelentkezzen ki az Azure Portalon, és majd jelentkezzen be újra.

    Aktiválja az Azure AD-szerepkörhöz, látni fogja az aktiválás fázisa. Ha minden szakasz befejeződött, megjelenik egy **Kijelentkezés** hivatkozásra. Ez a hivatkozás segítségével jelentkezzen ki. Aktiválási késleltetést a legtöbb esetben ez fogja megoldani.

1. A PIM ellenőrizze, hogy a szerepkör tagjaként szerepel.

## <a name="next-steps"></a>További lépések

- [A PIM saját Azure-erőforrás szerepkörök aktiválása](pim-resource-roles-activate-your-roles.md)
