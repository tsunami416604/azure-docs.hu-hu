---
title: Felhasználók hitelesítése az Azure Active Directoryban
description: Azure AD-rendszergazdaként hogyan gondoskodhatom a felhasználóhitelesítés biztonságáról és tehetem egyszerűbbé a hitelesítést a végfelhasználók számára?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 0b232ed8bacfeb896fd5ee6ff9e2a58b71dc1517
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162971"
---
# <a name="what-methods-are-available-for-authentication"></a>Milyen hitelesítési módszerek érhetők el?

A hírekben folyamatosan azt lehet hallani, hogy a jelszavakat ellopják, és az identitásokat feltörik. A jelszó mellett egy második hitelesítési tényező megkövetelése azonnal növeli a vállalat biztonságát. A Microsoft Azure Active Directory (Azure AD) különféle szolgáltatásaival, mint az Azure Multi-Factor Authentication (Azure MFA) és az új Azure AD-jelszó önkiszolgáló kérése (SSPR) a rendszergazdák kiegészítő hitelesítési módszerekkel gondoskodhatnak a vállalat és a felhasználók védelméről.

Ha egy felhasználó egy érzékeny alkalmazáshoz szerezne hozzáférni, új jelszót kér, vagy engedélyezné a Windows Hellót, a rendszer további azonosítással ellenőrzi, hogy valóban az-e, akinek mondja magát.

A további azonosítás különféle hitelesítési módszerekkel történhet, például a következőkkel:

* E-mailben vagy szöveges üzenetben kapott kód
* Telefonhívás
* A telefonra kapott értesítés vagy kód
* Biztonsági kérdések megválaszolása

![Példa a login.microsoftonline.com bejelentkezési oldal megjelenésére a Chrome-ban](media/overview-authentication/overview-login.png)

Az Azure MFA és az új Azure AD-jelszó önkiszolgáló kérése révén a rendszergazdák nagyobb kontrollt kapnak a konfiguráció, a szabályzatok, a monitorozás és a jelentéskészítés felett az Azure AD és az Azure Portal használatával a vállalat védelme érdekében.

## <a name="self-service-password-reset"></a>Új jelszó önkiszolgáló kérése

Az új jelszó önkiszolgáló kérésével a felhasználók a rendszergazda beavatkozása nélkül kérhetnek új jelszót, ahol és amikor szükségük van rá.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

Az új jelszó önkiszolgáló kérése a következőkből áll:

* **Jelszó módosítása:** Tudom a jelszavam, de szeretném módosítani.
* **Új jelszó kérése:** Nem tudok bejelentkezni, és szeretnék új jelszót kérni egy vagy több jóváhagyott hitelesítési módszer használatával.
* **Fiók zárolásának feloldása:** Nem tudok bejelentkezni, mivel a fiókom zárolva lett, és szeretném feloldani a zárolást egy vagy több jóváhagyott hitelesítési módszer használatával.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. A rendszergazda által engedélyezett hitelesítési módszerek használatával az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáféréssel szemben, miközben lehetővé teszi az igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát.

## <a name="next-steps"></a>További lépések

Következő lépésként konfiguráljuk az új jelszó önkiszolgáló kérését és az Azure Multi-Factor Authenticationt.

Az új jelszó önkiszolgáló kérése használatának megkezdéséhez tekintse meg [az SSPR engedélyezését bemutató rövid útmutató cikket](quickstart-sspr.md).

Az új jelszó önkiszolgáló kérésével kapcsolatos további információkért tekintse meg [az új Azure AD-jelszó önkiszolgáló kérésének működését bemutató](concept-sspr-howitworks.md) cikket.

Az Azure Multi-Factor Authenticationnel kapcsolatos további információkért tekintse meg [az Azure Multi-Factor Authentication működését bemutató](concept-mfa-howitworks.md) cikket.