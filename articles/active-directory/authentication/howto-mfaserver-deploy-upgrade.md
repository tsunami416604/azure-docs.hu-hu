---
title: Az Azure MFA-kiszolgáló frissítése |} A Microsoft Docs
description: A lépések és útmutatást az Azure multi-factor Authentication-kiszolgáló frissítése újabb verzióra.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: dbb95c8aadc66e7ece93fa800055a0f6be81f015
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161412"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>A legújabb Azure multi-factor Authentication-kiszolgáló frissítése

Ez a cikk végigvezeti a folyamat frissítése az Azure multi-factor Authentication (MFA)-kiszolgáló 6.0-s vagy újabb. Ha a frissítés a PhoneFactor ügynökről régi verziója van szüksége, tekintse meg [frissítés a PhoneFactor ügynökről az Azure multi-factor Authentication-kiszolgáló](howto-mfaserver-deploy-upgrade-pf.md).

Ha frissít a 6.x-es vagy régebbi v7.x, vagy újabb, az összes összetevő váltson .NET 2.0-s .NET 4.5-ös verzióját. Az összes összetevő is frissítésére van szükség a Microsoft Visual C++ 2015 újraterjeszthető 1 vagy újabb verziója. Az MFA-kiszolgáló telepítője telepíti ezeket az összetevőket x86 és x64 verziója, ha még nincsenek telepítve. Ha a felhasználói portál és a Mobile App Web Service futtatni eltérő kiszolgálókra, azok a csomagok telepítése ezen összetevők frissítése előtt szüksége. A Microsoft Visual C++ 2015 Redistributable legfrissebb a kereshet a [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Telepítse a legújabb verziót az Azure MFA-kiszolgáló

1. Kövesse az utasításokat a [töltse le az Azure multi-factor Authentication-kiszolgáló](howto-mfaserver-deploy.md#download-the-mfa-server) az Azure MFA-kiszolgáló legújabb verziójának beszerzéséhez.
2. Készítsen biztonsági másolatot az MFA-kiszolgáló adatfájl található: C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (feltéve, hogy az alapértelmezett telepítési hely) a fő MFA-kiszolgálón.
3. Ha magas rendelkezésre állás érdekében több kiszolgálón futtatja, módosítsa az ügyfélszámítógépeken, az MFA-kiszolgáló hitelesítést, hogy a forgalmat küld, amely frissíti a kiszolgálók leáll. Ha terheléselosztót használ, MFA-kiszolgáló eltávolítása a terheléselosztóból, a frissítéshez, és adja hozzá az a kiszolgáló a farm be újra.
4. Futtassa az új telepítő minden egyes MFA-kiszolgálón. Először frissíteni az alárendelt kiszolgálók, mert a régi adatok fájl a fő által replikált elolvasása. 

  Nem kell az aktuális MFA-kiszolgáló eltávolítása a telepítő futtatása előtt. A telepítő egy helyszíni frissítését végzi. A telepítési útvonalat felülettől a beállításjegyzéket a korábbi telepítésből származó, (például C:\Program Files\Multi-Factor Authentication Server) ugyanazon a helyen telepíti. 
  
5. Ha a Microsoft Visual C++ 2015 újraterjeszthető csomag telepítése kéri, fogadja el a rendszer. A csomag x86 és x64 verziója van telepítve.
5. Ha a Web Service SDK-t használ, az új Web Service SDK telepítése kéri. Az új Web Service SDK telepítésekor győződjön meg arról, hogy a virtuális könyvtár neve megegyezik a korábban telepített virtuális könyvtárat (például Phonefactorwebservicesdk).
6. Ismételje meg az összes alárendelt kiszolgálókon. A beosztottak kell az új fő egyikét léptesse, majd a régi fő kiszolgáló frissítéséhez. 

## <a name="upgrade-the-user-portal"></a>A felhasználói portál frissítése

1. Készítsen biztonsági másolatot a web.config fájlt, amely a virtuális könyvtárban, a felhasználói portál telepítési helyére (pl. C:\inetpub\wwwroot\MultiFactorAuth). Ha az alapértelmezett téma módosítások történtek, készítsen biztonsági másolatot a App_Themes\Default mappát is. Célszerűbb másolatot készít az alapértelmezett mappát, és hozzon létre egy új témát, mint a módosítsa az alapértelmezett téma.
2. Ha a felhasználói portál a többi MFA-kiszolgáló összetevő ugyanazon a kiszolgálón fut, az MFA-kiszolgáló telepítését kéri a felhasználói portál frissítése. Fogadja el a rendszer, és a felhasználói portál frissítés telepítéséhez. Ellenőrizze, hogy a virtuális könyvtár neve megegyezik a korábban telepített virtuális könyvtárat (például a MultiFactorAuth).
3. Ha a felhasználói portált a saját kiszolgálóján, az MFA-kiszolgálók egyikét telepítési helyére másolja a multifactorauthenticationuserportalsetup64.msi telepítőfájlt fájlt, és helyezi azt az alakzatot a felhasználói portál webkiszolgálója. Futtassa a telepítőt. 

  Ha hiba lép fel azzal, "A Microsoft Visual C++ 2015 újraterjeszthető Update 1 vagy újabb szükség," töltse le és telepítse a legújabb frissítési csomagot a [Microsoft Download Center](https://www.microsoft.com/download/). Telepítse a x86 és x64 verziókat.

4. A felhasználói portál frissített szoftver telepítése után, hasonlítsa össze a web.config biztonsági mentést végzett, az 1. lépés-az új web.config fájlba. Ha nincsenek új jogcímek az új Web.config, másolja át a biztonsági mentési web.config a virtuális könyvtár felülírja az újat. Egy másik lehetőség, hogy másolja és illessze be az appSettings értékeket és a Web Service SDK URL-cím a biztonsági mentési fájlból, az új web.config.

Ha a felhasználói portál több kiszolgálón, ismételje meg a telepítés összes őket. 

## <a name="upgrade-the-mobile-app-web-service"></a>A Mobile App Web Service frissítése

> [!NOTE]
> Ha az Azure MFA-kiszolgáló 8.0-s verziójánál régebbiről frissít 8.0-nál újabb verzióra, a mobilalkalmazás webszolgáltatását eltávolíthatja a frissítés után

## <a name="upgrade-the-ad-fs-adapters"></a>Az AD FS-adapter frissítése

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Ha az MFA fut, mint az AD FS különböző kiszolgálókon

Ezek az utasítások csak akkor érvényesek, ha a multi-factor Authentication kiszolgáló külön-külön futtatja a parancsot az AD FS-kiszolgálók. Ha mindkét szolgáltatás ugyanazokat a kiszolgálókat futtatnia, kihagyhatja ezt a szakaszt, és nyissa meg a telepítési lépéseket. 

1. Mentse a MultiFactorAuthenticationAdfsAdapter.config fájlt az AD FS-ben regisztrált másolatát, vagy a konfiguráció a következő PowerShell-paranccsal exportálhatja: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Az adapter neve az "WindowsAzureMultiFactorAuthentication" vagy "AzureMfaServerAuthentication" függően a korábban telepített verzióját.
2. Az alábbi fájlokat az MFA-kiszolgáló telepítési helyéről másolja az AD FS-kiszolgálók:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Szerkessze a Register-MultiFactorAuthenticationAdfsAdapter.ps1 parancsfájlt hozzáadásával `-ConfigurationFilePath [path]` végéhez a `Register-AdfsAuthenticationProvider` parancsot. Cserélje le *[elérési út]* a teljes elérési útját a MultiFactorAuthenticationAdfsAdapter.config fájlt vagy a konfigurációs fájlban exportálja az előző lépésben. 

  Ellenőrizze az új MultiFactorAuthenticationAdfsAdapter.config megtekintheti, ha azok megfelelnek-e a régi konfigurációs fájl attribútumokat. Attribútumok hozzáadott vagy eltávolított az új verzióban, másolja az attribútumértékek az a régi konfigurációs fájlt az új névre, vagy módosítsa megfelelően a régi konfigurációs fájlt.

### <a name="install-new-ad-fs-adapters"></a>Új AD FS-adapter telepítése

> [!IMPORTANT] 
> A felhasználók nem szükséges, ez a szakasz 3 – 8. lépések során a kétlépéses hitelesítés végrehajtására. Ha az AD FS több fürtön konfigurálva van, távolítsa el, frissítése és visszaállításához a üzemszünet elkerülése érdekében a többi fürtök függetlenül a farm minden egyes fürt.

1. Bizonyos AD FS-kiszolgálók eltávolítása a farmból. Ezek a kiszolgálók frissítse, amíg a többi továbbra is futnak.
2. Az új AD FS-adapter telepítése eltávolítja az AD FS-farm minden egyes kiszolgálón. Ha az MFA-kiszolgáló AD FS-kiszolgálókon telepítve van, az MFA-kiszolgáló-rendszergazdai UX keresztül frissítheti Ellenkező esetben futtassa a MultiFactorAuthenticationAdfsAdapterSetup64.msi frissítése. 

  Ha hiba lép fel azzal, "A Microsoft Visual C++ 2015 újraterjeszthető Update 1 vagy újabb szükség," töltse le és telepítse a legújabb frissítési csomagot a [Microsoft Download Center](https://www.microsoft.com/download/). Telepítse a x86 és x64 verziókat.

3. Lépjen a **az AD FS** > **hitelesítési házirendek** > **globális többtényezős hitelesítési házirend szerkesztése**. Törölje a jelet **WindowsAzureMultiFactorAuthentication** vagy **AzureMFAServerAuthentication** (attól függően, a telepített aktuális verziót). 

  Ez a lépés befejezése után a kétlépéses ellenőrzés keresztül MFA-kiszolgáló nem áll rendelkezésre a AD FS-fürt 8. lépés végrehajtásáig.

4. A régebbi verzióját az AD FS-adapter regisztrációját a Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-parancsfájl futtatásával. Ügyeljen arra, hogy a *-név* paraméter ("WindowsAzureMultiFactorAuthentication" vagy "AzureMFAServerAuthentication") megegyezik a 3. lépésben megjelenő nevét. Ez vonatkozik az ugyanazon AD FS-fürt összes kiszolgálóján, mivel a központi konfigurációs van.
5. Az új AD FS-adapter regisztrálja a Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-parancsfájl futtatásával. Ez vonatkozik az ugyanazon AD FS-fürt összes kiszolgálóján, mivel a központi konfigurációs van.
6. Indítsa újra az AD FS szolgáltatás eltávolítja az AD FS-farm minden egyes kiszolgálón.
7. Kiszolgálókat ad hozzá a frissített vissza az AD FS-farm, és a többi kiszolgáló eltávolítása a farmból.
8. Lépjen a **az AD FS** > **hitelesítési házirendek** > **globális többtényezős hitelesítési házirend szerkesztése**. Ellenőrizze **AzureMfaServerAuthentication**.
9. Ismételje meg a az AD FS-farm eltávolítottuk a kiszolgálók frissítése a 2. lépésben, és indítsa újra az AD FS szolgáltatást az adott kiszolgálókon.
10. Vissza az AD FS-farm adja hozzá ezeket a kiszolgálókat.

## <a name="next-steps"></a>További lépések

- Példák a [speciális az Azure multi-factor Authentication és külső VPN-ekre vonatkozó forgatókönyvek](howto-mfaserver-nps-vpn.md)

- [MFA-kiszolgáló szinkronizálása a Windows Server Active Directoryval](howto-mfaserver-dir-ad.md)

- [Windows-hitelesítés konfigurálása](howto-mfaserver-windows.md) alkalmazásai számára
