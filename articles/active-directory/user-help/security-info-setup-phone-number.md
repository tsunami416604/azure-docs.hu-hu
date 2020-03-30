---
title: A telefonszám beállítása ellenőrzési módszerként – Azure AD
description: A Biztonsági adatok (előzetes verzió) lap beállítása személyazonosságának igazolására a telefonszámával és a mobileszközével ellenőrzési módszerként.
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
ms.openlocfilehash: e85be9140aabe5b66e63d3ccdd4a3ea907a1d6fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062404"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Telefonszám beállítása ellenőrzési módszerként

Az alábbi lépésekkel hozzáadhatja a kétfaktoros ellenőrzési és jelszó-visszaállítási módszereket. Miután első alkalommal beállította ezt, visszatérhet a **Biztonsági adatok** lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

Ha a rendszer a munkahelyi vagy iskolai fiókba való bejelentkezés után azonnal kéri, hogy állítsa be ezt, tekintse meg a [biztonsági adatok beállítása a bejelentkezési lap kérdésében](security-info-setup-signin.md) című témakör részletes lépéseit.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> A biztonsági adatok nem támogatják a telefonbővítmények használatát. Még ha hozzá is adja a megfelelő formátumot, a +1 4255551234X12345 értéket, a program eltávolítja a bővítményeket a hívás megkezdése előtt.
>
> Ha nem látja a telefon lehetőséget, lehetséges, hogy a szervezet nem engedélyezi, hogy ezt a beállítást ellenőrzésre használja. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

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

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Telefonhívások beállítása a Biztonsági adatok lapon

A szervezet beállításaitól függően előfordulhat, hogy a telefonhívásokat használhatja a biztonsági adatok egyik módszereként.

>[!Note]
>Ha telefonhívás helyett szöveges üzenetet szeretne kapni, kövesse a [Biztonsági adatok beállítása a szöveges üzenetek](security-info-setup-text-msg.md) használata című cikkben leírt lépéseket.

### <a name="to-set-up-phone-calls"></a>Telefonhívások beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

    ![Saját profil lap, kiemelt biztonsági adatok hivatkozásai](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a **Biztonsági adatok lehetőséget** a bal oldali navigációs ablakban vagy a Biztonsági **adatok** blokkban található hivatkozáson, majd válassza a Metódus **hozzáadása** lehetőséget a **Biztonsági adatok** lapon.

    ![Biztonsági adatok lap kiemelt Hozzáadási módszer beállítással](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **Metódus hozzáadása** lapon válassza a **Legördülő** lista Telefon elemet, majd a **Hozzáadás gombot.**

    ![Módszer hozzáadása mező, a Telefon beállítással](media/security-info/securityinfo-myprofile-addphonetext.png)

4. A **Telefon** lapon írja be a mobileszköz telefonszámát, válassza **a Hívás**nekem lehetőséget, majd kattintson a **Tovább gombra.**

    ![Telefonszám hozzáadása és telefonhívások kiválasztása](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Fogadja az ellenőrző telefonhívást, küldje el a megadott telefonszámra, és kövesse az utasításokat.

    Az oldal a siker megjelenítéséhez változik.

    ![Sikeres értesítés, a telefonszám összekapcsolása, a telefonhívások fogadásának kiválasztása és a fiók](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A biztonsági adatok frissülnek, és a kétlépéses ellenőrzés vagy a jelszó-visszaállítás során a telefonhívások segítségével ellenőrizheti személyazonosságát. Ha a telefonhívásokat alapértelmezett módszerként szeretné ellátni, olvassa el a cikk [Alapértelmezett biztonsági információmódszerének módosítása](#change-your-default-security-info-method) című szakaszát.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Telefonhívások törlése a biztonsági adatok módszereiből

Ha a továbbiakban nem szeretné a telefonhívásokat biztonsági adatként használni, eltávolíthatja azt a **Biztonsági adatok** lapról.

>[!Important]
>Ha véletlenül törli a telefonhívásokat, nem lehet visszavonni. A metódust újra hozzá kell adnia a cikk [Telefonhívások beállítása](#set-up-phone-calls-from-the-security-info-page) szakaszában leírt lépéseket követve.

### <a name="to-delete-phone-calls"></a>Telefonhívások törlése

1. A **Biztonsági adatok** lapon válassza a **Telefon** beállítás melletti **Törlés** hivatkozást.

    ![Hivatkozás a telefon metódusának biztonsági adatokból való törléséhez](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. A telefonszám törléséhez válassza **az** **Igen** lehetőséget a megerősítést kérő mezőben. A telefonszám törlése után törlődik a biztonsági adatokközül, és eltűnik a **Biztonsági adatok** lapról. Ha a **Telefon** az alapértelmezett módszer, az alapértelmezett módszer egy másik elérhető módszerre változik.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

Ha azt szeretné, hogy a telefonhívások legyenek az alapértelmezett módszer, amelyet akkor használnak, amikor kétfaktoros ellenőrzéssel vagy jelszó-visszaállítási kérelmekkel jelentkezik be a munkahelyi vagy iskolai fiókjába, beállíthatja azt a **Biztonsági adatok** lapon.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

1. A **Biztonsági adatok** lapon válassza a **Módosítás** hivatkozást az **Alapértelmezett bejelentkezési módszer** adatai mellett.

    ![Az alapértelmezett bejelentkezési módszer hivatkozásának módosítása](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Válassza a **Telefon - hívás (*_your_phone_number_*)** elemet az elérhető módszerek legördülő listájában, majd válassza a **Megerősítés**lehetőséget.

    ![Az alapértelmezett bejelentkezés módjának kiválasztása](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    A Telefon - hívás ( ***_your_phone_number_*)** bejelentkezési módosításaihoz használt alapértelmezett módszer.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További lehetőségek et is választhat arra vonatkozóan, hogy a szervezet hogyan veszi fel Önhez a személyazonosságának igazolását, attól függően, hogy mit próbál tenni. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használja a hitelesítő alkalmazást, hogy jóváhagyási értesítést vagy véletlenszerűen generált jóváhagyási kódot kapjon a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a hitelesítő alkalmazás használatához](security-info-setup-auth-app.md)című témakörben találja.

- **Mobileszköz szövege.** Adja meg mobileszköz-számát, és kapjon egy kódot, amelyet a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához használ. A személyazonosság szöveges üzenettel (SMS) történő ellenőrzéséről a [Biztonsági adatok beállítása szöveges üzenetküldés (SMS) használatához](security-info-setup-text-msg.md)című témakörben talál.

- **Biztonsági kulcs.** Regisztrálja a Microsofttal kompatibilis biztonsági kulcsot, és használja azt pin-kóddal együtt a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság biztonsági kulccsal történő ellenőrzéséről a [Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)lépésenként.

- **E-mail cím.** Adja meg munkahelyi vagy iskolai e-mail címét, hogy e-mailt kapjon a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. A levelezés beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a levelezés használatához.](security-info-setup-email.md)

- **Biztonsági kérdések.** Válaszoljon a rendszergazda által a szervezet számára létrehozott néhány biztonsági kérdésre. Ez a beállítás csak jelszó-visszaállításesetén érhető el, a kétlépéses ellenőrzésnél nem. A biztonsági kérdések beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a biztonsági kérdések használatához](security-info-setup-questions.md) című cikkben találja.

    >[!Note]
    >Ha ezek közül a lehetőségek közül néhány hiányzik, akkor valószínűleg azért, mert a szervezet nem engedélyezi ezeket a módszereket. Ebben az esetben ki kell választania egy elérhető módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
