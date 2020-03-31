---
title: Az Azure MFA-kiszolgáló frissítése – Azure Active Directory
description: Az Azure többtényezős hitelesítési kiszolgáló újabb verzióra való frissítésének lépései és útmutatásai.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756c45541907c52448805376e1b054180c31fdf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848102"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>A legújabb Azure Multi-Factor Authentication-kiszolgáló frissítése

Ez a cikk bemutatja az Azure multi-factor authentication (MFA) Server v6.0 vagy újabb verzió frissítésének folyamatát. Ha frissítenie kell a PhoneFactor-ügynök egy régi verzióját, olvassa el [a PhoneFactor-ügynök frissítése Azure többtényezős hitelesítési kiszolgálóra](howto-mfaserver-deploy-upgrade-pf.md)című részt.

Ha v6.x vagy régebbi verzióról v7.x vagy újabb verzióra frissít, az összes összetevő .NET 2.0-ról .NET 4.5-re változik. Minden összetevőhöz szükség van a Microsoft Visual C++ 2015 újraterjeszthető 1. Az MFA-kiszolgáló telepítője telepíti az összetevők x86-os és x64-es verzióját is, ha még nincsenek telepítve. Ha a Felhasználói portál és a Mobilalkalmazás webszolgáltatás külön kiszolgálókon fut, az összetevők frissítése előtt telepítenie kell ezeket a csomagokat. A Microsoft Visual C++ 2015 legújabb újraterjeszthető frissítését a [Microsoft letöltőközpontban](https://www.microsoft.com/download/)keresheti meg. 

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem ajánlja fel az MFA Server alkalmazást az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták az MFA-kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.

Frissítési lépések egy pillantással:

* Az Azure MFA-kiszolgálók frissítése (beosztottak, majd főkiszolgáló)
* A felhasználói portál példányainak frissítése
* Az AD FS adapter példányainak frissítése

## <a name="upgrade-azure-mfa-server"></a>Az Azure MFA-kiszolgáló frissítése

1. Az Azure [többtényezős hitelesítési kiszolgáló letöltése](howto-mfaserver-deploy.md#download-the-mfa-server) című témakör utasításait az Azure MFA-kiszolgáló telepítőjének legújabb verziójának beszerzéséhez használja.
2. Készítsen biztonsági másolatot a fő MFA-kiszolgáló N.\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata mappájában található MFA-kiszolgáló adatfájljáról.
3. Ha több kiszolgálót futtat magas rendelkezésre állás érdekében, módosítsa az MFA-kiszolgálón hitelesített ügyfélrendszereket, hogy ne küldjenek forgalmat a frissítő kiszolgálóknak. Ha terheléselosztót használ, távolítsa el az alárendelt MFA-kiszolgálót a terheléselosztóból, végezze el a frissítést, majd adja hozzá a kiszolgálót a farmhoz.
4. Futtassa az új telepítőt minden MFA-kiszolgálón. Először frissítse az alárendelt kiszolgálókat, mert olvasni tudják a főkiszolgáló által replikált régi adatfájlt.

   > [!NOTE]
   > Kiszolgáló frissítésekor el kell távolítani a terheléselosztásból vagy a forgalom megosztásából más MFA-kiszolgálókkal.
   >
   > A telepítő futtatása előtt nem kell eltávolítania a jelenlegi MFA-kiszolgálót. A telepítő helyben frissíti a frissítést. A telepítési útvonalat a rendszer az előző telepítésből veszi fel a rendszerleíró adatbázisból, így ugyanazon a helyen (például C:\Program Files\Multi-Factor Authentication Server) települ.
  
5. Ha a rendszer a Microsoft Visual C++ 2015 újraterjeszthető frissítőcsomag telepítését kéri, fogadja el a kérdést. A csomag x86-os és x64-es verziója is telepítve van.
6. Ha a webszolgáltatás SDK-ját használja, a rendszer kéri az új webszolgáltatás SDK telepítését. Az új WebService SDK telepítésekor győződjön meg arról, hogy a virtuális könyvtár neve megegyezik a korábban telepített virtuális könyvtárral (például MultiFactorAuthWebServiceSdk).
7. Ismételje meg a lépéseket az összes alárendelt kiszolgálón. Előléptetje az egyik beosztottat új főkiszolgálóvá, majd frissítse a régi főkiszolgálót.

## <a name="upgrade-the-user-portal"></a>A felhasználói portál frissítése

Mielőtt erre a szakaszra lépne, fejezze be az MFA-kiszolgálók frissítését.

1. Készítsen biztonsági másolatot a user portal telepítési helyének virtuális könyvtárában található web.config fájlról (például C:\inetpub\wwwroot\MultiFactorAuth). Ha az alapértelmezett témát módosította, készítsen biztonsági másolatot a App_Themes\Alapértelmezett mappáról is. Jobb, ha másolatot készít az Alapértelmezett mappáról, és új témát hoz létre, mint az Alapértelmezett téma módosítása.
2. Ha a felhasználói portál ugyanazon a kiszolgálón fut, mint a többi MFA-kiszolgáló-összetevő, az MFA-kiszolgáló telepítése a felhasználói portál frissítését kéri. Fogadja el a kérdést, és telepítse a Felhasználói portál frissítését. Ellenőrizze, hogy a virtuális könyvtár neve megegyezik-e a korábban telepített virtuális könyvtárral (például MultiFactorAuth).
3. Ha a felhasználói portál a saját kiszolgálóján található, másolja a MultiFactorAuthenticationUserPortalSetup64.msi fájlt az egyik MFA-kiszolgáló telepítési helyéről, és helyezze át a felhasználói portál webkiszolgálójára. Indítsa el a telepítőt.

   Ha a "Microsoft Visual C++ 2015 újraterjeszthető 1. [Microsoft Download Center](https://www.microsoft.com/download/) Telepítse mind az x86-os, mind az x64-es verziókat.

4. A frissített User Portal szoftver telepítése után hasonlítsa össze az 1. Ha az új web.config-ban nincsenek új attribútumok, másolja a biztonsági másolat web.config-ját a virtuális könyvtárba az új felülírásához. Egy másik lehetőség az alkalmazásbeállítási értékek és a webszolgáltatás SDK-URL-címének másolása/beillesztése a biztonságimásolat-fájlból az új web.config fájlba.

Ha a felhasználói portál több kiszolgálón van, ismételje meg a telepítést mindegyiken.

## <a name="upgrade-the-mobile-app-web-service"></a>A mobilalkalmazás webszolgáltatásának frissítése

> [!NOTE]
> Ha az Azure MFA Server 8.0-nál régebbi és 8,0+ verziójú verziójáról frissít, a mobilalkalmazás webszolgáltatása a frissítés után eltávolítható

## <a name="upgrade-the-ad-fs-adapters"></a>Az AD FS-adapterek frissítése

Mielőtt erre a szakaszra lépne, fejezze be az MFA-kiszolgálók és a felhasználói portál frissítését.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Ha az MFA az AD FS-en kívül más kiszolgálókon fut

Ezek az utasítások csak akkor érvényesek, ha a többtényezős hitelesítési kiszolgálót az AD FS-kiszolgálóktól elkülönítve futtatja. Ha mindkét szolgáltatás ugyanazon a kiszolgálón fut, hagyja ki ezt a szakaszt, és lépjen a telepítési lépésekre. 

1. Mentse az AD FS fájlban regisztrált MultiFactorAuthenticationAdfsAdapter.config fájl egy példányát, vagy `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`exportálja a konfigurációt a következő PowerShell paranccsal: . Az adapter neve "WindowsAzureMultiFactorAuthentication" vagy "AzureMfaServerAuthentication" a korábban telepített verziótól függően.
2. Másolja a következő fájlokat az MFA-kiszolgáló telepítési helyéről az AD FS-kiszolgálókra:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. A Register-MultiFactorAuthenticationAdfsAdapter.ps1 parancsfájl szerkesztéséhez adja `-ConfigurationFilePath [path]` hozzá `Register-AdfsAuthenticationProvider` a parancs végét. Cserélje le az *[elérési utat]* a MultiFactorAuthenticationAdfsAdapter.config fájl vagy az előző lépésben exportált konfigurációs fájl teljes elérési útjára.

   Ellenőrizze az új MultiFactorAuthenticationAdfsAdapter.config attribútumait, hogy megegyeznek-e a régi konfigurációs fájllal. Ha az új verzióban hozzáadtak vagy eltávolítottak attribútumokat, másolja az attribútumértékeket a régi konfigurációs fájlból az újba, vagy módosítsa a régi konfigurációs fájlt.

### <a name="install-new-ad-fs-adapters"></a>Új AD FS-adapterek telepítése

> [!IMPORTANT]
> A felhasználóknak nem kell kétlépéses ellenőrzést végrehajtaniuk a szakasz 3-8. Ha az AD FS több fürtben van konfigurálva, eltávolíthatja, frissítheti és visszaállíthatja a farm egyes fürtjeit a többi fürttől függetlenül, hogy elkerülje az állásidőt.

1. Néhány AD FS-kiszolgáló eltávolítása a farmból. Frissítse ezeket a kiszolgálókat, amíg a többi még fut.
2. Telepítse az új AD FS-adaptert az AD FS-farmról eltávolított minden kiszolgálóra. Ha az MFA-kiszolgáló minden AD FS-kiszolgálón telepítve van, az MFA-kiszolgáló felügyeleti felhasználói felületén keresztül frissíthet. Ellenkező esetben frissítse a MultiFactorAuthenticationAdfsAdapterSetup64.msi fájl futtatásával.

   Ha a "Microsoft Visual C++ 2015 újraterjeszthető 1. [Microsoft Download Center](https://www.microsoft.com/download/) Telepítse mind az x86-os, mind az x64-es verziókat.

3. Nyissa meg **az AD FS** > **hitelesítési házirendek** > **Globális többtényezős hitelesítési házirendjét.** Törölje a jelet a **WindowsAzureMultiFactorAuthentication** vagy **az AzureMFAServerAuthentication jelölőnégyzetből** (a jelenlegi telepített verziótól függően).

   Ha ez a lépés befejeződött, az MFA-kiszolgálón keresztüli kétlépéses ellenőrzés nem érhető el ebben az AD FS-fürtben, amíg nem fejezi be a 8.

4. Törölje az AD FS-adapter régebbi verziójának regisztrációját a Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell parancsfájl futtatásával. Győződjön meg arról, hogy a *-Name* paraméter (vagy "WindowsAzureMultiFactorAuthentication" vagy "AzureMFAServerAuthentication") megegyezik a 3. Ez az AD FS-fürt összes kiszolgálójára vonatkozik, mivel központi konfiguráció van.
5. Regisztrálja az új AD FS-adaptert a Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell parancsfájl futtatásával. Ez az AD FS-fürt összes kiszolgálójára vonatkozik, mivel központi konfiguráció van.
6. Indítsa újra az AD FS szolgáltatást az AD FS-farmról eltávolított minden kiszolgálón.
7. Adja hozzá a frissített kiszolgálókat az AD FS-farmhoz, és távolítsa el a többi kiszolgálót a farmból.
8. Nyissa meg **az AD FS** > **hitelesítési házirendek** > **Globális többtényezős hitelesítési házirendjét.** Ellenőrizze **az AzureMfaServerAuthentication**.
9. Ismételje meg a 2.
10. Adja hozzá ezeket a kiszolgálókat az AD FS-farmhoz.

## <a name="next-steps"></a>További lépések

* Példák [speciális forgatókönyvekre az Azure többtényezős hitelesítésével és a külső VPN-ekkel](howto-mfaserver-nps-vpn.md)

* [Az MFA-kiszolgáló szinkronizálása a Windows Server Active Directoryval](howto-mfaserver-dir-ad.md)

* [A Windows-hitelesítés konfigurálása](howto-mfaserver-windows.md) az alkalmazásokhoz
