---
title: "Azure-sablonok összetett megoldások kialakítása |} Microsoft Docs"
description: "Azt mutatja be ajánlott eljárások az Azure Resource Manager sablonok összetett forgatókönyvek tervezése"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ce1141d6-ece7-4976-acea-1db1f775409e
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: dcc31f7a8c85a8f7fbd554371a66fb1e348bca17
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="design-patterns-for-azure-resource-manager-templates-when-deploying-complex-solutions"></a>Az Azure Resource Manager-sablonok összetett megoldások telepítésekor tervezési minták
Egy rugalmas megközelítéssel Azure Resource Manager-sablonok alapján telepítheti a komplex topológiákba gyorsan és következetesen legyenek. Core ajánlatok fejlődnek, könnyen módosíthatja az történő központi telepítését vagy halegyedeket esetek vagy az ügyfelek a Variant típusú adatok befogadásához.

Ez a témakör egy nagyobb tanulmány részét képezi. Olvassa el a teljes dokumentum, le kell töltenie [globális osztály Azure Resource Manager sablonok szempontok és eljárások bizonyítása](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

Sablonok a következő előnyöket a mögöttes Azure Resource Manager alkalmazkodás és olvashatóság JavaScript Object Notation (JSON) kombinálva. Sablonok használatával:

* Topológiákat és a munkaterhelések következetesen kell telepíteni.
* Erőforráscsoportok használata az alkalmazásban az erőforrások kezelése.
* Szerepköralapú hozzáférés-vezérlést (RBAC) megfelelő hozzáférési jogot a felhasználók, csoportok és a szolgáltatások alkalmazni.
* Címkézési társítások segítségével teheti gördülékennyé feladatokat, mint a számlázási kumulatív.

Ez a cikk részletesen fogyasztás forgatókönyvek, architektúra és a Tervező munkamenetek és az Azure Ügyféltanácsadói csapatának (AzureCAT) ügyfelekkel valós sablon megvalósítások során feltárt megvalósítási minták. Messze academic, ezek a módszerek bizonyítottan 12 a Linux-alapú OSS felső technológiák, például sablonokat fejlesztésének figyelmeztet eljárásokat: Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, alapszintű DataStax vállalati Apache Cassandra, Elasticsearch, Jenkins, MongoDB, PostgreSQL, Redis és Nagios technológiával. 

Ez a cikk osztja meg ezek az eljárások segítséget nyújtanak a globális osztály Azure Resource Manager-sablonok tervezővel bizonyítása.  

A munka az ügyfelek, a vállalatok számára, a rendszer kiegészítők (SI) s és a megosztott fürtkötetek több Resource Manager sablon felhasználásának követelménye azonosította azt. Az alábbiakban gyakori forgatókönyvek és minták magas szintű áttekintését különböző vevő típusokhoz.

## <a name="enterprises-and-system-integrators"></a>A vállalatok és rendszerintegrátorok
Nagy méretű szervezeteknek belül általában látható Resource Manager-sablonok két fogyasztóinak: belső szoftver a fejlesztési csapat és a vállalati informatikai. Azt talált, amely a forgatókönyvek a SIs térkép a forgatókönyvekre azoknak a vállalatoknak, így ugyanazok a feltételek vonatkoznak.

### <a name="internal-software-development-teams"></a>Belső szoftver a fejlesztési csapat
Ha a csoport az üzleti támogató szoftverek házon belül fejlesztett alkalmazásokra, a sablonok helyezhet üzembe gyorsan technológiákat biztosítja a megoldások üzleti-specifikus könnyű megoldást biztosítson. Sablonok használatával gyorsan hozzon létre képzési környezetek, amelyek lehetővé teszik a csoport tagjai ahhoz, hogy a szükséges.

Használhatja a sablont,- vagy kiterjesztése, vagy írja őket az igényeinek megfelelően. A sablonokon belüli címkézést, megadhatja számlázási összefoglaló különböző nézetekkel például team projekt, egyéni vagy oktatási.

Vállalatok gyakran szeretné, hogy a megoldás egységes telepítési sablon létrehozása a szoftver a fejlesztési csapat. A sablon megkötések könnyíti meg úgy, hogy a környezetben lévő egyes elemek rögzítettek, és nem bírálható felül. Például egy bank sablon RBAC tartalmazza, így a programozók nem vizsgálja felül az adatokat küldeni a személyes tárfiók banki megoldás lehet szükség.

### <a name="corporate-it"></a>Vállalati informatikai
Vállalati informatikai szervezetek általában a sablonokkal kézbesítéséhez felhő kapacitása és a felhőben üzemeltetett képességeket.

#### <a name="cloud-capacity"></a>Felhő kapacitása
Gyakori módja a vállalati informatikai csoportok a felhőben kapacitás csoportjai "pólóra méretek", amelyek szabványos ajánlat például kis, közepes méretű és nagy jelenti. A méretű pólóra ajánlatok kombinálhatja a különböző típusú, és ugyanakkor biztosítható a szabványosítás, amely lehetővé teszi egy szint mennyiségek sablonokkal. A sablonok konzisztens módon, amely érvényesíti a vállalati házirendeket és címkézés használatával biztosítja a jóváírások szervezetek fel kapacitást biztosítanak.

Szükség lehet például arra, hogy belül, amely a szoftver a fejlesztési csapat telepítheti a megoldások fejlesztői, tesztelési vagy üzemi környezetben. A környezet egy előre definiált hálózati topológia és a szoftver a fejlesztési csapat nem módosítható, például a a nyilvános internethez és a csomag hozzáférésére vonatkozó szabályok elemeknek van. A környezet különböző hozzáférési jogokkal rendelkező ilyen környezetben a szervezet-specifikus szerepköröket is rendelkezhetnek.

#### <a name="cloud-hosted-capabilities"></a>Felhőben üzemeltetett képességek
Sablonok használatával támogatják a felhőben üzemeltetett lehetőségekről, beleértve az egyes szoftvercsomagok vagy belső üzletágak felkínált összetett ajánlatokat. Példa egy összetett ajánlat lenne elemzés,--szolgáltatás – elemzés, adatmegjelenítési és más technológiák – előre meghatározott hálózati topológia optimalizált, csatlakoztatott konfigurációban kézbesíteni.

Felhőben üzemeltetett képességeket kínál, amelyek azok van építve a felhő kapacitása által létrehozott biztonsági és a szerepkör megfontolások érintett. Ezek a képességek eredeti formájukban, vagy egy felügyelt szolgáltatásként érhető el. Az utóbbi, hozzáférés-korlátozott szerepkörök felügyelet céljából a környezetbe való hozzáférés engedélyezése szükséges.

## <a name="cloud-service-vendors"></a>Cloud service szállítók
Való egyeztetés után több CSV-k, felismertük több megközelítés közül választhat az ügyfelek és a hozzá tartozó követelmény szolgáltatásokat telepíti.

### <a name="csv-hosted-offering"></a>Fürt megosztott kötetei szolgáltatás által üzemeltetett ajánlat
Az ajánlat működteti a saját Azure-előfizetéssel, ha két üzemeltetési szempontok megegyeznek: minden ügyfél esetében a különböző központi telepítési üzembe, vagy a méretezési egység, amelyek az összes ügyfél számára használja a megosztott infrastruktúra telepítése.

* **Különböző központi telepítések minden ügyfél esetében.** Egy ügyfél különböző telepítés a különböző ismert konfigurációk rögzített topológiák igényel. Előfordulhat, hogy különböző virtuális gépek (VM) méretét, a változó számú csomópontok és a társított tárolási különböző mennyiségű történő központi telepítését. A központi telepítések címkézés összesítő számlázási minden ügyfél szolgál. Lehetővé teszi az ügyfeleknek a felhőalapú környezetben aspektusainak elérésére RBAC lehet engedélyezni.
* **Méretezési egységek megosztott több-bérlős környezetekben.** A sablon jelenthet a méretezési egység, több-bérlős környezetekben. Ugyanazon az infrastruktúrán ebben az esetben az összes ügyfél támogatására szolgál. A központi telepítések határoz meg, hogy a kapacitás az üzemeltetett elérhető, például a felhasználók száma és a tranzakciók száma a szintű erőforrások csoportja. A méretezési egységeket vannak növelhető vagy csökkenthető, igény szerint van szüksége.

### <a name="csv-offering-injected-into-customer-subscription"></a>Ügyfél-előfizetést be a nézetmodellbe, CSV ajánlat
Érdemes lehet a szoftver telepítése ebben a végfelhasználók által birtokolt előfizetések. Sablonok segítségével az ügyfél Azure figyelembe különböző központi telepítéséhez.

A központi telepítéseket az RBAC használatát, így frissítése és a felhasználói fiókon belül a központi telepítés kezelése.

### <a name="azure-marketplace"></a>Azure Piactér
A hirdetményt, és a piactéren, például az Azure piactéren keresztül ajánlatok létrehozásában értékesítés képes biztosítani a különböző típusú központi telepítések, az ügyfél Azure-fiók futtató sablonok fejleszthet. A különböző központi telepítéseket általában jelentheti egy Póló mérete (kis, közepes, nagy), a termék/célközönség típusa (közösségi, fejlesztők, vállalati) vagy a szolgáltatás típusa (alapszintű, a magas rendelkezésre állású).  Néhány esetben ezek a típusok engedélyezi, hogy bizonyos attribútumai a központi telepítést végez, például Virtuálisgép-típussá vagy lemezek számát adja meg.

## <a name="oss-projects"></a>OSS-projektek
Belül nyílt forráskódú projekteket, a Resource Manager-sablonok segítségével bevált gyakorlatok a megoldás üzembe helyezéséhez egy közösségi engedélyezése. Sablonok tárolhatja a GitHub-tárházban, így a közösségi felülvizsgálhatja őket az adott idő alatt. Felhasználók a saját Azure-előfizetések sablonok telepítése.

Az alábbi szakaszok ismertetik a dolgot figyelembe kell vennie a megoldás tervezése előtt.

## <a name="identifying-what-is-outside-and-inside-a-vm"></a>Kívül és belül a virtuális gépek azonosítása
A sablon tervezéséhez, akkor célszerű nézze meg a követelmények tekintetében kívül és belül a virtuális gépek (VM):

* Külső azt jelenti, hogy a virtuális gépek és a környezet más erőforrások, mint például a hálózati topológia, címkézéssel, a tanúsítványok vagy kulcsok és a szerepköralapú hozzáférés-vezérlés hivatkozik. Ezeket az erőforrásokat a sablon a részét képezik.
* Belső azt jelenti, hogy a telepített szoftverek és a teljes kívánt állapot konfigurációs. Más módszerek, például Virtuálisgép-bővítmények vagy parancsfájlok, részben vagy egészben szolgálnak. Ezek a mechanizmusok azonosítását és a sablon hajtja végre, de nem található.

Tevékenységek kellene tennie "belül a kezdő" közös például-  

* Telepítése vagy eltávolítása a kiszolgálói szerepkörök és szolgáltatások
* Telepítse és konfigurálja a szoftver a csomópont vagy a fürt szintjén
* Egy webkiszolgálón webhelyek központi telepítése
* Adatbázis-sémák telepítése
* Beállításjegyzék vagy egyéb típusú konfigurációs beállítások kezelése
* Fájlok és könyvtárak kezelése
* Indítás, Leállítás, és a folyamatok és szolgáltatások kezelése
* Helyi csoportok és felhasználói fiókok kezelése
* Telepítheti és kezelheti a csomagokat (.msi, .exe, yum, stb.)
* Környezeti változók kezelése
* Natív parancsfájlt (a Windows PowerShell, bash, stb.)

### <a name="desired-state-configuration-dsc"></a>Kívánt állapot konfigurációs (szolgáltatása DSC)
Szeretné végezni a központi telepítés meghaladja a virtuális gépek belső állapotával kapcsolatos, győződjön meg arról, hogy a központi telepítés nem "sodródni" a konfigurációról, hogy definiálva, és be van jelölve a verziókövetési rendszerrel. Ez a megközelítés biztosítja a fejlesztők számára, vagy műveleti személyzet nem módosításokat alkalmi, amelyek nem vetted, tesztelni, vagy a verziókövetési rendszerrel rögzített környezetbe. A vezérlő fontos, mert a manuális módosítások nem szerepelnek a verziókövetési rendszerrel. Akkor is nem részei a normál telepítése, és hatással lesz a jövőbeni automatizált regisztrációt, a szoftver.

A belső munkatársra túl kívánt állapot konfigurációs az is fontos biztonsági szempontból. A támadók rendszeresen próbál kedvezőtlenül és szoftver rendszerek kihasználására. Ha sikeres, általában fájlok telepítése és ellenkező esetben a sérült biztonságú rendszer állapotának módosítása. Kívánt állapot konfigurációs, segítségével azonosíthatja a kívánt és a tényleges állapot közötti eltérések és egy ismert beállításainak visszaállítása.

Nincsenek a legnépszerűbb mechanizmusok DSC - a PowerShell DSC, Chef vagy Puppet erőforrás-bővítményt. Ezek a bővítmények telepítheti a virtuális gép kezdeti állapotában és is győződjön meg arról, hogy a kívánt állapot megmarad.

## <a name="common-template-scopes"></a>Közös sablon hatókörök
Az a tapasztalat világossá három fő megoldás sablonok hatókörök is láttuk. – A kapacitás, a funkció és a végpont megoldás – ezek három hatókör a következő szakaszok ismertetik.

### <a name="capacity-scope"></a>A kapacitás hatókör
A kapacitás hatókör nyújt több erőforrást, amely előre konfigurált meg megfelelően szabályozások és házirendek szabványos topológiában. A leggyakoribb például egy vállalati informatikai vagy SI forgatókönyvben szabványos fejlesztői környezetben telepít.

### <a name="capability-scope"></a>Képesség hatókör
A funkció hatókör központi telepítését és konfigurálását a topológia egy adott technológia összpontosít. Gyakori forgatókönyvek, például az SQL Server, Cassandra, Hadoop technológiák többek között.

### <a name="end-to-end-solution-scope"></a>Végpontok közötti megoldás hatókör
A végpont megoldás hatókörök megcélzott túl egyetlen funkció, és helyette arra irányul, hogy egy több képességek álló teljes körű megoldást hoz létre.  

A megoldás hatókörű sablon hatóköréhez ügyfélrendszer egy vagy több képesség hatókörbe tartozó sablonokat Megoldásfüggő erőforrásokat, a logikai és a kívánt állapot készletként. A megoldás hatókörű sablon példa: adatok végpontok közötti adatcsatorna megoldás sablonná. A sablon előfordulhat, hogy keverje Megoldásfüggő topológia és állapotának például Kafka, a Storm és a Hadoop több megoldás funkció hatókörbe tartozó sablonokat.

## <a name="choosing-free-form-vs-known-configurations"></a>Szabad formátumú és ismert konfigurációk kiválasztása
Először érdemes a sablont kell rugalmasan fogyasztók a lehető legnagyobb mértékben, de sok szempontok hatással a választott szabadkézi konfigurációk és beállításainak használ-e. Ez a szakasz azonosítja a kulcs felhasználói követelmények és az ebben a dokumentumban megosztott módszerrel alakú technikai szempontokat.

### <a name="free-form-configurations"></a>Szabad formátumú konfigurációk
A felületen szabad formátumú konfigurációk hangkártya ideális. Engedélyezi, hogy válassza ki a virtuális gép típusát, és adjon meg egy tetszőleges számú csomópontot, és a csatlakoztatott lemezekkel azokat a csomópontokat, a – és ehhez a sablonhoz paraméterekként. Ezt a módszert azonban bizonyos forgatókönyvek nem ideális el.

A [virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), a másik Virtuálisgép-típusokon és az elérhető méretek azonosítja, és a tartós számának minden egyes lemezek (2, 4, 8, 16 vagy 32), amely csatolható. Minden csatlakoztatott lemezre 500 IOPS biztosít, és ezeknek a lemezeknek többszörösei lehet közösen egy szorzóval IOPS adott számú lehet. Például 16 lemezeket is vonható készletbe 8000 iops értéket biztosítanak. Az operációs rendszerben, a Microsoft Windows tárolóhelyek vagy a redundáns tömbjének (RAID) olcsó lemezek Linux konfigurációval készletezését történik.

Egy szabad formátumú konfiguráció lehetővé teszi a kijelölt Virtuálisgép-példányok több, különböző virtuális gép meg kell adnia, és azokat a példányokat, a Virtuálisgép-típussá különböző lemezeket és konfigurálása a virtuális gép tartalmát egy vagy több parancsfájlok méretének.

Esetében gyakori, hogy a központi telepítés lehetnek többféle típusú csomópontok, például a fő és adatcsomópontokat, úgy erre a rugalmasságra gyakran esetén minden csomópont-típus van megadva.

Bármely jelentőséggel fürtök üzembe helyezésekor megkezdésekor, megkezdheti a ezek összetett forgatókönyvek esetében használható. Ha egy Hadoop-fürt volt telepítése, például 8 főcsomópont és 200 adatcsomópontokat és készletezett 4 csatolt minden egyes főcsomópont és készletezett 16 csatlakoztatott lemezek adatok csomópontonként kellene lennie 208 virtuális gépek és 3,232 lemezek kezelése.

A tárfiók kérelmek fenti az azonosított 20 000 tranzakciók másodpercenkénti száma korlátozza, így kell nézze meg tárolási fiók particionálás, valamint számítások segítségével határozhatja meg, ez a topológia olyan tárfiókok megfelelő számú lesz szabályozás. A szabad formátumú megközelítés által támogatott kombinációk számos megadott, dinamikus számítások annak meghatározásához, hogy a megfelelő particionálás szükségesek. Az Azure Resource Manager sablon nyelvi jelenleg matematikai funkcióval rendelkezik, ezért el kell végeznie ezeket a számításokat a kódban, a megfelelő adatokkal olyan egyedi, kódolt sablon létrehozása.

A vállalati informatikai és SI forgatókönyvek esetében valaki kell a sablonok karbantartása, és a telepített topológiákat támogatja a egy vagy több szervezet számára. Ez a további terhelés – különböző konfigurációt, és minden ügyfél esetében sablonok – nem kívánatos.

Ezek a sablonok segítségével központi telepítése az Azure-előfizetés az ügyfél környezete, de a vállalati informatikai csapata hozza létre és CSV-k általában telepíteni őket a saját előfizetések vásárlóiknak számlázási jóváírási függvénnyel. Ezekben az esetekben a célja, hogy több ügyfélhez kapacitás telepítése egyes előfizetések között, és láthatóan tartja sűrűn feltöltve az előfizetések előfizetés sprawl minimalizálása érdekében a központi telepítések – Ez azt jelenti, hogy legalább egy előfizetésre kezeléséhez. Valóban dinamikus telepítési méret az ilyen típusú sűrűség elérése igényel a gondos tervezéssel és a további fejlesztési állványok munka a szervezet nevében.

Emellett egy API-híváson keresztül előfizetések nem hozható létre, de ezt manuálisan kell tennie a portálon keresztül. Előfizetések száma növekszik, a létrejövő előfizetés sprawl emberi beavatkozást igényelnek – nem lehet automatizálni. A központi telepítések mérete túl nagy szórása azonos akkor kell előre ellátnia az előfizetések manuálisan ellenőrizze, hogy az előfizetések elérhető számos.

E tényezők, figyelembe véve a valóban szabad formátumú konfigurációs kevesebb mint, először blush tetszetős.

### <a name="known-configurations--the-t-shirt-sizing-approach"></a>Ismert konfigurációk – a pólóra méretezési módszer
Ahelyett, hogy kínálnak a sablont, amely teljes rugalmasságot és számos változata biztosít, mint a felhasználói élmény a megszokott mintát követi, hogy lehetővé teszik a ismert konfigurációk kiválasztása – érvényben, szabványos pólóra például védőfal kis, közepes és nagy méretű. Más pólóra méretű például a termék ajánlatokat, community edition vagy enterprise edition.  Más esetekben lehet munkaterhelés-specifikus konfigurációk egy technológia – például a térkép csökkentése vagy a nem sql.

Sok vállalati informatikai szervezetek, OSS szállítók és SIs elérhetővé tegye az ajánlatok ma ily módon a helyszíni virtualizált környezetekben (vállalatok) vagy szoftver-,--szolgáltatás (SaaS) offerings (CSV-k és OSVs).

Ezt a módszert biztosít az ügyfelek különböző méretű előre konfigurált megfelelő, ismert konfigurációk. Ismert konfigurációk nélkül végfelhasználók kell saját méretezése fürtön határozza meg, figyelembe a platform erőforrás-korlátozások és számításokat végezni az eredményül kapott particionálás storage-fiókok és egyéb erőforrások (mert a fürt méretét és erőforrás korlátozások) azonosításához. Ismert konfigurációk engedélyezi az ügyfeleknek a egyszerűen válassza ki a megfelelő pólóra méretet – Ez azt jelenti, hogy egy adott központi telepítés. Azonkívül, hogy hatékonyabb működését az ügyfél, néhány ismert konfigurációk, ezért könnyebben támogatja, és segít a következők támogatásával sűrűség magasabb szintű.

Egy ismert konfigurációs módszer pólóra méretek összpontosított is eltérő számú méretet egy csomópontja. Például egy kis pólóra mérete 3 – 10 csomópontok között lehet.  A Póló mérete volna kell megtervezni, hogy legfeljebb 10 csomópontok alkalmazásához, és adja meg a fogyasztó, hogy a meghatározott maximális mérete legfeljebb szabad formátumú beállításokat.  

Egy pólóra munkaterhelés típusától függően lehet, hogy további szabad formátumú jellegű telepíthetők, de lesz, hogy a munkaterhelés különböző csomópont méretét és a szoftver konfigurációs van a csomópont a csomópontok száma tekintetében.

Pólóra méretet termékajánlatokat, például a közösségi vagy Enterprise, előfordulhat, hogy is telepíthető, csomópontok maximális száma és a különböző erőforrás esetében általában kötve kapcsolatos vagy a szolgáltatás rendelkezésre állásával között a különböző ajánlatokat.

Az egyedi változatok a JSON-alapú sablonok használatával is lehetővé teszi az ügyfelek. A kiugró meghatározásakor a megfelelő tervezési és fejlesztői támogatási és költségszámítás szempontjai is használhatja.

A sablon fogyasztás forgatókönyvet, és ez a dokumentum elején meghatározott követelmények alapján felismertük sablon felbontás ellen egy mintát.

## <a name="capacity-and-capability-scoped-solution-templates"></a>Kapacitás és a megoldás funkció hatókörű sablonok
Felbontás ellen, hogy támogatja szeretné újrafelhasználni, kiterjeszthetőség, tesztelése és tooling eszköz sablonok fejlesztését moduláris megközelítését ismerteti. Ez a szakasz részletes ez hogyan alkalmazhatók a felbontás ellen megközelítés kapacitás és -készítés hatókörű sablonok.

Ez a megközelítés egy fő sablont paraméterértékek kap egy sablon végfelhasználói, majd hivatkozik, és számos különböző típusú sablonok és a parancsfájlok után alább látható módon. Adjon meg értékeket a kapcsolt sablonok mindkét paraméterek, a statikus változókat és a létrehozott változókat szolgálnak.

![Sablonparaméterek](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Egy fő sablont, majd a kapcsolt sablonok átadott paraméterek**

Az alábbi szakaszok a sablonok és a parancsfájlok, amelyek ugyanazt a sablont a van a kiválasztott típusú összpontosítanak. A szakaszok az állapotadatokat a sablonok között továbbításához megközelítések jelenthet. Minden sablon és a parancsfájl típusát, a kép leírt példák együtt. Tekintse meg a környezetfüggő például "üzembe azt: a minta megvalósítása" a jelen dokumentum későbbi.

### <a name="template-metadata"></a>Sablon metaadatait
Sablon metaadatait (metadata.json fájl) kulcs/érték párok, amelyek ismertetik a JSON-ban, amely az emberek és szoftver-rendszerek által olvasható sablont tartalmaz.

![Sablon metaadatait](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Sablon metaadatait a metadata.json fájl leírása**

Szoftver ügynökök kérje le a metadata.json fájlt, és az adatokat és a hivatkozás közzététele a sablon egy weblap vagy könyvtár. Elemek például *itemDisplayName*, *leírás*, *összefoglaló*, *githubUsername*, és *dateUpdated*.

Egy példa fájl egészében alább láthatók.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### <a name="main-template"></a>Fő sablon
A fő sablon paraméterek kap a felhasználó, ezt az információt használja a komplex objektum változók feltöltéséhez, és végrehajtja a kapcsolt sablonok.

![Fő sablon](./media/best-practices-resource-manager-design-templates/main-template.png)

**A fő sablon paraméterek kap a felhasználó**

Egy megadott paraméter olyan ismert konfigurációs más néven a pólóra méretparaméternek kis, közepes vagy nagy a szabványos értékek miatt. A gyakorlatban ez a paraméter több módon használhatja. További információkért lásd: "Ismert konfigurációs erőforrások sablon" a dokumentum későbbi szakaszában.

Bizonyos erőforrások telepítése egy felhasználó paraméter által megadott ismert konfiguráció függetlenül. Ezeket az erőforrásokat egyetlen megosztott erőforrás-sablon használatával telepített, és más sablonok által megosztott, a megosztott erőforrás sablon első futtatásakor.

Bizonyos erőforrások opcionálisan telepítése a megadott ismert konfiguráció függetlenül.

### <a name="shared-resources-template"></a>Megosztott erőforrások sablon
Ez a sablon minden ismert konfigurációk által közösen használt erőforrások nyújt. A virtuális hálózat, a rendelkezésre állási készletek, valamint a más erőforrások, amelyek szükségesek, függetlenül a központilag telepített ismert konfigurációs sablon tartalmazza.

![Sablonerőforrás](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Megosztott erőforrások sablon**

Erőforrás nevét, például a virtuális hálózat nevét, a fő sablon alapján. Adja meg azokat, hogy a sablon belül változóként, vagy a felhasználó, a szervezete által megkövetelt paraméterként fogadásukra.

### <a name="optional-resources-template"></a>Nem kötelező erőforrások sablon
A választható erőforrások sablonban programozott módon rendszerbe telepítendő erőforrásokat egy paraméter vagy változó értéke alapján.

![Nem kötelező erőforrások](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Nem kötelező erőforrások sablon**

Például az egy nem kötelező erőforrások sablon segítségével konfigurálhatja a jumpbox, amely lehetővé teszi a közvetett telepített környezethez a nyilvános interneten keresztül. Szeretné használni egy paraméter vagy változó adja meg, hogy a jumpbox engedélyezni kell, és a *concat* hozhat létre, mint a cél neve a sablon függvény *jumpbox_enabled.json*. Sablon csatolása a jumpbox telepítéséhez használja az eredményül kapott változó.

A választható erőforrások sablon több helyen is kapcsolhat:

* Minden egyes telepítésre alkalmazható, ha a megosztott erőforrások sablonból egy paraméterekkel hivatkozás létrehozásához.
* Válassza ki az ismert konfigurációk kell, ha alkalmazható – például csak telepíteni, a nagyméretű telepítések – paraméterekkel vagy változó adatvezérelt hivatkozás létrehozása az ismert konfigurációs sablonból.

Nem kötelező-e egy adott erőforráshoz nem vezethetik a sablon fogyasztó hanem a sablon szolgáltató. Például szükség lehet egy adott termék követelmény vagy a termék bővítmény (common a CSV-k) kielégítéséhez, vagy a házirendeknek az érvényesítését (gyakori, hogy a SIs és a nagyvállalati informatikai csoportok). Ezekben az esetekben a változó segítségével adja meg, hogy az erőforrás kell telepíteni.

### <a name="known-configuration-resources-template"></a>Ismert konfigurációs erőforrások sablon
A fő sablonban paraméter számára elérhetővé tehető lehetővé teszi a felhasználó a sablon adja meg a telepíteni kívánt ismert konfigurációját. Gyakran az ismert konfigurációs rögzített konfigurációs méretek védőfal, kis, közepes és nagy számú használja a pólóra mérete megközelítést.

![Ismert konfigurációs erőforrások](./media/best-practices-resource-manager-design-templates/known-config.png)

**Ismert konfigurációs erőforrások sablon**

A pólóra mérete módszer általában arra használják, de a paraméterek minden olyan ismert konfigurációk jelenthet. Például egy vállalati alkalmazás, például a fejlesztői, tesztelési és termék környezeteket készlete is megadhat. Vagy használhat egy felhőalapú szolgáltatást a különböző méretezési egységeket, termékverziók vagy termék konfigurációkat, például a közösségi, Developer és Enterprise.

Csakúgy, mint a megosztott erőforrás sablon változók kerülnek átadásra a ismert konfigurációk sablon alkalmazásból:

* A felhasználó – Ez azt jelenti, hogy a fő sablont küldött paraméterek.
* Egy szervezet – Ez azt jelenti, hogy a változók a fő sablonban, amelyek belső követelmények vagy házirendek.

### <a name="member-resources-template"></a>Tag erőforrások sablon
Egy ismert konfigurációs belül egy vagy több tag csomóponttípusok gyakran érhetők el. Például Hadoop lehetősége főcsomópont és adatcsomópontokat. Ha MongoDB telepíti, akkor adatcsomópontokat és egy soron. Ha alapszintű DataStax telepít, akkor adatok csomópontok és a virtuális gép telepítve OpsCenter és.

![Tagok erőforrások](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Tag erőforrások sablon**

Minden egyes a csomópontok is rendelkezik különböző méretű virtuális gépekhez csatlakoztatott lemezek, telepítése és beállítása a csomópontokat, a virtuális gépeket, a példányok száma és egyéb részletek portbeállításokat parancsfájlok számát. Így az egyes csomóponttípusok lekérdezi a saját tag erőforrás-sablon üzembe helyezése és az infrastruktúra konfigurálása, valamint telepítheti és konfigurálhatja a virtuális Gépen belül szoftver parancsfájlok végrehajtása részleteit tartalmazza.

Virtuális gépek általában parancsfájlok kétféle használt, széles körben újrafelhasználható és egyéni parancsfájlok.

### <a name="widely-reusable-scripts"></a>Széles körben újrafelhasználható parancsfájlok
Széles körben újrafelhasználható parancsfájlok segítségével többféle típusú sablonok. Széles körben újrafelhasználható parancsfájlokat jobb példái egyik RAID áll IOPS nagyobb számú tárolókészlet lemezek és a Linux beállítása. A szoftver telepítését a virtuális gép, függetlenül az ezt a parancsfájlt újbóli bevált gyakorlatát biztosít gyakori forgatókönyvek.

![Újrafelhasználható parancsfájlok](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Tag erőforrásokat sablonok meghívhatja széles körben újrafelhasználható parancsfájlok**

### <a name="custom-scripts"></a>Egyéni parancsfájlok
Sablonok általában egy vagy több olyan parancsfájlok, amelyek telepítése és konfigurálása a virtuális gépeken szoftver hívja. A közös minta látható a nagy topológiákhoz, ahol egy vagy több tag típusa több példánya van telepítve. Minden virtuális gép ezzel párhuzamosan futtatható kezdeményezett egy telepítési parancsfájlt a telepítési parancsprogram, minden virtuális gép (vagy egy megadott tag típusú virtuális gépeinek) üzembe helyezése után is nevezett követ.

![Egyéni parancsfájlok](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Tag erőforrásokat sablonok meghívhatja a parancsfájlokat egy adott célra, például a Virtuálisgép-konfiguráció**

## <a name="capability-scoped-solution-template-example---redis"></a>Képesség hatókörű megoldás sablon példa - Redis
Előfordulhat, hogy működése a megvalósítást megjelenítéséhez vizsgáljuk meg a sablont, amely elősegíti a a telepítését és konfigurálását a Redis szabványos pólóra méretben felépítése gyakorlati példa.  

A központi telepítést a megosztott erőforrásokat (virtuális hálózatot, tárfiókot, rendelkezésre állási készletek) és egy opcionális erőforrás (jumpbox) vannak. Több ismert konfigurációk-ként pólóra méretek (kis, közepes, nagy), de az egyetlen csomópont írja be. Van még két célú-specifikus parancsfájlok (telepítési, konfigurációs).

### <a name="creating-the-template-files"></a>A sablon fájlok létrehozása
Azuredeploy.json fő sablont hozna létre.

Megosztott resources.json nevű megosztott erőforrások sablon létrehozása

Létrehozott egy választható erőforrás-sablont egy jumpbox jumpbox_enabled.json nevű központi telepítésének engedélyezése

A redis csak egyetlen csomópont típusa, használ, így a csomópont-resources.json nevű egyetlen tag erőforrás sablont hoz létre.

A redis gyorsítótárral kívánt minden egyes csomóponton telepíteni, majd állítsa be a fürtöt.  Parancsfájlok használatával megfeleljen a telepítést, és állítsa be, a redis-fürt-install.sh és a redis-fürt-setup.sh rendelkezik.

### <a name="linking-the-templates"></a>A sablonok csatolása
A sablonnal linking, a fő sablon mutató ki a megosztott erőforrások sablont, amely létrehozza a virtuális hálózat.

A logika engedélyezéséhez adja meg, hogy egy jumpbox kell telepíteni, hogy a sablon fogyasztók fő sablonban. Egy *engedélyezett* értékét a *EnableJumpbox* paraméter azt jelzi, hogy az ügyfél egy jumpbox telepíteni szeretné. Ha ez az érték van megadva, a sablon összefűzi *_enabled* a jumpbox funkció alap sablon nevét az utótagnak pedig.

A fő sablont alkalmazza a *nagy* paraméter értékét egy alap tanúsítványsablon-nevet pólóra méretét, és ezután használja az utótagnak pedig ezt az értéket egy sablon társítani kívánt kimenő *technology_on_os_large.json*.

Ezen az ábrán a topológia hasonló lesz.

![Redis sablon](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Egy Redis-sablon sablon struktúra**

### <a name="configuring-state"></a>Állapot beállítása
A fürt csomópontjaihoz az állapot beállítása két lépésből áll, mindkét célú adott parancsfájlok által képviselt.  "redis-fürt-install.sh" Redis telepíti, és "redis-fürt-setup.sh" állítja be a fürtöt.

### <a name="supporting-different-size-deployments"></a>A különböző méretű központi telepítések támogatása
Változók, belül a pólóra mérete sablon központi telepítése a megadott méretű minden típusú csomópontok számát adja meg (*nagy*). Majd központilag telepíti ezt a számot erőforrás hurkok, egyedi nevek erőforrások biztosítása a csomópontnév a numerikus sorozatszáma hozzáfűzésével használó Virtuálisgép-példányokat *copyIndex()*. Az hajtja végre ezeket a lépéseket működés közbeni és a meleg zóna virtuális gépeket, a a pólóra sablon

## <a name="decomposition-and-end-to-end-solution-scoped-templates"></a>Felbontás ellen és végpont megoldás hatókörű sablonok
A végpont megoldás hatókörű megoldássablon arra irányul, hogy egy végpont megoldást hoz létre.  Erre akkor általában több képesség hatókörbe tartozó sablonokat további erőforrások, a logikai és a állapot áll.

Az alábbi ábrán kiemelt, mint ugyanannak a modellnek hatókörű funkció sablonok használt ki van bővítve a végpont megoldás hatókörű sablonok.

A megosztott erőforrások sablon és a választható erőforrásokat sablonok szolgál, ugyanezt a funkciót, mint a kapacitás és funkció sablon megközelítések hatókörű, de a teljes körű megoldás hatóköre.

Teljes körű megoldás hatókörű sablonok is általában rendelkezhetnek pólóra mérete, a konfiguráció erőforrások ismert sablon tükrözi, a megoldás egy adott ismert konfiguráció szükséges.

A konfigurációs erőforrások sablon ismert hivatkozásokat egy vagy több képesség, amely kapcsolódik a teljes körű megoldás a megoldás sablonokat, továbbá a tag erőforrás sablonok, amelyek szükségesek a teljes körű megoldás hatókörét.

Lehet, hogy a megoldás pólóra mérete eltér az egyedi képesség hatókörű sablon, változók belül a konfigurációs erőforrások ismert sablon segítségével hatókörű alárendelt funkció megoldás sablonok központi telepítése a megfelelő Póló mérete a megfelelő értéket adjon meg.

![Végpontok közötti](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**A modell használt kapacitás és -készítés hatókörű sablonok teljes körű megoldás sablon hatókörök könnyen bővíthető megoldás**

## <a name="preparing-templates-for-the-marketplace"></a>A piactér sablonok előkészítése
Az előző módszerrel könnyen alkalmazkodik a vállalatok számára, a SIs és a CSV-k, ahová telepíteni magukat a sablonok vagy engedélyezi az ügyfeleknek a saját telepítendő forgatókönyvek.

Egy másik kívánt forgatókönyv telepít, a sablon a piactéren keresztül.  Ez a felbontás ellen megközelítés működik, valamint a piactér néhány apró eltéréstől.

Ahogy korábban említettük sablonok tesz a piactéren eladásra különböző központi telepítési típusok használhatók. Különböző központi telepítési típusok pólóra méretek (kis, közepes, nagy), a termék/célközönség típusa (közösségi, fejlesztők, vállalati) vagy a szolgáltatás típusa (alapszintű, a magas rendelkezésre állású) lehet.

Alább látható módon a meglévő teljes körű megoldás vagy hatókörű funkció sablonok állítható könnyen be a listában a különböző ismert konfigurációk a piactéren.

A paraméterek a fő sablont először módosultak, hogy távolítsa el a bejövő tshirtSize nevű paramétert.

A különböző központi telepítési típusok konfigurációs erőforrások ismert sablon leképezni, amíg is van szükségük a közös erőforrások és a megosztott erőforrások sablonban és potenciálisan azokat az opcionális erőforrás szolgáltatásokban található konfiguráció.

Ha közzé szeretné tenni a sablont a piactéren, kapcsolatot hoz létre a fő sablont, amely kiváltja a korábban elérhető bejövő paraméterének egy változóhoz, a sablonon belül a beágyazott tshirtSize különböző példányait.

![Piactér](./media/best-practices-resource-manager-design-templates/marketplace.png)

**A megoldás testreszabásához hatókörű a piactér sablonját**

## <a name="next-steps"></a>Következő lépések
* Megosztás állapot-sablonok, lásd: [állapotát az Azure Resource Manager sablonokban megosztása](best-practices-resource-manager-state.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

