---
title: Önkiszolgáló jelszó-visszaállítás testreszabása – Azure Active Directory
description: Megtudhatja, hogyan szabhatja testre a felhasználói megjelenítési és felhasználói élmény lehetőségeit az Azure AD önkiszolgáló jelszó-visszaállításhoz
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394253"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Az Azure Active Directory önkiszolgáló jelszó-visszaállításfelhasználói élményének testreszabása

Az önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi az Azure Active Directory (Azure AD) felhasználóiszámára a jelszó módosítását vagy alaphelyzetbe állítását rendszergazda vagy ügyfélszolgálati közreműködés nélkül. Ha egy felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, követheti a figyelmeztetést, hogy feloldja a blokkolást, és visszatérjen a munkához. Ez a képesség csökkenti az ügyfélszolgálati hívásokat és a termelékenység csökkenését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba.

A felhasználók SSPR-élményének javítása érdekében testreszabhatja a jelszó-visszaállítási lap, az e-mail értesítések vagy a bejelentkezési oldalak megjelenését. Ezekkel a testreszabási lehetőségekkel egyértelművé teheti a felhasználó számára, hogy a megfelelő helyen vannak, és magabiztosságot adhat nekik a vállalati erőforrások elérésében.
    
Ebből a cikkből megtudhatja, hogyan szabhatja testre az SSPR e-mail hivatkozását a felhasználók, a vállalat márkajelzése és az AD FS bejelentkezési lap hivatkozása számára.

## <a name="customize-the-contact-your-administrator-link"></a>A "Kapcsolatfelvétel a rendszergazdával" hivatkozás testreszabása

Annak érdekében, hogy a felhasználók segítséget nyújthassunk az önkiszolgáló jelszó-visszaállításhoz, a jelszó-visszaállítási portálon megjelenik a "Forduljon a rendszergazdához" hivatkozás. Ha egy felhasználó ezt a hivatkozást választja, két dolgot tesz:

* Ha ez a kapcsolati hivatkozás az alapértelmezett állapotban marad, a rendszer e-mailt küld a rendszergazdáknak, és megkéri őket, hogy adjanak segítséget a felhasználó jelszavának módosításához. A következő minta e-mail az alapértelmezett e-mail üzenetet jeleníti meg:

    ![Mintakérelem a rendszergazdának küldött e-mailek alaphelyzetbe állításához](./media/howto-sspr-customization/sspr-contact-admin.png)

* Ha testre van szabva, a felhasználót a rendszergazda által megadott weblapra vagy e-mail címre küldi segítségért.
    * Ha ezt testreszabja, azt javasoljuk, hogy ezt olyan ra kvanva, amit a felhasználók már ismernek támogatásként.

    > [!WARNING]
    > Ha ezt a beállítást olyan e-mail címmel és fiókkal szabja testre, amely jelszó-visszaállítást igényel, előfordulhat, hogy a felhasználó nem tud segítséget kérni.

### <a name="default-email-behavior"></a>Az e-mailek alapértelmezett viselkedése

Az alapértelmezett kapcsolattartási e-mailt a rendszer a következő sorrendben küldi el a címzetteknek:

1. Ha az *ügyfélszolgálati rendszergazdai* szerepkör vagy *jelszó-rendszergazdai* szerepkör van hozzárendelve, az ilyen szerepkörrel rendelkező rendszergazdák értesítést kapnak.
1. Ha nincs hozzárendelt rendszergazda vagy jelszó-rendszergazda, a *rendszergazdai* szerepkörrel rendelkező rendszergazdák értesítést kapnak.
1. Ha az előző szerepkörök egyike sincs hozzárendelve, a rendszer értesíti a *globális rendszergazdákat.*

Minden esetben legfeljebb 100 címzettet értesítenek.

Ha többet szeretne megtudni a különböző rendszergazdai szerepkörökről és hozzárendelésükről, olvassa el [a Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakört.](../users-groups-roles/directory-assign-admin-roles.md)

### <a name="disable-contact-your-administrator-emails"></a>A "Kapcsolatfelvétel a rendszergazdával" e-mailek letiltása

Ha a szervezet nem szeretne értesítést küldeni a rendszergazdáknak a jelszó-visszaállítási kérelmekről, a következő konfigurációs beállítások használhatók:

* Testreszabhatja az helpdesk hivatkozást, hogy webes URL-t vagy mailto-címet adjon meg: címet, amelyet a felhasználók a segítség hez használhatnak. Ez a beállítás a **Jelszó visszaállítása** > **testreszabás egyéni** > **helpdesk e-mail vagy URL**csoportban található.
* Engedélyezze az önkiszolgáló jelszó-visszaállítást minden felhasználó számára. Ez a beállítás a **Jelszó-visszaállítás** > **tulajdonságai csoportban található.** Ha nem szeretné, hogy a felhasználók alaphelyzetbe állítsák saját jelszavukat, hatókör-hozzáférést biztosíthat egy üres csoporthoz. *Nem javasoljuk ezt a lehetőséget.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>A bejelentkezési lap és a hozzáférési panel testreszabása

Testreszabhatja a bejelentkezési lapot, például hozzáadhat egy emblémát, amely a vállalati márkajelzéshez illeszkedő képpel együtt jelenik meg. A vállalati márkajelzés konfigurálásáról a [Vállalati márkajelzés hozzáadása a bejelentkezési laphoz az Azure AD-ben](../fundamentals/customize-branding.md)című témakörben talál további információt.

A kiválasztott grafikák a következő körülmények között jelennek meg:

* Miután a felhasználó megadta a felhasználónevét
* Ha a felhasználó hozzáfér a testreszabott URL-címhez:
   * A paraméter `whr` nek a jelszó-visszaállítási oldalra való átadva, például`https://login.microsoftonline.com/?whr=contoso.com`
   * A paraméter `username` nek a jelszó-visszaállítási oldalra való átadva, például`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Könyvtár neve

Ha felhasználóbarátabbá szeretné tenni a dolgokat, módosíthatja a szervezet nevét a portálon és az automatizált kommunikációban. Ha módosítani szeretné a könyvtárnév attribútumot az Azure Portalon, keresse meg az **Azure Active Directory** > **tulajdonságai**című. Ez a barátságos szervezetnév-beállítás a leginkább látható az automatikus e-mailekben, mint a következő példákban:

* A rövid név az e-mailben, például "*Microsoft nevében CONTOSO demo*"
* A tárgy az e-mailben, például "*CONTOSO demo fiók e-mail ellenőrző kód*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Az AD FS bejelentkezési lapjának testreszabása

Ha az Active Directory összevonási szolgáltatásokat (AD FS) használja a felhasználói bejelentkezési eseményekhez, a bejelentkezési lapra mutató hivatkozást a [bejelentkezési lap leírásának hozzáadása](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)című cikkben található útmutatás sal adhat hozzá.

Adjon meg a felhasználóknak egy hivatkozást a lapra, *https://passwordreset.microsoftonline.com*hogy beléphessenek az SSPR-munkafolyamatba, például . Az AD FS bejelentkezési lapra mutató hivatkozás hozzáadásához használja a következő parancsot az AD FS-kiszolgálón:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>További lépések

Az SSPR környezetben való használatának megértéséhez olvassa el [az Azure AD jelszókezelés jelentéskészítési beállításai](howto-sspr-reporting.md)című témakört.

Ha Önnek vagy felhasználóinak problémái vannak az SSPR-rel, olvassa el [az Önkiszolgáló jelszó-visszaállítás hibaelhárítása című témakört.](active-directory-passwords-troubleshoot.md)
