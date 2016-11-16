---
title: "Azure Active Directory Reporting: első lépések | Microsoft Docs"
description: "Felsorolja az Azure Active Directory Reportingban elérhető különböző jelentéseket."
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6ce0e0ce9004e1b331328fca5830f01b6ce6af6c


---
# <a name="getting-started-with-azure-active-directory-reporting"></a>Bevezetés az Azure Active Directory Premium Reporting használatába
## <a name="what-it-is"></a>Mi ez?
Az Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a címtárához. A benne foglalt jelentések listája:

### <a name="security-reports"></a>Biztonsági jelentések
* Bejelentkezések ismeretlen forrásokról
* Több hibát követő bejelentkezések
* Bejelentkezések különböző földrajzi régiókból
* Bejelentkezések gyanús tevékenységeket mutató IP-címekkel
* Rendszertelen bejelentkezési tevékenység
* Bejelentkezések potenciálisan fertőzött eszközökről
* Rendellenes bejelentkezési tevékenységet mutató felhasználók

### <a name="activity-reports"></a>Tevékenységjelentések
* Alkalmazáshasználat: összegzés
* Alkalmazáshasználat: részletes
* Alkalmazás irányítópultja
* Alkalmazás-kiépítési hibák
* Egyéni felhasználói eszközök
* Egyéni felhasználói tevékenység
* Csoporttevékenység-jelentés
* Jelszó-visszaállítási regisztrációs tevékenységjelentés
* Jelszó-visszaállítási tevékenység

### <a name="audit-reports"></a>Naplózási jelentések
* Címtárnaplózási jelentés

> [!TIP]
> Az Azure AD Reporting további dokumentációiért lásd: [View your access and usage reports](active-directory-view-access-usage-reports.md) (A hozzáférési és használati jelentések megtekintése).
> 
> 

## <a name="how-it-works"></a>Működés
### <a name="reporting-pipeline"></a>Jelentéskészítési folyamat
A jelentéskészítési folyamat három fő lépésből áll. Minden felhasználói bejelentkezéskor vagy hitelesítéskor a következő történik:

* Először a rendszer hitelesíti a felhasználót (sikeresen vagy sikertelenül), és az eredményt az Azure Active Directory szolgáltatás adatbázisaiban tárolja.
* Rendszeres időközönként minden friss bejelentkezést feldolgoz. Ezen a ponton a rendellenes tevékenységeket észlelő, illetve biztonságos algoritmusok gyanús tevékenységeket keresnek az összes legutóbbi bejelentkezésben.
* A feldolgozás után a rendszer minden jelentést leír, gyorsítótáraz és kiad a klasszikus Azure portálon.

### <a name="report-generation-times"></a>Előállítási idők jelentése
Az Azure AD platform által feldolgozott hitelesítések és bejelentkezések nagy száma miatt a legutóbb feldolgozott bejelentkezések átlagosan egy órával korábbiak. Ritka esetben akár 8 órát is igénybe vehet a legutóbbi bejelentkezések feldolgozása.

A legutóbb feldolgozott bejelentkezést az egyes jelentések tetején megjelenő súgószövegben találja.

![Az egyes jelentések tetején megjelenő súgószöveg](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Az Azure AD Reporting további dokumentációiért lásd: [View your access and usage reports](active-directory-view-access-usage-reports.md) (A hozzáférési és használati jelentések megtekintése).
> 
> 

## <a name="getting-started"></a>Bevezetés
### <a name="sign-into-the-azure-classic-portal"></a>Bejelentkezés a klasszikus Azure portálra
Először globális rendszergazdaként vagy szabályozási ügyintézőként be kell jelentkeznie a [klasszikus Azure portálra](https://manage.windowsazure.com). Emellett Azure-előfizetési szolgáltatási rendszergazdának vagy társadminisztrátornak is kell lennie, vagy a „Hozzáférés az Azure AD-hez” Azure-előfizetést kell használnia.

### <a name="navigate-to-reports"></a>Navigálás a jelentésekhez
A jelentések megtekintéséhez nyissa meg a Jelentések lapot a címtár tetején.

Ha most nyitja meg először a jelentéseket, akkor a megtekintésük előtt el kell fogadnia a megjelenő párbeszédpanel feltételeit. A rendszer így győződik meg arról, hogy elfogadható, hogy a szervezet rendszergazdái megtekintik ezeket az adatokat, amelyek egyes országokban bizalmas információnak számítanak.

![Párbeszédpanel](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Az egyes jelentések megismerése
Lépjen egyenként a jelentésekre, így megtekintheti az összegyűjtött adatokat és a feldolgozott bejelentkezéseket. Itt találja meg [az összes jelentés listáját](active-directory-reporting-guide.md).

![Minden jelentés](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>A jelentések letöltése CSV-fájlként
Az egyes jelentések letölthetők CSV-fájlként (vesszővel tagolt adatfájlként). Ezeket a fájlokat felhasználhatja az Excelben, a PowerBI-ban vagy más külső elemzőprogramokban az adatok további elemzéséhez.

Ha egy jelentést CSV-formátumban szeretne letölteni, navigáljon a jelentéshez, és kattintson lent a „Letöltés” gombra.

![Letöltés gomb](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Az Azure AD Reporting további dokumentációiért lásd: [View your access and usage reports](active-directory-view-access-usage-reports.md) (A hozzáférési és használati jelentések megtekintése).
> 
> 

## <a name="next-steps"></a>Következő lépések
### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Rendellenes bejelentkezési tevékenységek riasztásainak testreszabása
Navigáljon a „Konfigurálás” lapra a címtárban.

Görgessen az „Értesítések” szakaszhoz.

Engedélyezze vagy tiltsa le az „Email Notifications of Anomalous sign-ins” (Értesítés e-mailben a rendellenes bejelentkezésekről) szakaszt.

![Az Értesítések szakasz](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integráció az Azure AD Reporting API-val
Lásd: [Bevezetés a Reporting API használatába](active-directory-reporting-api-getting-started.md).

### <a name="engage-multifactor-authentication-on-users"></a>Multi-Factor Authentication engedélyezése a felhasználók számára
Válasszon ki egy felhasználót egy jelentésben.

Kattintson a képernyő alján található „MFA engedélyezése” gombra.

![A Multi-Factor Authentication gomb a képernyő alján](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Az Azure AD Reporting további dokumentációiért lásd: [View your access and usage reports](active-directory-view-access-usage-reports.md) (A hozzáférési és használati jelentések megtekintése).
> 
> 

## <a name="learn-more"></a>Részletek
### <a name="audit-events"></a>Események naplózása
Megtudhatja, milyen eseményeket naplóz az [Azure Active Directory Reporting Audit Events](active-directory-reporting-audit-events.md) a címtárban.

### <a name="api-integration"></a>API-integráció
Lásd: [Bevezetés a Reporting API használatába](active-directory-reporting-api-getting-started.md) és [API-referenciadokumentáció](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Kapcsolatfelvétel
Az [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) e-mail címre bármilyen észrevételét, problémáját vagy kérdését elküldheti.

> [!TIP]
> Az Azure AD Reporting további dokumentációiért lásd: [View your access and usage reports](active-directory-view-access-usage-reports.md) (A hozzáférési és használati jelentések megtekintése).
> 
> 




<!--HONumber=Nov16_HO2-->


