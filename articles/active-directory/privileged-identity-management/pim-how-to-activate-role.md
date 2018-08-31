---
title: A PIM az Azure AD-címtárbeli szerepkörök aktiválása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet aktiválni az Azure AD-címtárbeli szerepkörök az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7c990fc0d385af9527bf55339b2fa617e589ec0e
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190586"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>A PIM az Azure AD-címtárbeli szerepkörök aktiválása

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) egyszerűbbé teszi, hogy hogyan kezelhetik a vállalatok számára az erőforrások az Azure AD és más Microsoft online szolgáltatásaihoz, például az Office 365 vagy a Microsoft Intune emelt szintű hozzáférés.  

Ha Ön történtek egy rendszergazdai szerepkör jogosult, ez azt jelenti, is aktiválhatja a szerepkört, amikor szüksége van privilegizált műveletek elvégzéséhez. Például alkalmanként kezelheti az Office 365-funkciókat, ha a szervezet a kiemelt szerepkörű rendszergazdák nem teheti elérhetővé, állandó globális rendszergazda, mivel az adott szerepkör milyen hatással van más szolgáltatások túl. Ehelyett, győződjön meg arról, hogy jogosult az Azure AD-szerepkörök, például az Exchange Online rendszergazdai. Kérheti, hogy a szerepkör aktiválása, ha a jogosultságok szükségesek, és ezután lesz rendszergazdai felügyelete egy előre meghatározott időtartamra.

Ez a cikk a rendszergazdák számára, miközben a PIM-ben betöltött szerepkörük aktiválnia kell.

## <a name="activate-a-role"></a>A szerepkör aktiválása

Amikor szüksége van egy szerepkör, aktiválás segítségével kérheti a **saját szerepkörök** navigációs lehetőség az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**. A PIM-csempét az irányítópulton adásával kapcsolatos információkért lásd: [használatához a PIM](pim-getting-started.md).

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **saját szerepkörök** a jogosult listájának megtekintéséhez az Azure AD-címtárbeli szerepkörök.

    ![Az Azure AD-címtárbeli szerepkörök – saját szerepkörök](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Keresse meg egy szerepkör, amely aktiválni szeretné.

    ![Az Azure AD-címtárbeli szerepkörök - szerepkörök listáját](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kattintson a **aktiválás** a szerepkör aktiválási részletei ablak megnyitásához.

1. Ha a szerepkör megköveteli a multi-factor authentication (MFA), kattintson a **a folytatás előtt azonosítsa magát**. Csak egyszer munkamenetenként hitelesítésre van.

    ![Ellenőrizze a többtényezős hitelesítéssel, mielőtt a szerepkör aktiválása](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Kattintson a **saját identitás ellenőrzése** és kövesse az utasításokat a további biztonsági ellenőrzést nyújtanak.

    ![További biztonsági ellenőrzés](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Kattintson a **aktiválás** az aktiválási panel megnyitásához.

    ![Az aktiválás panel](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Ha szükséges, adja meg egy egyéni Aktiválás kezdete.

1. Adja meg az aktiválási időtartamát.

1. Az a **aktiválási OK** adja meg az aktiválási kérés okát. Egyes szerepkörök szükséges egy probléma jegyszám fogja tartalmazni.

    ![Befejezett aktiválási panel](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Kattintson a **aktiválása**.

    Ha a szerepkör nem igényel jóváhagyási, most már aktiválva van, és a szerepkör megjelenik az aktív szerepkörök listájában. Ha a [a szerepkör megköveteli a jóváhagyási](./azure-ad-pim-approval-workflow.md) aktiválni, egy értesítés jelenik meg a böngészőben arról értesíti, a kérelem van függőben lévő jóváhagyási jobb felső sarkában található.

    ![Értesítés függőben lévő kérelem](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti a függőben lévő aktiválási kéréseit állapotát.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **saját kérések** a kérések listájának megtekintéséhez.

    ![Az Azure AD-címtárbeli szerepkörök – saját kérések](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="use-a-role-immediately-after-activation"></a>Olyan szerepkört használjon az aktiválás után azonnal

Gyorsítótárazás miatt aktiválások nem történik meg azonnal a frissítés nélkül az Azure Portalon. Ha az késleltetések lehetőségét csökkentheti a szerepkör aktiválása után van szüksége, használhatja a **alkalmazás-hozzáférés** lapot a portálon. Ezen a lapon elérhető alkalmazások azonnal ellenőrizze az új szerepkör-hozzárendeléseket.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **alkalmazás-hozzáférés** lapot.

    ![A PIM alkalmazás-hozzáférés](./media/pim-how-to-activate-role/pim-application-access.png)

1. Kattintson a **Azure Active Directory** , nyissa meg újra a portálon a a **minden felhasználó** lapot.

    Erre a hivatkozásra kattint, frissítésének kényszerítése, ha van egy jelölőnégyzetet az új Azure AD szerepkör-hozzárendeléseket.

## <a name="deactivate-a-role"></a>A szerepkör inaktiválása

Miután aktiválta már a szerepkör, azt automatikusan inaktiválja az időkorlátot (jogosult időtartam) elérésekor.

Ha a rendszergazdai feladatok korai végrehajtani, egy szerepkörhöz manuálisan az Azure AD Privileged Identity Management is inaktiválhatók.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **saját szerepkörök**.

1. Kattintson a **aktív szerepkörök** az aktív szerepkörök listájának megtekintéséhez.

1. A szerepkör végzett keresés használatával, és kattintson a **inaktiválás**.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Jóváhagyást igénylő szerepkörök aktiválási nincs szükség, ha egy függőben lévő kérelem bármikor visszavonhatja.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **az Azure AD-címtárbeli szerepkörök**.

1. Kattintson a **saját kérések**.

1. A szerepkör, amely megszakítja, kattintson a **Mégse** gombra.

    Ha a Mégse gombra kattint, a kérelem megszakítása A szerepkör aktiválását ismét el küldjön új aktiválási kérelmet.

   ![Függőben lévő kérelem megszakítása](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="next-steps"></a>További lépések

- [A PIM saját Azure-erőforrás szerepkörök aktiválása](pim-resource-roles-activate-your-roles.md)
