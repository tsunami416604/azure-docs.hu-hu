---
title: Microsoft kereskedelmi piactér – Transact-képességek
description: Ez a cikk a kereskedelmi Piactéri Transact lehetőség díjszabását, számlázását, számlázását és kifizetési szempontjait ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: dsindona
ms.openlocfilehash: b190f2392fae11b074b2ee90a1c89f272b88818d
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84790297"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Kereskedelmi Piactéri Transact-képességek

## <a name="transactions-by-publishing-option"></a>Tranzakciók közzétételi lehetőség szerint

A kiadó vagy a Microsoft felelős a kereskedelmi piactéren található ajánlatokhoz tartozó szoftverlicenc-tranzakciók kezeléséért. Az ajánlathoz választott közzétételi lehetőség határozza meg, hogy ki kezeli a tranzakciót. Lásd: [közzétételi lehetőség meghatározása](./determine-your-listing-type.md#choose-a-publishing-option) a rendelkezésre álláshoz és az egyes közzétételi lehetőségek magyarázatához.

### <a name="list-trial-and-byol-publishing-options"></a>Listázási, próbaverziós és BYOL közzétételi lehetőségek

A meglévő kereskedelmi képességekkel rendelkező kiadók kiválaszthatják a lista, a próbaverzió és a saját licencek (BYOL) közzétételi lehetőségeit promóciós és felhasználói beszerzési célokra. Ezekkel a beállításokkal a Microsoft nem vesz részt közvetlenül a kiadó szoftverlicenc-tranzakciójában, és nincs társított tranzakciós díj. A kiadók felelősek a szoftverlicenc-tranzakció valamennyi aspektusának támogatásához, beleértve a sorrendet, a teljesítést, a mérést, a számlázást, a számlázást, a fizetést és a gyűjtést. A List és a próbaverzió közzétételi lehetőségeivel a kiadók az ügyféltől gyűjtött kiadói szoftverlicenc-díjak 100%-át őrzik meg.

### <a name="transact-publishing-option"></a>Transact közzétételi lehetőség

A Transact Publishing lehetőség kihasználja a Microsoft kereskedelmi képességeinek előnyeit, és teljes körű megoldást kínál a felderítésre és a kiértékelésre a vásárlás és a megvalósítás terén. A Transact-ajánlatok számlázása egy meglévő Microsoft-előfizetés vagy egy hitelkártya alapján történik, amely lehetővé teszi a Microsoft számára, hogy a közzétevő nevében üzemeltetje a Cloud Marketplace-tranzakciókat.

Akkor válassza a Transact lehetőséget, amikor új ajánlatot hoz létre a partner Centerben. Az **ajánlat beállítása** lapon a **telepítés részletei**területen válassza az "igen, szeretnék eladni a Microsofton keresztül lehetőséget, és a Microsoft gazdagép tranzakciói a saját nevükben" elemet. Ez a beállítás csak akkor jelenik meg, ha a Transact elérhető az ajánlat típusaként.

## <a name="transact-overview"></a>A Transact áttekintése

A Transact Publishing lehetőség használata esetén a Microsoft engedélyezi harmadik féltől származó szoftverek értékesítését és néhány ajánlat típusának üzembe helyezését az ügyfél Azure-előfizetésében. A kiadónak meg kell fontolnia az infrastruktúra-díjak és a saját szoftverlicenc-díjak számlázását a számlázási modell és az ajánlat típusának kiválasztásakor.

A Transact Publishing lehetőség jelenleg a következő típusú ajánlatok esetében támogatott:

- Virtual machines (Virtuális gépek)
- Azure-alkalmazások
- SaaS-alkalmazás

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

A **Virtual Machines** és az **Azure-alkalmazások**esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetése alapján számoljuk el. Az infrastruktúra-használati díjak díjszabása és a szoftveres szolgáltató licencelési díjaitól függetlenül, az ügyfél számláján kell megjelenni.

**SaaS-alkalmazások**esetén a közzétevőnek az Azure-infrastruktúra használati díjait és a szoftverlicenc-díjat egyetlen Cost-elemként kell megadnia.  Az ügyfél számára átalány-díj formájában történik. Az Azure-infrastruktúra használatát a rendszer felügyeli, és közvetlenül a partnernek számlázza. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadók általában az Azure-infrastruktúra használati díjait az szoftverlicenc díjszabása szerint csomagolják. A szoftver licencelési díjait nem számítjuk fel, és nem használják a felhasználást.

## <a name="transact-billing-models"></a>Transact számlázási modellek

A használt tranzakciós lehetőségtől függően a szoftverlicenc-díjak a következők:

- **Ingyenes** – a szoftveres licencek díjmentesek.
- **Saját licenc** használata (BYOL) – a licenccel kapcsolatos összes díjat közvetlenül a közzétevő és az ügyfél között kezelheti. A Microsoft csak az Azure-infrastruktúra használati díjain halad át. Ez csak a virtuális gépekre és az Azure-alkalmazásokra vonatkozik.
- **Utólagos elszámolású – a** szoftverlicenc-díj a felhasznált Azure-infrastruktúra alapján óránkénti (vCPU) díjszabás szerint jelenik meg. Ez csak a virtuális gépekre és az Azure-alkalmazásokra vonatkozik.
- Az **előfizetés díjszabása** – a szoftverlicenc-díj a havi vagy az éves, ismétlődő díj, amely átalányként vagy munkaállomásonkéntiként van kiszámlázva. Ez csak az SaaS-alkalmazásokra (havi vagy éves) és az Azure Applications által felügyelt alkalmazásokra vonatkozik (havonta).
- **Ingyenes szoftver próbaverziója** – a szoftverlicenc 30 vagy 90 napig díjmentes.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ingyenes és saját licencek (BYOL) díjszabása

Az ingyenes vagy a saját licencű tranzakciós ajánlat közzététele esetén a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A lista és a próbaverzió közzétételi lehetőségeihez hasonlóan a közzétevő a szoftverlicenc-díjak 100%-át is megtartja.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Utólagos elszámolás és előfizetés (hely alapján) díjszabása

Az utólagos elszámolású vagy előfizetési tranzakciós ajánlat közzétételekor a Microsoft biztosítja a technológiát és szolgáltatásokat a szoftverlicenc-vásárlások, a visszaadott adatok és a jóváírások feldolgozásához. Ebben a forgatókönyvben a közzétevő engedélyezi a Microsoft számára, hogy ügynökként működjön ezen célokból. A közzétevő lehetővé teszi a Microsoft számára, hogy elősegítse a szoftverlicencelési tranzakciót, miközben megtartja a megjelölését eladóként, szolgáltatóként, terjesztőként és licencelőként.

A Microsoft lehetővé teszi, hogy az ügyfelek a Microsoft kereskedelmi piactérének és a végfelhasználói licencszerződésének feltételeitől függően megrendelést, licencet és használati feltételeket alkalmazzanak. Meg kell adnia a saját végfelhasználói licencszerződését, vagy az ajánlat létrehozásakor a [standard szerződést](./standard-contract.md) kell választania.

### <a name="free-software-trials"></a>Ingyenes szoftveres próbaverziók

A Transact Publishing forgatókönyvek esetében 30 vagy 90 napig ingyenesen elérhetővé teheti a szoftverlicenc-t. Ez a diszkont képesség nem tartalmazza az Azure-infrastruktúra használatának költségeit a partneri megoldás használatával.

### <a name="private-offers"></a>Személyes ajánlatok

Az ajánlati típusok és a számlázási modellek használata mellett egy ajánlat értékesítésére is lehetősége van, és az egyeztetett, az ügylet-specifikus díjszabással vagy az egyéni konfigurációkkal is elvégezheti a tranzakciót. Mind a három Transact közzétételi lehetőség támogatja a privát ajánlatokat.

Ez a lehetőség nagyobb vagy alacsonyabb díjszabást tesz lehetővé, mint a nyilvánosan elérhető ajánlat. A privát ajánlatok kedvezményes vagy prémium szintű ajánlathoz adhatók. Egy vagy több ügyfél számára elérhetővé teheti az Azure-előfizetését az ajánlat szintjén.

### <a name="examples"></a>Példák

**Használatalapú fizetés** 

Az utólagos elszámolású szolgáltatás a következő felépítésű:

|A licenc díja  | $1,00/óra   |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
|*Az ügyfelet a Microsoft számlázza*    |  *$1,14/óra*       |
||

Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $1,14-ra.

|Microsoft-számlák  | $1,14/óra  |
|---------|---------|
|A Microsoft a licencelési díj 80%-ában fizet|   $0,80/óra     |
|A Microsoft a licencelési díj 20%-át megtartja  |  $0,20/óra       |
|A Microsoft az Azure használati díja 100%-át őrzi meg | $0,14/óra |
||

**Saját licenc használata (BYOL)**

A BYOL a következő Cost-struktúrával rendelkezik:

|A licenc díja  | Licencszerződés megtárgyalása és számlázása  |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
|*Az ügyfelet a Microsoft számlázza*    |  *$0,14/óra*       |
||

Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $0,14-ra.

|Microsoft-számlák  | $0,14/óra  |
|---------|---------|
|A Microsoft megtartja az Azure használati költségeit    |   $0,14/óra     |
|A Microsoft a licencelési díj 0%-át őrzi meg   |  $0,00/óra       |
||

**SaaS-alkalmazás előfizetése**

Ezt a beállítást úgy kell beállítani, hogy a Microsofton keresztül értékesítsen, és akár havi, akár évenkénti díjszabással vagy felhasználónként. Ha a SaaS-ajánlathoz engedélyezi az **értékesítés a Microsoft** számára lehetőséget, a következő Cost-struktúra áll rendelkezésére:

| A licenc díja       | $100,00/hó  |
|--------------|---------|
| Azure-használati díj (D1/1-Core)    | A számlázás közvetlenül a közzétevőre történik, nem az ügyfél |
| *Az ügyfelet a Microsoft számlázza*    |  *$100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján)*  |
||

Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.

Azok a partnerek, akik a kedvezményes Piactéri szolgáltatási díjjal rendelkeznek, csökkentett tranzakciós díjat kapnak az SaaS-ajánlatokról, a május 2019-ig, 2020-ig.

Ebben az esetben a Microsoft $100,00-es számlákat küld a szoftverlicenc számára, és kifizeti a $90,00-et a közzétevőnek:

|Microsoft-számlák  | $100,00/hó  |
|---------|---------|
|A Microsoft a licencelési díj 80%-ában fizet <br> \*A Microsoft a licencelési díj 90%-át fizet minden minősített SaaS-alkalmazáshoz   |   $80,00/hó <br> \*$90,00/hó    |
|A Microsoft a licencelési díj 20%-át megtartja <br> \*A Microsoft minden minősített SaaS-alkalmazás esetében megőrzi a licencek költségeit.  |  $20,00/hó <br> \*$10,00     |

A kereskedelmi piactéren közzétett egyes SaaS-termékek esetében a Microsoft a **piactér szolgáltatási díját** 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra. Ahhoz, hogy ajánlata bejusson, legalább az egyik ajánlatát a Microsoft számára kell kijelölni, vagy az IP-címek közös értékesítésének feltételeit, vagy az IP-címek közös értékesítését. A csökkentett Piactéri szolgáltatási díj a hónapra való fogadásához a jogosultságnak legalább öt munkanapon belül meg kell felelnie az előző naptári hónap vége előtt. A Marketplace szolgáltatási díja nem vonatkozik a kereskedelmi Piactéren keresztül elérhető virtuális gépekre, felügyelt alkalmazásokra vagy más termékekre. Ez a kedvezményes díj a minősített ajánlatok számára érhető el, a Microsoft által a 2019-es és a 2020-es június 30. között gyűjtött licencek díjaival. Ezt követően a díj vissza fog térni a normál mennyiségre.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Ügyfél-számlázás, fizetés, Számlázás és gyűjtemények

**Számlázás és fizetés** – az ügyfél által előnyben részesített számlázási módszert használhatja előfizetés vagy áttéréshez szoftverlicenc-díjak továbbítására.

**Nagyvállalati szerződés** – ha az ügyfél előnyben részesített számlázási módszere a Microsoft nagyvállalati szerződés, a szoftverlicenc-díjat a számlázási módszer alapján részletezett költségként számítjuk fel, elkülönítve az Azure-specifikus használati költségektől.

**Bankkártyák és havi számla** – az ügyfelek bankkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjat a Nagyvállalati Szerződés-forgatókönyvhöz hasonlóan számítjuk fel, amely az Azure-specifikus használati költségektől függ.

**Ingyenes kreditek és pénzügyi kötelezettségvállalás** – egyes ügyfelek a nagyvállalati szerződés pénzügyi kötelezettségvállalását választják, vagy az Azure-ban ingyenes krediteket adtak meg az Azure-ban. Bár ezek a kreditek felhasználhatók az Azure-használat megfizetésére, nem használhatók a kiadói szoftverlicenc-díjakért.

**Számlázás és gyűjtemények** – a kiadói szoftverlicenc számlázása az ügyfél által kiválasztott számlázási módszer használatával történik, és a számlázási ütemtervet követi. A Nagyvállalati Szerződés nélküli ügyfelek számlázása havonta történik a piactér szoftverlicenc esetében. A Nagyvállalati Szerződés rendelkező ügyfelekért havonta kell fizetnie egy negyedévente bemutatott számlán keresztül.

Ha az előfizetés vagy az utólagos elszámolású díjszabási modell van kiválasztva, a Microsoft a közzétevő ügynöke működik, és felelős a számlázás, a fizetés és a gyűjtés minden vonatkozásában.

### <a name="publisher-payout-and-reporting"></a>Kiadói kifizetés és jelentéskészítés

A Microsoft által ügynökként gyűjtött licencelési díjakat 20%-os tranzakciós díj terheli, kivéve, ha másként van megadva, és a kiadói kifizetés időpontjában le vannak vonva.

Az ügyfelek általában a Nagyvállalati Szerződés vagy egy hitelkártya-alapú utólagos elszámolású szerződés használatával vásárolnak. A szerződés típusa határozza meg a számlázást, a számlázást, a gyűjtést és a kifizetés időzítését.

>[!NOTE]
>A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Analytics szakaszán keresztül érhető el.

#### <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

További információ és jogi szabályzatok: [közzétevői szerződés](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (a partner Centerben érhető el).

A számlázási kérdésekkel kapcsolatos segítségért forduljon a [kereskedelmi piactér közzétevője támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-követelmények

Ez a szakasz a különböző ajánlati típusok Transact-követelményeit ismerteti.

### <a name="requirements-for-all-offer-types"></a>Az összes ajánlat típusára vonatkozó követelmények

- A Transact közzétételi lehetőséghez Microsoft-fiók és pénzügyi információk szükségesek, az ajánlat díjszabási modelljétől függetlenül.
- A kötelező pénzügyi információk közé tartozik a kifizetési fiók és az adó profil.

A fiókok beállításával kapcsolatos további információkért lásd: [a kereskedelmi piactér fiókjának kezelése a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account).

### <a name="requirements-for-specific-offer-types"></a>Az egyes ajánlatok típusaira vonatkozó követelmények

A Transact Publishing beállítás csak a következő Piactéri ajánlatok esetében használható:

- **Virtuális gép** – válassza az ingyenes, a saját licenc vagy az utólagos elszámolású modelleket, valamint az ajánlat szintjén meghatározott SKU-t. Az ügyfél Azure-számláján a Microsoft a mögöttes Azure-infrastruktúra díjaitól függetlenül bemutatja a kiadói szoftverlicenc-díjat. Az Azure-infrastruktúra díjait a közzétevő szoftver használja.

- **Azure-alkalmazás: megoldás-sablon vagy felügyelt** alkalmazás – egy vagy több virtuális gépet kell kiépítenie, és a virtuális gép díjszabásának összegével kell lekérnie. Egyetlen csomagban felügyelt alkalmazások esetén a virtuális gépek díjszabása helyett egy átalánydíjas havi előfizetés is kiválasztható a díjszabási modellként. Bizonyos esetekben az Azure-infrastruktúra használati díjait a rendszer külön adja át az ügyfélnek a szoftverlicenc-díjaktól, de ugyanarra a számlázási utasításra. Ha azonban a felügyelt alkalmazáshoz az ISV-infrastruktúra díjait konfigurálja, az Azure-erőforrások számlázása a kiadóra történik, és az ügyfél egy átalánydíjas díjat kap, amely magában foglalja az infrastruktúra, a szoftverlicenc és a felügyeleti szolgáltatások költségeit.

- **SaaS-alkalmazás** – több-bérlős megoldásnak kell lennie, a hitelesítéshez [Azure Active Directoryt](https://azure.microsoft.com/services/active-directory/) kell használnia, és integrálni kell a [SaaS-megvalósítási API](partner-center-portal/pc-saas-fulfillment-api-v2.md)-kkal. Az Azure-infrastruktúra használatának felügyelete és számlázása közvetlenül Önnek (a partnernek) történik, ezért az Azure-infrastruktúra használati díjait és a szoftveres licencelési díjakat egyetlen költségként kell elszámolni. Részletes útmutatásért lásd: [új SaaS-ajánlat létrehozása a kereskedelmi piactéren](partner-center-portal/create-new-saas-offer.md).

## <a name="next-steps"></a>További lépések

- Az ajánlat kiválasztásának és konfigurálásának véglegesítéséhez tekintse át a támogathatósági követelményeket a közzétételi beállítások az ajánlat típusa szerint szakaszban.
- Tekintse át a közzétételi mintákat a kirakati példákkal, hogy a megoldás hogyan térképezi fel az ajánlat típusát és konfigurációját.
