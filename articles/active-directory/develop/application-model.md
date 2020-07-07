---
title: Alkalmazás modellje | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 774822eae5ab327f57da3eca4499ca14d3e0c7a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82584310"
---
# <a name="application-model"></a>Alkalmazásmodell

Az alkalmazások maguk is bejelentkezhetnek a felhasználókba, vagy delegálhatja a bejelentkezést egy identitás-szolgáltatóhoz. Ez a témakör az alkalmazások Microsoft Identity platformmal való regisztrálásához szükséges lépéseket ismerteti.

## <a name="registering-an-application"></a>Alkalmazás regisztrálása

Ahhoz, hogy egy identitás-szolgáltató tudja, hogy egy felhasználó hozzáfér egy adott alkalmazáshoz, a felhasználónak és az alkalmazásnak is regisztrálva kell lennie az identitás-szolgáltatónál. Ha az Azure AD-vel regisztrálja az alkalmazást, olyan identitás-konfigurációt biztosít az alkalmazáshoz, amely lehetővé teszi, hogy integrálható legyen a Microsoft Identity platformmal. Az alkalmazás regisztrálása a következőket is lehetővé teszi:

* Szabja testre az alkalmazás arculatát a bejelentkezési párbeszédablakban. Ez azért fontos, mert ez az első olyan felhasználói élmény, amelyet a felhasználó az alkalmazással fog rendelkezni.
* Döntse el, hogy csak akkor szeretné-e bejelentkezni a felhasználók számára, ha azok a szervezethez tartoznak. Ez egyetlen bérlői alkalmazás. Vagy bármely munkahelyi vagy iskolai fiókkal való bejelentkezés engedélyezése a felhasználók számára. Ez egy több-bérlős alkalmazás. A személyes Microsoft-fiókokat, illetve a LinkedIn, a Google és egyéb közösségi fiókokat is lehetővé teheti.
* Hatókör-engedélyek kérése. Kérheti például a "user. Read" hatókört, amely engedélyt ad a bejelentkezett felhasználó profiljának olvasásához.
* Adja meg a webes API-hoz való hozzáférést meghatározó hatóköröket. Általában, amikor egy alkalmazás szeretne hozzáférni az API-hoz, engedélyt kell kérnie az Ön által meghatározott hatókörökre.
* Ossza meg a titkos kódot a Microsoft Identity platformmal, amely igazolja az alkalmazás identitását.  Ez abban az esetben fontos, ha az alkalmazás bizalmas ügyfélalkalmazás. A bizalmas ügyfélalkalmazások olyan alkalmazások, amelyek biztonságosan tárolhatják a hitelesítő adatokat. A hitelesítő adatok tárolásához megbízható háttér-kiszolgálót igényelnek.

A regisztrálást követően az alkalmazás egyedi azonosítót kap, amelyet az alkalmazás a Microsoft Identity platformmal oszt meg, amikor jogkivonatokat kér. Ha az alkalmazás [bizalmas ügyfélalkalmazás](developer-glossary.md#client-application), akkor a titkos kulcsot vagy a nyilvános kulcsot is megosztja, attól függően, hogy a rendszer a tanúsítványokat és a titkokat használta-e.

A Microsoft Identity platform a két fő funkciót teljesítő modellt használó alkalmazásokat jelöli:

* Azonosítsa az alkalmazást az általa támogatott hitelesítési protokollok alapján
* Adja meg a hitelesítéshez szükséges összes azonosítót, URL-címet, titkot és kapcsolódó információt.

Microsoft Identity platform:

* A hitelesítés futtatásának támogatásához szükséges összes adattal rendelkezik
* Az összes adat megtartásával döntheti el, hogy az alkalmazás milyen erőforrásokhoz férhet hozzá, és milyen esetekben kell teljesítenie egy adott kérést
* Infrastruktúrát biztosít az alkalmazás-kiépítés megvalósításához az alkalmazás fejlesztői bérlője és bármely más Azure AD-bérlő között
* Kezeli a felhasználói hozzájárulásukat a jogkivonat-kérelmek ideje alatt, és megkönnyíti a bérlők közötti alkalmazások dinamikus kiépítés

A **jóváhagyás** az a folyamat, amelynek során az erőforrás-tulajdonos engedélyt ad egy ügyfélalkalmazás számára a védett erőforrások eléréséhez, az adott engedélyek alatt az erőforrás tulajdonosának nevében. Microsoft Identity platform:

* Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
* Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

## <a name="multi-tenant-apps"></a>Több-bérlős alkalmazások

A Microsoft Identity platformban az [Application Object](developer-glossary.md#application-object) egy alkalmazást ír le. A központi telepítés ideje alatt a Microsoft Identity platform az Application objektumot használja tervrajzként egy [egyszerű szolgáltatásnév](developer-glossary.md#service-principal-object)létrehozásához, amely egy adott alkalmazás konkrét példányát jelöli a címtárban vagy a bérlőn belül. Az egyszerű szolgáltatásnév azt határozza meg, hogy az alkalmazás mit tud valójában egy adott címtárban, ki használhatja azt, milyen erőforrásokhoz férhet hozzá, és így tovább. A Microsoft Identity platform egy egyszerű szolgáltatásnevet hoz létre egy alkalmazás-objektumból a [beleegyező](developer-glossary.md#consent)módon.

Az alábbi ábrán egy egyszerűsített Microsoft Identity platform kiépítési folyamata látható. Két bérlőt mutat be: *A* és *B*.

* A *bérlő* az alkalmazás tulajdonosa.
* A *B bérlő* az alkalmazást egy egyszerű szolgáltatásnév használatával hozza létre.

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A B bérlő felhasználója megpróbál bejelentkezni az alkalmazásba, az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A felhasználói hitelesítő adatok beszerzése és ellenőrzése hitelesítéssel történik.
1. A rendszer megkéri a felhasználót, hogy adjon hozzáférést az alkalmazásnak a B bérlőhöz való hozzáféréshez.
1. A Microsoft Identity platform az A bérlő Application objektumát használja tervként egy egyszerű szolgáltatásnév létrehozásához a B bérlőben.
1. A felhasználó megkapja a kért jogkivonatot.

Ezt a folyamatot további bérlők esetében is megismételheti. Az A bérlő megőrzi az alkalmazás tervét (Application Object). Az összes többi bérlő felhasználói és rendszergazdái, akikkel az alkalmazás beleegyezik, folyamatosan szabályozhatja, hogy az alkalmazás milyen műveleteket végezhet el az egyes bérlők megfelelő egyszerű szolgáltatásán keresztül. További információ: [alkalmazás-és szolgáltatásnév-objektumok a Microsoft Identity platformon](app-objects-and-service-principals.md).

## <a name="next-steps"></a>További lépések

A hitelesítési és engedélyezési alapismeretekkel kapcsolatos egyéb témakörökhöz:

* Tekintse meg a [hitelesítés](authentication-vs-authorization.md) és engedélyezés című témakört, amely a Microsoft Identity platform hitelesítésének és engedélyezésének alapvető fogalmait ismerteti.
* Tekintse meg a [biztonsági jogkivonatokat](security-tokens.md) , hogy megtudja, hogyan használhatók a hozzáférési tokenek, a frissítési tokenek és az azonosító tokenek a hitelesítés és az engedélyezés során.
* Az [alkalmazás bejelentkezési folyamata](app-sign-in-flow.md) című témakörben megismerheti a webes, asztali és mobil alkalmazások bejelentkezési folyamatát a Microsoft Identity platformon.

További információ az alkalmazás modelljéről:

* Ismerje meg, [Hogyan és miért adja hozzá az alkalmazásokat az Azure ad](active-directory-how-applications-are-added.md) -hez, ha további információt szeretne a Microsoft Identity platform alkalmazás-és egyszerű szolgáltatásairól.
* Az egybérlős alkalmazásokkal és a több-bérlős alkalmazásokkal kapcsolatos további információkért tekintse meg a [bérleti Azure Active Directory](single-and-multi-tenant-apps.md) .
* A [Azure Active Directory B2C dokumentációjában](https://docs.microsoft.com/azure/active-directory-b2c) talál további információt arról, hogy az Azure ad hogyan is biztosít Azure Active Directory B2C, hogy a szervezetek a felhasználók, jellemzően ügyfelek, közösségi identitások, például Google-fiók használatával jelentkezzenek be.
