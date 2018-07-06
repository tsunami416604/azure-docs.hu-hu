---
title: Engedélyezze a LinkedIn-kapcsolatok a Microsoft-alkalmazások és szolgáltatások az Azure Active Directoryban |} A Microsoft Docs
description: Azt ismerteti, hogyan engedélyezheti vagy tilthatja le a Microsoft-alkalmazások az Azure Active Directoryban a LinkedIn-fiókkapcsolatok
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871966"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>A Microsoft-alkalmazások és szolgáltatások LinkedIn-fiókkapcsolatok
Ebből a cikkből megismerheti a LinkedIn-fiókkapcsolatok a bérlő az Azure Active Directory (Azure AD) felügyeleti központban kezelheti. 

> [!IMPORTANT]
> LinkedIn-fiók kapcsolatok funkció jelenleg tesszük elérhetővé az Azure AD-bérlők számára. Amikor bevezetné a bérlőjéhez, alapértelmezés szerint engedélyezve van. Ez nem érhető Egyesült Államok kormányzati ügyfelei számára, és a szervezetek számára az Exchange Online szolgáltatásban működő postaládák az ausztráliai, Kanada, Kína, Franciaország, Németország, India, Dél-Korea, Egyesült Királyság, japán és Dél-Afrika. Ezek a postaláda-helyek támogatása hamarosan elérhető lesz.  Naprakész képet bevezetéséről, lásd: a [Office 365 ütemterv](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) lapot.

## <a name="benefit-to-users"></a>Kihasználhatja a felhasználók számára
Miután a felhasználók a saját LinkedIn-fiók csatlakoztatásával, LinkedIn adatok személyre szabott információkat és a funkciók különböző Microsoft-alkalmazások vagy szolgáltatások megjelenítése. Felhasználói láthatják a személyek információival, azok működik együtt a Microsoft profil kártya akkor is, ha azokat a szervezeten kívüli személyeknek. Az idő múlásával LinkedIn szerzett is lesz, így relevánsabb és testre szabott munkahelyi. Például a LinkedIn javaslat új kapcsolat alapján is felhasználók számára, akik, vagy az adott napon naptári embert surface információival.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>LinkedIn-fiókkapcsolatok hogyan jelennek meg a felhasználó számára
LinkedIn-fiókkapcsolatok engedélyezése a felhasználók nyilvános LinkedIn-profil információt belüli egyes Microsoft-alkalmazásaikat. A bérlő felhasználói lehet váltani, csatlakozzon a LinkedIn és a Microsoft munkahelyi vagy iskolai fiókok LinkedIn-profil további információt. További információkért lásd: [LinkedIn információkat és a Microsoft-alkalmazások és szolgáltatások szolgáltatásait](https://go.microsoft.com/fwlink/?linkid=850740).

Ha a szervezet felhasználói csatlakozzon a LinkedIn és a Microsoft munkahelyi vagy iskolai fiókokat, akkor két lehetősége van: 
* Engedélyezze a fiókot is az adatmegosztást. Ez azt jelenti, hogy azok engedélyt az adatok megosztása a Microsoft munkahelyi vagy iskolai fiókkal, valamint a Microsoft a LinkedIn-fiók munkahelyi vagy iskolai fiók adatok megosztását a LinkedIn-fiók. A Linkedinnel megosztott adatok elhagyja az online szolgáltatások. 
* Engedélyezze a csak azok a Microsoft munkahelyi LinkedIn-fiók és az iskolai fiókkal az adatok megosztása

További információ a felhasználók LinkedIn és a Microsoft között megosztott adatok munkahelyi vagy iskolai fiókok, lásd: [a munkahelyi vagy iskolai Microsoft-alkalmazásokkal a LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Felhasználói fiókok leválaszthatja](https://www.linkedin.com/help/linkedin/answer/85097) és a megosztási engedélyek bármikor adatok eltávolítása. 
* [Felhasználók szabályozhatják, hogyan a saját LinkedIn-profilok megtekintett](https://www.linkedin.com/help/linkedin/answer/83), például hogy a profil tekinthet meg a Microsoft-alkalmazások.

## <a name="privacy-considerations"></a>Adatvédelmi szempontok
LinkedIn fiókkapcsolatok lehetővé teszi, hogy Microsoft-alkalmazások és szolgáltatások hozzáférhetnek a felhasználók LinkedIn-adatok egy részét. Olvassa el a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement/) további információ az adatvédelmi megfontolásokat az Azure ad-ben a LinkedIn-fiókkapcsolatok engedélyezésekor. 

## <a name="manage-linkedin-account-connections"></a>LinkedIn-fiókkapcsolatok kezelése
LinkedIn-fiók kapcsolatok funkció alapértelmezés szerint engedélyezve van a teljes bérlő számára. LinkedIn-fiókkapcsolatok a teljes bérlőn, engedélyezése vagy letiltása a kijelölt felhasználók számára a bérlőben LinkedIn-fiókkapcsolatok választhat. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Engedélyezheti vagy tilthatja le a LinkedIn kapcsolata a bérlő az Azure Portalon

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.
2. Válassza ki **felhasználók**.
3. Az a **felhasználók** panelen válassza ki **felhasználói beállítások**.
4. A **LinkedIn-fiókkapcsolatok**:
  * Válassza ki **Igen** ahhoz, hogy a bérlő összes felhasználója a LinkedIn-fiókkapcsolatok
  * Válassza ki **kijelölt** engedélyezéséhez LinkedIn-fiók csak a kiválasztott bérlő kapcsolatok felhasználók
  * Válassza ki **nem** letiltása az összes felhasználó LinkedIn-fiókkapcsolatok ![engedélyezése LinkedIn-fiókkapcsolatok](./media/linkedin-integration/linkedin-integration.png)
5. Ha elkészült, válassza a beállítások mentéséhez **mentése**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Engedélyezi vagy letiltja a LinkedIn-fiókkapcsolatok csoportházirend használatával a szervezet Office 2016-alkalmazások

1. Töltse le a [Office 2016 felügyeleti sablonfájlok (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Bontsa ki a **ADMX** fájlokat, és másolja őket a központi tárolóban.
3. Nyissa meg a Csoportházirend kezelése.
4. Hozzon létre egy csoportházirend-objektum a következő beállítással: **felhasználói konfiguráció** > **felügyeleti sablonok** > **a Microsoft Office 2016**  >  **Egyéb rendelkezések** > **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban**.
5. Válassza ki **engedélyezve** vagy **letiltott**.
  * Ha a szabályzat a **engedélyezve**, a **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** az Office 2016 beállítások párbeszédpanelen található beállítás engedélyezve van. Ez azt is jelenti, hogy a szervezet felhasználói az Office alkalmazások munkahelyi LinkedIn-szolgáltatásokat használhat.
  * Ha a szabályzat a **le van tiltva**, a **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** beállítást található az Office 2016 beállításai párbeszédpanelen állítsa be a letiltott állapotba, és a végfelhasználók a beállítás nem módosítható. A szervezet felhasználói az Office 2016 alkalmazásaikban nem használható a munkahelyi LinkedIn-szolgáltatásokat.

A csoportházirend érvényes csak az Office 2016-alkalmazások a helyi számítógépeken. Felhasználói láthatják az Office 365 teljes profil kártyák munkahelyi LinkedIn-szolgáltatásokat, akkor is, ha a LinkedIn elkezdődnek Office 2016-alkalmazásokban. 

### <a name="learn-more"></a>Részletek 
* [LinkedIn-adatokat és a szolgáltatások a Microsoft-alkalmazásba](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn-súgóközpont](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>További lépések
A következő hivatkozás használatával tekintse meg az aktuális LinkedIn-fiókkapcsolatok beállítása az Azure Portalon:

[LinkedIn-fiókkapcsolatok konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 