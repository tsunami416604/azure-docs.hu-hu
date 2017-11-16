---
title: "Az Azure MFA-felhő vagy -kiszolgáló kiválasztása | Microsoft Docs"
description: "Az Önnek legmegfelelőbb többtényezős hitelesítési biztonsági megoldás kiválasztásához döntse el, mit szeretne biztonságossá tenni, és hol tárolja a rendszer a felhasználóit.  Ezután válassza a felhő, az MFA-kiszolgáló vagy az AD FS lehetőséget."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 18d5cd8f3904a0c6ea89df440d917785510409cb
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Az Azure Multi-Factor Authentication-megoldás kiválasztása
Mivel az Azure Multi-Factor Authentication (MFA) számos változata használható, meg kell válaszolni néhány kérdést annak eldöntéséhez, hogy melyiket érdemes használni.  A kérdések a következők:

* [Mit próbálok biztonságossá tenni?](#what-am-i-trying-to-secure)
* [Hol tárolja a rendszer a felhasználókat?](#where-are-the-users-located)
* [Mely szolgáltatásokra van szükségem?](#what-features-do-i-need)

A következő szakaszok útmutatást nyújtanak az egyes kérdések megválaszolásához.

## <a name="what-am-i-trying-to-secure"></a>Mit próbálok biztonságossá tenni?
A megfelelő kétlépéses ellenőrzési megoldás meghatározásához először meg kell válaszolni azt a kérdést, hogy mit szeretnénk biztonságossá tenni egy második hitelesítési módszerrel.  Egy alkalmazást az Azure-ban?  Vagy egy távelérésű rendszert?  Annak meghatározásával, hogy mit szeretne biztonságossá tenni, választ kap arra a kérdésre, hogy hol szükséges engedélyezni a többtényezős hitelesítést.  

| Mit próbál biztonságossá tenni? | MFA a felhőben | MFA-kiszolgáló |
| --- |:---:|:---:|
| Belső Microsoft-alkalmazások |● |● |
| SaaS-alkalmazások az alkalmazáskatalógusban |● |  |
| Az Azure AD-alkalmazásproxyn keresztül közzétett webalkalmazások |● |  |
| Nem az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások | |● |
| Távelérés, például VPN vagy RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Hol tárolja a rendszer a felhasználókat?
Ezután annak alapján, hogy a rendszer hol tárolja a felhasználókat, meghatározhatja a megfelelő megoldást, legyen az a felhőben, vagy a helyszínen az MFA-kiszolgálót használva.

| Felhasználó helye | MFA a felhőben | MFA-kiszolgáló |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD és helyszíni AD összevonással az AD FS-sel |● |● |
| DirSync, Azure AD Sync és Azure AD Connect szolgáltatást használó Azure AD és helyszíni AD – jelszó-szinkronizálás nélkül |● |● |
| DirSync, Azure AD Sync és Azure AD Connect szolgáltatást használó Azure AD és helyszíni AD – jelszó-szinkronizálással |● | |
| Helyszíni Active Directory | |● |

## <a name="what-features-do-i-need"></a>Mely szolgáltatásokra van szükségem?
A következő táblázat a felhőbeli Multi-Factor Authentication és a Multi-Factor Authentication-kiszolgáló szolgáltatásait hasonlítja össze.

| Szolgáltatás | MFA a felhőben | MFA-kiszolgáló |
| --- |:---:|:---:|
| Mobilalkalmazásos értesítés második tényezőként | ● | ● |
| Mobilalkalmazásos ellenőrzőkód második tényezőként | ● | ● |
| Telefonhívás második tényezőként | ● | ● |
| Egyirányú SMS második tényezőként | ● | ● |
| Kétirányú SMS második tényezőként | | ● |
| Hardvertokenek második tényezőként | | ● |
| Alkalmazásjelszavak az MFA-t nem támogató Office 365-ügyfelekhez | ● | |
| A hitelesítési módszerek rendszergazdai szabályozása | ● | ● |
| PIN-mód | | ● |
| Csalási riasztás |● | ● |
| MFA-jelentések |● | ● |
| Egyszeri mellőzés | | ● |
| Egyéni üdvözlések a telefonhívásokhoz | ● | ● |
| Testreszabható hívóazonosító a telefonhívásokhoz | ● | ● |
| Megbízható IP-címek | ● | ● |
| MFA megjegyzése megbízható eszközökön | ● | |
| Feltételes hozzáférés | ● | ● |
| Gyorsítótár |  | ● |

## <a name="next-steps"></a>Következő lépések

Most, hogy tisztában van a különbséggel a felhőalapú Azure Multi-Factor Authentication és a helyszíni MFA-kiszolgáló között, beállíthatja és használatba veheti az Azure Multi-Factor Authenticationt. **Válassza ki az ikont, amely a forgatókönyvéhez tartozik**

<center>

[![MFA a felhőben](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![MFA-kiszolgáló](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</center>
