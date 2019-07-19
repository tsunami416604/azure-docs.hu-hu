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
ms.openlocfilehash: 7f695d9f0240f8c27ea0bedba7e532d37a177752
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304746"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-üzembehelyezési tervek
Átfogó útmutatót keres az Azure Active Directory (Azure AD) egyes képességeinek üzembe helyezéséhez? Az alábbi üzembehelyezési tervek végigvezetik az egyes gyakoribb Azure AD-képességek sikeres bevezetéséhez szükséges, az üzleti értékkel, a tervezési szempontokkal, a kialakítással és az üzemeltetéssel kapcsolatos eljárásokon. 

A dokumentumok többek között e-mailes sablonokat, rendszerarchitektúra-diagramokat és gyakori teszteseteket is tartalmaznak. 

Kíváncsiak vagyunk a dokumentumokkal kapcsolatos visszajelzésére. Ennek a rövid [felmérésnek](https://aka.ms/deploymentplanfeedback) a kitöltésével tudathatja velünk, mennyire találta hasznosnak a dokumentumokat. 

## <a name="include-the-right-stakeholders"></a>A megfelelő résztvevők belefoglalása

Amikor megkezdi az üzembe helyezést egy új képesség megtervezése során, fontos, hogy a szervezeten belül kulcsfontosságú érdekelteket vegyen fel. Javasoljuk, hogy azonosítsa és dokumentálja azokat a személyeket vagy személyeket, akik a következő szerepköröket teljesítik, és működjenek együtt velük a projektben való részvételük meghatározásához.  

A szerepkörök a következők lehetnek: 

|Role |Leírás |
|-|-|
|Végfelhasználó|Azoknak a felhasználóknak a reprezentatív csoportja, amelyekhez a képességet alkalmazni kívánja. Gyakran megtekinti a kísérleti program módosításait.
|INFORMATIKAI támogatás kezelője|Támogatja a szervezeti képviselőt, aki megadhatja a változás támogatását a helpdesk szemszögéből.  
|Identity Architect vagy Azure globális rendszergazda|Az Identitáskezelés csapatának képviselője, amely meghatározza, hogy a változás hogyan igazodik a szervezet alapvető Identity Management infrastruktúrához.|
|Alkalmazás üzleti tulajdonosa |Az érintett alkalmazás (ok) általános üzleti tulajdonosa, amely magában foglalhatja a hozzáférés kezelését is.  A felhasználói élmény és a változás hasznosságát is megadhatja a végfelhasználó szemszögéből.
|Biztonsági tulajdonos|A biztonsági csapat képviselője, amely kijelentkezhet, hogy a terv megfelel a szervezete biztonsági követelményeinek.|
|Megfelelőség-kezelő|A szervezeten belül a vállalat, az iparág vagy a kormányzati követelmények teljesítésének biztosításáért felelős személy.|

**A részvételi szintek a következők lehetnek:**

- Az **R**esponsible a projekt tervének és eredményének megvalósításához 

- **Pproval és**-eredmény 

- **C**ontributor a projekt tervéhez és eredményéhez 

- **Nformed és**végeredmény
 
## <a name="deployment-plans"></a>Üzembe helyezési csomagok



|Forgatókönyv |Leírás |
|-|-|
|[Többtényezős hitelesítés](../authentication/howto-mfa-getstarted.md)|Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. A rendszergazda által engedélyezett hitelesítési módszerek használatával az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáféréssel szemben, miközben lehetővé teszi az igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát.|
|[Feltételes hozzáférés](https://aka.ms/deploymentplans/ca)|A feltételes hozzáféréssel olyan automatizált hozzáférés-vezérlési döntéseket hozhat létre, amelyek a feltételek alapján hozzáférhetnek a felhőalapú alkalmazásokhoz.|
|[Új jelszó önkiszolgáló kérése](https://aka.ms/deploymentplans/sspr)|Az új jelszó önkiszolgáló kérésével a felhasználók a rendszergazda beavatkozása nélkül igényelhetnek új jelszót, ahol és amikor csak szükségük van rá.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) segítségével felügyelheti az Azure AD, az Azure-erőforrások és más Microsoft Online Services Kiemelt felügyeleti szerepköreit. A PIM olyan megoldásokat kínál, mint az igény szerinti hozzáférés, a jóváhagyási munkafolyamatok kérése és a teljes körűen integrált hozzáférési felülvizsgálatok, amelyek segítségével valós időben azonosíthatja, feltárhatja és megakadályozhatja a Kiemelt szerepkörök rosszindulatú tevékenységeit.|
|[Egyszeri bejelentkezés](https://aka.ms/deploymentplans/sso)|Az egyszeri bejelentkezéssel mindössze egyetlen bejelentkezéssel és egyetlen felhasználói fiókkal elérheti az üzleti tevékenységeihez szükséges összes alkalmazást és erőforrást. Miután bejelentkezett, a Microsoft Office-ból anélkül léphet át a SalesForce-ba vagy a Boxba, hogy újfent el kellene végeznie a hitelesítést (például meg kellene adnia egy jelszót).|
|[Közvetlen egyszeri bejelentkezés](https://aka.ms/SeamlessSSODPDownload)|Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. A szolgáltatás bekapcsolását követően a felhasználóknak nem kell megadniuk a jelszavukat az Azure AD-be való bejelentkezéshez, sőt általában még a felhasználónevüket sem kell megadniuk. A szolgáltatás használatával a felhasználók könnyen elérhetik a felhőalapú alkalmazásokat anélkül, hogy ehhez további helyszíni összetevőkre lenne szükség.|
|[Hozzáférési panel](https://aka.ms/AccessPanelDPDownload)|A felhasználók egy egyszerű központ felderítése és érik el a alkalmazásokat kínál. Lehetővé teszi önkiszolgáló képességek kérhet hozzáférést az új alkalmazások és a csoportok, például a hatékonyabb legyen, vagy mások nevében ezekhez az erőforrásokhoz való hozzáférés kezelése.|
|[ADFS a jelszókivonat-szinkronizáláshoz](https://aka.ms/deploymentplans/adfs2phs)|A jelszókivonat-szinkronizálás szinkronizálja a felhasználói jelszavak kivonatait a helyszíni Active Directoryból az Azure AD-be, így az Azure AD úgy tudja hitelesíteni a felhasználókat, hogy nem kell kommunikálnia a helyszíni Active Directoryval.|
|[ADFS az átmenő hitelesítéshez](https://aka.ms/deploymentplans/adfs2pta)|Az Azure AD átmenő hitelesítésével a falhasználók ugyanazzal a jelszóval jelentkezhetnek be a helyszíni és a felhőalapú alkalmazásokba. Ezzel a szolgáltatással javítható a felhasználók felhasználói élménye, mivel eggyel kevesebb jelszót kell megjegyezni, továbbá csökkenthetők az informatikai ügyfélszolgálat költségei, mivel a felhasználók kisebb valószínűséggel felejtik el a bejelentkezési adataikat. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat.|
|[Azure AD-alkalmazásproxy](https://aka.ms/deploymentplans/appproxy)|Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. A saját eszközeiken szeretnek dolgozni, legyenek azok táblagépek, telefonok vagy laptopok. És emellett a munkavállalók az összes alkalmazásukat el szeretnék érni, a felhőben üzemelő SaaS-alkalmazásokat és a helyszínen üzemelő vállalati alkalmazásokat egyaránt. A helyszíni alkalmazások eléréséhez régebben virtuális magánhálózatokat (VPN) vagy szegélyhálózatokat (más néven DMZ vagy demilitarizált zóna) használtak. Ezeket a megoldásokat azonban nem csupán nagyon nehéz és komplex dolog védelemmel ellátni, hanem a beállításuk és az üzemeltetésük is nagyon költséges. Van jobb módszer is! – az Azure AD-alkalmazásproxy|
|[Felhasználók regisztrálása](https://aka.ms/UserProvisioningDPDownload)|Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a felhőalapú (SaaS-) alkalmazásokban, például a Dropboxban, a Salesforce-ban vagy a ServiceNow-ban.|
|[Bejövő felhasználók WORKDAY-központú átadása](https://aka.ms/WorkdayDeploymentPlan)|WORKDAY-központú bejövő Felhasználókiépítés az Active Directory hoz létre a folyamatban lévő identitáskezelést alapjait és üzleti folyamatok, a mérvadó identitási adatokat használó minőségét javítja. Ezzel a funkcióval, zökkenőmentesen kezelhető az alkalmazottak és a függő munkavállalók identitás-életciklus informatikai üzembe helyezési műveletek (például a létrehozás, engedélyezés, Joiner-Rekordáthelyezőnek – "kilépő" folyamatok (például új szabad, Leállítás, átvitel) leképezhető szabályok konfigurálása Tiltsa le, és törölje fiókok).|
