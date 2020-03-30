---
title: Mi az az Azure Active Directory B2C?
description: Megtudhatja, hogy az Azure Active Directory B2C használatával hogyan támogathatja az alkalmazások külső identitásait, például a Facebookkal, a Google-lal és más identitásszolgáltatókkal való közösségi bejelentkezést.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 61b6d5ea903d00519c58556bc99da7065741a6e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78184060"
---
# <a name="what-is-azure-active-directory-b2c"></a>Mi az az Azure Active Directory B2C?

Az Azure Active Directory B2C szolgáltatásként biztosítja az üzleti és az ügyfelek identitását. Az ügyfelek az általuk előnyben részesített közösségi, nagyvállalati vagy helyi fiókidentitások használatával egyszeri bejelentkezési hozzáférést kapnak az alkalmazásokhoz és API-khoz.

![Az Azure AD B2C identitásszolgáltatók és az alsóbb rétegbeli alkalmazások infografikája](./media/overview/azureadb2c-overview.png)

Az Azure Active Directory B2C (Azure AD B2C) egy ügyfélidentitás-hozzáférés-kezelési (CIAM) megoldás, amely felhasználók millióit és naponta több milliárd hitelesítést képes támogatni. Gondoskodik a hitelesítési platform skálázásáról és biztonságáról, figyeli és automatikusan kezeli az olyan fenyegetéseket, mint a szolgáltatásmegtagadás, a jelszóspray vagy a találgatásos támadások.

## <a name="custom-branded-identity-solution"></a>Egyedi márkájú identitáskezelési megoldás

Az Azure AD B2C egy fehér címkés hitelesítési megoldás. Testreszabhatja a teljes felhasználói élményt a márkával, hogy zökkenőmentesen illeszkedjen a webes és mobilalkalmazásokhoz.

Testreszabhatja az Azure AD B2C által megjelenített összes oldalt, amikor a felhasználók regisztrálnak, bejelentkeznek és módosítják a profiladatukat. Testreszabhatja a HTML-, CSS-és JavaScript-kódot a felhasználói utaksorán, hogy az Azure AD B2C-élmény úgy tűnjön, és úgy érezze, hogy az alkalmazás natív része.

![Testreszabott regisztrációs és bejelentkezési oldalak és háttérkép](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Egyszeri bejelentkezés a felhasználó által biztosított identitással

Az Azure AD B2C szabványokon alapuló hitelesítési protokollokat használ, például az OpenID Connect, az OAuth 2.0 és az SAML protokollt. Ez integrál -val a leg--bb korszerű pályázatokat és kereskedelmi távoli- a- polc szoftver.

![Az Azure AD B2C-vel szövetséges külső identitások diagramja](./media/overview/scenario-singlesignon.png)

A webes alkalmazások, a mobilalkalmazások és az API-k központi hitelesítési hatóságaként az Azure AD B2C lehetővé teszi, hogy mindegyik számára egyetlen bejelentkezési (SSO) megoldást hozzon létre. Központosíthatja a felhasználói profil- és beállításadatok gyűjteményét, és részletes elemzéseket készíthet a bejelentkezési viselkedésről és a regisztrációs konverzióról.

## <a name="integrate-with-external-user-stores"></a>Integrálás külső felhasználói áruházakkal

Az Azure AD B2C egy könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot képes tárolni. Azonban külső rendszerekkel is integrálható. Például használja az Azure AD B2C hitelesítéshez, de delegálegy külső ügyfélkapcsolat-kezelés (CRM) vagy az ügyfélhűség-adatbázis, mint az igazság forrása az ügyféladatok.

Egy másik külső felhasználói áruház forgatókönyv, hogy az Azure AD B2C kezeli az alkalmazás hitelesítését, de integrálható egy külső rendszer, amely tárolja a felhasználói profilt vagy a személyes adatokat. Például az adatok tárolási követelmények, például a regionális vagy helyszíni adattárolási házirendek teljesítéséhez.

![Az Azure AD B2C külső felhasználói tárolóval való kommunikációjának logikai diagramja](./media/overview/scenario-remoteprofile.png)

Az Azure AD B2C megkönnyítheti az adatok gyűjtését a felhasználótól a regisztráció vagy a profil szerkesztése során, majd átadja ezeket az adatokat a külső rendszernek. Ezután a jövőbeli hitelesítések során az Azure AD B2C lekérheti az adatokat a külső rendszerből, és szükség esetén az alkalmazásnak küldött hitelesítési jogkivonat-válasz részeként.

## <a name="progressive-profiling"></a>Progresszív profilalkotás

Egy másik felhasználói út lehetőség magában foglalja a progresszív profilalkotást. A progresszív profilalkotás lehetővé teszi az ügyfelek számára, hogy minimális mennyiségű információ gyűjtésével gyorsan végrehajtsák az első tranzakciójukat. Ezután fokozatosan gyűjtsön további profiladatokat az ügyféltől a jövőbeli bejelentkezésekkor.

![A progresszív profilalkotás vizuális ábrázolása](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Harmadik fél személyazonosságának ellenőrzése és ellenőrzése

Az Azure AD B2C használatával megkönnyítheti az identitás-ellenőrzést és -ellenőrzést a felhasználói adatok gyűjtésével, majd egy harmadik fél rendszernek való átadásával, hogy érvényesítést, megbízhatósági pontozást és jóváhagyást adjon a felhasználói fiókok létrehozásához.

![A külső identitásellenőrzés felhasználói folyamatát bemutató diagram](./media/overview/scenario-idproofing.png)

Ez csak néhány az Azure AD B2C,mint az ügyfelek identitásplatformja. Az áttekintés következő szakaszai egy Azure AD B2C-t használó bemutatóalkalmazáson vezetnek be. Az [Azure AD B2C](technical-overview.md)részletesebb technikai áttekintésére is rátekinthet.

## <a name="example-woodgrove-groceries"></a>Példa: WoodGrove Élelmiszerek

[A WoodGrove Élelmiszeregy][woodgrove] élő webes alkalmazás, amelyet a Microsoft hozott létre, hogy bemutassa az Azure AD B2C számos funkcióját. A következő néhány szakasz az Azure AD B2C által a WoodGrove webhelyre biztosított hitelesítési lehetőségek et áttekinti.

### <a name="business-overview"></a>Üzleti áttekintés

WoodGrove egy online élelmiszerbolt, hogy eladja élelmiszert mind az egyéni fogyasztók és az üzleti ügyfelek számára. Üzleti ügyfeleik a vállalatuk vagy az általuk kezelt vállalkozások nevében vásárolnak élelmiszert.

### <a name="sign-in-options"></a>Bejelentkezési beállítások

A WoodGrove Élelmiszerüzletek számos bejelentkezési lehetőséget kínálnak az ügyfelek és az üzlet közötti kapcsolat alapján:

* **Az egyes** ügyfelek egyéni fiókokkal regisztrálhatnak vagy bejelentkezhetnek, például közösségi identitásszolgáltatóval vagy e-mail címmel és jelszóval.
* **Az üzleti** ügyfelek regisztrálhatnak vagy bejelentkezhetnek a vállalati hitelesítő adataikkal.
* **Partnerek** és a beszállítók olyan egyének, akik a kínálat az élelmiszerbolt termékeket eladni. A partneridentitást az [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md)biztosítja.

![Egyéni (B2C), üzleti (B2C) és partneri (B2B) bejelentkezési oldalak](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Egyéni ügyfelek hitelesítése

Amikor egy ügyfél kiválasztja **a Bejelentkezést a személyes fiókjával,** a rendszer átirányítja őket az Azure AD B2C által üzemeltetett testreszabott bejelentkezési lapra. Az alábbi képen láthatja, hogy a felhasználói felületet (UI) úgy szabtuk testre, hogy úgy nézzen ki és érezze magát, mint a WoodGrove Élelmiszerwebhely. WoodGrove ügyfelei nek nem szabad nem tudja, hogy a hitelesítési élményt az Azure AD B2C üzemelteti és biztosítja.

![Az Azure AD B2C által üzemeltetett egyéni WoodGrove bejelentkezési lap](./media/overview/sign-in.png)

A WoodGrove lehetővé teszi ügyfelei számára, hogy regisztráljanak és jelentkezzenek be a Google-, Facebook- vagy Microsoft-fiókjukkal, mint identitásszolgáltatójukkal. Vagy regisztrálhatnak az e-mail címük és a jelszafokkal, hogy létrehozzanak egy *helyi fiókot.*

Amikor egy ügyfél a **Regisztráció a személyes fiókkal** lehetőséget választja, majd **most regisztrálja,** egyéni regisztrációs oldal jelenik meg számára.

![Az Azure AD B2C által üzemeltetett egyéni WoodGrove-regisztrációs lap](./media/overview/sign-up.png)

Miután megadta az e-mail címet, és az **Ellenőrző kód küldése**lehetőséget választotta, az Azure AD B2C elküldi nekik a kódot. Miután beírták a kódjukat, válasszuk **a Kód ellenőrzése**lehetőséget, majd a többi adatot a képernyőn, el kell fogadniuk a szolgáltatási feltételeket is.

A **Létrehozás** gombra kattintva az Azure AD B2C visszairányítja a felhasználót a WoodGrove Élelmiszerwebhelyre. Átirányítások esetén az Azure AD B2C átad egy OpenID Connect hitelesítési jogkivonatot a WoodGrove webalkalmazásnak. A felhasználó most már be van jelentkezve, és készen áll az indulásra, a megjelenítendő neve a jobb felső sarokban látható, jelezve, hogy be vannak jelentkezve.

![WoodGrove Élelmiszerwebhely fejléce, amely en látható, hogy a felhasználó be van jelentkezve](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Üzleti ügyfelek hitelesítése

Amikor egy ügyfél kiválasztja az **üzleti ügyfelek**csoport egyik beállítását, a WoodGrove Élelmiszer-webhely egy másik Azure AD B2C-szabályzatot hív meg, mint az egyes ügyfelek számára.

Ez a házirend a felhasználó számára egy lehetőséget, hogy használja a vállalati hitelesítő adatokat a regisztráció hoz és a bejelentkezéshez. A WoodGrove-példában a felhasználók nak be kell jelentkezniük bármely Office 365-ös vagy Azure AD-fiókkal. Ez a szabályzat egy [több-bérlős Azure AD-alkalmazást](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) és az Azure AD-végpontot használ az `/common` Azure AD B2C összeegyezéséhez a világ bármely Office 365-ügyféllel.

### <a name="authenticate-partners"></a>Partnerek hitelesítése

A **Bejelentkezés a szállítói fiókkal** hivatkozás az Azure Active Directory B2B együttműködési funkcióját használja. Az Azure AD B2B az Azure Active Directory ban a partneridentitások kezeléséhez. Ezeket az identitásokat az Azure Active Directoryból össze lehet ékesítették az Azure AD B2C-vel védett alkalmazásokhoz való hozzáféréshez.

További információ az Azure AD B2B-ről [a Mi a vendégfelhasználói hozzáférés az Azure Active Directory B2B-ben?](../active-directory/b2b/what-is-b2b.md)

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>További lépések

Most, hogy már van egy ötlete, hogy mi az Azure AD B2C, és néhány forgatókönyv segíthet, mélyebbre ássa magát a funkciók és a technikai szempontok.

> [!div class="nextstepaction"]
> [Az Azure AD B2C technikai áttekintése >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
