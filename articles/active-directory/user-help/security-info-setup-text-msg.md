---
title: Állítsa be a biztonsági adatok (előzetes verzió) használatához, szöveges üzenetküldés – Azure Active Directory |} A Microsoft Docs
description: Hogyan állítható be a biztonsági adatok a személyazonosságát, szöveges üzenetküldés és a mobil eszköz használatával.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9e4ae21ecc6538b33aed1566c10ddcd22b86c7
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341034"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Állítsa be a biztonsági adatok (előzetes verzió) használatához, szöveges üzenetküldés
Ezekkel a lépésekkel adja hozzá a kétfaktoros ellenőrzést, és a jelszó alaphelyzetbe állítási metódus. Miután beállította a először, visszatérhet a **biztonsági adatok** lap hozzáadása, frissítése vagy törlése a biztonsági információkat.

Ha kéri, jelentkezzen be munkahelyi vagy iskolai fiókjával után azonnal beállítaniuk ezt, tekintse meg a részletes lépéseit a [állítsa be a biztonsági adatok a bejelentkezési oldal használatával](security-info-setup-signin.md) cikk.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Ha nem látja a telefonos elrendezésre, lehetséges, hogy a szervezet nem engedélyezi, hogy az ellenőrzési telefonszám. Ebben az esetben kell válasszon egy másik módszert, vagy további segítségért forduljon a rendszergazdához.

## <a name="set-up-text-messages-from-the-security-info-page"></a>A biztonsági adatok weblapját a szöveges üzenetek beállítása
Szervezeti beállítások, attól függően fogja tudni használni a biztonsági adatok módszerekkel egyik üzenetkezelési. A szöveges üzenet lehetőséget a telefonos elrendezésre egy részét képezi, így a telefonszám mellékét értékre állítjuk mindent ugyanúgy azonos beállításokkal, de nem hívja meg a Microsoft, szöveges üzenetben használandó fogja választani.

>[!Note]
>Ha szeretne kapni a telefonhívás, szöveges üzenet helyett, kövesse a [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md) cikk.

### <a name="to-set-up-text-messages"></a>SMS-EK beállítása

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával, és keresse meg a https://myprofile.microsoft.com/ lapot.

    ![Saját profil lapot, amely a kijelölt biztonsági adatai hivatkozások](media/security-info/securityinfo-myprofile-page.png)

2. Válassza ki **biztonsági adatok** a bal oldali navigációs ablaktáblán vagy a hivatkozás a **biztonsági adatok** letiltása, és válassza ki **metódus hozzáadása** a a **biztonsági adatai**  lapot.

    ![Biztonsági adatok weblapját a kiemelt Hozzáadás metódus beállítás](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Az a **adjon meg egy metódust** lapon jelölje be **Phone** a legördülő listából válassza ki, és válassza ki a **Hozzáadás**.

    ![A kiválasztott Phone metódus mezőben hozzáadása](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Az a **Phone** lapon írja be a telefonszámot a mobil eszköz számára, válassza a **kódot kérek SMS**, majd válassza ki **tovább**.

    ![Adja hozzá a telefonszámot, és válassza ki a szöveges üzenetek](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Írja be a kódot a mobil eszközére szöveges üzeneten keresztül kap, és válassza **tovább**.

    ![Adja hozzá a telefonszámot, és válassza ki a szöveges üzenetek](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A lap vált a sikeres.

    ![Sikeres címtármódosítást jelző értesítés, a telefon csatlakozás szám, szöveg használt és az Ön fiókjának fogadhasson](media/security-info/securityinfo-myprofile-phonetext-success.png)

    A biztonsági adatok frissítése, és használhatja a szöveges üzenetben a személyazonosságát, ha a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása használatakor. Ha azt szeretné, győződjön meg arról, szöveges üzenetben az alapértelmezett módszer, olvassa el a [alapértelmezett biztonsági adatai módszerre állna](#change-your-default-security-info-method) című szakaszát.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>A biztonsági adatok módszerek az üzenetkezelési törlése
Ha már nem szeretne biztonsági adatai módszerként használhatják a szöveges üzenetek, eltávolíthatja azt a **biztonsági adatok** lapot.

>[!Important]
>Ha véletlenül üzenetkezelési törli, nincs semmilyen módon nem lehet visszavonni. Kell adja hozzá újra, a módszer lépései a [állítsa be a szöveges üzenetek](#set-up-text-messages-from-the-security-info-page) című szakaszát.

### <a name="to-delete-text-messaging"></a>Szöveges üzenetküldés törlése

1. Az a **biztonsági adatok** lapon válassza ki a **törlése** mellett kapcsolni a **Phone** lehetőséget.

    ![Törli a telefonról és az üzenetkezelési metódus a biztonsági adatok összekapcsolása](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Válassza ki **Igen** a megerősítés mezőben törlése a **Phone** számát. Miután törlődik a telefonszámát, a rendszer eltávolítja a biztonsági adatait és, eltűnik a **biztonsági adatok** lap. Ha **Phone** az alapértelmezett módszer az alapértelmezett értékre vált egy másik használható módszer.

## <a name="change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatai módszer megváltoztatása
Ha azt szeretné használni, amikor bejelentkezik a munkahelyi vagy iskolai fiókjába kétfaktoros ellenőrzési, vagy a jelszó-átállítási kérelmek alapértelmezett módszernek üzenetkezelési, beállíthatja azt a **biztonsági adatok** lapot.

### <a name="to-change-your-default-security-info-method"></a>Az alapértelmezett biztonsági adatai módszerének módosítása

1. Az a **biztonsági adatok** lapon válassza ki a **módosítása** mellett kapcsolni a **alapértelmezett bejelentkezési módszer** információkat.

    ![Módosítás hivatkozásra az alapértelmezett bejelentkezési módszer](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Válassza ki **Phone – szöveg (*_your_phone_number_*)** elérhető módszerek, és válassza ki a legördülő listából **megerősítése**.

    ![Az alapértelmezett bejelentkezési módszer kiválasztása](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Az alapértelmezett módszer használt bejelentkezési módosítások **Phone – szöveg (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>További biztonsági adatok módszerek
Hogyan a szervezet névjegyeket igazolhatja személyazonosságát, akkor mi alapján szeretne ehhez további lehetősége van. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le, és a egy hitelesítő alkalmazást használja, vagy egy jóváhagyási értesítést, vagy egy véletlenszerűen létrehozott jóváhagyási kód lekérése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Állítsa be és a Microsoft Authenticator alkalmazással kapcsolatos lépésenkénti útmutatót lásd: [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszközök számát, és telefonhívás beszerzése a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása. Igazolja személyazonosságát, egy telefonszámmal kapcsolatos lépésenkénti útmutatót lásd: [állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címet egy e-mailt beolvasni a jelszó-visszaállításhoz. Ez a beállítás nem érhető el a kétlépéses ellenőrzéshez. Állítsa be az e-mailben kapcsolatos lépésenkénti útmutatót lásd: [biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).

- **Biztonsági kérdések.** Néhány a szervezet számára a rendszergazda által létrehozott biztonsági kérdések megválaszolása. Ez a beállítás csak akkor használható, a jelszó-visszaállításhoz, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállítása kapcsolatos lépésenkénti útmutatót lásd: a [beállítása a biztonsági adatok a biztonsági kérdések használata](security-info-setup-questions.md) cikk.
    
    >[!Note]
    >Ha ezek a beállítások némelyike hiányzik, azt azért nagy valószínűséggel a szervezet nem engedélyezi azokat a módszereket. Ha ez a helyzet, szüksége lesz egy rendelkezésre álló módszer kiválasztásához, vagy további segítségért forduljon a rendszergazdához.

## <a name="next-steps"></a>További lépések

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.