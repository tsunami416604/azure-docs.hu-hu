---
title: "Az Azure AD Join beállítása a felhasználóknak | Microsoft Docs"
description: "Ez a cikk ismerteti, hogy a rendszergazdák miként állíthatják be az Azure AD Joint a helyszíni címtár- és eszközregisztrációhoz."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: bfc5d415-c918-4d8b-afee-b3f41cc28469
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0480c4f89b6036897bc8d7eff81b56262ea8806c
ms.openlocfilehash: e4e0fa77552c4df2ea5bb9ddae916e7c661824d1
ms.contentlocale: hu-hu
ms.lasthandoff: 02/23/2017


---
# <a name="setting-up-azure-ad-join-in-your-organization"></a>Az Azure AD Join beállítása a vállalatánál
Az Azure Active Directory Join (Azure AD Join) beállítása előtt szinkronizálnia kell a helyszíni felhasználócímtárat a felhővel, vagy manuálisan kell felügyelt fiókokat létrehoznia az Azure AD-ben.

A helyszíni felhasználók Azure Advel történő szinkronizálásának részletes útmutatásáért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

A felhasználók manuális létrehozásához és felügyeletéhez lásd: [Felhasználófelügyelet az Azure AD-ben](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Az eszközregisztráció beállítása
1. Jelentkezzen be az Azure Portalra rendszergazdaként.
2. A bal oldali panelen válassza az **Active Directory** elemet.
3. A **Címtár** lapon válassza ki a címtárat.
4. Válassza a **Konfigurálás** lapot.
5. Lépjen az **Eszközök** szakaszra.
6. Az **Eszközök** lapon állítsa be a következőket:  
   * **ESZKÖZÖK MAXIMÁLIS SZÁMA FELHASZNÁLÓNKÉNT:** Válassza ki azon eszközök maximális számát, amelyekkel a felhasználók az Azure AD-ben rendelkezhetnek.  Ha egy felhasználó eléri ezt a kvótát, nem tud további eszközöket felvenni, amíg el nem távolít egy vagy több meglévő eszközt.
   * **TÖBBTÉNYEZŐS HITELESÍTÉS MEGKÖVETELÉSE ESZKÖZÖK CSATLAKOZTATÁSÁHOZ:** Állítsa be, hogy a felhasználóknak meg kell-e adniuk egy második hitelesítési tényezőt ahhoz, hogy csatlakoztassák az eszközüket az Azure AD-hez. Az Azure többtényezős hitelesítésére vonatkozó további információért lásd: [Azure Multi-Factor Authentication a felhőben – első lépések](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).
   * **A FELHASZNÁLÓK CSATLAKOZTATHATJÁK AZ ESZKÖZEIKET AZ AZURE AD-HEZ:** Válassza ki azokat a felhasználókat és csoportokat, amelyek csatlakoztathatják az eszközeiket az Azure AD-hez.
   * **TOVÁBBI RENDSZERGAZDÁK AZ AZURE AD-HOZ CSATLAKOZTATOTT ESZKÖZÖKÖN:** Az Azure AD Prémium kiadással vagy a Nagyvállalati mobilitási csomaggal (EMS) kiválaszthatja, hogy mely felhasználók kapjanak helyi rendszergazdai jogosultságokat az eszközhöz. A globális rendszergazdák és eszköztulajdonosok alapértelmezés szerint helyi rendszergazdai jogosultságot kapnak.

<center>![Az eszközregisztráció beállítása](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

Miután beállította az Azure AD Joint a felhasználók számára, a vállalati vagy személyes eszközeiken keresztül csatlakozhatnak az Azure AD-hez.

A következő három forgatókönyvvel engedélyezheti, hogy a felhasználók beállíthassák az Azure AD Joint:

* A felhasználók a vállalat tulajdonában álló eszközről csatlakoznak közvetlenül az Azure AD-hez.
* A felhasználók egy tartományon keresztül csatlakoztathatnak egy, a vállalat tulajdonában álló eszközt a helyszíni Active Directoryhoz, majd kiterjeszthetik az eszközt az Azure AD-re.
* A felhasználók munkahelyi vagy iskolai fiókokat adnak a Windows rendszerhez a személyes eszközükön

## <a name="additional-information"></a>További információ
* [Vállalati használatú Windows 10: Az eszközök munkahelyi célú használata](active-directory-azureadjoin-windows10-devices-overview.md)
* [A felhőalapú képességek kiterjesztése a Windows 10-eszközökre az Azure Active Directory Joinon keresztül](active-directory-azureadjoin-user-upgrade.md)
* [További információk az Azure AD Join használati forgatókönyveiről](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD-hez Windows 10-es környezetben](active-directory-azureadjoin-devices-group-policy.md)
* [Az Azure AD Join beállítása](active-directory-azureadjoin-setup.md)


