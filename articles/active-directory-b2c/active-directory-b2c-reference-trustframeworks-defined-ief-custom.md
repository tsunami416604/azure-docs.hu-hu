---
title: "Az Azure Active Directory B2C: Hivatkozás - keretrendszerek megbízható |} Microsoft Docs"
description: "Azure Active Directory B2C egyéni házirendek és az identitás élmény keretrendszer témája"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 4e2de9c4d1c0f92970911e132fffaacbd01d9ad0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Az Azure AD B2C identitás élmény keretrendszerrel megbízhatósági keretrendszerek meghatározása

Az Azure Active Directory B2C identitás élmény keretében használó egyéni házirendekkel (Azure AD B2C-vel), adja meg a szervezet egy központosított szolgáltatással. Ez a szolgáltatás révén egyszerűbben egyik fontos a nagy közösségi identitás-összevonási. Egyetlen megbízhatósági kapcsolat és egy egyetlen metaadatok exchange összetettségét csökken.

Az Azure AD B2C egyéni házirendek, amelyek a identitás élmény keretrendszer ahhoz, hogy válaszoljon a következő kérdésekre:

- Mik a jogi, biztonsági, adatvédelmi és, hogy be kell tartani, adatvédelmi szabályzatok?
- Kik tartoznak, az ügyfelek és a folyamatok egy akkreditált résztvevő váljon Mik?
- Kik a akkreditált információk Identitásszolgáltatók (más néven: "jogcímek szolgáltatók"), és hogy mit kínálnak?
- A meghatalmazott függő entitások (és szükség esetén mi szükséges a)?
- Mik azok a műszaki "on"vezetéken együttműködési követelményeket a résztvevők?
- Mik a működési "futtatókörnyezet" szabályokat, amelyek kényszerítettnek kell lennie a digitális identitásokat információcsere?

Ezeket a kérdéseket válaszolja, az Azure AD B2C egyéni házirendek, amelyek az identitás élmény keretrendszer használja a megbízható keretrendszer (TF) szeretne létrehozni. Mérlegeljük, ez a szerkezet és akkor biztosít.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>A megbízható keretrendszer és az összevonási felügyeleti foundation ismertetése

A megbízható keretrendszer az azonosító, biztonsági, adatvédelmi és adatvédelmi szabályzatok, amelyhez meg kell felelnie a érdeklő közösségi résztvevők írásbeli meghatározásához.

Összevont identitás alap biztosít végfelhasználói identitás megbízhatósági Internet léptékű elérésében. Delegálásával át harmadik félnek az Identitáskezelés, a felhasználó egyetlen digitális identitásokat újrahasználhatók több függő entitást.  

Identitás megbízhatósági szükséges, hogy identitás-szolgáltatóktól (IdPs) és az attribútum szolgáltatók (AtPs) felelnie bizonyos biztonsági, adatvédelmi, és működési házirendek és eljárásokat.  Ha azok nem hajtható végre a közvetlen ellenőrzések, függő entitások (RPs) ki kell dolgoznia megbízhatósági kapcsolata a IdPs és azok választhatják a AtPs.  

A fogyasztók és szolgáltatók digitális azonosító adatok száma növekedésével is nehézkes páros felügyeleti ezek megbízhatósági kapcsolatban, vagy még akkor is, amely a hálózati kapcsolathoz szükséges műszaki metaadatok páros exchange folytatja.  Összevonási hubok értek csak korlátozott sikeres, a problémák megoldásához.

### <a name="what-a-trust-framework-specification-defines"></a>Milyen a keretrendszer megbízhatósági specifikációval meghatározása
TFs a nyitott identitás Exchange (OIX) megbízható keretrendszer modell, ahol minden közösségi iránt, egy adott TF specifikációja szabályozzák linchpins. Meghatározza, hogy ilyen TF leírást:

- **A Közösség definíciójával egyik fontos biztonsági és adatvédelmi metrikák:**
    - A szintjei közül (LOA), amelyek a résztvevők; által kínált/szükséges például egy rendezett sorozata az vetett bizalmat minősítések digitális azonosító adatok hitelességének.
    - A szintek, amelyek a résztvevők; által kínált/szükséges védelem (LOP) például egy rendezett sorozata az abban, hogy minősítések, amely kezeli a közösségi érdeklő résztvevők digitális azonosító adatok védelméről.

- **A digitális identitásokat információk leírása, amely rendelkezik felajánlott/szükséges résztvevők által**.

- **A műszaki házirendek digitális azonosító adatok tartozó, és így LOA és LOP méri. Ezek a házirendek írásbeli rendszerint tartalmazza a következő kategóriákba tartozó házirendek:**
    - Identitás-ellenőrző a házirendek, például: *erősen hogyan van egy személy azonosító adatok vetted?*
    - Biztonsági házirendek, például: *információk sértetlenségét és bizalmasságát védett erősen hogyan vannak?*
    - Adatvédelmi szabályzatok, például: *ellenőrző a felhasználó rendelkezik személyes azonosításra alkalmas adatokat (PII) keresztül*?
    - Túlélést házirendek, például: *Ha egy szolgáltató működése megszűnik, hogyan működik folytonosságának és védelem személyazonosításra alkalmas függvény?*

- **Éles üzemi pontjának és digitális azonosító adatok felhasználása a műszaki profilok. Ezeket a profilokat a következők:**
    - Hatókör felületek, amelyek digitális azonosító adatok esetében érhető el a megadott LOA.
    - A tömörített együttműködési műszaki követelményei.

- **A különböző szerepkörökben, amely a Közösségben résztvevő hajthat végre, és ezek a szerepkörök teljesítéséhez szükséges megfelel leírását.**

Így a TF specifikációval szabályozza, hogyan történik a azonosító adatok a Közösség egyik fontos a résztvevők között: a függő entitások, identitáskezelési és attribútum szolgáltatók és attribútum hitelesítők.

A TF specifikációval egy vagy több dokumentumot, amely a Közösség, amely szabályozza a helyességi feltétel érdeklő és digitális azonosító adatok belül a Közösség által felhasznált irányításhoz referenciaként szolgálnak. Egy dokumentált szabályzatokat és eljárásokat úgy tervezték, hogy a digitális identitásokat érdeklő egy Közösség tagjai közötti internetes tranzakciók használt megbízhatónak.  

Más szóval a TF specifikációval életképes összevont identitás-ökoszisztéma létrehozásához a közösségi szabályokat határozza meg.

Jelenleg nincs ilyen megközelítés a juttatás széles körű megállapodás. Nem kétséges, amely megbízhatósági keretrendszer specifikációk digitális identitásokat ökoszisztéma ellenőrizhető biztonsági, megbízhatósági és adatvédelmi jellemzőkkel, ami azt jelenti, hogy felhasználhatók között több csoport egyik fontos fejlesztésének megkönnyítése.

Az adott ok, az Azure AD B2C egyéni házirendek, amelyek az identitás-élmény keretrendszerével a meghatározás alapjaként használ a saját adatok egy TF az együttműködés elősegítése érdekében.  

Az Azure AD B2C egyéni házirendeket használó identitás élmény keretében egy TF specifikációjának emberi és géppel olvasható adatok keverékével jelölik. Ebben a modellben (általában szakaszok több felé irányulnak irányítás) egyes részeit közzétett biztonsági és adatvédelmi szabályzat dokumentációját és a kapcsolódó eljárások hivatkozásként jelennek meg (ha van ilyen). Más szakaszok részletesen leírja a konfigurációs metaadatai és futásidejű szabályok, amelyek egyszerűbbé teszik a működési automation.

## <a name="understand-trust-framework-policies"></a>Keretrendszer megbízhatósági házirendek ismertetése

Tekintetében végrehajtására a TF megadását áll házirendjei, amelyek lehetővé teszik a teljes felügyeletet gyakorolhat az identitás-működést és a feladatait.  Az identitás élmény keretrendszer használó Azure AD B2C egyéni házirendek lehetővé teszik a hozhatnak létre, és hozzon létre saját TF keresztül ilyen deklaratív házirendek meghatározására és konfigurálása:

- A hivatkozás vagy hivatkozásai, amelyek meghatározzák az összevont identitás ökoszisztéma a Közösség, amely a TF vonatkozik. Azok a TF dokumentációjára mutató hivatkozásokat. A (előre meghatározott) működési "futtatókörnyezet" szabály, vagy a felhasználó útvonal be, amely automatizálásához és/vagy az exchange és a jogcímek használatát szabályozza. Ezek az felhasználói utak társítva egy LOA (és egy LOP). Egy házirend ezért rendelkezhet felhasználói utak rendelkező különböző idő (és LOPs).

- Az identitás- és attribútum, vagy a jogcímek szolgáltatókat, a közösségi iránt érdeklődik, és a műszaki profilokat támogatják-e a hozzájuk kapcsolódó (a sávon kívüli) LOA/LOP akkreditációs együtt.

- Az integráció attribútum hitelesítők vagy jogcímszolgáltatóktól.

- A függő entitások a Közösség (megállapítás).

- A résztvevők közötti hálózati kommunikációk létrehozó metaadatait. Ezek a metaadatok a műszaki profilok használt tranzakció során történő bekötését végző "on"közti együttműködés a függő entitáshoz, és más közösségi résztvevők között.

- Ha bármely protokoll átalakítása (például a SAML-alapú, az OAuth2, WS-Federation, és az OpenID Connect).

- A hitelesítési követelményeknek.

- A többtényezős vezénylési, ha van ilyen.

- A jogcím elérhető és a résztvevők érdeklő közösségi leképezései közös sémát.

- Minden a jogcímek átalakítása, és a lehetséges minimalizálásáról ebben a környezetben, az exchange és a jogcímek használatának fenntartása érdekében.

- A kötés és a titkosítás.

- A jogcím-tárolás.

### <a name="understand-claims"></a>Jogcímek ismertetése

> [!NOTE]
> Együttesen hivatkozunk azon lehetséges típusait, előfordulhat, hogy felcserélhetők "jogcímként" azonosító adatok: a végfelhasználók hitelesítő adatok, a identitás vizsgálata során, a kommunikációs eszköz, a fizikai hely, jogcímek személyes azonosítási attribútumok, és így tovább.  
>
> Használjuk "jogcímek"--ahelyett, hogy "attribútum" – mivel az online tranzakció, ezen adatok összetevők nincsenek tények, a függő entitás által közvetlenül ellenőrizhető. Ahelyett, hogy fontosságúak helyességi feltételek vagy a jogcímeket, kapcsolatos tények, amelynek a függő entitás ki kell dolgoznia elegendő abban, hogy a végfelhasználó kért tranzakcióazonosító megadását.  
>
> Mivel az Azure AD B2C egyéni házirendek, amelyek az identitás-élmény keretrendszerével egyszerűsítése érdekében minden típusú digitális azonosító adatok cseréjének függetlenül attól, hogy a mögöttes protokollt egységes módon is használjuk "jogcímek" kifejezés a felhasználói hitelesítés vagy attribútum lekérési meghatározva.  Hasonlóképpen, a kifejezés "Jogcímszolgáltatók" használjuk együttesen Identitásszolgáltatók, attribútum szolgáltatók és attribútum hitelesítők utal, ha azt nem szeretné, hogy a speciális funkciókhoz megkülönböztetésére.   

Így azok szabályozzák, hogyan történik a azonosító adatok egy függő entitás, identitáskezelési és attribútum szolgáltatók és attribútum hitelesítők között. Ezek szabályozhatja, hogy mely identitás és attribútum szolgáltatók a következők: egy függő entitás hitelesítéshez szükséges. Akkor kell tekinteni tartományspecifikus nyelv (DSL), ez azt jelenti, hogy egy számítógép nyelv, amely rendelkezik egy adott alkalmazás tartomány öröklési, kifejezetten *Ha* utasítások, polimorfizmus.

Ezek a házirendek az Azure AD B2C egyéni házirendek identitás élmény keretében, ami a TF szerkezet géppel olvasható részét alkotják. A műveleti adatokat, beleértve a jogcím-szolgáltatók metaadatok és műszaki profilok, jogcímek sémadefiníciók, jogcímek átalakítása funkciók és felhasználói útvonal be, amely lehetővé teszi működési vezénylési és automatizálás kitölti tartalmaznak.  

Akkor feltételezhető, hogy lehet *élő dokumentumok* mert van esély arra, hogy azok tartalmát keresztül idő vonatkozó aktív résztvevők deklarált azokban a házirendekben változik. A lehetséges, amely a használati feltételek rendelkező módosíthatja is van.  

Összevonási telepítési és karbantartási jelentősen egyszerűsített folyamatos megbízható és a csatlakozási újrakonfigurálás a függő entitások védelmi különböző jogcím-szolgáltatók/hitelesítők csatlakoztassa, vagy hagyja meg (a Közösség által képviselt) által megadott szabályzatok.

Együttműködési lehetőség egy másik jelentős kihívás. További jogcímek szolgáltatók/hitelesítők integrálni kell, mert a függő entitások valószínűleg nem támogatja a szükséges protokollokat. Az Azure AD B2C egyéni házirendek megoldhatja a problémát, ipari szabványnak számító protokollokat támogató és kérelmek transzponálása, amikor a függő entitások és attribútum szolgáltatók nem támogatja ugyanazt a protokollt az adott felhasználó utak alkalmazásával.  

Felhasználói utak közé tartozik a protokoll-profilokat és történő bekötését végző "on"közti együttműködés a függő entitáshoz, és más résztvevők között használt metaadatok. Identitás információs exchange kérelem/válasz üzenetet a TF megadását részeként közzétett házirendekkel való megfelelőség kényszerítése a alkalmazott működési futásidejű szabályok is vonatkoznak. A felhasználó utak lényege a felhasználói élmény tetreszabását billentyűt. Azt is világítsa a rendszer a protokoll szintjén működéséről.

Ennek alapján függő entitás alkalmazásai és portálokról esik szó, attól függően, hogy a környezet elindíthat az Azure AD B2C egyéni házirendek, amelyek kihasználja a identitás élmény keretrendszer átadja egy adott házirend nevét és pontosan a viselkedést és információcsere beolvasása szeretnék muss, fuss vagy kockázata nélkül.
