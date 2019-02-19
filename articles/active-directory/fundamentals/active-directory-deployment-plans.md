---
title: Központi telepítési csomagok – Azure Active Directory |} A Microsoft Docs
description: Teljes körű útmutatást üzembe helyezése az Azure Active Directory számos funkciót.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dfce660340654e98883ae6e696644175c005459
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343085"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-üzembehelyezési tervek
Átfogó útmutatót keres az Azure Active Directory (Azure AD) egyes képességeinek üzembe helyezéséhez? Az alábbi üzembehelyezési tervek végigvezetik az egyes gyakoribb Azure AD-képességek sikeres bevezetéséhez szükséges, az üzleti értékkel, a tervezési szempontokkal, a kialakítással és az üzemeltetéssel kapcsolatos eljárásokon. 

A dokumentumok többek között e-mailes sablonokat, rendszerarchitektúra-diagramokat és gyakori teszteseteket is tartalmaznak. 

Kíváncsiak vagyunk a dokumentumokkal kapcsolatos visszajelzésére. Ennek a rövid [felmérésnek](https://aka.ms/deploymentplanfeedback) a kitöltésével tudathatja velünk, mennyire találta hasznosnak a dokumentumokat. 

|Forgatókönyv |Leírás |
|-|-|
|[Többtényezős hitelesítés](https://aka.ms/MFADPDownload)|Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. A rendszergazda által engedélyezett hitelesítési módszerek használatával az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáféréssel szemben, miközben lehetővé teszi az igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát.|
|Feltételes hozzáférés [letölthető terv](https://aka.ms/CADPDownload) vagy [online-előfizetéssel](https://aka.ms/deploymentplans/ca)|A feltételes hozzáféréssel automatikus döntéshozatali képességeket valósíthat meg azzal kapcsolatban, hogy különféle feltételek alapján kik férhetnek hozzá felhőalkalmazásaihoz.|
|[Új jelszó önkiszolgáló kérése](https://aka.ms/SSPRDPDownload)|Az új jelszó önkiszolgáló kérésével a felhasználók a rendszergazda beavatkozása nélkül igényelhetnek új jelszót, ahol és amikor csak szükségük van rá.|
|[Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)|Az Azure AD Privileged Identity Management (PIM) segít a kiemelt jogosultságú rendszergazdai szerepkörök kezelése az Azure ad-ben, Azure-erőforrások és más Microsoft Online Services. A PIM például igény szerinti elérése, a kérelem-jóváhagyási munkafolyamatokat és a teljes körűen integrált hozzáférési felülvizsgálatok így azonosíthatja, tárhat fel, és a valós idejű kiemelt szerepkörök rosszindulatú tevékenységek megakadályozása megoldásokat nyújt.|
|[Egyszeri bejelentkezés](https://aka.ms/SSODPDownload)|Az egyszeri bejelentkezéssel mindössze egyetlen bejelentkezéssel és egyetlen felhasználói fiókkal elérheti az üzleti tevékenységeihez szükséges összes alkalmazást és erőforrást. Miután bejelentkezett, a Microsoft Office-ból anélkül léphet át a SalesForce-ba vagy a Boxba, hogy újfent el kellene végeznie a hitelesítést (például meg kellene adnia egy jelszót).|
|[Közvetlen egyszeri bejelentkezés](https://aka.ms/SeamlessSSODPDownload)|Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. A szolgáltatás bekapcsolását követően a felhasználóknak nem kell megadniuk a jelszavukat az Azure AD-be való bejelentkezéshez, sőt általában még a felhasználónevüket sem kell megadniuk. A szolgáltatás használatával a felhasználók könnyen elérhetik a felhőalapú alkalmazásokat anélkül, hogy ehhez további helyszíni összetevőkre lenne szükség.|
|[Hozzáférési panel](https://aka.ms/AccessPanelDPDownload)|A felhasználók egy egyszerű központ felderítése és érik el a alkalmazásokat kínál. Lehetővé teszi önkiszolgáló képességek kérhet hozzáférést az új alkalmazások és a csoportok, például a hatékonyabb legyen, vagy mások nevében ezekhez az erőforrásokhoz való hozzáférés kezelése.|
|[ADFS a jelszókivonat-szinkronizáláshoz](https://aka.ms/ADFSTOPHSDPDownload)|A jelszókivonat-szinkronizálás szinkronizálja a felhasználói jelszavak kivonatait a helyszíni Active Directoryból az Azure AD-be, így az Azure AD úgy tudja hitelesíteni a felhasználókat, hogy nem kell kommunikálnia a helyszíni Active Directoryval.|
|[ADFS az átmenő hitelesítéshez](https://aka.ms/ADFSTOPTADPDownload)|Az Azure AD átmenő hitelesítésével a falhasználók ugyanazzal a jelszóval jelentkezhetnek be a helyszíni és a felhőalapú alkalmazásokba. Ezzel a szolgáltatással javítható a felhasználók felhasználói élménye, mivel eggyel kevesebb jelszót kell megjegyezni, továbbá csökkenthetők az informatikai ügyfélszolgálat költségei, mivel a felhasználók kisebb valószínűséggel felejtik el a bejelentkezési adataikat. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.|
|[Azure AD-alkalmazásproxy](https://aka.ms/AppProxyDPDownload)|Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. A saját eszközeiken szeretnek dolgozni, legyenek azok táblagépek, telefonok vagy laptopok. És emellett a munkavállalók az összes alkalmazásukat el szeretnék érni, a felhőben üzemelő SaaS-alkalmazásokat és a helyszínen üzemelő vállalati alkalmazásokat egyaránt. A helyszíni alkalmazások eléréséhez régebben virtuális magánhálózatokat (VPN) vagy szegélyhálózatokat (más néven DMZ vagy demilitarizált zóna) használtak. Ezeket a megoldásokat azonban nem csupán nagyon nehéz és komplex dolog védelemmel ellátni, hanem a beállításuk és az üzemeltetésük is nagyon költséges. Van jobb módszer is! – az Azure AD-alkalmazásproxy|
|[Felhasználók regisztrálása](https://aka.ms/UserProvisioningDPDownload)|Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a felhőalapú (SaaS-) alkalmazásokban, például a Dropboxban, a Salesforce-ban vagy a ServiceNow-ban.|
|[Bejövő felhasználók WORKDAY-központú átadása](https://aka.ms/WorkdayDeploymentPlan)|WORKDAY-központú bejövő Felhasználókiépítés az Active Directory hoz létre a folyamatban lévő identitáskezelést alapjait és üzleti folyamatok, a mérvadó identitási adatokat használó minőségét javítja. Ezzel a funkcióval, zökkenőmentesen kezelhető az alkalmazottak és a függő munkavállalók identitás-életciklus informatikai üzembe helyezési műveletek (például a létrehozás, engedélyezés, Joiner-Rekordáthelyezőnek – "kilépő" folyamatok (például új szabad, Leállítás, átvitel) leképezhető szabályok konfigurálása Tiltsa le, és törölje fiókok).|
