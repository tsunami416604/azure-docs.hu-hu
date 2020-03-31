---
title: Hitelesítési adatok regisztrálása saját jelszavának alaphelyzetbe állításához - Azure AD
description: Regisztrálja az ellenőrzési módszer adatait az Azure AD önkiszolgáló jelszó-visszaállításhoz, így rendszergazdai segítség nélkül is alaphelyzetbe állíthatja saját jelszavát.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062642"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Regisztrálja az ellenőrzési módszer adatait a saját jelszavának visszaállításához

Ha elfelejtette a munkahelyi vagy iskolai jelszavát, soha nem kapott jelszót a szervezettől, vagy ki van zárva a fiókjából, a biztonsági adatok és a mobileszköz segítségével alaphelyzetbe állíthatja munkahelyi vagy iskolai jelszavát.

Ahhoz, hogy regisztrálhassa adatait, és alaphelyzetbe állíthassa saját jelszavát, a rendszergazdának be kell kapcsolnia ezt a funkciót. Ha nem látja az **Elfelejtette a jelszó** beállítást, az azt jelenti, hogy a rendszergazda nem kapcsolta be a funkciót a szervezetnél. Ha úgy gondolja, hogy ez helytelen, kérjen segítséget az ügyfélszolgálattól.

>[!Important]
>Ez a cikk az önkiszolgáló jelszó-visszaállítási regisztrációt használni próbáló felhasználók számára készült. Ez azt jelenti, hogy a rendszergazda segítsége nélkül is alain@contoso.comvisszaállíthatja saját munkahelyi vagy iskolai jelszavát (például a rendszergazda segítségét. Ha Ön rendszergazda, és az önkiszolgáló jelszó-visszaállítás bekapcsolására vonatkozó információkat keresi az alkalmazottak vagy más felhasználók számára, olvassa el az [Azure AD önkiszolgáló jelszó-visszaállításának telepítése és egyéb cikkek című témakört.](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)

## <a name="set-up-your-password-reset-verification-method"></a>A jelszó-visszaállítás i.ellenőrzési módszerének beállítása

1. Nyissa meg a webböngészőt az eszközén, és lépjen a [biztonsági adatok lapra](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Attól függően, hogy a rendszergazda hogyan állította be a szervezetet, az alábbi lehetőségek közül egy vagy több áll majd rendelkezésre a biztonsági ellenőrzési módszerként való beállításhoz. Ha több lehetőség is rendelkezésre áll, javasoljuk, hogy egynél több biztonsági ellenőrzési módszert használjon, ha valamelyik módszer elérhetetlenné válik.

    - **Hitelesítési alkalmazás.** Válassza ki, hogy a Microsoft Authenticator alkalmazást vagy más hitelesítő alkalmazást használja biztonsági ellenőrzési módszerként. Az alkalmazás beállításáról a [Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként](security-info-setup-auth-app.md)című témakörben talál további információt.

    - **Sms-ezés.** Válassza ki, hogy szöveges üzeneteket küldjön magának a mobileszközére. A szöveges üzenetek beállításáról a [Szöveges üzenetek beállítása ellenőrzési módszerként](security-info-setup-text-msg.md)című témakörben talál további információt.

    - **Telefonhívások.** Válassza ki, hogy kap egy telefonhívást a regisztrált telefonszámát. A telefonhívások beállításáról a [Telefonszám beállítása ellenőrzési módszerként](security-info-setup-phone-number.md)című témakörben talál további információt.

    - **Biztonsági kulcs.** Microsoft-kompatibilis biztonsági kulcsot használ. További információt a [Biztonsági kulcs beállítása ellenőrzési módszerként](security-info-setup-security-key.md)című témakörben talál.

    - **E-mail cím.** Válasszon egy másodlagos e-mail címet, amely az elfelejtett vagy hiányzó jelszó megadása nélkül is használható. Ez csak a jelszó alaphelyzetbe állítása, nem pedig biztonsági ellenőrzési módszer. Az e-mail cím beállításáról az [E-mail cím beállítása ellenőrzési módszerként](security-info-setup-email.md)című témakörben talál további információt.

    - **Biztonsági kérdések.** Válassza ki, hogy a rendszergazda által beállított és előre meghatározott biztonsági kérdéseket állítja be és válaszoljon meg. Ez csak a jelszó alaphelyzetbe állítása, nem pedig biztonsági ellenőrzési módszer. A biztonsági kérdésekről a [Biztonsági kérdések beállítása ellenőrzési módszerként](security-info-setup-questions.md)című témakörben talál további információt.

3. Miután kiválasztotta és beállította a módszereket, válassza a **Befejezés** gombot a folyamat befejezéséhez.

    > [!Note]
    > A telefonszámához vagy e-mail címéhez megadott adatok nincsenek megosztva a szervezet globális címtárával. Csak ön és a rendszergazdája láthatja ezeket az adatokat. Csak ön láthatja a biztonsági kérdésekre adott válaszokat.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásaik

 Íme néhány gyakori hibaeset és azok megoldásai:

| Hibaüzenet |  Lehetséges megoldás |
| --- | --- | --- |
| Forduljon a rendszergazdához.<br>Azt észleltük, hogy a felhasználói fiók jelszavát nem a Microsoft kezeli. Ennek eredményeképpen nem tudjuk automatikusan visszaállítani a jelszavát.<br>További segítségért forduljon informatikai személyzetéhez.| Ha ez a hibaüzenet a felhasználói azonosító beírása után jelenik meg, az azt jelenti, hogy a szervezet belsőleg kezeli a jelszavát, és nem szeretné, hogy állítsa vissza a jelszavát **a Nem érhető el a fiók** hivatkozásához. Ebben a helyzetben a jelszó alaphelyzetbe állításához forduljon a szervezet ügyfélszolgálatához vagy a rendszergazdához segítségért. |
| A fiók nincs engedélyezve a jelszó-visszaállításhoz.<br>Sajnáljuk, de az informatikai személyzet nem állította be a fiókját a szolgáltatáshoz való használatra.<br>Ha szeretné, felvehetjük a kapcsolatot a szervezet egyik rendszergazdájával, hogy visszaállítsa a jelszavát. | Ha a felhasználói azonosító beírása után ez a hibaüzenet jelenik meg, az azt jelenti, hogy a szervezet nem kapcsolta be a jelszó-visszaállítási funkciót, vagy nem használhatja azt. Ebben a helyzetben a jelszó alaphelyzetbe állításához ki kell választania a **Rendszergazda kapcsolata** hivatkozást. Miután a hivatkozásra kattintott, a rendszer e-mailt küld a szervezet ügyfélszolgálatának vagy rendszergazdájának, amely tudatja velük, hogy új jelszót szeretne beállítani. |
| Nem tudtuk ellenőrizni a fiókját.<br>Ha szeretné, felvehetjük a kapcsolatot a szervezet egyik rendszergazdájával, hogy visszaállítsa a jelszavát. | Ha ez a hibaüzenet a felhasználói azonosító beírása után jelenik meg, az azt jelenti, hogy a szervezet bekapcsolta a jelszó-visszaállítást, és használhatja azt, de nem regisztrált a szolgáltatásra. Ebben az esetben a jelszó visszaállításához kapcsolatba kell lépnie a szervezet ügyfélszolgálatával vagy a rendszergazdával. A jelszó-visszaállításra való regisztrációról az eszközre való visszatekintés után a fenti témakörben olvashat. |

## <a name="next-steps"></a>További lépések

- [A jelszó módosítása önkiszolgáló jelszó-visszaállítással](active-directory-passwords-update-your-own-password.md)

- [Biztonsági adatok lap](https://mysignins.microsoft.com/security-info)

- [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)

- [Ha nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
