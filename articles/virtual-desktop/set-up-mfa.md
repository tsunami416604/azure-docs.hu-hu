---
title: Az Azure multi-Factor Authentication beállítása a Windows rendszerű virtuális asztali környezethez – Azure
description: Az Azure multi-Factor Authentication beállítása a fokozott biztonság érdekében a Windows Virtual Desktopban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998471"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication beállítása

A Windows rendszerű virtuális asztal Windows-ügyfele kiváló megoldás a Windows rendszerű virtuális asztalok helyi géppel való integrálására. Ha azonban a Windows rendszerű virtuális asztali fiókját a Windows-ügyfélre konfigurálja, bizonyos intézkedésekre van szükség, hogy a felhasználók biztonságban maradjanak.

Amikor először jelentkezik be, az ügyfél a felhasználónevet, a jelszót és az Azure MFA-t kéri. Ezután, amikor legközelebb bejelentkezik, az ügyfél a Azure Active Directory (AD) vállalati alkalmazásból emlékszik a tokenre. Ha a **Megjegyzés**bejelölése lehetőséget választja, a felhasználók az ügyfél újraindítása után is bejelentkezhetnek, anélkül, hogy újra meg kellene adniuk a hitelesítő adataikat.

Habár a hitelesítő adatok megjegyzése kényelmes, az üzembe helyezést a vállalati forgatókönyvek vagy a személyes eszközök kevésbé biztonságosak is tehetik. A felhasználók biztosításához meg kell győződnie arról, hogy az ügyfél továbbra is kéri az Azure Multi-Factor Authentication (MFA) hitelesítő adatok megadását. Ez a cikk bemutatja, hogyan konfigurálhatja a Windows rendszerű virtuális asztal feltételes hozzáférési szabályzatát a beállítás engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A következő lépésekkel kell kezdenie:

- Rendelje hozzá az összes felhasználót a következő licencek közül:
  - E3 vagy E5 Microsoft 365
  - prémium szintű Azure Active Directory P1 vagy P2
  - E3 vagy E5 Enterprise Mobility + Security
- Egy Azure Active Directory csoport, amely a felhasználók csoport tagjaként van hozzárendelve.
- Engedélyezze az Azure MFA-t az összes felhasználó számára. Ennek módjáról további információt a [felhasználó kétlépéses ellenőrzésének megkövetelése](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)című témakörben talál.

>[!NOTE]
>A következő beállítás a [Windows rendszerű virtuális asztali webes ügyfélprogramra](https://rdweb.wvd.microsoft.com/webclient/index.html)is érvényes.

## <a name="opt-in-to-the-conditional-access-policy"></a>A feltételes hozzáférési szabályzat beléptetése

1. Nyissa meg **Azure Active Directory**.

2. Lépjen a **minden alkalmazás** lapra. Az "alkalmazás típusa" legördülő menüben válassza a **vállalati alkalmazások**, majd a **Windows rendszerű virtuális asztali ügyfél**keresése lehetőséget.

    ![A minden alkalmazás lap képernyőképe. A felhasználó a "Windows virtuális asztali ügyfél" kifejezést adta a keresési sávban, és az alkalmazás megjelenik a keresési eredmények között.](media/all-applications-search.png)

3. Válassza a **feltételes hozzáférés**lehetőséget.

    ![Egy képernyőkép, amely azt mutatja, hogy a felhasználó az egérmutatót a feltételes hozzáférés lap fölé viszi.](media/conditional-access-location.png)

4. Válassza az **+ új házirend**elemet.

   ![A feltételes hozzáférés oldalának képernyőképe. A felhasználó az egérmutatót az új házirend gomb fölé viszi.](media/new-policy-button.png)

5. Adja meg a **szabály** **nevét** , majd **válassza ki** az előfeltételekben létrehozott **csoport** nevét.

6. Válassza a **kiválasztás**, majd a **kész**lehetőséget.

7. Ezután nyissa meg a **felhőalapú alkalmazásokat vagy műveleteket**.

8. A **kiválasztás** panelen válassza ki a **Windows rendszerű virtuális asztali** nagyvállalati alkalmazást.

    ![Képernyőkép a Cloud apps vagy a Actions lapról. A felhasználó a Windows rendszerű virtuális asztali alkalmazást választotta ki a mellette lévő pipa bejelölésével. A kiválasztott alkalmazás piros színnel van kiemelve.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Az alábbi ábrán látható módon meg kell jelennie a képernyő bal oldalán kiválasztott Windows-alapú virtuális asztali ügyfélalkalmazásnak is. A szabályzat működéséhez a Windows rendszerű virtuális asztali és a Windows rendszerű virtuális asztali ügyfelek vállalati alkalmazásai is szükségesek.
    >
    > ![Képernyőkép a Cloud apps vagy a Actions lapról. A Windows rendszerű virtuális asztali és a Windows rendszerű virtuális asztali ügyfélprogramok piros színnel vannak kiemelve.](media/cloud-apps-enterprise-selected.png)

9. Válassza a **kiválasztás** lehetőséget.

10. Következő, nyitott **támogatás** 

11. Válassza a **többtényezős hitelesítés megkövetelése**lehetőséget, majd jelölje be **a kiválasztott vezérlők egyikének megkövetelése**jelölőnégyzetet.
   
    ![A Grant oldal képernyőképe. "A többtényezős hitelesítés megkövetelése" beállítás ki van választva.](media/grant-page.png)

    >[!NOTE]
    >Ha a szervezetében MDM regisztrált eszközök találhatók, és nem szeretné, hogy megjelenjenek az MFA-kérések, jelölje be az **eszköz megfelelőként való megjelölésének megkövetelése**lehetőséget is.

12. Válassza a **munkamenet**lehetőséget.

13. Állítsa a **bejelentkezési gyakoriságot** **aktív**értékre, majd módosítsa az értékét **1 órára**.

    ![A munkamenet oldalának képernyőképe. A munkamenet menü a bejelentkezési gyakoriság legördülő menüit jeleníti meg az "1" és az "Hours" értékre.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >A Windows rendszerű virtuális asztali környezet aktív munkamenetei továbbra is működni fognak a szabályzat módosításakor. Ha azonban leválasztja vagy kijelentkezik, a hitelesítő adatokat 60 perc elteltével újra meg kell adnia. A beállítások módosításakor az időtúllépési időszakot a kívánt mértékben kiterjesztheti (ha a szervezete biztonsági házirendjéhez igazodik).
    >
    >Az alapértelmezett beállítás a 90 napos gördülő ablak, ami azt jelenti, hogy az ügyfél újra bejelentkezik, amikor megpróbálnak hozzáférni egy erőforráshoz, miután 90 nap vagy annál több ideig inaktív a gépen.

14. Engedélyezze a szabályzatot.

15. Válassza a **Létrehozás** lehetőséget a szabályzat megerősítéséhez.

Ezzel készen is van! A szabályzat tesztelésével győződjön meg arról, hogy az engedélyezési lista a kívánt módon működik.
