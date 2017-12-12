---
title: "Használati forgatókönyvek és az Azure AD-csatlakozás telepítési szempontjai |} Microsoft Docs"
description: "Ismerteti, hogyan rendszergazdák állíthat be az Azure AD Join a saját végfelhasználóik számára (az alkalmazottak, a diákok, más felhasználókat). A cikk ismerteti az Azure AD Joint a különböző valós forgatókönyv is."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 173ad6f07699ca6bfa534dedc053663bba571382
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Használati forgatókönyvek és az Azure AD-csatlakozás telepítési szempontjai
## <a name="usage-scenarios-for-azure-ad-join"></a>Az Azure AD Join használati forgatókönyvek
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>1. forgatókönyv: Vállalatok nagy mértékben a felhőben
Az Azure Active Directory Join (Azure AD Join) előnyei Ha jelenleg működik és identitáskezelést saját üzleti a felhőben vagy a felhőre rövidesen áthelyezi. Használhatja az Azure ad-ben bejelentkezni a Windows 10 létrehozott fiók. Keresztül [első alkalommal történő futtatásakor élmény (FRX) folyamat](active-directory-azureadjoin-user-frx.md), vagy az Azure AD összeillesztésével [a beállítások menü](active-directory-azureadjoin-user-upgrade.md), a felhasználók csatlakozhatnak a gépek az Azure AD.  A felhasználók is élvezheti az (egyszeri bejelentkezés SSO) hozzáférést a felhőalapú erőforrásokhoz Office 365-höz hasonló böngészőjük vagy egy Office-alkalmazásban.

### <a name="scenario-2-educational-institutions"></a>2. forgatókönyv: Oktatási intézmények
Oktatási intézmények általában rendelkeznek kétféle felhasználói: faculty és a diákok. Faculty tagok számít a szervezet hosszabb távú tagjai. A helyszíni fiókokat hoz létre nekik kívánatos. A diákok a szervezet shorter-term tagjai és fiókjukat kezelheti az Azure ad-ben. Ez azt jelenti, hogy a felhő helyett a helyszínen tárolt alatt directory méretezési továbbíthatja. Azt is jelenti, hogy a diákok fogja tudni Windows jelentkezzen be a saját Azure AD-fiókok és Office 365-erőforrások eléréséhez az Office-alkalmazásokban.

### <a name="scenario-3-retail-businesses"></a>3. forgatókönyv: Kiskereskedelemben
Kereskedelmi cégek határozza dolgozó munkatársak és a hosszú távú alkalmazottak rendelkezik. Általában a helyi fiókok létrehozásához, és tartományhoz csatlakoztatott számítógépeken használja a hosszabb távú teljes munkaidejű alkalmazottak számára. De határozza munkavállalók a szervezet shorter-term tagjai, és kezelheti a fiókok, ahol a felhasználói licencek könnyebben átvihetők kívánatos. A felhasználói fiókokat hozhat létre az Office 365 licencek a felhőben, ha ezek a felhasználók beolvasása a bejelentkezés a Windows és Office alkalmazások az Azure AD-fiókot, amíg a licenccel rendelkező nagyobb rugalmasságot karbantartása, hogy kilép a előnyeit.

### <a name="scenario-4-additional-scenarios"></a>4. forgatókönyv: További forgatókönyvek
Az előnyöket, azt a korábbiakban említettük, valamint hogy a felhasználók az eszközök regisztrálása az Azure AD miatt egy egyszerűsített csatlakozó élmény, hatékony kezelése, automatikus mobileszköz-kezelési beléptetés és egyszeri bejelentkezés az Azure AD előnyei és a helyszíni erőforrások.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Azure AD-csatlakozás telepítési szempontjai
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Lehetővé teszi a felhasználók közvetlenül az Azure AD a vállalat tulajdonában álló eszközről csatlakozni
A vállalatok partnervállalatokban és a szervezetek a csak felhőalapú fiókok biztosításához. Ezek a partnerek egyszerűen ezután hozzáférhetnek vállalati alkalmazások és erőforrások eléréséről az egyszeri bejelentkezés. Ebben a forgatókönyvben olyan felhasználók számára, akik elsősorban a felhőben, például az Office 365-öt vagy SaaS-alkalmazásokhoz Azure AD hitelesítésében támaszkodó erőforrások alkalmazható.

### <a name="prerequisites"></a>Előfeltételek
**A vállalati szinten (rendszergazda)**

* Azure-előfizetések az Azure Active Directoryval  

**A felhasználói szinten**

* Windows 10 (Professional és Enterprise kiadás)

### <a name="administrator-tasks"></a>Rendszergazdai feladatok
* [Az eszközregisztráció beállítása](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Felhasználói feladatok
* [A telepítés során az Azure AD-val a Windows 10 új eszköz beállítása](active-directory-azureadjoin-user-frx.md)
* [Egy Windows 10-es eszköz beállítása az Azure AD a beállítások menüből](active-directory-azureadjoin-user-upgrade.md)
* [A szervezet egy személyes Windows 10 rendszerű eszköz csatlakoztatása](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>A Windows 10-re a szervezetében BYOD engedélyezése
Állíthat be a felhasználók és az alkalmazottak a személyes Windows eszközök használata (BYOD) hozzáférni a vállalati alkalmazások és erőforrások használatára. A felhasználók az Azure AD-fiókok (munkahelyi vagy iskolai fiókok) adhat hozzá egy saját Windows eszköz biztonságos és megfelelő módon erőforrások eléréséhez.

### <a name="prerequisites"></a>Előfeltételek
**A vállalati szinten (rendszergazda)**

* Azure AD-előfizetés

**A felhasználói szinten**

* Windows 10 (Professional és Enterprise kiadás)

### <a name="administrator-tasks"></a>Rendszergazdai feladatok
* [Az eszközregisztráció beállítása](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Felhasználói feladatok
* [A szervezet egy személyes Windows 10 rendszerű eszköz csatlakoztatása](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>További információ
* [Vállalati használatú Windows 10: Az eszközök munkahelyi célú használata](active-directory-azureadjoin-windows10-devices-overview.md)
* [A felhőalapú képességek kiterjesztése a Windows 10-eszközökre az Azure Active Directory Joinon keresztül](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passporton keresztül jelszó nélkül identitások hitelesítése](active-directory-azureadjoin-passport.md)
* [További információk az Azure AD Join használati forgatókönyveiről](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD-hez Windows 10-es környezetben](active-directory-azureadjoin-devices-group-policy.md)
* [Az Azure AD Join beállítása](active-directory-azureadjoin-setup.md)

