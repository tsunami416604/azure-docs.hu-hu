---
title: Hitelesítés a Microsoft identity platform |} Az Azure
description: További tudnivalók a Microsoft identity platform, az alkalmazás hitelesítési modellezheti, API, a kiépítést, és a leggyakrabban használt hitelesítési forgatókönyvek, hogy a Microsoft identity platform támogatja.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1d54347b9a3ccc72cfd5b88400d699d93132fbf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785570"
---
# <a name="what-is-authentication"></a>Mi a hitelesítés?

A *hitelesítési* valamely fél érvényes hitelesítő adatainak bekérése, amely egy identitás- és hozzáférés-vezérléshez használható rendszerbiztonsági tag létrehozását eredményezi. Egyszerűbb megfogalmazásban a hitelesítés az a folyamat, amelynek során a felhasználó bebizonyítja, hogy az, akinek vallja magát. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni.

Az *engedélyezés* egy műveletre jogosító engedély megadása egy hitelesített rendszerbiztonsági tag számára. Meghatározza az elérhető adatok körét és az azokon végrehajtható műveleteket. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni.

A Microsoft identity platform leegyszerűsíti a hitelesítési alkalmazásfejlesztőknek szánt identitás-szolgáltatás, megadásával szabványos protokollok, mint például az OAuth 2.0 és OpenID Connect támogatása, valamint a különböző platformok, nyílt forráskódú kódtárai segít gyorsan a kódírás.

A Microsoft identity platform programozási modell két elsődleges használati eset szerepelnek:

* Az OAuth 2.0 engedélyezési folyamat – amikor az erőforrás tulajdonosa adja meg az engedélyt az ügyfélalkalmazás számára, hogy az elérhesse az erőforrás-tulajdonos erőforrásait.
* Az erőforrások ügyfél általi elérése – ezt az erőforrás-kiszolgáló valósítja meg a hozzáférési jogkivonatokban foglalt jogcímértékek alapján, amelyek a vonatkozó hozzáférés-vezérlési döntések meghozatalát irányítják.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>A Microsoft identitásplatformja hitelesítés alapjai

Vegyük a legalapvetőbb forgatókönyvet, amelyben az identitásokat alkalmazni kell: a felhasználónak a böngészőben hitelesítenie kell magát egy webalkalmazásban. Az alábbi ábra ezt a forgatókönyvet mutatja be:

![Webalkalmazásba való bejelentkezés áttekintése](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Az ábrán lévő összetevőkről a következőket érdemes tudni:

* A Microsoft identity platform az identitásszolgáltató az. Az identitásszolgáltató feladata a szervezet címtárában lévő felhasználók és alkalmazások identitásának ellenőrzése, majd a felhasználók és alkalmazások sikeres hitelesítését követően a biztonsági jogkivonatok kiadása.
* Egy alkalmazás, amely kiszervezik a Microsoft identitásplatformja hitelesítést szeretne regisztrálni kell az Azure Active Directoryban (Azure AD). Az Azure AD regisztrálja és egyedileg azonosítja az alkalmazást a címtárban.
* Fejlesztők használhatják a nyílt forráskódú Microsoft identity platform hitelesítési tárak megkönnyítése hitelesítési protokoll részleteinek kezeli az Ön számára. További információ: a Microsoft identity platform [2.0-s verziójú hitelesítési tárak](reference-v2-libraries.md) és [1.0-s verziójú hitelesítési tárak](active-directory-authentication-libraries.md).
* A felhasználó hitelesítését követően az alkalmazásnak ellenőriznie kell a felhasználó biztonsági jogkivonatát, hogy megbizonyosodhasson róla a hitelesítés sikeréről. A rövid útmutatók, oktatóanyagok és a különböző nyelvekhez és keretrendszerekhez elérhető kódminták mutatják be, hogy az alkalmazásnak mit kell tennie.
  * Az alkalmazások gyors felépítésével és a jogkivonatok beszerzését és frissítését, a felhasználók bejelentkeztetését és a felhasználóadatok megjelenítését végző funkcionalitás hozzáadásával kapcsolatban lásd a dokumentáció **Rövid útmutatók** szakaszát.
  * A hitelesítéssel kapcsolatos legfontosabb fejlesztői feladatok elvégzéséhez, például a hozzáférési jogkivonatok beszerzéséhez és azok Microsoft Graph API és egyéb API-k hívásaiban való használatához, a hagyományos böngészőalapú alkalmazásokon belüli, OpenID Connect használatával végzett Microsoft-bejelentkezések megvalósításához, valamint hasonló feladatokhoz használható részletes, forgatókönyv-alapú eljárásokat a dokumentáció **Oktatóanyagok** szakaszában találja.
  * A kódminták letöltéséhez látogasson el a [GitHubra](https://github.com/Azure-Samples?q=active-directory).
* A hitelesítési folyamathoz használt kérés-válasz folyamatot az alkalmazott hitelesítési protokoll határozza meg, például az OAuth 2.0, az OpenID Connect, a WS-Federation vagy az SAML 2.0. A protokollokkal kapcsolatos további információkért lásd a dokumentáció **Fogalmak > Protokollok** szakaszát.

A fenti példaforgatókönyvben az alkalmazásokat az alábbi két szerepkör szerint osztályozhatjuk:

* Olyan alkalmazások, amelyeknek biztonságosan kell hozzáférniük az erőforrásokhoz
* Olyan alkalmazások, amelyek az erőforrások szerepét töltik be

Most, hogy az identitás az app model és API-t az alapokat, látogasson el lapunkra áttekintése, hogyan kiépítés működik a Microsoft identity platform, és hivatkozásokat tartalmaz a gyakori forgatókönyvek részletes információ a Microsoft identity platform támogatja.

## <a name="application-model"></a>Alkalmazásmodell

A Microsoft identity platform, amely van kialakítva, hogy két fő funkciókat teljesíteni modell a következő alkalmazások jelöli:

* **Az alkalmazás azonosítása a támogatott hitelesítési protokollnak megfelelően** – Ez az összes azonosító, URL-cím, titkos kulcs és a hitelesítés alkalmával szükséges összes vonatkozó információ számba vételét jelenti. Itt a Microsoft identity platform:

    * Tárolja a futásidejű hitelesítés támogatásához szükséges összes adatot.
    * Tárolja a szükséges adatokat annak eldöntéséhez, hogy az alkalmazásoknak milyen erőforrásokat kellhet elérniük, és az adott kéréseket teljesíteni kell-e, illetve milyen körülmények között kell teljesíteni.
    * Biztosítja az infrastruktúrát az alkalmazásregisztráció megvalósításához az alkalmazásfejlesztő bérlőjén és a többi Azure AD-bérlőn.

* **Felhasználói jóváhagyás, jogkivonat kérése idő alatt kezelni, és elősegítik a bérlők között az alkalmazások dinamikus kiépítést** – Itt a Microsoft identity platform:

    * Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
    * Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

A Microsoft identity platform egy **alkalmazásobjektum** során egy absztrakt entitást ismerteti. A fejlesztők alkalmazásokkal dolgoznak. A telepítéskor, a Microsoft identity platform az adott alkalmazáshoz objektumot használ webportált hozhat létre egy **egyszerű szolgáltatás**, amely egy adott könyvtárban vagy a bérlő egy alkalmazás egy konkrét példányát jelöli. Ez a szolgáltatásnév határozza meg, hogy az alkalmazás ténylegesen milyen tevékenységeket hajthat végre az adott célcímtárban, mely erőforrásokat érheti el, és így tovább. A Microsoft identity platform hoz létre egy egyszerű szolgáltatást az alkalmazásobjektum keresztül **hozzájárulás**.

Az alábbi ábrán látható egy egyszerűsített a Microsoft identity platform-üzembehelyezési hátterében a jóváhagyási folyamatot.  Két bérlőjéhez léteznek, (A és B), ahol A bérlő az alkalmazás tulajdonosa, és a bérlőtől a B bérlőhöz van hárítható el az alkalmazást egy egyszerű szolgáltatást.  

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

|   |   |
|---|---|
| 1 | A felhasználó a bérlőtől a B bérlőhöz próbál jelentkezzen be az alkalmazás |
| 2 | A rendszer kéri és ellenőrzi a felhasználó hitelesítő adatait |
| 3 | A rendszer kéri a felhasználó hozzájárulását ahhoz, hogy az alkalmazás hozzáférjen a B bérlőhöz |
| 4 | A Microsoft identity platform használja az alkalmazásobjektum a tervrajz létrehozni egy szolgáltatásnevet a bérlőtől a B bérlőhöz |
| 5 | A felhasználó megkapja az igényelt jogkivonatot |
|   |   |

A folyamat tetszőleges mennyiségben ismételhető a többi bérlőre (C, D és így tovább) vonatkozóan. A bérlő megőrzi a tervezet az alkalmazás (alkalmazásobjektum). A többi bérlőn, amelyekre az alkalmazás hozzájárulást kapott, továbbra is a felhasználók és a rendszergazdák határozhatják meg az egyedi bérlőkön lévő megfelelő szolgáltatásnév-objektumokon keresztül, hogy az alkalmazás milyen tevékenységeket hajthat végre. További információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok a Microsoft identity platform](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>A Microsoft identity platform biztonsági jogkivonatokat a jogcím

Biztonsági jogkivonatok (hozzáférési és azonosító-jogkivonatokat), a Microsoft identity platform által kiadott jogcímeket, vagy a helyességi feltételek hitelesítését, a tulajdonos adatait tartalmaznak. Az alkalmazások a jogcímeket különféle műveletekhez használhatják:

* A jogkivonat érvényesítése
* Az alany címtárbérlőjének azonosítása
* A felhasználó adatainak megjelenítése
* Az alany engedélyezésének meghatározása

Az egyes biztonsági jogkivonatokban lévő jogcímek a jogkivonat típusától, a felhasználó hitelesítéséhez használt hitelesítő adatoktól és az alkalmazás konfigurációjától függenek.

Az alábbi táblázat röviden a Microsoft identity platform által kibocsátott jogcímek különböző típusú biztosítja. Részletesebb információkért lásd: a [hozzáférési jogkivonatokat](access-tokens.md) és [azonosító-jogkivonatokat](id-tokens.md) identity platform a Microsoft által kiadott.

| Jogcím | Leírás |
| --- | --- |
| Alkalmazásazonosító | A jogkivonatot használó alkalmazást azonosítja. |
| Célközönség | A fogadó erőforrást azonosítja, amelynek a jogkivonat szól. |
| Alkalmazáshitelesítési környezet osztályhivatkozása | Az ügyfél hitelesítési módját adja meg (nyilvános vagy bizalmas ügyfél). |
| Hitelesítési időpont | A hitelesítés megtörténtének dátumát és időpontját rögzíti. |
| Hitelesítési módszer | A jogkivonatalany hitelesítésének módját adja meg (jelszó, tanúsítvány stb.). |
| Utónév | A felhasználó Azure AD-ben beállított utónevét adja meg. |
| Csoportok | Az olyan Azure AD-csoportok objektumazonosítóit adja meg, amelyeknek az adott felhasználó tagja. |
| Identitásszolgáltató | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. |
| Kibocsátás időpontja | A jogkivonat kibocsátásának időpontját rögzíti, amely alapján általában a jogkivonat frissességét szokás elbírálni. |
| Kiállító | A jogkivonatot kiadó biztonsági jegykiadó szolgáltatást, valamint az Azure AD-bérlőt azonosítja. |
| Vezetéknév | A felhasználó Azure AD-ben beállított vezetéknevét adja meg. |
| Name (Név) | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. |
| Objektumazonosító | Az alany Azure AD-beli megváltoztathatatlan egyedi azonosítóját adja meg. |
| Szerepkörök | A felhasználónak kiosztott Azure AD-beli alkalmazás-szerepkörök rövid neveit adja meg. |
| Hatókör | Az ügyfélalkalmazás számára megadott engedélyeket jelzi. |
| Tárgy | Azt a szolgáltatásnevet adja meg, amelynek az adatait a jogkivonat hitelesíti. |
| Bérlőazonosító | A jogkivonatot kibocsátó címtárbérlő megváltoztathatatlan egyedi azonosítóját adja meg. |
| Jogkivonat élettartama | A jogkivonat érvényességi idejét határozza meg. |
| Felhasználó egyszerű neve | Az alany egyszerű felhasználónevét adja meg. |
| Verzió | A jogkivonat verziószámát adja meg. |

## <a name="next-steps"></a>További lépések

* További információ a [alkalmazástípusok és a Microsoft identitásplatformja támogatott forgatókönyveket](app-types.md)
