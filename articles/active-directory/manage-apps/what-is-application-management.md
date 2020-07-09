---
title: Alkalmazások kezelése az Azure Active Directoryban | Microsoft Docs
description: Áttekintés a Azure Active Directory (AD) használatáról identitás-és hozzáférés-kezelési (IAM) rendszerként a Felhőbeli és a helyszíni alkalmazásokhoz.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aae292d97457ebe1d36a839b779b9233037ea60
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055205"
---
# <a name="what-is-application-management"></a>Mi az alkalmazáskezelés?

Az Azure AD egy identitás-és hozzáférés-kezelési (IAM) rendszer. Egyetlen helyet biztosít a digitális identitásokkal kapcsolatos információk tárolásához. A szoftveralkalmazások úgy konfigurálhatók, hogy az Azure AD-t használják a felhasználói adatok tárolásának helyeként. 

Az Azure AD-t úgy kell konfigurálni, hogy integrálható legyen az alkalmazással. Más szóval tudnia kell, hogy mely alkalmazások használják identitási rendszerként. Az Azure AD-t az alkalmazások felügyeletének és kezelésének folyamata is ismert.

Az alkalmazásokat a Azure Active Directory portál kezelés szakaszában található **vállalati alkalmazások** panelen kezelheti.

![A vállalati alkalmazások lehetőség az Azure AD-portál kezelés szakaszában.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Mi az identitás-és hozzáférés-kezelési (IAM) rendszer?
Az alkalmazás egy olyan szoftver, amely valamilyen célra használatos. A legtöbb alkalmazásnál a felhasználóknak be kell jelentkezniük ahhoz, hogy az alkalmazás testre szabott élményt nyújtson az adott felhasználó számára. Más szóval az alkalmazásnak ismernie kell a felhasználó identitását az alkalmazás használatával. Mivel tudja, milyen funkciókat ajánl fel vagy távolíthat el a felhasználó számára.

Ha minden alkalmazás külön nyomon követheti a felhasználókat, az eredmény az összes alkalmazáshoz tartozó különböző felhasználónevek és bejelentkezések silója lesz. Egy alkalmazás nem tud semmit a többi alkalmazás felhasználóival kapcsolatban.

A központosított identitásrendszer ezt a problémát úgy oldja meg, hogy egyetlen helyet biztosít a felhasználói adatok tárolására, amelyeket az összes alkalmazás használhat. Ezeket a rendszereket identitás-és hozzáférés-kezelési (IAM) rendszereknek nevezzük. Az Azure Active Directory a Microsoft Cloud-hoz készült IAM rendszer.

>[!TIP]
>A IAM rendszer egyetlen helyet biztosít a felhasználói identitások nyomon követéséhez. Az Azure AD a Microsoft Cloud-hoz készült IAM rendszer.


## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Minden nap új alkalmazásokat adnak hozzá, fejlesztenek és selejteznek le. A sok alkalmazással és hozzáférési ponttal minden eddiginél nagyobb jelentőséggel bír a felhőalapú megoldás használata az összes alkalmazás felhasználói hozzáférésének kezeléséhez.

>[!TIP]
>Az Azure AD App Gallery számos népszerű alkalmazást tartalmaz, amelyek már előre konfigurálva vannak az Azure AD-ben való együttműködéshez.

## <a name="how-does-azure-ad-work-with-applications"></a>Hogyan működik az Azure AD az alkalmazásokkal?

Az Azure AD leegyszerűsíti az alkalmazások kezelésének módját azáltal, hogy egyetlen identitási rendszert biztosít a Felhőbeli és a helyszíni alkalmazások számára. A szolgáltatott szoftverek (SaaS), helyszíni alkalmazások és üzletági (LOB) alkalmazások is hozzáadhatók az Azure AD-hez. Ezután a felhasználók egyszer bejelentkeznek az alkalmazások biztonságos és zökkenőmentes eléréséhez, valamint az Office 365 és a Microsoft egyéb üzleti alkalmazásaihoz. A [felhasználók üzembe helyezésének automatizálásával](../app-provisioning/user-provisioning.md)csökkentheti az adminisztrációs költségeket. A többtényezős hitelesítést és a feltételes hozzáférési szabályzatokat is használhatja a biztonságos alkalmazás-hozzáférés biztosításához.

![Az Azure AD-n keresztül összevont alkalmazásokat bemutató diagram](media/what-is-application-management/app-management-overview.png)

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Milyen típusú alkalmazásokat lehet integrálni az Azure AD-vel?

A **nagyvállalati alkalmazásokhoz** és az Azure ad-vel való felügyelethez négy fő típusú alkalmazás adható meg:

- **Azure ad Gallery-alkalmazások** – az Azure ad tartalmaz egy katalógust, amely az Azure ad-vel való egyszeri bejelentkezéshez előre integrált, több ezer alkalmazást tartalmaz. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. [Ismerje meg az alkalmazások integrálásának megtervezését](plan-an-application-integration.md), vagy a [SaaS-alkalmazás oktatóanyagokban](https://docs.microsoft.com/azure/active-directory/saas-apps/)részletes integrációs lépéseket kaphat az egyes alkalmazásokhoz.

- Helyszíni **alkalmazások alkalmazásproxy** használatával – az Azure ad Application proxy segítségével a helyszíni webalkalmazásokat az Azure ad-vel integrálva támogathatja az egyszeri bejelentkezést. Ezután a végfelhasználók ugyanúgy férhetnek hozzá a helyszíni webalkalmazásokhoz, mint az Office 365 és más SaaS-alkalmazások, lásd: [távoli hozzáférés biztosítása helyszíni alkalmazásokhoz az Azure ad alkalmazásproxy használatával](application-proxy.md).

- **Testreszabott alkalmazások** – a saját üzletági alkalmazásai kiépítésekor az egyszeri bejelentkezés támogatásához integrálhatja őket az Azure ad-vel. Az alkalmazás Azure AD-vel való regisztrálásával szabályozhatja az alkalmazás hitelesítési házirendjét. További információ: [útmutató a fejlesztőknek](developer-guidance-for-integrating-applications.md).

- **Galérián kívüli alkalmazások** – saját alkalmazások használata Az Azure AD-be való felvételsel más alkalmazásokhoz is támogathatja az egyszeri bejelentkezést. Az alkalmazások integrálásának többféle módja van, ezek némelyike alább látható. További információ: [az egyszeri bejelentkezés konfigurálása nem katalógusbeli alkalmazásokhoz](configure-single-sign-on-non-gallery-applications.md).

>[!TIP]
>Az Azure AD-t az alkalmazással is integrálhatja, még akkor is, ha az még nincs előre konfigurálva, és az alkalmazás-katalógusban. Az **Azure ad-t a következők bármelyikével integrálhatja**
> - Bármely Webhivatkozás vagy alkalmazás, amely a **Felhasználónév és a jelszó mezőt**jeleníti meg.
> - Bármely olyan alkalmazás, amely támogatja az **SAML vagy az OpenID Connect protokollokat**.
> - Minden olyan alkalmazás, amely támogatja a **tartományok közötti Identitáskezelés-felügyeleti (scim)** szabványt.

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatkezelés feltételes hozzáférési szabályzatokkal

A [feltételes hozzáféréssel](../conditional-access/concept-conditional-access-cloud-apps.md) rendelkező Azure ad egyszeri bejelentkezés (SSO) összekapcsolása magas szintű biztonságot nyújt az alkalmazások eléréséhez. A biztonsági képességek közé tartozik a felhőalapú Identitáskezelés, a kockázatalapú hozzáférés-vezérlés, a natív többtényezős hitelesítés és a feltételes hozzáférési szabályzatok. E funkciókkal részletesen szabályozható szabályzatok állíthatók be alkalmazások vagy magasabb szintű biztonságot igénylő csoportok alapján.

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel

Az egyszeri bejelentkezés (SSO) a bejelentkezési kérések számának csökkentésével vagy a kérések kiiktatásával kiváló bejelentkezési élményt biztosít a meglévő felhasználók számára a különböző alkalmazások és az Office 365 esetében. A felhasználó egységesebb környezetet érzékel és kevésbé zavarja a sok bejelentkezési kérés és a sokféle jelszó kezelésének szükségessége. Az üzleti csoport az önkiszolgálással és a dinamikus tagsággal kezelni és engedélyezni tudja a hozzáféréseket. Az alkalmazáshoz való hozzáférések kezelésének a vállalaton belül a megfelelő emberek számára történő lehetővé tétele javítja az azonosítási rendszer biztonságát.

Az egyszeri bejelentkezés javítja a biztonságot. Az *egyszeri bejelentkezés nélkül* a rendszergazdáknak minden egyes alkalmazáshoz külön kell felhasználói fiókokat létrehozni és frissíteni, ami időbe kerül. A felhasználóknak is többféle hitelesítő adatot kell nyomon követniük az alkalmazásaik eléréséhez. Ennek eredményeképpen a felhasználók hajlanak arra, hogy leírják a jelszavaikat vagy másféle jelszókezelési megoldást használjanak, ami adatbiztonsági kockázatot jelent. [További információ az egyszeri bejelentkezésről](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség

Az Azure AD segítségével az alkalmazás bejelentkezéseket a biztonsági információk és események kezelésének (SIEM) eszközeit felhasználó jelentésekkel követheti nyomon. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése

Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

Az emberi erőforrások célzott alkalmazásai, illetve a felhasználók nagy készletével rendelkező más alkalmazások esetében az alkalmazások kiépítésének automatizálásával automatizálható a felhasználók kiépítésének és megszüntetésének folyamata, lásd: [Mi az Application kiépítés?](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>További lépések

- [Az Azure AD-bérlőben már konfigurált alkalmazások megtekintése](view-applications-portal.md)
- [Gyors útmutató: gyűjteménybeli alkalmazás hozzáadása az Azure AD-bérlőhöz](add-application-portal.md)
- [Gallery-alkalmazás hozzáadása az Azure AD-szervezethez](add-gallery-app.md)
- [Ismerkedés az alkalmazások integrálásával](plan-an-application-integration.md)
- [Útmutató a kiépítés automatizálásához](../app-provisioning/user-provisioning.md)