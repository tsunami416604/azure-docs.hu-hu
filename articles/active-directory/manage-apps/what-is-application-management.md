---
title: Alkalmazások kezelése az Azure Active Directoryban | Microsoft Docs
description: Ez a cikk az Azure Active Directory helyszíni, felhőbeli és a SaaS-alkalmazásokba történő integrálásának előnyeit mutatja be.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: barbkess
ms.reviewer: arvinh
ms.openlocfilehash: e9cbc29950b1e5363691d44aa6e34391c541762b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155902"
---
# <a name="application-management-with-azure-active-directory"></a>Alkalmazáskezelés az Azure Active Directory használatával

Az Azure Active Directory (Azure AD) biztonságos és zökkenőmentes elérést biztosít a felhőbeli és helyszíni alkalmazásokhoz. A felhasználók egyszeri bejelentkezéssel elérhetik az Office 365-öt és az egyéb üzleti Microsoft-, SaaS-, helyszíni és üzletági alkalmazásokat. A felhasználó létrehozás automatizálásával csökkenti az adminisztratív költségeket. Használja a többtényezős hitelesítést és a feltételes hozzáférési szabályzatokat a biztonságos alkalmazás hozzáférés biztosításához.

![Az Azure AD-n keresztül összevont alkalmazások](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Minden nap új alkalmazásokat adnak hozzá, fejlesztenek és selejteznek le. Ilyen sok alkalmazás és hozzáférési pont esetén minden eddiginél fontosabb a felhőalapú megoldás használata az összes alkalmazás felhasználói hozzáférésének kezelésére.

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatkezelés feltételes hozzáférési szabályzatokkal
A feltételes hozzáférési szabályzatok és az Azure AD egyszeri bejelentkezés (SSO) összekapcsolása az alkalmazások eléréséhez magas szintű biztonságot szavatol. A biztonsági funkciók közé tartozik a felhőméretű identitásvédelem, a kockázat-alapú hozzáférés-vezérlés, a natív többtényezős hitelesítés és a feltételes hozzáférési szabályzatok. E funkciókkal részletesen szabályozható szabályzatok állíthatók be alkalmazások vagy magasabb szintű biztonságot igénylő csoportok alapján.

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel
Az egyszeri bejelentkezés (SSO) a bejelentkezési kérések számának csökkentésével vagy a kérések kiiktatásával kiváló bejelentkezési élményt biztosít a meglévő felhasználók számára a különböző alkalmazások és az Office 365 esetében. A felhasználó egységesebb környezetet érzékel és kevésbé zavarja a sok bejelentkezési kérés és a sokféle jelszó kezelésének szükségessége. Az üzleti csoport az önkiszolgálással és a dinamikus tagsággal kezelni és engedélyezni tudja a hozzáféréseket. Az alkalmazáshoz való hozzáférések kezelésének a vállalaton belül a megfelelő emberek számára történő lehetővé tétele javítja az azonosítási rendszer biztonságát.

Az egyszeri bejelentkezés javítja a biztonságot. Az *egyszeri bejelentkezés nélkül* a rendszergazdáknak minden egyes alkalmazáshoz külön kell felhasználói fiókokat létrehozni és frissíteni, ami időbe kerül. A felhasználóknak is többféle hitelesítő adatot kell nyomon követniük az alkalmazásaik eléréséhez. Ennek eredményeképpen a felhasználók hajlanak arra, hogy leírják a jelszavaikat vagy másféle jelszókezelési megoldást használjanak, ami adatbiztonsági kockázatot jelent. 

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség
Az Azure AD segítségével az alkalmazás bejelentkezéseket a biztonsági információk és események kezelésének (SIEM) eszközeit felhasználó jelentésekkel követheti nyomon. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése
Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

