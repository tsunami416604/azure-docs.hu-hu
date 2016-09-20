<properties 
    pageTitle="Bevezetés az Azure Multi-Factor Authentication és az Active Directory összevonási szolgáltatások használatába" 
    description="Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" ms.topic="get-started-article" 
    ms.date="08/04/2016" 
    ms.author="billmath"/>

# Bevezetés az Azure Multi-Factor Authentication és az Active Directory összevonási szolgáltatások használatába



<center>![Felhő](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Ha a szervezete az AD FS használatával vonta össze a helyszíni Active Directoryt az Azure Active Directoryval, a következő két módon használható az Azure Multi-Factor Authentication.

- A felhőerőforrások védelme az Azure Multi-Factor Authentication vagy az Active Directory összevonási szolgáltatások használatával 
- A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló használatával 

A következő táblázat foglalja össze a hitelesítésbeli különbségeket az erőforrások az Azure Multi-Factor Authentication használatával, illetve az AD FS használatával való védelme esetén

|Hitelesítés – Böngészőalapú alkalmazások|Hitelesítés – Nem böngészőalapú alkalmazások
:------------- | :------------- | :------------- |
Az Azure AD-erőforrások védelme az Azure Multi-Factor Authentication használatával |<li>A hitelesítés első tényezője a helyszínen történik az AD FS-sel.</li> <li>A második tényező felhőalapú hitelesítést használó telefonalapú módszer.</li>|A végfelhasználók alkalmazásjelszavakkal jelentkezhetnek be.
Az Azure AD-erőforrások védelme az Active Directory összevonási szolgáltatásokkal |<li>A hitelesítés első tényezője a helyszínen történik az AD FS-sel.</li><li>A második tényező a helyszínen történik a jogcím betartásával.</li>|A végfelhasználók alkalmazásjelszavakkal jelentkezhetnek be.

Összevont felhasználók alkalmazásjelszavaival kapcsolatos figyelmeztetések: 

- Az alkalmazásjelszavak ellenőrzése felhőalapú hitelesítéssel történik, így mellőzik az összevonásokat. Az összevonás csak alkalmazásjelszó beállításakor van aktív használatban.
- Az alkalmazásjelszó nem tartja be a helyszíni ügyfél hozzáférés-vezérlési beállításait.
- Az alkalmazásjelszavak használata esetén nem érhető el a helyszíni hitelesítésnaplózás.
- A fiókok letiltása/törlése akár 3 óráig is eltarthat a dirsync szinkronizálással, ami késlelteti az alkalmazásjelszó letiltását/törlését a felhőalapú identitásban.

Az Azure Multi-Factor Authentication vagy az Azure Multi-Factor Authentication-kiszolgáló az AD FS-sel való beállításával kapcsolatos információért lásd a következőt:

- [A felhőerőforrások védelme az Azure Multi-Factor Authentication és az AD FS használatával](multi-factor-authentication-get-started-adfs-cloud.md)
- [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és a Windows Server 2012 R2 AD FS használatával](multi-factor-authentication-get-started-adfs-w2k12.md)
- [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS 2.0-s verziójának használatával](multi-factor-authentication-get-started-adfs-adfs2.md)







 



<!--HONumber=sep16_HO1-->


