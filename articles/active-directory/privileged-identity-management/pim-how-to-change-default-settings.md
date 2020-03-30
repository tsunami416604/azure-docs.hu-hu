---
title: Az Azure AD szerepkör-beállításainak konfigurálása a PIM-ben – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja az Azure AD szerepkör-beállításait az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd525973a8c8ba5bb3103126b4ddb5b6973590a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205024"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Az Azure AD szerepkör-beállításainak konfigurálása a kiemelt identitáskezelésben

A kiemelt szerepkör-rendszergazda testreszabhatja a kiemelt identitáskezelés (PIM) az Azure Active Directory (Azure AD) szervezet, beleértve a felhasználói élmény módosítása a felhasználó, aki aktiválja a jogosult szerepkör-hozzárendelés.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

2019 novemberétől kezdődően a kiemelt identitáskezelés Azure AD-szerepkörök része frissül egy új verzióra, amely megfelel az Azure-erőforrás-szerepkörök élményének. Ez további funkciókat, valamint [a meglévő API-t is módosítja.](azure-ad-roles-features.md#api-changes) Az új verzió bevezetés alatt, hogy mely eljárásokat követi ebben a cikkben attól függ, hogy a kiemelt identitáskezelés jelenleg rendelkezik-e. Ebben a szakaszban ismertetett lépéseket követve határozza meg, hogy a Kiemelt identitáskezelés melyik verzióját használja. Miután ismeri a kiemelt identitáskezelés verzióját, kiválaszthatja a cikkben az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben van.
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** Ha az áttekintő oldal tetején szalaghirdetés található, kövesse a cikk **Új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **Előző verzió** lap utasításait.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Kövesse a cikkben leírt lépéseket az Azure AD-szerepkörökre vonatkozó kérelmek jóváhagyásához vagy elutasításához.

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépésekkel nyissa meg az Azure AD-szerepkör beállításait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben lévő felhasználóval.
Gt
1. Nyissa meg **az Azure AD kiemelt identitáskezelési** &gt; **Azure AD szerepkör-beállításait.** &gt; **Role settings**

    ![Az Azure AD-szerepköröket felsoroló szerepkör-beállítások lap](./media/pim-how-to-change-default-settings/role-settings.png)

1. Válassza ki azt a szerepkört, amelynek beállításait konfigurálni szeretné.

    ![Szerepkör-beállítás részletei lap felsorolja több hozzárendelési és aktiválási beállítások](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. A **Szerkesztés gombra** a Szerepkör-beállítások lap megnyitásához kattintson a Szerkesztés gombra.

    ![Szerepkör-beállítások szerkesztése lap a hozzárendelési és aktiválási beállítások frissítési beállításaival](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    Az egyes szerepkörek szerepkör-beállítási ablaktábláján több beállítás is konfigurálható.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

A szerepkör beállításainak konfigurálásakor az egyes hozzárendeléstípusokhoz (jogosult és aktív) két hozzárendelés-időtartam-beállítás közül választhat. Ezek a beállítások lesznek az alapértelmezett maximális időtartam, amikor egy felhasználó van rendelve a szerepkör privilegizált identitáskezelés.

A **következő jogosult** hozzárendelési időtartam-beállítások közül választhat:

| | |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | A globális rendszergazdák és a kiemelt szerepkör-rendszergazdák állandó jogosult hozzárendelést rendelhetnek hozzá. |
| **Jogosult hozzárendelés lejárata után** | A globális rendszergazdák és a kiemelt szerepkör-rendszergazdák megkövetelhetik, hogy minden jogosult hozzárendelések egy megadott kezdési és befejezési dátum. |

Az **aktív** hozzárendelés idotartama lehetőségek közül választhat:

| | |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | A globális rendszergazdák és a kiemelt szerepkör-rendszergazdák állandó aktív hozzárendelést rendelhetnek hozzá. |
| **Aktív hozzárendelés lejárata után** | A globális rendszergazdák és a kiemelt szerepkör-rendszergazdák megkövetelhetik, hogy minden aktív hozzárendelésnek meghatározott kezdési és befejezési dátummal kell rendelkeznie. |

> [!NOTE]
> A megadott befejezési dátummal rendelkező összes hozzárendelést a globális rendszergazdák és a kiemelt szerepkör-rendszergazdák megújíthatják. A felhasználók önkiszolgáló kéréseket is kezdeményezhetnek a [szerepkör-hozzárendelések bővítésére vagy megújítására.](pim-resource-roles-renew-extend.md)

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A kiemelt identitáskezelés az Azure többtényezős hitelesítés opcionális kényszerítése két különböző forgatókönyv esetén.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Többtényezős hitelesítés megkövetelése aktív hozzárendelésesetén

Bizonyos esetekben előfordulhat, hogy egy felhasználót rövid ideig (például egy napig) szeretne egy szerepkörhöz rendelni. Ebben az esetben a hozzárendelt felhasználóknak nem kell aktiválást kérni. Ebben a forgatókönyvben a kiemelt identitáskezelés nem kényszerítheti ki a többtényezős hitelesítést, ha a felhasználó a szerepkör-hozzárendelést használja, mert már aktív a szerepkörben a hozzárendelés időpontjától kezdve.

Annak érdekében, hogy a hozzárendelést teljesítő rendszergazda az legyen, akinek mondják magát, az aktív hozzárendelésnél a többtényezős hitelesítést az **aktív hozzárendelés többtényezős hitelesítésének megkövetelése** jelölőnégyzet bejelölésével kényszerítheti ki.

### <a name="require-multi-factor-authentication-on-activation"></a>Többtényezős hitelesítés megkövetelése aktiváláskor

Megkövetelheti a szerepkörre jogosult felhasználóktól, hogy az aktiválás előtt bizonyítsák, hogy kit használnak az Azure többtényezős hitelesítésével. A többtényezős hitelesítés biztosítja, hogy a felhasználó az, akinek mondja magát, és kellő bizonyossággal. A beállítás kényszerítése megvédi a kritikus erőforrásokat olyan helyzetekben, amikor a felhasználói fiók biztonsága sérülhet.

Ha az aktiválás előtt többtényezős hitelesítést szeretne igényelni, jelölje be a **Többtényezős hitelesítés megkövetelése aktiváláskor** jelölőnégyzetet a Hozzárendelés csoport **Szerepkörszerkesztése beállításának**Hozzárendelés lapján.

További információ: [Többtényezős hitelesítés és kiemelt identitáskezelés.](pim-how-to-require-mfa.md)

## <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Az **Aktiválás maximális** időtartamcsúszkája segítségével beállíthatja azt a maximális időtartamot órákban, amerre egy szerepkör aktív marad, mielőtt lejárna. Ez az érték 1 és 24 óra között lehet.

## <a name="require-justification"></a>Sorkizárás megkövetelése

Megkövetelheti, hogy a felhasználók az aktiváláskor adjanak meg üzleti indoklást. Ha sorkizárást szeretne igényelni, jelölje be az **Aktív hozzárendelés sorkizárásának megkövetelése** vagy **az Aktiválások megkövetelése** jelölőnégyzetet.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyás szükséges

Ha több jóváhagyóbeállítást állít be, a jóváhagyás akkor fejeződik be, amikor egyikük jóváhagyja vagy elutasítja. Legalább két felhasználó jóváhagyása nem követelhet meg jóváhagyást. A szerepkör aktiválásához jóváhagyás szükséges, kövesse az alábbi lépéseket.

1. Jelölje be az **Aktiváláshoz jóváhagyás megkövetelése** jelölőnégyzetet.

1. Válassza **a Jóváhagyók kiválasztása**lehetőséget.

    ![Felhasználó vagy csoport ablaktáblájának kijelölése jóváhagyók kiválasztásához](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Jelöljön ki legalább egy felhasználót, majd kattintson **a Kijelölés gombra.** Legalább egy jóváhagyót ki kell jelölnie. Nincsenek alapértelmezett jóváhagyók.

    A kiválasztott jóváhagyók listájában megjelennek a beállítások.

1. Miután megadta az összes szerepkör-beállítást, válassza a **Frissítés** lehetőséget a módosítások mentéséhez.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="open-role-settings"></a>Szerepkör-beállítások megnyitása

Az alábbi lépésekkel nyissa meg az Azure AD-szerepkör beállításait.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza a **Beállítások lehetőséget.**

    ![Azure AD-szerepkörök – Beállítások](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Válassza a **Szerepkörök lehetőséget.**

1. Válassza ki azt a szerepkört, amelynek beállításait konfigurálni szeretné.

    ![Azure AD-szerepkörök – Beállítási szerepkörök](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Az egyes szerepkörek beállítási lapján több beállítás is konfigurálható. Ezek a beállítások csak **azokat** a felhasználókat érintik, akik jogosultak a hozzárendelésre, az **állandó** hozzárendeléseket nem.

## <a name="activations"></a>Aktiválások

Az **Aktiválások** csúszkával beállíthatja azt a maximális időtartamot órákban, amerre egy szerepkör aktív marad, mielőtt lejárna. Ez az érték 1 és 72 óra között lehet.

## <a name="notifications"></a>Értesítések

Az **Értesítések** kapcsolóval megadhatja, hogy a rendszergazdák e-mailben értesítést kapjanak-e a szerepkörök aktiválásakor. Ez az értesítés hasznos lehet a jogosulatlan vagy törvénytelen aktiválások észleléséhez.

Ha **az Engedélyezés**beállítás van megállítva, a rendszer értesítéseket küld a következő kretén:

- Kiemelt szerepkör-rendszergazda
- Biztonsági rendszergazda
- Globális rendszergazda

További információt az [E-mail értesítések a Kiemelt identitáskezelés ben](pim-email-notifications.md)című témakörben talál.

## <a name="incidentrequest-ticket"></a>Incidens/jegy kérése

Használja az **Incidens/Jegykérés** kapcsolót, hogy a jogosult rendszergazdáknak meg kell adniuk a jegyszámot a szerepköraktiváláskor. Ez a gyakorlat hatékonyabbá teheti a szerepkör-hozzáférési naplózást.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A **többtényezős hitelesítés** kapcsolóval megadhatja, hogy a felhasználóknak ellenőrizniük kell-e identitásukat az MFA-val, mielőtt aktiválhatnák a szerepköreiket. Munkamenetenként csak egyszer kell igazolniuk a személyazonosságukat, nem minden alkalommal, amikor aktiválnak egy szerepkört. Az MFA engedélyezésekor két tippet kell szem előtt tartania:

- Azok a felhasználók, akik Microsoft-fiókkal rendelkeznek az e-mail címükhöz (általában @outlook.com, de nem mindig), nem regisztrálhatnak az Azure többtényezős hitelesítéséhez. Ha szerepköröket szeretne hozzárendelni a Microsoft-fiókkal rendelkező felhasználókhoz, vagy állandó rendszergazdává kell tennie őket, vagy le kell tiltania a többtényezős hitelesítést az adott szerepkörhöz.
- Az Azure Multi-Factor Authentication nem tilthatja le az Azure AD és az Office 365 magas szintű jogosultsággal rendelkező szerepkörök esetében. Ez a biztonsági funkció a következő szerepkörök védelmét segíti:  
  
  - Az Azure information protection rendszergazdája
  - Számlázási rendszergazda
  - Felhőalkalmazás-rendszergazda
  - Megfelelőségi rendszergazda
  - Feltételes hozzáférésű rendszergazda
  - A Dynamics 365 rendszergazdája
  - Ügyfél lockbox hozzáférés jóváhagyója
  - Címtár írók
  - Exchange-rendszergazda
  - Globális rendszergazda
  - Intune-rendszergazda
  - Power BI-rendszergazda
  - Kiemelt szerepkör-rendszergazda
  - Biztonsági rendszergazda
  - SharePoint-rendszergazda
  - Skype Vállalati verzió-rendszergazda
  - Rendszergazda

További információ: [Többtényezős hitelesítés és kiemelt identitáskezelés.](pim-how-to-require-mfa.md)

## <a name="require-approval"></a>Jóváhagyás megkövetelése

Ha delegálni szeretné a szerepkör aktiválásához szükséges jóváhagyást, kövesse az alábbi lépéseket.

1. Állítsa a **Jóváhagyás megkövetelése** kapcsolót **Engedélyezve értékre.** Az ablaktábla kibővül a jóváhagyók kijelölésének lehetőségeivel.

    ![Azure AD-szerepkörök – Beállítások – Jóváhagyás megkövetelése](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Ha nem ad meg jóváhagyókat, a kiemelt szerepkör-rendszergazda lesz az alapértelmezett jóváhagyó, és ezután jóvá kell hagynia a szerepkör összes aktiválási kérelmét.

1. Jóváhagyók hozzáadásához kattintson **a Jóváhagyók kijelölése gombra.**

    ![Azure AD-szerepkörök – Beállítások – Jóváhagyás megkövetelése](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Jelöljön ki egy vagy több jóváhagyót a Kiemelt szereprendszergazda mellett, majd kattintson a **Kijelölés gombra.** Azt javasoljuk, hogy adjon hozzá legalább két jóváhagyók. Még akkor is, ha önmagát nevezi meg jóváhagyóként, nem hagyhatja jóvá a szerepkör aktiválását. A kiválasztott jóváhagyók listájában megjelennek a beállítások.

1. Miután megadta az összes szerepkör-beállítást, a **Mentés gombra** választ a módosítások mentéséhez.

---

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
- [Biztonsági riasztások konfigurálása az Azure AD-szerepkörökhöz a kiemelt identitáskezelésben](pim-how-to-configure-security-alerts.md)
