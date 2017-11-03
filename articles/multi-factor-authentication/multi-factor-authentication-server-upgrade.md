---
title: "Az Azure MFA kiszolgáló frissítése |} Microsoft Docs"
description: "Lépéseket és útmutatást az Azure multi-factor Authentication kiszolgáló újabb verzióra való frissítéséhez."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 3a2fb7413fa1391969d00ad281fd1ade3e0f40a8
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>A legújabb Azure multi-factor Authentication kiszolgáló frissítése

Ez a cikk végigvezeti a frissítés az Azure multi-factor Authentication (MFA) kiszolgáló v6.0 folyamata vagy magasabb. Ha frissítenie kell az a PhoneFactor-ügynök régebbi verzióját, tekintse meg a [a PhoneFactor ügynökről frissítsen az Azure multi-factor Authentication kiszolgáló](multi-factor-authentication-get-started-server-upgrade.md).

Frissítés 6.x vagy régebbi a v7.x vagy újabb, az összes összetevő váltson .NET 2.0-s .NET 4.5. Minden összetevő is Microsoft Visual C++ 2015 Redistributable Update 1 vagy újabb szükséges. A multi-factor Authentication kiszolgáló telepítő telepíti ezeket az összetevőket x86 és x64 verziója, ha még nincsenek telepítve. Ha a felhasználói portál és a mobilalkalmazás webszolgáltatás futtatása eltérő kiszolgálókra, telepítendő azokat a csomagokat az összetevőket a frissítés előtt. A Microsoft Visual C++ 2015 Redistributable legfrissebb kereshet a a [Microsoft Download Center](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Telepítse a legújabb verziót az Azure MFA kiszolgáló

1. Kövesse az utasításokat a [töltse le az Azure multi-factor Authentication kiszolgáló](multi-factor-authentication-get-started-server.md#download-the-mfa-server) az Azure MFA kiszolgáló letöltéséhez.
2. A multi-factor Authentication kiszolgáló adatfájljából helye: C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (feltéve, hogy az alapértelmezett telepítési helyére telepíti) a fő multi-factor Authentication kiszolgáló biztonsági másolatot készíteni.
3. Ha több kiszolgálót a magas rendelkezésre állású, módosítsa az ügyfélszámítógépeken, a multi-factor Authentication kiszolgálón elvégzett hitelesítéshez, hogy azok leállítása forgalom küldése a kiszolgálókat, amelyeket frissíteni. Ha terheléselosztót használ, multi-factor Authentication kiszolgáló eltávolítása a terheléselosztó, hajtsa végre a frissítést, és majd adja hozzá a kiszolgálót újra üzembe a farmban.
4. Minden multi-factor Authentication kiszolgálón indítsa el az új telepítőjét. Először frissítenie alárendelt kiszolgálók, mert a régi adatok fájl a fő szerint replikált elolvasása. 

  Nem kell a telepítő futtatása előtt távolítsa el az aktuális MFA kiszolgáló. A telepítő egy helybeni frissítést hajt végre. A telepítési útvonalat van felvenni, a korábbi telepítés a beállításjegyzékből, telepíti a helyre (például, C:\Program Files\Multi-Factor Authentication kiszolgáló). 
  
5. Ha a számítógép a Microsoft Visual C++ 2015 újraterjeszthető csomag telepítése, fogadja el a rendszer. A csomag x86 és x64 verziója van telepítve.
5. A webszolgáltatási SDK használatakor, az új webszolgáltatási SDK telepítésének megkezdésére. Ha az új webszolgáltatási SDK telepíti, győződjön meg arról, hogy a virtuális könyvtár neve megegyezik-e a korábban telepített virtuális könyvtárat (például MultiFactorAuthWebServiceSdk).
6. Ismételje meg az összes alárendelt kiszolgálókon. A beosztottak kell lennie az új főkiszolgáló egyikét léptesse, majd a régi fő kiszolgáló frissítésekor. 

## <a name="upgrade-the-user-portal"></a>A felhasználói portál frissítése

1. Készítsen biztonsági másolatot a web.config fájl, amely a virtuális könyvtárban, a felhasználói portál telepítési helyének (például C:\inetpub\wwwroot\MultiFactorAuth). Ha az alapértelmezett téma végzett módosításokat, a App_Themes\Default mappában, valamint biztonsági másolatot készíteni. Másolatot készít az alapértelmezett mappát, és hozzon létre egy új téma módosítsa az alapértelmezett téma-nál nagyobb legyen.
2. Ha a felhasználói portál a többi MFA kiszolgáló-összetevő ugyanazon a kiszolgálón fut, a multi-factor Authentication kiszolgáló telepítési kéri a felhasználói portál frissítése. Fogadja el a rendszer, és a felhasználói portál frissítés telepítéséhez. Ellenőrizze, hogy a virtuális könyvtár neve megegyezik-e a korábban telepített virtuális könyvtárat (például a MultiFactorAuth).
3. Ha a felhasználói portál egy saját kiszolgálót, másolja a MultiFactorAuthenticationUserPortalSetup64.msi fájlt a telepítési helyére telepíti a multi-factor Authentication kiszolgálók közül az egyik, és a felhasználói portál webes kiszolgálóra. Futtassa a telepítőt. 

  Ha a hiba akkor fordul elő, azzal, "A Microsoft Visual C++ 2015 Redistributable Update 1 vagy nagyobb szükség," töltse le és telepítse a legújabb frissítési csomagot a [Microsoft Download Center](https://www.microsoft.com/download/). Telepítse a x86 és x64 verziók.

4. A felhasználói portál frissített szoftverfrissítések telepítése után hasonlítsa össze a web.config – biztonsági másolatának meg az 1. lépésben az új web.config fájllal. Ha nem új attribútum szerepel az új web.config, másolja át a biztonsági mentési web.config a virtuális könyvtár felülírja az újjal. Másolja és illessze be az appSettings értékek és a Web Service SDK URL-CÍMÉT a biztonságimásolat-fájlt az új web.config be egy másik lehetőség egy.

Ha a felhasználói portál több kiszolgálón, ismételje meg az összes szoftver telepítését. 


## <a name="upgrade-the-mobile-app-web-service"></a>A mobilalkalmazás webszolgáltatás frissítése

1. Készítsen biztonsági másolatot a web.config fájl, amely a virtuális könyvtárban, a mobilalkalmazás webszolgáltatás telepítési helyének (például C:\inetpub\wwwroot\app vagy C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. A multi-factor Authentication kiszolgáló telepítési helyére másolja a multifactorauthenticationmobileappwebservicesetup64.msi programot fájlt, és elhelyezi a Mobile Apps regisztrációs webkiszolgálóra.
3. Futtassa a telepítőt. 

  Ha a hiba akkor fordul elő, arról, hogy a Microsoft Visual C++ 2015 terjeszthető frissítésének 1-es vagy újabb rendszer szükséges, töltse le és telepítse a legújabb frissítési csomagot a [Microsoft Download Center](https://www.microsoft.com/download/). Telepítse a x86 és x64 verziók.

4. A frissített mobilalkalmazás webszolgáltatásának szoftver telepítése után hasonlítsa össze a web.config fájl, amely biztonsági mentésekor az 1. lépésben az új web.config fájllal. Ha nem új attribútum szerepel az új web.config, a mentett web.config másolja a virtuális könyvtárba, és felülírja az újjal. Másolja és illessze be az appSettings értékek és a Web Service SDK URL-CÍMÉT a biztonságimásolat-fájlt az új web.config be egy másik lehetőség egy.

Ha a mobilalkalmazás webszolgáltatás több kiszolgálón, ismételje meg az összes szoftver telepítését. 

## <a name="upgrade-the-ad-fs-adapters"></a>Az AD FS-adapter frissítése


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Ha MFA fut, mint az AD FS különböző kiszolgálókon

Ezek az utasítások csak akkor érvényesek, ha a multi-factor Authentication kiszolgáló külön futtatja a parancsot az AD FS-kiszolgálók. Ha mindkét szolgáltatás ugyanazon a kiszolgálón fut, hagyja ki ezt a szakaszt, és nyissa meg a telepítési lépések. 

1. A MultiFactorAuthenticationAdfsAdapter.config fájlt az AD FS-ben regisztrált másolatának mentése, vagy a konfiguráció a következő PowerShell-paranccsal exportálhatja: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Az adapternév értéke "WindowsAzureMultiFactorAuthentication" vagy "AzureMfaServerAuthentication" attól függően, hogy a korábban telepített verzió.
2. Másolja a következő fájlokat az AD FS-kiszolgáló a multi-factor Authentication kiszolgáló telepítési helyére:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Adja hozzá a Register-MultiFactorAuthenticationAdfsAdapter.ps1 parancsfájlban szerkesztése `-ConfigurationFilePath [path]` végének a `Register-AdfsAuthenticationProvider` parancsot. Cserélje le *[elérési_út]* a teljes elérési útját a MultiFactorAuthenticationAdfsAdapter.config fájlt vagy a konfigurációs fájl exportálása az előző lépésben. 

  Ellenőrizze az új MultiFactorAuthenticationAdfsAdapter.config megjelenítéséhez, ha megfelelnek-e a régi konfigurációs fájl attribútumokat. Ha attribútuma hozzáadott vagy eltávolított az új verzióban, másolja az attribútumértékek a régi konfigurációs fájl az új üzenetbe vagy módosítsa a régi konfigurációs fájlt az egyeztetéshez.

### <a name="install-new-ad-fs-adapters"></a>Új AD FS-adapter telepítése

> [!IMPORTANT] 
> A felhasználók nem szükséges kétlépéses ellenőrzés 3 – 8. lépések során a jelen szakasz. Ha több fürt konfigurált AD FS, távolítsa el, frissítése és az állásidő elkerülése érdekében más fürtök függetlenül a farm egyes fürtök visszaállítása.

1. Néhány AD FS-kiszolgáló eltávolítása a farmból. Ezek a kiszolgálók frissítése, amíg a többi tovább működik.
2. Az új AD FS-adapter telepítése eltávolítja az AD FS-farm minden egyes kiszolgálón. Ha a multi-factor Authentication kiszolgáló telepítve van az AD FS-kiszolgálókon, frissítheti a multi-factor Authentication kiszolgáló rendszergazdája UX keresztül Ellenkező esetben frissítése MultiFactorAuthenticationAdfsAdapterSetup64.msi futtatásával. 

  Ha a hiba akkor fordul elő, azzal, "A Microsoft Visual C++ 2015 Redistributable Update 1 vagy nagyobb szükség," töltse le és telepítse a legújabb frissítési csomagot a [Microsoft Download Center](https://www.microsoft.com/download/). Telepítse a x86 és x64 verziók.

3. Ugrás a **AD FS** > **hitelesítési házirendek** > **globális többtényezős hitelesítési házirend szerkesztése**. Törölje a jelet **WindowsAzureMultiFactorAuthentication** vagy **AzureMFAServerAuthentication** (attól függően, hogy a legfrissebb verzió van telepítve). 

  Ez a lépés végrehajtása után multi-factor Authentication kiszolgálón keresztül a kétlépéses ellenőrzést nem érhető el az AD FS fürt 8. lépés végrehajtásáig.

4. Az AD FS-adapter régebbi verziója, a Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-parancsfájl futtatásával törölje regisztrációját. Győződjön meg arról, hogy a *-név* paraméter ("WindowsAzureMultiFactorAuthentication" vagy "AzureMFAServerAuthentication") a 3. lépésben megjelenő neve megegyezik-e. Ez vonatkozik az ugyanazon az AD FS-fürtben lévő mindegyik kiszolgálónak, mivel nincs központi konfiguráció.
5. A regisztrálást az új AD FS-adapter a Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-parancsfájl futtatásával. Ez vonatkozik az ugyanazon az AD FS-fürtben lévő mindegyik kiszolgálónak, mivel nincs központi konfiguráció.
6. Indítsa újra az AD FS szolgáltatás eltávolítja az AD FS-farm minden egyes kiszolgálón.
7. Vegye fel a frissített kiszolgálók újból az AD FS-farm, és a többi kiszolgáló eltávolítása a farmból.
8. Ugrás a **AD FS** > **hitelesítési házirendek** > **globális többtényezős hitelesítési házirend szerkesztése**. Ellenőrizze **AzureMfaServerAuthentication**.
9. Ismételje meg a 2. a kiszolgálók most eltávolítja az AD FS-farm frissítése, és indítsa újra az AD FS szolgáltatás ezeken a kiszolgálókon.
10. Ezek a kiszolgálók hozzáadása vissza az AD FS-farm.

## <a name="next-steps"></a>Következő lépések

- Példák az beszerzése [speciálisabb forgatókönyvek esetén az Azure multi-factor Authentication és a külső VPN-EK](multi-factor-authentication-advanced-vpn-configurations.md)

- [A Windows Server Active Directory MFA kiszolgáló szinkronizálása](multi-factor-authentication-get-started-server-dirint.md)

- [Windows-hitelesítés konfigurálása](multi-factor-authentication-get-started-server-windows.md) az alkalmazások számára
