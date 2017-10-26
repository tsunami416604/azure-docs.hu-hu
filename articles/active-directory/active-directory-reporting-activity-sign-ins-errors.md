---
title: "Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai az Azure Active Directory portálon | Microsoft Docs"
description: "Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjainak referenciája."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: dcdd8b5830edb542cb99d07f1b0087629d374264
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai az Azure Active Directory portálon

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

- Ki jelentkezett be az Azure Active Directory használatával?
- Melyik alkalmazásba jelentkeztek be?
- Melyik bejelentkezés volt sikertelen, és miért?

Ez a témakör tartalmazza a hibakódokat és a kapcsolódó leírásaikat. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hogyan tudom megjeleníteni a sikertelen bejelentkezéseket? 

A **[Bejelentkezések](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** menüponton át vezet az út a bejelentkezési tevékenység adataihoz, a menüpont az **Azure Active** **Tevékenység** szakaszában található.


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Sign-in activity")


A bejelentkezési jelentésben megjelenítheti az összes sikertelen bejelentkezést. Ehhez válassza a **Sikertelen** elemet a **Bejelentkezési állapot** mezőben.


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Sign-in activity")

Ha egy elemre kattint a megjelenő listában, megnyílik a **Tevékenység részletei: Bejelentkezések** panel. Ez a nézet tartalmazza a bejelentkezések összes olyan részletét, amelyet az Azure Active Directory nyomon követ, beleértve a **bejelentkezési hibakódot** és a **sikertelenség okát** is.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Sign-in activity")


A bejelentkezési adatok Azure Portallal való elérésének alternatívájaként a [jelentéskészítő API-t](active-directory-reporting-api-getting-started-azure-portal.md) is használhatja.


A következő szakasz az összes lehetséges hiba és a kapcsolódó leírások teljes áttekintését tartalmazza. 

## <a name="error-codes"></a>Hibakódok

| Hiba| Leírás |
| --- | --- |
| 50001| Az X nevű egyszerű szolgáltatás nem található az Y nevű bérlőben. Ez akkor történhet, ha az alkalmazást nem a bérlő rendszergazdája telepítette. Vagy az egyszerű erőforrás nem található a címtárban, vagy érvénytelen|
| 50008| Hiányzik a SAML helyességi feltétel, vagy rosszul van konfigurálva a jogkivonatban.|
| 50011| A válaszcím hiányzik, rosszul van konfigurálva, vagy nem egyezik az alkalmazáshoz konfigurált válaszcímekkel.|
| 50053| A fiók zárolva van, mert a felhasználó túl sokszor próbált meg bejelentkezni helytelen felhasználói azonosítóval vagy jelszóval.|
| 50054| Régi jelszót használt a hitelesítéshez.|
| 50055| Érvénytelen jelszó, lejárt jelszót írt be.|
| 50057| A felhasználói fiók le van tiltva.|
| 50058| Nem található információ a felhasználó identitásáról a megadott hitelesítő adatokban vagy a felhasználó nem található a bérlőben vagy csendes bejelentkezési kérést küldtek, de nem jelentkezett be felhasználó vagy a szolgáltatás nem tudta hitelesíteni a felhasználót.|
| 50074| Erős (kéttényezős) hitelesítésre van szükség|
| 50079| A felhasználónak kéttényezős hitelesítést kell igényelnie|
| 50126| Érvénytelen felhasználónév vagy jelszó, vagy érvénytelen helyszíni felhasználónév vagy jelszó.|
| 50131| Különböző feltételes hozzáférési hibákban használatos. Például rossz Windows-eszközállapot, a kérés gyanús tevékenység miatt blokkolva, a hozzáférési szabályzattal és a biztonsági szabályzattal kapcsolatos döntések.|
| 50133| A jelszó lejárta vagy nemrégi módosítása miatt a munkamenet érvénytelen.|
| 50144| A felhasználó Active Directory jelszava lejárt.|
| 65001| Az X alkalmazás nem rendelkezik engedéllyel az Y alkalmazás eléréséhez, vagy az engedély vissza lett vonva. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy engedélyezési kérést a bérlő rendszergazdájának, hogy az Y alkalmazás nevében végezzen tevékenységet a Z erőforráson.|
| 65005| Az alkalmazás által igényelt erőforrás-hozzáférési lista nem tartalmaz az erőforrás által felderíthető alkalmazásokat, vagy az ügyfélalkalmazás olyan erőforrás hozzáférését kérte, amely nincs meghatározva a szükséges erőforrás-hozzáférési listán, vagy a Graph szolgáltatás rossz kérést adott vissza, vagy az erőforrás nem található.|
| 70001| Az X nevű alkalmazás nem található az Y nevű bérlőben. Ez akkor történhet, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Elképzelhető, hogy rossz bérlőhöz küldte a hitelesítési kérést.|
| 80001| Nem érhető el hitelesítési ügynök.|
| 80002| A hitelesítési ügynök jelszó-érvényesítési kérése túllépte az időkorlátot.|
| 80003| A hitelesítési ügynök érvénytelen választ kapott.|
| 80004| A bejelentkezési kérésben helytelen egyszerű felhasználónevet (UPN-t) használtak.|
| 80005| Hitelesítési ügynök: hiba történt.|
| 80007| A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz.|
| 80010| A hitelesítési ügynök nem tudta visszafejteni a jelszót.|
| 81001| A felhasználó Kerberos-jegye túl nagy.|
| 81002| Nem érvényesíthető a felhasználó Kerberos-jegye.|
| 81003| Nem érvényesíthető a felhasználó Kerberos-jegye.|
| 81004| A Kerberos-hitelesítési kísérlet meghiúsult.|
| 81008| Nem érvényesíthető a felhasználó Kerberos-jegye.|
| 81009| Nem érvényesíthető a felhasználó Kerberos-jegye.|
| 81010| A zavartalan egyszeri bejelentkezés meghiúsult, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen.|
| 81011| Nem található a felhasználói objektum a felhasználó Kerberos-jegyének információi alapján.|
| 81012| Az Azure AD-ba bejelentkezni próbáló felhasználó különbözik az eszközbe jelentkezett felhasználótól.|
| 81013| Nem található a felhasználói objektum a felhasználó Kerberos-jegyének információi alapján.|
| 90014| Különböző esetekben használatos, amikor egy várt mező nincs jelen a hitelesítő adatokban.|
| 90093| A kéréshez a rendszer tiltott hibakódú gráfot adott vissza.|



## <a name="next-steps"></a>Következő lépések

További részletekért lásd: [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](active-directory-reporting-activity-sign-ins.md).

