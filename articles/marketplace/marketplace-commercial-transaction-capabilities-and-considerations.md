---
title: A piactér kereskedelmi tranzakcióinak képességei és szempontjai | Azure
description: Ez a cikk ismerteti a Transact díjszabását, a számlázást, a számlázást és a kifizetési szempontokat az ajánlat típusához.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: c58a36988e3aee9b122446e3ee3c4878a582b8ad
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871029"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Kereskedelmi piactér tranzakciós képességei és szempontjai

Ez a cikk a kereskedelmi piactérhez kapcsolódó alábbi kereskedelmi témákat ismerteti

* Piactér-közzétételi beállítások
* A Transact általános áttekintése
* Transact számlázási modellek
* Transact-követelmények

## <a name="marketplace-publishing-options"></a>Piactér-közzétételi beállítások

A kereskedelmi piactér-közzétevők számára a következő közzétételi lehetőségek érhetők el.

### <a name="list--trial-publishing-options"></a>& Próbaverziós közzétételi beállításainak listázása

A kiadók kihasználhatják a lista, a próbaverzió és a BYOL közzétételi lehetőségeit a promóciós és a felhasználói beszerzési célokra. Ezekkel a beállításokkal a Microsoft nem vesz részt közvetlenül a közzétevő szoftverlicenc-tranzakciójában, és nincs társított tranzakciós díj. A kiadók felelősek a szoftverlicenc-tranzakció valamennyi aspektusának támogatásához, beleértve a következőket: rendelés, teljesítés, mérés, Számlázás, Számlázás, fizetés és gyűjtés. A List és a próbaverzió közzétételi lehetőségeivel a kiadók az ügyféltől gyűjtött kiadói szoftverlicenc-díjak 100%-át őrzik meg. 

### <a name="transact-publishing-option"></a>Transact közzétételi lehetőség

A lista és a próbaverzió közzétételi lehetőségei mellett a Transact közzétételi lehetőség is elérhető a közzétevők számára. Ez kihasználja a Microsoft globálisan elérhető kereskedelmi képességeinek előnyeit, és lehetővé teszi a Microsoft számára, hogy a közzétevő nevében üzemeltetje a Cloud Marketplace-tranzakciókat.

## <a name="transact-general-overview"></a>A Transact általános áttekintése

A Transact Publishing lehetőség használata esetén a Microsoft engedélyezi harmadik féltől származó szoftverek értékesítését, és néhány ajánlat típusának üzembe helyezését az ügyfél Azure-előfizetésében. A kiadónak meg kell fontolnia az infrastruktúra-díjak számlázását, valamint a kiadó saját szoftverlicenc-díját a számlázási modell és az ajánlat típusának kiválasztásakor.

A Transact Publishing lehetőség jelenleg a következő típusú ajánlatok esetében támogatott: Virtual Machines, Azure-alkalmazások és SaaS-alkalmazások.


![[A vállalati ügyletek lebonyolítása az Azure Marketplace-en]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

**Virtual Machines és Azure-alkalmazásokhoz**

Virtual Machines és Azure-alkalmazások esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetése alapján számoljuk el.  Az infrastruktúra-használati díjak díjszabása és a szoftveres szolgáltató licencelési díjaitól függetlenül, az ügyfél számláján kell megjelenni.

**SaaS-alkalmazások esetén**

SaaS-alkalmazások esetén a közzétevőnek az Azure-infrastruktúra használati díjait és a szoftverlicenc-díjat egyetlen költségként kell megadnia.  Az ügyfél számára átalány-díj formájában történik. Az Azure-infrastruktúra használatát a rendszer felügyeli, és közvetlenül a partnernek számlázza.  Az ügyfél nem látja a tényleges infrastruktúra-használati díjakat.  A kiadók általában az Azure-infrastruktúra használati díjait az szoftverlicenc díjszabása szerint csomagolják.  A szoftver licencelési díjait nem számítjuk fel, és nem használják a felhasználást.

## <a name="transact-billing-models"></a>Transact számlázási modellek

A használt tranzakciós lehetőségtől függően a közzétevő szoftverlicenc-díja a következőképpen jelenik meg:  

* Ingyenes: Szoftveres licencek díjmentesek. 

* Saját licenc használata (BYOL): A szoftveres licencekre vonatkozó díjak közvetlenül a közzétevő és az ügyfél között kezelhetők. A Microsoft csak az Azure-infrastruktúra használati díjain halad át. (Csak Virtual Machines és Azure-alkalmazások.)

* Utólagos elszámolás: A szoftverlicenc-díjak a felhasznált Azure-infrastruktúra alapján óránkénti (vCPU) díjszabás szerint jelennek meg. Ez csak Virtual Machines és Azure-alkalmazásokra vonatkozik.

* • Előfizetés díjszabása: A szoftverlicenc-díj a havi vagy éves, ismétlődő díj, amely átalányként vagy-munkaállomásonkéntiként van feltüntetve. Ez csak az SaaS-alkalmazásokra és az Azure-alkalmazások – felügyelt alkalmazásokra vonatkozik.

* Ingyenes szoftver próbaverziója: A szoftveres licencek díja 30 vagy 90 nap.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ingyenes és saját licencek (BYOL) díjszabása

Az ingyenes vagy a saját licencű tranzakciós ajánlat közzététele esetén a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A lista és a próbaverzió közzétételi lehetőségeihez hasonlóan a közzétevő a szoftverlicenc-díjak 100%-át is megtartja. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Utólagos elszámolás és előfizetés (hely alapján) díjszabása

A WPay és az előfizetés díjszabása az utólagos elszámolású vagy előfizetési tranzakciós ajánlatok közzétételekor a Microsoft biztosítja a technológiát és szolgáltatásokat a szoftverlicenc-vásárlások, a visszaadások és a jóváírások feldolgozásához. Ebben a forgatókönyvben a közzétevő engedélyezi a Microsoft számára, hogy ügynökként működjön ezen célokból. A közzétevő lehetővé teszi a Microsoft számára, hogy elősegítse a szoftverlicencelési tranzakciót, miközben megtartja a megjelölését eladóként, szolgáltatóként, terjesztőként és licencelőként.

A Microsoft lehetővé teszi, hogy az ügyfelek a Microsoft kereskedelmi piactér és a kiadó végfelhasználói licencszerződésének feltételei és kikötései alapján közzétevő szoftvert rendeljenek, licencet és használjanak. A kiadóknak meg kell adniuk a végfelhasználói licencszerződést, vagy az ajánlat létrehozásakor a [szokásos szerződést](https://docs.microsoft.com/azure/marketplace/standard-contract) kell választaniuk.


### <a name="free-software-trials"></a>Ingyenes szoftveres próbaverziók

A Transact Publishing forgatókönyvek esetében a közzétevő 30 napig vagy 90 napig ingyenesen elérhetővé teheti a szoftverlicenc-t. Ez a diszkont képesség nem tartalmazza az Azure-infrastruktúra használatának költségeit, amelyet a partneri megoldás használata vezérel.

### <a name="private-offers"></a>Privát ajánlatok

Az ajánlatok típusai és a számlázási modellek használata mellett a kiadók egy privát ajánlatot is igényelhetnek, az egyeztetett, az alku-specifikus díjszabással vagy az egyéni konfigurációkkal együtt. A 3 Transact-közzétételi lehetőség mind a privát ajánlatokat támogatja.

Ez a lehetőség nagyobb vagy alacsonyabb díjszabást tesz lehetővé, mint a nyilvánosan elérhető ajánlat. A privát ajánlatokat kedvezményre lehet használni, vagy prémium ajánlatot adhat egy ajánlathoz. Egy vagy több ügyfél számára elérhetővé teheti az Azure-előfizetését az ajánlat szintjén.


### <a name="examples"></a>Példák

**Pay-As-You-Go** 

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

**Saját licenc használata (BYOL)**

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

**SaaS-alkalmazás előfizetése**

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
|A Microsoft a licencelési díj 80%-ában fizet <br> \*A Microsoft a licencelési díj 90%-át fizet minden minősített SaaS-alkalmazáshoz   |   $80,00/hó <br> \*$90,00/hó    |
|A Microsoft a licencelési díj 20%-át megtartja <br> \*A Microsoft minden minősített SaaS-alkalmazás esetében megőrzi a licencek költségeit.  |  $20,00/hó <br> \*$10,00     |

* **Csökkentett Piactéri szolgáltatási díj:** A kereskedelmi piactéren közzétett egyes SaaS-termékek esetében a Microsoft a piactér szolgáltatási díját 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra.  Ahhoz, hogy a termék megfelelő legyen, legalább az egyik terméket meg kell jelölnie a Microsoft számára az IP-címek közös értékesítésének vagy az IP-címek közös értékesítésének megfelelően. A csökkentett Piactéri szolgáltatási díj a hónapra való fogadásához a jogosultságnak legalább öt (5) munkanapon belül meg kell felelnie az adott naptári hónap vége előtt. A Marketplace szolgáltatási díja nem vonatkozik a kereskedelmi Piactéren keresztül elérhető virtuális gépekre, felügyelt alkalmazásokra vagy más termékekre.  Ez a csökkentett Piactéri szolgáltatási díj a Microsoft által a 2019 és a 2020. május 1. és a. június 30. között gyűjtött licencek díjai alapján lesz elérhető.  Ezt követően a Piactéri szolgáltatási díj visszaáll a normál mennyiségre.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Ügyfél-számlázás, fizetés, Számlázás és gyűjtemények

**Számlázás és fizetés**

A közzétevő használhatja az ügyfél előnyben részesített számlázási módszerét az előfizetés vagy a ÁTTÉRÉSHEZ szoftverlicenc-díjainak továbbítására.

**Nagyvállalati szerződés** 

Ha az ügyfél előnyben részesített számlázási módszere a Microsoft Nagyvállalati Szerződés, a szoftverlicenc-díjat a számlázási módszer alapján részletezett költségként kell elszámolni, az Azure-specifikus használati költségektől elkülönítve.

**Bankkártyák és havi számla** 

Az ügyfelek bankkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjat a Nagyvállalati Szerződés-forgatókönyvhöz hasonlóan számítjuk fel, amely az Azure-specifikus használati költségektől függ.

Ha például az ügyfél bankkártyát használ:

|Leírás    |    Date  |
|----------|----------|
|Rendelési időszak   | Augusztus 15., 2018-augusztus 30., 2018 |
|Befejezési időszak (hónap)   | Augusztus 30, 2018 |
|Számlázási dátum | Szeptember 1., 2018 |
|Ügyfél-fizetési dátum | Szeptember 1., 2018 |
|Letéti időszak (csak bankkártyák, 30 nap) | Szeptember 1., 2018 – szeptember 30, 2018 |
|Gyűjtési időszak kezdete | Szeptember 1., 2018 |
|Gyűjtési időszak vége (legfeljebb, 30 nap) | Szeptember 30., 2018 |
|Kifizetési számítás dátuma (havonta, 15-én) | Október 1, 2018 |
|Kifizetés dátuma | Október 15., 2018 |

Ha az ügyfél Nagyvállalati Szerződés használatával vásárol:

| Leírás |    Date  |
|----------|----------|
|Rendelési időszak | Augusztus 15., 2018-augusztus 30., 2018 |
|Befejezési időszak (negyedév) | Szeptember 30., 2018 |
|Számlázási dátum | Október 15., 2018 |
|Letéti időszak (csak bankkártyák, 30 nap) | n/a |
|Gyűjtési időszak kezdete | Október 15., 2018 |
|Gyűjtési időszak vége (maximum, 90 nap) | Jan. január 15., 2019 |
|Ügyfél-fizetési dátum | Dec. 30, 2018 |
|Kifizetési számítás dátuma (havonta, 15-én) | Jan. január 15., 2019 |
|Kifizetés dátuma | Február 15., 2019 |

**Ingyenes kreditek és pénzügyi kötelezettségvállalás** 

Egyes ügyfelek a Nagyvállalati Szerződés pénzügyi kötelezettségvállalásával, vagy az Azure-hoz való használatra az Azure-ban ingyenes krediteket választanak. Bár ezek a kreditek felhasználhatók az Azure-használat megfizetésére, nem használhatók a kiadói szoftverlicenc-díjakért.

**Számlázás és gyűjtemények** 

A kiadói szoftverlicenc számlázása az ügyfél által kiválasztott számlázási módszer használatával történik, és a számlázási ütemtervet követi. A Nagyvállalati Szerződés nélküli ügyfelek számlázása havonta történik a piactér szoftverlicenc esetében. A Nagyvállalati Szerződés rendelkező ügyfelekért havonta kell fizetnie egy negyedévente bemutatott számlán keresztül.

Ha az előfizetés vagy az utólagos elszámolású díjszabási modell van kiválasztva, a Microsoft a közzétevő ügynöke működik, és felelős a számlázás, a fizetés és a gyűjtés minden vonatkozásában.

### <a name="publisher-payout-and-reporting"></a>Kiadói kifizetés és jelentéskészítés

* A Microsoft által ügynökként gyűjtött licencelési díjakat 20%-os tranzakciós díj terheli, kivéve, ha másként van megadva, és a kiadói kifizetés időpontjában le vannak vonva.

* Az ügyfelek általában a Nagyvállalati Szerződés vagy egy hitelkártya-alapú utólagos elszámolású szerződés használatával vásárolnak. A szerződés típusa határozza meg a számlázást, a számlázást, a gyűjtést és a kifizetés időzítését.

>[!NOTE] 
>A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Cloud Partner Portal vagy Analytics szakaszának elemzések szakaszán keresztül érhető el.

#### <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

További információkat és jogi szabályzatokat a kiadói [szerződésben](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) talál (a Cloud Partner Portalban érhető el).

Ha segítséget szeretne kapni a számlázási kérdésekkel kapcsolatban, forduljon a [kereskedelmi piactér kiadója támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-követelmények

Ebben a szakaszban a különböző típusú ajánlatok Transact-követelményeit tárgyaljuk.

### <a name="requirements-for-all-offer-types"></a>Az összes ajánlat típusára vonatkozó követelmények

- A Transact közzétételi lehetőséghez Microsoft-fiók és pénzügyi információk szükségesek, az ajánlat díjszabási modelljétől függetlenül.
- A kötelező pénzügyi információk közé tartozik a kifizetési fiók és az adó profil.

A fiókok beállításával kapcsolatos további információkért lásd: [a partner Center-fiók kezelése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Az egyes ajánlatok típusaira vonatkozó követelmények

A Transact Publishing beállítás csak a következő Piactéri ajánlatok esetében használható: 

**Virtuális gép** 

Válassza az ingyenes, a saját licences vagy az utólagos elszámolású modelleket, valamint az ajánlat szintjén meghatározott SKU-t. Az ügyfél Azure-számláján a Microsoft a mögöttes Azure-infrastruktúra díjaitól függetlenül bemutatja a kiadói szoftverlicenc-díjat. Az Azure-infrastruktúra díjait a közzétevő szoftver használja.

**Azure-alkalmazások: Megoldás sablonja vagy felügyelt alkalmazás** 

Egy vagy több virtuális gépet kell kiépíteni, és a virtuális gép díjszabásának összegével kell lekérnie. Egyetlen csomagban felügyelt alkalmazások esetén a virtuális gépek díjszabása helyett egy átalánydíjas havi előfizetés is kiválasztható a díjszabási modellként. Bizonyos esetekben az Azure-infrastruktúra használati díjait a rendszer külön adja át az ügyfélnek a szoftverlicenc-díjaktól, de ugyanarra a számlázási utasításra. Ha azonban a felügyelt alkalmazáshoz az ISV-infrastruktúra díjait konfigurálja, az Azure-erőforrások számlázása a kiadóra történik, és az ügyfél egy átalánydíjas díjat kap, amely magában foglalja az infrastruktúra, a szoftverlicenc és a felügyeleti szolgáltatások költségeit.

## <a name="next-steps"></a>További lépések

* Tekintse át a jogosult-e a kijelölés és a konfiguráció az Ön ajánlatát véglegesítéséhez ajánlat típushoz című által a közzétételi beállítások.
* Tekintse át a közzétételi minták storefront-példák hogyan megoldását képez le egy ajánlat típusát és a konfiguráció alapján.
