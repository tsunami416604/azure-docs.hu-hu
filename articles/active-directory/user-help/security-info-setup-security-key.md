---
title: Biztonsági adatok beállítása biztonsági kulcs használatára (előzetes verzió) – Azure Active Directory | Microsoft Docs
description: A biztonsági adatok beállítása, hogy az identitást a Fast Identity online (FIDO2) biztonsági kulcs használatával ellenőrizze.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386498"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>Biztonsági adatok beállítása biztonsági kulcs használatára (előzetes verzió)

A biztonsági kulcsokat jelszó nélküli bejelentkezési módszerként is használhatja a szervezeten belül. A biztonsági kulcs egy olyan fizikai eszköz, amelyet egyedi PIN-kóddal használ a munkahelyi vagy iskolai fiókjába való bejelentkezéshez. Mivel a biztonsági kulcsok megkövetelik, hogy a fizikai eszköz és a szükséges elemek közül csak egy erősebb hitelesítési módszer legyen, mint a Felhasználónév és a jelszó.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

## <a name="what-is-a-security-key"></a>Mi az a biztonsági kulcs?

Jelenleg a [Fast Identity online (](https://fidoalliance.org/fido2/) FIDO2) jelszóval nem rendelkező hitelesítési módszer használatával támogatjuk a biztonsági kulcsok több kialakítását és szolgáltatóját. Ez a módszer lehetővé teszi a munkahelyi vagy iskolai fiókba való bejelentkezést, hogy hozzáférjen a szervezet felhőalapú erőforrásaihoz és a támogatott böngészőkhöz.

A rendszergazda vagy a szervezete biztonsági kulcsot fog biztosítani, ha a munkahelyi vagy iskolai fiókjához szükséges. Különböző típusú biztonsági kulcsokat használhat, például egy USB-kulcsot, amelyet csatlakoztathat az eszközhöz vagy egy NFC-kulcshoz, amely egy NFC-olvasóra koppint. A gyári dokumentációban talál további információt a biztonsági kulcsról, beleértve a típus típusát is.

> [!Note]
> Ha nem tud FIDO2 biztonsági kulcsot használni, más jelszó nélküli hitelesítési módszereket is használhat, például a Microsoft Authenticator alkalmazást vagy a Windows Hello-t. További információ a Microsoft Authenticator alkalmazásról: [Mi a Microsoft Authenticator alkalmazás?](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). A Windows Hello szolgáltatással kapcsolatos további információkért lásd: a [Windows Hello áttekintése](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Előkészületek

A biztonsági kulcs regisztrálásának megkezdése előtt ellenőrizze az alábbiakat:

- A rendszergazda bekapcsolta ezt a funkciót a szervezeten belüli használatra.

- Olyan eszközt használ, amely legalább Windows 10, 1903-es és a Microsoft Edge böngésző használatával fut.

- A rendszergazda vagy a szervezete fizikai biztonsági kulcsot kapott. A biztonsági kulcsnak FIDO2 és Microsoft-kompatibilisnek kell lennie. Ha bármilyen kérdése van a biztonsági kulccsal kapcsolatban, és hogy kompatibilis-e, lépjen kapcsolatba a szervezet ügyfélszolgálatával.

## <a name="register-your-security-key"></a>A biztonsági kulcs regisztrálása

Elő kell készítenie a biztonsági kulcsot a Windows és egy egyedi PIN-kód használatához, mielőtt a kulcs használatával bejelentkezne a munkahelyi vagy iskolai fiókjába.

1. Lépjen a **saját profil** lapra https://myprofile.microsoft.com , és jelentkezzen be, ha még nem tette meg.

2. Válassza a **biztonsági adatok**, majd a **metódus hozzáadása**lehetőséget, majd válassza a **biztonsági kulcs** lehetőséget a **metódus hozzáadása** legördülő listából.

    ![Hozzáadási mód mező, a biztonsági kulccsal kiválasztva](media/security-info/security-info-security-key-add-method.png)

3. Válassza a **Hozzáadás**lehetőséget, majd válassza ki, hogy milyen típusú biztonsági kulcsot használ, vagy **USB-eszközt** vagy NFC- **eszközt**.

    ![Válassza ki, hogy rendelkezik-e a biztonsági kulcs USB-vagy NFC-típusával](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Ha nem tudja biztosan, hogy milyen típusú biztonsági kulcsot tartalmaz, tekintse meg a gyártói dokumentációt. Ha nem biztos a gyártónál, kérjen segítséget a szervezet ügyfélszolgálatának.

4. A biztonsági kulcsot fizikailag elérhetővé kell tennie, majd a **biztonsági kulcs** mezőben válassza a **tovább**lehetőséget.

    ![Biztonsági kulcs indítási regisztrációs mezője](media/security-info/security-info-security-key-start-setup.png)

    Megjelenik egy új mező, amely segít az új bejelentkezési módszer beállításában.

5. Az **új bejelentkezési módszer beállítása** mezőben válassza a **tovább**, majd a következőket:

    - Ha a biztonsági kulcs egy USB-eszköz, szúrja be a biztonsági kulcsot az eszköz USB-portjába.

    - Ha a biztonsági kulcs egy NFC-eszköz, koppintson a biztonsági kulcsra az olvasója számára.

6. Írja be az egyedi biztonsági kulcs PIN-kódját a **Windows biztonsági** mezőbe, majd kattintson **az OK gombra**.

    Visszatér az **új bejelentkezési módszer beállítása** mezőbe.

7. Kattintson a **Tovább** gombra.

8. Térjen vissza a **biztonsági adatok** lapra, adjon meg egy nevet, amelyet később felismer az új biztonsági kulcshoz, majd kattintson a **tovább**gombra.

    ![Biztonsági adatok lap, a biztonsági kulcs elnevezése](media/security-info/security-info-security-key-name.png)

    A biztonsági kulcs regisztrálva van, és készen áll arra, hogy bejelentkezzen a munkahelyi vagy iskolai fiókjába.

9. A **biztonsági kulcs** mező bezárásához kattintson a **kész** gombra.

    A biztonsági **adatok lap a** biztonsági kulcs adataival frissül.

    ![Biztonsági adatok lap, az összes regisztrált metódussal](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Biztonsági kulcs törlése a biztonsági adatokból

Ha már nem szeretné használni a biztonsági kulcsot, törölheti a kulcsot a biztonsági adataiból. Habár ez leállítja a biztonsági kulcsot a munkahelyi vagy iskolai fiókjával, a biztonsági kulcs továbbra is tárolja az adatokat és a hitelesítő adatokat. Az adatok és a hitelesítő adatok biztonsági kulcsból való törléséhez kövesse a jelen cikk [Microsoft-kompatibilis biztonsági kulcs](#reset-your-security-key) alaphelyzetbe állítása című szakaszának utasításait.

1. A **Törlés** hivatkozásra kattintva távolítsa el a biztonsági kulcsot.

2. A **biztonsági kulcs törlése** mezőben kattintson az **OK gombra** .

    A biztonsági kulcsot törli a rendszer, és a továbbiakban nem fogja tudni használni a munkahelyi vagy iskolai fiókjába való bejelentkezéshez.

>[!Important]
>Ha tévedésből törölte a biztonsági kulcsot, újra kell regisztrálnia a jelen cikk a [biztonsági kulcs regisztrálása](#register-your-security-key) című szakaszának útmutatásai alapján.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>A biztonsági kulcs beállításainak kezelése a Windows beállításaiból

A biztonsági kulcs beállításait a **Windows beállításai** alkalmazásból kezelheti, beleértve a biztonsági kulcs alaphelyzetbe állítását és az új biztonsági kulcs PIN-kódjának létrehozását is.

### <a name="reset-your-security-key"></a>Biztonsági kulcs alaphelyzetbe állítása

Ha törölni kívánja a fizikai biztonsági kulcsban tárolt összes fiókadatokat, vissza kell adnia a kulcsot a gyári alapértékekre. A biztonsági kulcs alaphelyzetbe állítása törli a kulcs összes adatát, így elkezdheti a használatot.

>[!IMPORTANT]
>A biztonsági kulcs alaphelyzetbe állítása törli a kulcs összes elemét, alaphelyzetbe állítja a gyári beállításokat.
>
> **Minden adat és hitelesítő adat törölve lesz.**

#### <a name="to-reset-your-security-key"></a>A biztonsági kulcs alaphelyzetbe állítása

1. Nyissa meg a Windows beállításai alkalmazást, válassza a **fiókok**, majd a **bejelentkezési beállítások**, majd a **biztonsági kulcs**elemet, és válassza a **kezelés**lehetőséget.

2. Helyezze be a biztonsági kulcsot az USB-portba, vagy koppintson az NFC-olvasóra a személyazonosságának ellenőrzéséhez.

3. Kövesse a képernyőn megjelenő utasításokat az adott biztonsági kulcs gyártója alapján. Ha a kulcs gyártója nem szerepel a képernyőn megjelenő utasításokban, további információért tekintse meg a gyártó webhelyén.

4. A **kezelés** képernyő bezárásához kattintson a **Bezárás** gombra.

### <a name="create-a-new-security-key-pin"></a>Új biztonsági kulcs PIN-kódjának létrehozása

Létrehozhat egy új biztonsági kulcsot tartalmazó PIN-kódot a biztonsági kulcshoz.

#### <a name="to-create-a-new-security-key-pin"></a>Új biztonsági kulcs PIN-kódjának létrehozása

1. Nyissa meg a Windows beállításai alkalmazást, válassza a **fiókok**, majd a **bejelentkezési beállítások**, majd a **biztonsági kulcs**elemet, és válassza a **kezelés**lehetőséget.

2. Helyezze be a biztonsági kulcsot az USB-portba, vagy koppintson az NFC-olvasóra a személyazonosságának ellenőrzéséhez. 5
3. Válassza a **Hozzáadás** lehetőséget a **biztonsági kulcs PIN** -kódjának területén, írja be és erősítse meg az új biztonsági kulcs PIN-kódját, majd kattintson **az OK gombra**.

    A biztonsági kulcsot a rendszer az új biztonsági kulcs PIN-kódjával frissíti a munkahelyi vagy iskolai fiókjával való használathoz. Ha úgy dönt, hogy ismét módosítja a PIN-kódot, akkor a **módosítás** gombra kattinthat. 6
4. A **kezelés** képernyő bezárásához kattintson a **Bezárás** gombra.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

További beállításokkal is megtudhatja, hogy a szervezet hogyan forduljon a személyazonosságának ellenőrzéséhez a you're alapján. A lehetőségek a következők:

- **Hitelesítő alkalmazás.** Töltse le és használjon egy hitelesítő alkalmazást a jóváhagyási értesítés vagy a véletlenszerűen generált jóváhagyási kód beszerzéséhez a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz. A Microsoft Authenticator alkalmazás beállításával és használatával kapcsolatos részletes utasításokért lásd: [biztonsági adatok beállítása hitelesítő alkalmazás használatára](security-info-setup-auth-app.md).

- **Mobileszköz szövege.** Adja meg a mobileszköz számát, és szerezzen be egy, a kétlépéses ellenőrzéshez vagy a jelszó-visszaállításhoz használni kívánt kódot. Az identitás szöveges üzenettel (SMS) való ellenőrzésével kapcsolatos részletes utasításokért lásd: [biztonsági információk beállítása szöveges üzenetküldés (SMS) használatára](security-info-setup-text-msg.md).

- **Mobileszköz vagy munkahelyi telefonhívás.** Adja meg a mobileszköz számát, és telefonhívást kap a kétlépéses ellenőrzéshez vagy a jelszó alaphelyzetbe állításához. Az identitás telefonszámmal történő ellenőrzésével kapcsolatos részletes útmutatásért lásd: [biztonsági információk beállítása telefonhívások használatához](security-info-setup-phone-number.md).

- **E-mail-cím.** Adja meg a munkahelyi vagy iskolai e-mail-címét, hogy e-mailt kapjon a jelszó alaphelyzetbe állításához. Ez a beállítás kétlépéses ellenőrzés esetén nem érhető el. Az e-mailek beállításával kapcsolatos részletes útmutatásért lásd: [biztonsági adatok beállítása e-mailek használatára](security-info-setup-email.md).

- **Biztonsági kérdések.** Válaszoljon a szervezete rendszergazdája által létrehozott biztonsági kérdésekre. Ez a beállítás csak a jelszó-visszaállításhoz érhető el, és nem a kétlépéses ellenőrzéshez. A biztonsági kérdések beállításával kapcsolatos részletes utasításokért tekintse meg a biztonsági [kérdések beállítása a biztonsági kérdésekre](security-info-setup-questions.md) című cikket.

## <a name="next-steps"></a>További lépések

- További információ a jelszóval nem rendelkező hitelesítési módszerekről: a [Microsoft Azure ad megkezdi a FIDO2 biztonsági kulcsai nyilvános előzetes verziójának használatát,](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) a jelszavakat engedélyező bejelentkezések blogját, vagy a mi az [Microsoft Authenticator alkalmazás](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview) beolvasását? [ Windows Hello –](https://www.microsoft.com/windows/windows-hello) áttekintő cikkek.

- Részletesebb információ a [Microsoft-kompatibilis biztonsági kulcsokról](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó](https://passwordreset.microsoftonline.com/) -visszaállítási portálról, vagy kövesse a [munkahelyi vagy iskolai jelszó](user-help-reset-password.md) alaphelyzetbe állítása című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
