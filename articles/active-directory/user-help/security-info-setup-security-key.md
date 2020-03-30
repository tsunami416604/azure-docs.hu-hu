---
title: Biztonsági kulcs beállítása ellenőrzési módszerként – Azure AD
description: A Biztonsági adatok (előzetes verzió) lap beállítása a gyors identitásonline (FIDO2) biztonsági kulcs ellenőrzési módszerként való használatához.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062336"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Biztonsági kulcs beállítása ellenőrzési módszerként

A biztonsági kulcsokat jelszó nélküli bejelentkezési módszerként használhatja a szervezeten belül. A biztonsági kulcs olyan fizikai eszköz, amelyet egyedi PIN-kóddal használnak a munkahelyi vagy iskolai fiókba való bejelentkezéshez. Mivel a biztonsági kulcsok megkövetelik, hogy a fizikai eszköz, és valami csak tudod, ez tekinthető erősebb ellenőrzési módszer, mint a felhasználónevet és a jelszót.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Ha nem látja a biztonsági kulcsot, lehetséges, hogy a szervezet nem engedélyezi ezt a beállítást az ellenőrzéshez. Ebben az esetben más módszert kell választania, vagy további segítségért forduljon a szervezet ügyfélszolgálatához.

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

## <a name="what-is-a-security-key"></a>Mi az a biztonsági kulcs?

Jelenleg számos biztonsági kulcstervezést és -szolgáltatót támogatunk a [Fast Identity Online (FIDO2)](https://fidoalliance.org/fido2/) jelszó nélküli hitelesítési protokollok használatával. Ezek a kulcsok lehetővé teszik, hogy jelentkezzen be a munkahelyi vagy iskolai fiókjába, hogy hozzáférjen a szervezet felhőalapú erőforrásaihoz, amikor egy támogatott eszközön és webböngészőben.

A rendszergazda vagy a szervezet biztonsági kulcsot biztosít, ha azt a munkahelyi vagy iskolai fiókjához igénylik. A biztonsági billentyűk különböző típusait használhatja, például az eszközhöz csatlakoztatott USB-kulcsot vagy egy NFC-kulcsot, amelyre egy NFC-olvasóra koppint. A biztonsági kulcsról további információt a gyártó dokumentációjában talál, beleértve azt is, hogy milyen típusú.

> [!Note]
> Ha nem tudja használni a FIDO2 biztonsági kulcsot, más jelszó nélküli ellenőrzési módszereket is használhat, például a Microsoft Authenticator alkalmazást vagy a Windows Hello alkalmazást. A Microsoft Authenticator alkalmazásról a [Mi a Microsoft Authenticator alkalmazás?](user-help-auth-app-overview.md) A Windows Hello szolgáltatásról a [Windows Hello – áttekintés című témakörben olvashat bővebben.](https://www.microsoft.com/windows/windows-hello)

## <a name="before-you-begin"></a>Előkészületek

A biztonsági kulcs regisztrálása előtt a következőknek kell lenniük:

- A rendszergazda bekapcsolta ezt a funkciót a szervezeten belüli használatra.

- Olyan eszközön van, amelyen a Windows 10 Május 2019 Frissítés fut, és egy támogatott böngészőt használ.

- A rendszergazda vagy a szervezet által jóváhagyott fizikai biztonsági kulccsal rendelkezik. A biztonsági kulcsnak FIDO2 és Microsoft-kompatibilisnek kell lennie. Ha bármilyen kérdése van a biztonsági kulccsal kapcsolatban, és hogy kompatibilis-e, forduljon a szervezet ügyfélszolgálatához.

## <a name="register-your-security-key"></a>A biztonsági kulcs regisztrálása

A biztonsági kulcsot létre kell hoznia, és egyedi PIN-kódot kell megadnia, mielőtt a kulccsal bejelentkezhetne munkahelyi vagy iskolai fiókjába. Lehet, hogy legfeljebb 10 kulcs van regisztrálva a fiókjában. 

1. Nyissa meg a Saját https://myprofile.microsoft.com **profil** lapot, és jelentkezzen be, ha még nem tette meg.

2. Válassza a **Biztonsági adatok**, majd a **Metódus hozzáadása**lehetőséget, majd a Metódus **hozzáadása** legördülő listában válassza a **Biztonsági kulcs** lehetőséget.

    ![Metódus hozzáadása mező, kijelölt Biztonsági kulccsal](media/security-info/security-info-security-key-add-method.png)

3. Válassza **a Hozzáadás**lehetőséget, majd válassza ki a biztonsági kulcs típusát, legyen az **USB-eszköz** vagy **NFC-eszköz.**

    ![Válassza ki, hogy USB- vagy NFC-típusú biztonsági kulccsal rendelkezik-e](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Ha nem biztos abban, hogy milyen típusú biztonsági kulccsal rendelkezik, olvassa el a gyártó dokumentációját. Ha nem biztos a gyártóban, kérjen segítséget a szervezet ügyfélszolgálatátél.

4. A biztonsági kulcs fizikailag elérhetővé, majd a **Biztonsági kulcs** mezőben válassza a **Tovább**gombot.

    ![Biztonsági kulcs indítási regisztrációs mezője](media/security-info/security-info-security-key-start-setup.png)

    Egy új doboz jelenik meg, amely segít végigjárni az új bejelentkezési módszer beállításán.

5. Az **Új bejelentkezési módszer beállítása** párbeszédpanelen válassza a **Tovább**lehetőséget, majd tegye a következőket:

    - Ha a biztonsági kulcs USB-eszköz, helyezze be a biztonsági kulcsot az eszköz USB-portjába.

    - Ha a biztonsági kulcs NFC-eszköz, koppintson a biztonsági kulcsra az olvasóhoz.

6. Írja be az egyedi biztonsági kulcs PIN-kódját a **Windows biztonsági** mezőjébe, majd kattintson az **OK gombra.**

    Vissza fog térni az **új bejelentkezési módszer beállítása** mezőhöz.

7. Válassza a **Tovább lehetőséget.**

8. Térjen vissza a **Biztonsági adatok** lapra, írjon be egy nevet, amelyet később fel fog ismerni az új biztonsági kulcshoz, majd válassza a **Tovább**gombot.

    ![Biztonsági adatok lap, a biztonsági kulcs elnevezése](media/security-info/security-info-security-key-name.png)

    A biztonsági kulcs regisztrálva van, és készen áll a munkahelyi vagy iskolai fiókba való bejelentkezésre.

9. A **Biztonsági kulcs** bezárásához válassza a **Kész** lehetőséget.

    A **Biztonsági adatok** lap frissül a biztonsági kulcs adataival.

    ![Biztonsági adatok lap, az összes regisztrált módszerrel együtt](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Biztonsági kulcs törlése a biztonsági adatokból

Ha a biztonsági kulcsot elszeretné helyezni, vagy már nem szeretné használni, törölheti a kulcsot a biztonsági adatokból. Ez megakadályozza, hogy a biztonsági kulcs a munkahelyi vagy iskolai fiókkal együtt legyen, a biztonsági kulcs továbbra is tárolja az adatokat és a hitelesítő adatokat. Ha magából a biztonsági kulcsból szeretné törölni az adatokat és a hitelesítő adatokat, kövesse a cikk [Microsoft-kompatibilis biztonsági kulcs ának alaphelyzetbe állítása](#reset-your-security-key) című szakaszának utasításait.

1. Válassza az eltávolítani kívánt biztonsági kulcs **törlési** hivatkozását.

2. A Biztonsági **kulcs törlése** párbeszédpanelen válassza az **Ok** lehetőséget.

    A biztonsági kulcs törlődik, és a továbbiakban nem használhatja a munkahelyi vagy iskolai fiókjába való bejelentkezéshez.

>[!Important]
>Ha véletlenül töröl egy biztonsági kulcsot, a cikk [Biztonsági kulcs regisztrálása](#register-your-security-key) című részében található utasítások segítségével újra regisztrálhatja azt.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>A biztonsági kulcs beállításainak kezelése a Windows beállításai ból

A biztonsági kulcs beállításait a **Windows Beállítások** alkalmazásból kezelheti, beleértve a biztonsági kulcs alaphelyzetbe állítását és egy új biztonsági kulcs PIN-kódjának létrehozását.

### <a name="reset-your-security-key"></a>A biztonsági kulcs alaphelyzetbe állítása

Ha törölni szeretné a fizikai biztonsági kulcson tárolt összes fiókadatot, vissza kell küldenie a kulcsot a gyári alapértékekre. A biztonsági kulcs alaphelyzetbe állítása mindent töröl a kulcsról, így újrakezdheti a számítógépet.

>[!IMPORTANT]
>A biztonsági kulcs alaphelyzetbe állítása mindent töröl a kulcsról, és visszaállítja a gyári alapértékeket.
>
> **Minden adat és hitelesítő adat törlődik.**

#### <a name="to-reset-your-security-key"></a>A biztonsági kulcs alaphelyzetbe állítása

1. Nyissa meg a Windows Beállítások alkalmazást, válassza a **Fiókok**lehetőséget, válassza **a Bejelentkezési beállítások lehetőséget,** válassza a **Biztonsági kulcs**lehetőséget, és válassza a **Kezelés lehetőséget.**

2. Helyezze be a biztonsági kulcsot az USB-portba, vagy koppintson az NFC-olvasóra a személyazonosság ának ellenőrzéséhez.

3. Kövesse a képernyőn megjelenő utasításokat az adott biztonsági kulcs gyártójától függően. Ha a kulcs gyártója nem szerepel a képernyőn megjelenő útmutatóban, további információt a gyártó webhelyén talál.

4. A **Kezelés** képernyő bezárásához válassza a **Bezárás** gombot.

### <a name="create-a-new-security-key-pin"></a>Új biztonsági kulcs PIN-kódjának létrehozása

Új biztonsági kulcs PIN-kódját hozhatja létre a biztonsági kulcshoz.

#### <a name="to-create-a-new-security-key-pin"></a>Új biztonsági kulcs PIN-kódjának létrehozása

1. Nyissa meg a Windows Beállítások alkalmazást, válassza a **Fiókok**lehetőséget, válassza **a Bejelentkezési beállítások lehetőséget,** válassza a **Biztonsági kulcs**lehetőséget, és válassza a **Kezelés lehetőséget.**

2. Helyezze be a biztonsági kulcsot az USB-portba, vagy koppintson az NFC-olvasóra a személyazonosság ának ellenőrzéséhez.
3. Válassza a **Hozzáadás** lehetőséget a **Biztonsági kulcs PIN-kódjának** területén, írja be és erősítse meg az új biztonságikulcs PIN-kódját, majd kattintson **az OK gombra.**

     A biztonsági kulcs az új biztonsági kulcs PIN-kódjával frissül, és a munkahelyi vagy iskolai fiókjához használható. Ha úgy dönt, hogy ismét módosítja a PIN-kódot, **kiválaszthatja** a Módosítás gombot.
4. A **Kezelés** képernyő bezárásához válassza a **Bezárás** gombot.

## <a name="additional-security-info-methods"></a>További biztonsági információs módszerek

A biztonsági kulcs regisztrálásához legalább egy további biztonsági ellenőrzési módszerrel kell rendelkeznie. További információt az [Áttekintés szakaszban](security-info-add-update-methods-overview.md) talál. 

## <a name="next-steps"></a>További lépések

- A jelszó nélküli ellenőrzési módszerekről további információt a Microsoft Azure AD megkezdi a [FIDO2 biztonsági kulcsok nyilvános előzetes verziójában, lehetővé téve a jelszó nélküli bejelentkezési blogot,](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) vagy olvassa el [a Mi a Microsoft Hitelesítő alkalmazás?](user-help-auth-app-overview.md) és a [Windows Hello áttekintő cikkei.](https://www.microsoft.com/windows/windows-hello)

- A [Microsoft-kompatibilis biztonsági kulcsokról](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)további információt talál.

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
