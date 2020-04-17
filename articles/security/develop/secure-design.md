---
title: Biztonságos alkalmazások tervezése a Microsoft Azure-ban
description: Ez a cikk ismerteti a webes alkalmazás projekt követelmény- és tervezési fázisai során figyelembe vesszen ajánlott eljárásokat.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c9e3cfa689f2e528f4d20e796017ae9d91c29fe2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461718"
---
# <a name="design-secure-applications-on-azure"></a>Biztonságos alkalmazások tervezése az Azure-ban
Ebben a cikkben bemutatjuk a biztonsági tevékenységek és vezérlők, hogy fontolja meg, amikor alkalmazásokat tervez a felhőben. A [microsofti biztonsági fejlesztési életciklus (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) követelményei és tervezési fázisai során figyelembe veendő biztonsági kérdések kel és koncepciókkal kapcsolatos képzési segédanyagokról van szó. A cél az, hogy segítsen meghatározni a tevékenységek et és az Azure-szolgáltatásokat, amelyek segítségével egy biztonságosabb alkalmazás tervezéséhez használható.

Ez a cikk a következő SDL fázisokat ismerteti:

- Képzés
- Követelmények
- Tervezés

## <a name="training"></a>Képzés
Mielőtt elkezdené fejleszteni a felhőalapú alkalmazást, szánjon időt az Azure biztonságának és adatvédelmének megismerésére. Ezzel a lépéssel csökkentheti az alkalmazás kihasználható biztonsági réseinek számát és súlyosságát. Felkészültebb lesz, hogy megfelelően reagáljon a folyamatosan változó fenyegetési környezetre.

A következő erőforrások használatával ismerkedjen meg a fejlesztők számára elérhető Azure-szolgáltatásokkal és az Azure-ral kapcsolatos gyakorlati tanácsokkal:

  - [Az Azure fejlesztői útmutatója bemutatja,](https://azure.microsoft.com/campaigns/developer-guide/) hogyan kezdheti el az Azure-t. Az útmutató bemutatja, hogy mely szolgáltatásokkal futtathatja az alkalmazásokat, tárolhatja az adatokat, építhet it-alkalmazásokat, és telepítheti megoldásait hatékonyabban és biztonságosabban.

  - [Az Azure-fejlesztők nek szóló útmutató](../../guides/developer/azure-developer-guide.md) alapvető információkat nyújt azoknak a fejlesztőknek, akik fejlesztési igényeiknek megfelelően szeretnék használni az Azure platformot.

  - [Az SDK-k és az eszközök](https://docs.microsoft.com/azure/index?pivot=sdkstools) ismertetik az Azure-ban elérhető eszközöket.

  - [Az Azure DevOps Services](https://docs.microsoft.com/azure/devops/) fejlesztési együttműködési eszközöket biztosít. Az eszközök közé tartoznak a nagy teljesítményű folyamatok, az ingyenes Git-adattárak, a konfigurálható Kanban táblák, valamint a kiterjedt automatizált és felhőalapú terheléstesztelés.
    A [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) egyesíti az erőforrásokat a DevOps-eljárások, a Git-verziókövetés, az agilis módszerek, hogyan dolgozunk a DevOps a Microsoft, és hogyan értékelheti a saját DevOps-progresszió.

  - [Az éles környezetbe való lenyomáselőtt figyelembe veendő öt legfontosabb biztonsági elem](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) megmutatja, hogyan védheti meg webes alkalmazásait az Azure-ban, és hogyan védheti meg alkalmazásait a leggyakoribb és legveszélyesebb webalkalmazás-támadásokkal szemben.

  - [A Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) parancsfájlok, eszközök, bővítmények és automatizálások gyűjteménye, amely kielégíti a kiterjedt automatizálást használó DevOps-csapatok átfogó Azure-előfizetési és erőforrás-biztonsági igényeit. Az Azure-hoz való Biztonságos DevOps-készlet bemutatja, hogyan integrálhatja zökkenőmentesen a biztonságot a natív DevOps-munkafolyamatokba. A készlet olyan eszközökkel foglalkozik, mint a biztonsági ellenőrző tesztek (SVTs), amelyek segítségével a fejlesztők biztonságos kódot írhatnak, és tesztelhetik a felhőalkalmazások biztonságos konfigurációját a kódolási és a korai fejlesztési fázisokban.

  - [Az Azure-megoldások biztonsági gyakorlati tanácsai](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) a felhőalapú megoldások tervezése, üzembe helyezése és kezelése során az Azure-ban használható biztonsági gyakorlati tanácsok gyűjteményét biztosítják.

## <a name="requirements"></a>Követelmények
A követelmények definíciós fázisa döntő lépés annak meghatározásában, hogy mi az alkalmazás, és mit fog tenni, amikor megjelenik. A követelmények fázis is egy ideje, hogy gondolja át a biztonsági vezérlőket, hogy az alkalmazásba épít. Ebben a fázisban is megkezdi a lépéseket, amelyeket az SDL-ben fog tenni egy biztonságos alkalmazás felszabadítása és üzembe helyezése érdekében.

### <a name="consider-security-and-privacy-issues"></a>A biztonsági és adatvédelmi problémák mérlegelése
Ez a fázis a legjobb alkalom, hogy fontolja meg az alapvető biztonsági és adatvédelmi kérdések. A biztonság és az adatvédelem elfogadható szintjének meghatározása a projekt kezdetén segít a csapatnak:

- Ismerje meg a biztonsági problémákkal kapcsolatos kockázatokat.
- A biztonsági hibák azonosítása és javítása a fejlesztés során.
- Alkalmazza a biztonság és az adatvédelem meghatározott szintjeit a teljes projektsorán.

Amikor az alkalmazás követelményeit írja, ügyeljen arra, hogy fontolja meg a biztonsági vezérlőket, amelyek segíthetnek az alkalmazás és az adatok biztonságának megőrzésében.

### <a name="ask-security-questions"></a>Biztonsági kérdések feltehetése
Biztonsági kérdéseket tehet fel, például:

  - Az alkalmazás bizalmas adatokat tartalmaz?

  - Az alkalmazásom olyan adatokat gyűjt vagy tárol, amelyek megkövetelik, hogy betartsam az olyan iparági szabványokat és megfelelőségi programokat, mint a [Szövetségi Pénzügyi Intézetek Vizsgálótanácsa (FFIEC)](../blueprints/ffiec-analytics-overview.md) vagy a [Payment Card Industry Data Security Standards (PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - Az alkalmazásom olyan érzékeny személyes vagy ügyféladatokat gyűjt vagy tartalmaz, amelyek önmagukban vagy más adatokkal felhasználhatók egyetlen személy azonosítására, kapcsolatfelvételre vagy megkeresésre?

  - Az alkalmazásom olyan adatokat gyűjt vagy tartalmaz, amelyek felhasználhatók az egyén orvosi, oktatási, pénzügyi vagy foglalkoztatási adataihoz való hozzáféréshez? Az adatok érzékenységének azonosítása a követelmények fázisában segít az adatok osztályozásában és az alkalmazáshoz használt adatvédelmi módszer azonosításában.

  - Hol és hogyan tárolják az adataimat? Gondolja át, hogyan fogja figyelni az alkalmazás által a váratlan változásokhoz (például a lassabb válaszidőkhez) használt tárolási szolgáltatásokat. Képes lesz befolyásolni a naplózást, hogy részletesebb adatokat gyűjtsön és részletesen elemezze a problémát?

  - Az alkalmazásom elérhető lesz a nyilvánosság (az interneten) vagy csak belsőleg? Ha az alkalmazás elérhető a nyilvánosság számára, hogyan védi az esetleg gyűjtött adatokat a helytelen felhasználástól? Ha az alkalmazás csak belsőleg érhető el, fontolja meg, hogy a szervezetben kinek kell hozzáférnie az alkalmazáshoz, és mennyi ideig kell hozzáféréssel rendelkeznie.

  - Ismeri az identitásmodellt, mielőtt elkezdené az alkalmazás tervezését? Hogyan fogja meghatározni, hogy a felhasználók, akik azt mondják, hogy ők, és mi a felhasználó jogosult erre?

  - Az alkalmazásom érzékeny vagy fontos feladatokat lát el (például pénzátutalás, ajtók kinyitása vagy gyógyszer szállítása)?
    Gondolja át, hogyan ellenőrzi, hogy a bizalmas feladatot végző felhasználó jogosult-e a feladat végrehajtására, és hogyan hitelesíti, hogy a személy az, akinek mondja magát. Az engedélyezés (AuthZ) egy hitelesített rendszerbiztonsági tag engedélyének megadása. A hitelesítés (AuthN) az a cselekmény, amikor egy fél jogos hitelesítő adatokért kér.

  - Végez-e az alkalmazásom kockázatos szoftvertevékenységeket, például lehetővé teszi a felhasználók számára fájlok vagy más adatok feltöltését vagy letöltését? Ha az alkalmazás kockázatos tevékenységeket végez, fontolja meg, hogy az alkalmazás hogyan védi meg a felhasználókat a rosszindulatú fájlok vagy adatok kezelésétől.

### <a name="review-owasp-top-10"></a>OWASP top 10 áttekintése
Fontolja meg az [<span class="underline">OWASP Top 10 alkalmazásbiztonsági kockázatok áttekintését.</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
Az OWASP Top 10 a webes alkalmazások kritikus biztonsági kockázataival foglalkozik.
Ezeknek a biztonsági kockázatoknak a ismertetése segíthet olyan követelmények és tervezési döntések meghozatalában, amelyek minimálisra csökkentik ezeket a kockázatokat az alkalmazásban.

Fontos, hogy a biztonsági ellenőrzéseken gondolkodjon a jogsértések megelőzése érdekében.
Azonban azt is szeretné [feltételezni,](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) hogy a jogsértés bekövetkezik. Feltételezve, hogy a jogsértés segít előre megválaszolni néhány fontos kérdést a biztonsággal kapcsolatban, így nem kell őket vészhelyzetben megválaszolni:

  - Hogyan fogok észlelni egy támadást?

  - Mit teszek, ha támadás vagy behatolás történik?

  - Hogyan fogok felépülni a támadásból, mint az adatszivárgás vagy a manipulálás?

## <a name="design"></a>Tervezés

A tervezési fázis kritikus fontosságú a tervezési és funkcionális előírásokra vonatkozó legjobb gyakorlatok kialakításához. Emellett kritikus fontosságú a kockázatelemzés elvégzéséhez, amely segít a biztonsági és adatvédelmi problémák csökkentésében a projekt során.

Ha rendelkezik biztonsági követelményekkel, és biztonságos tervezési koncepciókat használ, elkerülheti vagy minimalizálhatja a biztonsági hibák lehetőségeit. A biztonsági hiba az alkalmazás tervezésének olyan felügyelete, amely lehetővé teheti a felhasználó számára, hogy rosszindulatú vagy váratlan műveleteket hajtson végre az alkalmazás kiadása után.

A tervezési fázisban azt is átgondolhatja, hogyan alkalmazhatja a biztonságot a rétegekben; Egy védelmi szint nem feltétlenül elég. Mi történik, ha a támadó átjut a webalkalmazás tűzfalán (WAF)? Azt akarja, hogy egy másik biztonsági ellenőrzés a helyén, hogy megvédje a támadás ellen.

Ezt szem előtt tartva a következő biztonságos tervezési koncepciókat és azokat a biztonsági vezérlőket tárgyaljuk meg, amelyekkel a biztonságos alkalmazások tervezésekor foglalkoznia kell:

- Használjon biztonságos kódolási könyvtárat és szoftverkeretrendszert.
- Keresse meg a sebezhető összetevőket.
- Fenyegetésmodellezés használata az alkalmazás tervezése során.
- Csökkentse a támadási felületet.
- Az identitás házirendjének elfogadása elsődleges biztonsági kerületként.
- A fontos tranzakciók hoz szükséges újrahitelesítés.
- Kulcskezelési megoldás használatával kulcsok, hitelesítő adatok és egyéb titkos kulcsok biztonságossá.
- Érzékeny adatok védelme.
- Hibamentes intézkedések végrehajtása.
- Használja ki a hiba- és kivételkezelés előnyeit.
- Használja a naplózást és a riasztást.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Biztonságos kódolási könyvtár és szoftverkeretrendszer használata

A fejlesztéshez használjon biztonságos kódolási könyvtárat és beágyazott biztonságot tartalmazó szoftverkeretrendszert. A fejlesztők a meglévő, bevált funkciókat (titkosítás, bemeneti higiénia, kimeneti kódolás, kulcsok vagy kapcsolati karakterláncok és bármi más, ami biztonsági vezérlőnek minősülne) használhatják a biztonsági vezérlők fejlesztése helyett. Ez segít megvédeni a biztonsággal kapcsolatos tervezési és megvalósítási hibákat.

Győződjön meg arról, hogy a keretrendszer legújabb verzióját és a keretrendszerben elérhető összes biztonsági funkciót használja. A Microsoft átfogó [fejlesztői eszközöket](https://azure.microsoft.com/product-categories/developer-tools/) kínál minden fejlesztő számára, akik bármilyen platformon vagy nyelven dolgoznak a felhőalkalmazások biztosításához. A választott nyelvvel különböző [SDK-k](https://azure.microsoft.com/downloads/)közül választhat.
Kihasználhatja a teljes funkcionalitású integrált fejlesztési környezeteket (IDe) és a speciális hibakeresési képességekkel és beépített Azure-támogatással rendelkező szerkesztőket.

A Microsoft számos [nyelvet, keretrendszert és eszközt kínál,](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) amelyek segítségével alkalmazásokat fejleszthet az Azure-ban. Ilyen például az [Azure a .NET és a .NET Core fejlesztőkszámára.](https://docs.microsoft.com/dotnet/azure/) Az általunk kínált nyelvekhez és keretrendszerekhez rövid útmutatókat, oktatóanyagokat és API-hivatkozásokat talál, amelyek segítenek a gyors kezdésben.

Az Azure számos szolgáltatást kínál, amelyekkel webhelyeket és webalkalmazásokat üzemeltethet. Ezek a szolgáltatások lehetővé teszik a fejlesztést a kedvenc nyelvén, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP vagy Python.
[Az Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) egyike ezeknek a szolgáltatásoknak.

A Web Apps hozzáadja a Microsoft Azure erejét az alkalmazáshoz. Ez magában foglalja a biztonság, a terheléselosztás, az automatikus skálázás és az automatikus felügyelet. A DevOps-funkciók at a Web Apps, például a csomagkezelés, az átmeneti környezetek, az egyéni tartományok, az SSL/TLS-tanúsítványok és az Azure DevOps, a GitHub, a Docker Hub és más források folyamatos üzembe helyezését is kihasználhatja a DevOps- és a Kvtappinó-szolgáltatásokban.

Az Azure más szolgáltatásokat is kínál, amelyek segítségével webhelyeket és webalkalmazásokat üzemeltethet. A legtöbb forgatókönyvhöz a Webalkalmazások a legjobb választás. Mikroszolgáltatás-architektúra esetén fontolja meg az [Azure Service Fabric.](https://azure.microsoft.com/documentation/services/service-fabric)
Ha a kódot futtató virtuális gépek nagyobb mértékű felügyeletére van szüksége, akkor érdemes megfontolnia az [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) használatát.
Az Azure-szolgáltatások közötti választásról az [Azure App Service, a Virtuális gépek, a Service Fabric és a Felhőszolgáltatások összehasonlítása című](/azure/architecture/guide/technology-choices/compute-decision-tree)témakörben talál további információt.

### <a name="apply-updates-to-components"></a>Frissítések alkalmazása összetevőkre

A biztonsági rések elkerülése érdekében folyamatosan leltároznia kell az ügyféloldali és a kiszolgálóoldali összetevőket (például keretrendszereket és kódtárakat), valamint a frissítésekhez való függőségeit. Az új biztonsági rések és a frissített szoftververziók folyamatosan megjelennek. Győződjön meg arról, hogy rendelkezik egy folyamatban lévő tervvel a rendszer kódtára, osztályozására és frissítések vagy konfigurációs módosítások alkalmazására a használt tárakra és összetevőkre.

Az Eszközjavaslatokhoz tekintse meg az [OWASP](https://www.owasp.org/index.php/Main_Page) megnyitása című [lapazismert biztonsági résű összetevők használatát.](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) A használt összetevőkkel kapcsolatos biztonsági résekről is előfizethet az e-mailes riasztásokra.

### <a name="use-threat-modeling-during-application-design"></a>Fenyegetésmodellezés használata az alkalmazás tervezése kor

A fenyegetésmodellezés az üzleti és alkalmazásra leselkedő potenciális biztonsági fenyegetések azonosításának, majd a megfelelő megoldások biztosításának folyamata. Az SDL meghatározza, hogy a csapatoknak a tervezési fázisban kell részt venniük a fenyegetésmodellezésben, amikor a lehetséges problémák megoldása viszonylag egyszerű és költséghatékony. A fenyegetésmodellezés használata a tervezési fázisban nagyban csökkentheti a teljes fejlesztési költséget.

A fenyegetésmodellezési folyamat megkönnyítése érdekében az [SDL threat modellező eszközt](threat-modeling-tool.md) nem biztonsági szakértőkkel együtt terveztük. Ez az eszköz megkönnyíti a fenyegetésmodellezést minden fejlesztő számára azáltal, hogy egyértelmű útmutatást nyújt a fenyegetésmodellek létrehozásához és elemzéséhez.

Az alkalmazástervezésének modellezése és a [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) fenyegetések számbavétele – hamisítás, illetéktelen beavatkozás, megtagadás, információhoz való hozzáférés, szolgáltatásmegtagadás és jogosultságilletéktelenítés – minden megbízhatósági határon keresztül hatékonynak bizonyult a tervezési hibák korai észleléséhez. Az alábbi táblázat a STRIDE fenyegetéseket sorolja fel, és néhány példacsökkentést tartalmaz, amelyek az Azure által biztosított funkciókat használják. Ezek a mérséklések nem fognak működni minden helyzetben.

| Fenyegetés | Biztonsági tulajdonság | Az Azure platform lehetséges kockázatcsökkentési lehetőségei |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Hamisítás               | Hitelesítés        | [HTTPS-kapcsolatok megkövetelése](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Hamisít              | Integritás             | SSL/TLS-tanúsítványok érvényesítése. Az SSL/TLS-t használó alkalmazásoknak teljes mértékben ellenőrizniük kell azoknak az entitásoknak az X.509 tanúsítványait, amelyekhez csatlakoznak. Az [X509-es tanúsítványok kezeléséhez](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)azure Key Vault-tanúsítványokat használhat. |
| Megtagadás            | Megtagadás elmaradása       | Engedélyezze az Azure [figyelését és diagnosztikát.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)|
| Információhoz való hozzáférés | Titkosság       | Bizalmas adatok titkosítása [nyugalmi](../fundamentals/encryption-atrest.md) és [átvitel közben.](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) |
| Szolgáltatásmegtagadás      | Rendelkezésre állás          | A szolgáltatásesetleges megtagadási feltételeinek teljesítménymutatóit figyelheti. Kapcsolatszűrők megvalósítása. [Az Azure DDoS-védelem](../../virtual-network/ddos-protection-overview.md#next-steps)– az alkalmazástervezéssel kapcsolatos gyakorlati tanácsokkal kombinálva – védelmet nyújt a DDoS-támadások ellen.|
| Jogosultság emelése | Engedélyezés         | Használja az <span class="underline"> </span> Azure Active Directory [kiemelt identitáskezelést.](../../active-directory/privileged-identity-management/pim-configure.md)|

### <a name="reduce-your-attack-surface"></a>Csökkentse a támadási felületet

A támadási felület a lehetséges biztonsági rések teljes összege. Ebben a dokumentumban az alkalmazás támadási felületére összpontosítunk.
A hangsúly az alkalmazás támadáselleni védelmén van. A támadási felület minimalizálásának egyszerű és gyors módja a fel nem használt erőforrások és a kód eltávolítása az alkalmazásból. Minél kisebb az alkalmazás, annál kisebb a támadási felület. Például távolítsa el a következőket:

- A még nem kiadott funkciók kódja.
- Hibakeresés támogatási kódja.
- Hálózati adapterek és protokollok, amelyek nincsenek használatban, vagy amelyek elavultak.
- Virtuális gépek és egyéb erőforrások, amelyek nem használ.

Az erőforrások rendszeres karbantartása és a fel nem használt kód eltávolításának biztosítása nagyszerű módja annak, hogy a rosszindulatú szereplők kevesebb lehetőséget adjanak a támadásra.

A támadási felület csökkentésének részletesebb és részletesebb módja a támadási felület elemzésének befejezése. A támadási felület elemzése segít leképezni a rendszer azon részeit, amelyeket felül kell vizsgálni és tesztelni kell a biztonsági rések szempontjából.

A támadási felület elemzésének célja, hogy megértse az alkalmazás kockázati területeit, így a fejlesztők és a biztonsági szakemberek tisztában vannak azzal, hogy az alkalmazás mely részei támadhatnak. Ezután megtalálhatja a módját, hogy minimalizálja ezt a potenciált, nyomon követheti, hogy mikor és hogyan változik a támadási felület, és hogy ez mit jelent kockázati szempontból.

A támadási felület elemzése segít azonosítani:

- A biztonsági rések ellenőrzéséhez és teszteléséhez szükséges funkciók és a rendszer egyes részei.
- Nagy kockázatú kódterületek, amelyek mélyreható védelmet igényelnek (a rendszer azon részei, amelyeket meg kell védenie).
- Amikor módosítja a támadási felületet, és frissítenie kell egy fenyegetésértékelést.

A potenciális gyenge pont vagy biztonsági rés kihasználásának lehetőségeinek csökkentése érdekében alaposan elemezni kell az alkalmazás teljes támadási felületét. Ez magában foglalja a rendszerszolgáltatásokhoz való hozzáférés letiltását vagy korlátozását, a minimális jogosultság elvének alkalmazását, valamint a réteges védelem alkalmazását, ahol csak lehetséges.

Megvitatjuk [a támadási felület felülvizsgálatának elvégzését](secure-develop.md#conduct-attack-surface-review) az SDL ellenőrzési fázisában.

> [!NOTE]
> **Mi a különbség a fenyegetésmodellezés és a támadási felület elemzése között?**
A fenyegetésmodellezés az alkalmazáspotenciális biztonsági fenyegetéseinek azonosítását és a fenyegetések elleni megfelelő megoldások biztosítását jelenti. A támadási felület elemzése azonosítja a támadásra nyitott, nagy kockázatú kódterületeket. Ez magában foglalja a módját, hogy megvédje a magas kockázatú területeken az alkalmazás és felülvizsgálata és tesztelése ezeket a kódterületeket, mielőtt az alkalmazás üzembe helyezése.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Identitási házirend elfogadása elsődleges biztonsági kerületként

Felhőalapú alkalmazások tervezésekor fontos, hogy a hálózatközpontú megközelítésről az identitásközpontú megközelítésre bővítse ki a biztonsági peremterületet. Történelmileg az elsődleges helyszíni biztonsági terület egy szervezet hálózata volt. A legtöbb helyszíni biztonsági terv a hálózatot használja elsődleges biztonsági kimutatásként. A felhőalapú alkalmazások, akkor jobban szolgálja az identitás elsődleges biztonsági kerülete identitás.

A webes alkalmazások fejlesztésének identitásközpontú megközelítésének kifejlesztésére:

- Többtényezős hitelesítés kényszerítése a felhasználók számára.
- Használjon erős hitelesítési és engedélyezési platformokat.
- Alkalmazza a minimális kiváltság elvét.
- Just-in-time hozzáférés megvalósítása.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Többtényezős hitelesítés kényszerítése a felhasználók számára

Használjon kétfaktoros hitelesítést. A kétfaktoros hitelesítés a hitelesítés és engedélyezés jelenlegi szabványa, mivel elkerüli a felhasználónév- és jelszótípusokban rejlő biztonsági hiányosságokat. Az Azure felügyeleti felületekhez (Azure portal/távoli PowerShell) és az ügyfélfelé irányuló szolgáltatásokhoz való hozzáférést úgy kell megtervezni és konfigurálni, hogy [az Azure többtényezős hitelesítést használhassa.](../../active-directory/authentication/concept-mfa-howitworks.md)

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Erős hitelesítési és engedélyezési platformok használata

Az egyéni kód helyett használjon platformáltal biztosított hitelesítési és engedélyezési mechanizmusokat. Ennek az az oka, hogy az egyéni hitelesítési kód kifejlesztése hibát okozhat. A kereskedelmi kódot (például a Microsofttól) gyakran alaposan felülvizsgálják a biztonság érdekében. [Az Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) az Azure-megoldás az identitás- és hozzáférés-kezeléshez. Ezek az Azure AD-eszközök és -szolgáltatások segítenek a biztonságos fejlesztésben:

- [Az Azure AD identitásplatform (Azure AD fejlesztőknek)](../../active-directory/develop/about-microsoft-identity-platform.md) egy felhőalapú identitásszolgáltatás, amelyet a fejlesztők olyan alkalmazások készítésére használnak, amelyek biztonságosan bejelentkeznek a felhasználókba. Az Azure AD segítséget nyújt azoknak a fejlesztőknek, akik egybérlős, üzletági (LOB) alkalmazásokat és több-bérlős alkalmazásokat szeretnének fejleszteni. Az alapvető bejelentkezésmellett az Azure AD használatával készített alkalmazások is hívhatják a Microsoft API-kat és az Azure AD platformra épülő egyéni API-kat. Az Azure AD identitásplatform támogatja az iparági szabványnak megfelelő protokollok, mint az OAuth 2.0 és az OpenID Connect.

- [Az Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) egy identitáskezelési szolgáltatás, amelynek segítségével testre szabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezzenek be és kezeljék a profiljukat az alkalmazások használata során. Ide tartoznak többek között az iOS, Android és .NET rendszerre kifejlesztett alkalmazások is. Az Azure AD B2C lehetővé teszi ezeket a műveleteket, miközben védi az ügyfél identitások.

#### <a name="apply-the-principle-of-least-privilege"></a>A legkisebb jogosultság elvének alkalmazása

A [legkevésbé privilégium](https://en.wikipedia.org/wiki/Principle_of_least_privilege) fogalma azt jelenti, hogy a felhasználók nak pontosan meg kell adniuk a hozzáférést és az ellenőrzést, hogy végezhessék a munkájukat, és semmi több.

Szüksége lenne egy szoftverfejlesztőnek tartományi rendszergazdai jogokra? Egy felügyeleti asszisztensnek szüksége van-e a személyi számítógép felügyeleti vezérlőihez való hozzáférésre? A szoftverhez való hozzáférés kiértékelése nem különbözik. Ha [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md) használatával különböző képességeket és jogosultságot biztosít a felhasználóknak az alkalmazásban, nem ad mindenkinek hozzáférést mindenhez. Ha korlátozza a hozzáférést az egyes szerepkörekhez szükséges elérésre, korlátozza a biztonsági probléma bekövetkezésének kockázatát.

Győződjön meg arról, hogy az alkalmazás a hozzáférési minták során a [legkevesebb jogosultságot](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) kényszeríti.

> [!NOTE]
>  A szabályok a legkevésbé kiváltság kell alkalmazni a szoftvert, és az emberek létre a szoftvert. A szoftverfejlesztők hatalmas kockázatot jelenthetnek az informatikai biztonságra nézve, ha túl sok hozzáférést kapnak. A következmények súlyosak lehetnek, ha a fejlesztő rosszindulatú szándékkal rendelkezik, vagy túl sok hozzáférést kap. Azt javasoljuk, hogy a szabályok a legkisebb jogosultsági kell alkalmazni a fejlesztők a fejlesztési életciklus teljes időtartama alatt.

#### <a name="implement-just-in-time-access"></a>Just-in-time hozzáférés megvalósítása

*Just-in-time* (JIT) hozzáférés megvalósítása a jogosultságok expozíciós idejének további csökkentése érdekében. Az [Azure AD kiemelt identitáskezelés használatával:](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity)

- Adja meg a felhasználóknak azokat az engedélyeket, amelyekcsak a jitre van szükségük.
- Rendeljen szerepköröket egy rövidebb időtartamhoz, bízva abban, hogy a jogosultságok automatikusan visszavonásra kerülnek.

### <a name="require-re-authentication-for-important-transactions"></a>A fontos tranzakciók újbóli hitelesítésének megkövetelése

[A webhelyek közötti kérelemhamisítás](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (más néven *XSRF* vagy *CSRF)* egy olyan webes alkalmazások elleni támadás, amelyben egy rosszindulatú webalkalmazás befolyásolja az ügyfélböngésző és az adott böngészőben megbízható webalkalmazás közötti interakciót. A webhelyek közötti kérelemhamisítási támadások azért lehetségesek, mert a webböngészők bizonyos típusú hitelesítési jogkivonatokat automatikusan küldenek a webhelyre minden kéréssel.
Ez a fajta kihasználás a rendszer úgy is ismert, mint egy *kattintásos támadás* vagy *munkamenet-lovaglás,* mert a támadás kihasználja a felhasználó korábban hitelesített munkamenet.

Az ilyen típusú támadások elleni védekezés legjobb módja, ha olyasmit kér a felhasználótól, amit csak a felhasználó tud biztosítani minden fontos tranzakció előtt, például vásárlás, fiókinaktiválás vagy jelszómódosítás előtt. Megkérheti a felhasználót, hogy adja meg újra a jelszavát, töltsön ki egy captcha-t, vagy küldjön be egy titkos jogkivonatot, amelycsak a felhasználó rendelkezik. A leggyakoribb megközelítés a titkos jogkivonat.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Kulcskezelési megoldás használata kulcsok, hitelesítő adatok és egyéb titkos kulcsok védelméhez

A kulcsok és a hitelesítő adatok elvesztése gyakori probléma. Az egyetlen dolog, ami rosszabb, mint elveszíteni a kulcsokat és a hitelesítő adatokat, az az, hogy egy jogosulatlan fél hozzáfér hozzájuk. A támadók kihasználhatják az automatikus és manuális technikákat a kódtárolókban, például a GitHubban tárolt kulcsok és titkos kulcsok megkereséséhez. Ne helyezzen kulcsokat és titkos kulcsokat ezekbe a nyilvános kódtárolókba vagy bármely más kiszolgálóra.

A kulcsokat, a tanúsítványokat, a titkos kulcsokat és a kapcsolati karakterláncokat mindig kulcskezelési megoldásba helyezze. Használhat egy központi megoldást, amelyben a kulcsok és a titkos kulcsok hardveres biztonsági modulokban (HSM) tárolódnak. Az Azure hsm-et biztosít a felhőben az [Azure Key Vault](../../key-vault/general/overview.md)segítségével.

Key Vault egy *titkos tároló:* ez egy központi felhőszolgáltatás az alkalmazás titokainak tárolására. A Key Vault biztonságban tartja a bizalmas adatokat azáltal, hogy egyetlen központi helyen tartja az alkalmazástitkos kulcsokat, és biztonságos hozzáférést, engedélyvezérlést és hozzáférés-naplózást biztosít.

A titkokat az egyes *páncéltermekben tárolják.* Minden tároló saját konfigurációval és biztonsági házirendekkel rendelkezik a hozzáférés szabályozásához. Az adatokat egy REST API-n vagy egy ügyfél SDK-n keresztül jut el, amely a legtöbb programozási nyelvhez elérhető.

> [!IMPORTANT]
> Az Azure Key Vault a kiszolgálóalkalmazások konfigurációs titkos kulcsainak tárolására szolgál. Nem az alkalmazásfelhasználókhoz tartozó adatok tárolására szolgál. Ez tükröződik a teljesítményjellemzőiben, az API-jában és a költségmodelljében.
>
> A felhasználói adatokat máshol kell tárolni, például egy Azure SQL Database-példányban, amely átlátszó adattitkosítással (TDE) rendelkezik, vagy egy Azure Storage Service Encryption szolgáltatást használó tárfiókban. Az alkalmazás által az adattárak eléréséhez használt titkos kulcsok megtekinthetők az Azure Key Vaultban.

### <a name="protect-sensitive-data"></a>Bizalmas adatok védelme

Az adatok védelme a biztonsági stratégia alapvető része.
Az adatok osztályozása és az adatvédelmi igények azonosítása segít az alkalmazás tervezésében az adatbiztonság szem előtt tartásával. A tárolt adatok osztályozása (kategorizálása) érzékenység és üzleti hatás szerint segít a fejlesztőknek az adatokhoz kapcsolódó kockázatok meghatározásában.

Az összes vonatkozó adatot bizalmasként címkézheti az adatformátumok tervezésekor. Győződjön meg arról, hogy az alkalmazás a vonatkozó adatokat bizalmasként kezeli. Ezek a gyakorlatok segíthetnek a bizalmas adatok védelmében:

- Használjon titkosítást.
- Kerülje a titkos kulcsok, például kulcsok és jelszavak kódolását.
- Győződjön meg arról, hogy a hozzáférés-vezérlés és a naplózás a helyén van.

#### <a name="use-encryption"></a>Titkosítás használata

Az adatok védelmének a biztonsági stratégia lényeges részét kell, hogy tartsa.
Ha az adatok egy adatbázisban vannak tárolva, vagy ha azok oda-vissza mozognak a helyek között, használja az [inaktív adatok](../fundamentals/encryption-atrest.md) titkosítását (az adatbázisban) és az [átvitel alatt lévő adatok](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) titkosítását (a felhasználó, az adatbázis, az API vagy a szolgáltatás végpontja felé és részéről). Azt javasoljuk, hogy mindig SSL/TLS protokollokat használjon az adatok cseréjéhez. Győződjön meg arról, hogy a TLS legújabb verzióját használja titkosításra (jelenleg ez az 1.2-es verzió).

#### <a name="avoid-hard-coding"></a>Kerülje a kódolást

Vannak dolgok, amelyeket soha nem szabad kódolni a szoftveredben. Néhány példa állomásnevek vagy IP-címek, URL-címek, e-mail címek, felhasználónevek, jelszavak, tárfiók kulcsok és egyéb kriptográfiai kulcsok. Fontolja meg a végrehajtási követelményeket arról, hogy mit lehet vagy nem lehet kódolni a kódban, beleértve a kód megjegyzés szakaszait is.

Amikor megjegyzéseket tesz a kódba, győződjön meg arról, hogy nem ment semmilyen bizalmas adatot. Ez magában foglalja az e-mail címét, a jelszavakat, a kapcsolati karakterláncokat, az alkalmazással kapcsolatos olyan információkat, amelyeket csak a szervezet en kívül ismer, és minden mást, amely előnyt biztosíthat a támadónak az alkalmazás vagy szervezet megtámadásában.

Alapvetően tegyük fel, hogy a fejlesztési projektben minden köztudott lesz, amikor telepítik. Kerülje a bizalmas adatok belefoglalását a projektbe.

Korábban az [Azure Key Vaultot](../../key-vault/general/overview.md)vitattuk meg. A Key Vault segítségével titkos kulcsokat, például kulcsokat és jelszavakat tárolhat ahelyett, hogy kódolnák őket, titkos kulcsokat és jelszavakat tárolhat. Ha a Key Vault ot az Azure-erőforrások felügyelt identitásaival együtt használja, az Azure-webalkalmazás könnyedén és biztonságosan hozzáférhet a titkos konfigurációs értékekhez anélkül, hogy a forrásvezérlőben vagy konfigurációban titkos kulcsokat tárolna. További információ: [Titkos kulcsok kezelése a kiszolgálóalkalmazásokban az Azure Key Vault használatával](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)című témakörben olvashat.

### <a name="implement-fail-safe-measures"></a>Hibabiztos intézkedések végrehajtása

Az alkalmazásnak képesnek kell lennie a végrehajtás során előforduló [hibák](https://docs.microsoft.com/dotnet/standard/exceptions/) konzisztens módon történő kezelésére. Az alkalmazásnak minden hibát fel kell fognia, és vagy biztonságosnak vagy lezártnak kell lennie.

Azt is győződjön meg arról, hogy a hibák naplózása megfelelő felhasználói környezetben a gyanús vagy rosszindulatú tevékenységek azonosításához. A naplókat elegendő ideig meg kell őrizni ahhoz, hogy lehetővé tegyék a késleltetett törvényszéki elemzést. A naplóknak olyan formátumban kell lenniük, amely et egy naplókezelési megoldás könnyen felhasználhatja. Győződjön meg arról, hogy a biztonsággal kapcsolatos hibákra vonatkozó riasztások aktiválódnak. A nem megfelelő naplózás és figyelés lehetővé teszi a támadók számára, hogy további támadási rendszereket és tartsa nak fennmaradást.

### <a name="take-advantage-of-error-and-exception-handling"></a>Használja ki a hiba- és kivételkezelés előnyeit

A helyes hiba- és [kivételkezelés megvalósítása](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) fontos része a védelmi kódolásnak. A hiba- és kivételkezelés elengedhetetlen a rendszer megbízhatóságának és biztonságossá tételéhez. A hibakezelés során elkövetett hibák különböző biztonsági résekhez vezethetnek, például információkat szivárogtatnak ki a támadóknak, és segítenek a támadóknak jobban megtudni a platformot és a kialakítást.

Győződjön meg arról, hogy:

- A kivételeket központosított módon kezeli, hogy elkerülje a kódban lévő duplikált [próbálkozási/fogási blokkokat.](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)

- Minden váratlan viselkedést az alkalmazáson belül kezel.

- A felhasználók számára megjelenített üzenetek nem szivárogtatnak ki kritikus adatokat, de elegendő információt nyújtanak a probléma magyarázatához.

- A kivételek naplózása, és elegendő információt biztosítanak a törvényszéki vagy incidens-reagálási csapatok számára a kivizsgáláshoz.

[Az Azure Logic Apps](../../logic-apps/logic-apps-overview.md) első osztályú élményt nyújt a függő rendszerek által okozott [hibák és kivételek kezeléséhez.](../../logic-apps/logic-apps-exception-handling.md) A Logic Apps segítségével munkafolyamatokat hozhat létre az alkalmazásokat, adatokat, rendszereket és szolgáltatásokat vállalatok és szervezetek közötti integráló feladatok és folyamatok automatizálásához.

### <a name="use-logging-and-alerting"></a>Naplózás és riasztás használata

[Naplózza](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) a biztonsági problémákat a biztonsági vizsgálatokhoz, és riasztást küldjön a problémákról, hogy az emberek időben megismerjék a problémákat. Engedélyezze a naplózást és a naplózást az összes összetevőn. A naplónaplóknak rögzíteniük kell a felhasználói környezetet, és azonosítaniuk kell az összes fontos eseményt.

Ellenőrizze, hogy nem naplózza-e a felhasználó által a webhelyre küldött bizalmas adatokat. A bizalmas adatok közé tartoznak például a következők:

- Felhasználói hitelesítő adatok
- Társadalombiztosítási számok vagy egyéb azonosító információk
- Hitelkártyaszámok vagy egyéb pénzügyi információk
- Egészségügyi információk
- Személyes kulcsok vagy más adatok, amelyek a titkosított adatok visszafejtésére használhatók
- Az alkalmazás hatékonyabb támadására használható rendszer- vagy alkalmazásadatok

Győződjön meg arról, hogy az alkalmazás figyeli a felhasználókezelési eseményeket, például a sikeres és sikertelen felhasználói bejelentkezéseket, a jelszó-visszaállítást, a jelszómódosításokat, a fiókzárolást és a felhasználói regisztrációt. Ezeknek az eseményeknek a naplózása segít észlelni és reagálni a potenciálisan gyanús viselkedésre. Azt is lehetővé teszi, hogy gyűjtsön műveleti adatokat, például, hogy ki fér hozzá az alkalmazáshoz.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben olyan biztonsági vezérlőket és tevékenységeket javasoljuk, amelyek segítségével biztonságos alkalmazásokat fejleszthet és helyezhet üzembe.

- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
- [Biztonságos alkalmazások telepítése](secure-deploy.md)
