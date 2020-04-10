---
title: Az Azure többtényezős hitelesítésének beállítása a Windows virtuális asztalhoz – Azure
description: Az Azure többtényezős hitelesítésének beállítása a Windows virtuális asztal fokozott biztonsága érdekében.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998471"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication beállítása

A Windows-ügyfél windowsos virtuális asztal kiváló lehetőség a Windows virtuális asztal integrálására a helyi számítógéppel. Ha azonban a Windows virtuális asztal fiókját a Windows-ügyfélprogramba állítja, bizonyos intézkedéseket kell tennie saját maga és a felhasználók biztonságának megőrzése érdekében.

Amikor először jelentkezik be, az ügyfél kéri a felhasználónevét, jelszavát és az Azure MFA. Ezt követően a következő bejelentkezéskor az ügyfél emlékezni fog a jogkivonatot az Azure Active Directory (AD) vállalati alkalmazásból. Ha az **Emlékezzen rám**lehetőséget választja, a felhasználók az ügyfél újraindítása után bejelentkezhetnek anélkül, hogy újra meg kellene adniuk a hitelesítő adataikat.

A hitelesítő adatok megjegyzése azonban kényelmes, de a vállalati forgatókönyvekben vagy személyes eszközökön történő központi telepítéseket is csökkentheti a biztonság. A felhasználók védelme érdekében győződjön meg arról, hogy az ügyfél továbbra is kéri az Azure többtényezős hitelesítés (MFA) hitelesítő adatait. Ez a cikk bemutatja, hogyan konfigurálható a feltételes hozzáférés házirend a Windows virtuális asztalhoz a beállítás engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Rendelje hozzá az összes felhasználóhoz az alábbi licencek egyikét:
  - Microsoft 365 E3 vagy E5
  - Azure Active Directory Prémium P1 vagy P2
  - Vállalati mobilitás + E3 vagy E5 biztonság
- Egy Azure Active Directory-csoport a felhasználók rendelt csoporttagjai.
- Engedélyezze az Azure MFA-t az összes felhasználó számára. Ennek módjáról a [Felhasználó kétlépéses ellenőrzésének megkövetelése](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)című témakörben talál további információt.

>[!NOTE]
>A következő beállítás a [Windows Virtual Desktop webes ügyfélprogramra](https://rdweb.wvd.microsoft.com/webclient/index.html)is vonatkozik.

## <a name="opt-in-to-the-conditional-access-policy"></a>A feltételes hozzáférésre vonatkozó házirend betiltása

1. Nyissa meg **az Azure Active Directoryt**.

2. Nyissa meg a **Minden alkalmazás** lapot. Az "Alkalmazás típusa" legördülő menüben válassza a **Vállalati alkalmazások**lehetőséget, majd keresse meg a **Windows virtuális asztali ügyfele t.**

    ![Képernyőkép a Minden alkalmazás lapról. A felhasználó beírta a "Windows virtuális asztali ügyfélprogramot" a keresősávba, és az alkalmazás megjelent a keresési eredmények között.](media/all-applications-search.png)

3. Válassza a **Feltételes hozzáférés**lehetőséget.

    ![Képernyőkép, amelyen a felhasználó az egérmutatót a Feltételes hozzáférés lap fölé viszi.](media/conditional-access-location.png)

4. Válassza a **+ Új házirend lehetőséget.**

   ![Képernyőkép a Feltételes hozzáférés lapról. A felhasználó az egérmutatót az új házirendgomb fölé viszi.](media/new-policy-button.png)

5. Adja meg a **szabály** **nevét,** majd **válassza ki** az előfeltételekben létrehozott **csoport** *nevét.

6. Válassza **a Kijelölés**lehetőséget, majd a **Kész**lehetőséget.

7. Ezután nyissa meg a **Cloud Apps-alkalmazásokat vagy -műveleteket.**

8. A **Kijelölés** panelen válassza a **Windows Virtual Desktop** Enterprise alkalmazást.

    ![Képernyőkép a Felhőalkalmazások vagy műveletek lapról. A felhasználó a mellette lévő pipa bejelölésével választotta ki a Windows virtuális asztal alkalmazást. A kijelölt alkalmazás piros színnel van kiemelve.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >A képernyő bal oldalán a Windows virtuális asztali ügyfél alkalmazás is látható, ahogy az az alábbi képen látható. A házirend működéséhez a Windows virtuális asztal és a Windows virtuális asztali ügyfélvállalati alkalmazásra is szükség van.
    >
    > ![Képernyőkép a Felhőalkalmazások vagy műveletek lapról. A Windows virtuális asztal és a Windows virtuális asztali ügyfél alkalmazások piros színnel vannak kiemelve.](media/cloud-apps-enterprise-selected.png)

9. **Kijelölés kijelölése**

10. Ezután nyissa meg **a Grant** 

11. Jelölje be **a Többtényezős hitelesítés megkövetelése**jelölőnégyzetet, majd a **Kijelölt vezérlők egyikének megkövetelése**lehetőséget.
   
    ![Képernyőkép a Grant lapról. A "Többtényezős hitelesítés megkövetelése" lehetőség van kiválasztva.](media/grant-page.png)

    >[!NOTE]
    >Ha a szervezetben rendelkezik MDM-en regisztrált eszközökkel, és nem szeretné, hogy megjelenjenek az MFA-kérdés, akkor az **Eszköz megkövetelése megfelelőként való megjelölése**lehetőséget is választhatja.

12. Válassza a **Munkamenet**lehetőséget.

13. Állítsa a **bejelentkezési gyakoriságot** **Aktív**értékre, majd módosítsa az értékét **1 órára.**

    ![Képernyőkép a Munkamenet lapról. A munkamenet menüben a bejelentkezési gyakoriság legördülő menüje "1" és "Órák" lesz.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >A Windows virtuális asztal környezetben az aktív munkamenetek a házirend módosításakor is működni fognak. Ha azonban bontja a kapcsolatot vagy kijelentkezik, 60 perc elteltével újra meg kell adnia a hitelesítő adatait. A beállítások módosításakor a kívánt időtúltöltést meghosszabbíthatja (feltéve, hogy az igazodik a szervezet biztonsági házirendjéhez).
    >
    >Az alapértelmezett beállítás egy 90 napos gördülő ablak, ami azt jelenti, hogy az ügyfél megkéri a felhasználókat, hogy jelentkezzenek be újra, amikor megpróbálnak hozzáférni egy erőforráshoz, miután 90 napig vagy hosszabb ideig inaktívak.

14. Engedélyezze a házirendet.

15. A házirend megerősítéséhez válassza a **Létrehozás** gombot.

Ezzel készen is van! Nyugodtan tesztelje a szabályzatot, hogy megbizonyosodjon arról, hogy az engedélyezési lista a kívánt nak megfelel.
