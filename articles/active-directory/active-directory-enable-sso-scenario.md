---
title: Alkalmazások kezelése az Azure Active Directory |} Microsoft Docs
description: Ez a cikk az Azure Active Directory integrálása a helyszíni, felhő- és SaaS-alkalmazásokhoz előnyei.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: e05b2d515b997e769306146a5390d4d44fd5cf50
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="managing-applications-with-azure-active-directory"></a>Alkalmazások kezelése az Azure Active Directoryban
A tényleges munkafolyamat vagy a tartalom túl vállalatok rendelkeznek összes alkalmazás két alapvető követelményei:

1. A hatékonyság növelésére, alkalmazások könnyen megtalálhatóvá és hozzáférést kell lennie
2. Biztonsági és irányítási engedélyezéséhez a szervezet igényeinek és a szervezett a felhasználók és ténylegesen hozzáférő minden alkalmazás

A világ ez érhető el, leginkább vezérlőelemre identitásával felhőalapú alkalmazások "*ki jogosult tegye mi*".

A számítástechnikai terminológia:

* *Aki* nevezik *identitás* -felhasználók és csoportok kezelése
* *Mi* nevezik *hozzáférések felügyeletével* – a védett erőforrásokhoz való hozzáférés kezelése

Összetevőket egyaránt együtt nevezik *identitás- és hozzáférés-kezelés (IAM)*, által definiált a [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) csoportot "*a biztonsági állapotokban, amely lehetővé teszi a megfelelő személyek számára jobb oldalán a megfelelő erőforrásokhoz férnek hozzá a megfelelő okokból alkalommal*".

Gépházban, de mi a probléma? Ha IAM *nem kezelt* egy helyen, egy integrált megoldás részeként:

* Identitás rendszergazdák kell külön-külön létrehoznia, és külön-külön, frissítse az összes alkalmazás felhasználói fiókokat a redundancia és időigényes tevékenység.
* A felhasználóknak kell memorize több hitelesítő adatokat a munkához szükséges alkalmazásokhoz. Ennek eredményeképpen a felhasználók írja le a jelszavát, vagy használjon más egyéb adatok biztonsági kockázatokkal jelszó megoldások általában.
* Redundáns, sok időt vesz igénybe tevékenységek Rövidítse le a felhasználók és rendszergazdák dolgozik, így növelik a lényeg az üzleti üzleti tevékenységét.

Igen milyen általában megakadályozza, hogy a szervezetek integrált IAM-megoldások bevezetése?

* A műszaki megoldások telepítve, és minden egyes szervezet saját alkalmazásokhoz igazított igénylő szoftverplatformok alapulnak.
* A felhőalapú alkalmazásokhoz, mint az IT-szervezet integrálható a meglévő IAM-megoldások gyorsabb ütemben gyakran fogadnak el.
* Biztonsági és a figyelés tooling további testreszabási és az integráció átfogó E2E forgatókönyvek eléréséhez szükséges.

## <a name="azure-active-directory-integrated-with-applications"></a>Az Azure Active Directory integrált alkalmazások
Az Azure Active Directory a Microsoft átfogó szolgáltatott identitási (IDaaS) szolgáltatás megoldás, amely:

* Lehetővé teszi a felhő alapú szolgáltatásként IAM 
* Központi hozzáférési kezelhető, egyszeri bejelentkezést (SSO) és a jelentéskészítési 
* Támogatja az integrált kezelés [alkalmazások ezer](https://azure.microsoft.com/marketplace/active-directory/) az alkalmazás katalógusában, például a Salesforce, Google Apps, mezőben, Concur és egyéb. 

Az Azure Active Directoryval minden alkalmazás közzététele a partnerek és ügyfelek (üzleti vagy fogyasztó) ugyanazzal az identitással rendelkezik, és hozzáférés-kezelési képességeket.<br> Ez lehetővé teszi, hogy jelentősen csökkenti a működési költségek.

Mi történik, ha kell megvalósítani, amelyek még nem szerepel a alkalmazáskatalógusában az alkalmazás? Ez nem egy kicsit több időt vesz igénybe, mint az egyszeri bejelentkezés konfigurálása az alkalmazás-galériából alkalmazások, az Azure AD biztosít, amely segít a konfiguráció varázsló.

Az Azure AD értékének túllép "csak" felhőalapú alkalmazásokhoz. Is használhatja azt a helyszíni alkalmazások biztonságos távoli hozzáférést biztosító. A biztonságos távoli hozzáférés, megszüntetheti a VPN-EK vagy más hagyományos távelérési felügyeleti megvalósítások szükségességét.

Az alkalmazások központi kezelési és egyszeri bejelentkezés (SSO) megadásával az Azure AD a fő adatok biztonsági és a termelékenység problémák megoldást biztosít.

* Felhasználók férhetnek hozzá a bevétel több időt adjon előállítása vagy üzleti végzett műveletek tevékenységek egy bejelentkezési több alkalmazást.
* Identitás rendszergazdák kezelhetik az alkalmazások egyetlen helyen való hozzáférés.

A felhasználó és a vállalat előnye nyilvánvaló. Vegyük a következő előnyöket közelebbről is identitás rendszergazda és a szervezet.

## <a name="integrated-application-benefits"></a>Integrált alkalmazás előnyei
Az egyszeri bejelentkezési folyamat rendelkezik két lépésből áll:

* Hitelesítés, a folyamatot a felhasználó identitásának érvényesítése.
* Engedélyezési, alkalmazással való engedélyezése vagy letiltása a hozzáférési házirendek az erőforráshoz való hozzáférés.

Ha az Azure AD segítségével kezelheti az alkalmazásokat és az egyszeri bejelentkezés engedélyezése:

* Hitelesítés a felhasználó a helyszíni (pl. AD) vagy Azure AD-fiókot történik.
* Engedélyezési végrehajtja a Azure AD-hozzárendelés és a védelmi házirend egységes felhasználói élmény biztosítása, és így lehetővé teszi bármilyen alkalmazás, függetlenül annak belső funkciói a hozzárendelés, a helyek és a többtényezős hitelesítés feltételek hozzáadása.

Fontos megérteni, hogy az engedélyezési végrehajtása a célalkalmazás válaszából módja attól függően, hogy az alkalmazás az Azure ad-val lett integrálva változik.

* **Szolgáltató által előre integrált alkalmazások** például Office 365 és Azure, ezek a közvetlenül az Azure AD épül, és az átfogó identitás- és hozzáférés kezelésére alkalmas a tőle függő alkalmazások. Ezek az alkalmazások elérésére engedélyezve van a címtáradatokat és a hitelesítési karakterláncok kiállításához.
* **A Microsoft és az egyéni alkalmazások előre integrált alkalmazások** ezek a belső alkalmazásspecifikus címtár támaszkodnak, és az Azure AD függetlenül is működőképesek független felhőalapú alkalmazásokhoz. Ezek az alkalmazások elérésére engedélyezve van az alkalmazás hitelesítő adatok az alkalmazás fiókhoz hozzárendelt kiállításával. Attól függően, hogy az alkalmazások képességei a hitelesítő adatokat lehet egy összevonási token vagy a felhasználónév- és a jelszót, amely korábban az alkalmazás lett kiépítve.
* **A helyszíni alkalmazások** elsősorban a helyszíni alkalmazásokhoz való hozzáférés engedélyezése az Azure AD-alkalmazásproxy azzal közzétett alkalmazások. Ezek az alkalmazások egy központi helyszíni címtár, például a Windows Server Active Directory támaszkodnak. Ezek az alkalmazások elérésére képes biztosítani az alkalmazás tartalmát a végfelhasználó közben a helyi bejelentkezés követelmény érvényesítenie engedélyezve van a proxy futtatásától.

Például ha egy felhasználó csatlakozik a szervezet, szeretné az Azure AD bejelentkezés elsődleges műveletekre vonatkozó hozzon létre egy fiókot a felhasználóhoz. Ha a felhasználó a kezelt alkalmazás, például a Salesforce hozzáférésre van szüksége, is szeretné hozzon létre egy fiókot a felhasználóhoz a Salesforce-ban, és hivatkozás működik SSO végrehajtásához Azure-fiókjába. Amikor a felhasználó elhagyja a szervezetet, célszerű törölni az Azure AD-fiókot, és a IAM a fiókokhoz tartozó tárolja az alkalmazások a felhasználó hozzáfért.

## <a name="access-detection"></a>Hozzáférés észlelése
A modern vállalatok informatikai részlegek ismerik gyakran nem minden a felhőalapú alkalmazások vannak használatban lévő. A Cloud App Discovery együtt az Azure AD lehetőséget biztosít a megoldás az alkalmazások észlelésére.

## <a name="account-management"></a>Fiókkezelés
Hagyományosan a különböző alkalmazások kezelésére egy kézi művelet által végrehajtott informatikai vagy a szervezet támogatási személyzetnek. Az Azure AD fiókkezelés teljesen automatizálja minden szolgáltató integrált alkalmazások és az automatizált felhasználókiépítése vagy SAML fordítója kiosztása támogató Microsoft által előre integrált alkalmazások között.

## <a name="automated-user-provisioning"></a>Automatizált felhasználókiépítése
Egyes alkalmazások automation felületek létrehozására és Eltávolítás (vagy deaktiválása) fiókok adja meg. Ha egy szolgáltató ilyen interfész, az Azure ad elkészítéséhez használja. Ez csökkenti a működési költségek, mivel a felügyeleti feladatok automatikusan megtörténik, és javítja a biztonsági környezet, mivel csökkenti a jogosulatlan hozzáférés esélyét.

## <a name="access-management"></a>Hozzáférés-kezelés
Az Azure ad-vel kezelheti használó egyéni alkalmazások vagy alapú hozzárendelések szabály eléréséhez. Delegálhatja hozzáférés-kezeléshez a szervezet biztosítja a legjobb felügyeletet és a támogató személyzet terhelésének csökkentése a megfelelő személyeknek.

## <a name="on-premises-applications"></a>Helyszíni alkalmazások
A beépített alkalmazásban proxy lehetővé teszi a felhasználók számára, ami azt eredményezi, a helyszíni alkalmazások közzétételét élmény modern felhőalapú alkalmazására és a következő előnyöket is konzisztens hozzáférést az Azure AD-figyelési, jelentéskészítési és biztonsági képességeket.

## <a name="reporting-and-monitoring"></a>Jelentéskészítési és figyelés
Az Azure AD biztosít előre integrált jelentéskészítési és figyelési képességek, amelyek lehetővé teszik, hogy tudja, aki hozzáfér az alkalmazásokhoz, és ha azok ténylegesen használják őket.

## <a name="related-capabilities"></a>Kapcsolódó képességei
Az Azure ad-val biztonságossá teheti az alkalmazások részletes hozzáférési házirendek és előre integrált többtényezős Hitelesítést. További információt az Azure MFA lásd [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Első lépések
Első lépésként alkalmazások integrálása az Azure ad-vel, vessen egy pillantást a [integrálása Azure Active Directory és az alkalmazások első lépések útmutató](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Részletes telepítési tervét az egyszeri bejelentkezés az SaaS-alkalmazáshoz](http://aka.ms/ssodeploymentplan)

