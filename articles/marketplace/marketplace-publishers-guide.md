---
title: "Az Azure piactér és AppSource Publisher útmutatója"
description: "Lépésről lépésre ismerteti, és az Azure piactéren új közzétevők ellenőrzőlisták közzététele"
services: Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: 
author: ellacroi
manager: msmbaldwin
editor: 
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/18/2018
ms.author: ellacroi
ms.openlocfilehash: 7faa34b5528c53c06aeda25be17cbc5f3db2acdf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="azure-marketplace-and-appsource-publisher-guide"></a>Az Azure piactér és AppSource Publisher útmutatója

Üdvözli a [Azure piactér](https://azuremarketplace.microsoft.com) és [AppSource](https://appsource.microsoft.com) Publisher útmutató. Ez az útmutató célja segíteni az új és meglévő közzétevők megérteni, hogyan használhatók ki az Azure piactéren és AppSource kirakatokkal, az alkalmazások és szolgáltatások közzétételéhez szükséges lépéseket, és hogyan cége a Microsofttal közösen. Ez az útmutató végén kell többet megtudjon az alábbi témakörök mindegyikének és tudja, hol találhatók további információk:

- Milyen előnyökkel jár a listát a piactér kirakatokkal egyikében
- Mik az Azure piactér és AppSource
- Hogyan használhatók ki a kirakatokkal
- Mely kirakat megfelel az ajánlatok és szolgáltatások 
- Milyen típusú alkalmazások és szolgáltatások ajánlatok közzé kell tenni.
- Mik az egyes közzétételi lehetőségek műszaki és üzleti követelmények
- Előzetes közzététel eszköz ellenőrzőlista
- Hogyan legyen, a közzétevő
- Kínál a WHERE létrehozása és közzététele
- Optimalizálja a listát, és nyissa meg piacra jutási erőforrásokat meghajtó hatás használata
- Súgó és támogatás WHERE kérdésekre az Azure piactérről, AppSource, vagy a közzétételi útmutatóban lépjen kapcsolatba a piactér csapatának  **cloudmarketplace@microsoft.com** . 

## <a name="benefits-of-participating-in-the-marketplace"></a>Részt vesz a piactér előnyei

Az Azure piactér és AppSource a Microsoft közös Ugrás piacra jutási tevékenységek indító pontok és lendítõtömeggel üzleti növekedésének megfelelően. Indítsa el az előléptetés, igény szerinti létrehozásának és az értékesítési és közös használ, a megtekintését a felhő üzleti motor centerpiece lehet. Nincs részt vesz a piactér díja van. Célunk a legjobb megoldás és a szolgáltatások, amely a fiókpartner-ökoszisztéma nyújt, Microsoft-ügyfelek csatlakozhatnak.

Az üzleti nő a piactér lehetőségeinek kihasználásához:

- **Készítése érdeklődők és értékesítési lehetőségek.** Írjon be egy bővített portfóliót megoldások az új piacok a Microsoft cloud platform. Felülértékesítési és kereszt-értékesít piactér ajánlatokat. 
- **Javíthatja az üzleti értékekre, és növelje a meglévő és új ügyfelek üzlet mérete.** Üzlet mérete és a cím ügyfélszolgálatnak problémás pontok nő, amikor a felhő helyezi át a munkaterheléseket. Csökkentse az értékesítési ciklusok, és növelheti üzlet jövedelmezőségre teljes megoldásokat kínál, a cél bizonyos munkaterhelések és iparági forgatókönyvek.
- **Gyakorlatban használható elemzések lekérése.** A sikerességi arány ennél a sikeres. Mélyebb betekintés az a Cloud Partner portálra keresztül a listaelemek teljesítményére. Ismerje meg, mi hajt végre, mi részletes útmutatást hozta létre, és maximalizálhatja a kampány tevékenységeket.

>[!NOTE]
>Alkalmazások, amely az Office insights érik el az a közzétételi folyamat Office-alkalmazásokkal.

## <a name="what-are-azure-marketplace-and-appsource"></a>Mik az Azure piactér és AppSource?

A Microsoft biztosít két különböző piactér kirakatokkal, amelyek lehetővé teszik a partnerek számára, hogy a listában ajánlatok kísérletek engedélyezése, és közvetlenül a Microsoft ügyfelei és az ökoszisztémákhoz transact: [Azure piactér] (https://azuremarketplace.microsoft.com) és [AppSource] (https://appsource.microsoft.com). Ezek kirakatokkal engedélyezése a felhasználók kereséséhez, próbálja meg, és alkalmazásokat vásárolhat és egyre gyorsabban jelennek meg a digitális átalakítása, és közzétevők súgó szolgáltatások nő a vállalatok számára a hozzáférést a Microsoft ügyfelei és az ökoszisztémákhoz partner növelésével.
 
A piactér kirakatokkal célcsoportok és a Microsoft cloud termékek szükséges található ügyfelek igazodik. Minden egyes áruháznak speciális közzétételi beállítások segítségével maximalizálhatja a közzétételi való befektetés az alábbi táblázat foglalja össze:


|          |Azure Piactér |AppSource  |
|---------|---------|---------|
|Célközönség     |Informatikai szakemberek számára, a fejlesztők (specialistája szerepkörei tartalmazzák, DBAs, SecOps, DevOps, stb.)    | Sor az üzleti döntéshozók (a specialistája szerepkör tartalmazza a beszerzést, a gyártási, a számviteli, a stb.)      |
|A beépített vagy bővítése     |Azure         | Azure, a Dynamics 365, az Office 365, a Power BI és a kiemelt alkalmazások       |
|Megoldások és a szolgáltatások típusai     |  Infrastruktúra-megoldások és szolgáltatások   | Befejezett sor üzleti felhőalapú alkalmazások, az Office 365-bővítmények és a szolgáltatások        |
|Közzétételi beállítások     |  Megkereshetnek, tanácsadási szolgáltatásokat kínálnak, próbaverzió, virtuális gép megoldás sablonokat, és a felügyelt alkalmazások       |  Ingyenes próbaverzió, lekérése most tesztelése, lépjen kapcsolatba Me kínált tanácsadás      |
|Alkalmazáson belüli élményt biztosít a felhasználók a hozzáférést az alkalmazások és szolgáltatások alkalmazását környezetében  | Azure-portál és a parancssori felület         | Az Office 365, Dynamics 365 Power BI-ban Office ügyfél alkalmazások       |

## <a name="leveraging-these-storefronts"></a>Ezek kirakatokkal kihasználva

Minden egyes kirakat egyedi ügyfelek igényeinek megfelelően működik, és lehetővé teszi, hogy a célcsoport-kezelési szerepkör lehetővé teszi a megfelelő megoldás vagy az alapján, akik az ügyfél szolgáltatás.

Informatikai szakemberek és a felhő fejlesztők keresztül végezhetnek a **Azure piactér** felderítésére, próbálja meg, és IaaS, SaaS és PaaS megoldások vásárlása:


|Ügyfél szükséges  |Azure Piactér |
|---------|---------|
|**További felhőalapú platform funkcióinak technikai és üzleti igényeinek követelményeket**     |  Biztosít egy egyre bővülő portfóliót kiegészítő alkalmazások és szolgáltatások Azure optimalizálva       |
|**A megfelelő alkalmazás vagy szolgáltatás felderítéséhez kihívást találja**    |  Biztosít egy lehetőségekkel megkereshetők, próbálja meg, és a megoldások és az Azure-szolgáltatások megvásárlása        |
|**A méretezhető mechanizmussal kell külső alkalmazások és szolgáltatások**   | Lehetővé teszi, hogy létrehozását és méretezhető központi telepítésének harmadik féltől származó alkalmazások és szolgáltatások konfigurálása        |
|**Új alkalmazások és szolgáltatások integrálását, és a már meglévő megoldások használatához szükséges**  |   Harmadik féltől származó alkalmazások és szolgáltatások könnyen integrálható a meglévő megoldások Azure      |

Az üzleti felhasználók megszólítása **AppSource** keresése, próbálja meg és üzleti SaaS-alkalmazásokhoz és megvalósítási szolgáltatások meghajtó üzleti eredmények számítanak, és csökkentse idő érték:: 


|Ügyfél szükséges  |AppSource  |
|---------|---------|
|**Az üzleti megoldások, amelyek használhatók a Microsoft-termékek keresése már használata** | Lehetővé teszi az ügyfeleknek külső alkalmazások és szolgáltatások segítségével a Microsoft felhőalapú alkalmazások és technológiákat.       |
|**Könnyedén megtalálhatja a jobb oldalon a megfelelő megoldás, illetve implementációjára szolgáltatás lehetővé teszi.**    |   Biztosít egy lehetőségekkel észlelése próbaidőszak, és alkalmazások és szolgáltatások és bővítmények      |
|**Az üzleti megoldások címzéséhez az adott üzleti kihívást iparág-specifikus sor**   | Cím kapcsolatos követelmények között számos iparágakban segítségével végzett végpontok közötti iparági megoldásokat biztosít     |
|**Alkalmazások termelékenység, hatékonyságát és üzleti elemzések készítése javítása érdekében**    | Alkalmazások biztosít üzleti, beleértve az ügyfélszolgálat, HR, műveletek és sok más        |
| **Alkalmazások egyedi helyzetükhöz érdekében partnert tapasztalt végrehajtása** | Itt katalógusát a szolgáltatások ajánlatok tanácsadás megoldások alapján Dynamics 365, a Power bi-ban, a powerapps segítségével, és 3. fél alkalmazások állnak rendelkezésre AppSource üzleti felhasználók tanácsadás található szolgáltatások tervezési képes biztosítani a kiszámítható eredményekkel |

## <a name="understanding-the-differences-between-storefronts"></a>Kirakatokkal közötti különbségek ismertetése

Egy kirakat kezdődik-e a szolgáltatásokat a célközönség azonosító kiválasztása: Azure piactér igazítását az informatikai szakemberek és fejlesztők igényeit, és AppSource igazodik az üzleti felhasználók igényeinek. A megoldás mindkét célközönség célozza, ha csak egyszer listájába az mindkét kirakatokkal közzé kell.
 
Vegye figyelembe az egyes kirakat további előnyei:

|Storefront juttatás  |Azure Piactér  |AppSource   |
|---------|---------|---------|
|**Számlázási rugalmasságot**    | A virtuális gépek, "Használatalapú" számlázási lehetőségek, használja a Microsofttal kötött nagyvállalati szerződése vagy webes közvetlen értékesítési modellek. Árképzési beállítások is, ahol egy ajánlatot az termékbevételezésekor szabad ingyenes szint előfizetés, valamint egy próbálja azt most előfizetésének promotionally szabad után, amely átalakítja a szolgáltatás fizetős korlátozott időtartamra. "Kapcsolja a saját licenc" aktiválási is közzétevők támogatásához mindkét számlázási lehetőség helyzetekben lehetősége virtuális gépek vannak telepítve az Azure-alkalmazások (például a megoldás sablon vagy a felügyelt alkalmazások) használata, ha az összes Azure-erőforrások kiépítése számlázása közvetlenül az ügyfél számára | AppSource kínál a próbaverziója kiépítését, de jelenleg nem biztosítja a commerce engedélyezett közzétételi beállítás; Ez lehetővé teszi, hogy kihasználja az aktuális rendezés és számlázási infrastruktúra invesztálni és módosítások nélkül        |
|**Lehetővé teszi a többi partner-kapcsolatok**     |Az Azure Piactér jelenleg nem teszi lehetővé a közzétevőt úgy, hogy a szolgáltatás szolgáltató vagy a kézbesítési partnerek csatolása az ajánlat, de ez a funkció ekkor elindul a 2018         |  A független szoftverszállítók rendszerintegrátoroktól és szolgáltatók által felügyelt kapcsolható szabott megvalósítási forgatókönyvek esetén új ügyfelek együttműködési értékesítő támogatása      |
|**Automatizálás**     |    Az Azure Piactér jelenleg nem teszi lehetővé a közzétevőt úgy, hogy egy szolgáltatás szolgáltató vagy a kézbesítési partnerek csatolása vonatkozó ajánlatot     | Használja ki az automatizált Szolgáltatottszoftver-bővítmény szolgáltatáskiépítéssel és Solution Templates segítségével automatizálhatja a Szolgáltatottszoftver-alapú adatok gyűjtése és a központi telepítési forgatókönyvek        |A független szoftverszállítók rendszerintegrátoroktól és szolgáltatók által felügyelt kapcsolható szabott megvalósítási forgatókönyvek esetén új ügyfelek együttműködési értékesítő támogatása
|**Több Felhőtípusok**     |   Közzéteszi a nyilvános felhő- és a helyszíni megoldások Azure vermen keresztül, vagy Azure Government és regionális felhők, beleértve a kínai és Németországban közzététele      |    AppSource jelenleg nem nyújtanak támogatást Azure verem, Azure Government vagy regionális felhők     |
|**Az ügyfél számára a környezet bemutató**     |  A megoldás az Azure portálon élményét környezetfüggő keresés (virtuális gépek és sablonok megoldás) elérhetővé tétele       |  A Microsoft-termékek, például Dynamics 365, a Power BI az Office 365 és az alkalmazáson belüli kezelőfelület segítségével további vásárlók    |

## <a name="select-a-publishing-option"></a>Válassza ki a közzétételi beállítást

Minden egyes kirakat támogatja több közzétételi beállítások és Ajánlattípusaival: lista létrehozása és Transact. Válassza ki az ajánlat típusa, amely a legjobban jelképezi a az alkalmazás és szolgáltatás részletes adatai. Minden közzétételi beállítások rendszerében partnerek vezethet a megosztás eléréséhez. 

Ez a szakasz végigvezeti a rendelkezésre álló lehetőségeket, hogy mindkét kirakatokkal a. 


|**Közzétételi beállítás**  | **Az ajánlattípus** | **Storefront**  |
|---------|---------|---------|
|**List**    |    Megkereshetnek, szolgáltatás tanácsadás     |  Azure Marketplace, AppSource       |
|**Próbaverzió**   |     Ingyenes próbaverzió, SaaS-próbaverzióra, interaktív bemutató, tesztelése    |  Azure Marketplace, AppSource       |
|**Transact**     |   Virtuális gép, sablon megoldás, felügyelt alkalmazás      |    Azure Piactér     |

### <a name="list"></a>LIST

Használjon **forduljon Me** egy próba - vagy tranzakciószintű részvétel esetén nem valósítható meg. Ez a megközelítés előnye, hogy lehetővé teszi, hogy a megoldás a piaci a közzétevők azonnali megkezdéséhez, amely képes elindítani az üzleti lendkerék eligazodást üzletek be kell nurtured érdeklődők fogadása. Azonban a hátránya, hogy az ügyfél engagement korlátozva, szemben a más típusú.

>[!IMPORTANT]
>Ügyfél Me ajánlott listaelem típus, és csak olyan esetekben használható fennáll semmilyen módon nem lehet létrehozni egy próbaverziója. Ügyfél engagement próbaverziójában és Transact kínál. Ha bármilyen típusú próbaverziója, a bevezetési folyamat varázsló végigvezeti a forgatókönyvtől függően ezek a lehetőségek egyikére.

Amikor az ajánlat tevődik össze szolgáltatások (pl., felméréseket, megvalósításokhoz, műhelyek), használja a **Consulting Services tanácsadó szolgálat** típus kínálnak. Ajánlat hatókör, időtartama és ár javítani kell, egyetlen ügyfél számára kell lennie, és helyszíni kell elvégezni.

### <a name="trial"></a>TRIAL

A próbaverzió élményt nyújtó növeli az ügyfeleknek, és ezért egy gazdagabb elérhetővé tegyék a megoldást kínál engagement szintjét. A próbaverzió lehetővé teszi az ügyfelek a megoldás felfedezése megvásárlása előtt. Próbaverzió nyújthassunk hogy magasabb veszélyét annak, hogy az előléptetés a kirakatokkal a, és az ügyfél kapcsolattartás során több és több funkcióval érdeklődők kell látnia.
 
Az összes próbaverzió beállítások vannak telepítve, azokat a próbaverzió környezet és/vagy az Azure-előfizetéssel, nem pedig a felhasználói környezet vagy az Azure-előfizetést. Próbaverzió kell felhasználói vezetett további beszerzésének nélkül és minimális, ha bármely, egy egyszerű befejezéséhez további konfigurációs használati eset. Próbaverzió tartalmaznia kell szabad támogatási legalább a próbaidőszak alatt. Próbaverziós felhasználók nurtured legyen, és figyeli a legjobb eredmények elérése érdekében a szándékos értékelési útvonalon. Közzétevők javasoljuk, hogy a piactér érdeklődők, mind a közzétevő saját alkalmazásbeli az eszközintelligencia segítségével figyelheti és kezelheti a próbaverziós felhasználók.

Nincsenek 4 jellemző próbaverzió forgatókönyvek:


|**Próba beállítás**  |**Főbb előnyök**  |**Válassza a beállítást, ha...**  |
|---------|---------|---------|
|**Ingyenes próbaverzió**    |     Lehetővé teszi, hogy az ügyfél előtt egy automatikus módszerrel átalakítása fizetett vásárolnak, próbálja meg a termék, és lehetővé teszi, hogy az ügyfél és a közös engagement a Microsoft értékesítési csoportok fogalmak igazolása |     A megoldásban ez egy virtuális gép vagy megoldás sablon vagy a megoldás egy SaaS kínál, és hogy az ajánlat egy több-bérlős Szolgáltatottszoftver-termék, akkor lehet futtassa az ügyfél felületet, akinek gyorsan, ha már egyetlen bérlő van, de ad hozzá, az ügyfelek első "Vendég us sszon'|
**Kipróbálása**     |     Lehetővé teszi, hogy az ügyfél előtt vásárolnak, próbálja meg a termék, és ez elősegítheti a megoldás a előre konfigurált telepítő |   A megoldás a virtuális gép, Megoldássablonban vagy egybérlős SaaS-alkalmazás, vagy összetett kiépítését, vagy nem rendelkezik a próbaverzió átalakítása fizetett ajánlat metódus |
|**Interaktív bemutató**    |  Lehetővé teszi az ügyfelek a a terméket telepítő összetettsége nélkül művelet megjelenítéséhez       |    A megoldás, hogy a próbaidőszak a érhető nehezen összetett telepítési van szükség.     |


#### <a name="free-trial"></a>Free Trial (Ingyenes próba)

Használja a **ingyenes próba** Ha a megoldás vagy alkalmazás ajánlatok egy ingyenes-az-próbálja meg, a Szolgáltatottszoftver-alapú próbaverzióra. Ezt a beállítást az érdekelt ügyfelektől, segít az üzleti lendkerék start kiváló minőségű érdeklődők meghajtók. Ingyenes próbaverzió is jelenik meg a korlátozott használat vagy korlátozott időtartamra próba fiókokat, és tartalmaznia kell egy hívás a-műveleti annak érdekében, átalakítás fizetős használja a szoftver.

#### <a name="test-drive"></a>Kipróbálás

Használja a **tesztelése** amikor a megoldást már telepítették keresztül egy vagy több virtuális gépek IaaS vagy SaaS-alkalmazásokhoz. Ennek a megközelítésnek az az előnye, az automatizált üzembe helyezést virtuális berendezés vagy egy partner által szolgáltatott "interaktív bemutató" az ügyfél-értékelési további költségek nélkül megoldás az ügyfélnek a couched teljes megoldás környezet. Az ügyfél nem kell egy meglévő Azure-ügyfél, segítve a jobb minőségű előállításához vezet.

További előnyökkel is jár a **tesztelése**:

- 27 %-a felhasználói keresések piactéren vannak is vizsgálati meghajtók csak megjelenítése ajánlatok felhasználók 
- Teszt meghajtók ajánlatok létrehozása nélkül ajánlatok-nál több 38 % érdeklődők 
- az Azure piactéren új ügyfél tulajdonszerzések 36 % származhat próbát tartott ügyfelek 
- Microsoft mező eladók jobb megértése érdekében a termék közös értékesít erőfeszítéseket engedélyezése

#### <a name="interactive-demo"></a>Interaktív bemutató

Tenni az ügyfelek keresztül elősegítheti a termék egy **interaktív bemutató**. Ez a beállítás előnye, hogy egy próbaverziója biztosíthat összetett megoldások bonyolult telepítése nélkül. Ez a beállítás középpontjába a megoldást tekintse meg az ügyfél biztosít, és lehetővé teszi, hogy a közzétevőt úgy is lehet nurtured való eligazodást üzletek elindítani az üzleti lendkerék érdeklődők kapni. 

### <a name="transact"></a>TRANSACT

Az Azure piactérről, használja a **virtuális gép** amikor a megoldást már telepítették, mint a virtuális készülék azokat az ügyfél előfizetéséhez. Virtuális gépek teljesen commerce keresztül használatalapú vagy BYOL-kompatibilis licencelési modell engedélyezve. A Microsoft commerce-tranzakciót futtat, és az ügyfél nevében a közzétevő váltók stb. Közzétevők élvezheti az ügyfél előnyben részesített fizetési kapcsolatot a Microsofttal, beleértve a nagyvállalati szerződés, ami a. 

>[!NOTE]
>Jelenleg egy nagyvállalati szerződés pénzügyi kötelezettségvállalások is használható a virtuális készülék Azure használati szemben, de nem a közzétevő szoftverek licenc díjak ellen.

Használjon egy **Azure Megoldássablonban** amikor megoldást igényel a virtuális készülék túl további üzembe helyezési és konfigurálási automation. Megoldás sablonok automatizálhatja egy vagy több virtuálisgép-erőforrások kiépítése, és emellett építhető ki hálózati és tárolási erőforrásokat. Megoldás sablonok előnyei automation egyetlen virtuális gépeket, valamint a teljes IaaS-alapú megoldás környezetekben. Megoldás sablonok létrehozásával kapcsolatos további [Itt](https://github.com/MicrosoftDocs/azure-docs).

Használjon egy **Azure felügyelt alkalmazás** való üzembe helyezés esetén egy virtuális gép vagy a teljes IaaS-alapú megoldás egy ügyfél előfizetését, amikor a közzétevőt vagy az ügyfél által a 3. fél, például egy SI vagy MSP által kezelt megoldás. További információ [által felügyelt alkalmazások építése Itt](https://docs.microsoft.com/azure/managed-applications/overview). Gyakran ismételt kérdések listájáért lásd: a [Azure piactéren GYIK](https://azure.microsoft.com/marketplace/faq/).

>[!NOTE]
> Felügyelt alkalmazások központilag telepíthető a piactéren keresztül kell lennie. Ha ügyfél-kommunikáció problémát jelent, vegye figyelembe, hogy meg tudják érheti el az ügyfelek érdekelt Ha átfutási megosztás engedélyezve van.

### <a name="azure-certified"></a>Azure Certified

Az összes virtuális gépet az Azure piactéren közzétett ellenőrzi a **Azure hitelesített** program. A program biztosítja az ügyfelek, hogy a virtuális gép kompatibilis az Azure platformon és piactér modellt kínál, online lemezkép biztonsági megfelelőség lehetővé teszi, beleértve a vírusok és kártevők és lehetővé teszi, hogy javítható ajánlat-szinten badging az előléptetés a Microsoft vállalati felhasználók érvényesített megoldásként.

#### <a name="marketplace-commercial-considerations"></a>Piactér kereskedelmi kapcsolatos szempontok

Nincsenek nem díjak piactér való részvételhez. Nincs nem bevétel megosztás részvételhez piactér közzétételekor a listában, a próbaverzió és a BYOL Transact-beállítások használatával. További részletekért lásd: a [piactér részvételét házirendek](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

#### <a name="pay-as-you-go-and-bring-your-own-license-billing-options"></a>Használatalapú és Bring Your-saját-licencet számlázási lehetőségek

Használatakor a használatalapú fizetés Transact közzétételi beállítást, a használat alapú szoftver-licencelési bevétel megosztott 80 %-os / 20 %, és a Microsoft közötti kulcsattribútumokkal. Egyetlen ajánlat öleli fel a használatalapú fizetés és kapcsolja a saját licenc számlázási modell, és is létezhetnek az ajánlat szintjén, külön SKU. Ezt az ajánlatot a felhő partnerportálon konfigurálható.

Vegye figyelembe az ebben a példában:

Ha engedélyezi a használatalapú fizetés lehetőség:


|A licenc költség   | $1,00 óránként        |
|---------|---------|
|Az Azure felhasználás költsége (D1/1-mag)     | $0.14 óránként  |
|**Ügyfél lesz számlázva, Microsoft által**    | **$1.14 óránként**       |

Ebben a forgatókönyvben a Microsoft számára a közzétett virtuálisgép-lemezkép óránként 1.14 $ váltók stb.


|**Microsoft váltók** |**$1.14 óránként**  |
|---------|---------|
|Microsoft fizet 80 %-át a licenc költség | $0,80 óránként        |
|Microsoft tartja a 20 %-át a licenc költség    | $0,20 óránként        |
|A Microsoft megtartja az Azure-felhasználás költsége     |   $0.14 óránként      |

Ezzel szemben ha engedélyezi a kapcsolja a saját licenc lehetőség:

|A licenc költség     | Licenc díj egyeztetett és közzétevő által kiszámlázott        |
|---------|---------|
|Az Azure felhasználás költsége (D1/1-mag)    | $0.14 óránként         |
|**Ügyfél lesz számlázva, Microsoft által**     | **$0.14 óránként**        |

Ebben a forgatókönyvben a Microsoft számára a közzétett virtuálisgép-lemezkép óránként 0.14 $ váltók stb. 

|**Microsoft váltók**    |   **$0.14 óránként**      |
|---------|---------|
|A Microsoft megtartja az Azure-felhasználás költsége     |    $0.14 óránként     |
|A Microsoft megtartja a licenc költség 0 %     |  0,00 $ óránként       |

### <a name="single-billing-and-payment-methods"></a>Single-számlázási és a fizetési módok

Egy fontos használatával a közzétételi beállítást Transact előnye, hogy a Microsoft is "single-számlázási" licenc költségeit az alapul szolgáló Azure kihasználtsága közvetlenül, az ügyfél egyszerre. Ez a forgatókönyv, Microsoft váltók és gyűjti az Ön nevében, a használatukkal nincs szükség arra is létrehozhat saját beszerzési kapcsolat az ügyféllel. Ez takaríthat meg időt és erőforrásokat az értékesítés, nem gyűjt a számlázási üzenetsorokra összpontosíthat.

### <a name="enterprise-agreement"></a>Nagyvállalati szerződés

Microsoft-ügyfél néha segítségével egy nagyvállalati szerződés (EA) a Microsoft-termékek, köztük a Azure használati után kell fizetnie. A fizetési beállítása, amely a szoftver licenc, és a felhőalapú szolgáltatások legalább három év szervezeteknek készült. Az ügyfelek által a kifizetések helyett egy kezdeti fizetési terjednek. Amikor egy EA ügyfél – használatalapú fizetés Transact listát használ, a közzétevő származó szoftverek licenc költségeit számlázás követi a negyedéves EA keretét számlázási ciklusban.

### <a name="monetary-commitment"></a>Pénzügyi kötelezettségvállalás 

Nagyvállalati ügyfeleink az Azure-ra vonatkozó előzetes pénzügyi kötelezettségvállalással felvehetik az Azure-t a nagyvállalati szerződésükbe. Ez a kötelezettségvállalás az év során úgy használható fel, hogy az ügyfél igénybe veszi az Azure globális adatközpontokon keresztül biztosított sokféle felhőszolgáltatása közül azokat, amelyekre szüksége van.

## <a name="prerequisites-for-marketplace-publishing"></a>Piactér-közzététel előfeltételei

### <a name="for-all-marketplace-publishing-options"></a>A közzétételi beállítások minden piactér


|**Követelmény**  |**Részletek**  |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Részvétel házirendek**    | Tekintse át az Azure piactér részvételét házirendek [itt] (https://azure.microsoft.com/support/legal/marketplace/participation-policies/).       | Lista, próbaverzió Transact        |
|**Integráció a Microsoft**    | Az Azure piactér ajánlatok kell kihasználja kiterjesztése a Microsoft Azure-szolgáltatások, például a számítási, hálózati vagy tárolási, illetve hangolhatók össze a meglévő Azure piactér kategóriát, például adatbázisok, biztonság, hálózat, stb. A teljes listát található [Itt](https://azuremarketplace.microsoft.com/marketplace/apps).        | Lista, próbaverzió Transact        |
|**Célközönség**    | Az Azure piactér ajánlatokat az informatikai szakemberek számára, a felhő fejlesztők és az egyéb technikai felhasználói szerepkörök kell lennie.       |  Lista, próbaverzió Transact 
|**Felügyeleti vezethet**    | Érdeklődők kap a piactérről, engedélyeznie kell a CRM (Marketo, Microsoft Dynamics vagy Salesforce) átfutási adatok fogadására.        |   Lista, próbaverzió Transact      |
|**Adatvédelmi szabályzatát, illetve a használati feltételek**     |   Az adatvédelmi szabályzat egy nyilvános URL-CÍMEN keresztül elérhetőnek kell lennie, és a használati feltételeket kell bemeneti szövegként közzététele során.      |   Lista, próbaverzió Transact      |
|**Támogatás**     |  Az ajánlat tartalmaznia kell egy nyilvánosan elérhető támogatási URL-címet, ahol az ügyfelek talál segítséget. A próbaverzió támogatási meg kell adni további költségek nélkül a próbaidőszak.       |  Próbaverzió Transact       |

### <a name="prerequisites-specific-to-trial-publishing"></a>A próbaverzió közzétételi konkrét Előfeltételek

|**Követelmény**  | **Részletek**  |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Ingyenes próbaverzió lejárta és próbaverziója**     |  Az ügyfél az alkalmazás használhatja az ingyenes korlátozott ideig kell lennie.<br>Ez azt jelenti, hogy az ügyfél nem vesznek részt a terméket, és nem az alapul szolgáló Microsoft-k termék vagy szolgáltatás költségének licencek vagy előfizetések díjai. Mivel minden próbaverzió beállítását a közzétevő Microsoft termék-előfizetéshez vannak telepítve, próbaverzió költség optimalizálása és a felügyeleti kizárólag a közzétevő.<br>Választhat egy ingyenes, interaktív bemutató vagy tesztelése. Beállításoktól függetlenül az ingyenes próbaverzióval kell nyújtania az ügyfél egy közötti időtartamot adja az alkalmazása nem jelent többletköltséget kipróbálásához.<br> Kapcsolatfelvétel cloudmarketplace@microsoft.com próbát létrehozásának megkezdéséhez. Megjegyzés: Az Azure piactér SaaS próba lép engedélyeznie kell a felhasználók számára történő bejelentkezést az Active Directory hitelesítő adatok működik. [Részletek](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences) |   Próbaverzió      | 
| **Könnyen konfigurálható, azonnal használható megoldás**    |  Az alkalmazás könnyű és gyors konfigurálásához és állítson be kell lennie.       |  Próbaverzió       |
|**Rendelkezésre állás/üzemideje**    |    A Szolgáltatottszoftver-alkalmazás vagy a platform legalább 99,9 %-os hasznos üzemidőt kell rendelkeznie.     |    Próbaverzió     |
|**Azure Active Directory**    |    Azure Active Directory összevont egyszeri bejelentkezést engedélyeznie kell az ajánlatot (AAD összevont egyszeri bejelentkezés) hozzájárulásával engedélyezve van.      |  Próbaverzió|

### <a name="prerequisites-specific-to-transact-publishing"></a>A közzététel Transact vonatkozó Előfeltételek


|**Követelmény**  |**Részletek** |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Számlázási és annak mérésére**    |  Támogatnia kell a virtuális gép vagy a saját licenc vagy használatalapú, havi-számlázási.       |    Transact    |
|**Azure-kompatibilis virtuális merevlemez (VHD)**     |   Virtuális gépek kell kialakítani, a [Windows] (https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation) vagy () [Linux]https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)    |   Transact      |

### <a name="prerequisites-specific-to-consulting-services-publishing"></a>Közzététel szolgáltatások tanácsadás vonatkozó Előfeltételek


|**Követelmények** |**Részletek**  |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Szolgáltatások ajánlat jellemzői**     | Az egyeztetett szolgáltatás kézbesített rögzített hatókör, időtartam, rögzített rögzített ár (vagy szabad) kell lennie. engagement, elsősorban értékesítési célú, egyetlen ügyfél korlátozódik, és helyszíni végzett        |    Lista     |
|**Partner tanácsadás követelményei**    |   **AppSource only.**  Az ügyfél Engagement Dynamics 365 [ezüst vagy arany felhő felhasználói kapcsolat felügyeleti kompetencia](https://partner.microsoft.com/en-us/membership/cloud-customer-relationship-management-competency). A pénzügyi és műveletek Enterprise edition Dynamics 365: ezüst vagy arany [vállalati erőforrás-tervezési] (https://partner.microsoft.com/en-us/membership/enterprise-resource-planning-competency) kompetencia, és a záró 12 hónapon keresztül minimális bevétel, 25 Ft a Felhőbeli műveletek. Dynamics 365 pénzügyi, illetve műveletek, Business edition: szolgálhat [felhőalapú szolgáltatások szolgáltató (CSP)](https://partner.microsoft.com/en-us/cloud-solution-provider) vagy [digitális Partner a rekord (DPOR)](https://partner.microsoft.com/en-us/membership/digital-partner-of-record) legalább egy ügyfél. A Power BI: A feltételeknek [megoldás Partner] (file:///C:/Users/ellacroi/Downloads/BI%20Partner%20Program%20Overview%20 & % 20Incentives.pdf). PowerApps: [Partner Showcase] rendelkezik (https://powerapps.microsoft.com/en-us/partner-showcase/) megoldás |    Lista     |

## <a name="using-azure-active-directory-to-enable-trials"></a>Az Azure Active Directory használatával kísérletek engedélyezése
Az Azure Active Directory (AAD) egy identitás felhőszolgáltatás, amely lehetővé teszi, hogy a hitelesítést a Microsoft munkahelyi vagy iskolai fiók az iparági szabványos protokollok segítségével: OAuth és az OpenID Connect. További tudnivalók az AAD [Itt](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features). 

Microsoft összes piactér felhasználóit hitelesíti az aad-ben, ezért amikor a hitelesített felhasználók a próbaverzió listázása a piactéren keresztül kattint, és újra irányítja a rendszer a próbaverzióra környezetben, létesíthet a felhasználó közvetlenül a próbaverzió anélkül, hogy egy további bejelentkezési lépést. A [jogkivonatot, amely az alkalmazás aad megkapja a hitelesítés során] (https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens) értékes felhasználói adatokat, amelyek segítségével felhasználói fiók létrehozása az alkalmazás, amely lehetővé teszi a telepítési élmény automatizálhatja és növeli az elágazás tartalmaz átalakítás. 

Az AAD használata az alkalmazás vagy a próbaidőszak 1 kattintással hitelesítés engedélyezése:

- Leegyszerűsíti a felhasználói élmény a próba a piactér 
- Kezeli a érzetéhez a terméken belüli számára, még ha a felhasználót a rendszer átirányítja piactérről a tartományhoz vagy a környezet létrehozása
- Csökkenti a átirányítási elhagyása valószínűségét, mert nincs olyan további bejelentkezés lépés
- Csökkenti az AAD-felhasználókat nagy sokaságát telepítési korlátok

### <a name="certifying-your-azure-active-directory-integration-for-marketplace"></a>Az Azure Active Directory-integráció piactér igazoló

Több-Bérlős alkalmazásokhoz:

Ha támogatja az AAD ma

- Az alkalmazás regisztrálása az Azure-portálon
- A "Tallózás" próbaverziója megszerezni az AAD szolgáltatás több-bérlős támogatási funkciójának engedélyezése
- [További információk itt](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

Ha még nem ismeri a AAD összevont egyszeri bejelentkezés

- Az alkalmazás regisztrálása az Azure-portálon
- Egyszeri bejelentkezés az aad-ben [OpenID Connect] segítségével fejlesztése (https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) vagy [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code)
- A "Tallózás" próbaverziója megszerezni az AAD szolgáltatás több-bérlős támogatási funkciójának engedélyezése
- [További információk itt](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified)

Single-bérlői alkalmazások:

Az egyetlen bérlői alkalmazások több lehetőség áll rendelkezésre:

- Felhasználók hozzáadása a címtárban, () [Azure B2B] vendég felhasználókhttps://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- A "Me forduljon" via ügyfelek manuális kiépítési próbaverzió
- Fejlesztés egy ügyfél "Tesztelése" /
- Egy több-bérlős bemutató mintaalkalmazás SSO összeállítása

##<a name="publishing-processes-by-product-for-office-dynamics-and-power-bi"></a>Az Office, Dynamics, és a Power BI termék közzétételi folyamatok
Office-, Dynamics, és a Power BI AppSource alkalmazások esetén, többet is megtudhat az adott dokumentációjában ebben a szakaszban a meghatározott követelmények. 


|Product |Közzétételi információkat  |
|---------|---------|
|Office 365     |    Tekintse át a [közzétételi folyamat és irányelveket]( https://docs.microsoft.com/en-us/office/dev/store/submit-to-the-office-store).     |
|Dynamics 365 a pénzügyi és műveletek  |   Enterprise Edition való fejlesztéskor tekintse át a [közzétételi folyamat és irányelveket](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source)      |
|Az ügyfél Engagement Dynamics 365 |Tekintse át a [közzétételi folyamat és irányelveket](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/developer/publish-app-appsource) |
|Power BI   |     Tekintse át a [folyamat és az útmutatást.]( https://docs.microsoft.com/en-us/power-bi/developer/office-store)    |
|Cortana Intelligence     |    További tudnivalók [AppSource a Cortana](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide)     |
|AppSource tanácsadás ajánlatok     |  Tekintse át a [irányelvek és megtudhatja, hogyan nyújt az ajánlatot.]( https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf)    |



## <a name="cloud-partner-portal-pre-publishing-checklist-for-azure-marketplace"></a>Cloud Partner portálra előre közzététele az Azure piactér ellenőrzőlista

A közzétételi folyamat elindítása előtt érdemes egy ajánlatot létrehozásához szükséges összetevők megismerése. A következő összetevők az ajánlat létrehozása közzétételi munkafolyamat az a Cloud Partner portálra elvégzéséhez szükséges. 

### <a name="storefront-details"></a>Storefront részletei


|Szüksége lesz a összetevő közzététele  |Az ajánlattípus  |
|---------|---------|
|**Ajánlat neve (200 karakter) és leírását (2000 karakter)**    |  Összes        |
|**MPN azonosítója, valamint a**   |  Tanácsadási szolgáltatásokat       |
|**Ország vagy régió rendelkezésre állása**   | Összes        |
|**Bevonási időtartama**     |   Tanácsadási szolgáltatásokat      |
|**Alkalmazandó iparágakban, a kategóriák és a keresési kulcsszavakat**     |  Összes       |
|**Vállalati emblémát (48 x 48, 216 x 216)**     |  Tanácsadási szolgáltatásokat       |
|**A termék áttekintő videó (nem kötelező)**  |  Összes       |
|**Képernyőfelvételek (max. 5, 1280 x 720)**   |    Összes     |
|**Marketing dokumentumok (Max3)**    |  Összes       |
|**Cél vezethet**    |   Összes      |

### <a name="contacts"></a>Kapcsolatok


|Szüksége lesz a összetevő közzététele  |Az ajánlattípus  |
|---------|---------|
|**Kapcsolattartási adatok (támogatási, műszaki osztály, kereskedelmi)**    |    Összes     |

### <a name="technical-info"></a>Technikai információ


|Szüksége lesz a összetevő közzététele  |Az ajánlattípus |
|---------|---------|
|**Próba URL-címe**     |  Összes próbaverziós ajánlat típusa       |
|**Támogatott nyelvek**    |   Összes próbaverziós ajánlat típusa      |
|**Alkalmazás verziószáma és a kiadási dátum**    |   Összes próbaverziós ajánlat típusa      |
|**Támogatási URL-címe**    |   Az összes próbaverziós ajánlat típusok, a virtuális gépek      |
|**Használati feltételek használatát és az adatvédelmi szabályzat URL-címe**     |    Összes     |

### <a name="test-drive"></a>Kipróbálás


|Szüksége lesz a összetevő közzététele  |Az ajánlattípus  |
|---------|---------|
|**Leírás és időtartama**     |  Csak kipróbálása       |
|**Felhasználói útmutatója**     |   Csak kipróbálása      |
|**Teszt meghajtó videó (Max 1)**     |  Csak kipróbálása       |
|**Teszt meghajtó ország vagy régió rendelkezésre állása**    |   Csak kipróbálása      |
|**Azure erőforráscsoport-név**   |         |
|**Azure Subscription ID**     |  Csak kipróbálása       |
|**Az Azure AD-bérlő azonosítója**   |    Csak kipróbálása     |
|**Azure AD App ID**  |  Csak kipróbálása       |
|**Az Azure AD alkalmazás-kulcs**     |   Csak kipróbálása      |

### <a name="storefrontmarketplace"></a>Storefront/Marketplace


|Szüksége lesz a összetevő közzététele  |Az ajánlattípus  |
|---------|---------|
|**Cím (maximum 50 karakter)**    |  Transact "virtuális gépek, az Azure Apps (megoldás sablonok és a felügyelt alkalmazások)       |
|**Összefoglalás (Max 200 karakter)**    |  Transact "virtuális gépek, az Azure Apps (megoldás sablonok és a felügyelt alkalmazások)       |
|**Hosszú összefoglalás (maximum 256 karakter)**     |   Transact "virtuális gépek, az Azure Apps (megoldás sablonok és a felügyelt alkalmazások)      |
|**HTML-alapú leírása (char maximális 3000)**    |  Transact "virtuális gépek, az Azure Apps (megoldás sablonok és a felügyelt alkalmazások)       |
|**Vállalati emblémát (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)**    |  Transact "virtuális gépek, az Azure Apps (megoldás sablonok és a felügyelt alkalmazások)       |

### <a name="sku"></a>SKU


|Szüksége lesz a összetevő közzététele  |Az ajánlattípus  |
|---------|---------|
|**Verziószám**     |    Transact "Azure alkalmazásokat (megoldás sablonok és a felügyelt alkalmazások)     |
|**A csomagfájl a sablonfájlokat importálni és createUIDefinitionFile tartalmazó**   |Transact "Azure alkalmazásokat (megoldás sablonok és a felügyelt alkalmazások)         |
|**Operációs rendszer részletei**    |   Transact "virtuális gépek      |
|**Portok és protokollok használata**    |  Transact "virtuális gépek       |
|**Minden virtuális merevlemez használatban verziója és a SAS URL-cím lemez**   |  Transact "virtuális gépek       |

## <a name="become-a-publisher"></a>A Publisher válik

Ebben a szakaszban a lépések azt ismertetik: legyen, a közzétevő, az Azure piactér és AppSource; a Cloud Partner portálra, fog használni, létrehozásához, amely hozzáfér közzététele, és és az ajánlatot. 

### <a name="process-overview"></a>Folyamat áttekintése


|Piactér-regisztrációs lépései  |Time  |Leírás  |
|---------|---------|---------|
|Microsoft azonosító létrehozása     |   15 perc      |   A Microsoft ID, amely azonosítja a Partner partnerek szükséges. A Microsoft ID Cloud Partner portálra eléréséhez használható.       |
|Piactér jelölési képernyő     |  1-3 napos       |  Partnerek kell küldenie a jóváhagyási folyamat indul el, hogy a piactér jelölési formájában. Az űrlap elküldése után a piactér bevezetési csapat tekintse át az alkalmazást, és a kérelem érvényesítése.       |
|Regisztrálja a fejlesztői központ     |    5 – 10 nap     | Alkalmazás regisztrálása a Microsoft Developer Center, szükség a Microsoft annak ellenőrzésére, hogy a Partner áll-e egy érvényes jogalany az ország, amelyen regisztrálva van egy érvényes adó azonosítóval. Fejlesztői központ lehetővé teszi egy regisztrált a Microsoft Developer kell, és adja meg az Azure fejlesztőprogrambeli a hozzáférést a partnert. <br><br>*Megjegyzés: Ha nem végzi el a piactér jelölési képernyő, kérni fog $99 regisztrációs díjat kell fizetnie. Ahhoz, hogy ez a díj az elérhetővé tételt követően, a piactér jelölési űrlap kitöltése és kapni fog egy promóciós kódot e-mailben.*  |
|Jelentkezzen be a Cloud Partner portálra     |  15 perc       |   Ha a Partner megkapja a jóváhagyást a piactér csapat a jelölési jóváhagyják, Partner való hozzáférés a [Cloud Partner portálra](https://cloudpartner.azure.com/) engedélyezve van. Partner kell használni a Microsoft ID a Cloud Partner portálra, a közzétevő profilba jelölési formájában használatát a bejelentkezéshez használt. Miután a fejlesztői központban regisztrált, a Partner kell a fejlesztői központban regisztrált fiókjában társítandó közzététele az Azure piactér Publisher profilt.      |

#### <a name="create-a-microsoft-id"></a>Microsoft azonosító létrehozása

A teljes piactérre közzétételi folyamat egy e-mail címet, amely azonosítja a piactér fiókot fogja használni. Ez az e-mail cím egy Microsoft ID regisztrálni kell, és mindkét fog történni a [Microsoft Developer Center](https://developer.microsoft.com/) és [Cloud Partner portálra](https://cloudpartner.azure.com/). Csak egy Microsoft ID-fiókot az Azure piactér és AppSource ajánlatokról kell lennie, és határozottan ajánlott, hogy nincsenek megosztva, más szolgáltatásokkal vagy kínál.

A kiválasztott e-mail címet lehetőleg kell a vállalati tartományhoz, és az informatikai csapat által vezérelt. Tekintse át a részeket [irányelvek és hogyan Tos](#Guidelines-and-How-Tos) irányelvek beleértve a Microsoft ID piactér fiók kezelése és az útmutató a Microsoft IDs létrehozásához AAD összevont tartományokban irányelvek előzetes létrehozása egy. 

#### <a name="submit-the-marketplace-nomination-form"></a>A piactér jelölési űrlap elküldése
A piactér bevezetési folyamat részeként meg kell küldenie jelölési űrlap, az alkalmazás vagy szolgáltatás ajánlat, a vállalati információk és a szintű támogatást biztosító akkor-nak adatokat küld.  
Az űrlap elküldése után a piactér team tekintse át az alkalmazást, és a kérelem érvényesítése. Ha a kérelem áttekintette van, a rendszer értesíti, szükséges legyen, a Cloud Partner portálra egy jóváhagyott fele végrehajtani a következő lépéssel e-mailben. Küldjön a jelölési a:

Az Azure piactér jelölési: http://aka.ms/listonazuremarketplace   
AppSource jelölési: http://aka.ms/listonappsource

#### <a name="register-in-the-developer-center"></a>Regisztrálja a fejlesztői központban

A [Microsoft Developer Center](https://developer.microsoft.com/) a vállalati információk regisztrálhatók. A bejegyzés a vállalat érvényes képviselője kell lennie, és biztosítania kell az identitásukat érvényesítéséhez személyes adataik. A személy regisztrálása a Microsoft ID, amelyet a vállalat kell használnia, és ugyanazt a fiókot kell használni a [Cloud Partner portálra](https://cloudpartner.azure.com/). 

>[!IMPORTANT]
>Ellenőrizze, hogy ellenőrizze, hogy a vállalat már nincs a Microsoft Developer Center fiók előtt hozzon létre egyet.

A folyamat során rendszer információgyűjtés vállalati cím, banki adatokat, és adó adatok. Ezek az adatok általában megtalálhatók a pénzügyi vagy üzleti kapcsolattartói adatok között. Ezenkívül el kell végeznie a következő közzétevő profil összetevőket a különböző fázisait ajánlat létrehozásának és telepítésének keresztül:


|**A Publisher profil**  |**Profil elejéig**  |**Átmeneti**  |**Lista és a fiók létrehozása**  |**Transact**
|---------|---------|---------|---------|---------|
|**Vállalati eszközregisztrációs**     | **Rendelkeznie kell**        |  **Rendelkeznie kell**       | **Rendelkeznie kell**        |  **Rendelkeznie kell**       |
|**Profil Adószám**   |    Optional     |    Optional     |  Optional       | **Rendelkeznie kell**      |
|**Banki fiók**     |   Optional      |    Optional     |  Optional       |  **Rendelkeznie kell**      |

Tekintse meg a függelék: Ez az eljárás részletes leírását a fejlesztői központban regisztrálásával kapcsolatos utasításokat. 

#### <a name="log-in-to-the-cloud-partner-portal"></a>Jelentkezzen be a Cloud Partner portálra

Miután megkapta a jóváhagyást a piactér csapat, amely a jelölési jóváhagyják, és a regisztrált a [Microsoft Developer Center](https://dev.windows.com), egy fiókot hoz létre, hozzáférhet a [felhő Partner portál](https://cloudpartner.azure.com). Első bejelentkezési hitelesítő adatok fognak szerepelni a jelölési jóváhagyása e-mailben. 

A közzétevő profil eléréséhez a piactér fiókot (Microsoft ID) használja. Egyszer a Cloud Partner portálra, az utolsó lépése, hogy a fejlesztői központban regisztrált fiókjában társítsa a megfelelő piactér Publisher profilhoz közzététele. Ezt megteheti a felhő partnerportálon keresztül a gombra a képernyő alján, a közzétevő profilban.

A Cloud Partner portálra használatával részletes információkért tekintse meg a [további](https://cloudpartner.azure.com/#Learn) belül a portálon, majd kattintson a dokumentáció a menüben. 


## <a name="getting-support"></a>Keresztüli támogatás

Ez az az Azure piactéren a támogatási lehetőségeket listája:

**Az Azure piactér általános kapcsolatos kérdésekben:**
|Támogatási csatornán keresztül |Leírás |
|---------|---------|
|E-mail: cloudmarketplace@microsoft.com     |  A bevezetési támogatást a terjesztési lista. A bevezetési kérésekhez, felderítési munkamenetek és architektúra tervezési munkamenetek (ADS) partnerekkel beállítása használ.        |

**Az Azure piactér támogatási közzététele:**

|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|E-mail: 
azurecertified@microsoft.com |   Támogatja a partnerek Azure piactér alkalmazások közzétételére. Közép-európai időzónában üzleti óra.      |
|E-mail:
AzureMarketOnboard@microsoft.com |   Az Azure piactér megoldás jelölési űrlap és a folyamat támogatja. Közép-európai időzónában üzleti óra.      |
|E-mail: 
Amp-testdrive@microsoft.com |   Bevezetési hozzáférést biztosít a teszt meghajtók. Közép-európai időzónában üzleti óra. | 

**Az Azure piactér portál támogatják:**

|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|E-mailek [támogatása](https://go.microsoft.com/fwlink/?linkid=844975)    |   Piactér-közzétételi Portáljára támogatás. Támogatás 24/7 rendelkezésre.        |

**A technikai támogatási szolgálathoz**


|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|Slackhez: [csatlakozás a Slackhez piactér](https://join.marketplace.azure.com)    |   Slack környezet műszaki kapcsolatos problémákkal rendelkező partnerek támogatásához. Ott 350 + partnerek háttérműveleten dolgozik ebben a környezetben.        |
|MSDN fórumain: [piactér](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=DataMarket)     | A Microsoft Developer Network fórum.         |
|StackOverflow: [Azure](https://stackoverflow.com/questions/tagged/azure)     |    StackOverflow webhely több részből áll, amelyek megoldások és kérdése van minden Azure és a AMP kapcsolatos olyan környezetet biztosítson:<ul><li>StackOverflow: [Azure piactér](https://stackoverflow.com/questions/tagged/azure-marketplace)</li><li>StackOverflow: [az Azure Resource Manager](https://stackoverflow.com/questions/tagged/azure-resource-manager)</li><li>StackOverflow: [Azure virtuális gépeken](https://stackoverflow.com/questions/tagged/azure-virtual-machine)</li></ul> |


**Marketing erőforrások**

|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|E-mail: cosell@microsoft.com    |  Támogatja a bevezetési folyamat és a közös értékesít program kapcsolatos kérdésekre. Közép-európai időzónában alapján.        |
|E-mail: gtm@microsoft.com    |  Nyissa meg piacra jutási előnyei és a program kérdések támogatja. Közép-európai időzónában üzleti óra.        |
|E-mail: CEBrand@Microsoft.com     |  Azure emblémák és branding márka használatával kapcsolatos kérdésekre ad választ.       |

## <a name="guidelines-and-how-tos"></a>Irányelvek és használati útmutatók

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-an-azure-marketplace-account"></a>A Microsoft Id kezelése az Azure piactér-fiók létrehozására vonatkozó irányelvek

A vállalati fiók létrehozásakor követi ezeket az irányelveket, ha egynél több személy kell megnyitni a fiókot a Microsoft-fiókkal bejelentkezik a fiók eléréséhez.

>[!IMPORTANT]
>Lehetővé teszi több felhasználó is elérheti a fejlesztői központban regisztrált fiókjában, javasoljuk, hogy az Azure Active Directoryval egyéni felhasználók számára, ki férhet hozzá a fiókot az egyéni bejelentkezik szerepkörök hozzárendelése az Azure AD hitelesítő adatait. További információért tekintse át a [AAD összevont tartományok segítséget](#guidance-with-aad-federated-domains). A Microsoft-fiók, amelyhez tartozik például a vállalat tartománya, de nem egy személy e-mail címmel történő létrehozásához windowsapps@fabrikam.com.

- A lehető legkevesebb fejlesztők a Microsoft-fiókhoz való hozzáférés korlátozásához.
- A vállalati e-mailek terjesztési lista, amely tartalmazza az összes felhasználó el tudja érni a fejlesztői fiók beállítása, és ez az e-mail cím hozzá biztonsági adataihoz. Ez lehetővé teszi, hogy az alkalmazottak a listán szereplő megkapni a biztonsági kódokat, ha szükséges, és a Microsoft-fiók biztonsági adatainak kezelése. Ha egy terjesztési listát beállítása esetén nem valósítható meg, az egyéni e-mail fiók tulajdonosának kell eléréséhez, és a biztonsági kódot (például ha új biztonsági adatokat a fiók hozzáadódik, vagy ha azt kell elérni egy új eszközről) felkéréskor megosztás elérhető legyen.
- Adja hozzá a munkahelyi telefonszámot, amelyet a bővítmény nem igényel, és kulcs csapattagok számára érhető el.
- Jelentkezzen be a vállalat fejlesztői fiókjába a megbízható eszközök segítségével a fejlesztők általában rendelkeznek. Minden kulcs csoporttag megbízható alkalmazáskatalógusából hozzáféréssel kell rendelkeznie. Ez csökkenti a szükséges biztonsági kódok küldését, ha a fiókjához fér hozzá.
- Ha a fiókhoz való hozzáférés engedélyezése a nem megbízható számítógépről van szüksége, korlátozhatják, hogy legfeljebb öt fejlesztők számára. Ideális esetben a fejlesztők hozzá kell férnie a fiók a számítógépek, amelyek azonos földrajzi és a hálózati helyet.
- Gyakran tekintse át a vállalat biztonsági adatait, [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) való győződjön meg arról, hogy az összes.

A fejlesztői fiókba elsősorban a megbízható számítógépekhez legyenek elérhetők. Ez azért fontos, mert fiókonként hetente létrehozott kódok száma korlátozva van. Azt is lehetővé teszi, hogy a legtöbb zökkenőmentes bejelentkezési élményt.
További információt a további fejlesztői fiók irányelvek és biztonsági [Itt](https://docs.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account).

### <a name="guidance-for-microsoft-ids-in-aad-federated-domain"></a>Útmutató a Microsoft-azonosítót az aad-ben összevont tartományhoz

A vállalati fiók használatával kell összevont [Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/), és hibát adnak vissza, ha a Microsoft ID létrehozásakor a vállalati e-mail címmel. Ha hibaüzenetet kap, először forduljon az informatikai csapat számára, győződjön meg arról, hogy ez a helyzet. Ez egy ismert probléma, és jelenleg is dolgozunk névfeloldása. Kerülő megoldás lehet a következő:

Javasoljuk, hogy az új e-mail cím létrehozása a  **@outlook.com**  tartomány. Kövesse az alábbi lépéseket:

1. Lépjen [ https://signup.live.com/signup ](https://signup.live.com/signup) válassza **egy új e-mail-cím beszerzése**


2. Hozzon létre az új e-mail címet, és adjon meg egy jelszót. Ez létrehoz egy új Microsoft-ID, valamint az e-mail postaládájuk az Outlook.com-os szolgáltatásban. Továbbra is a regisztrációs folyamat során, amíg ez a fiók nincs létrehozva.

>[!IMPORTANT]
>Gondoskodjon arról, hogy az e-mailek azonosító vagy terjesztési lista (függőség eltávolításához egyéni felhasználók számára egy terjesztési listát ajánlott) a fejlesztői központjában a regisztrációhoz használja először regisztrálva a Microsoft-fiók. Ha nem, majd regisztrálja a használatával [hivatkozás](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1).

Emellett **bármely e-mail azonosítója a Microsoft vállalati tartományi** nem használható a fejlesztői központban regisztrációs.

A Microsoft ID ezzel a fiókkal létrehozása után jelentkezzen be a fiókjába [postaláda](https://outlook.live.com/owa/) , és hozzon létre egy e-mail továbbítás szabály, amely minden az e-mailt a postaláda az e-mailhez cím áthelyezése az Azure piactéren kezelheti a tartományban létrehozott fiók. Tekintse meg a [hivatkozás](https://support.office.com/en-us/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed) e-mail továbbítás szabály létrehozásával az Outlook.com-on.

Az utolsó lépés befejezése után kell a Microsoft ID küldött e-mail fiókja a tartományban lévő összes e-mailek/kommunikációját. Meg kell használnia a @outlook.com e-mail cím a Microsoft Developer Center és a Cloud Partner portálra hitelesítéséhez.

### <a name="instructions-on-how-to-register-in-the-development-center"></a>Regisztrálja a fejlesztői központban útmutatást

1. Nyissa meg egy új Internet Explorer InPrivate vagy Chrome Incognito böngészési munkamenet győződjön meg arról, hogy még nem jelentkezett személyes fiókra.
2. Ugrás a [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) el egy értékesítő a fejlesztői központjában. Olvassa el a következő fontos megjegyzés, mielőtt továbblép.

   ![Fejlesztői központ E-mail](./media/marketplace-publishers-guide/registerdevcenteremail.png)

3. A varázsló "Segítsen megvédeni a fiókját", amely fog személyazonosságát keresztül telefonszámát vagy e-mail címét.
4. A "Nyilvántartási-fiók adatainak" területen válassza ki a **ország vagy régió fiók** a legördülő menüből.

   ![Fiók adatai](./media/marketplace-publishers-guide/devcenterregistrationaccountinfo.png)
   
   >[!WARNING]
   >"Értékesít-feladó" országok: a szolgáltatások az Azure piactéren, hogy a regisztrált entitás kell lennie egy jóváhagyott "értékesít-from" országok fenti. Ez a korlátozás kifizetés és adózás okból is. További információkért lásd: a [piactér részvételét házirendek]https://azure.microsoft.com/support/legal/marketplace/participation-policies/.

5. Jelölje ki a "fiók típusa" **vállalati** , majd a **következő** gombra.

   >[!IMPORTANT]
   >Jobb megértése érdekében a fióktípusoknak a használata, és amelyek kiválaszthatja, tekintse meg az oldal [típusok, a helyek és a díjak](https://docs.microsoft.com/en-us/windows/uwp/publish/account-types-locations-and-fees).

6. Adja meg a **Publisher megjelenített név**, általában a vállalat nevét.

   >[!TIP]
   >A fejlesztői központjában megadott publisher megjelenített név nem jelenik meg az Azure piactéren után ajánlatát felsorolt kerül. Azonban ez a regisztrációs folyamat befejezéséhez ki kell tölteni.

7. Adja meg a **kapcsolattartási adatok** fiók ellenőrzése.

   >[!IMPORTANT]
   >A pontos kapcsolattartási adatokat kell megadnia, mert azt fogja a vállalata az ellenőrzési folyamat jóvá kell hagyni a fejlesztői központban.

8. Adja meg a kapcsolattartási adatait a **vállalati jóváhagyó**. Vállalati jóváhagyó az a személy, aki ellenőrizheti, hogy Ön jogosult-e, hozzon létre egy fiókot a fejlesztői központban a szervezet nevében. Kattintson a **következő** áthelyezése a **"Fizetési szakasz"** után.

   ![Fejlesztői központ fizetési](./media/marketplace-publishers-guide/devcenterregistrationpayment.png)

9. Adja meg a fizetési adatok fizetni a fiókját. Ha egy promóciós kódot, amely lefedi a regisztrációs költségét, akkor, amely Itt adhatja meg. Ellenkező esetben adja meg a hitelkártya adatait, vagy a támogatott piacokon PayPal. Ha elkészült, kattintson a **következő** a áthelyezése a **"Képernyő felülvizsgálati."**

   ![Fejlesztői központ fizetési](./media/marketplace-publishers-guide/devcenterregistrationpayment2.png)

10. Tekintse át a fiók adatait, és győződjön meg arról, hogy minden rendben. Majd olvassa el és fogadja el a használati feltételek Microsoft Azure piactér Publisher megállapodás. Jelölje be a jelölőnégyzetet annak jelzésére, hogy elolvasta és elfogadta a ezeket a feltételeket.
11. Kattintson a **Befejezés** a regisztráció megerősítéséhez. Egy megerősítő üzenetet küldünk a e-mail címre.
12. Ha azt tervezi, hogy csak szabad ajánlatok közzététele, kattintson a **Ugrás a [Cloud Partner portálra](https://cloudpartner.azure.com)**.

Ha azt tervezi, hogy közzététele kereskedelmi (TRANSACT) ajánlatok – pl. óránkénti számlázási modellt – kínál a virtuális gép kattintson **módosításához** ahol meg kell adnia az adó és banki adatok a fejlesztői Központban regisztrált fiókjában.

Ha jobban szeret frissítheti később az adó és a bank adatait, majd áthelyezheti a következő szakaszban. 

>[!IMPORTANT]
>Esetén (TRANSACT) kereskedelmi ajánlatokat akkor nem tudnak a ajánlatok leküldéses üzemi környezetben az adó és banki információk befejeződés nélkül.

#### <a name="add-tax-and-banking-information"></a>Adja hozzá az adó és a banki adatokat

Ha közzé szeretné tenni a beszerzési kereskedelmi ajánlatokat, akkor is kell kifizetés hozzáadása és adó adatok, és küldje el a fejlesztői központban érvényesítése. Ha közzé szeretné tenni csak szabad vagy BYOL kínál, akkor nem szükséges hozzáadni ezt az információt. Később adhat hozzá, de csak bizonyos idő a adó információinak ellenőrzésére. Ha ismeri a beszerzési kereskedelmi ajánlatokat tartalmazni fogja, azt javasoljuk, hogy hozzáadja a lehető leghamarabb.

**Banki adatok**
1. Jelentkezzen be Microsoft-fiókjával a Microsoft Developer Centerben.
2.  Kattintson **kifizetés fiók** a bal oldali menü alatti **fizetési mód kiválasztása** kattintson **banki** vagy **PayPal**.

   >    [!IMPORTANT]
   >    Ha kereskedelmi ajánlatokat, amelyek az ügyfelek vásárlása a piactéren, ez az a fiók, ahol ezek a vásárlások a kifizetés fog kapni.

3. Adja meg a fizetési adatait, és kattintson a Mentés gombra, ha elégedett.

   >    [!IMPORTANT]
   >    Kifizetés fiókját módosítani kell, ha ugyanezen lépések fent, az aktuális adatok cseréjének az új információval. A kifizetés fiók módosítása késleltetheti-e a kifizetések által egy fizetési ciklus. Ez a késés az oka, hogy igazolnia kell a fiókját, ugyanúgy, ahogy azt a kifizetés fiók első beállításakor. Akkor lesz továbbra is beolvasása kifizette a teljes összeg után a fiók ellenőrzése megtörtént; fizetési határideje a jelenlegi fizetési ciklus nem kerülnek be a következő egy.

4. Kattintson a **tovább.** 

**Adó információk**

1. Jelentkezzen be a [Microsoft Developer Center](https://dev.windows.com) Microsoft-fiókjával (ha szükséges).
2. Kattintson a **profil adó** a bal oldali menüben.
3. Az a **a adó űrlap** lapon válassza ki az országot vagy régiót állandó rezidens esetében, és válassza ki az országot vagy régiót, ahol tartsa elsődleges lehetőségekkel. Kattintson a **Tovább** gombra.
4. Írja be a adó adatait, és kattintson **tovább.**

   >    [!WARNING]
   >    Nem lesz képes a kereskedelmi kínál az adó és banki vonatkozó információk a Microsoft Developer Center fiókjában befejeződés nélkül éles leküldése.

Ha problémába ütközik fejlesztői központ regisztrációját, jelentkezzen egy támogatási jegy alábbi:

1. A támogatási hivatkozásra https://developer.microsoft.com/windows/support
2. A **Kapcsolatfelvétel** területen kattintson a gombra **incidens nyújt** ahogy az az alábbi képernyőfelvételhez.
3. Válasszon ki a "Súgó a fejlesztői központ" legyen **probléma típusa** és "közzététel és -alkalmazások kezelése a", **kategória**. Ezt követően kattintson a gombra "Kezdő email."

   ![Fejlesztői központ probléma](./media/marketplace-publishers-guide/devcentersubmitincident.png)

4. Fogja ellátni a bejelentkezési oldalt. Használja bármely Microsoft-fiók jelentkezzen be. Ha nincs Microsoft-fiókkal, majd hozzon létre egyet a következő hivatkozás segítségével. 
5. Adja meg a hiba részleteit, és küldje el a jegy parancsával a **Submit** gombra.