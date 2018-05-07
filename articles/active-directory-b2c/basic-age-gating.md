---
title: Használja az Azure AD B2C átjáró kora |} Microsoft Docs
description: Ismerje meg az alkalmazást kiskorúak azonosítása.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: 3d6804f7e546547d734f966656362111b31078a4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Az Azure AD B2C átjáró kora használatával

>[!IMPORTANT]
>A funkció jelenleg private Preview verziójára.  Tekintse meg a [szolgáltatás blog](https://blogs.msdn.microsoft.com/azureadb2c/) részleteket, ez lesz a érhető el, vagy kérjen AADB2CFeedback@microsoft.com.  Ne használja ezt a termelési könyvtárak, ezeket az új funkciók használatához adatvesztést eredményezhet, és előfordulhat, hogy rendelkezik viselkedését, amíg megnyitjuk általánosan rendelkezésre álló váratlan változását.  
>

##<a name="age-gating"></a>Az átjáró kora
Kor átjáró lehetővé teszi az alkalmazás kiskorúak azonosítása az Azure AD B2C segítségével.  Ha szeretné, a felhasználó nem jelentkezik be az alkalmazást, vagy hogy lépjen vissza, amely azonosítja a felhasználócsoport kor és beleegyezést állapotuk további JOGCÍMEKKEL rendelkező és az alkalmazáshoz.  

>[!NOTE]
>Szülői jóváhagyása követi nyomon a felhasználói attribútum nevű `consentProvidedForMinor`.  Ez a tulajdonság, a Graph API-n keresztül frissítheti és való frissítése során fogja használni ezt a mezőt `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>A könyvtár a kor átjáró beállítása
Ahhoz, hogy egy felhasználó folyamatában átjáró kor, kell konfigurálni a címtárban, így további tulajdonságát. Ez a művelet végezhető el `Properties` a menü (amely csak akkor, ha Ön a private Preview verziójára része lesz).  
1. Az Azure AD B2C-bővítményben, kattintson a a **tulajdonságok** a bérlője a bal oldali menüben.
2. Az a **kora átjáró** területen kattintson a a **konfigurálása** gombra.
3. Várjon, amíg a művelet elvégzéséhez, és a címtárban a kor átjáró hoznak létre.

##<a name="enabling-age-gating-in-your-user-flow"></a>A felhasználó folyamatában átjáró kora engedélyezése
A címtár használandó kora átjáró beállítása után a az előzetes verzió felhasználói forgalomban használhatja ezt a szolgáltatást.  Ehhez a szolgáltatáshoz, melyek nem kompatibilis a meglévő típusú felhasználói adatfolyamok módosításokat.  Engedélyezi a következő lépések átjáró kor:
1. Hozzon létre egy előzetes felhasználói folyamat.
2. Miután létrejött, Ugrás **tulajdonságok** a menüben.
3. Az a **kora átjáró** szakaszban, nyomja meg a váltógomb a funkció engedélyezéséhez.
4. Ezután kiválaszthatja, hogyan szeretné kezelni olyan felhasználót, kiskorúak azonosítani.

##<a name="what-does-enabling-age-gating-do"></a>Mire engedélyezése kora átjáró?
Kor átjáró engedélyezése után a felhasználó folyamatában az a felhasználó számára a módosításokat.  Regisztráció, a felhasználók most már a születési és a felhasználói attribútumok a felhasználói folyamat konfigurált együtt lakóhelye kell adnia.  Jelentkezzen be, a felhasználók, amelyek még nem korábban megadott születési dátum és a tartózkodási ország kér a rendszer ezt az információt a legközelebb bejelentkeznek az.  E két érték közül az Azure AD B2C alapján megállapítható, hogy a felhasználó egy kisebb lesz, és frissítse a `ageGroup` mezőben, az érték lehet `null`, `Undefined`, `Minor`, `Adult`, és `NotAdult`.  A `ageGroup` és `consentProvidedForMinor` mezők majd kiszámításához használt `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>A korszűrő átjáró beállítások
Ha szeretné az Azure AD B2C kell blokk kiskorúak szülői jóváhagyása nélkül, vagy lehetővé teszik, és mi a teendő velük a döntések az alkalmazást.  

###<a name="allowing-minors-without-parental-consent"></a>Így kiskorúak szülői jóváhagyása nélkül
Amely rendelkezik vagy aláírási, bejelentkezés felhasználói forgalom vagy mindkettő kiválaszthatja az alkalmazásba hozzájárulás nélkül kiskorúak engedélyezéséhez.  A kiskorúak szülői jóváhagyása nélkül, azok hogy jogosult-e jelentkezni, vagy jelentkezzen be a normál, ingyenesen kibocsát egy azonosító jogkivonat a `legalAgeGroupClassification` jogcímek.  Ezt az igényt dönthet úgy, hogy ezek a felhasználók jogosultak a élmény használatával, mint áthaladás beleegyezést gyűjtéséhez élményt (és frissítse a `consentProvidedForMinor` mező).

###<a name="blocking-minors-without-parental-consent"></a>Blokkoló kiskorúak szülői jóváhagyása nélkül
Amely rendelkezik vagy aláírási, bejelentkezés felhasználói forgalom vagy mindkettő kiválaszthatja az alkalmazásba hozzájárulás nélkül kiskorúak blokkolására.  Az Azure AD B2C letiltott felhasználók kezelésére két lehetőség áll rendelkezésre:
* A JSON küldi vissza az alkalmazás - ezt a beállítást olyan választ küld az alkalmazásnak, hogy egy kisebb hozzáférése le van tiltva.
* A felhasználó megjelenik egy lap tájékoztatja, hogy nem érhető el az alkalmazást egy hibalap - megjelenítése

##<a name="known-issues"></a>Ismert problémák
###<a name="customization-unavailable-for-new-pages"></a>Nem érhető el az új lapok testreszabása
Nincsenek két új lapokat, amely elérhető a felhasználói folyamat kora átjáró engedélyezésekor.  Ezeken a lapokon az ország és születési gyűjtéséhez bejelentkezhet, és a hibalap nem használható oldal elrendezést vagy nyelvi testreszabása.  Ez a beállítás el a következő frissítés érhető el.

###<a name="format-for-the-response-when-a-minor-is-blocked"></a>A válasz egy kisebb blokkolásáról formátumban.
A válasz jelenleg formátuma, egy következő frissítés kiiktatása ezt a hibát.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>A telepítés során felvett adott attribútumok törlése teheti a címtárban nem használható kora átjáró.
Kor átjáró beállításaiban, a directory-ban egy beállítást konfigurálta a `Properties`.  Ha törli vagy `legalCountry` vagy `dateOfBirth`, a bérlő többé ne használhassa kora átjáró, és ezeket a tulajdonságokat nem lehet újból létre kell hozni.

###<a name="list-of-countries-is-incomplete"></a>Azon országok listája nem fejeződött be
Jelenleg legalCountry országok listája nem fejeződött be, a többi országok adjuk hozzá a következő frissítés.