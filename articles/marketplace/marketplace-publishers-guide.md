---
title: Az Azure piactér és AppSource publisher útmutató
description: Lépésről lépésre ismerteti, és az Azure piactéren új közzétevők ellenőrzőlisták közzététele
services: Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: msmbaldwin
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/18/2018
ms.author: ellacroi
ms.openlocfilehash: f090bcd56377d167dddab1b8f942d473aecb66a2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-marketplace-and-appsource-publisher-guide"></a>Az Azure piactér és AppSource publisher útmutató

Üdvözli a [Azure piactér](https://azuremarketplace.microsoft.com) és [AppSource](https://appsource.microsoft.com) publisher útmutató. Ez az útmutató célja segíteni a meglévő és új közzétevők használata az Azure piactér és AppSource kirakatokkal, az alkalmazások és szolgáltatások közzététele és hogyan nő a Microsoft számos üzleti ismertetése. 

Ez az útmutató végén kell további információ az egyes ezek a témakörök tudja és tudja, hol találhatók további információk:

- A következő előnyöket Mik a piactér kirakatokkal egyikében listaelem
- A kirakatokkal használata
- Mely kirakat megfelel az ajánlatok és szolgáltatások 
- Milyen típusú alkalmazások és szolgáltatások ajánlatok közzé kell tenni.
- A műszaki és üzleti követelmények Mik az egyes közzétételi beállítások
- Előzetes közzététel eszköz ellenőrzőlista létrehozása
- Hogyan legyen, a közzétevő
- Kínál a WHERE létrehozása és közzététele
- Optimalizálja a listát, és nyissa meg piacra jutási erőforrásokat meghajtó hatás használata
- Honnan szerezhetők be a Súgó és támogatás

Az Azure piactéren, AppSource vagy közzétételi útmutatóban, forduljon a piactér csapatának cloudmarketplace@microsoft.com. 

## <a name="benefits-of-participating-in-the-marketplace"></a>Részt vesz a piactér előnyei

Az Azure piactér és AppSource is a Microsoft közös Ugrás piacra jutási tevékenységek indító pontokat lendítõtömeggel üzleti növekedésének megfelelően. Indítsa el az előléptetés, igény szerinti létrehozásának és az értékesítési és közös használatával végezheti el a megtekintését a felhő üzleti motor centerpiece. Nincs részt vesz a piactér díja van. Célunk a legjobb megoldás és a szolgáltatások, amely a fiókpartner-ökoszisztéma nyújt, Microsoft-ügyfelek csatlakozhatnak.

Az üzleti nő a piactér lehetőségeinek kihasználásához:

- **Érdeklődők és értékesítési lehetőségek készítése**. Írjon be egy bővített portfóliót megoldások az új piacok a Microsoft cloud platform. Felülértékesítési és kereszt-értékesít piactér ajánlatokat. 
- **Javíthatja az üzleti értékekre, és növelje a meglévő és új ügyfelek üzlet mérete**. Üzlet mérete nő, és a címet az ügyfélproblémák, amikor munkaterheléseket telepít át a felhőbe. Csökkentse az értékesítési ciklusok, és növelheti üzlet jövedelmezőségre teljes megoldásokat kínál, a cél bizonyos munkaterhelések és iparági forgatókönyvek.
- **Gyakorlatban használható elemzések lekérése**. A sikerességi arány ennél a sikeres. Mélyebb betekintés az a Cloud Partner portálra keresztül a listaelemek teljesítményére. Ismerje meg, mi hajt végre, mi részletes útmutatást hozta létre, és maximalizálhatja a kampány tevékenységeket.

>[!NOTE]
>Elemzések kiterjesztése Office-alkalmazásokat érik el a közzétételi folyamat kell végrehajtaniuk Office-alkalmazásokhoz.

## <a name="azure-marketplace-and-appsource-storefronts"></a>Az Azure piactér és AppSource kirakatokkal

A Microsoft biztosít két különböző piactér kirakatokkal, amelyek lehetővé teszik a partnerek számára, hogy a listában ajánlatok kísérletek engedélyezése, és közvetlenül a Microsoft ügyfelei és az ökoszisztémákhoz transact: [Azure piactér](https://azuremarketplace.microsoft.com) és [AppSource](https://appsource.microsoft.com). Ezek kirakatokkal engedélyezi az ügyfeleknek megtalálni, próbálja meg, és alkalmazásokat és szolgáltatásokat, amely egyre gyorsabban jelennek meg a digitális átalakítása megvásárlása. Annak elősegítése, nő a vállalatok számára hozzáférést a Microsoft ügyfelei növelésével és partneri ökoszisztéma közzétevők.
 
A piactér kirakatokkal célcsoportok és a Microsoft cloud termékek szükséges található ügyfelek igazodik. Minden egyes áruháznak speciális közzétételi beállítások segítségével maximalizálhatja a közzétételi befektetéseit. Az alábbi táblázat foglalja össze ezeket a beállításokat:


|          |Azure Piactér |AppSource  |
|---------|---------|---------|
|Célközönség     |Informatikai szakemberek és fejlesztők (specialistája szerepkörök következők DBAs, SecOps, DevOps)    | Sor üzleti döntéshozók (specialistája szerepkörök következők beszerzési, gyártási, nyilvántartás)      |
|A beépített vagy bővítése     |Azure         | Azure-ban Dynamics 365, az Office 365, a Power bi-ban, a powerapps segítségével       |
|Megoldás- és szolgáltatástípusok     |  Infrastruktúra-megoldások, szolgáltatások   | Befejeződött az üzleti felhőalapú alkalmazásokhoz, az Office 365-bővítmények, a szolgáltatások        |
|Közzétételi lehetőségek     |  Megkereshetnek, Consulting Services tanácsadó szolgálat kínálnak, próba, virtuális gép, sablon megoldás, felügyelt alkalmazás       |  Ingyenes próbaverzió beszerzése most teszt meghajtó, Me forduljon Consulting Services tanácsadó szolgálat ajánlat      |
|Alkalmazáson belüli tapasztalhat a felhasználók hozzáférésének az alkalmazások és szolgáltatások alkalmazását kontextusában  | Azure-portál és az Azure parancssori felület         | Office 365, Dynamics 365, Power BI, Office kliensalkalmazások       |

## <a name="using-the-storefronts"></a>A kirakatokkal használatával

Minden egyes kirakat egyedi ügyfelek igényeinek megfelelően működik. Ez lehetővé teszi a célcsoport-kezelési szerepkör, így a megfelelő megoldás vagy az alapján, akik az ügyfél szolgáltatás kínálhat.

Az informatikusok és a felhő fejlesztők megkereshetők, próbálja meg, és IaaS, SaaS és PaaS megoldások vásárlása az Azure piactéren keresztül megszólítása:


|Ügyfél szükséges  |Azure Piactér |
|---------|---------|
|**További felhőalapú platform funkcióinak technikai és üzleti igényeinek követelményeket**     |  Biztosít egy egyre bővülő portfóliót kiegészítő alkalmazások és szolgáltatások Azure optimalizálva       |
|**A megfelelő alkalmazás vagy szolgáltatás felderítéséhez kihívást találja**    |  Biztosít egy lehetőségekkel megkereshetők, próbálja meg, és a megoldások és az Azure-szolgáltatások megvásárlása        |
|**A méretezhető mechanizmussal kell külső alkalmazások és szolgáltatások**   | Lehetővé teszi, hogy létrehozását és méretezhető központi telepítésének harmadik féltől származó alkalmazások és szolgáltatások konfigurálása        |
|**Új alkalmazások és szolgáltatások integrálását, és a már meglévő megoldások használatához szükséges**  |   Harmadik féltől származó alkalmazások és szolgáltatások könnyen integrálható a meglévő megoldások Azure      |

Meghajtó üzleti eredmények számítanak, és csökkentse idő érték AppSource megtalálni, próbálja meg, és üzleti SaaS-alkalmazásokhoz és megvalósítási szolgáltatásokat az üzleti felhasználók megszólítása: 


|Ügyfél szükséges  |AppSource  |
|---------|---------|
|**Üzleti megoldások, amelyek Microsoft-termékekkel működnek már használata** | Lehetővé teszi az ügyfelek külső alkalmazások és szolgáltatások segítségével a Microsoft felhőalapú alkalmazások és technológiák       |
|**Könnyedén megtalálhatja a jobb megoldás, illetve implementációjára szolgáltatás lehetővé teszi.**    |   Biztosít egy lehetőségekkel felderítése, próbálja meg, és alkalmazások és szolgáltatások és bővítmények      |
|**Az adott üzleti kihívást megoldására iparág-specifikus üzleti megoldások**   | Cím kapcsolatos követelmények között számos iparágakban segítségével végzett végpontok közötti iparági megoldásokat biztosít     |
|**Alkalmazások termelékenység, hatékonyságát és üzleti elemzések készítése javítása érdekében**    | Alkalmazások biztosít üzletágak, beleértve az ügyfélszolgálat, HR, műveletek és sok más        |
| **Alkalmazások egyedi helyzetükhöz érdekében partnert tapasztalt végrehajtása** | Dynamics 365, a Power BI, PowerApps és külső alkalmazások kiszámítható eredményekkel fájlmegosztásba üzleti felhasználók alapuló megoldásokat Consulting Services tanácsadó szolgálat ajánlatok katalógusba |

## <a name="understanding-the-differences-between-storefronts"></a>Kirakatokkal közötti különbségek ismertetése

Válassza a kirakat kezdődik, az előfizetéshez a célközönség azonosító. Az Azure piactéren az informatikai szakemberek és fejlesztők igazodik. AppSource igazodik az üzleti felhasználók igényeinek. Ha a megoldás mindkét célközönség célozza, akkor mindkét kirakatokkal lista csak egyszer közzé kell.
 
Vegye figyelembe az egyes kirakat további előnyei:

|Storefront juttatás  |Azure Piactér  |AppSource   |
|---------|---------|---------|
|**Számlázási rugalmasságot**    | A virtuális gépek használatalapú számlázási lehetőségek használja a Microsoft nagyvállalati szerződés (EA) vagy a webes közvetlen értékesítési modellek. Árképzési beállítások is, ahol egy ajánlatot az termékbevételezésekor szabad ingyenes szint előfizetés. Is próbálja azt most előfizetés, amely promotionally szabad korlátozott időtartamra, amely után a fizetős verzióra alakítja át. A beállítás a közzétevők támogató is betöltheti az aktiválás a saját licenc. <br><br>Mindkét számlázási lehetőség forgatókönyvekben, ahol virtuális gépek vannak telepítve (például egy megoldássablonban vagy felügyelt alkalmazást), az Azure apps via kiépített Azure-erőforrások számlázása közvetlenül az ügyfél számára. | AppSource tesz lehetővé a próbaverziója kiépítését, de jelenleg biztosít egy kereskedelmi engedélyezett közzétételi beállítást. Az aktuális rendezés és számlázási infrastruktúra invesztálni és módosítások nélkül használható.        |
|**Kapcsolatok más partnerekkel folytatott együttműködés elősegítése**     |Az Azure piactéren jelenleg nem teszi lehetővé a közzétevőt úgy, hogy a szolgáltatás szolgáltató vagy a kézbesítési partnerek csatolása az ajánlat, de ez a funkció 2018 elindul.         |  Független szoftvergyártók rendszerintegrátoroktól és felügyelt szolgáltatók adott megvalósítása forgatókönyvek lehet társítani. Ez a lehetőség támogatja az új ügyfelek együttműködési értékesítő.      |
|**Automatizálás**     |    Az Azure piactéren jelenleg nem teszi lehetővé a közzétevőt úgy, hogy egy szolgáltatás szolgáltató vagy a kézbesítési partnerek csatolása ajánlatot.     | A beépülő modul szolgáltatáskiépítéssel automatizált SaaS előnyeit. Megoldás sablonok használatával automatizálhatja a Szolgáltatottszoftver-alapú adatok gyűjtése és a központi telepítési forgatókönyvek.        |Független szoftvergyártók rendszerintegrátoroktól és felügyelt szolgáltatók kapcsolható szabott megvalósítási forgatókönyvek esetén támogató együttműködési kínál az új ügyfél számára.
|**Több felhőtípusok**     |   Közzéteszi a nyilvános felhő- és a helyszíni megoldások Azure vermen keresztül, vagy Azure Government és regionális felhők, beleértve a kínai és Németországban közzétételére.      |    AppSource jelenleg nem nyújtanak Azure verem, Azure Government vagy regionális felhők támogatást.     |
|**Az ügyfél számára a környezet bemutató**     |  Elérhetővé teszi a megoldás az Azure portálon környezetfüggő kereséshez (virtuális gépek és sablonok megoldás) élményt nyújt.       |  A Microsoft-termékek, például Dynamics 365, a Power bi-ban, és az Office 365 az alkalmazáson belüli kezelőfelület segítségével további ügyfelek elérni.    |

## <a name="publishing-options"></a>Közzétételi lehetőségek

Minden egyes kirakat több közzétételi beállítások és ajánlat típusokat támogatja. Válassza ki az ajánlat típusa, amely a legjobban jelképezi a az alkalmazás és szolgáltatás részletes adatai. Minden közzétételi beállítások rendszerében partnerek vezethet a megosztás eléréséhez. 

|**Közzétételi beállítás**  | **Az ajánlattípus** | **Storefront**  |
|---------|---------|---------|
|**List**    |    Megkereshetnek, tanácsadás     |  Azure Marketplace, AppSource       |
|**Próbaverzió**   |     Ingyenes próbaverzió, SaaS-próbaverzióra, interaktív bemutató, kipróbálása    |  Azure Marketplace, AppSource       |
|**Tranzakció**     |   Virtuális gép, sablon megoldás, felügyelt alkalmazás      |    Azure Piactér     |

### <a name="list"></a>Lista

Használjon forduljon kérek a próba- vagy tranzakció-szintjén részvétel esetén nem valósítható meg. Ennek a megközelítésnek az az előnye, azonnali megkezdéséhez fogadására, amely képes elindítani az üzleti lendkerék eligazodást üzletek be kell nurtured érdeklődők piacra jutási megoldással közzétevők hasonlít. Azonban a hátránya, hogy az ügyfél engagement korlátozva, szemben a más típusú.

>[!IMPORTANT]
>Ügyfél engagement próbaverziójában és Transact kínál. Me forduljon érték megkapja az érdeklődési, tehát ha úgy dönt, hogy a lista tartalmazza az ilyen típusú, győződjön meg arról, hogy a vezető cél van beállítva, és készen áll a átfutási maximalizálása. 

Amikor az ajánlat elsősorban szakmai-szolgáltatásokból állnak (például felméréseket, megvalósításokhoz, műhelyek), használja a a Consulting Services tanácsadó szolgálat fel típusa. Ajánlat hatókör, időtartama és ár javítani kell, egyetlen ügyfél számára kell lennie, és a helyen kell elvégezni.

### <a name="trial"></a>Próbaverzió

Biztosít egy próbaverziója növeli az ügyfeleknek, és ezért egy gazdagabb elérhetővé tegyék a megoldást kínál engagement szintjét. A próbaverzió lehetővé teszi az ügyfelek a megoldás felfedezése megvásárlása előtt. Próbaverziója hogy magasabb veszélyét annak, hogy az előléptetés a kirakatokkal a, és az ügyfél kapcsolattartás során több és több funkcióval érdeklődők kell látnia.
 
Az összes próbaverzió beállítások vannak telepítve, a kísérleti környezetben és/vagy az Azure-előfizetéssel, nem pedig a felhasználói környezetben vagy az Azure-előfizetés. Próbaverzió kell lennie az ügyfél következtében további beszerzésének nélkül és minimális, ha bármely, egy egyszerű befejezéséhez további konfigurációs használati eset. Próbaverzió tartalmaznia kell szabad támogatási legalább a próbaidőszak alatt. Próbaverziós felhasználók nurtured legyen, és figyeli a legjobb eredmények elérése érdekében a szándékos értékelési útvonalon. Közzétevők javasoljuk, hogy a piactér érdeklődők, mind a közzétevő saját alkalmazásbeli az eszközintelligencia segítségével figyelheti és kezelheti a próbaverziós felhasználók.

Három jellemző próba forgatókönyvek van:


|**Próba beállítás**  |**Főbb előnyök**  |**Válassza ezt a beállítást, ha...**  |
|---------|---------|---------|
|**Ingyenes próbaverzió**    |     Lehetővé teszi, hogy az ügyfél a termék kipróbálásához vásárolnak egy automatikus módszerrel átalakítása fizetett használata előtt. Emellett lehetővé teszi az igazolást, ügyfél és a Microsoft értékesítési csoportok közös engagement fogalom. |     A megoldás egy virtuális géphez vagy sablonhoz megoldás.<br><br> A megoldás egy SaaS kínál, és egy több-bérlős Szolgáltatottszoftver-termék ajánlatot tesz. <br><br>A számítógép első indításakor az ügyfél megszerezni, akinek gyorsan rendelkezik. <br><br>Egyetlen bérlővel rendelkezik, de vendégként ügyfelek ad hozzá.|
**Kipróbálása**     |     Lehetővé teszi, hogy az ügyfél előtt vásárolnak, próbálja meg a termék. Egy előre konfigurált telepítő is ez elősegítheti a megoldás. |   A megoldás egy virtuális gép, megoldássablonban vagy egy bérlői SaaS-alkalmazás, vagy összetett kiépítését. <br><br>A próbaverzió átalakítása fizetett ajánlat metódus nem rendelkezik. |
|**Interaktív bemutató**    |  Lehetővé teszi az ügyfelek a a terméket telepítő összetettsége nélkül művelet megjelenítéséhez.       |    A megoldás, amely nehéz elérni a próbaidőszak lenne összetett telepítési igényel.     |


#### <a name="free-trial"></a>Ingyenes próbalehetőség

Egy ingyenes próbaverzióra használja, ha a megoldáshoz, vagy az alkalmazás által a ingyenes-az-próbálja meg, a Szolgáltatottszoftver-alapú próbaverziójának. Ez a beállítás kiváló minőségű érdeklődők meghajtók érdekelt ügyfél segítségével az üzleti lendkerék indíthat el. Ingyenes próbaverzió korlátozott használatú vagy korlátozott időtartamra próba fiókok jelenítheti meg. Tartalmazniuk kell a fizetős használja a szoftver átalakítás felgyorsítása művelet hívása.

#### <a name="test-drive"></a>Próbaüzem

Teszt meghajtót használ, a megoldás segítségével egy vagy több virtuális gépek az infrastruktúra-szolgáltatási vagy SaaS-alkalmazásokkal való telepítésekor. Ennek a megközelítésnek az az előnye, az automatizált üzembe helyezést virtuális berendezés vagy egy partner által szolgáltatott "interaktív bemutató" az ügyfél-értékelési további költségek nélkül megoldás az ügyfélnek a couched teljes megoldás környezet. Az ügyfél nem kell egy meglévő Azure ügyfél magasabb színvonalú érdeklődők kialakításához.

Nincsenek további előnyökkel is jár próbát:

- 27 %-a felhasználói kereséseket a piactéren csak megjelenítése ajánlatok teszt meghajtók felhasználók vannak is. 
- Teszt meghajtók ajánlatok készítése 38 % további érdeklődők mint ajánlatok nélkül. 
- Új ügyfél kérése a piactéren 36 %-át ügyfeleket, próbát tartott határozza meg. 
- Teszt meghajtók engedélyezése a Microsoft mező eladók jobb megértése érdekében a termék közös értékesít erőfeszítéseket.

#### <a name="interactive-demo"></a>Interaktív bemutató

Az ügyfelek elősegítheti a termék keresztül igénybe az interaktív bemutató használatával. Ez a beállítás előnye, hogy egy próbaverziója biztosíthat összetett megoldások bonyolult telepítése nélkül. A beállítás hatására az ügyfelek középpontjába a megoldást tekintse meg. És elérhetővé teszi a kiadóinak érdeklődőket, is lehet nurtured való eligazodást üzletek elindítani az üzleti lendkerék kapni. 

### <a name="transaction"></a>Tranzakció

Az Azure piactéren használja egy *virtuális gép* amikor a megoldást már telepítették, mint egy virtuális készülékre az ügyfél előfizetéséhez. Virtuális gépek teljesen commerce keresztül használatalapú vagy BYOL-kompatibilis licencelési modell engedélyezve. A Microsoft commerce-tranzakciót futtat, és az ügyfél nevében a közzétevő váltók stb. A közzétevő lekérdezi az előnye, hogy az ügyfél előnyben részesített fizetési kapcsolatot a Microsofttal, beleértve a nagyvállalati szerződés kihasználva. 

>[!NOTE]
>Jelenleg egy nagyvállalati szerződés pénzügyi kötelezettségvállalások is használható a virtuális készülék Azure használati szemben, de nem a közzétevő szoftverek licenc díjak ellen.

Használjon egy *Azure megoldássablonban* amikor megoldást igényel a virtuális készülék túl további üzembe helyezési és konfigurálási automation. Megoldás sablonok automatizálhatja egy vagy több virtuálisgép-erőforrások kiépítése, és a hálózati és tárolási erőforrásokat oszthat. Megoldás sablonok automation előnyt biztosíthatnak, egyetlen virtuális gépek és a teljes IaaS-alapú megoldás környezetekben. Megoldás sablonok létrehozásával kapcsolatos további [GitHub](https://github.com/MicrosoftDocs/azure-docs).

Használjon egy *Azure által kezelt alkalmazás* amikor egy ügyfél előfizetését – a virtuális gép vagy a teljes IaaS-alapú megoldások telepít, és a közzétevőt vagy az ügyfél szeretne (például egy SI harmadik fél által kezelt megoldás vagy MSP). Felügyelt alkalmazások létrehozásával kapcsolatos további [Azure által felügyelt alkalmazások – áttekintés](https://docs.microsoft.com/azure/managed-applications/overview). A gyakran ismételt kérdések felsorolását, [piactéren GYIK](https://azure.microsoft.com/marketplace/faq/).

>[!NOTE]
> Felügyelt alkalmazások központilag telepíthető a piactéren keresztül kell lennie. Ha ügyfél-kommunikáció problémát jelent, vegye figyelembe, hogy érhető el érdekelt ügyfelek Ha átfutási megosztás engedélyezve van.

### <a name="azure-certified-program"></a>Az Azure Certified program

Az Azure piactéren közzétett összes virtuális gépet az Azure hitelesített program ellenőrzi. A program:

- Biztosítja a felhasználók, hogy a virtuális gép kompatibilis az Azure platformon és a piactér modellt kínál.
- Online lemezkép biztonsági megfelelőség szempontjából, beleértve a vírusok és kártevő teszteket.
- Lehetővé teszi, hogy az ajánlat szintjén javítása érdekében érvényesített megoldásként a Microsoft vállalati felhasználók előléptetés badging.

#### <a name="marketplace-commercial-considerations"></a>Piactér kereskedelmi kapcsolatos szempontok

Nincs részt vesz a piactér díja van. Nincs bevétel megosztást a piactér részt vesz a listában, a próbaverzió és a BYOL tranzakcióbeállításokat közzétételekor folyamatban van. További információkért lásd: a [piactér részvételét házirendek](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

#### <a name="pay-as-you-go-and-bring-your-own-license-billing-options"></a>Használatalapú fizetés, és a saját licenc számlázási beállítások

Használatalapú fizetés tranzakció közzétételi beállításként használatakor a használat alapú szoftver licencelési bevétel rendre megosztott 80/20 %, és a Microsoft, között. Egyetlen ajánlat, használatalapú fizetés, mind a saját licenc számlázási modellek öleli fel, és az ajánlat szintjén, külön termékváltozatok létezhet. A felhő partnerportálon az ajánlatához konfigurálhatja.

Tekintse meg a következő példát.

Ha engedélyezi a használatalapú fizetés lehetőség:


|A licenc költsége   | $1,00 óránként        |
|---------|---------|
|Az Azure felhasználás költsége (D1/1-mag)     | $0.14 óránként  |
|**Ügyfél lesz számlázva, Microsoft által**    | **$1.14 óránként**       |

Ebben a forgatókönyvben a Microsoft $1.14 a közzétett virtuálisgép-lemezkép használata óránkénti váltók stb.


|**Microsoft váltók** |**$1.14 óránként**  |
|---------|---------|
|Microsoft fizet 80 %-át a licenc költség | $0,80 óránként        |
|Microsoft tartja a 20 %-át a licenc költség    | $0,20 óránként        |
|A Microsoft Azure használati költségek tartja     |   $0.14 óránként      |

Ezzel szemben ha engedélyezi a kapcsolja a saját licenc lehetőség:

|A licenc költsége     | Licenc díj egyeztetett és közzétevő által kiszámlázott        |
|---------|---------|
|Az Azure felhasználás költsége (D1/1-mag)    | $0.14 óránként         |
|**Ügyfél lesz számlázva, Microsoft által**     | **$0.14 óránként**        |

Ebben a forgatókönyvben a Microsoft $0.14 a közzétett virtuálisgép-lemezkép használata óránkénti váltók stb. 

|**Microsoft váltók**    |   **$0.14 óránként**      |
|---------|---------|
|A Microsoft Azure használati költségek tartja     |    $0.14 óránként     |
|A Microsoft megtartja a licenc költség 0 %     |  0,00 $ óránként       |

### <a name="single-billing-and-payment-methods"></a>Single-számlázási és a fizetési módszerek

Egy fontos a tranzakció-közzétételi beállítás segítségével előnye, hogy a Microsoft is egyetlen-számlázási licenc költségeit az alapul szolgáló Azure kihasználtsága közvetlenül, az ügyfél egyszerre. Ez a forgatókönyv, Microsoft váltók és gyűjti az Ön nevében, a használatukkal nincs szükség arra is létrehozhat saját beszerzési kapcsolat az ügyféllel. Ez takaríthat meg időt és erőforrásokat az értékesítés, nem gyűjt a számlázási üzenetsorokra összpontosíthat.

### <a name="enterprise-agreement"></a>Nagyvállalati szerződés

Microsoft-ügyfél néha egy nagyvállalati szerződés használnak a Microsoft-termékek, köztük a Azure használati után kell fizetnie. A fizetési beállítása, amely a szoftver licenc, és a felhőalapú szolgáltatások legalább három év szervezeteknek készült. Az ügyfelek által a kifizetések helyett egy kezdeti kifizetés terjednek. Amikor az ügyfél egy EA listázása – használatalapú fizetés tranzakció használ, a közzétevő származó szoftverek licenc költségeit számlázás követi a negyedéves EA túlhasználati számlázási ciklus.

### <a name="monetary-commitment"></a>Pénzügyi kötelezettségvállalást a 

Nagyvállalati ügyfeleink az Azure-ra vonatkozó előzetes pénzügyi kötelezettségvállalással felvehetik az Azure-t a nagyvállalati szerződésükbe. Hogy előre fel az év folyamán a globális adatközpontjai ajánlatokról Azure-felhőszolgáltatás számos bármely kombinációjával.

## <a name="prerequisites-for-marketplace-publishing"></a>Piactér-közzététel előfeltételei

### <a name="prerequisites-for-all-marketplace-publishing-options"></a>Minden piactér közzétételi beállítások használatának előfeltételei


|**Követelmény**  |**Részletek**  |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Részvétel házirendek**    | Tekintse át az Azure piactér [részvételét házirendek](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).       | Lista, próba, tranzakció        |
|**Integráció a Microsoft**    | Azure piactér ajánlatok kell használni, vagy számítási, hálózati vagy tárolási például a Microsoft Azure szolgáltatástípusok kiterjesztése. Például adatbázisok, a biztonság és a hálózat meglévő Azure piactér kategória kell rendezve. Tekintse meg a [teljes lista](https://azuremarketplace.microsoft.com/marketplace/apps).        | Lista, próba, tranzakció        |
|**Célközönség**    | Az Azure piactér ajánlatokat az informatikai szakembereknek, a felhő fejlesztők és az egyéb technikai felhasználói szerepkörök kell lennie.       |  Lista, próba, tranzakció 
|**Felügyeleti vezethet**    | Érdeklődők kap a piactérről, engedélyeznie kell a CRM (Marketo, Microsoft Dynamics vagy Salesforce) átfutási adatok fogadására.        |   Lista, próba, tranzakció      |
|**Adatvédelmi szabályzatát, illetve a használati feltételek**     |   Az adatvédelmi szabályzat egy nyilvános URL-CÍMEN keresztül elérhetőnek kell lennie. A használati feltételek szövegként közzététele során kell megadni.      |   Lista, próba, tranzakció      |
|**Támogatás**     |  Az ajánlat tartalmaznia kell egy nyilvánosan elérhető támogatási URL-címet, ahol az ügyfelek talál segítséget. A próbaverzió támogatási meg kell adni további költségek nélkül a próbaidőszak.       |  Próbaverzió, tranzakció       |

### <a name="prerequisites-specific-to-trial-publishing"></a>Próba közzététel adott előfeltételei

|**Követelmény**  | **Részletek**  |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Ingyenes próbaverzió lejárta és próbaverziója**     |  Az ügyfél az alkalmazás használhatja az ingyenes korlátozott ideig kell lennie.<br><br>Ez azt jelenti, hogy az ügyfél nem vesznek részt a terméket, és nem az alapul szolgáló Microsoft-k termék vagy szolgáltatás költségének licencek vagy előfizetések díjai. Mert mind a próbaverziós beállítást a közzétevő Microsoft termék-előfizetéshez vannak telepítve, a közzétevő kizárólag vezérli próba költség optimalizálása és kezelését.<br><br>Válassza ki a próba-előfizetésre, az interaktív bemutató, vagy kipróbálása. Beállításoktól függetlenül az ingyenes próbaverzióval kell nyújtania az ügyfél minimális idő kipróbálni az alkalmazás további költség nélkül.<br><br>A folyamat próbát létrehozásának megkezdéséhez elérhetők a cloudmarketplace@microsoft.com. <br><br>Vegye figyelembe, hogy az Azure piactér SaaS próba észlel kell engedélyezése a felhasználók számára az Active Directory munkahelyi hitelesítő adatokkal jelentkezhetnek be. [További információk](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences). |   Próbaverzió      | 
| **Könnyen konfigurálható, azonnal használható megoldás**    |  Az alkalmazás könnyű és gyors konfigurálásához és állítson be kell lennie.       |  Próbaverzió       |
|**Rendelkezésre állás/üzemideje**    |    A Szolgáltatottszoftver-alkalmazás vagy a platform legalább 99,9 %-os hasznos üzemidőt kell rendelkeznie.     |    Próbaverzió     |
|**Azure Active Directory**    |    Az ajánlat lehetővé kell tenni, Azure Active Directory (Azure AD) összevont egyszeri bejelentkezést (SSO) hozzájárulási engedélyezve van.      |  Próbaverzió|

### <a name="prerequisites-specific-to-transaction-publishing"></a>Tranzakció közzététel adott előfeltételei


|**Követelmény**  |**Részletek** |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Számlázási és annak mérésére**    |  A virtuális gép támogatnia kell a saját licenc vagy a használat alapú, a havi számlázási.       |    Tranzakció    |
|**Azure-kompatibilis virtuális merevlemez (VHD)**     |   Virtuális gépek kell épülő [Windows](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation) vagy [Linux](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).    |   Tranzakció      |

### <a name="prerequisites-specific-to-consulting-services-publishing"></a>Consulting Services tanácsadó szolgálat közzététel adott előfeltételei


|**Követelmények** |**Részletek**  |**Közzétételi beállítás**  |
|---------|---------|---------|
|**Szolgáltatási ajánlat tulajdonságok**     | A tanácsadási szolgáltatást kell lennie: <br>-A meghatározott hatókör, rögzített időtartamú, rögzített ár (vagy szabad) engagement kézbesíteni. <br>-Elsősorban értékesítési célú. <br>-Korlátozott egyetlen ügyfél számára. <br>-Hely a lefolytatni.        |    Lista     |
|**Consulting Services tanácsadó szolgálat partner követelményei**    |   *Csak AppSource*:  <br>- **Az ügyfél Engagement Dynamics 365**: rendelkezik ezüst vagy arany [felhő Ügyfélkapcsolat-kezelés](https://partner.microsoft.com/en-us/membership/cloud-customer-relationship-management-competency) kompetencia. <br>- **A pénzügyi és műveletek Enterprise edition Dynamics 365**: rendelkezik ezüst vagy arany [vállalati erőforrás-tervezés](https://partner.microsoft.com/en-us/membership/enterprise-resource-planning-competency) kompetencia, és egy minimális bevétel $ 25 000 felhőalapú műveleteket a záró 12 hónapon belül. <br>- **A pénzügyi és a műveletek, Business edition Dynamics 365**: szolgálhat [felhőalapú szolgáltatások szolgáltató (CSP)](https://partner.microsoft.com/en-us/cloud-solution-provider) vagy [digitális Partner a rekord (DPOR)](https://partner.microsoft.com/en-us/membership/digital-partner-of-record) legalább egy ügyfél. <br>- **A Power BI**: a [megoldás Partner] (file:///C:/Users/ellacroi/Downloads/BI%20Partner%20Program%20Overview%20 & % 20Incentives.pdf) feltételeknek. <br>- **PowerApps**: rendelkezik egy [Partner Showcase](https://powerapps.microsoft.com/en-us/partner-showcase/) megoldás. |    Lista     |

## <a name="using-azure-active-directory-to-enable-trials"></a>Az Azure Active Directoryval kísérletek engedélyezése
Az Azure Active Directory egy identitás felhőszolgáltatás, amely lehetővé teszi a hitelesítést a Microsoft munkahelyi vagy iskolai fiók szabványos protokollok segítségével: OAuth és az OpenID Connect. További tudnivalók az Azure AD meg a [termék weblap](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features). 

Microsoft Azure AD-val senki piactér hitelesíti. Amikor a hitelesített felhasználók a próbaidőszak listázása a piactéren keresztül kattint, és átirányítja a kísérleti környezetben, anélkül, hogy egy további bejelentkezési lépést a felhasználó oszthat közvetlenül a próbaverzió. A [jogkivonatot, amely az alkalmazás fogad az Azure AD-hitelesítés során](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens) , amely a felhasználói fiók létrehozása az alkalmazás segítségével értékes felhasználói adatokat tartalmaz. Ezután automatizálhatja a telepítési élmény és átalakítás érdekében. 

Az Azure AD az alkalmazás vagy a próbaidőszak egy kattintással hitelesítés engedélyezése:

- Leegyszerűsíti a felhasználói élmény a próba a piactér. 
- Fenntartja a működését a terméken belüli élményt, akkor is, ha a felhasználó a piactérről átirányítja a tartomány vagy kísérleti környezetben.
- Csökkenti a átirányítási elhagyása valószínűségét, mivel nincs további bejelentkezés lépés.
- Csökkenti a nagy feltöltése az Azure Active Directory-felhasználók központi telepítési lépést.

### <a name="certify-your-azure-ad-integration-for-the-marketplace-multitenant-applications"></a>Az Azure AD-integráció a piactér tanúsítására: több-bérlős alkalmazásokhoz

Ha az Azure AD ma támogatja:

- Az alkalmazás regisztrálása az Azure portálon.
- A több-bérlős támogatással funkció engedélyezése az Azure AD egy egyetlen kattintással próbaverziója eléréséhez.
- [További információk](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications).

Ha most ismerkedik az Azure AD összevont egyszeri bejelentkezés:

- Az alkalmazás regisztrálása az Azure portálon.
- Egyszeri bejelentkezés az Azure AD használatával fejlesztése [OpenID Connect](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) vagy [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code).
- A több-bérlős támogatással funkció engedélyezése az Azure AD egy egyetlen kattintással próbaverziója eléréséhez.
- [További információk](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified).

### <a name="certify-your-azure-ad-integration-for-the-marketplace-single-tenant-applications"></a>Az Azure AD-integráció a piactér tanúsítására: single-bérlői alkalmazások

Single-bérlői alkalmazások több lehetőség áll rendelkezésre:

- Felhasználók hozzáadása a könyvtárhoz vendégként használatával [Azure B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Az ügyfelek forduljon Me keresztül manuálisan kiépítése próbaverzió
- Ügyfél próbát fejlesztéséhez.
- Hozzon létre egy több-bérlős bemutató mintaalkalmazás egyszeri Bejelentkezést.

## <a name="publishing-processes-by-product-for-office-dynamics-and-power-bi"></a>Az Office, Dynamics, és a Power BI termék közzétételi folyamatok
Office-, Dynamics, és a Power BI AppSource alkalmazások esetén részletesebb kapcsolatos követelményekről az ebben a szakaszban a termékspecifikus dokumentációjában. 


|Product |Közzétételi információkat  |
|---------|---------|
|Office 365     |    Tekintse át a [közzétételi folyamat és irányelveket]( https://docs.microsoft.com/en-us/office/dev/store/submit-to-the-office-store).     |
|Dynamics 365 a pénzügyi és műveletek  |   Ön éppen való fejlesztéskor Enterprise Edition, tekintse át a [közzétételi folyamat és irányelveket](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source).      |
|Az ügyfél Engagement Dynamics 365 |Tekintse át a [közzétételi folyamat és irányelveket](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/developer/publish-app-appsource). |
|Power BI   |     Tekintse át a [közzétételi folyamat és irányelveket]( https://docs.microsoft.com/en-us/power-bi/developer/office-store).    |
|Cortana Intelligence     |    További tudnivalók [AppSource a Cortana](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide).     |
|AppSource tanácsadás ajánlatok     |  Tekintse át a [irányelvek és megtudhatja, hogyan nyújt az ajánlatot](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf).    |



## <a name="cloud-partner-portal-pre-publishing-checklist-for-the-azure-marketplace"></a>Cloud Partner portálra előre közzétételi ellenőrzőlista az Azure piactéren

A közzétételi folyamat megkezdése előtt érdemes egy ajánlatot készítéséhez szükséges összetevők megismerése. A következő összetevők az ajánlat létrehozása közzétételi munkafolyamat az a Cloud Partner portálra elvégzéséhez szükséges. 

### <a name="storefront-details"></a>Storefront részletei


|A közzétételi összetevő lesz szüksége  |Az ajánlattípus  |
|---------|---------|
|**Ajánlat (200 karakter) nevét és leírását (2000 karaktereket)**    |  Összes        |
|**Microsoft-partnernek (MPN) hálózati azonosító**   |  Összes       |
|**Ország vagy régió rendelkezésre állása**   | Összes        |
|**Bevonási időtartama**     |   Tanácsadási szolgáltatásokat      |
|**Alkalmazandó iparágakban, a kategóriák és a keresési kulcsszavakat**     |  Összes       |
|**Vállalati emblémát (48 x 48, 216 x 216)**     |  Tanácsadási szolgáltatásokat       |
|**A termék áttekintő videó (nem kötelező)**  |  Összes       |
|**Képernyőfelvételek (maximális 5, 1280 x 720)**   |    Összes     |
|**Marketing dokumentumok (maximális 3)**    |  Összes       |
|**Cél vezethet**    |   Összes      |

### <a name="contacts"></a>Kapcsolatok


|A közzétételi összetevő lesz szüksége  |Az ajánlattípus  |
|---------|---------|
|**Kapcsolattartási adatok (támogatási, műszaki osztály, kereskedelmi)**    |    Összes     |

### <a name="technical-info"></a>Technikai információ


|A közzétételi összetevő lesz szüksége  |Az ajánlattípus |
|---------|---------|
|**Próba URL-címe**     |  Összes próbaverziós ajánlat típusa       |
|**Támogatott nyelvek**    |   Összes próbaverziós ajánlat típusa      |
|**Alkalmazás verzió száma és a kiadási dátum**    |   Összes próbaverziós ajánlat típusa      |
|**Támogatási URL-címe**    |   Az összes próbaverziós ajánlat típusok, a virtuális gépek      |
|**Használati feltételei és adatvédelmi szabályzat URL-cím**     |    Összes     |

### <a name="test-drive"></a>Próbaüzem


|A közzétételi összetevő lesz szüksége  |Az ajánlattípus  |
|---------|---------|
|**Leírás és időtartama**     |  Csak kipróbálása       |
|**Felhasználói útmutatója**     |   Csak kipróbálása      |
|**Meghajtó videó (legfeljebb 1) tesztelése**     |  Csak kipróbálása       |
|**Teszt meghajtó ország vagy régió rendelkezésre állása**    |   Csak kipróbálása      |
|**Azure erőforráscsoport-név**   |         |
|**Az Azure előfizetés-azonosító**     |  Csak kipróbálása       |
|**Az Azure AD bérlő azonosítója**   |    Csak kipróbálása     |
|**Az Azure AD-alkalmazás azonosítója**  |  Csak kipróbálása       |
|**Az Azure AD alkalmazás-kulcs**     |   Csak kipróbálása      |

### <a name="storefrontmarketplace"></a>Kirakat/piactér


|A közzétételi összetevő lesz szüksége  |Az ajánlattípus  |
|---------|---------|
|**Cím (legfeljebb 50 karakter)**    |  Tranzakció: virtuális gépek, az Azure apps (megoldás sablonok és a felügyelt alkalmazások)       |
|**Összefoglalás (legfeljebb 200 karakter)**    |  Tranzakció: virtuális gépek, az Azure apps (megoldás sablonok és a felügyelt alkalmazások)       |
|**Hosszú összefoglalás (legfeljebb 256 karakter)**     |   Tranzakció: virtuális gépek, az Azure apps (megoldás sablonok és a felügyelt alkalmazások)      |
|**HTML-alapú leírása (legfeljebb 3000 olyan karakter)**    |  Tranzakció: virtuális gépek, az Azure apps (megoldás sablonok és a felügyelt alkalmazások)       |
|**Vállalati emblémát (40 x 40, 90 x 90, 115 x 115, 255 x 115, 815 x 290)**    |  Tranzakció: virtuális gépek, az Azure apps (megoldás sablonok és a felügyelt alkalmazások)       |

### <a name="sku"></a>SKU


|A közzétételi összetevő lesz szüksége  |Az ajánlattípus  |
|---------|---------|
|**Verziószám**     |    Tranzakció: Azure alkalmazásokat (megoldás sablonok és a felügyelt alkalmazások)     |
|**Csomagfájlt, amely tartalmazza a sablonfájlokat importálni és createUIDefinitionFile**   |Tranzakció: Azure alkalmazásokat (megoldás sablonok és a felügyelt alkalmazások)         |
|**Operációs rendszer részletei**    |   Tranzakció: virtuális gépek      |
|**Portok és protokollok használata**    |  Tranzakció: virtuális gépek       |
|**Lemez verzió és minden virtuális merevlemez használatban SAS URL-címe**   |  Tranzakció: virtuális gépek       |

## <a name="becoming-a-publisher"></a>A közzétevő végrehajtása

Ebben a szakaszban a lépések azt ismertetik:

- Az Azure piactér és AppSource közzétevő válik.
- A Cloud Partner portálra eléréséhez. Build, közzététele és kezelése az ajánlatot, hogy a portál fogja használni. 

### <a name="process-overview"></a>Folyamat áttekintése


|Piactér-regisztrációs lépésében  |Time  |Leírás  |
|---------|---------|---------|
| A Microsoft Partner Network regisztrálása | 15 perc | Közzétevők regisztrálva kell lennie a Microsoft Partner Network (MPN) első szintű fiók érvényesítése és kap további előnyökkel is jár, és az Azure piactér Publisher alatt támogatása |
|Microsoft azonosító létrehozása     |   15 perc      |   Partnerek Microsoft ID. kell A Microsoft ID a Cloud Partner portálra eléréséhez használható.       |
|A piactér jelölési űrlap elküldése     |  1-3 napos       |  Partnerek kell küldenie a jóváhagyási folyamat számára a piactér jelölési formájában. Az űrlap elküldése után a piactér bevezetési csapat tekintse át az alkalmazást, és a kérelem érvényesítése.       |
|Regisztrálja a fejlesztői központban     |    5 – 10 nap     | Alkalmazás regisztrálása a Microsoft Developer Centerben, ellenőrizze, hogy a partner, az ország, amelyen regisztrálva érvényes adó azonosítója érvényes jogalany a Microsoft szükség. A fejlesztői központ lehetővé teszi egy regisztrált a Microsoft developer kell, és adja meg az Azure fejlesztőprogrambeli a hozzáférést a partnert. <br><br>Vegye figyelembe, hogy a piactér jelölési képernyő még nem fejeződött be, ha meg kell adnia $99 regisztrációs díjat kell fizetnie. Ahhoz, hogy ez a díj az elérhetővé tételt követően, a piactér jelölési űrlap kitöltése és kapni fog egy promóciós kódot e-mailben.  |
|Jelentkezzen be a Cloud Partner portálra     |  15 perc       |   Miután a partner megkapja a jóváhagyást a piactér csapat a jelölési jóváhagyják, partneri hozzáférés a [Cloud Partner portálra](https://cloudpartner.azure.com/) engedélyezve van. A partner által használandó a Microsoft ID a jelölési űrlapból jelentkezzen be a közzétevő profilját, az a Cloud Partner portálra. A fejlesztői központban a regisztrációt követően a partner kell a fejlesztői központban regisztrált fiókjában társítandó közzététele az Azure piactér publisher profilt.      |

#### <a name="create-a-microsoft-id"></a>Microsoft azonosító létrehozása

A teljes piactérre közzétételi folyamat egy e-mail címet, amely azonosítja a piactér fiókot használja. Ez az e-mail cím egy Microsoft ID regisztrálni kell, és mindkét használható a [Microsoft Developer Center](https://developer.microsoft.com/) és a [Cloud Partner portálra](https://cloudpartner.azure.com/). 

Csak egy Microsoft ID-fiókot az Azure piactér és AppSource ajánlatok szükség van. Erősen ajánlott, hogy Ön nem megoszthatják az egyéb szolgáltatásoknak vagy ajánlatokat.

A kijelölt e-mail cím lehetőleg kell a vállalati tartományhoz, és az informatikai csapat által vezérelt. Az útmutató egy azonosító létrehozása előtt tekintse át a részeket [irányelvek és használati útmutatók](#guidelines-and-how-tos). 

#### <a name="register-in-microsoft-partner-network"></a>A Microsoft Partner Network regisztrálása 
Az Azure piactér vagy AppSource Publisher a vállalat váljon a Microsoft fog kapcsolattal. A Microsoft Partner Network (MPN) való részvétellel meg fog kell fog hozzáférni az alapvető előnyt értékre segítenek a vállalat a műszaki megoldások kidolgozásában és cége (pl.: technikai támogatást). A piactér közzétevőként való részvétellel hozzájárulnak a előnye a Microsoft Partner Network. Regisztrálja a MPN, látogasson el [Microsoft Partner Network](https://partner.microsoft.com/en-us/membership/). Ha a vállalata már regisztrálva van az MPN kell érvényesíteni. Regisztrálás után a rendszer kéri a fiók érvényesítéséhez a MPN azonosító publisher profiljainak érvényesítéséhez [Cloud Partner portálra](https://cloudpartner.azure.com/). 

#### <a name="submit-the-marketplace-nomination-form"></a>A piactér jelölési űrlap elküldése
A piactér bevezetési folyamat részeként kell jelölési űrlapot elküldeni. Az űrlap az alkalmazás vagy szolgáltatás ajánlat, a vállalati információk és támogatás szolgáltatja majd szintjének kapcsolatos információkat tartalmaz. 

- [Az Azure piactér jelölési képernyő](http://aka.ms/listonazuremarketplace)   
- [AppSource jelölési képernyő](http://aka.ms/listonappsource)

Az űrlap elküldése után a piactér team tekintse át az alkalmazást, és a kérelem érvényesítése. Miután a kérelem áttekintette van, a rendszer értesíti, a következő lépések a Cloud Partner portálra a jóváhagyott váláshoz a e-mailben.

#### <a name="register-in-the-developer-center"></a>Regisztrálja a fejlesztői központban

A [Microsoft Developer Center](https://developer.microsoft.com/) szükség a közzététel az alkalmazásokat, amelyek transact képességei, például a virtuális gépek, a megoldás sablonok és az Azure által kezelt alkalmazások. Ez a követelmény lehetővé teszi a Microsoft vállalati információk vállalata jogi, az adó és a entitások banki érvényesítéséhez. A bejegyzés a vállalat érvényes képviselője kell lennie, és biztosítania kell az identitásukat érvényesítéséhez személyes adataik. A személy regisztrálása a Microsoft ID, amelyet a vállalat kell használnia, és ugyanazt a fiókot kell használni a [Cloud Partner portálra](https://cloudpartner.azure.com/). 

>[!IMPORTANT]
>Mielőtt Developer Center a Microsoft-fiók létrehozása, győződjön meg arról, hogy a vállalat még nem rendelkezik egy.

A folyamat során azt fogja információgyűjtés vállalati cím, banki adatokat, és adó adatok. Ezek az adatok általában megtalálhatók a pénzügyi vagy üzleti kapcsolattartói adatok között. Ezenkívül el kell végeznie a következő közzétevő profil összetevők a különböző fázisait ajánlat létrehozásának és telepítésének befejezéséhez:


|**A Publisher profil**  |**Profil elejéig**  |**Átmeneti**  |**Lista és a fiók létrehozása**  |**Transact**
|---------|---------|---------|---------|---------|
|**Vállalati eszközregisztrációs**     | Rendelkeznie kell        |  Rendelkeznie kell       | Rendelkeznie kell        |  Rendelkeznie kell       |
|**Profil Adószám**   |    Optional     |    Optional     |  Optional       | Rendelkeznie kell      |
|**Banki fiók**     |   Optional      |    Optional     |  Optional       |  Rendelkeznie kell      |

Ez az eljárás részletes leírását, lásd: [regisztrálja a fejlesztői központban útmutatást](#instructions-on-how-to-register-in-the-developer-center). 

#### <a name="sign-in-to-the-cloud-partner-portal"></a>Jelentkezzen be a Cloud Partner portálra

Miután megkapta a jóváhagyást a piactér csapat, amely a jelölési jóváhagyják, és a regisztrált a [Microsoft Partner Network](https://partner.microsoft.com/en-us/membership/) és a [Microsoft Developer Center](https://dev.windows.com) (Ha a szükséges), egy fiókot hoz létre, hozzáférhet a [Cloud Partner portálra](https://cloudpartner.azure.com). Első bejelentkezési hitelesítő adatok fognak szerepelni a jelölési jóváhagyása e-mailben. 

A közzétevő profil eléréséhez a piactér fiókot (Microsoft ID) használja. Egyszer a Cloud Partner portálra, az utolsó lépése, hogy a Microsoft Partner Network és fejlesztői központ fiók társítása (ha szükséges) a megfelelő piactér Publisher profil közzététele. Ezt megteheti a felhő partnerportálon keresztül a gombra a képernyő alján, a közzétevő profilban.

A Cloud Partner portálra használatáról további információkért látogasson el a [további](https://cloudpartner.azure.com/#Learn) menüjében a portálon, és nézze meg a **dokumentáció** szakasz. 


## <a name="support"></a>Támogatás

Ez az az Azure piactéren a támogatási lehetőségeket listája:

**Az Azure piactér általános kapcsolatos kérdésekben**
|Támogatási csatornán keresztül |Leírás |
|---------|---------|
|E-mail: cloudmarketplace@microsoft.com     |  Bevezetési támogatás terjesztési lista. A bevezetési kérésekhez, felderítési munkamenetek és architektúra tervezési munkamenetek partnerekkel beállítása használ.        |

**Támogatási közzététele az Azure piactéren**

|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|E-mail: azurecertified@microsoft.com |   Alkalmazás-közzététel partnerek Azure piactér támogatása. Munkaidő a közép-európai időzónában van.      |
|E-mail: AzureMarketOnboard@microsoft.com |   Az Azure piactér megoldás jelölési űrlap és a folyamat támogatása. Munkaidő a közép-európai időzónában van.      |
|E-mail: Amp-testdrive@microsoft.com |   Tesztelje a meghajtók bevezetési elérésére. Munkaidő a közép-európai időzónában van. | 

**Azure portál piactér-támogatás**

|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|E-mailek: [támogatása](https://go.microsoft.com/fwlink/?linkid=844975)    |   Piactér-közzétételi Portáljára támogatás. Támogatás mindig, nappal és éjszaka áll rendelkezésre.        |

**A technikai támogatási szolgálathoz**


|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|MSDN fórumain: [piactér](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=DataMarket)     | A Microsoft Developer Network fórum.         |
|Stack overflow webhelyre: [Azure](https://stackoverflow.com/questions/tagged/azure)     |    A verem túlcsordulás környezet megoldások és kérdései vannak az Azure és a piactér kapcsolatos kapcsolatban:<ul><li>[Az Azure piactéren](https://stackoverflow.com/questions/tagged/azure-marketplace)</li><li>[Azure Resource Manager](https://stackoverflow.com/questions/tagged/azure-resource-manager)</li><li>[Azure Virtual Machines](https://stackoverflow.com/questions/tagged/azure-virtual-machine)</li></ul> |


**Marketing erőforrások**

|Támogatási csatornán keresztül  |Leírás  |
|---------|---------|
|E-mail: cosell@microsoft.com    |  Támogatja a bevezetési folyamat és a közös értékesít program kapcsolatos kérdésekre. Közép-európai időzónában alapján.        |
|E-mail: gtm@microsoft.com    |  Nyissa meg piacra jutási előnyei és a program kérdések támogatása. Munkaidő a közép-európai időzónában van.        |
|E-mail: CEBrand@Microsoft.com     |  Az Azure emblémák és branding használatával kapcsolatos kérdésekre adott válaszok.       |

## <a name="guidelines-and-how-tos"></a>Irányelvek és használati útmutatók

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-an-azure-marketplace-account"></a>A Microsoft ID kezelése az Azure piactér-fiók létrehozására vonatkozó irányelvek

A vállalati fiók létrehozásakor követi ezeket az irányelveket, ha egynél több személy kell megnyitni a fiókot a Microsoft-fiókkal bejelentkezik a fiók eléréséhez.

>[!IMPORTANT]
>Ahhoz, hogy több felhasználó is elérheti a fejlesztői központban regisztrált fiókjában, azt javasoljuk, Azure Active Directory szerepkörök hozzárendelése egyéni felhasználók számára. Ezek a felhasználók férhetnek hozzá a fiókot az egyéni bejelentkezik az Azure AD hitelesítő adatait. További információkért lásd: [útmutató a Microsoft IDs egy Azure ad tartományi összevont](#guidance-for-microsoft-ids-in-an-azure-ad-federated-domain). Hozzon létre a Microsoft-fiókjához tartozó e-mail cím használatával, a vállalati tartományhoz, de nem egy adott. Például: windowsapps@fabrikam.com.

- A lehető legkevesebb fejlesztők a Microsoft-fiókhoz való hozzáférés korlátozásához.
- A vállalati e-mailek terjesztési lista, amely tartalmazza az összes felhasználó el tudja érni a fejlesztői fiók beállítása, majd adja hozzá ezt az e-mail címet a biztonsági adatokat. Ez lehetővé teszi, hogy az alkalmazottak a listán szereplő megkapni a biztonsági kódokat, ha szükséges, és a Microsoft-fiók biztonsági adatainak kezelése. Egy terjesztési listát beállítása esetén nem valósítható meg, ha az egyéni e-mail fiók tulajdonosának kell lennie a rendelkezésre álló hozzáférési és osszanak meg a biztonsági kódot, ha a rendszer kéri. (Például a tulajdonos kér ahhoz a fiókhoz, vagy egy új eszközről lehet hozzáférni a program hozzáadja a új biztonsági adatait.)
- Adja hozzá a munkahelyi telefonszámot, amelyet a bővítmény nem igényel, és kulcs csapattagok számára érhető el.
- Jelentkezzen be a vállalat fejlesztői fiókjába a megbízható eszközök segítségével a fejlesztők általában rendelkeznek. Minden kulcs csoporttag megbízható alkalmazáskatalógusából hozzáféréssel kell rendelkeznie. A hozzáférés csökkenti a szükséges biztonsági kódok küldését, ha valaki van a fiókjához fér hozzá.
- Ha a fiókhoz való hozzáférés engedélyezése a nem megbízható számítógépről van szüksége, korlátozhatják, hogy legfeljebb öt fejlesztők számára. Ideális esetben a fejlesztők hozzá kell férnie a fiók a számítógépek, amelyek azonos földrajzi és a hálózati helyet.
- Gyakran ellenőrizze a vállalat biztonsági adatokat a [kezelése lap](https://account.live.com/proofs/Manage) való győződjön meg arról, hogy az összes.

A fejlesztői fiókba elsősorban a megbízható számítógépekhez legyenek elérhetők. Ez azért fontos, mert fiókonként hetente létrehozott kódok száma korlátozva van. Használja a megbízható számítógépekhez is lehetővé teszi, hogy a legtöbb zökkenőmentes bejelentkezési élményt.

További fejlesztői fiók irányelvek és biztonsági további információkért lásd: [fejlesztői fiókot](https://docs.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account).

### <a name="guidance-for-microsoft-ids-in-an-azure-ad-federated-domain"></a>Útmutató a Microsoft IDs összevont Azure AD-tartomány

A vállalati fiók is lehet összevont keresztül [Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/). Ha mégis megpróbálja létrehozása a Microsoft ID vállalati e-mail címmel hibát ad vissza. Ha hibaüzenetet kap, először forduljon az informatikai csapat számára, győződjön meg arról, hogy ez a helyzet. Ez egy ismert probléma, és jelenleg is dolgozunk névfeloldása. 

A probléma megoldásához, azt javasoljuk, hogy hozzon létre egy új e-mail címet a @outlook.com tartomány és hozzon létre egy szabályt. Kövesse az alábbi lépéseket:

1. Lépjen a [bejelentkezési lapon](https://signup.live.com/signup) válassza **egy új e-mail-cím beszerzése**.


2. Hozzon létre az új e-mail címet, és adjon meg egy jelszót. Ebben a lépésben létrehoz egy új Microsoft-ID, mind az e-mail postaládájuk az Outlook.com-os szolgáltatásban. Továbbra is a regisztrációs folyamat során, amíg ez a fiók nincs létrehozva.

   >[!IMPORTANT]
   >Győződjön meg arról, hogy a fejlesztői központban a regisztrációhoz használni kívánt e-mail Azonosítót vagy terjesztési lista, amely a Microsoft-fiók regisztrálva van-e. (Javasoljuk egy terjesztési listát a függőség eltávolításához egyének.) Ha nem, [a regisztrációt](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1).
   >
   >Emellett *minden e-mail-azonosítója a Microsoft vállalati tartományi* fejlesztői központ regisztrációs nem használható.

3. Miután létrehozta a Microsoft ID ezzel a fiókkal, jelentkezzen be a fiókba [postaláda](https://outlook.live.com/owa/). [Hozzon létre egy e-mailek továbbítása szabály](https://support.office.com/en-us/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed) minden e-maileket a postaláda áthelyezése az e-mail cím a tartomány kezelése az Azure piactér fiók létrehozott.

Az utolsó lépés befejezése után Outlook a minden e-mailek-kommunikáció a Microsoft ID e-mail fiókja a tartományban lévő küldése. Kell használnia a @outlook.com e-mail cím a Microsoft Developer Center és a Cloud Partner portálra hitelesítéséhez.

### <a name="instructions-on-how-to-register-in-the-developer-center"></a>Regisztrálja a fejlesztői központban útmutatást

1. Nyissa meg egy új Internet Explorer InPrivate vagy Chrome Incognito böngészési munkamenet győződjön meg arról, hogy még nem jelentkezett személyes fiókra.
2. Lépjen a [fejlesztői központ](http://dev.windows.com/registration?accountprogram=azure) egy értékesítő el.
3. Fejezze be a **segítsen megvédeni a fiókját** varázslót, amely ellenőrzi, hogy az identitás keresztül telefonszámát vagy e-mail címét.

   ![Telefon információ a "Segítsen megvédeni a fiókját" varázslóban jelölőnégyzetéből](./media/marketplace-publishers-guide/registerdevcenteremail.png)

4. Az a **regisztrációs-fiók adatainak** területen a legördülő menüből válassza ki a fiókot az országában vagy régiójában.

   ![Az országában vagy régiójában mezőben adatokkal](./media/marketplace-publishers-guide/devcenterregistrationaccountinfo.png)
   
   >[!WARNING]
   >A szolgáltatások az Azure piactéren értékesítésére, ellenőrizze, hogy a regisztrált entitás jóváhagyott "értékesít-feladó" országok egyikéből. Ez a korlátozás kifizetés és adózás okból is. További információkért lásd: a [piactér részvételét házirendek](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

5. A **fióktípus**, jelölje be **vállalati** , és válassza a **következő** gombra.

   >[!IMPORTANT]
   >Jobb megértése érdekében a fióktípusoknak a használata, és amelyek kiválaszthatja, lásd: [típusok, a helyek és a díjak](https://docs.microsoft.com/en-us/windows/uwp/publish/account-types-locations-and-fees).

6. A **Publisher megjelenített név**, adja meg a megjelenített neve (általában a vállalat nevét).

   >[!TIP]
   >A fejlesztői központban a megadott közzétevő megjelenített név nem jelenik meg az Azure piactéren után ajánlatát szerepel. Azonban meg kell adnia ezt a jelölőnégyzetet, a regisztráció befejezéséhez.

7. A **kapcsolattartási adatok**, írja be a fiók hitelesítési adatait.

   >[!IMPORTANT]
   >Meg kell adnia a pontos kapcsolattartási adatait. Az ellenőrzési folyamat a fejlesztői központban a vállalat jóváhagyására vonatkozó használja.

8. A **vállalati jóváhagyó**, írja be a jóváhagyó kapcsolattartási adatait. A jóváhagyó az a személy, aki ellenőrizheti, hogy Ön jogosult-e, hozzon létre egy fiókot a fejlesztői központban a szervezet nevében. Válassza ki **következő** befejezésekor.

   ![A kijelölt szakaszok "Fiók info" lapon](./media/marketplace-publishers-guide/devcenterregistrationpayment.png)

9. Adja meg a fizetési adatok fizetni a fiókját. Ha egy promóciós kódot, amely lefedi a regisztrációs költségét, akkor, amely Itt adhatja meg. Ellenkező esetben adja meg a hitelkártya adatait vagy a támogatott piacokon PayPal információkat. Amikor elkészült, válassza ki a **következő**.

   ![Fejlesztői központ fizetési információk](./media/marketplace-publishers-guide/devcenterregistrationpayment2.png)

10. Tekintse át a fiók adatait, és győződjön meg arról, hogy minden rendben. Majd olvassa el és fogadja el a használati feltételek azoknak a Microsoft Azure piactérről közzétevő. Jelölje be a annak jelzésére, hogy elolvasta és elfogadta a ezeket a feltételeket.
11. Válassza ki **Befejezés** a regisztráció megerősítéséhez. Egy megerősítő üzenetet küldünk a e-mail címre.
12. Ha csak szabad ajánlatok közzététele, válassza ki a **nyissa meg a Cloud Partner portálra**.

    Ha kereskedelmi (tranzakciós) ajánlatok--közzététele óránkénti számlázási modellt – kínál a virtuális gép jelölje ki például **módosításához**. Ezután meg kell adnia a bank és adó információt a fejlesztői központban regisztrált fiókjában, a következő szakaszban leírtak szerint.

#### <a name="add-bank-and-tax-information"></a>Banki és az adót adatok megadása

Ha közzé szeretné tenni a beszerzési kereskedelmi ajánlatokat, akkor is kell kifizetés hozzáadása és adó adatok, és küldje el a fejlesztői központban érvényesítése. Ha közzé szeretné tenni csak szabad vagy BYOL kínál, akkor nem kell hozzáadnia ezt az információt. Később adhat hozzá, de csak bizonyos idő a adó információinak ellenőrzésére. Ha ismeri a beszerzési kereskedelmi ajánlatokat tartalmazni fogja, azt javasoljuk, hogy hozzáadja a lehető leghamarabb.

>[!IMPORTANT]
>(Tranzakciós) kereskedelmi ajánlatokat, nem lehet leküldéssel terjeszteni a ajánlatok üzemi banki és adó információk végrehajtásáig.

Banki adatok hozzáadása:
1. Jelentkezzen be Microsoft-fiókjával a Microsoft Developer Centerben.
2. Válassza ki **kifizetés fiók** a bal oldali menüben. A **fizetési mód kiválasztása**, jelölje be **banki** vagy **PayPal**.

   >[!IMPORTANT]
   >Ha kereskedelmi ajánlatokat, amelyek az ügyfelek vásárlása a piactéren, ez az a fiók, ahol ezek a vásárlások a kifizetés fog kapni.

3. Adja meg a fizetési adatait, majd válassza ki **mentése** Ha elégedett.

   >[!IMPORTANT]
   >Ha szeretne frissíteni vagy kifizetés fiókjához, hajtsa végre a fenti lépéseket, de az aktuális információkat cserélje le az új információkkal. A kifizetés fiók módosítása késleltetheti-e a kifizetések által egy fizetési ciklus. Ez a késés az oka, hogy igazolnia kell a fiókját, ugyanúgy, ahogy azt a kifizetés fiók első beállításakor. Akkor lesz továbbra is beolvasása fizetett a teljes összeg után a fiók ellenőrzése után. Fizetési határideje a jelenlegi fizetési ciklus nem kerülnek be a következő egy.

4. Kattintson a **Tovább** gombra. 

Adó információ hozzáadása:

1. Jelentkezzen be a [Microsoft Developer Center](https://dev.windows.com) Microsoft-fiókjával (ha szükséges).
2. Válassza ki **profil adó** a bal oldali menüben.
3. Az a **a adó űrlap** lapon válassza ki az országot vagy régiót állandó rezidens esetében, és válassza ki az országot vagy régiót, ahol tartsa elsődleges lehetőségekkel. Kattintson a **Tovább** gombra.
4. Írja be a adó adatait, majd válassza ki **következő**.

Ha problémába ütközik fejlesztői központ regisztrációját, naplózni egy támogatási jegy:

1. Lépjen a [támogatási oldalára](https://developer.microsoft.com/windows/support).
2. A **Kapcsolatfelvétel**, jelölje be a **incidens nyújt** gombra.

   !["Submit incidens" gomb](./media/marketplace-publishers-guide/devcentersubmitincident.png)

3. Válassza ki **Dev Center webhely számára** , **problématípust**, és válassza ki **közzététel és kezeli az alkalmazásokat** , **kategória**. Ezután válassza ki a **indítsa el az e-mailek** gombra.   
4. A bejelentkezési oldalon Microsoft-fiók használatával jelentkezzen be. Ha nincs Microsoft-fiókja, létrehozhat egy hivatkozásra kattint. 
5. Adja meg a hiba részleteit, és küldje el a jegy kiválasztásával a **Submit** gombra.
