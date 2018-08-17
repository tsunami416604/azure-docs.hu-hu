---
title: Az Azure-identitáskezelés alapjai | Microsoft Docs
description: A céges alkalmazásokhoz és adatokhoz való felhasználói hozzáférés felügyeletére és átlátására jelenleg a felhőalapú identitások kínálják a leghatékonyabb módszert.
keywords: ''
author: eross-msft
manager: mtillman
ms.reviewer: jsnow
ms.author: lizross
ms.date: 08/07/2018
ms.topic: overview
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 797c35bad03c063203e3616740af633b71835a9c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39640216"
---
# <a name="fundamentals-of-azure-identity-management"></a>Az Azure-identitáskezelés alapjai

Ahogy egyre több digitális céges erőforrás kerül a vállalati hálózaton kívülre (a felhőbe vagy egyes eszközökre), úgy válik elengedhetetlenné egy jól működő felhőalapú identitás- és hozzáférés-kezelési megoldás üzembe helyezése. A céges alkalmazásokhoz és adatokhoz való felhasználói hozzáférés felügyeletére és átlátására jelenleg a felhőalapú identitások kínálják a leghatékonyabb módszert.

A Microsoft immár több mint egy évtizede biztosít védelmet a felhőalapú identitásoknak, és az [Azure Active Directory (AD)](active-directory-whatis.md) révén mostantól ugyanezek a védelmi rendszerek az Ön számára is elérhetővé válnak. Az Azure AD segítségével a vállalati rendszergazdák egyszerűen, minden eddiginél fejlettebb biztonsági és szabályozási megoldásokkal biztosíthatják a felhasználók és a rendszergazdák felelősségre vonhatóságát.

Az Azure AD Premium egy olyan, fejlett védelmi funkciókkal ellátott, felhőalapú identitás- és hozzáférés-kezelési megoldás, amellyel egyetlen biztonságos identitás állítható be az összes alkalmazáshoz, engedélyezhető a ([Microsoft intelligens biztonsági gráffal](https://www.microsoft.com/en-us/security/intelligence) kibővített) identitásvédelem, valamint a Privileged Identity Management. Az Azure AD Premium nem csupán egy újabb monitorozási vagy jelentéskészítési eszköz, de valós idejű védelmet is biztosít a felhasználói identitásoknak, valamint lehetővé teszi kockázatalapú, adaptív hozzáférési szabályzatok létrehozását a céges adatok védelme érdekében.

Az alábbi rövid videó az Azure AD identitáskezelési és -védelmi funkcióiról nyújt gyors áttekintést:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

A Microsoft nem csupán egy hordozható identitást biztosít, hanem egy olyan eszközkészletet is, amellyel automatizálható, biztonságossá tehető és kezelhető a cég vagy intézmény informatikai rendszere. Még a felhőalapú számítógép-használat elterjedését követően is egyre növekvő igény mutatkozik olyan informatikai feladatok kezelése és felügyelete iránt, amilyenek például az új felhasználói jelszavakat kérő ügyfélszolgálati hívások, a felhasználóicsoport-kezelés vagy az alkalmazáskérelmek. Tovább bonyolítja a dolgokat, hogy az alkalmazottak manapság már a munkahelyükre is behozzák személyes eszközeiket, és használatra kész SaaS-alkalmazásokat használnak – ezen alkalmazások vállalati adatközpontokat és nyilvános felhőplatformokat átfogó felügyeletének biztosítása pedig komoly kihívást jelent.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>A helyszíni Active Directory csatlakoztatása az Azure AD-hez és az Office 365-höz
Azon cégek, amelyek jelentős összegeket fektettek be a helyszíni Active Directory kiépítésébe, mostantól a felhőre is kiterjeszthetik azt a helyszíni címtárak és az Azure AD integrálásával, [hibrid identitáskezelés](https://aka.ms/aadframework) keretében. A helytől független, közös erőforrás-hozzáférési identitás létrehozásával a felhasználók munkája hatékonyabbá válik. A felhasználók és a cégek ettől kezdve egyszeri bejelentkezéssel férhetnek hozzá a helyszíni erőforrásokhoz és a felhőszolgáltatásokhoz (például az Office 365-höz) is.

Az integrációhoz csak az [Azure AD Connectre](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) van szükség. Az Azure AD Connect az identitásszinkronizálási igényeket támogató képességeket biztosít, valamint olyan identitásintegrációs eszközök régebbi verzióit váltja fel, mint például a DirSync és az Azure AD Sync. Az Azure AD Connect használatával az identitáskezelés, illetve a helyszíni erőforrások és az Azure AD közötti szinkronizálás a következőkön keresztül valósul meg:

- Szinkronizálás – ez az összetevő a felhasználók, csoportok és egyéb objektumok létrehozásáért felelős. Segítségével arról is meggyőződhet, hogy a helyszíni felhasználókhoz és csoportokhoz tartozó identitásadatok megegyeznek a felhőben található hasonló adatokkal. Bekapcsolható a jelszóvisszaírás is, amely biztosítja a helyszíni címtárak szinkronizálását, ha egy felhasználó frissíti Azure AD-jelszavát.
- Hitelesítés – Ha az Azure AD-t tekintjük az új vezérlő elemnek, akkor a hitelesítés lesz a felhőalapú hozzáférés alapja. A megfelelő hitelesítési módszer kiválasztása kulcsfontosságú egy hibrid Azure AD-identitáskezelési megoldás kiépítése során. [Ebben az útmutatóban](https://aka.ms/auth-options) talál információkat annak eldöntéséhez, hogy a felhőalapú hitelesítést (jelszókivonat-szinkronizálás/átmenő hitelesítés) vagy az összevont céges hitelesítést válassza.
- Állapotfigyelés – Az [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) hatékony monitorozási képességgel rendelkezik, valamint egy központi helyet biztosít az Azure Portalon az ilyen tevékenységek megtekintéséhez.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>A hatékonyság növelése és az ügyfélszolgálati költségek csökkentése önkiszolgáló műveletekkel és egyszeri bejelentkezéssel

Az alkalmazottak hatékonyabban tudnak dolgozni, ha csak egy egyetlen felhasználónevet és jelszót kell megjegyezniük, amelyet minden eszközön egyformán használhatnak. Azzal is időt takaríthatnak meg, ha olyan önkiszolgáló feladatokat hajthatnak végre, mint például az [új jelszó kérése elfelejtett jelszó esetén](https://docs.microsoft.com/azure/active-directory/active-directory-passwords), vagy egy alkalmazáshoz való hozzáférés kérése az ügyfélszolgálat közreműködése nélkül.

Az Azure AD [kiterjeszti a felhőbe a helyszíni Active Directoryt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect), lehetővé téve a felhasználók számára, hogy elsődleges céges fiókjukat a munkájuk elvégzéséhez szükséges, tartományhoz csatlakoztatott eszközökhöz, vállalati erőforrásokhoz, valamint az összes web- és SaaS-alkalmazáshoz egyaránt használhassák. Amellett, hogy a felhasználóknak nem kell többféle felhasználónév-jelszó kombinációt fejben tartaniuk, a felhasználói alkalmazás-hozzáférés megadása (vagy megszüntetése) is automatizálható a céges csoporttagság és az alkalmazotti státusz alapján. A katalógusban szereplő alkalmazások, illetve az [Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started) használatával kifejlesztett és közzétett saját helyszíni alkalmazások hozzáférése is szabályozható.

## <a name="manage-and-control-access-to-corporate-resources"></a>A vállalati erőforrásokhoz való hozzáférés kezelése és szabályozása
A Microsoft identitás- és hozzáférés-kezelési megoldásai segítséget nyújtanak az informatikai szakemberek számára az alkalmazások és erőforrások hozzáférés-védelmének kiépítésében a vállalati adatközpontokban és a felhőben, olyan ellenőrzési szintek közbeiktatásával, mint például a [többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) vagy a [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). A gyanús tevékenységek monitorozása speciális biztonsági jelentések kiadásán, naplózáson és riasztásokon keresztül valósul meg, így csökkenthető a potenciális biztonsági problémák kockázata.

Az Azure AD Premium feltételes hozzáférési szabályzatai lehetővé teszik a vállalati rendszergazdák számára, hogy szabályzatalapú hozzáférési szabályokat hozhassanak létre bármely Azure AD-hoz csatlakoztatott alkalmazáshoz (SaaS-alkalmazásokhoz, felhőben futtatott egyéni alkalmazásokhoz vagy helyszíni webalkalmazásokhoz). Az Azure AD valós időben értékeli ki ezeket a szabályzatokat és kényszeríti az alkalmazásukat, ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz. Az Azure identitásvédelmi szabályzataival egy gyanús tevékenység észlelésekor bizonyos műveletek automatikusan végrehajthatók. Ilyen művelet lehet például a magas kockázati besorolású felhasználók hozzáférésének blokkolása, a többtényezős hitelesítés kikényszerítése, illetve a felhasználói jelszavak visszaállítása, ha úgy gondolja, hogy sérült a hitelesítő adatok biztonsága.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

A prémium szintű P2 Azure Active Directory új alkalmazása, a [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), lehetővé teszi a rendszergazdai fiókok és azok erőforrás-hozzáférésének felderítését, korlátozását és monitorozását az Azure Active Directoryban és egyéb online Microsoft-szolgáltatásokban. Az alkalmazás ráadásul abban is segítséget nyújt, hogy az igény szerinti rendszergazdai hozzáférést pontosan a szükséges időtartam alatt felügyelhesse.

A Privileged Identity Managementtel kikényszeríthetők az igény szerinti rendszergazdai jogosultságok, így a rendszergazdák egy előre meghatározott időtartamra kérelmezhetnek többtényezős hitelesítésű, ideiglenes emelt szintű jogosultságokat. Az időtartam lejártával ezek a fiókok újra a normál felhasználói állapotnak megfelelő jogosultságokkal fognak rendelkezni.

## <a name="benefits-of-azure-identity"></a>Az Azure-identitás előnyei

Az Azure-identitáskezelés segítségével:

-   A vállalat minden egyes felhasználójához létrehozhat és kezelhet egy-egy identitást, így a felhasználók, csoportok és eszközök folyamatosan szinkronizálva lesznek az [Azure Active Directory Connecttel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Egyszeri bejelentkezéses hozzáférést biztosíthat alkalmazásaihoz (például több ezer előre integrált SaaS-alkalmazáshoz), illetve biztonságos távelérést biztosíthat helyszíni SaaS-alkalmazásokhoz az [Azure AD-alkalmazásproxyval](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Szabályalapú [többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) kikényszerítésével fokozhatja az alkalmazás-hozzáférés biztonságát a helyszíni és a felhőalkalmazásokban.

-   [Új jelszó önkiszolgáló kérésével](https://docs.microsoft.com/azure/active-directory/active-directory-passwords), valamint a [MyApps portálról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help) indított csoport- és alkalmazás-hozzáférési kérelmekkel fokozhatja a felhasználói termelékenységet.

-   Kihasználhatja az egész világra kiterjedő, nagyvállalati szintű, felhőalapú identitás- és hozzáférés-kezelési megoldás [magas rendelkezésre állásából és megbízhatóságából](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) fakadó előnyöket.

## <a name="next-steps"></a>További lépések
[További tudnivalók az Azure-identitáskezelési megoldásokról](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)
