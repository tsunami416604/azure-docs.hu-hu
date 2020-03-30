---
title: Az OPC Vault tanúsítványkezelési szolgáltatásbiztonságos futtatása - Azure | Microsoft dokumentumok
description: Bemutatja, hogyan futtatja biztonságosan az OPC Vault tanúsítványkezelési szolgáltatását az Azure-ban, és hogyan tekinti át az egyéb biztonsági irányelveket, amelyeket figyelembe kell vennie.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271706"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Az OPC Vault tanúsítványkezelési szolgáltatás biztonságos futtatása

Ez a cikk bemutatja, hogyan futtatja az OPC Vault tanúsítványkezelési szolgáltatás biztonságosan az Azure-ban, és áttekinti az egyéb biztonsági irányelveket, hogy fontolja meg.

## <a name="roles"></a>Szerepkörök

### <a name="trusted-and-authorized-roles"></a>Megbízható és engedélyezett szerepkörök

Az OPC Vault mikroszolgáltatás lehetővé teszi, hogy a szolgáltatás különböző részeinek eléréséhez különböző szerepkörök et.

> [!IMPORTANT]
> A központi telepítés során a parancsfájl csak azt a felhasználót adja hozzá, aki a központi telepítési parancsfájlt futtatja az összes szerepkör felhasználójaként. Éles környezetben történő telepítés esetén tekintse át ezt a szerepkör-hozzárendelést, és konfigurálja újra megfelelően az alábbi irányelveket követve. Ez a feladat az Azure Active Directory (Azure AD) Enterprise Applications portálon a szerepkörök és szolgáltatások manuális hozzárendelését igényli.

### <a name="certificate-management-service-roles"></a>Tanúsítványkezelési szolgáltatásszerepkörei

Az OPC Vault mikroszolgáltatás a következő szerepköröket határozza meg:

- **Olvasó**: Alapértelmezés szerint a bérlő bármely hitelesített felhasználója olvasási hozzáféréssel rendelkezik. 
  - Olvasási hozzáférés az alkalmazásokhoz és a tanúsítványkérelmekhez. Alkalmazások és tanúsítványkérelmek listázása és lekérdezése. Az eszközfelderítési információk és a nyilvános tanúsítványok olvasási hozzáféréssel is elérhetők.
- **Író**: A Writer szerepkör egy felhasználóhoz van rendelve, hogy bizonyos feladatokhoz írási engedélyeket adjon hozzá. 
  - Olvasási/írási hozzáférés az alkalmazásokhoz és a tanúsítványkérelmekhez. Alkalmazások regisztrálása, frissítése és regisztrációjának megszüntetése. Tanúsítványkérelmeket hozhat létre, és jóváhagyott személyes kulcsokat és tanúsítványokat szerezhet be. A személyes kulcsokat is törölheti.
- **Jóváhagyó**: A jóváhagyó szerepkör egy felhasználóhoz van rendelve a tanúsítványkérelmek jóváhagyásához vagy elutasításához. A szerepkör nem tartalmaz más szerepkört.
  - Az OPC Vault mikroszolgáltatás API-hoz való hozzáférés jóváhagyó szerepkör mellett a felhasználónak is rendelkeznie kell a kulcsaláíró engedéllyel az Azure Key Vaultban a tanúsítványok aláírásához.
  - A Writer és a Approver szerepkört különböző felhasználókhoz kell rendelni.
  - A jóváhagyó fő feladata a tanúsítványkérelmek létrehozásának jóváhagyása és elutasítása.
- **Rendszergazda**: A rendszergazdai szerepkör egy felhasználóhoz van rendelve a tanúsítványcsoportok kezeléséhez. A szerepkör nem támogatja a Jóváhagyó szerepkört, de tartalmazza az Író szerepkört.
  - A rendszergazda kezelheti a tanúsítványcsoportokat, módosíthatja a konfigurációt, és visszavonhatja az alkalmazástanúsítványokat egy új tanúsítvány-visszavonási lista (CRL) kiállításával.
  - Ideális esetben a Writer, a Approver és a Administrator szerepkörök különböző felhasználókhoz vannak rendelve. A nagyobb biztonság érdekében a Jóváhagyó vagy a Rendszergazda szerepkörrel rendelkező felhasználóknak is kulcsaláírási engedélyre van szüksége a Key Vaultban, tanúsítványok kiállításához vagy kiállító hitelesítésszolgáltatói tanúsítvány ának megújításához.
  - A mikroszolgáltatás-adminisztrációs szerepkör mellett a szerepkör is, de nem kizárólagosan:
    - A hitelesítésbiztosítási szervezet biztonsági gyakorlata végrehajtásának igazgatásáért felelős.
    - A tanúsítványok létrehozásának, visszavonásának és felfüggesztésének kezelése. 
    - Kriptográfiai kulcs életciklus-kezelése (például a kiállító hitelesítésszolgáltató kulcsainak megújítása).
    - A hitelesítésszolgáltatót üzemeltető szolgáltatások telepítése, konfigurálása és karbantartása.
    - A szolgáltatások napi szintű működése. 
    - Hitelesítés- és adatbázis-biztonsági mentés és helyreállítás.

### <a name="other-role-assignments"></a>Egyéb szerepkör-hozzárendelések

A szolgáltatás futtatásakor vegye figyelembe a következő szerepköröket is:

- A külső legfelső szintű hitelesítésszolgáltatóval kötött tanúsítványbeszerzési szerződés tulajdonosa (például amikor a tulajdonos tanúsítványokat vásárol egy külső hitelesítésszolgáltatótól, vagy olyan hitelesítésszolgáltatót működtet, amely egy külső hitelesítésszolgáltató alárendeltje).
- A hitelesítésszolgáltató fejlesztése és érvényesítése.
- Az ellenőrzési nyilvántartások áttekintése.
- Az *engedélyezett* szerepkörben vannak azok a személyzet, akik segítenek a hitelesítésszolgáltató támogatásában vagy a fizikai és felhőalapú létesítmények kezelésében, de közvetlenül nem megbízhatóak a hitelesítésszolgáltatói műveletek elvégzésében. Az engedélyezett szerepkörben lévő személyek feladatkészletét is dokumentálni kell.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Megbízható és engedélyezett szerepkörök tagságának áttekintése negyedévente

Tekintse át legalább negyedévente a megbízható és engedélyezett szerepkörök tagságát. Győződjön meg arról, hogy az egyes szerepkörökben lévő személyek (manuális folyamatokhoz) vagy szolgáltatásidentitások (automatizált folyamatokhoz) készlete minimálisra van tartjuk.

### <a name="role-separation-between-certificate-requester-and-approver"></a>A tanúsítványkérelmező és a jóváhagyó szerepkör-elkülönítése

A tanúsítványkiállítási folyamatnak ki kell kényszerítenie a tanúsítványkérelmező és a tanúsítványjóváhagyó szerepkör (személyek vagy automatizált rendszerek) közötti szerepkör-elkülönítést. A tanúsítvány kiállítását tanúsítványjóváhagyó szerepkörrel kell igazolni, amely ellenőrzi, hogy a tanúsítványkérelmező jogosult-e tanúsítványok beszerzésére. A tanúsítványjóváhagyó szerepkört betöltendő személyeknek hivatalosan felhatalmazott személynek kell lenniük.

### <a name="restrict-assignment-of-privileged-roles"></a>Kiemelt szerepkörök hozzárendelésének korlátozása

A kiemelt szerepkörök hozzárendelését, például a Rendszergazdák és jóváhagyók csoport tagságának engedélyezését korlátozott számú felhatalmazott személyzetre kell korlátozni. A kiemelt szerepkör-módosításokat 24 órán belül vissza kell vonni. Végül tekintse át a kiemelt szerepkör-hozzárendeléseket negyedévente, és távolítsa el a szükségtelen vagy lejárt hozzárendeléseket.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>A kiemelt szerepköröknek kétfaktoros hitelesítést kell használniuk

Többtényezős hitelesítés (más néven kétfaktoros hitelesítés) használatával a jóváhagyók és rendszergazdák interaktív bejelentkezései a szolgáltatásba.

## <a name="certificate-service-operation-guidelines"></a>Tanúsítványszolgáltatás működésének irányelvei

### <a name="operational-contacts"></a>Operatív kapcsolatok

A tanúsítványszolgáltatásnak naprakész biztonsági választervvel kell rendelkeznie, amely részletes műveleti incidens-válasz kapcsolattartókat tartalmaz.

### <a name="security-updates"></a>Biztonsági frissítések

Minden rendszert folyamatosan figyelni kell, és frissíteni kell a legújabb biztonsági frissítésekkel.

> [!IMPORTANT]
> Az OPC Vault szolgáltatás GitHub-tárháza folyamatosan frissül a biztonsági javításokkal. Figyelje ezeket a frissítéseket, és alkalmazza őket a szolgáltatásrendszeres időközönként.

### <a name="security-monitoring"></a>A biztonság monitorozása

Iratkozzon fel vagy hajtson végre megfelelő biztonsági figyelést. Például iratkozzon fel egy központi figyelési megoldásra (például az Azure Security Center vagy az Office 365 figyelési megoldás), és konfigurálja megfelelően annak érdekében, hogy a biztonsági események továbbítása a figyelési megoldás.

> [!IMPORTANT]
> Alapértelmezés szerint az OPC Vault szolgáltatás az [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) figyelőmegoldásként üzembe helyezése. Erősen ajánlott egy olyan biztonsági megoldás hozzáadása, mint az [Azure Security Center.](https://azure.microsoft.com/services/security-center/)

### <a name="assess-the-security-of-open-source-software-components"></a>A nyílt forráskódú szoftverösszetevők biztonságának felmérése

A terméken vagy szolgáltatáson belül használt minden nyílt forráskódú összetevőnek közepes vagy nagyobb biztonsági résektől mentesnek kell lennie.

> [!IMPORTANT]
> A folyamatos integrációs buildek során az OPC Vault szolgáltatás GitHub-tárháza az összes összetevőt ellenőrzi a biztonsági rések szempontjából. Figyelheti ezeket a frissítéseket a GitHubon, és rendszeres időközönként alkalmazza őket a szolgáltatásra.

### <a name="maintain-an-inventory"></a>Készlet karbantartása

Eszközkészlet karbantartása az összes éles állomáshoz (beleértve az állandó virtuális gépeket is), az eszközökhöz, az összes belső IP-címtartományhoz, a VIP-khez és a nyilvános DNS-tartománynevekhez. Amikor hozzáad vagy eltávolít egy rendszert, eszköz IP-címét, VIP- vagy nyilvános DNS-tartományát, 30 napon belül frissítenie kell a készletet.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Az azure-i OPC Vault alapértelmezett mikroszolgáltatás-éles telepítésének leltára 

Az Azure-ban:
- **App Service Plan**: App service-csomag a szolgáltatás gazdagépek. Alapértelmezett S1.
- **App Service** for microservice: Az OPC Vault szolgáltatás gazdagép.
- **App Service** mintaalkalmazáshoz: Az OPC Vault mintaalkalmazás gazdagép.
- **Key Vault Standard:** A titkos kulcsok és az Azure Cosmos DB-kulcsok tárolására a webszolgáltatások.
- **Key Vault Premium**: A kibocsátó hitelesítésszolgáltatói kulcsok, a szolgáltatás aláírása, valamint a tároló konfigurációja és az alkalmazás személyes kulcsok tárolása.
- **Azure Cosmos DB**: Alkalmazás- és tanúsítványkérelmek adatbázisa. 
- **Application Insights:**(nem kötelező) figyelési megoldás webszolgáltatás és alkalmazás.
- **Azure AD alkalmazás regisztrációja:** a mintaalkalmazás, a szolgáltatás és a peremhálózati modul regisztrációja.

A felhőszolgáltatások esetében dokumentálni kell az összes állomásnevet, erőforráscsoportot, erőforrásnevet, előfizetés-azonosítót és a szolgáltatás üzembe helyezéséhez használt bérlői azonosítókat. 

Az Azure IoT Edge-ben vagy egy helyi IoT Edge-kiszolgálón:
- **OPC Vault IoT Edge modul**: Gyári hálózati OPC UA Global Discovery Server támogatása. 

Az IoT Edge-eszközök, az állomásnevek és IP-címek dokumentálni kell. 

### <a name="document-the-certification-authorities-cas"></a>A hitelesítésszolgáltatók dokumentálása

A hitelesítési hierarchia dokumentációjának tartalmaznia kell az összes üzemeltetett hitelesítés-működtető t. Ez magában foglalja az összes kapcsolódó alsóbb szintű hitelesítésszolgáltatót, szülő hitelesítésszolgáltatót és legfelső szintű hitelesítésszolgáltatót, még akkor is, ha azösszes nem a szolgáltatás kezeli őket. A hivatalos dokumentáció helyett az összes lejárt hitelesítésszolgáltatói tanúsítvány teljes egészét is megadhatja.

> [!NOTE]
> Az OPC Vault mintaalkalmazás támogatja a szolgáltatásban használt és dokumentációra előállított összes tanúsítvány letöltését.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Az összes hitelesítésszolgáltató által kiállított tanúsítványok dokumentálása

Adja meg az elmúlt 12 hónapban kiállított összes tanúsítvány teljes körű készletét.

> [!NOTE]
> Az OPC Vault mintaalkalmazás támogatja a szolgáltatásban használt és dokumentációra előállított összes tanúsítvány letöltését.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>A kriptográfiai kulcsok biztonságos törlésére vonatkozó szabványos működési eljárás dokumentálása

A hitelesítéshely-társadat élettartama alatt a kulcs törlése csak ritkán fordulhat elő. Ez az oka annak, hogy egyetlen felhasználó sem rendelkezik a Key Vault-tanúsítvány törlésére kijelölt joggal, és ezért nincsenek elérhető kérhető API-k a kiállító hitelesítésszolgáltatói tanúsítvány törléséhez. A hitelesítésszolgáltató titkosítási kulcsainak biztonságos törlésére szolgáló manuális szabványos működési eljárás csak az Azure Portalon lévő Key Vault közvetlen elérésével érhető el. A tanúsítványcsoportot a Key Vaultban is törölheti. Az azonnali törlés érdekében tiltsa le a [Key Vault helyreállítható törlési](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) funkcióját.

## <a name="certificates"></a>Tanúsítványok

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>A tanúsítványoknak meg kell felelniük a minimális tanúsítványprofilnak

Az OPC Vault szolgáltatás egy online hitelesítésszolgáltató, amely végső entitástanúsítványokat ad ki az előfizetőknek. Az OPC Vault mikroszolgáltatás követi ezeket az irányelveket az alapértelmezett megvalósításban.

- Minden tanúsítványnak a következő X.509 mezőket kell tartalmaznia az alábbiak szerint:
  - A verziómező tartalmának v3-nak kell lennie. 
  - A serialNumber mező tartalmának tartalmaznia kell legalább 8 bájt entrópiát, amelyet egy FIPS (Federal Information Processing Standards) 140 jóváhagyott véletlenszám-generátorból szereztek be.<br>
    > [!IMPORTANT]
    > Az OPC Vault sorozatszáma alapértelmezés szerint 20 bájt, és az operációs rendszer kriptográfiai véletlenszám-generátorából származik. A véletlenszám-generátor FIPS 140 jóváhagyott Windows eszközökön, de nem Linux. Fontolja meg ezt, amikor linuxos virtuális gépeket vagy Linux docker-tárolókat használó szolgáltatástelepítést választ, amelyen az alapul szolgáló OpenSSL technológia nincs jóváhagyva.
  - A issuerUniqueID és a subjectUniqueID mezők nem lehetnek jelen.
  - A végfelhasználói tanúsítványokat az IETF RFC 5280-as évszámú rfc-jével összhangban az alapvető megkötések kiterjesztésével kell azonosítani.
  - A pathLenConstraint mezőt 0-ra kell állítani a kiállító hitelesítésszolgáltatói tanúsítványhoz. 
  - A kiterjesztett kulcshasználat bővítménynek jelen kell lennie, és tartalmaznia kell a kiterjesztett kulcshasználat objektumazonosítók minimális készletét. Az anyExtendedKeyUsage OID (2.5.29.37.0) nem adható meg. 
  - A visszavont tanúsítványok listájának terjesztési pontjának (CDP) kiterjesztésének meg kell jelennie a kiállító hitelesítésszolgáltatói tanúsítványban.<br>
    > [!IMPORTANT]
    > A CDP-bővítmény az OPC Vault hitelesítésszolgáltatói tanúsítványaiban található. Mindazonáltal az OPC UA-eszközök egyéni módszereket használnak a visszavont tanúsítványok listájának terjesztésére.
  - A Authority Information Access bővítménynek jelen kell lennie az előfizetői tanúsítványokban.<br>
    > [!IMPORTANT]
    > A Authority Information Access bővítmény az OPC Vault előfizetői tanúsítványaiban található. Mindazonáltal az OPC UA-eszközök egyéni módszereket használnak a kiállító hitelesítésszolgáltató adatainak terjesztésére.
- Jóváhagyott aszimmetrikus algoritmusokat, kulcshosszokat, kivonatoló függvényeket és kitöltési módokat kell használni.
  - Az RSA és az SHA-2 az egyetlen támogatott algoritmus.
  - Az RSA titkosításra, kulcscserére és aláírásra használható.
  - Az RSA-titkosítás csak az OAEP, RSA-KEM vagy RSA-PSS kitöltési módokat használhatja.
  - A 2048 bitnél nagyobb vagy azzal egyenlő kulcshosszok szükségesek.
  - Használja az SHA-2 kivonatoló algoritmusok (SHA256, SHA384 és SHA512) családját.
  - A 20 évnél hosszabb vagy azzal egyenlő tipikus élettartamú RSA-gyökérhitelesítési kulcsoknak legalább 4096 bitnek kell lenniük.
  - Az RSA kiállító hitelesítés- és hitelesítés- és hitelesítés- és hitelesítés- és hitelesítésa-kulcsainak legalább 2048 bitesnek kell lenniük. Ha a hitelesítésszolgáltatói tanúsítvány lejárati dátuma 2030 után van, a hitelesítésszolgáltató kulcsának legalább 4096 bitesnek kell lennie.
- Tanúsítvány élettartama
  - Legfelső szintű hitelesítésszolgáltatói tanúsítványok: A legfelső szintű hitelesítésszolgáltatói tanúsítványok maximális érvényességi ideje nem haladhatja meg a 25 évet.
  - Alhitelesítésszolgáltatói vagy online kiállítóhitelesítési tanúsítványok: Az online állapotban lévő és csak előfizetői tanúsítványokat kiállító hitelesítésszolgáltatók maximális érvényességi ideje nem haladhatja meg a 6 évet. Ezeknél a hitelesítésszolgáltatói tanúsítványoknál a kapcsolódó személyes aláírási kulcsot nem szabad 3 évnél hosszabb ideig használni az új tanúsítványok kiállításához.<br>
    > [!IMPORTANT]
    > A kiállítói tanúsítvány, ahogy az az alapértelmezett OPC Vault mikroszolgáltatásban jön létre külső legfelső szintű hitelesítésszolgáltató nélkül, online al-hitelesítésszolgáltatóként kezeli, a megfelelő követelményekkel és élettartamokkal. Az alapértelmezett élettartam 5 év, a kulcs hossza 2048-nál nagyobb vagy egyenlő.
  - Minden aszimmetrikus kulcsnak legfeljebb 5 éves élettartammal és ajánlott 1 éves élettartammal kell rendelkeznie.<br>
    > [!IMPORTANT]
    > Alapértelmezés szerint az OPC Vault által kiállított alkalmazástanúsítványok élettartama 2 év, és minden évben cserélni kell. 
  - A tanúsítvány megújításakor a tanúsítvány új kulccsal újul meg.
- OPC UA-specifikus bővítmények az alkalmazáspéldány-tanúsítványokban
  - A subjectAltName kiterjesztés tartalmazza az Uri alkalmazást és az állomásneveket. Ezek közé tartozhatnak a FQDN, az IPv4 és az IPv6-címek is.
  - A keyUsage magában foglalja a digitalSignature, nonRepudiation, keyEncipherment, és dataEncipherment.
  - A extendedKeyUsage tartalmazza a kiszolgálóauth és clientAuth.
  - A authorityKeyIdentifier az aláírt tanúsítványokban van megadva.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>A hitelesítésszolgáltatói kulcsoknak és tanúsítványoknak meg kell felelniük a minimális követelményeknek

- **Személyes kulcsok:** Az RSA-kulcsoknak legalább 2048 bitesnek kell lenniük. Ha a hitelesítésszolgáltatói tanúsítvány lejárati dátuma 2030 után van, a hitelesítésszolgáltató kulcsának legalább 4096 bitesnek kell lennie.
- **Élettartam**: Az online állapotú és csak előfizetői tanúsítványokat kiállító hitelesítésszolgáltatók maximális érvényességi ideje nem haladhatja meg a 6 évet. Ezeknél a hitelesítésszolgáltatói tanúsítványoknál a kapcsolódó személyes aláírási kulcsot nem szabad 3 évnél hosszabb ideig használni az új tanúsítványok kiállításához.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>A hitelesítésszolgáltatói kulcsok hardveres biztonsági modulokkal védettek

Az OpcVault az Azure Key Vault Premium szolgáltatást használja, és a kulcsokat a FIPS 140-2 Level 2 hardveres biztonsági moduljai (HSM) védik. 

A Key Vault által használt kriptográfiai modulok, függetlenül attól, hogy a HSM vagy a szoftver, FIPS-érvényesítve vannak.The cryptographicmodules that Key Vault uses, whether HSM or software, are FIPS validated. A HSM-védelemmel ellátottként létrehozott vagy importált kulcsok feldolgozása egy HSM-en belül történik, fips 140-2 2 szintű 2-re érvényesítve. A szoftvervédelemmel ellátottként létrehozott vagy importált kulcsokat a FIPS 140-2 1.

## <a name="operational-practices"></a>Működési gyakorlatok

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>A tanúsítványigénylés hez vonatkozó szabványos működési PKI-eljárások dokumentálása és karbantartása

Dokumentálja és karbantartja a hitelesítésszolgáltatók tanúsítványok kiállításának módjára vonatkozó szabványos működési eljárásokat, beleértve a következőket: 
- Az előfizető azonosításának és hitelesítésének a módját. 
- A tanúsítványkérelem feldolgozásának és érvényesítésének módját (adott esetben a tanúsítványmegújítási és újrakulcskérelmek feldolgozásának módját is tartalmazza). 
- A kiállított tanúsítványok kiosztásának módját az előfizetők között. 

Az OPC Vault mikroszolgáltatás SOP leírása az [OPC Vault architektúrában](overview-opc-vault-architecture.md) és [az OPC Vault tanúsítványszolgáltatás ának kezelése.](howto-opc-vault-manage.md) Az eljárások az "OPC Unified Architecture Specification Part 12: Discovery and Global Services" (OPC Unified Architecture Specification Part 12: Discovery and Global Services) című részt követik.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>A tanúsítványok visszavonására vonatkozó szabványos működési PKI-eljárások dokumentálása és karbantartása

A tanúsítvány-visszavonási folyamat leírása az [OPC Vault architektúrájában](overview-opc-vault-architecture.md) és [az OPC Vault tanúsítványszolgáltatás ának kezelése](howto-opc-vault-manage.md)című ismerteti.
    
### <a name="document-ca-key-generation-ceremony"></a>Dokumentum hitelesítésa kulcsgenerálási ünnepség 

A kiállító hitelesítésszolgáltató kulcsgenerálása az OPC Vault mikroszolgáltatás egyszerűsített, az Azure Key Vault biztonságos tárolása miatt. További információt [az OPC Vault tanúsítványszolgáltatás ának kezelése című](howto-opc-vault-manage.md)témakörben talál.

Ha azonban külső legfelső szintű hitelesítésszolgáltatót használ, a hitelesítésszolgáltató kulcsgenerálási ceremóniájának meg kell felelnie az alábbi követelményeknek.

A hitelesítésnél a kulcslétrehozásáról szóló szertartást olyan dokumentált parancsfájlon kell végrehajtani, amely legalább a következő elemeket tartalmazza: 
- A szerepek és a résztvevők felelősségének meghatározása.
- Jóváhagyás a hitelesítéshitelesítési kulcs létrehozásáról tartott ceremónia lebonyolításához.
- Kriptográfiai hardver és aktiválási anyagok szükségesek a szertartás.
- Hardverelőkészítés (beleértve az eszköz/konfiguráció adatainak frissítését és kijelentkezését).
- Az operációs rendszer telepítése.
- A hitelesítésa kulcs létrehozásának lépései során végrehajtott konkrét lépések, például: 
  - Hitelesítési szolgáltatás alkalmazás telepítése és konfigurálása.
  - CA kulcsgeneráció.
  - Hitelesítésa kulcs biztonsági mentése.
  - Hitelesítésszolgáltatói tanúsítvány aláírása.
  - Aláírt kulcsok importálása a szolgáltatás védett HSM-jében.
  - A hitelesítésszolgáltatás rendszere leállt.
  - Anyagok előkészítése tárolásra.


## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az OPC Vault biztonságos kezelését, a következőket teheti:

> [!div class="nextstepaction"]
> [Biztonságos OPC UA eszközök OPC Vault](howto-opc-vault-secure.md)
