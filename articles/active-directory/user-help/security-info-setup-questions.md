---
title: Biztonsági kérdések beállítása ellenőrzési módszerként – Azure AD
description: A Biztonsági adatok (előzetes verzió) lap beállítása a személyazonosság igazolására az előre meghatározott biztonsági kérdések használatával ellenőrzési módszerként.
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
ms.openlocfilehash: 236f1e4e41488b926d9fc2e5e717e68090a0ed7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063968"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Biztonsági kérdések beállítása ellenőrzési módszerként

Az alábbi lépésekkel hozzáadhatja a jelszó-visszaállítási módszert. Miután első alkalommal beállította ezt, visszatérhet a **Biztonsági adatok** lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Ha nem látja a biztonsági kérdések lehetőséget, lehetséges, hogy a szervezet nem engedélyezi ezt a beállítást a jelszó-visszaállítási módszerhez. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="security-verification-versus-password-reset-authentication"></a>Biztonság ellenőrzése kontra jelszó-visszaállítási hitelesítés

A biztonsági adatok módszerei a kétfaktoros biztonsági ellenőrzéshez és a jelszó alaphelyzetbe állításához is használatosak. Azonban nem minden módszer használható mindkettőre.

| Módszer | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonhívások | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>A biztonsági kérdések beállítása a Biztonsági adatok lapon

A szervezet beállításaitól függően előfordulhat, hogy a biztonsági adatok egyik módszereként ki választhat és megválaszolhat néhány biztonsági kérdést. A rendszergazda beállítja, hogy hány biztonsági kérdést kell kiválasztania és megválaszolnia.

Ha biztonsági kérdéseket használ, javasoljuk, hogy azokat más módszerrel együtt használja. A biztonsági kérdések kevésbé biztonságosak lehetnek, mint más módszerek, mivel néhány ember talán tudja a kérdésekre adott válaszokat.

> [!Note]
> A biztonsági kérdéseket a rendszer a címtárban lévő felhasználói objektumon tárolja, és csak a regisztráció során válaszolhatja meg. A rendszergazda nem olvashatja vagy módosíthatja a kérdéseit vagy válaszait.
>
> Ha nem látja a biztonsági kérdések lehetőséget, lehetséges, hogy a szervezet nem engedélyezi a biztonsági kérdések használatát ellenőrzésre. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a rendszergazdához.
>
> A rendszergazdai fiókok nem használhatják a biztonsági kérdéseket jelszó-visszaállítási módszerként. Ha rendszergazdai szintű fiókként van bejelentkezve, nem fogja látni ezeket a lehetőségeket.

### <a name="to-set-up-your-security-questions"></a>A biztonsági kérdések beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

    ![Saját profil lap, kiemelt biztonsági adatok hivatkozásai](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a **Biztonsági adatok lehetőséget** a bal oldali navigációs ablakban vagy a Biztonsági **adatok** blokkban található hivatkozáson, majd válassza a Metódus **hozzáadása** lehetőséget a **Biztonsági adatok** lapon.

    ![Biztonsági adatok lap kiemelt Hozzáadási módszer beállítással](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **Metódus hozzáadása** lapon válassza a **Legördülő** lista Biztonsági kérdések elemet, majd a **Hozzáadás gombot.**

    ![Módszer hozzáadása mező, kijelölt biztonsági kérdésekkel](media/security-info/securityinfo-myprofile-addquestions.png)

4. A **Biztonsági kérdések** lapon válassza ki és válaszoljon a biztonsági kérdésekre, majd kattintson a **Mentés gombra.**

    ![Telefonszám hozzáadása és telefonhívások kiválasztása](media/security-info/securityinfo-myprofile-securityquestions.png)

    A biztonsági adatok frissülnek, és a biztonsági kérdések segítségével ellenőrizheti személyazonosságát a jelszó-visszaállítás használatakor.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Biztonsági kérdések törlése a biztonsági adatok kalkapcsolatos módszereiből

Ha a továbbiakban nem szeretné biztonsági kérdéseket használni biztonsági információként, eltávolíthatja őket a **Biztonsági adatok** lapról.

>[!Important]
>Ha véletlenül törli a biztonsági kérdéseket, nem lehet visszavonni. A cikk [Biztonsági kérdések beállítása](#set-up-your-security-questions-from-the-security-info-page) szakaszában ismertetett lépéseket követve újra hozzá kell adnia a metódust.

### <a name="to-delete-your-security-questions"></a>A biztonsági kérdések törlése

1. A **Biztonsági adatok** lapon válassza a **Törlés** hivatkozást a **Biztonsági kérdések** beállítás mellett.

    ![Hivatkozás a telefon metódusának biztonsági adatokból való törléséhez](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. A biztonsági **kérdések**törléséhez válassza az **Igen** lehetőséget a megerősítést kérő mezőben. A biztonsági kérdések törlése után a metódus törlődik a biztonsági adatokközül, és eltűnik a **Biztonsági adatok** lapról.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További lehetőségek et is választhat arra vonatkozóan, hogy a szervezet hogyan veszi fel Önhez a személyazonosságának igazolását, attól függően, hogy mit próbál tenni. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használja a hitelesítő alkalmazást, hogy jóváhagyási értesítést vagy véletlenszerűen generált jóváhagyási kódot kapjon a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a hitelesítő alkalmazás használatához](security-info-setup-auth-app.md)című témakörben találja.

- **Mobileszköz szövege.** Adja meg mobileszköz-számát, és kapjon egy kódot, amelyet a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához használ. A személyazonosság szöveges üzenettel (SMS) történő ellenőrzéséről a [Biztonsági adatok beállítása szöveges üzenetküldés (SMS) használatához](security-info-setup-text-msg.md)című témakörben talál.

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg mobileszköz-számát, és kapjon telefonhívást a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság telefonszámmal történő igazolásával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja a Microsofttal kompatibilis biztonsági kulcsot, és használja azt pin-kóddal együtt a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság biztonsági kulccsal történő ellenőrzéséről a [Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)lépésenként.

- **E-mail cím.** Adja meg munkahelyi vagy iskolai e-mail címét, hogy e-mailt kapjon a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. A levelezés beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a levelezés használatához.](security-info-setup-email.md)

    >[!Note]
    >Ha ezek közül a lehetőségek közül néhány hiányzik, akkor valószínűleg azért, mert a szervezet nem engedélyezi ezeket a módszereket. Ebben az esetben ki kell választania egy elérhető módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
