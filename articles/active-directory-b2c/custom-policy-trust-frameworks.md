---
title: A Azure Active Directory B2C referenciái – megbízhatósági keretrendszerek | Microsoft Docs
description: A Azure Active Directory B2C egyéni szabályzatokkal és az identitás-élmény keretrendszerével kapcsolatos témakörök.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ffa25eec9c4b668f428d8e8b5a780a5fe4625a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188885"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Megbízhatósági keretrendszerek meghatározása Azure AD B2C Identity Experience Framework-keretrendszerrel

Azure Active Directory B2C (Azure AD B2C) a személyazonossági élmény keretrendszert használó egyéni házirendek központosított szolgáltatással biztosítják a szervezetét. Ez a szolgáltatás csökkenti az identitás-összevonás összetettségét egy nagy közösség érdeklődési területén. Az összetettséget egyetlen megbízhatósági kapcsolat és egy metaadat-csere is csökkenti.

Azure AD B2C egyéni szabályzatok az identitási élmény keretrendszert használják a következő kérdések megválaszolásához:

- Milyen jogi, biztonsági, adatvédelmi és adatvédelmi szabályzatokat kell betartani?
- Kik a partnerek, és Mik azok a folyamatok, amelyek egy akkreditált résztvevőnek válnak?
- Kik az akkreditált identitás-szolgáltatók (más néven "jogcím-szolgáltatók"), és mit kínálnak?
- Kik az akkreditált függő entitások (és opcionálisan mit is igényelnek)?
- Mik a technikai "a drót" együttműködési követelményei a résztvevők számára?
- Milyen működési "futtatókörnyezeti" szabályokat kell kikényszeríteni a digitális azonosítók adatainak cseréjére?

Ezen kérdések megválaszolásához Azure AD B2C az Identity Experience Framework-t használó egyéni szabályzatokat a megbízhatósági keretrendszer (TF) szerkezet használatával. Tekintsük át ezt a konstrukciót és azt, amit biztosít.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>A megbízhatósági keretrendszer és az összevonási felügyeleti alaprendszer ismertetése

A megbízhatósági keretrendszer az identitás, a biztonság, az adatvédelem és az adatvédelmi szabályzatok írásos specifikációja, amelyeknek meg kell felelniük a Közösségen belüli résztvevőknek.

Az összevont identitás lehetővé teszi a végfelhasználói identitások Internet-méretezéssel való megvalósításának alapját. Az Identitáskezelés harmadik félnek való delegálásával a végfelhasználók egyetlen digitális identitása újra felhasználható több függő féllel.

Az Identity Assurance megköveteli, hogy az Identity Providers (IDP) és az attribútum-szolgáltatók (AtPs-EK) megfeleljenek az egyes biztonsági, adatvédelmi és üzemeltetési házirendeknek és eljárásoknak.  Ha nem tudnak közvetlen ellenőrzéseket végezni, a függő entitások (RPs) megbízhatósági kapcsolatot kell kifejleszteniük a IDP és a AtPs.

Mivel a felhasználók száma és a digitális személyazonossággal kapcsolatos információk mennyisége növekszik, nehéz tovább páros a megbízhatósági kapcsolatok felügyeletét, vagy akár a hálózati kapcsolathoz szükséges technikai metaadatok páros cseréjét is.  Az összevonási központok csak korlátozott sikert értek el ezen problémák megoldásakor.

### <a name="what-a-trust-framework-specification-defines"></a>A megbízhatósági keretrendszer specifikációjának meghatározása
A TFs az Open Identity Exchange (OIX) megbízhatósági keretrendszer modelljének linchpins, amelyben minden érdekelt Közösségre egy adott TF-specifikáció vonatkozik. Az ilyen TF-specifikációk a következőket határozzák meg:

- **A Közösség számára fontos biztonsági és adatvédelmi mérőszámok a következő meghatározásával:**
    - A résztvevők által kínált/szükséges megbízhatósági szintek (LOA); például a megbízhatósági minősítések rendezett halmaza a digitális azonosítók adatainak hitelességéhez.
    - A résztvevők által kínált/szükséges védelmi szintek (LOP); például egy rendezett megbízhatósági minősítést a Közösségen belüli résztvevők által kezelt digitális identitások adatainak védelmére.

- A **résztvevők által felkínált vagy szükséges digitális azonosító információk leírása**.

- **A digitális azonosító adatok előállítására és felhasználására vonatkozó technikai szabályzatok, így az LOA és a LOP méréséhez. Ezek az írott szabályzatok jellemzően a következő kategóriákat tartalmazzák:**
    - Személyazonosság-ellenőrző szabályzatok, például: *milyen mértékben van a személy személyazonossági adatai?*
    - Biztonsági házirendek, például az *adatok integritásának és titkosságának védelme?*
    - Adatvédelmi szabályzatok, például: *milyen szabályozást végeznek a felhasználók személyes azonosításra alkalmas adatokkal*?
    - Túlélési szabályzatok, például: *Ha a szolgáltató megszűnik a műveletek, hogyan végezheti el a személyes adatok folytonosságát és védelmét?*

- **A digitális azonosító adatok előállítására és felhasználására szolgáló technikai profilok. Ezek a profilok a következők:**
    - A hatókör-felületek, amelyekhez a rendszer a megadott LOA-ben elérhetővé válik a digitális azonosító információi.
    - Technikai követelmények a vezetékes együttműködéshez.

- **A Közösség résztvevői által elvégezhető különböző szerepkörök leírása, valamint a szerepkörök teljesítéséhez szükséges képzettségek.**

Így a TF specifikáció szabályozza, hogyan történik az identitási adatok cseréje a Közösség résztvevői között: függő entitások, identitás-és attribútum-szolgáltatók, valamint attribútumok ellenőrzői.

A TF-specifikáció egy vagy több olyan dokumentum, amely referenciául szolgál az olyan Közösség irányítására, amely a digitális személyazonossági információk Közösségen belüli érvényesítését és felhasználását szabályozza. Ez a szabályzatok és eljárások dokumentált készlete, amelynek célja, hogy megbízhatósági kapcsolatot hozzon létre a Közösség érdeklődési körének online tranzakciói számára használt digitális identitásokban.

Ez azt jelenti, hogy a TF specifikáció meghatározza az életképes összevont identitások ökoszisztémájának létrehozási szabályait egy Közösség számára.

Jelenleg széleskörű megállapodás van az ilyen megközelítés előnyeiről. Nem kétséges, hogy a megbízhatósági keretrendszer specifikációi megerősítik a digitális identitás-ökoszisztémák fejlesztését a ellenőrizhető biztonsági, megbízhatósági és adatvédelmi jellemzőkkel, ami azt jelenti, hogy több Közösség érdeklődésére is felhasználhatók.

Ezért Azure AD B2C az Identity Experience Framework-t használó egyéni szabályzatok a specifikációt használják a TF számára az együttműködési képesség elősegítése érdekében.

Azure AD B2C az identitási élmény keretrendszerét kihasználó egyéni szabályzatok az emberi és a géppel olvasható adatok keverékéből álló TF-specifikációnak minősülnek. A modell egyes részeit (általában az irányítás irányába jobban igazodó szakaszt) a közzétett biztonsági és adatvédelmi szabályzatokra mutató hivatkozásként, valamint a kapcsolódó eljárásokkal (ha vannak) jelölik. Más szakaszok részletesen ismertetik az operatív automatizálást megkönnyítő konfigurációs metaadatokat és futtatókörnyezeti szabályokat.

## <a name="understand-trust-framework-policies"></a>A megbízhatósági keretrendszer házirendjeinek ismertetése

A megvalósítás szempontjából a TF specifikáció olyan szabályzatokból áll, amelyek lehetővé teszik az identitás-viselkedés és a tapasztalatok teljes körű felügyeletét.  Az Identity Experience Framework-t használó egyéni házirendek lehetővé teszik, hogy saját TF-et hozzanak létre és hozzon létre az olyan deklaratív házirendekkel, amelyek meghatározhatják és konfigurálhatják a következőket: Azure AD B2C

- A Közösség összevont identitási ökoszisztémáját meghatározó dokumentum-hivatkozás vagy hivatkozások, amely a TF-hoz kapcsolódik. A TF dokumentációra hivatkoznak. Az (előre definiált) működési futtatókörnyezeti szabályok vagy a felhasználói utazások, amelyek automatizálják és/vagy vezérlik a jogcímek cseréjét és használatát. Ezeket a felhasználói útvonalakat egy LOA (és egy LOP) társítja. A szabályzatok ezért különböző LOAs (és LOPs) rendelkező felhasználói útvonalakat is tartalmazhatnak.

- Az identitás-és attribútum-szolgáltatók, illetve a jogcímek szolgáltatója a Közösségen belül, valamint az általuk támogatott technikai profilok, valamint a hozzájuk kapcsolódó (sávon kívüli) LOA/LOP akkreditáció.

- Az attribútum-ellenőrzők vagy jogcímek szolgáltatóinak integrációja.

- A Közösségen belüli függő felek (következtetéssel).

- A résztvevők közötti hálózati kommunikáció kialakításához szükséges metaadatok. Ezeket a metaadatokat a technikai profilokkal együtt használja a rendszer a forgalomban lévő, a függő entitás és más közösségi résztvevők közötti együttműködésre irányuló forgalomban.

- A protokoll átalakítása, ha van ilyen (például SAML 2,0, OAuth2, WS-Federation és OpenID Connect).

- A hitelesítési követelmények.

- A többtényezős előkészítés, ha van ilyen.

- Egy megosztott séma az összes rendelkezésre álló jogcím számára, és a Közösségen belüli résztvevőkhöz való hozzárendelés.

- A jogcímek átalakítását, valamint a lehetséges adatminimalizálás mértékét a jogcímek cseréjének és használatának fenntartása érdekében.

- A kötés és a titkosítás.

- A jogcím-tároló.

### <a name="understand-claims"></a>Jogcímek ismertetése

> [!NOTE]
> A rendszer közösen hivatkozik a "jogcímek" kifejezéssel kicserélt személyazonossági adatok lehetséges típusaira: a végfelhasználók hitelesítési hitelesítő adataival, a személyazonossággal kapcsolatos adatokkal, a kommunikációs eszközzel, a fizikai hellyel, a személyazonosításra alkalmas attribútumokkal és így tovább.
>
> A "jogcímek" kifejezést használjuk – a "Attributes" kifejezés helyett – mivel az online tranzakciók esetében ezek az adatösszetevők nem olyan tények, amelyeket a függő entitás közvetlenül ellenőrizheti. Ehelyett a jogcímek vagy jogcímek olyan tényekkel kapcsolatosak, amelyekhez a függő entitásnak megfelelő megbízhatóságot kell kialakítania ahhoz, hogy a végfelhasználó által kért tranzakciót megadják.
>
> A "jogcímek" kifejezést is felhasználjuk, mivel Azure AD B2C az identitást használó egyéni szabályzatok úgy vannak kialakítva, hogy a rendszer következetes módon leegyszerűsítse az összes digitális identitási információ cseréjét, függetlenül attól, hogy az alapul szolgáló protokoll definiálva van-e a felhasználói hitelesítéshez vagy az attribútumok lekéréséhez.  Hasonlóképpen a "jogcímek szolgáltatói" kifejezést használjuk, hogy közösen hivatkozzon a személyazonosság-szolgáltatókra, az attribútum-szolgáltatóra és az attribútum-ellenőrzőre, ha nem szeretnénk megkülönböztetni az adott függvények körét.

Így szabályozzák, hogyan történik a személyazonossági adatok cseréje a függő entitások, az identitás-és az attribútum-szolgáltatók, valamint az attribútumok ellenőrzői között. Azt szabályozzák, hogy mely identitás-és attribútum-szolgáltatók szükségesek a függő entitások hitelesítéséhez. Ezeket a tartományra jellemző nyelvnek (DSL) kell tekinteni, azaz egy adott alkalmazás-tartományhoz tartozó, örökléssel, *Ha* az utasítások, a polimorfizmus.

Ezek a szabályzatok a TF-konstrukció géppel olvasható részét képezik Azure AD B2C egyéni szabályzatokban, amelyek az identitási élmény keretrendszerét hasznosítják. Ide tartozik az összes működési adat, beleértve a jogcím-szolgáltatók metaadatait, a technikai profilokat, a jogcímek sémájának definícióit, a jogcím-átalakítási függvényeket és a kitöltött felhasználói útvonalakat az operatív előkészítés és automatizálás elősegítése érdekében.

A rendszer feltételezi, hogy *élő dokumentumoknak* minősülnek, mivel a tartalmakat a szabályzatokban szereplő aktív résztvevőkkel kapcsolatos idő múlásával érdemes megváltoztatni. Az is előfordulhat, hogy a résztvevő feltételei és kikötései változhatnak.

Az összevonás beállítása és karbantartása nagymértékben leegyszerűsíthető a függő entitások folyamatos megbízhatósággal és kapcsolat-újrakonfigurálásával, mint a különböző jogcím-szolgáltatók/ellenőrzők csatlakozása vagy elhagyása (a Közösség által jelölt) a szabályzatok halmaza.

Az együttműködési képesség egy másik jelentős kihívás. A további jogcím-szolgáltatók/ellenőrzők integrálására van szükség, mivel a függő entitások nem valószínű, hogy támogatják a szükséges protokollokat. Azure AD B2C egyéni szabályzatok ezt a problémát az iparági szabványnak megfelelő protokollok támogatásával és a kérések áttelepítésére vonatkozó konkrét felhasználói utazások alkalmazásával oldják meg, ha a függő entitások és az attribútumok szolgáltatói nem támogatják ugyanazt a protokollt.

A felhasználói útvonalak közé tartoznak azok a protokoll-profilok és metaadatok, amelyeket a rendszer a függő entitás és a többi résztvevő között a vezetékeken való együttműködéshez használ. A TF specifikációnak megfelelően vannak olyan operatív futtatókörnyezeti szabályok is, amelyek a közzétett szabályzatoknak való megfelelés érdekében érvényesek az azonosító adatok Exchange-kérelmére és a válaszüzenetekre. A felhasználói élményre vonatkozó elképzelés az, hogy az ügyfél-környezet testreszabása kulcsfontosságú. Emellett fényt derít arra is, hogy a rendszer hogyan működik a protokoll szintjén.

Ezen az alapon a függő entitások és a portálok a kontextustól függően meghívhatnak Azure AD B2C egyéni szabályzatokat, amelyek kihasználják az identitási élmény keretrendszerét egy adott szabályzat nevével, és pontosan a kívánt viselkedést és információcserét rendetlenség, felállás vagy kockázat nélkül.
