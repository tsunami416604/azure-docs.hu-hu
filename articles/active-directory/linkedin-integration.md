---
title: "A Microsoft-alkalmazások és szolgáltatások az Azure Active Directoryban LinkedIn-kapcsolatok engedélyezése |} Microsoft Docs"
description: "Ismerteti a Microsoft-alkalmazások az Azure Active Directoryban LinkedIn fiók kapcsolatok engedélyezése vagy letiltása"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 3bf224edea9e6da0d0eadb6fb6a409248de3d0e3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>LinkedIn fiók kapcsolatok a Microsoft-alkalmazások és szolgáltatások
Ebből a cikkből megismerheti az Azure Active Directory (Azure AD) felügyeleti központban a bérlő LinkedIn fiók kapcsolatok kezelésére. 

> [!IMPORTANT]
> LinkedIn fiók kapcsolatok használatának van jelenleg megkezdődött az Azure AD bérlők számára. Ha annak frissítése a bérlő, alapértelmezés szerint engedélyezve van. Nincs elérhető az Amerikai Egyesült Államok kormányzati ügyfelek és a szervezetek számára az Exchange Online szolgáltatásban működő Ausztrália, Kanada, Kína, Franciaország, Németország, India, Dél-Korea, Egyesült Királyságban, japán, és Dél-afrikai postaládák. Ezeket a postaláda-helyeket támogatása hamarosan elérhető.  Bevezetés információk naprakészen nézetet, tekintse meg a [Office 365 terv](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) lap.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Hogyan LinkedIn fiók kapcsolatok jelennek meg, a felhasználó számára
LinkedIn fiók kapcsolatok engedélyezése a felhasználók a Microsoft-alkalmazások némelyike belül nyilvános LinkedIn profil információk megjelenítéséhez. A bérlő a felhasználók eldönthetik, LinkedIn és a Microsoft munkahelyi vagy iskolai fiókok LinkedIn-profil további információk megjelenítéséhez. További információkért lásd: [LinkedIn információkat és a Microsoft-alkalmazások és szolgáltatások funkciókat](https://go.microsoft.com/fwlink/?linkid=850740).

Ha a szervezetében lévő felhasználók csatlakozzon a LinkedIn és a Microsoft munkahelyi vagy iskolai fiókok, azok két lehetőség közül választhat: 
* Mindkét fiókok közötti adatmegosztásra engedélyt. Ez azt jelenti, hogy azok engedélyt a LinkedIn fiókjuk adatok megosztása a Microsoft munkahelyi vagy iskolai fiókját, valamint a Microsoft munkahelyi vagy iskolai fiók adatok megosztása a LinkedIn-fiókkal. LinkedIn a megosztott adatok hagyja el az online szolgáltatások számára. 
* Ahhoz, hogy csak a LinkedIn-fiókot a Microsoft munkahelyi és iskolai fiókjával adatokat engedélyezése

További információ a felhasználói LinkedIn és a Microsoft között megosztott adatok munkahelyi vagy iskolai fiókok című [a Microsoft-alkalmazások a munkahelyi és iskolai LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Felhasználói fiókok leválaszthatja](https://www.linkedin.com/help/linkedin/answer/85097) és a megosztási engedélyek bármikor adatokat távolítja el. 
* [Felhasználók szabályozhatják, hogyan saját LinkedIn-profilját megtekintett](https://www.linkedin.com/help/linkedin/answer/83), például hogy a profil tekintheti meg Microsoft-alkalmazások.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások
LinkedIn fiók kapcsolatok lehetővé teszi, hogy a Microsoft-alkalmazások és szolgáltatások eléréséhez a felhasználók LinkedIn adatok egy részét. Olvassa el a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement/) további információt az adatvédelmi megfontolások LinkedIn fiók kapcsolatok engedélyezése az Azure ad-ben. 

## <a name="manage-linkedin-account-connections"></a>LinkedIn fiók kapcsolatok kezelése
LinkedIn fiók kapcsolatok funkció alapértelmezés szerint be van a teljes bérlő számára. Kiválaszthatja, hogy a teljes bérlő LinkedIn fiók kapcsolatok, engedélyezése vagy letiltása LinkedIn fiók kapcsolatok kijelölt felhasználóknak az Ön bérelt szolgáltatásának. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Engedélyezheti vagy tilthatja le a LinkedIn fiók kapcsolat a bérlője Azure-portálon

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.
2. Válassza ki **felhasználók**.
3. Az a **felhasználók** panelen válassza **felhasználói beállítások**.
4. A **LinkedIn fiók kapcsolatok**:
  * Válassza ki **Igen** LinkedIn fiók kapcsolatok az összes felhasználó számára az Ön bérelt szolgáltatásának engedélyezése
  * Válassza ki **kijelölt** engedélyezése LinkedIn-fiókok csak a kiválasztott bérlő kapcsolatok felhasználóinak
  * Válassza ki **nem** LinkedIn fiók kapcsolatok az összes felhasználó letiltása ![engedélyezése LinkedIn fiók kapcsolatok](./media/linkedin-integration/LinkedIn-integration.png)
5. Mentse a beállításokat, amikor elkészült, kiválasztásával **mentése**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>A csoportházirend segítségével a szervezet Office 2016 alkalmazások LinkedIn fiók kapcsolatok engedélyezése vagy letiltása

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
A következő hivatkozás segítségével megtekintheti az aktuális LinkedIn fiók kapcsolat beállítása az Azure portálon:

[LinkedIn fiók kapcsolatok konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 