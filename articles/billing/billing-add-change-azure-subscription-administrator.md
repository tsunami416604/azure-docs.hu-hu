---
title: "Hozzáadása vagy módosítása az Azure rendszergazdai előfizetés szerepkörök |} Microsoft Docs"
description: "Ismerteti, hogyan hozzáadása vagy módosítása az Azure Társadminisztrátoraként, a szolgáltatás-rendszergazda és a fiók rendszergazdájához"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.openlocfilehash: 178d7fbc17464f7c9a4bc891453d0999e4ba62c3
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Hozzáadása vagy módosítása, hogy az előfizetés vagy a szolgáltatások kezelése az Azure rendszergazdai szerepkörök

Módosíthatja az Azure rendszergazdai, amely kezeli az Azure-előfizetéshez és nem is kezeli az előfizetésében használt Azure-szolgáltatásokhoz. Azure számlázási adatokat előfizetések megtekintése és kezelése, akkor be kell jelentkeznie a fiók középre fiók rendszergazdaként. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Adja hozzá a Szerepalapú tulajdonos rendszergazda-előfizetéshez tartozó Azure-portálon 

Valaki hozzáadásához az előfizetéshez az Azure portálon rendszergazdaként ajánlott engedélyezése egy [RBAC](../active-directory/role-based-access-control-configure.md) tulajdonosi szerepkört. A tulajdonosi szerepkört kezelheti az erőforrásokat az előfizetéshez társított, és nem rendelkezik hozzáférési jogosultság más előfizetésekkel is. A tulajdonosok keresztül hozzáadhat a [Azure-portálon](https://portal.azure.com) erőforrás nem tudja kezelni a [a klasszikus Azure portálon](https://manage.windowsazure.com).

1. Jelentkezzen be a [előfizetések megtekintéséhez az Azure portálon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, amelyet a rendszergazda eléréséhez.
1. Válassza ki **hozzáférés-vezérlés (IAM)** a menüben.
1. Válassza ki **hozzáadása** > **szerepkör** > **tulajdonos**. Írja be a kívánt tulajdonosa adja, jelölje ki a felhasználót, és válassza ki a felhasználó e-mail címe **mentése**.

    ![A tulajdonosi szerepkört a kiválasztott képernyőkép](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>Hozzáadása vagy módosítása társadminisztrátoraként

Csak egy olyan tulajdonost társadminisztrátoraként adhatók hozzá. Szerepkörök, például közreműködői és ahhoz való olvasóra rendelkező más felhasználók társadminisztrátorként nem adható hozzá.

1. Még nem tette meg, ha valaki egy olyan tulajdonost, a fenti utasításokat követve adja hozzá.
2. **Kattintson a jobb gombbal** a tulajdonosa, az előzőekben adott hozzá, és válassza **közös rendszergazdaként Hozzáadás**. Ha nem látja a **közös rendszergazdaként Hozzáadás** lehetőségét, friss az oldalon, vagy próbálkozzon egy másik böngészőben. 

     ![Képernyőkép a társadminisztrátoraként hozzáadása](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >Kell hozzáadnia a "Tulajdonos" fiók a közös rendszergazdaként, ha a felhasználónak van szüksége az Azure-szolgáltatások kezelése [a klasszikus Azure portálon](https://manage.windowsazure.com/).

    Eltávolítja a közös rendszergazdai jogosultsággal, **kattintson a jobb gombbal** "Közös rendszergazda" felhasználói és válassza ki **közös rendszergazda eltávolítása**.

    ![Képernyőkép a társadminisztrátoraként eltávolítása](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Módosítsa az Azure-előfizetés szolgáltatás-rendszergazda

Csak a Fiókadminisztrátor az előfizetés szolgáltatás-rendszergazda módosíthatja. Alapértelmezés szerint amikor regisztrál, a szolgáltatás-rendszergazda megegyezik a fiók-rendszergazdaként.

1. Győződjön meg arról, hogy a forgatókönyv által támogatott a [vonatkozó szolgáltatás-rendszergazdák módosítása](#limits).
1. Jelentkezzen be [Account Center](https://account.windowsazure.com/subscriptions) fiók rendszergazdaként.
1. Válasszon egy előfizetést.
1. A jobb oldalon válassza ki a **előfizetés részleteinek szerkesztése**.

    ![Képernyőfelvétel a Szerkesztés előfizetés gomb Account Center](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Az a **szolgáltatás-rendszergazda** mezőbe írja be az e-mail cím a új szolgáltatás rendszergazdájával.

    ![Képernyőfelvétel a mezőbe a szolgáltatás-rendszergazdák e-mail módosítása](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Szolgáltatás-rendszergazdák módosítása vonatkozó korlátozások

* Az Azure AD-címtár minden előfizetés tartozik. A könyvtárat, az előfizetéshez társított találja, a [a klasszikus Azure portálon](https://manage.windowsazure.com/), jelölje be **beállítások** > **előfizetések**. Ellenőrizze az előfizetés-azonosító található a könyvtárban.
* Ha jelentkezett be a munkahelyi vagy iskolai fiókkal, a más fiókokat is hozzáadhat a szolgáltatás-rendszergazdaként a szervezetében. Például abby@contoso.com adhat hozzá bob@contoso.com , szolgáltatás-rendszergazda nem adható hozzá, de john@notcontoso.com kivéve, ha john@notcontoso.com rendelkezik-e a contoso.com directory jelenlétét. Munkahelyi vagy iskolai fiókok bejelentkezve felhasználók továbbra is vegye fel a Microsoft Account felhasználók szolgáltatás-rendszergazdaként.

  | Bejelentkezési módszer | Adja hozzá a Microsoft Account felhasználó rendszergazdai (SA)? | Adja hozzá a munkahelyi vagy iskolai fiókjával társításként ugyanazon a szervezeten belül? | Adja hozzá a munkahelyi vagy iskolai fiókjával társításként másik vállalatnál? |
  | --- | --- | --- | --- |
  |  Microsoft-fiók |Igen |Nem |Nem |
  |  Munkahelyi vagy iskolai fiókkal |Igen |Igen |Nem |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>A Fiókadminisztrátor az Azure-előfizetés módosítása

A Fiókadminisztrátor az előfizetés módosításához lásd [egy másik fiókot az Azure-előfizetés tulajdonjogának átruházása](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Nem biztos benne, aki a fiók rendszergazdájához?** Kövesse az alábbi lépéseket:

1. Jelentkezzen be a [előfizetések megtekintéséhez az Azure portálon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést, és ellenőrizze, majd keresse meg a **beállítások**.
1. Válassza ki **tulajdonságok**. A fiókadminisztrátor az előfizetés jelenik meg a **Fiókadminisztrátor** mezőbe.  

## <a name="types-of-azure-admin-accounts"></a>Az Azure rendszergazdai fiókok típusai

 Fiók rendszergazdájához, szolgáltatás-rendszergazda és társadminisztrátoraként háromféle a Microsoft Azure-ban rendszergazdai szerepkörrel. A következő táblázat ismerteti ezeket három rendszergazdai szerepkörök közötti különbség.

| Rendszergazdai szerepkör | Korlát | Leírás |
| --- | --- | --- |
| A Fiókadminisztrátor (AA) |1 / Azure-fiók |Ez az a személy, aki regisztrált a vásárolt Azure-előfizetések és elérésére jogosult a [Account Center](https://account.azure.com/Subscriptions) és különféle felügyeleti feladatok elvégzésére. Például előfizetések létrehozása, előfizetések megszakítja, módosítsa az előfizetés számlázási, és módosítsa a szolgáltatás-rendszergazda. |
| Szolgáltatás-rendszergazdai (SA) |1 / Azure-előfizetés |Ezt a szerepkört a szolgáltatások kezelésére jogosult a [Azure-portálon](https://portal.azure.com). Alapértelmezés szerint egy új előfizetés a fiók rendszergazdájához is a szolgáltatás rendszergazdájával. |
| A társadminisztrátoraként (CA) a [a klasszikus Azure portálon](https://manage.windowsazure.com) |200 előfizetésenként |Ez a szerepkör ugyanazokkal a hozzáférési jogosultságokkal rendelkezik, mint a szolgáltatás-rendszergazda, de nem módosíthatja az előfizetések és az Azure-címtárak közötti társítást. |

Az Azure Active Directory szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi a felhasználóknak adható hozzá több szerepkört. További információkért lásd: [Azure Active Directory szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>További információ az erőforrás hozzáférés-vezérlés és az Active Directory

* Hogyan szabályozott erőforrások elérése a Microsoft Azure-ban kapcsolatos további információkért lásd: [az az Azure-erőforrások hozzáférésének megismerése](../active-directory/active-directory-understanding-resource-access.md).
* Azure Active Directoryval kapcsolatos további információkért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) és [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
