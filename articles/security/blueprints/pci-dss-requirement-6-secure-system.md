---
title: Az Azure támogatási feldolgozási tervezetének - biztonságos rendszerre vonatkozó követelmények
description: PCI DSS követelmény 6
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: cd9054afc359d7568fcd08f983f374e0b2bc3792
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>A PCI DSS-kompatibilis környezetben biztonságos rendszerkövetelményei 
## <a name="pci-dss-requirement-6"></a>PCI DSS követelmény 6

**Fejlesztéséhez és biztonságos rendszerek és alkalmazások karbantartása**

> [!NOTE]
> Ezeket a követelményeket határozzák meg a [Payment Card Industry (PCI) biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/pci_security/) részeként a [PCI adatok biztonsági szabvány (DSS) 3.2-es verziójú](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Tekintse meg a PCI DSS tesztelési eljárások és az egyes követelményekhez útmutatót olvashat.

Lelkiismeretlen egyéni felhasználók számára a biztonsági rések segítségével rendszerek privilegizált hozzáférést. A biztonsági rések számos megszüntetett szállító által biztosított biztonsági javítások, amelyeket a szervezetek, amelyek a rendszerek kezelése kell telepíteni. Minden rendszer a felhasználás és az adatok kártya tulajdonosát a rosszindulatú támadók és a kártevő szoftverek elleni védelem érdekében az összes megfelelő szoftverjavítások kell rendelkeznie.

> [!NOTE]
> Megfelelő szoftverjavítások kiértékelve, és teszteléssel megfelelően, hogy a javítások nem ütköznek a meglévő biztonsági beállításokkal javításokat. Saját fejlesztésű alkalmazások számos biztonsági rések köszönhetően elkerült szabványos rendszer fejlesztési folyamatokkal használatával és biztonságos kódolási technikák is járhat.

## <a name="pci-dss-requirement-61"></a>6.1 PCI DSS követelmény

**6.1** egy folyamat azonosítására biztonsági rések segítségével megbízható forrásból kívül biztonsági biztonsági rést, létrehozásához, és rendelje hozzá a prioritás (a példában, mint a "magas", "közepes," vagy "alacsony") az újonnan felderített biztonsági kockázata biztonsági rések.

> [!NOTE]
> Kockázati rangsort ágazatban kialakult bevált gyakorlaton, valamint a célgyűjtemény figyelembevételével alapján. Például feltételek sorrendjét a biztonsági rések tartalmazhatják a CVSS pontszám és/vagy a szállító és/vagy típus érintett rendszerek által a besorolás figyelembevételével. 
> 
> Biztonsági rések értékelése, és rendelje hozzá a kockázat minősítések módszerei a szervezetek környezet és kockázatfelmérési stratégia alapján változhatnak. Kockázati rangsort kell, legalább az összes biztonsági rések azonosítása számít, a környezet "magas kockázatú". A kockázat rangsorolási mellett biztonsági rések tekinthetők "kritikus" Ha azokat egy közvetlen veszélyt a környezetet, a hatás kritikus rendszerek esetében, és/vagy annak lehetőségét jelezhetik eredményezne, ha nem címzett. Kritikus rendszerek biztonsági rendszerek, nyilvánosan elérhető eszközök és rendszerek, adatbázisok és más rendszerek vannak tárolva, a folyamat, előfordulhat, kártya tulajdonosát adatokat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Eljárások létrejött, és a hatókör határt a hipervizor-állomások a biztonsági rések vizsgálat megvalósítva. Biztonsági rések keresése a megfelelő biztonsági ellenőrzési eszközök megtörténik a kiszolgálói operációs rendszerek, adatbázisok és a hálózati eszközök. A biztonsági vizsgálatok legalább negyedévente történik. A Microsoft Azure szerződéseket független értékelő behatolást vagy a biztonság teszteléséhez a Microsoft Azure-határ. Piros-csapat gyakorlatokat is rendszeresen készül, és eredmények használ a biztonsági javítások. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló csökkenti a biztonsági rések Alkalmazásátjáró WAF, és az engedélyezett OWASP szabálykészletben kockázatát. További információkért lásd: [PCI útmutatást - megoldás a kockázat biztonsági réseket](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>PCI DSS követelmény 6.2

**6.2** rendszer összetevőit és a szoftver védelmének biztosításához az ismert biztonsági rések alkalmazható szállító által biztosított biztonsági javítások telepítésével. Kritikus biztonsági javítások telepítését kiadás egy hónapon belül.

> [!NOTE]
> Kritikus biztonsági javítások meg kell határozni a kockázat a követelmény 6.1 folyamat prioritás szerint.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure összes hálózati eszköz biztosításáért felelős és hipervizor-OS szoftverek védett az ismert biztonsági rések alkalmazható szállító által biztosított biztonsági javítások telepítésével. Hacsak nem használhatja a szolgáltatást az ügyfél kéri, annak érdekében, hogy operációs rendszer szintű biztonsági rések miatt sikertelen volt, és időben szervizelt van egy javítás felügyeleti folyamat. Az üzemi kiszolgálók beolvasása havonta javítás megfelelőség ellenőrzése. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy olyan PaaS service megoldás. Azure biztosít az összes szolgáltatás javítások karbantartása.|



## <a name="pci-dss-requirement-63"></a>PCI DSS követelmény 6.3

**6.3** fejlesztése belső és külső alkalmazások (beleértve a rendszergazdai hozzáféréssel webalapú alkalmazások) biztonságos, az alábbiak szerint:
- Megfelelően (például a biztonságos hitelesítési és naplózási) PCI DSS
- Az ipari szabványok és/vagy az ajánlott eljárások alapján
- A szoftver-fejlesztési életciklus során magában foglaló információ-biztonság 

> [!NOTE]
> Ez vonatkozik az összes belső fejlesztett, valamint a bespoke vagy egyéni szoftvert egy harmadik fél által fejlesztett.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure-alkalmazások és a végpontok fejlesztett a Microsoft biztonságos fejlesztési Életciklussal (SDL) módszertan használatával, amely DSS követelményeinek megfelelően. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló úgy lett kialakítva, hajtsa végre az ágazatban kialakult bevált gyakorlaton CHD védelme. Üzembe helyezési útmutatót biztosít a biztonsági meassures részleteit, és a naplózás engedélyezve van.|



### <a name="pci-dss-requirement-631"></a>PCI DSS követelmény 6.3.1

**6.3.1** fejlesztési, tesztelési és/vagy egyéni alkalmazás-fiókok, felhasználói azonosítókat és jelszavak alkalmazások válik aktívvá, vagy az ügyfelek kiadott előtt távolítsa el.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A végső biztonsági felülvizsgálat (FSR) Működésétől fő kiadásokban még éles központi telepítése előtt végzi a kijelölt biztonsági tanácsadó kívül az Azure fejlesztési csapat csak készen áll a termelési alkalmazások kiadott biztosításához. A végső felülvizsgálat részeként biztosítható, hogy a fiókok, a vizsgálati és a vizsgálati adatok el lettek távolítva. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy átmeneti és naplózza, és elkülönített szolgáltatást biztosít. A hálózati réteg mindegyikén [NSG] dedikált hálózati biztonsági csoport. További információkért lásd: [PCI útmutatást - hálózati biztonsági csoportok](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>PCI DSS követelmény 6.3.2

**6.3.2** egyéni kód megelőzően tekintse át a kiadásáról éles vagy az ügyfelek esetleges biztonsági rés (kézi vagy automatikus folyamat segítségével) tartalmaznia kell legalább a következő kódolás megismerése érdekében:
- Kódmódosításokat felülvizsgálata egyéni felhasználók számára nem a származó kódot és az egyéni kód-felülvizsgálati módszereket ismerő és biztonságos eljárásokkal kódolása.
- Kód értékelést győződjön meg arról, kód biztonságos kódolási igényeinek megfelelően fejlesztése
- Megfelelő javításokat a kiadási megvalósított előtt
- Kód-ellenőrzési eredmények áttekintette és előzetes kibocsátási felügyeleti jóvá 

> [!NOTE]
> A rendszer fejlesztési életciklusba részeként az összes egyéni kód (belső és nyilvánosan elérhető), a kód ellenőrzésekhez ez követelmény vonatkozik. 
>
> Kód értékelést tájékozott belső csoporthoz, vagy harmadik felek által lehet elvégezni. Nyilvánosan elérhető webalkalmazások lépnek is további vezérlők, cím elleni folyamatos fenyegetések és biztonsági rések, végrehajtását követően PCI DSS követelmény 6.6 meghatározott.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft Azure-alkalmazások és a végpontok fejlesztett Microsoft biztonságos fejlesztési Életciklussal (SDL) módszerek alapján. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy átmeneti és naplózza, és elkülönített szolgáltatást biztosít. A hálózati réteg mindegyikén [NSG] dedikált hálózati biztonsági csoport. További információkért lásd: [PCI útmutatást - hálózati biztonsági csoportok](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>6.4 PCI DSS követelmény

**6.4** kövesse módosítása vezérlő folyamatok és eljárások, az összes változtatás-összetevőkkel. A folyamatok tartalmaznia kell a következő (lásd a követelményeket 6.4.1 való 6.4.6).

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | A Microsoft az adott információ-biztonság szoftverfejlesztői kapcsolódó biztonsági szempontok a rendszer kezdetektől SDLC integrálni kell a NIST útmutatót követi. A Microsoft SDL a biztonsági gyakorlatai végrehajtásának folyamatos integrációja lehetővé teszi:<ul><li>Korai azonosítása és csökkenti a biztonsági réseket és konfigurációs hibák</li><li>Kódolási kihívást szükséges biztonsági vezérlők által okozott esetleges szoftver meg</li><li>Megosztott biztonsági szolgáltatások azonosítása és újbóli használatának ajánlott biztonsági eljárások az eszközök, amely azáltal növeli a biztonságot bevált módszerek és technikák használatával</li><li>A Microsoft már átfogó kockázatkezelési programjának végrehajtása</li></ul>A Microsoft Azure létesített változás, és felszabadíthatja a felügyeleti folyamatok végrehajtása jelentős változások többek között szabályozhatja:<ul><li>Az azonosító és a tervezett módosítás dokumentációja</li><li>A termék tervezés során üzleti célokat, a prioritásokkal és a forgatókönyvek azonosítása</li><li>A szolgáltatás vagy összetevő tervezési meghatározása</li><li>Előre meghatározott feltételek/ellenőrzés-listáját általános kockázati/hatásának alapján működőképesség áttekintése</li><li>A belépési és kilépési feltételeket fejlesztői (fejlesztés), a INT (integráció tesztelése), a FÁZIS (éles üzem előtti) és a termék (éles) környezetekhez megfelelő alapján teszteli, az engedélyezési és a változáskezeléshez. Az ügyfelek felelőssége saját Microsoft Azure szolgáltatásban üzemeltetett alkalmazásokat.</li></ul> |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló bemutató egy átmeneti és naplózza, és elkülönített szolgáltatást biztosít. <br /><br />A hálózati réteg mindegyikén [NSG] dedikált hálózati biztonsági csoport. További információkért lásd: [PCI útmutatást - hálózati biztonsági csoportok](payment-processing-blueprint.md#network-security-groups).<br /><br />Módosítások használata az Operations Management Suite naplózza, és a Runbookok segítségével naplógyűjtéshez. A Naplóelemzési változások kiterjedt naplózás biztosít. Módosítások tekintse át, és pontossága ellenőrizni. Részletesebb útmutatásért lásd: [PCI útmutatást - az Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>PCI DSS követelmény 6.4.1

**6.4.1** külön fejlesztési és tesztelési célú környezetben az üzemi környezetben, és a hozzáférés-vezérlést szét.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy átmeneti és naplózza, és elkülönített szolgáltatást biztosít. A hálózati réteg mindegyikén [NSG] dedikált hálózati biztonsági csoport. További információkért lásd: [PCI útmutatást - hálózati biztonsági csoportok](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>PCI DSS követelmény 6.4.2

**6.4.2** fejlesztési és tesztelési célú és éles környezetek között feladataik elválasztását igénylik

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy átmeneti és naplózza, és elkülönített szolgáltatást biztosít. A hálózati réteg mindegyikén [NSG] dedikált hálózati biztonsági csoport. További információkért lásd: [PCI útmutatást - hálózati biztonsági csoportok](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>PCI DSS követelmény 6.4.3-as

**6.4.3-as** termelési adatok (élő PANs) nem használhatók tesztelési, illetve a fejlesztési.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló nincs frissített elsődleges fiók száma (PÁSZTÁZÁS) adatok.|



### <a name="pci-dss-requirement-644"></a>PCI DSS követelmény 6.4.4

**6.4.4** Tesztadatok és fiókokat a rendszer összetevőit, a rendszer válik aktívvá, és üzembe helyezése előtt.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló nem rendelkezik egyetlen teszt fiókot.|



### <a name="pci-dss-requirement-645"></a>PCI DSS követelmény 6.4.5

**6.4.5** Változáskezelő eljárások biztonsági javítások és szoftverfrissítések módosítások végrehajtásához tartalmaznia kell a következőket:
- **6.5.4.1** gyakorolt hatás dokumentációját.
- **6.5.4.2** használati jóváhagyási jogosult felek által módosítható.
- **6.5.4.3** ellenőrizze, hogy a módosítás nem befolyásolhatja a rendszer a biztonsági funkciók.
- **6.5.4.4** kimenő biztonsági eljárásokat.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy olyan PaaS service megoldás. És az Azure biztosít az összes szolgáltatás javítások karbantartása.|



### <a name="pci-dss-requirement-646"></a>PCI DSS követelmény 6.4.6.

**6.4.6** jelentős változás befejeztével az összes vonatkozó PCI DSS követelmények kell megvalósítani, minden új vagy módosított rendszerek és hálózatok és frissíteni, mivel a megfelelő dokumentációját.

> [!NOTE]
> Ez a követelmény a legjobb 2018 január 31-ig elteltével követelmény válik.


**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A Contoso webes tároló egy olyan PaaS service megoldás. És az Azure biztosít az összes szolgáltatás javítások karbantartása.|



## <a name="pci-dss-requirement-65"></a>PCI DSS követelmény 6.5

**6.5** cím közös szoftver-fejlesztési folyamatokkal a biztonsági kódolási az alábbiak szerint:
- Évente legalább a naprakész biztonságos kódolási technikák, beleértve a közös kódolási biztonsági rések elkerülésével fejlesztők betanításához.
- A biztonságos kódolási irányelvek alapján alkalmazások fejlesztéséhez.

> [!NOTE]
> A biztonsági rések megtalálható a 6.5.1 6.5.10 keresztül volt aktuális az iparág ajánlott eljárásokkal, ha PCI DSS ezen verziója lett közzétéve, hogy. Azonban, a biztonsági rés felügyeleti ágazatban kialakult bevált gyakorlaton frissítésekor (például a OWASP útmutató, San-CWE első 25, CERT biztonságos kódolási, stb.) az aktuális ajánlott eljárásokat kell használni ezeket a követelményeket. 
> 
> [!NOTE]
> Követelmények 6.5.1 keresztül 6.5.6, lent vonatkozik minden alkalmazásra (belső vagy külső). Követelmények 6.5.7 keresztül 6.5.10, lent vonatkozik a webes alkalmazások és az alkalmazás felületek (belső vagy külső). 

- **6.5.1** injektálási hibái, különösen az SQL-injektálás. Az operációs rendszer parancs injektálási, LDAP és XPath injektálási hibái, valamint egyéb injektálási hibái figyelembe venni.
- **6.5.2** túlcsordul puffer
- **6.5.3** nem biztonságos kriptográfiai tároló
- **6.5.4** nem biztonságos kommunikáció
- **6.5.5** helytelen hibakezelés
- **6.5.6** megállapítja, hogy a biztonsági rések azonosítása során (PCI DSS követelmény 6.1 meghatározott) "magas kockázatú" biztonsági réseket
- **6.5.7** többhelyes scripting (lehetővé)
- **6.5.8** helytelen hozzáférés-vezérlő (például a nem biztonságos közvetlen objektum referenciák URL hozzáférés, a directory átjárás és a nem sikerült a felhasználói hozzáférés korlátozásához funkciók hiba)
- **6.5.9** webhelyközi kérések hamisítására (CSRF)
- **6.5.10** hibás hitelesítési és munkamenet-kezelés

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Lásd: "Microsoft Azure" szakasz [követelmény 6.4](#pci-dss-requirement-6-4). |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A webes tároló contoso bemutató biztonságos fejlesztési, egy DFD és a fenyegetések modell mutatja be a biztonságos fejlesztésre eljárások útmutatást nyújt a.|



## <a name="pci-dss-requirement-66"></a>PCI DSS követelmény 6.6.

**6.6** nyilvánosan elérhető webes alkalmazásokhoz, új veszélyeket és biztonsági rések folyamatosan, és ezeket az alkalmazásokat az alábbi módszerek egyikével ismert támadások elleni védelmét:

- Legalább évente és a módosítások után manuális vagy automatikus alkalmazás biztonsági rés biztonsági felmérőeszközöket vagy módszerek, nyilvánosan elérhető webalkalmazások áttekintése 

   > [!NOTE]
   > A vizsgálat nincs ugyanaz, mint a biztonsági vizsgálatok követelmény 11.2 elvégzi. 

- Egy automatizált műszaki megoldást, amely észleli, és megakadályozza, hogy a web-alapú támadások (például a webalkalmazás tűzfal) telepítése előtt nyilvánosan elérhető webes alkalmazásokhoz, az összes forgalom folyamatosan kereséséhez.

**Feladatok:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure teszteli az SDL folyamat részeként nyilvánosan elérhető webalkalmazások, mielőtt az éles környezetben telepített alkalmazások. Ezenkívül a Microsoft összes nyilvánosan elérhető webalkalmazások rendszeresen éles környezetben minden lehetséges biztonsági rések észlelése keres. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A hivatkozás megoldás csökkenti a biztonsági rések Alkalmazásátjáró WAF, és az engedélyezett OWASP szabálykészletben kockázatát. További információkért lásd: [PCI útmutatást - megoldás a kockázat biztonsági réseket](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>PCI DSS követelmény 6.7

**6.7** győződjön meg arról, hogy a biztonsági házirendek és üzemeltetési eljárások fejlesztésük és fenntartásuk biztonságos rendszerek, és az alkalmazások dokumentálva, valamint használatban van, és az összes érintett fél ismert.

**Feladatok:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Nem alkalmazható. |
| **Ügyfél<br />(PCI&#8209;DSS&nbsp;tervezetének)** | A hivatkozás megoldás csökkenti a biztonsági rések Alkalmazásátjáró WAF, és az engedélyezett OWASP szabálykészletben kockázatát. További információkért lásd: [PCI útmutatást - megoldás a kockázat biztonsági réseket](payment-processing-blueprint.md#application-gateway).|




