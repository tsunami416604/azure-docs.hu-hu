---
title: Mi az Azure Active Directory B2C? | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és kezelheti az alkalmazás bejelentkezési élmény Azure Active Directory B2C használatával kapcsolatos.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 878d4da715308abf6dbe536ea0d1df2a68225179
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711314"
---
# <a name="what-is-azure-active-directory-b2c"></a>Mi az Azure Active Directory B2C?

Az Azure Active Directory (Azure AD) B2C egy identitáskezelő szolgáltatás, amely lehetővé teszi az ügyfelek regisztrációjának és bejelentkezésének testreszabását és felügyeletét, valamint a profiljuk kezelését az alkalmazások használatakor. Ez az iOS, Android, .NET és más rendszerekre fejlesztett alkalmazásokra is vonatkozik. Az Azure AD B2C lehetővé teszi, hogy ezek a műveletek közben a felhasználói identitások védelmet egy időben.

Konfigurálhatja a különböző identity management műveletek végrehajtására az Azure AD B2C-vel regisztrált egy alkalmazást. Néhány példa:

- A regisztrált alkalmazás használatához regisztrálni az ügyfél engedélyezése
- Regisztrálta magát az ügyfél bejelentkezéshez és az alkalmazás használatának engedélyezése
- A szerkesztheti a profilját regisztrálta magát az ügyfél engedélyezése
- Az alkalmazás a többtényezős hitelesítés engedélyezése
- Az ügyfél regisztrálhat, és jelentkezzen be az adott identitás-szolgáltatóktól engedélyezése
- Hozzáférés biztosítása az API-t hoz létre az alkalmazásból
- A regisztráció és bejelentkezés felhasználói felület megjelenését és működését testreszabása
- Egyszeri bejelentkezési munkamenetek alkalmazás kezelése

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Mit kell gondolja át az Azure AD B2C használatához?

- Hogyan szeretné az ügyfél kommunikál az alkalmazásban?
- Mi az a felhasználói felület (UI) élményt nyújtsanak ügyfeleinek kívánt?
- Mely identitás-szolgáltatóktól szeretnék lehetőséget biztosítani az ügyfelek az alkalmazásban. választhat?
- A bejelentkezési folyamat igényel további API-k futtatásához?

### <a name="customer-interaction"></a>Kapcsolat az ügyféllel

Támogatja az Azure AD B2C [OpenID Connect](https://openid.net/connect/) minden ügyfél észlel. Az OpenID Connect Azure AD B2C-implementációjában az alkalmazás kezdeményezi a felhasználók utazás Azure AD B2C küldött hitelesítési kérések alapján. A kérés eredménye egy `id_token`. A biztonsági jogkivonatot az ügyfél identitásának jelöli.

Minden Azure AD B2C használó alkalmazást regisztrálni kell az Azure AD B2C-bérlő az Azure portál használatával. A regisztrációs folyamat gyűjt, és hozzárendeli az értékeket az alkalmazás. Ezek az értékek közé tartozik az Alkalmazásazonosító, amely egyedileg azonosítja az alkalmazást és egy átirányítási URI-t vissza a válaszok közvetlen használható.

Minden egyes alkalmazás közötti interakció hasonló felső szintű mintát követi:

1. Az alkalmazás egy házirendet, futtassa irányítja.
2. Az ügyfél szerint teljesíti a szabályzat a házirend-definíció.
3. Az alkalmazás egy biztonsági jogkivonatot kap.
4. Az alkalmazás a biztonsági jogkivonat alapján védett erőforrás elérésére tett kísérlet.
5. Az erőforrás-kiszolgáló ellenőrzi a biztonsági jogkivonatot, és megállapítja, hogy megadható-e hozzáférés.
6. Az alkalmazás rendszeres időközönként frissíti a biztonsági jogkivonatot.

A lépések némileg eltérőek lehetnek az Ön által létrehozott alkalmazás.

Az Azure AD B2C együttműködő Identitásszolgáltatók, az ügyfelek, a más rendszerekkel, és a helyi könyvtárnak sorban egy identitás-feladat végrehajtásához. Például jelentkezzen be az ügyfél, regisztrálni egy új ügyfél vagy a jelszó alaphelyzetbe állítása. Az alapul szolgáló platform, amely több résztvevős megbízhatósági kapcsolatot hoz létre, és ezeket a lépéseket kell megtennie a identitás élmény keretrendszer nevezik. Ezt a keretrendszert és egy (más néven felhasználó út vagy egy megbízható keretrendszer szabályzata) explicit módon meghatározza a szereplője, a műveletek, a protokollok és a feladatütemezési lépés befejezéséhez.

Az Azure AD B2C védje a szolgáltatásmegtagadási- és a jelszót az alkalmazások többféle módon elleni támadások. Az Azure AD B2C által használt észlelés és javaslat módszerek, például SZIN cookie-k és sebesség és a kapcsolat korlátai az erőforrások-szolgáltatásmegtagadási támadások elleni védelméhez. Megoldás is megtalálható a találgatásos jelszó támadások és a jelszó szótáras.

#### <a name="built-in-policies"></a>Beépített szabályzatok

Minden Azure AD B2C küldött kérelmek házirend határozza meg. A szabályzatok vezérlik az alkalmazás és az Azure AD B2C együttműködését viselkedését. Beépített házirendek az előre identitás leggyakoribb feladatokat, például a regisztráció, bejelentkezés, és a profil szerkesztése.  Például a regisztrációs szabályzatban teszi viselkedés szabályozására, a következő beállítások konfigurálása:

- Közösségi fiókokat, az ügyfél iratkozzon fel az alkalmazás segítségével
- Az ügyfelek, például utónév vagy irányítószámot összegyűjtött adatok
- Multi-Factor Authentication
- Minden bejelentkezési oldalak megjelenésének és arculatának
- Az alkalmazás a visszakapott információk

#### <a name="custom-policies"></a>Egyéni szabályzatok 

[Egyéni házirendek](active-directory-b2c-overview-custom.md) olyan konfigurációs fájlok, amelyek a identitás élmény keretrendszer viselkedésének meghatározása az Azure AD B2C-bérlőben. Egyéni házirendek teljes mértékben szerkeszthető sok feladatok elvégzéséhez. Egyéni házirendet egy jelenik meg egy vagy több XML-formátumú fájlok, amelyek hierarchikus lánc hivatkozhatnak egymásra. 

Különböző típusú több egyéni házirendet szükség szerint az Azure AD B2C-bérlő használható, és különböző alkalmazások felhasználhatók. Erre a rugalmasságra lehetővé teszi határozza meg, és módosítsa az ügyfél identitással kapcsolatos műveletet, a minimális vagy a kód nem módosultak. Házirendek használható HTTP-hitelesítési kérelmek egy speciális lekérdezési paraméter hozzáadásával.

Egyéni házirendek segítségével szabályozhatja a felhasználó utak az alábbi módszerek:

- Kölcsönhatások a további információkat API-k meghatározása, ellenőrizze a megadott felhasználói jogcímeket, vagy külső folyamatok eseményindítót.
- API-k vagy jogcímeket a címtárban a jogcímeket, mint alapján viselkedésének módosítása *migrationStatus*.
- Minden munkafolyamat beépített házirendek által nem jelzett. Például további információkat gyűjt az ügyfél egy bejelentkezési folyamata során, és az engedélyezés végrehajtása ellenőrizze, hogy elért egy erőforrást.

### <a name="identity-providers"></a>Identitásszolgáltatók

Az identitásszolgáltató egy olyan szolgáltatás, amely hitelesíti a felhasználói identitások és biztonsági jogkivonatokat bocsát ki. Az Azure AD B2C-ben az Ön bérlőjében, például a Microsoft-fiókkal, Facebook-on vagy Amazon többek között konfigurálhatja egy identitás-szolgáltatóktól száma. 

Az identitásszolgáltató konfigurálása az Azure AD B2C-bérlőben, rögzítenie kell az alkalmazásazonosító vagy ügyfél-azonosító és a jelszót vagy az ügyfél titkos kulcs az Ön által létrehozott identity provider alkalmazásból. Az azonosító és jelszó majd konfigurálására szolgálnak az alkalmazást.

### <a name="user-interface-experience"></a>Felhasználói felület élmény

A HTML- és CSS tartalmat, amely nem jelenik meg az ügyfelek többsége szabályozható. A lap felhasználói felületének testreszabása szolgáltatás segítségével testre szabhatja bármely házirend megjelenését és működését. Kezelheti a márka és egységes megjelenést az alkalmazás és az Azure AD B2C között is.

Az Azure AD B2C az ügyfél böngészőjében kód fut, és a modern megközelítés Cross-Origin Resource Sharing (CORS) néven. Először egy testreszabott HTML-tartalmakat a házirendben meghatározott URL-címet. Az Azure AD B2C egyesíti a HTML-tartalmakat, be töltve az URL-címről, majd a oldalát jeleníti meg, hogy az ügyfél felhasználói felületének elemmel.

Az Azure AD B2C paraméterek küldhet a lekérdezési karakterláncban. Úgy, hogy a paraméter a HTML-végpontot, dinamikusan módosíthatja az oldal tartalmát. Például a háttérkép, a Azure AD B2C regisztráció vagy bejelentkezés lapon adja meg a webkiszolgáló vagy a mobilalkalmazás paramétereinek alapján módosíthatja.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Hogyan kezdjem el az Azure AD B2C?

Az Azure AD B2C-ben a bérlő azt a szervezetet jelöli, és a felhasználók könyvtár. Minden Azure AD B2C bérlő, és más Azure AD B2C bérlő külön. A bérlő az ügyfelek, amelyek regisztráltak-e az alkalmazás használatára vonatkozó adatokat tartalmaz. Például jelszavak, profiladatok és engedélyeket.

Az Azure AD B2C-bérlő csatolása az Azure-előfizetéshez minden funkció engedélyezéséhez és a használati díjat kell fizetnie kell. Lehetővé teszi az Azure AD B2C ügyfeleknek bejelentkezni az alkalmazásba, regisztrálnia kell az alkalmazást az Azure AD B2C-bérlő.

Az alkalmazás használhatja az Azure AD B2C konfigurálása előtt először az Azure AD B2C bérlő létrehozása és az alkalmazás regisztrálásához. Az alkalmazás regisztrálásához hajtsa végre a lépéseit [oktatóanyag: egy alkalmazás-előfizetési, és jelentkezzen be az Azure AD B2C használatával regisztrálása](tutorial-register-applications.md).
  
Ha az ASP.NET web alkalmazásfejlesztő, állítsa be az alkalmazás a lépések fiókok hitelesítéséhez [oktatóprogram: webalkalmazás az Azure AD B2C segítségével fiókokhoz való hitelesítéshez szükséges engedélyezése](active-directory-b2c-tutorials-web-app.md).

Ha a következők egy asztali alkalmazások fejlesztői az alkalmazás beállítása a lépések fiókok hitelesítéséhez [oktatóanyag: az Azure AD B2C segítségével fiókokhoz való hitelesítéshez szükséges asztali alkalmazások engedélyezése](active-directory-b2c-tutorials-desktop-app.md).

Ha egy egyoldalas alkalmazásfejlesztő Node.js segítségével, állítsa be az alkalmazás a lépések fiókok hitelesítéséhez [oktatóanyag: lehetővé teszik egy egyoldalas alkalmazások az Azure AD B2C segítségével fiókokhoz való hitelesítéshez szükséges](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>További lépések

Indítsa el az alkalmazás a regisztrációt, és a bejelentkezési élmény konfigurálása által a az oktatóanyag folytatása.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy alkalmazás-előfizetési, és jelentkezzen be az Azure AD B2C használatával regisztrálása](tutorial-register-applications.md)
