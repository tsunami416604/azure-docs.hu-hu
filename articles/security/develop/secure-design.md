---
title: Biztonságos alkalmazások tervezése az a Microsoft Azure
description: Ez a cikk ismerteti az ajánlott eljárások az web projektet vonatkozó követelmény és tervezési fázisban.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 12b9793cabb261368c437bd2ae2dbb39cf078bef
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653281"
---
# <a name="design-secure-applications-on-azure"></a>Biztonságos alkalmazások tervezése az Azure-ban
Ebben a cikkben azt jelenthet biztonsági tevékenységeket és a vezérlőket felhőalkalmazások kialakításakor vegye figyelembe. Képzési lehetőségekkel, valamint a biztonsági kérdéseket és a követelmények során fontolja meg, és a Microsoft fázisai tervezési fogalmak [biztonságos fejlesztési Életciklussal (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) terjed ki. A célja, hogy a segítségével meghatározhatja a tevékenységeket és a egy biztonságosabb alkalmazások tervezéséhez használható Azure-szolgáltatásokhoz.

Ebben a cikkben a következő SDL fázisok terjed ki:

- Képzés
- Követelmények
- Tervezés

## <a name="training"></a>Képzés
A felhőalapú alkalmazások fejlesztésének megkezdése előtt tudni, biztonság és adatvédelem az Azure-ban időt vesz igénybe. Ez a lépés végrehajtásával csökkentheti számának és súlyosságának kihasználható biztonsági rések az alkalmazásban. Több előkészített megfelelően reagálni a folyamatosan változó fenyegetések világának fogja.

A képzési szakasz során a következő források segítségével Ismerkedjen meg az Azure-szolgáltatások, amelyek a fejlesztők számára elérhető, és az ajánlott biztonsági eljárások az Azure-ban:

  - [Fejlesztői útmutató az Azure-bA](https://azure.microsoft.com/campaigns/developer-guide/) bemutatja, hogyan kezdheti el az Azure-ral. Az útmutató ismerteti, milyen szolgáltatások segítségével futtathatja alkalmazásait, az adatok tárolására, intelligenciát építhet be, IoT-alkalmazások készítése és üzembe helyezése a megoldások olyan hatékony és biztonságos módon.

  - [Útmutató Azure-fejlesztők számára az első lépésekhez](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) fejlesztőknek, akik segítséget nyújt az Azure platform használatának a fejlődésük kell alapvető információkat biztosít.

  - [SDK-k és eszközök](https://docs.microsoft.com/azure/index#pivot=sdkstools) az Azure-ban elérhető eszközöket írja le.

  - [Az Azure DevOps-szolgáltatásokkal](https://docs.microsoft.com/azure/devops/) fejlesztési eszközöket biztosít. Az eszközök között található nagy teljesítményű folyamatok, ingyenes Git-tárházakat, konfigurálható kanbantáblák és alapos, automatizált, felhőalapú terheléses tesztelés.
    A [fejlesztési és üzemeltetési Erőforrásközpont](https://docs.microsoft.com/azure/devops/learn/) egyesíti a források fejlesztési és üzemeltetési eljárások, Git verziókövetés, pedig Agilis módszerekkel, hogyan Együttműködünk a Microsoft DevOps és hogyan saját DevOps-előmenetel értékelhet.

  - [Az 5 biztonsági legfontosabb kell figyelembe venni, mielőtt leküldené éles](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) bemutatja, hogyan biztonságossá tétele Azure-beli webalkalmazásait, és az alkalmazások a gyakori és a veszélyes webes alkalmazás támadások elleni védelme érdekében.

  - [Secure DevOps Kitet az Azure](https://azsk.azurewebsites.net/index.html) parancsfájlok, eszközök, bővítmények és automatizálását, amely átfogó Azure előfizetésben és erőforráscsoportban biztonsági igényeinek, amely széles körű automation használata a DevOps-csapatok caters gyűjteménye. A Secure DevOps Kitet az Azure jeleníti meg, hogyan biztonsági zökkenőmentesen integrálhatók a natív DevOps-munkafolyamatokba. A csomag címek eszközök, például a biztonsági ellenőrző teszteket (SVTs), amely megkönnyíti a fejlesztők biztonságos kód írása és tesztelése a biztonságos konfigurációt a felhőalapú alkalmazások fejlesztési kódolási és korai szakaszaiban.

  - [Ajánlott biztonsági eljárások az Azure-megoldások](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) biztonsági gyűjteményét biztosítja a tervezési, üzembe helyezése, és a felhőalapú megoldások kezelése az Azure használatának ajánlott eljárásait.

## <a name="requirements"></a>Követelmények
A követelmények meghatározása fázisban az alkalmazás van, és mit fog tenni akkor szabadul fel, ha a kiemelten fontos. A követelmények fázisban is egy ideig a biztonsági vezérlőket gondolja át, hogy fog-e építve az alkalmazásba. Ebben a fázisban a szükséges lépéseket is fog során az SDL-ből, hogy engedje, és biztonságos üzembe helyezése is megkezdése.

### <a name="consider-security-and-privacy-issues"></a>Fontolja meg a biztonsági és adatvédelmi problémák
Ebben a fázisban, fontolja meg az alapvető biztonsági és adatvédelmi problémák a legjobb ideje. Egy csapat meghatározása a teljesítményük elfogadható szintű biztonságot és adatvédelmet elején projekt segítségével:

- Ismerje meg, és a biztonsági problémák kapcsolódó kockázatokat.
- Azonosítsa és javítsa ki a biztonsági hibák fejlesztés során.
- Biztonság és adatvédelem a teljes projekt során létrehozott szintjei érvényesek.

Ha az alkalmazás a követelményeket ír, mindenképp vegye figyelembe a biztonsági vezérlőket, amelyek segítségével az alkalmazások és adatok biztonsága.

### <a name="ask-security-questions"></a>Biztonsági kérdések
Kérje meg a biztonsági kérdéseket, például:

  - Az alkalmazásom tartalmaz bizalmas adatokat?

  - Nem az alkalmazásom gyűjtése és me tartaniuk az iparági normák és a megfelelőségi programok, például a szükséges adatok tárolása a [Szövetségi pénzügyi intézmény vizsgálat Tanács (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) vagy a [Iparágával Data Security Standards (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - Nem az alkalmazásom gyűjtése vagy bizalmas személyes vagy a vásárlói adatokat tartalmaznak, amelyek segítségével, vagy saját maga, vagy más információ azonosításához, lépjen kapcsolatba, vagy keresse meg az egyetlen személy?

  - Nem az alkalmazásom gyűjtése, vagy egy személy, oktatási, pénzügyi, orvosi eléréséhez használható adatokat vagy alkalmazási információkat tartalmaznak? A követelmények fázis során az adatok érzékenysége azonosító segítségével az adatok besorolását, és az adatvédelmi módszert fog használni az alkalmazás azonosításához.

  - Hol és hogyan történik az adatok tárolása? Fontolja meg, hogyan figyeli a tárolási szolgáltatások, az alkalmazás által használt bármely (például a válaszidők lassabb) váratlan módosítások. Akkor fog tudni befolyásolhatják a naplózás részletesebb adatainak összegyűjtését és elemzését mélyebben probléma?

  - A nyilvánosan (az interneten) érhető el vagy belső csak lesz az alkalmazásom? Ha az alkalmazás a nyilvánosság számára érhető el, hogyan tegye megvédeni energiasémákból gyűjthetők a megfelelő módon használja el az adatokat? Ha az alkalmazás elérhető belső csak, vegye figyelembe a vállalatnál ki kell hozzáférni az alkalmazáshoz, és azok mennyi ideig hozzáféréssel kell rendelkeznie.

  - Az alkalmazás kialakítása megkezdése előtt tegye tisztában az identitás modell? Hogyan azt állapítja meg, hogy a felhasználók, akik mondják, és milyen egy felhasználó jogosult ehhez?

  - Az alkalmazásom végez a bizalmas vagy a fontos feladatok (például a pénzügyi, ajtó feloldása vagy orvosi továbbítása)?
    Fontolja meg, hogyan fogja ellenőrizni a felhasználó bizalmas feladatot végez, amelynek jogosítványa van elvégezni a feladatot, és hogyan fogja hitelesíteni, hogy az a személy, akinek mondják. Engedélyezés (AuthZ) a művelet egy hitelesített biztonsági megoldást valósíthatunk egyszerű engedélyt illetően, történik. Hitelesítés (hitelesítés) az a kihívást a jogosult hitelesítő adatokat egy entitás.

  - Hajtsa végre kockázatos szoftver tevékenységeket, például feltölthet, vagy töltse le a fájlokat vagy más adatokat a felhasználók az alkalmazásom? Ha az alkalmazás a kockázatos tevékenység hajtható végre, vegye figyelembe, hogy az alkalmazás hogyan védelme felhasználók kártevő fájlok és adatok kezelése alól.

### <a name="review-owasp-top-10"></a>Felülvizsgálat OWASP top 10
Fontolja meg, tekintse át a [ <span class="underline">OWASP Top 10-es alkalmazás biztonsági kockázatokat</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
Az OWASP Top 10 címek webalkalmazások számára kritikus fontosságú biztonsági kockázatokat.
A felsorolt biztonsági kockázatok kontrollálhassa segíthet vonatkozó követelmény és tervezési döntéseket hozhat, minimálisra csökkentik azokat az említett kockázatok az alkalmazásban.

Fontos szem előtt tartva adatszivárgás megelőzése érdekében a biztonsági vezérlők.
Azonban is érdemes [feltételezése](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) történik. Feltéve, hogy megsértése segít kapcsolatos biztonsági néhány fontos kérdésre választ előzetesen, hogy ne kelljen vészhelyzet vet fel:

  - Hogyan észleli a támadás?

  - Mit fog tenni, ha egy támadás vagy illetéktelen behatolás?

  - Hogyan fogom helyreállíthatja a támadást, mint az adatok kiszivárgását vagy illetéktelen módosítást?

## <a name="design"></a>Tervezés

A tervezési fázis, kritikus fontosságú gyakorlati tanácsok a tervezési és funkcionális leírások létrehozásához szükséges. Azt is fontos, amely segít a projekt teljes biztonsági és adatvédelmi problémák kockázatelemzésen végrehajtásához.

Ha biztonsági követelmények vonatkozhatnak a helyen, és használjon biztonságos tervezési alapelvek, elkerülése érdekében, vagy minimalizálása érdekében a biztonsági hibája lehetőségeit. Egy biztonsági hiba az alkalmazás, előfordulhat, hogy lehetővé teszi, hogy az alkalmazás kiadása után hajtsa végre a rosszindulatú vagy váratlan műveletet tervezési-felügyeletet.

A tervezési fázis során is gondolja miként alkalmazhatja a biztonsági rétegek; egy szint védelmi vonalként nem elég feltétlenül. Mi történik, ha egy támadó a webalkalmazási tűzfal (WAF) túlra? Azt szeretné, hogy egy másik biztonsági ellenőrzés, hogy a támadások elleni védelemre a helyen.

Ezt szem bemutatjuk a következő biztonságos tervezési alapelvek és a biztonsági vezérlőket kell foglalkozni a biztonságos alkalmazások tervezésekor:

- Biztonságos kódolási szalagtár és a egy szoftveres keretrendszer használni.
- Sebezhető összetevők vizsgálata.
- Használja a fenyegetések modellezése alkalmazás tervezése során.
- A támadási felület csökkentése.
- Fogadja el egy házirendet az identitás, az elsődleges biztonsági határt.
- Újbóli hitelesítés szükséges fontos tranzakciókra vonatkozóan.
- A kulcskezelési megoldást használni a kulcsokat, hitelesítő adatok és egyéb titkok biztonságos.
- Bizalmas adatok védelme.
- Bemutatjuk a hibamentes mértékeket.
- Kihasználhatja a hiba- és kivételkezelés.
- Naplózási és riasztási használja.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Biztonságos kódolási szalagtár és a egy szoftveres keretrendszer

Fejlesztéshez egy biztonságos kódolási szalagtár és a egy szoftveres keretrendszer, amely a beágyazott biztonsági használja. A fejlesztők a meglévő, bevált funkciók (titkosítás, bemeneti fertőtlenítést is, a kódolási kimeneti, kulcsok vagy kapcsolati karakterláncok és bármi olyanra, amely a biztonsági ellenőrzés magánjellegűnek tekintett) biztonsági vezérlők előzmények fejlesztése helyett. Ez segít a biztonsággal kapcsolatos tervezési és megvalósítási hibái elleni védelme.

Lehet, hogy a keretrendszer és az összes keretében elérhető biztonsági funkciók legújabb verzióját használja-e. A Microsoft kínál átfogó [fejlesztői eszközkészletet nyújt](https://azure.microsoft.com/product-categories/developer-tools/) összes dolgozó fejlesztők számára, bármely platformon és nyelven, a felhőbeli alkalmazások létrehozásához. A nyelven kódolhat különféle tárházából választva [SDK-k](https://azure.microsoft.com/downloads/).
Kihasználhatja a teljes körű integrált fejlesztési környezetekkel (ide-ket), és szerkesztőktől kezdve, amely speciális hibakeresési képességeket és a beépített Azure-támogatást.

A Microsoft számos olyan [nyelvek, keretrendszerek és eszközök](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) használható az Azure-alkalmazások fejlesztéséhez. Például [Azure .NET és .NET Core-fejlesztőknek](https://docs.microsoft.com/dotnet/azure/). Minden nyelv és keretrendszer, amely kínálunk látni fogja a rövid útmutatók, oktatóanyagok és API-referenciák, a gyors induláshoz.

Az Azure webhelyek és webes alkalmazások üzemeltetéséhez használt olyan szolgáltatásokhoz különböző kínál. Ezek a szolgáltatások lehetővé teszik a kedvenc nyelvén fejleszthet, amelyek e .NET, .NET Core, Java, Ruby, Node.js, PHP vagy Python.
[Az Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (webalkalmazások) egyike ezeket a szolgáltatásokat.

A Web Apps az Microsoft Azure ad hozzá az alkalmazást. Biztonsági, terheléselosztási, automatikus skálázást és az automatikus kezelés tartalmazza. Akkor is kihasználhatja fejlesztési és tesztelési lehetőségeket Web Apps, a csomagkezelés, például átmeneti környezetek, egyéni tartományokat, SSL/TLS-tanúsítványokkal és folyamatos üzembe helyezést az Azure DevOps, a GitHub, a Docker Hub és a más forrásokból.

Az Azure webhelyek és webes alkalmazások üzemeltetése is használó egyéb szolgáltatásokba kínál. A legtöbb forgatókönyvhöz a Webalkalmazások a legjobb választás. Fontolja meg egy micro szolgáltatás architektúrája [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Ha a kódot futtató virtuális gépek nagyobb mértékű felügyeletére van szüksége, akkor érdemes megfontolnia az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát.
Az Azure-szolgáltatások közötti választással kapcsolatos további információkért lásd: egy [Azure App Service, Virtual Machines, Service Fabric és Cloud Services összehasonlítása](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Frissítések alkalmazása az összetevők

Biztonsági rések elkerülése érdekében érdemes folyamatosan leltárt az ügyféloldali és kiszolgálóoldali összetevők (például keretrendszerek és könyvtárak) és a frissítések függősége. Folyamatosan jelennek meg új biztonsági réseket és a frissített szoftververziók. Győződjön meg arról, hogy egy folyamatban lévő terv figyelése, osztályozhatja és frissítések alkalmazása van, vagy a konfiguráció módosításait a szalagtárak és -összetevők használata.

Tekintse meg a [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) lap [összetevőket használ az ismert biztonsági rések](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) eszköz javaslatokért. Biztonsági rések kapcsolódó összetevők használata az e-mailes riasztásokhoz is előfizetni.

### <a name="use-threat-modeling-during-application-design"></a>Alkalmazás-tervezés során modellezési threat használata

Fenyegetések modellezése az a folyamat az üzleti és az alkalmazás potenciális biztonsági kockázatok azonosítása, és majd a megfelelő megoldások biztosítják az biztosítása. Az SDL-ből Megadja, hogy a tervezési fázisban, amikor potenciális problémák megoldása, viszonylag egyszerű és költséghatékony modellezési threat csapatok kell végezhetnek. A tervezési fázisban modellezés nagy mértékben csökkentheti a teljes fejlesztési költség threat használatával.

A fenyegetés a modellezési folyamat megkönnyítése érdekében alakítottuk ki a [SDL Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) szem előtt, nem biztonsági szakértőkkel. Ez az eszköz megkönnyíti a fenyegetések modellezése minden fejlesztő számára azáltal, hogy világos útmutatóval szolgál azokról, hogyan hozhat létre és enyhítési elemzése.

Az alkalmazás-tervezés modellezési és számbavétele [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) fenyegetések – Spoofing, módosítás, Letagadhatóság, Információfelfedés, szolgáltatásmegtagadás és jogok kiterjesztését – közötti összes megbízhatósági határait bizonyult hatékony módszert a Tervező hibák már a legelején észlelését. A következő táblázat sorolja fel a STRIDE fenyegetések, és bizonyos példa megoldások, Azure által biztosított szolgáltatásokat használó biztosít. Ezek a megoldások minden helyzetben nem fog működni.

| Fenyegetés | Biztonsági tulajdonság | Az Azure platform lehetséges megoldás |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Címek hamisítása               | Authentication        | [HTTPS-kapcsolatokat](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Módosítás              | Integritás             | Ellenőrizze az SSL/TLS-tanúsítványokkal. SSL/TLS használó alkalmazások teljes ellenőriznie kell az X.509-tanúsítványokat, az entitásokat, amelyekhez csatlakoznak. Az Azure Key Vault-tanúsítványok használata [kezelése a x509 tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| Letagadhatóság            | Letagadhatatlanság       | Engedélyezze az Azure [monitorozási és diagnosztikai](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Információfelfedés | Bizalmas       | Bizalmas adatok titkosítását [inaktív](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) és [átvitel](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Szolgáltatásmegtagadás      | Rendelkezésre állás          | Teljesítmény-mérőszámok lehetséges szolgáltatásmegtagadás szolgáltatási feltételek figyelése Kapcsolat szűrők megvalósításához. [Az Azure DDoS protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), alkalmazás-tervezés – ajánlott eljárások kombinálva, DDoS-támadásokkal szembeni védelmet biztosít.|
| Jogok kiterjesztése | Authorization         | Az Azure Active Directory használatával <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>A támadási felület csökkentése

A támadási felület a teljes összeg, ahol a potenciális biztonsági réseket fordulhatnak elő. Ez a tanulmány koncentrálunk egy alkalmazás támadható felületét.
Alkalmazások védelme a támadás való összeállításán van. Egy egyszerű és gyors csökkenteni a támadási felületet módja nem használt erőforrások és a kód eltávolítása az alkalmazásból. A kisebb az alkalmazáshoz, a kisebb a támadási felületet. Ha például eltávolítása:

- A funkciók még még nincs nyilvánosan kódja.
- Támogatási kód hibakereséséhez.
- Hálózati adapterek és protokollokkal, amelyek nem használnak, vagy amely már elavult.
- Virtuális gépek és egyéb erőforrásokat, de nem használja.

Az erőforrások rendszeres karbantartása állapotát, és annak biztosítása, hogy távolítsa el a nem használt kód módon nagyszerű győződjön meg róla, hogy nincsenek-e kevesebb lehetőségek az kártékony szándékú felhasználók a támadásokkal szemben.

A részletes, és részletes a támadási felület csökkentése módja egy támadási felület elemzése végrehajtásához. A támadási felület elemzése segít képezze le a kell vizsgálni és biztonsági rések tesztelt igénylő rendszer egyes részei.

A támadási felület elemzése célja, hogy megérteni az alkalmazások a kockázati területek úgy, hogy a fejlesztők és a biztonsági szakértők tudatában meg nyitva a támadásokkal szemben, az alkalmazás mely részei legyenek. Ezt követően annak minimalizálása érdekében ez lehetséges, nyomon követése módjai mikor és hogyan változik-e a támadási felületet, és Mi ez azt jelenti, hogy a kockázat szempontjából.

A támadási felület elemzése segít azonosítani:

- Függvények és a rendszer, tekintse át és teszteléséhez szükséges a biztonsági kockázatokat.
- Magas kockázatú területeit kódot (a rendszer védelmét igénylő részei) defense jellegű védelem megkövetelése.
- Amikor változtathatja meg a támadási felületet, és frissítenie kell egy fenyegetettség kiértékelése.

Csökkenti a támadók számára a potenciális gyenge helyszíni vagy a biztonsági rések kihasználására lehetőségek megköveteli, hogy alaposan elemezheti az alkalmazás általános támadási felületét. Azt is tartalmaz, letiltása, vagy a minimális jogosultság elvének alkalmazása a rendszerszolgáltatások való hozzáférés korlátozása, és alkalmazó rétegzett védelmet, amikor csak lehetséges.

Megbeszélhetünk [támadási felület felülvizsgálat végző](secure-develop.md#conduct-attack-surface-review) az SDL-ből, az ellenőrzési fázisban.

> [!NOTE]
> **Mi a különbség a fenyegetések modellezése és a támadási felület elemzése?**
Fenyegetések modellezése az a folyamat, az alkalmazás az esetleges biztonsági fenyegetések azonosítása és meggyőződött arról, hogy a behatolások megfelelő megoldások helyen. Támadási felület elemzése azonosítja a magas kockázatú területeit kód nyissa meg a támadásokkal szemben. Ez magában foglalja a megtalálni a módszereket, az alkalmazás és megtekintésével és tesztelése a fenti területekre kód az alkalmazás telepítése előtt magas kockázatú területek védelemre.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Az elsődleges biztonsági határt, egy házirendet az identitás elfogadása

A felhőalapú alkalmazások tervezésekor fontos bontsa ki a biztonsági szegélyhálózat-alapú fókusz, hogy hálózati-központú a olyan identitás-központú megközelítést. Hagyományosan az elsődleges helyszíni biztonsági határt volt egy szervezet hálózatához. A legtöbb a helyszíni biztonsági tervek, az elsődleges biztonsági pivot használni a hálózatot. A felhőalapú alkalmazásokhoz hogy jobban által kiszolgált identitása, mint az elsődleges biztonsági határ a mérlegeli.

Dolog, amire alkalmas fejlesztéséhez egy identitás-központú módszer a webes alkalmazások fejlesztéséhez használható:

- A felhasználók számára a többtényezős hitelesítés kikényszerítéséhez.
- Használjon erős hitelesítési és engedélyezési platformot.
- A legalacsonyabb jogosultsági szint elvét alkalmazni.
- Igény szerinti hozzáférés megvalósításához.

#### <a name="enforce-multi-factor-authentication-for-users"></a>A felhasználók számára a többtényezős hitelesítés

A kétfaktoros hitelesítést használni. Kétfaktoros hitelesítés hitelesítés és engedélyezés a jelenlegi standard, mivel ezzel elkerülheti a biztonsági hiányosságok, amely a felhasználónév és jelszó típusú hitelesítés öröklődnek. Az Azure-kezelőfelületek (az Azure portal/távoli PowerShell) és a ügyfél felé irányuló szolgáltatások elérésére terveztek és konfigurálni kell az használandó [Azure multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Használjon erős hitelesítési és engedélyezési platformot

Használja a platform által biztosított hitelesítési és engedélyezési mechanizmusokkal egyéni kód helyett. Ennek az oka egyéni kód fejlesztése gyakran fordul elő hiba is lehet. Kereskedelmi-kódokat (például Microsoft) gyakran biztonsági nagymértékben felül. [Az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) identitás és hozzáférés-kezelés az Azure-megoldás. Ezek az Azure AD-eszközök és szolgáltatások segítségével biztonságos fejlesztési:

- [Az Azure AD identity platform (Azure AD fejlesztőknek)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) van olyan felhőalapú identitásszolgáltatás, amellyel a fejlesztők olyan alkalmazások fordítása, biztonságosan tudjon felhasználókat bejelentkeztetni. Az Azure AD segít a fejlesztőknek szól, akik egybérlős, az üzletági (LOB) alkalmazásokat készítenek és a fejlesztők számára, akik a több-bérlős alkalmazások fejlesztéséhez. Mellett alapszintű bejelentkezés Azure AD-vel beépített alkalmazások meghívhatja a Microsoft APIs és az egyéni API-kat az Azure AD platformra épül. Az Azure AD identity platform támogatja a szabványos protokollok, például az OAuth 2.0 és OpenID Connect.

- [Az Azure Active Directory B2C (Azure AD B2C-vel)](https://docs.microsoft.com/azure/active-directory-b2c/) van egy identitáskezelő szolgáltatás segítségével testre szabhatja és szabályozhatja, hogy az ügyfelek regisztrálni, jelentkezzen be és profiljuk kezelését, az alkalmazások használatakor. Ez magában foglalja többek között fejlesztett iOS, Android-és .NET-alkalmazások. Az Azure AD B2C lehetővé teszi, hogy ezek a műveletek során a felhasználói identitások védelme.

#### <a name="apply-the-principle-of-least-privilege"></a>A legalacsonyabb jogosultsági szint elvét alkalmazni

A kiszolgálóvirtualizálás [legalacsonyabb jogosultsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) azt jelenti, mivel a felhasználók számára a hozzáférés és -felügyelet a feladatokat, és semmi további szükséges pontos szintjét.

A szoftverfejlesztők tartományi rendszergazdai jogosultsággal kell? Egy asszisztens kell a személyi számítógépen rendszergazdai vezérlők a hozzáférést? Szoftver hozzáférést kiértékelése semmiben nem különbözik. Ha [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) biztosíthat a felhasználóknak különböző lehetőségeket és a kiállítói az alkalmazásban, nem engedélyezi a Mindenki mindenhez hozzáfér. Mire szükség az egyes szerepkörökhöz való hozzáférés korlátozásával korlátozza a kockázata, hogy egy biztonsági probléma lépett fel.

Győződjön meg arról, hogy az alkalmazás kikényszeríti [legalacsonyabb jogosultsági](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) során az adathozzáférési mintáknak megfelelően.

> [!NOTE]
>  A legalacsonyabb jogosultsági szabályokat a szoftvert, és a szoftver létrehozása személyek alkalmazni kell. Szoftverfejlesztők lehet egy hatalmas informatikai biztonsági kockázat, ha túl sok hozzáférés generálnak számukra. A következmények súlyos lehet, ha egy fejlesztő rendelkezik az illető ártó szándékkal, vagy túl sok hozzáférést kap. Azt javasoljuk, hogy a legalacsonyabb jogosultsági szabályokat alkalmazza a fejlesztők a fejlesztési életciklus során.

#### <a name="implement-just-in-time-access"></a>Igény szerinti hozzáférés megvalósításához

Alkalmazzon *just-in-time* (JIT) hozzáférési tovább csökkentheti a jogosultságok expozíciós idejének csökkentéséhez. Használat [az Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) való:

- Engedélyezheti a felhasználók csak igény szerinti szükséges engedélyeket.
- Szerepkörök hozzárendelése egy rövidített időtartamának magabiztosan a jogosultságok automatikusan visszavonódnak.

### <a name="require-re-authentication-for-important-transactions"></a>Újbóli hitelesítés szükséges fontos tranzakciók

[Webhelyközi kérések hamisításának megakadályozása](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (más néven *XSRF* vagy *CSRF*) üzemeltetett webes alkalmazások, amelyben a rosszindulatú webes alkalmazás befolyásolja a közötti interakció ügyfél böngészője és a egy webes elleni támadás olyan alkalmazás, amely megbízik a böngészőben. Webhelyközi kérések hamisítása is lehetséges, mert webböngészők webhelynek küldött hitelesítési tokenek bizonyos típusú automatikusan minden kéréssel.
Ezt a kérdőívet kihasználásának más néven van egy *egykattintásos támadási* vagy *haladás munkamenet* , mert a támadás kihasználja a felhasználó korábban már hitelesített munkamenet.

Az ilyen típusú támadások elleni védelemre a legjobb módja, hogy kérje meg a felhasználót a hiba csak a felhasználó által biztosított minden fontos tranzakció, például a beszerzés, a fiók inaktiválása vagy a jelszó módosítása előtt. Előfordulhat, hogy a kérnie a felhasználót, hogy újra megadnia a jelszavát, fejezze be a captcha, vagy küldje el a titkos jogkivonat, amely csak a felhasználó lenne. A leggyakrabban használt módszer a titkos jogkivonat.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>A kulcsokat, hitelesítő adatok és egyéb titkok biztonságos kulcskezelés megoldás használatával

Kulcsok és hitelesítő adatok elvesztése gyakori probléma. Csak rosszabb, mint a kulcsok és hitelesítő adatok elvesztése kellene a jogosulatlan személyek érni őket. A támadók kihasználhatja az automatikus és manuális technikák kulcsok és titkok forráskódtárakból, például a Githubon tárolt található. Kulcsok és titkos kulcsok ne helyezzen el, ezek nyilvános kódtárházakhoz vagy bármilyen más kiszolgálót.

Mindig helyezze el a kulcsokat, a tanúsítványok, a titkos kulcsok és a kapcsolati karakterláncok egy kulcskezelési megoldást. Használhatja egy központi megoldás, amelyben kulcsok és titkos kódok találhatók a hardveres biztonsági modulokban (HSM). Az Azure biztosít a felhőben a hardveres biztonsági modul [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

Key vault egy *titkoskód-tárolót*: egy központosított felhőbeli szolgáltatás, alkalmazás titkainak tárolásához. A Key Vault biztosítja, hogy a bizalmas adatok biztonságos a titkos alkalmazáskulcsok egyetlen központi helyen tárolja, és biztonságos hozzáférést, az engedélyek ellenőrzést és a hozzáférés naplózásának megadásával.

Egyes titkos kódok tárolt *tárolók*. Az egyes tárolókban rendelkezik a saját konfigurációs és biztonsági szabályzatokat, amelyekkel szabályozható a hozzáférés. Az adatok REST API-n keresztül, vagy egy ügyfél SDK-t, amely elérhető a legtöbb programozási nyelv keresztül kap.

> [!IMPORTANT]
> Az Azure Key Vault konfigurációs titkok kiszolgálói alkalmazások tárolására szolgál. Nem célja a felhasználók tartozó adatok tárolására. Ez megjelenik a teljesítményt nyújt, API és költségmodellt.
>
> Szeretné tárolni, felhasználói adatok, például egy Azure SQL Database-példányt, amely rendelkezik a transzparens adattitkosítás (TDE) vagy a storage-fiókban, amely használja az Azure Storage Service Encryption. Az Azure Key Vaultban való hozzáférést, ezek az alkalmazás által használt titkos kulcsokat is tartani.

### <a name="protect-sensitive-data"></a>Bizalmas adatok védelme

Az adatok védelmének biztonsági stratégia fontos részét képezi.
Az adatok besorolását, és az adatvédelem azonosítása kell segítségével alakítsa ki alkalmazását az adatok biztonsági szem előtt. Írisz (be) tárolt adatokat érzékenység szerint, és az üzletmenetre gyakorolt hatás segít a fejlesztőknek a társított adatokat a kockázatok határozza meg.

Címkével megjelölve alkalmazható az összes adat, az adatformátumok a célnyelven tervezésekor. Győződjön meg arról, hogy az alkalmazás kezeli-e megjelölve a megfelelő adatokat. Ezen eljárások segítségével a bizalmas adatok védelmet:

- Titkosítás használata.
- Kerülje a titkos kulcsok fix kódolása például kulcsokat és jelszavakat.
- Győződjön meg arról, hogy hozzáférés-vezérlés és a naplózás helyen.

#### <a name="use-encryption"></a>A titkosítás használata

Az adatok védelmének fontos része a biztonsági stratégiát kell lennie.
Ha az adatok egy adatbázisban tárolt, vagy ha helyek közötti áthelyezése oda-vissza, a titkosítás használata [inaktív adatok](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (bár az adatbázisban) és a titkosítás [átvitt adatok](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (megérkezik, és a felhasználó elől az adatbázis, egy API-t vagy service-végpont). Azt javasoljuk, hogy mindig használjon az SSL/TLS-protokollok exchange-adatok. Győződjön meg arról, hogy a titkosítás TLS legújabb verzióját használja (jelenleg ez az 1.2-es verziójában).

#### <a name="avoid-hard-coding"></a>Kerülje a fix kódolása

Néhány dolgot soha nem változtatható a szoftverből kell lennie. Néhány példa olyan állomásnevek vagy IP címek, URL-címek, e-mail-címeket, felhasználónevek, jelszavak, a tárfiókkulcsok és más kriptográfiai kulcsok. Vegye fontolóra előírásokat mi is, vagy nem változtatható a kódban, beleértve a Megjegyzés szakaszokban a kódot.

A kódban hozzászólások helyezett, győződjön meg arról, hogy a bizalmas adatokat nem menti. Ez magában foglalja az e-mail címet, a jelszó, a kapcsolati karakterláncok, a az alkalmazással kapcsolatos információk volna csak által ismert valaki a szervezetben, és semmi mást, amelyek előfordulhat, hogy egy támadó a az alkalmazás vagy a szervezet intézményt előny .

Alapvetően azt feltételezik, hogy minden fejlesztési projektjéhez lesz nyilvános Tudásbázis telepítése. Kerülje bármiféle bizalmas adatokat a projektben.

Korábban már volt szó [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). A Key Vault segítségével tárolja a titkokat – például a kulcsok és jelszavak helyett fix kódolása őket. Használatakor a Key Vault felügyelt identitások együtt az Azure-erőforrások, az Azure-alapú webes alkalmazás is titkos konfigurációs értékek könnyű és biztonságos hozzáférést titkos kulcsok tárolása a verziókövetés vagy a konfiguráció nélkül. További tudnivalókért lásd: [kiszolgáló alkalmazásait az Azure Key Vault titkos kulcsainak kezelése](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Hibamentes intézkedések végrehajtása

Lehet, hogy az alkalmazás tudja kezelni [hibák](https://docs.microsoft.com/dotnet/standard/exceptions/) végrehajtás során történik, amely egységes módon. Az alkalmazás minden kell más hibákat és vagy sikertelen csökkentett vagy lezárt.

Gondoskodnia kell arról, hogy a megfelelő felhasználói környezet azonosíthatja a gyanús vagy rosszindulatú tevékenységből hibákat naplózza. Naplók, hogy a késleltetett nyomkereső elegendő ideig kell megőrizni. Naplók egy log management megoldás által könnyen felhasználható olyan formátumban kell lennie. Győződjön meg arról, hogy a biztonsági kapcsolatos hibákat a figyelmeztetéseket. Nincs elegendő naplózás és figyelés lehetővé teszi a támadók további rendszerek támadásokkal szemben, és adatmegőrzés karbantartása.

### <a name="take-advantage-of-error-and-exception-handling"></a>Kihasználhatja a hiba- és kivételkezelés

Javítsa ki a hibát a végrehajtási és [kivételkezelés](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) védelmi vonalként kódolási fontos része. Hiba- és kivételkezelés szempontjából fontos a rendszer megbízható és biztonságos. Különböző típusú biztonsági réseket, például a támadók információkat kiszivárgását és útmutatás nyújtása a támadók ismerje meg jobban a platform és tervezési hibakezelést a hibáktól vezethet.

Győződjön meg arról, hogy:

- Kezeli a kivételek elkerülése érdekében centralizált módon duplikált [try/catch blokkok](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) a kódban.

- Az alkalmazáson belüli összes nem várt működést kezeli.

- A felhasználók számára megjelenő üzeneteket nem nyilvánosságra kerüljenek a kritikus fontosságú adatokat, de azt ismertetik, a probléma elegendő információt tartalmaznak.

- Kivétel jelentkezett, és vizsgálatára forensics vagy incidens válasz csapatok számára elegendő információt nyújtanak.

[Az Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) a fürtkezelési élményt nyújt [hibák és kivételek kezelése](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) függő rendszerek által okozott. A Logic Apps segítségével automatizálhatja a feladatokat és folyamatokat, amelyek integrálják az alkalmazások, adatok, rendszerek és szolgáltatások vállalaton vagy intézményen belül munkafolyamatokat hozhat létre.

### <a name="use-logging-and-alerting"></a>Használható naplózási és riasztási

[Napló](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) a biztonsági kérdések, a biztonsági vizsgálatok, és időben kapcsolatos problémák ismert problémákról, győződjön meg arról, hogy a felhasználók eseményindító riasztások. Engedélyezze a vizsgálati és naplózási lévő valamennyi összetevőnél. Auditnaplók kell rögzíteni a felhasználói környezet, és azonosítsa az összes fontos eseményekről.

Ellenőrizze, hogy bármilyen bizalmas adathoz, hogy egy felhasználó beküld nem jelentkezik a helyhez. Bizalmas adatok közé:

- Felhasználói hitelesítő adatok
- Társadalombiztosítási számot vagy egyéb azonosításra
- Hitelkártyaszámokat és egyéb pénzügyi adatait
- Egészségügyi információk
- Titkos kulcsok vagy egyéb adatok titkosított adatok visszafejtésére szolgáló
- Rendszer vagy alkalmazás-információkat, amelyek segítségével hatékonyabban támadásokkal szemben az alkalmazás

Győződjön meg arról, hogy az alkalmazás figyeli-e a felhasználói felügyeleti események például a sikeres és sikertelen felhasználói bejelentkezések, új jelszó kérésekor, jelszó módosítására, fiókzárolás és felhasználói regisztráció. Ezeket az eseményeket a naplózás segítségével észlelheti és reagálhat a potenciálisan gyanús viselkedését. Azt is lehetővé teszi az adatgyűjtéshez műveletek, például, hogy ki fér hozzá az alkalmazást.

## <a name="next-steps"></a>További lépések
Az alábbi cikkeket azt javasoljuk, hogy a biztonsági vezérlők és tevékenységek, amelyek segítségével fejleszthet, és helyezzen üzembe biztonságos alkalmazásokat.

- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
- [Biztonságos alkalmazások üzembe helyezése](secure-deploy.md)
