---
title: Az OPC Vault Certificate Management szolgáltatás biztonságos futtatása – Azure | Microsoft Docs
description: Útmutatás az OPC-tár tanúsítványkezelő szolgáltatásának biztonságos futtatásához az Azure-ban és egyéb biztonsági irányelvek a megfontoláshoz.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b08358680793ccdadca27c5f2aa57fbffe89b53a
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973791"
---
# <a name="how-to-run-the-opc-ua-certificate-management-service-securely"></a>Az OPC UA Certificate Management szolgáltatás biztonságos futtatása

Ez a cikk azt ismerteti, hogyan lehet biztonságosan futtatni az OPC UA tanúsítványkezelő szolgáltatást az Azure-ban, és más biztonsági irányelveket is figyelembe kell venni.

## <a name="roles"></a>Szerepkörök

### <a name="trusted-and-authorized-roles"></a>Megbízható és meghatalmazott szerepkörök

Az OPC-tár szolgáltatás úgy van konfigurálva, hogy a különböző szerepkörök számára engedélyezze a szolgáltatás különböző részeinek elérését.

> [!IMPORTANT]
> Az üzembe helyezés során a parancsfájl csak azt a felhasználót adja meg, aki az üzembe helyezési parancsfájlt felhasználóként futtatja az összes szerepkörhöz.
> A szerepkör-hozzárendelést át kell tekinteni az éles üzembe helyezéshez, és megfelelően újra kell konfigurálni az alábbi irányelveket követve.
> Ehhez a feladathoz a szerepkörök és szolgáltatások manuális hozzárendelése szükséges az Azure AD Enterprise Applications portálon.

### <a name="certificate-management-service-roles"></a>Tanúsítványkezelő szolgáltatás szerepkörei

A Service a következő szerepköröket határozza meg:

- **Olvasó**: Alapértelmezés szerint a bérlő bármely hitelesített felhasználója rendelkezik olvasási hozzáféréssel. 
  - Olvasási hozzáférés az alkalmazásokhoz és tanúsítványkérelmek. Listázhatja és lekérdezheti az alkalmazásokat és a tanúsítványokra vonatkozó kérelmeket. Az eszköz felderítési információi és a nyilvános tanúsítványok olvasási hozzáféréssel is elérhetők.
- **Író**: Az író szerepkör hozzá van rendelve egy felhasználóhoz az írási engedélyek bizonyos feladatokhoz való hozzáadásához. 
  - Olvasási/írási hozzáférés az alkalmazásokhoz és a tanúsítványokhoz. Regisztrálhatja, frissítheti és törölheti az alkalmazásokat. Létrehozhat tanúsítvány-kérelmeket, és megszerezheti a jóváhagyott titkos kulcsokat és tanúsítványokat. A titkos kulcsokat is törölhet.
- **Jóváhagyó**: A jóváhagyó szerepkört egy felhasználó rendeli hozzá a tanúsítványkérelmek jóváhagyásához vagy elutasításához. A szerepkör nem tartalmaz más szerepkört.
  - Az OPC-tár Service API-hoz való hozzáférés jóváhagyói szerepkörén kívül a felhasználónak is rendelkeznie kell a kulcs aláírási engedéllyel a Key Vault a tanúsítványok aláírásához.
  - Az író és a jóváhagyó szerepkört különböző felhasználókhoz kell rendelni.
  - A jóváhagyó fő szerepe a tanúsítványkérelmek létrehozásának és elutasításának jóváhagyása.
- **Rendszergazda**: A rendszergazdai szerepkör egy felhasználóhoz van rendelve a Tanúsítványsablonok kezeléséhez. A szerepkör nem támogatja a jóváhagyó szerepkört, de magában foglalja az író szerepkört is.
  - A rendszergazda felügyelheti a tanúsítvány-csoportokat, módosíthatja a konfigurációt, és visszavonhatja az alkalmazás tanúsítványait egy új CRL kiadásával.
  - Ideális esetben a Writer, a jóváhagyó és a rendszergazdai szerepkörök különböző felhasználókhoz vannak rendelve. A további biztonság érdekében a jóváhagyó vagy a rendszergazdai szerepkörrel rendelkező felhasználónak a kulcstartóban a tanúsítványok kiállításához és a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megújításához is rendelkeznie kell a kulcs aláírása engedéllyel.
  - A Service-felügyeleti szerepkörön kívül a szerepkör magában foglalja a következőket is:
    - A HITELESÍTÉSSZOLGÁLTATÓ biztonsági eljárásai megvalósításának felügyeletéért felelős.
    - A tanúsítványok létrehozásának, visszavonásának és felfüggesztésének kezelése. 
    - Titkosítási kulcs életciklus-kezelése (például a kiállító HITELESÍTÉSSZOLGÁLTATÓI kulcsok megújítása).
    - A HITELESÍTÉSSZOLGÁLTATÓT működtető szolgáltatások telepítése, konfigurálása és karbantartása.
    - A szolgáltatások napi működése. 
    - A HITELESÍTÉSSZOLGÁLTATÓ és az adatbázis biztonsági mentése és helyreállítása.

### <a name="other-role-assignments"></a>Egyéb szerepkör-hozzárendelések

A következő szerepköröket is figyelembe kell venni és hozzá kell rendelni a szolgáltatás futtatásakor:

- A tanúsítvány-beszerzési szerződés üzleti tulajdonosa a külső legfelső szintű hitelesítésszolgáltatóval (abban az esetben, ha a tulajdonos külső hitelesítésszolgáltatótól vásárol tanúsítványokat, vagy egy külső HITELESÍTÉSSZOLGÁLTATÓhoz alárendelt HITELESÍTÉSSZOLGÁLTATÓT üzemeltet).
- A hitelesítésszolgáltató fejlesztése és érvényesítése.
- A naplózási rekordok áttekintése.
- A HITELESÍTÉSSZOLGÁLTATÓ támogatását, illetve a fizikai és a Felhőbeli létesítmények felügyeletét segítő személyzet, de a HITELESÍTÉSSZOLGÁLTATÓ műveleteinek elvégzéséhez közvetlenül nem megbízható szerepkört kell megadni. A jogosult szerepkörrel rendelkező személyek készletét is dokumentálni kell.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>A megbízható és a meghatalmazott szerepkörök tagságát évente felül kell vizsgálni

A megbízható és a meghatalmazott szerepkörök tagságát legalább negyedévente felül kell vizsgálni ahhoz, hogy az egyes szerepkörökben lévő személyek (manuális folyamatokhoz) vagy szolgáltatás-identitások (automatizált folyamatok esetében) a minimumra legyenek beállítva.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>A tanúsítvány-kiállítási folyamatnak ki kell kényszeríteni a szerepkör elkülönítését a tanúsítvány kérelmezője és a jóváhagyó között

A tanúsítvány-kiállítási folyamatnak ki kell kényszeríteni a szerepkör elkülönítését a tanúsítvány kérelmezője és a tanúsítvány-jóváhagyó szerepkörök (személyek vagy automatizált rendszerek) között. A tanúsítvány kiállítását jóvá kell hagynia egy tanúsítvány-jóváhagyó szerepkörrel, amely ellenőrzi, hogy a tanúsítvány kérelmezője jogosult-e tanúsítványok beszerzésére. A tanúsítvány-jóváhagyó szerepkörrel rendelkező személyeknek formálisan jogosult személynek kell lenniük.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>A Kiemelt szerepkörök felügyeletének korlátoznia kell a hozzáférést, és negyedévente felül kell vizsgálnia azokat

A Kiemelt szerepkörök hozzárendelését, például a rendszergazdák és a jóváhagyó csoport tagságának engedélyezését, korlátozott számú jogosult személyre kell korlátozni. Az emelt szintű szerepkör módosításainak 24 órán belül vissza kell vonnia a hozzáférést. Végül a Kiemelt szerepkör-hozzárendeléseket negyedévente felül kell vizsgálni, és a szükségtelen vagy lejárt hozzárendeléseket el kell távolítani.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>A Kiemelt szerepköröknek kétfaktoros hitelesítést kell használniuk

A többtényezős hitelesítést (kétfaktoros hitelesítés, MFA vagy TFA) a jóváhagyó és a rendszergazdák interaktív bejelentkezésére kell használni a szolgáltatásban.

## <a name="certificate-service-operation-guidelines"></a>A Certificate Service üzemeltetési iránymutatásai

### <a name="operational-contacts"></a>Operatív névjegyek

A Certificate Service-nek naprakész biztonsági válaszszabály szükséges a fájlra vonatkozóan, amely részletes működési incidensi válaszokat tartalmaz.

### <a name="security-updates"></a>Biztonsági frissítések

Minden rendszert folyamatosan figyelni és frissíteni kell a legújabb biztonsági frissítések/javítások megfelelőségével.

> [!IMPORTANT]
> Az OPC-tároló szolgáltatás GitHub-tárháza folyamatosan frissül a biztonsági javításokkal. A GitHubon lévő frissítéseket figyelni kell, és a frissítéseket rendszeres időközönként alkalmazni kell a szolgáltatásra.

### <a name="security-monitoring"></a>A biztonság monitorozása

Feliratkozás a megfelelő biztonsági figyelésre, például egy központi figyelési megoldásra való feliratkozással (például Azure Security Center, O365-figyelési megoldás), és megfelelően konfigurálja, hogy a biztonsági események továbbítva legyenek a figyelési megoldás.

> [!IMPORTANT]
> Alapértelmezés szerint az OPC-tár szolgáltatás üzembe helyezése az [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) figyelési megoldásként történik. A Azure Security Centerhoz hasonló biztonsági [](https://azure.microsoft.com/services/security-center/) megoldás hozzáadása kifejezetten ajánlott.

### <a name="assess-security-of-open-source-software-components"></a>A nyílt forráskódú szoftverek összetevőinek biztonságának felmérése

A terméken vagy szolgáltatáson belül használt összes nyílt forráskódú összetevőnek szabadnak kell lennie a mérsékelt vagy nagyobb biztonsági sebezhetőségtől.

> [!IMPORTANT]
> Az OPC-tároló szolgáltatás GitHub-tárháza ellenőrzi az összes összetevőt a biztonsági rések folyamatos integrációja során. A GitHubon lévő frissítéseket figyelni kell, és a frissítéseket rendszeres időközönként alkalmazni kell a szolgáltatásra.

### <a name="maintain-an-inventory"></a>Leltár karbantartása

Az eszközök leltározását minden üzemi gazdagép (beleértve az állandó virtuális gépeket is), az eszközök, az összes belső IP-címtartomány, a VIP-cím és a nyilvános DNS-tartománynevek számára kell fenntartani. A leltárt a rendszer, az eszköz IP-címe, a VIP vagy a nyilvános DNS-tartomány hozzáadásával vagy eltávolításával 30 napon belül frissíteni kell.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Az Azure OPC-tároló alapértelmezett üzemi üzembe helyezésének leltára: 

Az **Azure**-ban:
- **App Service terv**: App Service-csomag a Service hosts szolgáltatáshoz. Alapértelmezett S1.
- **App Service** a Service-hez: Az OPC Vault Service-gazdagép.
- **App Service** a minta alkalmazáshoz: Az OPC-tár minta alkalmazás-gazdagépe.
- Kulcstartó **standard**: A webszolgáltatások titkainak és Cosmos DB kulcsainak tárolásához.
- Kulcstartó **Premium**: A kiállító HITELESÍTÉSSZOLGÁLTATÓ kulcsainak üzemeltetése az aláírási szolgáltatáshoz a tár konfigurációja és az alkalmazás titkos kulcsainak tárolása érdekében.
- **Cosmos db**: Az alkalmazás-és tanúsítványkérelmek adatbázisa. 
- **Application Insights**: (nem kötelező) figyelési megoldás webszolgáltatásokhoz és alkalmazásokhoz.
- **AzureAD-alkalmazás regisztrálása**: A minta alkalmazás, a szolgáltatás és a peremhálózati modul regisztrálása.

A Cloud Services esetében dokumentálni kell a szolgáltatás üzembe helyezéséhez használt összes állomásnevet, erőforráscsoportot, erőforrás-nevet, előfizetés-azonosítót és TenantId. 

**IoT Edge** vagy helyi IoT Edge- **kiszolgálón**:
- **OPC**-tár IoT Edge modulja: A gyári hálózati OPC UA globális felderítési kiszolgáló támogatásához. 

A IoT Edge eszközökön a gazdagépeket és az IP-címeket dokumentálni kell. 

### <a name="document-the-certification-authorities-cas"></a>A hitelesítésszolgáltatók dokumentálása (CAs)

A HITELESÍTÉSSZOLGÁLTATÓI hierarchia dokumentációjának tartalmaznia kell minden olyan működő hitelesítésszolgáltatót, amely tartalmazza az összes kapcsolódó alárendelt hitelesítésszolgáltatót, a szülő hitelesítésszolgáltatót és a legfelső szintű hitelesítésszolgáltatót, még akkor is, ha azokat nem a szolgáltatás kezeli. A formális dokumentáció helyett az összes nem lejárt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány teljes készletét lehet megadni.

> [!IMPORTANT]
> Az OPC-tár minta alkalmazása a szolgáltatásban felhasznált és a szolgáltatásban előállított összes tanúsítvány letöltését teszi lehetővé a dokumentációban.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>A kiállított tanúsítványok dokumentálása minden hitelesítésszolgáltató (CAs) szerint

Az elmúlt 12 hónapban kiállított összes tanúsítvány teljes készletét meg kell adni a dokumentációhoz.

> [!IMPORTANT]
> Az OPC-tár minta alkalmazása a szolgáltatásban felhasznált és a szolgáltatásban előállított összes tanúsítvány letöltését teszi lehetővé a dokumentációban.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Az SOP dokumentálása a titkosítási kulcsok biztonságos törléséhez

A kulcs törlése csak ritkán történhet a HITELESÍTÉSSZOLGÁLTATÓ élettartama során, ezért a felhasználó nem rendelkezik a kulcstároló tanúsítványának törléséhez szükséges jogosultsággal, és miért nincsenek elérhető API-k egy kiállítói HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány törléséhez. A hitelesítésszolgáltató titkosítási kulcsainak biztonságos törléséhez használt kézi szabványos működési eljárás csak a kulcstartó Azure Portal való közvetlen elérésével és a kulcstartóban lévő tanúsítvány törlésével érhető el. Ha biztosítani szeretné, hogy az azonnali törlést lehetővé kell tennie, hogy a rendszer [törölje](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) a letiltást.

## <a name="certificates"></a>Tanúsítványok

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>A tanúsítványoknak meg kell felelniük a minimális tanúsítvány-profilnak

Az OPC-tár szolgáltatás olyan online hitelesítésszolgáltatók (CA-k), amelyek végfelhasználói tanúsítványokat bocsátanak ki az előfizetőknek.
Az OPC-tár webszolgáltatás az alapértelmezett implementációban követi ezeket az irányelveket.

- Az összes tanúsítványnak a következő X. 509 mezőket kell tartalmaznia az alább megadott módon:
  - A Version mező tartalmának v3-nek kell lennie. 
  - A serialNumber mező tartalmának legalább 8 bájtnak kell lennie, amely egy FIPS (Federal Information Processing Standards) 140 jóváhagyott véletlenszám-generátort tartalmaz.<br>
    > [!IMPORTANT]
    > Az OPC-tároló sorozatszáma alapértelmezés szerint 20 bájt, és az operációs rendszer titkosítási véletlenszám-generátora alapján érhető el. A véletlenszám-generátor a Windows-eszközökön jóváhagyott FIPS 140, de nem Linux-alapú. Ezt a tényt figyelembe kell venni, amikor olyan szolgáltatás-telepítést választ, amely linuxos virtuális gépeket vagy linuxos Docker-tárolókat használ, amelyeken a mögöttes technológia OpenSSL-je nem engedélyezett a FIPS 140-ben.
  - A issuerUniqueID és a subjectUniqueID mező nem lehet jelen.
  - A végfelhasználói tanúsítványokat az IETF RFC 5280-es verziójának megfelelően meg kell határozni az alapszintű megkötések bővítménnyel.
  - A kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz a pathLenConstraint mezőt 0-ra kell állítani. 
  - A kibővített kulcshasználat bővítménynek jelen kell lennie, és tartalmaznia kell a kibővített kulcshasználati objektumazonosítók (OID) minimális készletét. Nem adható meg az anyExtendedKeyUsage OID (2.5.29.37.0). 
  - A CRL terjesztési pont (CDP) bővítménynek jelen kell lennie a kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványban.<br>
    > [!IMPORTANT]
    > A CRL terjesztési pont (CDP) bővítménye megtalálható az OPC-tároló HITELESÍTÉSSZOLGÁLTATÓI tanúsítványainak területén, az OPC UA-eszközök azonban egyéni metódusokat használnak a CRL-ek terjesztésére.
  - A szolgáltatói információ-hozzáférési bővítménynek szerepelnie kell az előfizetői tanúsítványokban.<br>
    > [!IMPORTANT]
    > A szolgáltatói információ-hozzáférési bővítmény az OPC-tároló előfizetői tanúsítványában található, az OPC UA-eszközök azonban egyéni metódusokat használnak a kiállító HITELESÍTÉSSZOLGÁLTATÓ adatainak terjesztésére.
- A jóváhagyott aszimmetrikus algoritmusokat, a kulcs hosszát, a kivonatoló függvényeket és a kitöltési módot kell használni.
  - Az **RSA** és az **SHA-2** az egyetlen támogatott algoritmus (*).
  - Az RSA használható titkosításhoz, kulcscserehez és aláíráshoz.
  - Az RSA-titkosításnak csak a OAEP, az RSA-KEM vagy az RSA-PSS kitöltési módot kell használnia.
  - A kulcsok hossza > = 2048 bit szükséges.
  - Használja az SHA-2 kivonatoló algoritmust (SHA256, SHA384 és SHA512).
  - Az RSA legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI kulcsainak átlagos élettartama > = 20 év 4096 bit vagy nagyobb kell legyen.
  - Az RSA kiállító CA kulcsainak legalább 2048 bit értékűnek kell lenniük; Ha a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány lejárati dátuma 2030, akkor a CA-kulcsnak 4096 bit vagy nagyobbnak kell lennie.
- Tanúsítvány élettartama
  - Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok: A legfelső szintű hitelesítésszolgáltatók esetében a tanúsítvány maximális érvényességi időtartama nem haladhatja meg a 25 évet.
  - Alárendelt HITELESÍTÉSSZOLGÁLTATÓ vagy online kiállító HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok: A tanúsítvány maximális érvényességi időtartama az online hitelesítésszolgáltatók számára, és csak az előfizetői tanúsítványok kiadása nem haladhatja meg a hat évet. Ezen hitelesítésszolgáltatók esetében a kapcsolódó privát aláírási kulcs nem használható három évnél hosszabb ideig az új tanúsítványok kiállításához.<br>
    > [!IMPORTANT]
    > A kiállítói tanúsítvány, ahogy az az alapértelmezett OPC-tárolóban, a külső legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ nélkül lett létrehozva, úgy van kezelve, mint egy online alhitelesítésszolgáltató, amely megfelel a vonatkozó követelményeknek és élettartamoknak. A rendszer az alapértelmezett élettartamot öt évre állítja be > = 2048-es kulcs hosszával.
  - Minden aszimmetrikus kulcsnak legfeljebb ötéves élettartammal kell rendelkeznie, az ajánlott egy éves élettartammal.<br>
    > [!IMPORTANT]
    > Alapértelmezés szerint az OPC-tárolóban kiadott alkalmazás-tanúsítványok élettartama két év, és minden évben le kell cserélni. 
  - A tanúsítvány megújításakor a rendszer új kulccsal újítja meg.
- OPC UA-specifikus bővítmények az Application instance-tanúsítványokban
  - Az subjectAltName bővítmény magában foglalja az alkalmazás URI-ját és a gazdagépeket is, amelyek teljes tartománynevet, IPv4-és IPv6-címeket is tartalmazhatnak.
  - A kulcshasználat magában foglalja a digitalSignature, a letagadhatatlanság, a keyEncipherment és a dataEncipherment.
  - A extendedKeyUsage tartalmazza a serverAuth és/vagy a clientAuth.
  - Az authorityKeyIdentifier az aláírt tanúsítványokban van megadva.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>A HITELESÍTÉSSZOLGÁLTATÓI kulcsoknak és tanúsítványoknak meg kell felelniük a minimális követelményeknek

- **Titkos kulcsok**: Az **RSA** -kulcsoknak legalább 2048 bit értékűnek kell lenniük; Ha a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány lejárati dátuma 2030, akkor a CA-kulcsnak 4096 bit vagy nagyobbnak kell lennie.
- **Élettartam**: A tanúsítvány maximális érvényességi időtartama az online hitelesítésszolgáltatók számára, és csak az előfizetői tanúsítványok kiadása nem haladhatja meg a hat évet. Ezen hitelesítésszolgáltatók esetében a kapcsolódó privát aláírási kulcs nem használható három évnél hosszabb ideig az új tanúsítványok kiállításához.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>A HITELESÍTÉSSZOLGÁLTATÓI kulcsokat hardveres biztonsági modulok (HSM) védik

- A OpcVault az Azure kulcstartó prémium és kulcsait az FIPS 140-2 2. szintű hardveres biztonsági modulok (HSM) védik. 

A Key Vault által használt kriptográfiai modulok (a HSM vagy a szoftverek) a FIPS (szövetségi adatfeldolgozási szabványok) hitelesítése.<br>
A HSM által védettként létrehozott vagy importált kulcsok a HSM-ben lesznek feldolgozva, amely a 2. szintű FIPS 140-2-es szintre van érvényesítve.<br>
A szoftver által védettként létrehozott vagy importált kulcsok feldolgozása az 1. szintű FIPS 140-2-es szintre ellenőrzött titkosítási modulokon belül történik.

## <a name="operational-practices"></a>Működési eljárások

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>A tanúsítványigénylés szabványos működési PKI-eljárásainak dokumentálása és karbantartása

Dokumentálja és karbantartsa a hitelesítésszolgáltatók tanúsítványait, például a következőket: 
- Az előfizető azonosítása és hitelesítése 
- A tanúsítványkérelem feldolgozásának és ellenőrzésének módja (ha van ilyen) a tanúsítvány-megújítási és-újragenerálási kérelmek feldolgozásának módját is beleértve. 
- A kiállított tanúsítványok kiosztása az előfizetők számára 

Az OPC-tár Service SOP-t az [Áttekintés](overview-opc-vault-architecture.md) és a dokumentumok [kezelése](howto-opc-vault-manage.md) című témakör ismerteti. Az alábbi eljárások az OPC Unified Architecture Specification 12. részében olvashatók: Felderítési és globális szolgáltatások.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>A tanúsítványok visszavonására szolgáló szabványos PKI-eljárások dokumentálása és karbantartása

A tanúsítvány-visszavonási folyamatot az [Áttekintés](overview-opc-vault-architecture.md) és a dokumentumok [kezelése](howto-opc-vault-manage.md) című témakör ismerteti.
    
### <a name="document-certification-authority-key-generation-ceremony"></a>Dokumentum-hitelesítésszolgáltató kulcsának generálási ünnepsége 

Az OPC-tárolóban a kiállító HITELESÍTÉSSZOLGÁLTATÓ kulcsának generálását az Azure kulcstartó biztonságos tárterülete okozza, amelyet a dokumentáció [kezelése](howto-opc-vault-manage.md) című témakörben talál.

Ha azonban egy külső legfelső szintű hitelesítésszolgáltatót használ, a hitelesítésszolgáltató (CA) kulcsának generálási ünnepségének meg kell felelnie a következő követelményeknek:

A HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepségét olyan dokumentált parancsfájlon kell végrehajtani, amely legalább a következő elemeket tartalmazza: 
1. A szerepkörök és a résztvevői felelősségek meghatározása
2. A HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepségének végrehajtásának jóváhagyása
3. Az ünnepséghez szükséges titkosítási hardverek és aktiválási anyagok
4. Hardver-előkészítés (beleértve az eszköz/konfigurációs adatok frissítését és kijelentkezését)
5. Operációs rendszer telepítése
6. A HITELESÍTÉSSZOLGÁLTATÓI kulcs generálási ünnepsége során végrehajtott konkrét lépések, például: 
7. HITELESÍTÉSSZOLGÁLTATÓI alkalmazás telepítése és konfigurálása
8. HITELESÍTÉSSZOLGÁLTATÓI kulcs létrehozása
9. HITELESÍTÉSSZOLGÁLTATÓI kulcs biztonsági mentése
10. HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány aláírása
9. Aláírt kulcsok importálása a szolgáltatás védett HSM-ben.
11. HITELESÍTÉSSZOLGÁLTATÓ rendszerleállása
12. Anyagok előkészítése a tároláshoz


## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC-tároló biztonságos kezelését, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az OPC UA-eszközök biztonságossá tétele az OPC-tárolóval](howto-opc-vault-secure.md)