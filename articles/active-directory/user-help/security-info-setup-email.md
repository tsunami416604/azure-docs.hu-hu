---
title: E-mail cím beállítása ellenőrzési módszerként – Azure AD
description: A Biztonsági adatok (előzetes verzió) lap beállítása személyazonosságának igazolására e-mail-cím használatával, mint ellenőrzési módszer.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064002"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>E-mail cím beállítása ellenőrzési módszerként

Az alábbi lépésekkel hozzáadhatja a jelszó-visszaállítási módszert. Miután első alkalommal beállította ezt, visszatérhet a **Biztonsági adatok** lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Ha nem lát e-mail lehetőséget, lehetséges, hogy a szervezet nem engedélyezi ezt a beállítást a jelszó-visszaállítási módszerhez. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="security-vs-password-reset-verification"></a>Biztonság vs jelszó-visszaállítás ellenőrzése

A biztonsági adatok módszerei a kétfaktoros biztonsági ellenőrzéshez és a jelszó alaphelyzetbe állításához is használatosak. Azonban nem minden módszer használható mindkettőre.

| Módszer | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonhívások | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Az e-mail cím beállítása a Biztonsági adatok lapon

A szervezet beállításaitól függően előfordulhat, hogy az e-mail címét használhatja a biztonsági adatok egyik módszereként.

>[!Note]
>Javasoljuk, hogy olyan e-mail címet használjon, amelyhez nincs szükség a hálózati jelszó elérésére. Ha nem látja az e-mail lehetőséget, lehetséges, hogy a szervezet nem engedélyezi az e-mail használatát ellenőrzésre. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a rendszergazdához.

### <a name="to-set-up-your-email-address"></a>Az e-mail cím beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

    ![Saját profil lap, kiemelt biztonsági adatok hivatkozásai](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a **Biztonsági adatok lehetőséget** a bal oldali navigációs ablakban vagy a Biztonsági **adatok** blokkban található hivatkozáson, majd válassza a Metódus **hozzáadása** lehetőséget a **Biztonsági adatok** lapon.

    ![Biztonsági adatok lap kiemelt Hozzáadási módszer beállítással](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **Metódus hozzáadása** lapon válassza az **E-mail** elemet a legördülő listából, majd kattintson a **Hozzáadás gombra.**

    ![Módszer hozzáadása mező, kijelölt e-maillel](media/security-info/securityinfo-myprofile-addemail.png)

4. Az **E-mail** lapon írja be az alain@gmail.come-mail címét (például ), majd válassza a **Tovább**gombot.

    ![Telefonszám hozzáadása és telefonhívások kiválasztása](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Ez az e-mail cím nem lehet munkahelyi vagy iskolai e-mail cím.

5. Írja be a megadott e-mail címre küldött kódot, majd válassza a **Tovább gombot.**

    ![Telefonszám hozzáadása és szöveges üzenetek kiválasztása](media/security-info/securityinfo-myprofile-emailcode.png)

    A biztonsági adatok frissülnek, és az e-mail-címével ellenőrizheti személyazonosságát a jelszó-visszaállítás használatakor.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Az e-mail cím törlése a biztonsági adatok kalkapcsolatos módszereiből

Ha a továbbiakban nem szeretné az e-mail címét biztonsági adatként használni, eltávolíthatja azt a **Biztonsági adatok** lapról.

>[!Important]
>Ha véletlenül törli az e-mail címét, nem lehet visszavonni. A cikk [e-mail címének beállítása](#set-up-your-email-address-from-the-security-info-page) című szakaszában leírt lépéseket követve újra hozzá kell adnia a metódust.

### <a name="to-delete-your-email-address"></a>Az e-mail cím törlése

1. A **Biztonsági adatok** lapon válassza az **E-mail** elem melletti **Törlés** hivatkozást.

    ![Hivatkozás a telefon metódusának biztonsági adatokból való törléséhez](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Válassza az **Igen** lehetőséget a megerősítő mezőben az **e-mail** fiók törléséhez. Az e-mail fiók törlése után törlődik a biztonsági adatokból, és eltűnik a **Biztonsági adatok** lapról.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További lehetőségek et is választhat arra vonatkozóan, hogy a szervezet hogyan veszi fel Önhez a személyazonosságának igazolását, attól függően, hogy mit próbál tenni. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használja a hitelesítő alkalmazást, hogy jóváhagyási értesítést vagy véletlenszerűen generált jóváhagyási kódot kapjon a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a hitelesítő alkalmazás használatához](security-info-setup-auth-app.md)című témakörben találja.

- **Mobileszköz szövege.** Adja meg mobileszköz-számát, és kapjon egy kódot, amelyet a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához használ. A személyazonosság szöveges üzenettel (SMS) történő ellenőrzéséről a [Biztonsági adatok beállítása szöveges üzenetküldés (SMS) használatához](security-info-setup-text-msg.md)című témakörben talál.

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg mobileszköz-számát, és kapjon telefonhívást a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság telefonszámmal történő igazolásával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja a Microsofttal kompatibilis biztonsági kulcsot, és használja azt pin-kóddal együtt a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság biztonsági kulccsal történő ellenőrzéséről a [Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)lépésenként.

- **Biztonsági kérdések.** Válaszoljon a rendszergazda által a szervezet számára létrehozott néhány biztonsági kérdésre. Ez a beállítás csak jelszó-visszaállításesetén érhető el, a kétlépéses ellenőrzésnél nem. A biztonsági kérdések beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a biztonsági kérdések használatához](security-info-setup-questions.md) című cikkben találja.

    >[!Note]
    >Ha ezek közül a lehetőségek közül néhány hiányzik, akkor valószínűleg azért, mert a szervezet nem engedélyezi ezeket a módszereket. Ebben az esetben ki kell választania egy elérhető módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
