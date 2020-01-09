---
title: Kereskedelmi piactér – számlázás | Azure piactér
description: Ez a cikk a kereskedelmi piactérsel kapcsolatos kereskedelmi témájú témaköröket ismerteti.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 3f610dbc2c5cf052729857c09de1d437e52ac20a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480579"
---
# <a name="commercial-marketplace-billing"></a>Kereskedelmi piactér – számlázás 

Ez a cikk a kereskedelmi piactérsel kapcsolatos kereskedelmi témájú témaköröket tartalmazza:

- [Piactér-közzétételi beállítások](#marketplace-publishing-options) 
- [A Transact általános áttekintése](#transact-general-overview)
- [Transact számlázási modellek](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Piactér-közzétételi beállítások 

A kereskedelmi piactér számos közzétételi lehetőséget kínál a kiadók számára.

### <a name="list-and-trial-publishing-options"></a>A lista és a próbaverzió közzétételi lehetőségei

A kiadók kihasználhatják a listát, a próbaverziót és a saját licencek (BYOL) közzétételi lehetőségeit promóciós és felhasználói beszerzési célokra. Ezekkel a beállításokkal a Microsoft nem vesz részt közvetlenül a közzétevő szoftverlicenc-tranzakciójában, és nincs társított tranzakciós díj. A kiadók felelősek a szoftverlicenc-tranzakció valamennyi aspektusának támogatásához, beleértve a következőket: rendelés, teljesítés, mérés, Számlázás, Számlázás, fizetés és gyűjtés. A List és a próbaverzió közzétételi lehetőségeivel a kiadók az ügyféltől gyűjtött kiadói szoftverlicenc-díjak 100%-át őrzik meg.

### <a name="transact-publishing-option"></a>Transact közzétételi lehetőség

A lista és a próbaverzió közzétételi lehetőségei mellett a Transact közzétételi lehetőség is elérhető a közzétevők számára. Ez a lehetőség kihasználja a Microsoft globálisan elérhető kereskedelmi képességeinek előnyeit, és lehetővé teszi a Microsoft számára, hogy a közzétevő nevében üzemeltetje a Cloud Marketplace-tranzakciókat.

## <a name="transact-general-overview"></a>A Transact általános áttekintése

A Transact Publishing lehetőség használata esetén a Microsoft engedélyezi harmadik féltől származó szoftverek értékesítését, és néhány ajánlat típusának üzembe helyezését az ügyfél Azure-előfizetésében. A kiadónak meg kell fontolnia az infrastruktúra-díjak számlázását, valamint a kiadó saját szoftverlicenc-díját a számlázási modell és az ajánlat típusának kiválasztásakor. 

A Transact Publishing lehetőség jelenleg a következő típusú ajánlatok esetében támogatott: Virtual Machines, Azure Applications és SaaS-alkalmazások. 

![Transact az Azure Marketplace-en](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

#### <a name="for-virtual-machines-and-azure-applications"></a>Virtuális gépekhez és Azure-alkalmazásokhoz

A Virtual Machines és az Azure-alkalmazások esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetésének számítjuk fel. Az infrastruktúra-használati díjak díjszabása és a szoftveres szolgáltató licencelési díjaitól függetlenül, az ügyfél számláján kell megjelenni.

#### <a name="for-saas-apps"></a>SaaS-alkalmazások esetén

SaaS-alkalmazások esetén a közzétevőnek az Azure-infrastruktúra használati díjait és a szoftverlicenc-díjat egyetlen költségként kell megadnia. Az ügyfél számára átalány-díj formájában történik. Az Azure-infrastruktúra használatát a rendszer felügyeli, és közvetlenül a partnernek számlázza. Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat. A kiadók általában az Azure-infrastruktúra használati díjait az szoftverlicenc díjszabása szerint csomagolják. A szoftver licencelési díjait nem számítjuk fel, és nem használják a felhasználást.

## <a name="transact-billing-models"></a>Transact számlázási modellek

A használt tranzakciós lehetőségtől függően a közzétevő szoftverlicenc-díja a következőképpen jelenik meg:

- *Ingyenes*: szoftveres licencek díjmentesek.
- *Saját licenc használata (BYOL)* : a szoftveres licencekre vonatkozó bármilyen díj közvetlenül a közzétevő és az ügyfél között van kezelve. A Microsoft csak az Azure-infrastruktúra használati díjain halad át. (Csak Virtual Machines és Azure-alkalmazások.)
- *Utólagos elszámolás: a*szoftveres licencelési díjak a felhasznált Azure-infrastruktúra alapján óránkénti (vCPU) díjszabás szerint jelennek meg. Ez a modell csak a virtuális gépekre és az Azure-alkalmazásokra vonatkozik.
- Az *előfizetés díjszabása*: a szoftverlicenc-díj havi vagy éves, ismétlődő díj, amely átalányként vagy ülőhelyként van kiszámlázva. Ez a modell csak az SaaS-alkalmazásokra és az Azure-alkalmazások által felügyelt alkalmazásokra vonatkozik.
- *Ingyenes szoftver próbaverziója*: a szoftveres licencek díjmentesek, 30 vagy 90 napig.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ingyenes és saját licencek (BYOL) díjszabása

Az ingyenes vagy a saját licencű tranzakciós ajánlat közzététele esetén a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A lista és a próbaverzió közzétételi lehetőségeihez hasonlóan a közzétevő a szoftverlicenc-díjak 100%-át is megtartja.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Utólagos elszámolás és előfizetés (hely alapján) díjszabása

Az utólagos elszámolású vagy előfizetési tranzakciós ajánlat közzétételekor a Microsoft biztosítja a technológia és a szolgáltatások számára a szoftverlicenc-vásárlások, a visszaadott adatok és a díjak feldolgozását. Ebben a forgatókönyvben a közzétevő engedélyezi a Microsoft számára, hogy ügynökként működjön ezen célokból. A közzétevő lehetővé teszi a Microsoft számára, hogy elősegítse a szoftverlicencelési tranzakciót, miközben megtartja a megjelölését eladóként, szolgáltatóként, terjesztőként és licencelőként.

A Microsoft lehetővé teszi, hogy az ügyfelek a Microsoft kereskedelmi piactér és a kiadó végfelhasználói licencszerződésének feltételei és kikötései alapján közzétevő szoftvert rendeljenek, licencet és használjanak. A kiadóknak meg kell adniuk a végfelhasználói licencszerződést, vagy az ajánlat létrehozásakor a [standard szerződést](https://docs.microsoft.com/azure/marketplace/standard-contract) kell választaniuk.

### <a name="free-software-trials"></a>Ingyenes szoftveres próbaverziók

A Transact Publishing forgatókönyvek esetében a közzétevő 30 napig vagy 90 napig ingyenesen elérhetővé teheti a szoftverlicenc-t. Ez a diszkont képesség nem tartalmazza az Azure-infrastruktúra használatának költségeit, amelyet a partneri megoldás használata vezérel.

### <a name="private-offers"></a>Privát ajánlatok

Az ajánlatok típusai és a számlázási modellek használata mellett a kiadók egy privát ajánlatot is igényelhetnek, az egyeztetett és az ügylet-specifikus díjszabással vagy egyéni konfigurációkkal kiegészítve. A 3 Transact-közzétételi lehetőség mind a privát ajánlatokat támogatja.

Ez a lehetőség nagyobb vagy alacsonyabb díjszabást tesz lehetővé, mint a nyilvánosan elérhető ajánlat. A privát ajánlatok kedvezményes vagy prémium szintű ajánlatokat vehetnek igénybe. Egy vagy több ügyfél számára elérhetővé teheti az Azure-előfizetését az ajánlat szintjén.

### <a name="examples"></a>Példák

#### <a name="pay-as-you-go"></a>Utólagos fizetés

* Ha engedélyezi az utólagos elszámolású lehetőséget, akkor a következő Cost-szerkezettel rendelkezik.

|A licenc díja  | $1,00/óra  |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
|*Az ügyfelet a Microsoft számlázza*    |  *$1,14/óra*       |

* Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $1,14-ra.

|Microsoft-számlák  | $1,14/óra  |
|---------|---------|
|A Microsoft a licencelési díj 80%-ában fizet|   $0,80/óra     |
|A Microsoft a licencelési díj 20%-át megtartja  |  $0,20/óra       |
|A Microsoft az Azure használati díja 100%-át őrzi meg | $0,14/óra |

### <a name="bring-your-own-license-byol"></a>Saját licenc használata (BYOL)

* Ha engedélyezi a BYOL beállítást, a következő Cost-struktúra lesz.

|A licenc díja  | Licencszerződés megtárgyalása és számlázása  |
|---------|---------|
|Azure-használati díj (D1/1-Core)    |   $0,14/óra     |
|*Az ügyfelet a Microsoft számlázza*    |  *$0,14/óra*       |

* Ebben az esetben a Microsoft a közzétett virtuálisgép-rendszerképet használja óránként $0,14-ra.

|Microsoft-számlák  | $0,14/óra  |
|---------|---------|
|A Microsoft megtartja az Azure használati költségeit    |   $0,14/óra     |
|A Microsoft a licencelési díj 0%-át őrzi meg   |  $0,00/óra       |

### <a name="saas-app-subscription"></a>SaaS-alkalmazás előfizetése

Ezt a beállítást úgy kell beállítani, hogy a Microsofton keresztül értékesítsen, és akár havi, akár évenkénti díjszabással vagy felhasználónként.

• Ha engedélyezi az értékesítés a Microsoft számára lehetőséget egy SaaS-ajánlathoz, akkor a következő Cost-struktúra áll rendelkezésére.

|A licenc díja       | $100,00/hó  |
|--------------|---------|
|Azure-használati díj (D1/1-Core)    | A számlázás közvetlenül a közzétevőre történik, nem az ügyfél |
|*Az ügyfelet a Microsoft számlázza*    |  *$100,00/hó (Megjegyzés: a kiadónak figyelembe kell vennie a licencszerződésben felmerülő vagy továbbított infrastruktúra-költségek díját)*  |

* Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.
* Azok a partnerek, akik a kedvezményes Piactéri szolgáltatási díjjal rendelkeznek, csökkentett tranzakciós díjat kapnak az SaaS-ajánlatokról, a május 2019-ig, 2020-ig. Ebben az esetben a Microsoft $100,00-es számlát küld a szoftverlicenc számára, és kifizeti a $90,00-et a közzétevőnek.

|Microsoft-számlák  | $100,00/hó  |
|---------|---------|
|A Microsoft a licencelési díj 80%-ában fizet <br> \* a Microsoft a licencelési díj 90%-át fizet minden minősített SaaS-alkalmazáshoz   |   $80,00/hó <br> \* $90,00/hó    |
|A Microsoft a licencelési díj 20%-át megtartja <br> \* a Microsoft megtartja a licencek díjainak 10%-át minden minősített SaaS-alkalmazás esetében.  |  $20,00/hó <br> \* $10,00     |

**Csökkentett Piactéri szolgáltatási díj:** A kereskedelmi piactéren közzétett egyes SaaS-termékek esetében a Microsoft a piactér szolgáltatási díját 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra.  Ahhoz, hogy a termék megfelelő legyen, legalább az egyik terméket meg kell jelölnie a Microsoft számára az IP-címek közös értékesítésének vagy az IP-címek közös értékesítésének megfelelően. A csökkentett Piactéri szolgáltatási díj a hónapra való fogadásához a jogosultságnak legalább öt (5) munkanapon belül meg kell felelnie az előző naptári hónap vége előtt. A Marketplace szolgáltatási díja nem vonatkozik a kereskedelmi Piactéren keresztül elérhető virtuális gépekre, felügyelt alkalmazásokra vagy más termékekre.  Ez a csökkentett Piactéri szolgáltatási díj a Microsoft által a 2019 és a 2020. május 1. és a. június 30. között gyűjtött licencek díjai alapján lesz elérhető.  Ezt követően a Piactéri szolgáltatási díj visszaáll a normál mennyiségre.
