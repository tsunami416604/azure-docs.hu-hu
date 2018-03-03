---
title: "Engedélyezheti vagy tilthatja le a LinkedIn-integráció az Office-alkalmazásokhoz az Azure Active Directoryban |} Microsoft Docs"
description: "Ismerteti, hogyan engedélyezhető vagy tiltható le Microsoft-alkalmazások az Azure Active Directoryban LinkedIn-integráció"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cdfb5458b020e9d3a3f33cecbeb0ee7b9a48909d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Office alkalmazások LinkedIn-integráció
Ez a cikk bemutatja, hogyan korlátozhatja a felhasználókat, akiknek LinkedIn-integráció foglalt Azure Active Directory (Azure AD). LinkedIn-integráció alapértelmezés szerint engedélyezve van, a bérlő számára, amely lehetővé teszi a Microsoft-alkalmazások némelyike belül nyilvános LinkedIn adatok való hozzáadásakor. Minden felhasználó egymástól függetlenül választhat LinkedIn-fiókjuk csatlakozni a munkahelyi vagy iskolai fiókkal.

> [!IMPORTANT]
> LinkedIn-integráció nem telepítése minden Azure AD-bérlő egyszerre. Miután annak frissítése hogy az Azure-bérlőhöz, LinkedIn-integráció alapértelmezés szerint engedélyezve van. Nyissa meg helyi, állami és kormányzati bérlők LinkedIn-integráció nem érhető el. Bevezetés információk naprakészen nézetet, tekintse meg a [Office 365 terv](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) lap.

## <a name="linkedin-integration-from-the-user-perspective"></a>A felhasználó szemszögéből LinkedIn-integráció
Ha a szervezetében lévő felhasználók kapcsolódnak a LinkedIn a munkahelyi vagy iskolai fiók, [tehetik lehetővé, hogy adatokkal LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077) Microsoft-alkalmazások és szolgáltatások, amelyek a szervezet által használható. [Felhasználói fiókok leválaszthatja](https://www.linkedin.com/help/linkedin/answer/85097), amely távolítja el az adatok megosztása a Microsofttal LinkedIn engedély. LinkedIn integrációs nyilvánosan elérhető LinkedIn-profil adatait használja. [Felhasználók szabályozhatják, hogyan saját LinkedIn-profilját megtekintett](https://www.linkedin.com/help/linkedin/answer/83) LinkedIn adatvédelmi beállításai, beleértve, hogy a profil tekintheti meg Microsoft-alkalmazások.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások
Az Azure AD LinkedIn-integráció engedélyezése lehetővé teszi a Microsoft-alkalmazások és szolgáltatások eléréséhez a felhasználók LinkedIn adatok egy részét. Olvassa el a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement/) további információt az adatvédelmi megfontolások, ha engedélyezi a LinkedIn-integráció az Azure ad-ben. 

## <a name="manage-linkedin-integration"></a>LinkedIn-integráció kezelése
A vállalatok LinkedIn-integráció alapértelmezés szerint engedélyezve van a Azure AD-ben. LinkedIn-integráció lehetővé teszi, hogy minden felhasználó a szervezeten belül a Microsoft-szolgáltatások, például az Outlook megtekintés, LinkedIn profilok LinkedIn funkcióinak használatát. LinkedIn-integráció letiltása LinkedIn-szolgáltatásokat távolít el a Microsoft-alkalmazások és szolgáltatások, és leállítja az adatmegosztás LinkedIn és a szervezet Microsoft-szolgáltatások között.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Engedélyezheti vagy tilthatja le a szervezet az Azure portálon LinkedIn-integráció

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.
2. Válassza ki **felhasználók**.
3. Az a **felhasználók** panelen válassza **felhasználói beállítások**.
4. A **LinkedIn integrációs**, jelölje be **Igen** vagy **nem** engedélyezheti vagy tilthatja le a LinkedIn-integráció.
   ![LinkedIn-integráció engedélyezése](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Engedélyezheti vagy tilthatja le a csoportházirend segítségével a szervezet Office 2016 alkalmazások LinkedIn-integráció

1. Töltse le a [Office 2016 felügyeleti sablonfájlokat (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Bontsa ki a **ADMX** fájlokat, és másolja őket a **központi tárházban**.
3. Nyissa meg a házirend kezelése.
4. Hozzon létre egy csoportházirend-objektum a következő beállítás: **felhasználói konfiguráció** > **felügyeleti sablonok** > **Microsoft Office 2016**  >  **Vegyes** > **LinkedIn-integráció engedélyezése**.
5. Válassza ki **engedélyezett** vagy **letiltott**.
  * Ha a házirend van **engedélyezve**, a **megjelenítése LinkedIn-funkciók egy Office-alkalmazásban** az Office 2016 beállítások párbeszédpanelen található beállítás engedélyezve van. Ez azt is jelenti, hogy a szervezeti felhasználók az Office-alkalmazásokban LinkedIn funkciók is használhatók.
  * Ha a házirend van **le van tiltva**, a **megjelenítése LinkedIn-funkciók egy Office-alkalmazásban** talált az Office 2016 beállításai párbeszédpanelen állítsa be a letiltott állapotba, és a végfelhasználók a beállítás nem módosítható. A szervezeti felhasználók az Office 2016 alkalmazásaikat LinkedIn-funkciók nem használható. 

Ez a csoportházirend csak a helyi számítógépen Office 2016 alkalmazások érvényes. Felhasználók láthatják az Office 365 teljes profil kártyák LinkedIn-szolgáltatásokat, még akkor is, ha az Office 2016 alkalmazásaikban elkezdődnek LinkedIn. 

### <a name="learn-more"></a>Részletek 
* [LinkedIn-információkat és szolgáltatások a Microsoft-alkalmazások](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Súgó](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>További lépések
A következő hivatkozás segítségével megtekintheti az aktuális LinkedIn-integrációs beállításainak az Azure-portálon:

[LinkedIn-integráció konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 