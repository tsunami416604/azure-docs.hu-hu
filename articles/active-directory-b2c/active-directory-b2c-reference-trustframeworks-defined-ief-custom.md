---
title: Referencia - megbízhatósági keretrendszereket az Azure Active Directory B2C |} A Microsoft Docs
description: Az Azure Active Directory B2C-vel egyéni szabályzatok és az identitás-kezelőfelületi keretrendszer témája.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4eee1f2c9ca37227ca2cea0ef5e2ff25f6fad828
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443409"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Az Azure AD B2C identitás-kezelőfelületi keretrendszer megbízhatósági keretrendszerek definiálása

Az Azure Active Directory B2C (Azure AD B2C-vel) egyéni szabályzatot, amely használhatja az identitáskezelési keretrendszert is, adjon meg egy központosított szolgáltatásba szervezeténél. Ez a szolgáltatás fontos a nagy közösségi identitás-összevonási bonyolultságát is csökkenti. Az összetettséget egyetlen megbízhatósági kapcsolat és a egy egyetlen metaadatok exchange csökken.

Az Azure AD B2C-vel egyéni szabályzatok használhatja az identitáskezelési keretrendszert is, hogy a következő kérdések megválaszolásával:

- Mik azok a jogi, biztonsági, adatvédelmi és be kell tartani, hogy adatvédelmi szabályzatok?
- Kik az ügyfelek és Mik azok a folyamatok egy akkreditált résztvevő lenni?
- Kik a akkreditált információk Identitásszolgáltatók (más néven: "jogcímek szolgáltatók"), és hogy mit kínálnak?
- Az akkreditált függő entitások, akik (és opcionálisan, mi szükséges a)?
- Mik azok a műszaki "on"átkerül együttműködési követelményeket résztvevői?
- Mik azok a műveleti "runtime" szabályokat, amelyek a digitális azonosító adatok cseréjére vonatkozó kényszerítettnek kell lennie?

Ezek a kérdések megválaszolásához hozhatnak létre az Azure AD B2C-vel egyéni szabályzatok, amelyek a megbízható keretrendszer (TF) identitás-kezelőfelületi keretrendszer használatát. Vegyünk például tento konstruktor, és hogy mire használható.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>A megbízható keretrendszer és az összevonás felügyeleti foundation ismertetése

A megbízható keretrendszer az identitás, biztonsági, adatvédelmi és adatvédelmi szabályzatok, amelyhez meg kell felelnie a fontos közösségi résztvevők írásos meghatározását.

Összevont identitás erőforrásigény végfelhasználói identitás garancia Internet szintjén elérésére. Delegálásával át harmadik félnek az Identitáskezelés, a végfelhasználó egy egyetlen digitális identitás több függő újrahasználhatók.  

Identitás garancia szükséges, hogy az identitásszolgáltató (IDP) és attribútum szolgáltatók (AtPs) formátumhoz meghatározott biztonsági, adatvédelmi, és a működési szabályzatai és.  Közvetlen ellenőrzések nem végezhetik el, ha a függő entitások (RPs) kell fejlesztenie megbízhatósági kapcsolata az identitásszolgáltató és AtPs együttműködve választják.  

A fogyasztók és digitális azonosító adatok szolgáltatók növekedésével sokaságát tekintve nehéz lenne páros kezelését ezek megbízhatósági kapcsolatokat, vagy még hálózati kapcsolat szükséges technikai metaadatok páros exchange továbbra is.  Összevonási hubs el csak korlátozott sikeres, a problémák megoldásához.

### <a name="what-a-trust-framework-specification-defines"></a>Milyen keretrendszer megbízható specifikáció meghatározása
TFs a linchpins a nyitott identitás Exchange (OIX) megbízható keretrendszer modell, ahol minden egyes Közösség fontos egy adott TF specifikációt szabályozzák. Ilyen TF leírást határozza meg:

- **A Közösség hasznos hely meghatározása a biztonság és adatvédelem metrikák:**
    - A szintjei közül (LOA), amelyek a résztvevők; által kínált/szükséges Ha például egy rendezett készlet digitális azonosító adatok hitelességének megbízhatósági minősítése.
    - A szintek, amelyek a résztvevők; által kínált/szükséges védelem (LOP) Ha például egy rendezett készlet megbízhatósági minősítés, amely kezeli a lényeges a Közösségben résztvevő digitális azonosító adatok védelméről.

- **A digitális azonosító adatok, amely rendelkezik felajánlott/kötelező résztvevők leírása**.

- **A technikai házirendek, az éles és az digitális azonosító adatok felhasználását, és így LOA és LOP méréséhez. A megírt házirendek általában a következő kategóriákba tartozó házirendek a következők:**
    - Identitás-ellenőrző a szabályzatok, például: *erősen hogyan van egy személy azonosító adatok megvizsgált?*
    - Biztonsági házirendek, például: *információ sértetlenségét és védett bizalmassági erősen hogyan vannak?*
    - Adatvédelmi szabályzatok, például: *milyen control a felhasználó rendelkezik személyes azonosításra alkalmas adatokat (PII) keresztül*?
    - Túlélést házirendek, például: *szolgáltató működése megszűnik, ha honnan elérhetőségének folytonosságát és a személyazonosításra alkalmas adatok függvény?*

- **A technikai profilok az éles és az digitális azonosító adatok felhasználását. Ezek a profilok a következők:**
    - Hatókör felületek, amelynek digitális azonosító adatok egy megadott LOA címen érhető el.
    - Az átvitel közbeni együttműködés a műszaki követelményeket.

- **A különböző szerepköröket, amelyek a Közösségben résztvevő hajthat végre, és ezek a szerepkörök teljesítéséhez szükséges szoftverösszetevője leírását.**

Így TF specifikáció szabályozza, hogyan történik a azonosító adatok között a résztvevők a Közösség hasznos helyek: függő entitások, identitás- és attribútum szolgáltatók és attribútum hitelesítők.

Egy TF specifikációt egy vagy több dokumentumot, amely a Közösség fontos, hogy szabályozza a helyességi feltétel és a digitális azonosító adatok Közösségen belüli fogyasztásnál a cégirányítási referenciaként szolgálnak. Egy dokumentált szabályzatokat és eljárásokat az online tranzakció-Közösség fontos tagjai között használt digitális identitásokat a megbízhatósági kapcsolatot hoz létre.  

Más szóval TF specifikáció a közösségi egy működőképes összevont identitás ökoszisztéma létrehozására szolgáló szabályokat határozza meg.

Jelenleg nincs ilyen megközelítés a juttatás a széles körű szerződés. Nem kétséges, amely a bizalmi keretrendszer specifikációk digitális identitásokat rendszereit ellenőrizhető biztonsági, megbízhatósági és adatvédelmi jellemzőkkel, ami azt jelenti, hogy azok felhasználhatók a lényeges több logikai fejlesztésének megkönnyítése.

Amely az okból az identitáskezelési keretrendszert használó Azure AD B2C-vel egyéni szabályzatok használja a specifikáció egy TF annak adatreprezentációt alapjául szolgáló együttműködés megkönnyítése érdekében.  

Az Azure AD B2C-vel egyéni szabályzatok, az az identitás-kezelőfelületi keretrendszer képviseli egy emberi és gépi olvasásra alkalmas adatok vegyesen TF specifikációjának. Olyan (általában szakaszokat, amelyek további Dokumentumközpontú cégirányítási felé) Ez a modell részei közzétett biztonsági és adatvédelmi szabályzat dokumentációját és a kapcsolódó eljárások hivatkozásként jelennek meg (ha van). Más szakaszok részletesen leírja a konfigurációs metaadatok és a futtatókörnyezet szabályok, amelyek elősegítik a működési automation.

## <a name="understand-trust-framework-policies"></a>Keretrendszer megbízhatósági házirendek ismertetése

Tekintetében végrehajtására a TF specifikáció házirendjei, amelyek lehetővé teszik a teljes körű, és az identitás viselkedések áll.  Az identitáskezelési keretrendszert használó Azure AD B2C-vel egyéni szabályzatok lehetővé teszik a hozhat létre, és hozzon létre saját TF ilyen deklaratív házirendek meghatározása és konfigurálása:

- A hivatkozás vagy hivatkozásai, amelyek meghatározzák az összevont identitás ökoszisztéma a Közösség, hogy a TF vonatkozik. Azok a TF dokumentációs hivatkozásokat. A (előre meghatározott) működési "runtime" szabályokat, vagy a felhasználói utak, amely automatizálja és/vagy az exchange és a jogcímek használatát szabályozza. Ezek az felhasználói utak társítva egy LOA (és a egy LOP). Egy házirend így is kell az idő (és LOPs) változó felhasználói utak.

- Az identitás- és szolgáltatók, vagy a Jogcímszolgáltatók, a Közösségben, és a technikai profilok mellett, hogy azok vonatkozik (a sávon kívüli) LOA/LOP akkreditálási támogatják.

- Az integráció az attribútum ellenőrzőkkel és a Jogcímszolgáltatók.

- A függő entitások, a közösségi (által következtetésekhez).

- A résztvevők közötti hálózati kommunikáció létrehozó metaadatait. Ezeket a metaadatokat a technikai profilok szolgálnak a tranzakció során történő bekötését végző "on"közti együttműködés a függő entitáshoz, és egyéb közösségi résztvevők között.

- Az protokoll átalakítás, ha bármely (például a SAML, az OAuth2, WS-Federation és az OpenID Connect).

- A hitelesítési követelményeknek.

- A többtényezős vezénylési, ha van ilyen.

- Az összes rendelkezésre álló jogcímek és a résztvevők a lényeges közösségi leképezések közös sémát.

- Jogcímek átalakításokat, ebben a környezetben, az exchange és a jogcímek használatát átcsoportosítása lehetséges minimalizálásával együtt.

- A kötés és -titkosítást.

- A jogcímek tárolót.

### <a name="understand-claims"></a>Jogcímek ismertetése

> [!NOTE]
> Együttesen nevezzük, amely előfordulhat, hogy ki kell cserélni "jogcímként" azonosító adatok lehetséges típusait: személyes azonosítási attribútumok, a végfelhasználók hitelesítő adatok, a identitás vizsgálata során, a kommunikációs eszköz, a fizikai hely jogcímek és így tovább.  
>
> Használjuk "jogcímek"--ahelyett, hogy "attribútumok" – mivel az online tranzakció, ezek az adatok összetevők nem közvetlenül a függő entitás által ellenőrzött tényeket. Inkább azok helyességi feltételek vagy a jogcímek, az információ, amelyhez a függő entitás engedélyezési kért tranzakció a felhasználó megfelelő magabiztossággal kell fejlesztenie tényeket.  
>
> Is használjuk a "jogcímek" kifejezést, mert az identitáskezelési keretrendszert használó Azure AD B2C-vel egyéni szabályzatok úgy tervezték, hogy egységes módon végrehajtott, függetlenül attól, hogy van-e a mögöttes protokollt minden típusú digitális azonosító adatok cseréjének egyszerűsítése a felhasználói hitelesítés vagy attribútum beolvasásához definiálva.  Hasonlóképpen, az előfizetési időszak "Jogcímszolgáltatók" használjuk együttesen identitás-szolgáltatóktól, attribútum szolgáltatók és attribútum hitelesítők hivatkozni, ha nem szeretnénk funkcióik között.   

Így azok szabályozzák, hogyan történik a azonosító adatok egy függő entitás, identitás- és attribútum szolgáltatók és attribútum hitelesítők között. Ezek szabályozhatja, hogy melyik identitás, és attribútum szolgáltatók a következők: egy függő entitás hitelesítéshez szükséges. Azok kell tekinteni a tartomány-specifikus nyelvet (DSL), azt jelenti, egy számítógép nyelv, amely rendelkezik egy adott alkalmazás tartomány az öröklést, specializált *Ha* utasítások, polimorfizmus.

Ezek a szabályzatok az Azure AD B2C-vel egyéni szabályzatok az identitás-kezelőfelületi keretrendszer kihasználva a TF szerkezet gépi olvasásra alkalmas részét képezik. Ezek közé tartozik a működési adatait, például jogcímek szolgáltatók metaadatok és technikai profilok, jogcímek-sémadefiníciók, jogcímek átalakítása funkciók és, amelyek ki vannak töltve működési koordinálási és automatizálási elősegítésére felhasználói utak.  

Ezek a azt feltételezi, hogy *élő dokumentumok* azért van esély arra, hogy azok tartalmát keresztül idő vonatkozó aktív résztvevő deklarált azokban a házirendekben változik. Nincs az esélye, hogy a feltételeket és kikötéseket résztvevő is változhat.  

Összevonási telepítési és karbantartási technológiáikkal nagymértékben egyszerűsített védelmi folyamatos megbízhatóság és a kapcsolat reconfigurations a függő entitások, mint különböző jogcím-szolgáltatók/hitelesítők csatlakozásra és (a Közösség által jelölt) által házirendkészletben.

Együttműködési egy másik komoly nehézségeket. További jogcímek szolgáltatók/hitelesítők integrálni kell, mert a függő entitások nem valószínű, hogy a szükséges protokollokat támogatja. Az Azure AD B2C-vel egyéni szabályzatok megoldhatja a problémát, szabványos protokollok támogatása és transzponálása kérelmeket, amikor a függő entitások és attribútum szolgáltató nem támogatja ugyanazt a protokollt az adott felhasználó Journey alkalmazásával.  

Felhasználói utak közé tartozik a protokoll-profilokat és történő bekötését végző "on"közti együttműködés a a függő entitás és a résztvevők között használt metaadatok. Identitás information exchange kérés/válasz üzeneteket az TF-specifikáció részeként közzétett szabályzatok betartása alkalmazott műveleti modul szabályok is vonatkoznak. Felhasználói utak lényege az testre szabhatja az ügyfélélményt kulcs. Azt is feltárja az hogyan a rendszer a protokoll szintjén működik.

Ennek alapján függő alkalmazásait és portálok is, függően a környezeti meghívása az Azure AD B2C-vel egyéni szabályzatok, az az identitás-kezelőfelületi keretrendszer átadása egy adott házirend nevét és pontosan a viselkedést és információcsere beolvasása muss, fuss vagy kockázat nélkül szeretnének.
