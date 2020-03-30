---
title: A szöveges üzenetküldés beállítása ellenőrzési módszerként – Azure AD
description: A Biztonsági adatok (előnézeti) lap beállítása a személyazonosság ellenőrzéséhez szöveges üzenetek használatával, mint ellenőrzési módszer.
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
ms.openlocfilehash: 0525ee6d0ac5df61113e5a97c9812394323ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062353"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>A szöveges üzenetek beállítása ellenőrzési módszerként

Az alábbi lépésekkel hozzáadhatja a kétfaktoros ellenőrzési és jelszó-visszaállítási módszereket. Miután első alkalommal beállította ezt, visszatérhet a **Biztonsági adatok** lapra a biztonsági adatok hozzáadásához, frissítéséhez vagy törléséhez.

Ha a rendszer a munkahelyi vagy iskolai fiókba való bejelentkezés után azonnal kéri, hogy állítsa be ezt, tekintse meg a [biztonsági adatok beállítása a bejelentkezési lap kérdésében](security-info-setup-signin.md) című témakör részletes lépéseit.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Ha nem látja a telefon lehetőséget, lehetséges, hogy a szervezet nem engedélyezi, hogy ezt a beállítást ellenőrzésre használja. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Szöveges üzenetek beállítása a Biztonsági adatok lapon

A szervezet beállításaitól függően előfordulhat, hogy a szöveges üzeneteket is használhatja a biztonsági adatok egyik módjaként. A szöveges üzenet beállítás a telefon beállításának része, így mindent ugyanúgy állíthat be, mint a telefonszámához, de ahelyett, hogy a Microsoft felhívná, akkor szöveges üzenetet fog használni.

>[!Note]
>Ha szöveges üzenet helyett telefonhívást szeretne fogadni, kövesse a [Biztonsági adatok beállítása](security-info-setup-phone-number.md) a telefonhívások használatához című cikk ben leírt lépéseket.

### <a name="to-set-up-text-messages"></a>Szöveges üzenetek beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába, https://myprofile.microsoft.com/ és lépjen az oldalra.

    ![Saját profil lap, kiemelt biztonsági adatok hivatkozásai](media/security-info/securityinfo-myprofile-page.png)

2. Válassza a **Biztonsági adatok lehetőséget** a bal oldali navigációs ablakban vagy a Biztonsági **adatok** blokkban található hivatkozáson, majd válassza a Metódus **hozzáadása** lehetőséget a **Biztonsági adatok** lapon.

    ![Biztonsági adatok lap kiemelt Hozzáadási módszer beállítással](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. A **Metódus hozzáadása** lapon válassza a **Legördülő** lista Telefon elemet, majd a **Hozzáadás gombot.**

    ![Módszer hozzáadása mező, a Telefon beállítással](media/security-info/securityinfo-myprofile-addphonetext.png)

4. A **Telefon** lapon írja be a mobileszköz telefonszámát, válassza **a Kód szövege**lehetőséget, majd kattintson a **Tovább gombra.**

    ![Telefonszám hozzáadása és szöveges üzenetek kiválasztása](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Írja be a mobileszközére szöveges üzenetben küldött kódot, majd válassza a **Tovább**gombot.

    ![Telefonszám hozzáadása és szöveges üzenetek kiválasztása](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Az oldal a siker megjelenítéséhez változik.

    ![Sikeres értesítés, a telefonszám összekapcsolása, a szöveges üzenetek fogadásának lehetősége és a fiók](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A biztonsági adatok frissülnek, és a kétlépéses ellenőrzés vagy a jelszó-visszaállítás során szöveges üzenetküldéssel ellenőrizheti személyazonosságát. Ha a szöveges üzenetküldést szeretné alapértelmezettként ellátni, olvassa el a cikk [Alapértelmezett biztonsági információmódszerének módosítása](#change-your-default-security-info-method) című szakaszát.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Szöveges üzenetek törlése a biztonsági adatok módszereiből

Ha a továbbiakban nem szeretné a szöveges üzeneteket biztonsági adatként használni, eltávolíthatja azt a **Biztonsági adatok** lapról.

>[!Important]
>Ha véletlenül törli a szöveges üzeneteket, nem lehet visszavonni. A cikk [Szöveges üzenetek beállítása](#set-up-text-messages-from-the-security-info-page) szakaszában ismertetett lépéseket követve újra hozzá kell adnia a metódust.

### <a name="to-delete-text-messaging"></a>Szöveges üzenetek törlése

1. A **Biztonsági adatok** lapon válassza a **Telefon** beállítás melletti **Törlés** hivatkozást.

    ![Hivatkozás a telefon és a szöveges üzenetküldési mód biztonsági adatokból való törléséhez](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. A telefonszám törléséhez válassza **az** **Igen** lehetőséget a megerősítést kérő mezőben. A telefonszám törlése után törlődik a biztonsági adatokközül, és eltűnik a **Biztonsági adatok** lapról. Ha a **Telefon** az alapértelmezett módszer, az alapértelmezett módszer egy másik elérhető módszerre változik.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

Ha azt szeretné, hogy a szöveges üzenetek legyenek az alapértelmezett módszer, amelyet akkor használnak, amikor kétfaktoros ellenőrzéssel vagy jelszó-visszaállítási kérelmekkel jelentkezik be a munkahelyi vagy iskolai fiókjába, beállíthatja azt a **Biztonsági adatok** lapon.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatok módszerének módosítása

1. A **Biztonsági adatok** lapon válassza a **Módosítás** hivatkozást az **Alapértelmezett bejelentkezési módszer** adatai mellett.

    ![Az alapértelmezett bejelentkezési módszer hivatkozásának módosítása](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Válassza a **Telefon - szöveg (*_your_phone_number_*)** elemet az elérhető módszerek legördülő listájában, majd válassza a **Megerősítés gombot.**

    ![Az alapértelmezett bejelentkezés módjának kiválasztása](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    A Telefon - szöveg ( ***_your_phone_number_*)** bejelentkezési módosításaihoz használt alapértelmezett módszer.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További lehetőségek et is választhat arra vonatkozóan, hogy a szervezet hogyan veszi fel Önhez a személyazonosságának igazolását, attól függően, hogy mit próbál tenni. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használja a hitelesítő alkalmazást, hogy jóváhagyási értesítést vagy véletlenszerűen generált jóváhagyási kódot kapjon a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a hitelesítő alkalmazás használatához](security-info-setup-auth-app.md)című témakörben találja.

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg mobileszköz-számát, és kapjon telefonhívást a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság telefonszámmal történő igazolásával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **Biztonsági kulcs.** Regisztrálja a Microsofttal kompatibilis biztonsági kulcsot, és használja azt pin-kóddal együtt a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. A személyazonosság biztonsági kulccsal történő ellenőrzéséről a [Biztonsági adatok beállítása biztonsági kulcs használatához](security-info-setup-security-key.md)lépésenként.

- **E-mail cím.** Adja meg munkahelyi vagy iskolai e-mail címét, hogy e-mailt kapjon a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. A levelezés beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a levelezés használatához.](security-info-setup-email.md)

- **Biztonsági kérdések.** Válaszoljon a rendszergazda által a szervezet számára létrehozott néhány biztonsági kérdésre. Ez a beállítás csak jelszó-visszaállításesetén érhető el, a kétlépéses ellenőrzésnél nem. A biztonsági kérdések beállításával kapcsolatos részletes útmutatást a [Biztonsági adatok beállítása a biztonsági kérdések használatához](security-info-setup-questions.md) című cikkben találja.

    >[!Note]
    >Ha ezek közül a lehetőségek közül néhány hiányzik, akkor valószínűleg azért, mert a szervezet nem engedélyezi ezeket a módszereket. Ebben az esetben ki kell választania egy elérhető módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
