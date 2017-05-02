---
title: "Azure AD: regisztráció önkiszolgáló jelszó-visszaállításhoz | Microsoft Docs"
description: "Hitelesítési adatok regisztrálása önkiszolgáló jelszó-visszaállításhoz"
services: active-directory
keywords: "Active Directory-jelszókezelés, jelszókezelés, Azure AD önkiszolgáló jelszó-visszaállítás, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c6d3d9d8f399816928e794e8956bc35825462fb9
ms.lasthandoff: 04/25/2017


---
# <a name="register-for-self-service-password-reset"></a>Regisztráció önkiszolgáló jelszó-visszaállításra

Ha a rendszergazda engedélyezte, az önkiszolgáló jelszó-visszaállítás (SSPR) segítségével végfelhasználóként anélkül állíthatja vissza a saját jelszavát és oldhatja fel fiókja zárolását, hogy ehhez bárkivel beszélnie kéne. Mielőtt használhatná ezt a funkciót, hitelesítési módszereket kell regisztrálnia, vagy jóvá kell hagynia a rendszergazda által betöltött előre definiált hitelesítési módszereket.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR hitelesítés adatok regisztrálása vagy megerősítése

1. Nyissa meg az eszközén a webböngészőt, és lépjen a [jelszó-visszaállítási regisztrációs oldalra](http://aka.ms/ssprsetup)
2. Adja meg a rendszergazdától kapott felhasználónevet és jelszót.
3. A rendszergazda által jóváhagyott beállítások függvényében megjelenik egy vagy több az alábbi lehetőségek közül, amelyeket konfigurálnia kell vagy jóvá kell hagynia a használatukhoz.
    * Irodai telefonszám – Ezt csak a rendszergazda állíthatja be
    * Hitelesítő telefonszám – Itt egy másik telefonszámot, például egy mobiltelefonszámot lehet megadni, amelyhez hozzáfér, és amelyen képes hívásokat és szöveges üzeneteket fogadni (a rendszergazda itt esetleg már megadhatta előre az Ön telefonszámát, ha ennek az adatnak a használatát Ön engedélyezte a számára)
    * Hitelesítő e-mail-cím – Itt egy másodlagos e-mail-cím adható meg, amelyhez a visszaállítandó jelszó nélkül is hozzá tud férni
    * Biztonsági kérdések – Itt kérdések egy sorát találja, amelyeket a rendszergazda jóváhagyott, és amelyekre Ön adja meg a választ. Mindegyik kérdéshez eltérő válaszokat kell megadnia.
4. Adja meg és hagyja jóvá a rendszergazda által előírt információkat. Ha egynél több lehetőség áll rendelkezésére, javasoljuk, hogy több módszert is regisztráljon, hogy több mozgástere maradjon, ha az egyik módszer esetleg nem lenne elérhető (például mert utazik, és az irodai telefonját nem éri el)

    ![Regisztrálja a hitelesítési módszereket, majd kattintson a Befejezés gombra][Register]

5. Miután végzett a 4. lépéssel, válassza a **Befejezés** lehetőséget, és innentől használhatja az önkiszolgáló jelszó-visszaállítás szolgáltatást, amennyiben ez bármikor szükségessé válna az elkövetkezőkben.

A Hitelesítő telefonszám és a Hitelesítő e-mail-cím mezőkben megadott adatok a globális címjegyzékben nem lesznek láthatóak. Ezeket az adatokat kizárólag Ön és a rendszergazdák láthatják. A Biztonsági kérdésekre adott válaszokat pedig kizárólag Ön láthatja.

A rendszergazda esetleg bizonyos idő elteltével kérheti, hogy erősítse meg a hitelesítési módszereit, hogy bizonyos legyen, hogy még mindig regisztrálva vannak a megfelelő módszerek.

## <a name="next-steps"></a>Következő lépések

* [Jelszó visszaállítása az önkiszolgáló jelszó-visszaállítással](active-directory-passwords-update-your-own-password.md)
* [Jelszó-visszaállítási regisztrációs oldal](http://aka.ms/ssprsetup)
* [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)
* [Nem tud bejelentkezni a Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Jelszó-visszaállítási regisztrációs oldal a regisztrált módszerekkel és a Befejezés gombbal"

