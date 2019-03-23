---
title: Az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása, Windows 7- és 8.1 - Azure Active Directory
description: Hogyan lehet engedélyezni az önkiszolgáló jelszó-visszaállítás használatával elfelejtette a jelszavát, a Windows 7 vagy 8.1 bejelentkezési képernyő
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d3e955059724756eb7102c1b9fbbf55ed203ab
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370448"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Útmutató: A Windows 7, 8 és 8.1-es a jelszóátállítás engedélyezése

Rendszergazdaként engedélyezte az önkiszolgáló jelszó-visszaállítás (SSPR), de a felhasználók visszaállíthatják a jelszavukat, mivel azok nem lehet lekérni egy böngészőablakban történő eléréséhez a segélyszolgálat tartsa hívása a [SSPR portál](https://aka.ms/sspr). A Windows 10 rendszerű gépek, engedélyezheti a bejelentkezési képernyő, az oktatóprogram a "Jelszó visszaállítása" hivatkozás [az Azure AD-jelszó visszaállítása a bejelentkezési képernyőről](tutorial-sspr-windows.md), az alábbi útmutató segítségével engedélyezheti a Windows 7, 8 és 8.1-felhasználók alaphelyzetbe állítása az SSPR használata a Windows bejelentkezési képernyő jelszó.

Ellentétben a Windows 10 rendszerű gépek, Windows 7, 8 és 8.1-es gépek nem rendelkeznek az Azure AD-tartományhoz, vagy az Active Directory-tartományhoz követelmény a jelszó alaphelyzetbe állítása.

![Például Windows 7 bejelentkezési képernyő, az "Elfelejtette jelszavát?" hivatkozás jelenik meg](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Előfeltételek

* Engedélyezni kell az Azure AD önkiszolgáló jelszó-visszaállítását.
* Javított Windows 7 vagy Windows 8.1 operációs rendszer.
* Az útmutató segítségével engedélyezve a TLS 1.2 található [Transport Layer Security (TLS) beállításjegyzék-beállítások](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> A TLS 1.2 engedélyezve kell lennie, nem csak set automatikus egyeztetni.

## <a name="install"></a>Telepítés

1. Töltse le a megfelelő telepítőt az engedélyezni kívánt Windows verziójához.

   1. Szoftverfrissítési érhető el a Microsoft letöltőközpontban: [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Jelentkezzen be a gép, ahol szeretné telepíteni, és futtassa a telepítőt.
1. A telepítés után újraindítás erősen ajánlott.
1. Az újraindítás után a bejelentkezési képernyőn válasszon egy felhasználót, kattintson az "Elfelejtette a jelszavát?" kezdeményezheti a jelszó alaphelyzetbe állítása a munkafolyamat.
1. Végezze el a munkafolyamatot a jelszó képernyőn lépések.

![Például Windows 7 kattint az "Elfelejtette a jelszavát?" SSPR folyamat](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Beavatkozás nélküli telepítés

* Beavatkozás nélküli telepítéssel használja a parancs "msiexec /i SsprWindowsLogon.PROD.msi /qn"
* Csendes eltávolításához használja a parancs "msiexec /x SsprWindowsLogon.PROD.msi /qn"

## <a name="caveats"></a>Figyelmeztetések

Regisztrálnia kell az SSPR előtt az "Elfelejtette jelszavát" hivatkozásra.

![További biztonsági információkat kelljen a jelszó szükséges](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

A jelszó alaphelyzetbe állításához az értesítések és a kódokat a Microsoft Authenticator alkalmazás használatával az eredeti kiadásban nem működik. Felhasználók regisztrált, a szabályzat követelményeknek megfelelő alternatív módszerekkel kell rendelkeznie.

## <a name="troubleshooting"></a>Hibaelhárítás

A gép és az Azure ad-ben események lesznek naplózva.

Az Azure AD-események az IP-cím és ügyféltípus, ahol a jelszó-visszaállítás történt vonatkozó információk is.

![Például Windows 7 a jelszóátállítás az Azure AD auditnaplója](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

További naplózás szükség, ha egy beállításkulcsot a gépen engedélyezi a részletes naplózást is módosítható. Engedélyezi a részletes naplózást hibaelhárítási céllal.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

* Engedélyezi a részletes naplózást, hozzon létre egy REG_DWORD: "EnableLogging", és állítsa be az 1.
* Letiltja a részletes naplózást, módosítsa a REG_DWORD "EnableLogging" 0-ra.

Ha a Windows 7, 8 és 8.1-es gépek proxykiszolgálót vagy tűzfal mögé, HTTPS-forgalmat (443) passwordreset.microsoftonline.com engedélyezni kell.

## <a name="next-steps"></a>További lépések

* [A bejelentkezési képernyő jelszó átállításának Windows 10-es felhasználók engedélyezése](tutorial-sspr-windows.md)
