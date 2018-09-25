---
title: Korhatáralapú hozzáférés Azure Active Directory B2C használatával |} A Microsoft Docs
description: Ismerje meg az alkalmazás használatával kiskorúak azonosítása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a9a86d445deaea4872615f443ad53f76638a758
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056522"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Koralapú Azure AD B2C-ben

>[!IMPORTANT]
>Ez a szolgáltatás private preview verzióban van.  Tekintse át a [szolgáltatási blogunkat](https://blogs.msdn.microsoft.com/azureadb2c/) részleteket, mivel ezt lesz elérhető, vagy forduljon AADB2CPreview@microsoft.com.  Ne használja ezt a termelési könyvtárak, használja ezeket a funkciókat az adatvesztést eredményezhet, és előfordulhat, hogy váratlan módosítások mindaddig, amíg meg is vagyunk általánosan működésében.  
>

##<a name="age-gating"></a>Korhatáralapú
Korhatáralapú lehetővé teszi, hogy az Azure AD B2C segítségével azonosíthatók a kiskorúak az alkalmazásban.  Kiválaszthatja az alkalmazásba a felhasználó letiltása, vagy hogy térjen vissza az alkalmazást a további jogcím, amelyek azonosítják a felhasználó korcsoport és azok szülői beleegyezési állapotát.  

>[!NOTE]
>Szülői beleegyezési követi nyomon egy felhasználó attribútum nevű `consentProvidedForMinor`.  Ez a tulajdonság a Graph API-n keresztül frissítheti és frissítésekor fog használni ezt a mezőt `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>A címtár korhatáralapú beállítása
Felhasználói folyamat korhatáralapú hozzáférés használatához konfigurálja a címtárban, hogy a további tulajdonságok kell. Ez a művelet végezhető el `Properties` a menü (amely csak akkor aktív, ha a privát előzetes verzió része lesz).  
1. Az Azure AD B2C-bővítményben, kattintson a a **tulajdonságok** a bal oldali menüben a bérlő számára.
2. Alatt a **korhatáralapú** területén kattintson a a **konfigurálása** gombra.
3. Várjon, amíg a művelet befejeződik, és a címtár korhatáralapú hoznak létre.

##<a name="enabling-age-gating-in-your-user-flow"></a>A felhasználói folyamat korhatáralapú hozzáférés engedélyezése
A címtár használata a korhatáralapú beállítása után az előzetes verzió felhasználói folyamatokban ezután használhatja ezt a szolgáltatást.  A szolgáltatás használatához, hogy nem kompatibilis a felhasználói folyamatok meglévő típusú módosításokat.  Engedélyezi a korhatáralapú az alábbi lépéseket követve:
1. Előzetes verzió felhasználói folyamat létrehozása.
2. Miután létrejött, nyissa meg **tulajdonságok** menüjében.
3. Az a **korhatáralapú** szakaszban, nyomja le a váltógombot a funkció engedélyezéséhez.
4. Kiválaszthatja, hogyan szeretné kezelni a felhasználókat, amelyek kiskorúak azonosításához.

##<a name="what-does-enabling-age-gating-do"></a>Mire korhatáralapú engedélyezése?
Korhatáralapú engedélyezését követően a felhasználói folyamat a felhasználói élmény a módosításokat.  Regisztráció, a felhasználók most már meg kell adnia a születési idő és a lakóhelye együtt a felhasználói attribútumok a felhasználói folyamat konfigurálva.  Bejelentkezési akik korábban még nem megadott születési dátum és ország tartózkodási meg kell adnia az adatok legközelebb bejelentkeznek.  Ezt a két értéket, az Azure AD B2C alapján megállapítható, hogy a felhasználó egy kisebb lesz, és frissítse a `ageGroup` mező, az érték lehet `null`, `Undefined`, `Minor`, `Adult`, és `NotAdult`.  A `ageGroup` és `consentProvidedForMinor` mezők majd kiszámításához használt `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Korhatáralapú hozzáférés-beállítások
Kiválaszthatja, hogy rendelkezik Azure AD B2C-vel blokk kiskorúak nélkül szülői beleegyezési vagy teszik lehetővé, és az alkalmazást, mi a teendő velük a döntéseket.  

###<a name="allowing-minors-without-parental-consent"></a>Kiskorú szülői beleegyezési nélkül engedélyezése
Felhasználói folyamatok, amelyek lehetővé teszik vagy jelentkezzen be, jelentkezzen be, vagy mindkét lehet váltani, az alkalmazásba hozzájárulás nélkül kiskorúak.  Kiskorú szülői beleegyezési nélkül, a jogosult a bejelentkezéshez, vagy iratkozzon fel, ahogy a normál és az Azure AD B2C-azonosító jogkivonat a problémák a `legalAgeGroupClassification` jogcím.  Használatával kiválaszthatja, hogy a felhasználói élményt, ezek a felhasználók ezt az igényt, például áthaladna szülői beleegyezési gyűjtéséhez felületet (és frissítheti a `consentProvidedForMinor` mezőben).

###<a name="blocking-minors-without-parental-consent"></a>Kiskorú szülői beleegyezési nélkül blokkolása
Felhasználói folyamatok, amelyek lehetővé teszik vagy jelentkezzen be, jelentkezzen be vagy mindkettőt beállíthatja blokkolása kiskorúak alkalmazásból beleegyezése nélkül.  Az Azure AD B2C-ben a letiltott felhasználók kezelésére két lehetőség van:
* A JSON küldése az alkalmazásnak – ezt a beállítást olyan választ küld az alkalmazásnak, hogy egy kisebb blokkolása.
* A felhasználó megjelenik egy oldal, amely tájékoztatja őket, hogy nem tudják elérni az alkalmazást - hibalap megjelenítése