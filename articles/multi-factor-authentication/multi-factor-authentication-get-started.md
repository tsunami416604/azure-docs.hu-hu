---
title: Azure Multi-Factor Authentication – Első lépések
description: Az Önnek legmegfelelőbb többtényezős hitelesítési biztonsági megoldás kiválasztásához döntse el, mit szeretne biztonságossá tenni, és hol tárolja a rendszer a felhasználóit.  Ezután válassza a felhő, az MFA-kiszolgáló vagy az AD FS lehetőséget.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# Válassza az Önnek legmegfelelőbb többtényezős biztonsági megoldást
Mivel az Azure Multi-Factor Authentication számos változata használható, el kell döntenünk néhány dolgot, hogy megállapítsuk, melyiket érdemes használni.  Ezek a következők:

* [Mit próbálok biztonságossá tenni?](#what-am-i-trying-to-secure)
* [Hol tárolja a rendszer a felhasználókat?](#where-are-the-users-located)

A következő szakaszok útmutatást nyújtanak a kérdések megválaszolásához.

## Mit próbálok biztonságossá tenni?
Annak érdekében, hogy megállapítsuk a megfelelő többtényezős hitelesítési megoldást, először meg kell válaszolnunk a kérdést, hogy mit szeretne biztonságossá tenni egy második hitelesítési módszerrel.  Egy alkalmazást az Azure-ban?  Vagy például egy távelérésű rendszert?  Azzal, hogy megállapítjuk, mit szeretnénk biztonságossá tenni, megkapjuk a választ arra a kérdésre, hogy hol kell engedélyeznünk a többtényezős hitelesítést.  

| Mit próbál biztonságossá tenni? | Multi-Factor Authentication a felhőben | Multi-Factor Authentication-kiszolgáló |
| --- |:---:|:---:|
| Belső Microsoft-alkalmazások |* |* |
| Saas alkalmazások az alkalmazáskatalógusban |* |* |
| Az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások |* |* |
| Nem az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások | |* |
| Távelérés, például VPN vagy RDG | |* |

## Hol tárolja a rendszer a felhasználókat?
Ezután attól függően, hogy a rendszer hol tárolja a felhasználókat, megtalálhatjuk a megfelelő megoldást, legyen az többtényezős hitelesítés a felhőben, vagy a helyszínen az MFA-kiszolgálóval.

| Felhasználó helye | Megoldás |
| --- |:--- |
| Azure Active Directory |Multi-Factor Authentication a felhőben |
| Azure AD és helyszíni AD összevonással az AD FS-sel |A felhőbeli MFA és az MFA-kiszolgáló is elérhető lehetőség |
| DirSync, Azure AD Sync és Azure AD Connect szolgáltatást használó Azure AD és helyszíni AD – jelszó-szinkronizálás nélkül |A felhőbeli MFA és az MFA-kiszolgáló is elérhető lehetőség |
| DirSync, Azure AD Sync és Azure AD Connect szolgáltatást használó Azure AD és helyszíni AD – jelszó-szinkronizálással |Multi-Factor Authentication a felhőben |
| Helyszíni Active Directory |Multi-Factor Authentication-kiszolgáló |

A következő táblázat a felhőbeli Multi-Factor Authentication és a Multi-Factor Authentication-kiszolgáló szolgáltatásait hasonlítja össze.

| Multi-Factor Authentication a felhőben | Multi-Factor Authentication-kiszolgáló |
| --- |:---:|:---:|
| Mobilalkalmazásos értesítés második tényezőként |● |
| Mobilalkalmazásos ellenőrzőkód második tényezőként |● |
| Telefonhívás második tényezőként |● |
| Egyirányú SMS második tényezőként |● |
| Kétirányú SMS második tényezőként | |
| Hardvertokenek második tényezőként | |
| Alkalmazásjelszavak az MFA-t nem támogató ügyfelekhez |● |
| A hitelesítési módszerek rendszergazdai szabályozása |● |
| PIN-mód | |
| Csalási riasztás |● |
| MFA-jelentések |● |
| Egyszeri mellőzés | |
| Egyéni üdvözlések a telefonhívásokhoz |● |
| Testreszabható hívóazonosító a telefonhívásokhoz |● |
| Megbízható IP-címek |● |
| MFA megjegyzése megbízható eszközökön |● |
| Feltételes hozzáférés |● |
| Gyorsítótár | |

Most, hogy eldöntöttük, hogy a felhőalapú többtényezős hitelesítést vagy a helyszíni MFA-kiszolgálót érdemes használni, megkezdhetjük az Azure Multi-Factor Authentication beállítását és használatát.   **Válassza ki az ikont, amely a forgatókönyvéhez tartozik.**

<center>




[![Felhő](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Sep16_HO4-->


