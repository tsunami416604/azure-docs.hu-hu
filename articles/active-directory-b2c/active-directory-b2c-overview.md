---
title: Mi az az Azure Active Directory B2C? | Microsoft Docs
description: Ismerje meg, hogy hozhatja létre és kezelheti az alkalmazásai bejelentkezési folyamatait az Azure Active Directory B2C segítségével.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b667934fefe0b9afb5aeca786839f5e105be2c4d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844993"
---
# <a name="what-is-azure-active-directory-b2c"></a>Mi az az Azure Active Directory B2C?

Az Azure Active Directory (Azure AD) B2C egy identitáskezelő szolgáltatás, amellyel testreszabhatja és felügyelheti, hogy az ügyfelek hogyan használják az alkalmazását. Ez magában foglalja a regisztrációt, a bejelentkezést és a profilok kezelését, amikor az ügyfelek az alkalmazásait használják. Választhat iOS, Android, .NET és más rendszerekre fejlesztett alkalmazásokat is. Az Azure AD B2C az ügyfélidentitások védelme mellett teszi lehetővé e műveletek végrehajtását.

Az Azure AD B2C-ben regisztrált alkalmazásokat számos identitáskezelési feladat elvégzésére konfigurálhatja. Néhány példa:

- Engedélyezheti, hogy az ügyfelek regisztráljanak az Ön által regisztrált alkalmazás használatához
- Engedélyezheti, hogy a regisztrált ügyfelek bejelentkezzenek és megkezdjék az alkalmazás használatát
- Engedélyezheti, hogy a regisztrált ügyfelek szerkeszthessék a profiljukat
- Engedélyezheti a többtényezős hitelesítést az alkalmazásában
- Engedélyezheti ügyfeleinek, hogy egy adott identitásszolgáltató segítségével regisztráljanak és jelentkezzenek be
- Hozzáférést biztosíthat az alkalmazásából az Ön által létrehozott API-khoz
- Testre szabhatja a regisztráció és a bejelentkezés folyamatának megjelenését és működését
- Kezelheti az alkalmazás egyszeri bejelentkezési munkameneteit

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Mit kell átgondolnom az Azure AD B2C használata előtt?

- Hogyan használják az alkalmazásomat az ügyfelek?
- Milyen felhasználói felületet (UI) szeretnék nyújtani az ügyfeleknek?
- Mely identitásszolgáltatók közül választhassanak az ügyfelek az alkalmazásomban?
- Szükség van további API-kra a bejelentkezési folyamat futtatásához?

### <a name="customer-interaction"></a>Interakció az ügyfelekkel

Az Azure AD B2C támogatja az [OpenID Connect](https://openid.net/connect/) használatát minden ügyféloldali kezelőfelület esetében. Az OpenID Connect Azure AD B2C-implementációjában az alkalmazás az Azure AD B2C felé irányuló hitelesítési kérések küldésével indítja el a felhasználói utat. A kérés eredménye egy `id_token`. Ez a biztonsági jogkivonat határozza meg az ügyfél identitását.

Az Azure AD B2C-t használó alkalmazásokat regisztrálni kell egy Azure AD B2C-bérlőben az Azure Portal használatával. Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz. Ezen értékek között van egy alkalmazásazonosító, amely egyedileg azonosítja az alkalmazást. Meg van határozva egy átirányítási URI is, amely az alkalmazásnak küldött közvetlen válaszokhoz használható.

Az alkalmazások közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás az ügyfeleket egy szabályzat futtatására utasítja.
2. Az ügyfelek a szabályzat definíciója szerint teljesítik a szabályzat feltételeit.
3. Az alkalmazás kap egy biztonsági jogkivonatot.
4. Az alkalmazás a biztonsági jogkivonat segítségével megkísérli egy védett erőforrás elérését.
5. Az erőforrás-kiszolgáló ellenőrzi a biztonsági jogkivonatot, és megállapítja, hogy megadható-e hozzáférés.
6. Az alkalmazás rendszeres időközönként frissíti a biztonsági jogkivonatot.

Ezek a lépések a létrehozandó alkalmazás típusától függően némileg eltérőek lehetnek.

Az Azure AD B2C egymás után kommunikál az identitásszolgáltatókkal, az ügyfelekkel, más rendszerekkel és a helyi könyvtárral az identitásfeladat elvégzéséhez. Ilyen például az ügyfelek bejelentkeztetése, egy új ügyfél regisztrálása vagy egy jelszó visszaállítása. A több résztvevős megbízhatósági kapcsolatok létrehozásának és a lépések elvégzésének alapjául szolgáló platform neve Identity Experience Framework (Identitás-kezelőfelületi keretrendszer). Ez a keretrendszer és szabályzat (más néven felhasználói út vagy Bizalmikeretrendszer-szabályzat) explicit módon határozza meg az aktorokat, a műveleteket, a protokollokat és az elvégzendő lépések sorrendjét.

Az Azure AD B2C megvédi az alkalmazásait a szolgáltatásmegtagadási és jelszóalapú támadások ellen. Az Azure AD B2C olyan észlelési és kockázatcsökkentési technikákat használ, mint a SYN cookie-k vagy sebesség- és kapcsolatkorlátozások, hogy megvédje az erőforrásokat a szolgáltatásmegtagadási támadásokkal szemben. A rendszer kockázatcsökkentést alkalmaz a találgatásos támadásokkal és a szótáralapú jelszótámadásokkal szemben is.

#### <a name="user-flows"></a>Felhasználói folyamatok

Minden egyes Azure AD B2C-nek küldött kérések adja meg a felhasználói folyamat, amely egy szabályzatot, amely szabályozza, hogy az alkalmazás hogyan használja az Azure AD B2C-vel viselkedését. Előre definiált felhasználókövetési adatai a leggyakrabban használt identitás feladatok, például a regisztráció, bejelentkezést és profilszerkesztést, érhetők el az Azure AD B2C-portálon.  Például egy regisztrációs felhasználói folyamata lehetővé teszi a viselkedés szabályozására, a következő beállítások konfigurálásához:

- Közösségi fiókok, amelyekkel az ügyfelek regisztrálnak az alkalmazásra
- Az ügyfelekről gyűjtött adatok, úgymint utónév vagy irányítószám
- Multi-Factor Authentication
- A regisztrációs oldalak megjelenése és működése
- Az alkalmazásnak visszaadott adatok

#### <a name="custom-policies"></a>Egyéni szabályzatok 

Az [egyéni szabályzatok](active-directory-b2c-overview-custom.md) olyan konfigurációs fájlok, amelyek meghatározzák az [Identity Experience Framework](trustframeworkpolicy.md) viselkedését saját Azure AD B2C-bérlőjében. Az egyéni szabályzatok némi módosítással számos feladatot végrehajthatnak. Az egyéni szabályzatot egy vagy több XML formátumú fájl alkotja, amelyek hierarchikus sorrendben hivatkoznak egymásra. 

Az Azure AD B2C-bérlőben különböző típusú egyéni szabályzatok használhatók szükség szerint, és ezeket újra fel lehet használni más alkalmazásokhoz is. Ez a rugalmasság lehetővé teszi az ügyfélidentitási folyamatok meghatározását és módosítását a kód minimális módosításával vagy akár annak módosítása nélkül. A szabályzatok használata egy speciális lekérdezési paraméternek a HTTP-hitelesítési kérésekhez való hozzáadásával történik.

Az egyéni szabályzatok az alábbi módokon használhatók a felhasználói utak vezérléséhez:

- Meghatározhatja az API-kkal való kommunikációt további adatok rögzítéséhez, az ügyfelek által megadott jogcímek ellenőrzéséhez vagy külső folyamatok aktiválásához.
- Megváltoztathatja a viselkedést az API-kból származó vagy például a *migrationStatus* könyvtárban lévő jogcímek alapján.
- Bármely munkafolyamat, amelyre a beépített szabályzatok nem terjednek ki. Például további adatokat gyűjthet a bejelentkezés során az ügyfelektől vagy ellenőrizheti egy erőforrás eléréséhez szükséges hitelesítéseket.

### <a name="identity-providers"></a>Identitásszolgáltatók

Az identitásszolgáltató olyan szolgáltatás, amely hitelesíti az ügyfélidentitásokat és kiadja a biztonsági jogkivonatokat. Az Azure AD B2C-ben számos identitásszolgáltatót konfigurálhat a bérlőjében, többek között [Microsoft-fiókot](active-directory-b2c-setup-msa-app.md), [Facebook-fiókot](active-directory-b2c-setup-fb-app.md) vagy [Amazon-fiókot](active-directory-b2c-setup-amzn-app.md). 

Az Azure AD B2C-bérlő identitásszolgáltatójának konfigurálásához rögzítse az alkalmazás- vagy ügyfél-azonosítót és az Ön által létrehozott identitásszolgáltató-alkalmazás jelszavát vagy titkos ügyfélkulcsát. Az alkalmazás konfigurálásához ezt az azonosítót és jelszót használja.

### <a name="user-interface-experience"></a>A felhasználói felületet

Az ügyfelek számára megjelenő HTML- és CSS-tartalmak nagy része vezérelhető. Az oldal Felhasználói felület testre szabása funkciójának használatával tudja testre szabni bármely szabályzat megjelenését és működését. Ezzel a testreszabási funkcióval lehet fenntartani az alkalmazás és az Azure AD B2C közötti márkabeli és vizuális egységességet.

Az Azure AD B2C az ügyfél böngészőjében futtatja a kódot az Eltérő eredetű erőforrások megosztása (CORS) nevű modern megközelítést használva. Először adjon meg egy URL-t egy testreszabott HTML-tartalommal bíró szabályzatban. Az Azure AD B2C egyesíti a felhasználói felület elemeit az URL-ről betöltött HTML-tartalommal, majd megjeleníti az oldalt az ügyfélnek.

Paramétereket lekérdezési sztringben küldhet az Azure AD B2C-nek. Ha átadja a paramétereket a HTML-végpontnak, az oldal tartalma dinamikusan változik. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Melyek az első lépések az Azure AD B2C használatba vételekor?

Az Azure AD B2C-ben a bérlő képviseli az Ön vállalatát, és egyben a felhasználók címtára is. Mindegyik Azure AD B2C-bérlő önálló, és elkülönül a többi Azure AD B2C-bérlőtől. A bérlő adatokat tartalmaz az alkalmazása használatára regisztrált ügyfelekről. Ilyen adatok a jelszavaik, a profiljuk adatai és az engedélyeik.

Az összes funkció engedélyezéséhez és a használati díjak kifizetéséhez kapcsolja össze az Azure-előfizetését az Azure AD B2C-bérlőjével. Ha lehetővé szeretné tenni az ügyfelei számára az alkalmazásába való bejelentkezést, regisztrálja azt egy Azure AD B2C-bérlőben.

Mielőtt konfigurálná az alkalmazását az Azure AD B2C használatához, először létre kell hoznia egy Azure AD B2C-bérlőt és regisztrálnia kell az alkalmazását. Az alkalmazás regisztrálásához hajtsa végre a lépéseit [oktatóanyag: Regisztráció és bejelentkezés az Azure AD B2C használatával engedélyezése egy alkalmazás regisztrálása](tutorial-register-applications.md).
  
Ha Ön egy ASP.NET webes alkalmazás fejlesztője, az alkalmazás hitelesítéséhez a lépésekkel, a fiókok beállítása [oktatóanyag: A webalkalmazás az Azure AD B2C használatával fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-web-app.md).

Ha a rendszer egy asztali alkalmazás fejlesztője az alkalmazás beállítása a lépésekkel, a fiókok hitelesítésére [oktatóanyag: Egy asztali alkalmazás az Azure AD B2C használatával fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-desktop-app.md).

Ha Ön egy egyoldalas alkalmazás fejlesztője Node.js használatával, az alkalmazás hitelesítéséhez a lépésekkel, a fiókok beállítása [oktatóanyag: Egy egyoldalas alkalmazás az Azure AD B2C használatával fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>További lépések

Lépjen tovább az oktatóanyagra alkalmazása regisztrációs és bejelentkezési folyamata konfigurálásának megkezdéséhez.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Active Directory B2C-bérlő létrehozása](tutorial-create-tenant.md)
