---
title: Marketplace kereskedelmi tranzakciós képességek és megfontolások | Azure
description: Ez a cikk ismerteti a Transact árképzési, számlázási, számlázási és kifizetési szempontok egy ajánlat típus.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: dsindona
ms.openlocfilehash: de93a3c48e0b0aceb447e54e11190f487d1aa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279827"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>A kereskedelmi piactéri tranzakciókkal kapcsolatos képességek és szempontok

Ez a cikk a következő, kereskedelemmel kapcsolatos témaköröket ismerteti a kereskedelmi piactérszámára

* Piactér közzétételi beállításai
* Általános áttekintés lebonyolítása
* Tranzakciós számlázási modellek
* Tranzakciós követelmények

## <a name="marketplace-publishing-options"></a>Piactér közzétételi beállításai

A következő közzétételi lehetőségek érhetők el a kereskedelmi piactér közzétevői számára.

### <a name="list--trial-publishing-options"></a>A próbaverziós közzétételi lehetőségek & listája

A kiadók promóciós és felhasználói akvizíciós célokra használhatják a lista-, próba- és BYOL-közzétételi lehetőségeket. Ezekkel a beállításokkal a Microsoft nem vesz részt közvetlenül a közzétevő szoftverlicenc-tranzakcióiban, és nincs társított tranzakciós díj. A kiadók felelősek a szoftverlicenc-tranzakció minden aspektusának támogatásáért, beleértve, de nem kizárólagosan: a megrendelést, a teljesítést, a mérést, a számlázást, a számlázást, a fizetést és a beszedést. A lista- és próbaverzióközzétételi lehetőségekkel a kiadók a kiadói szoftverek licencelési díjainak 100%-át az ügyféltől tartják meg. 

### <a name="transact-publishing-option"></a>Közzétételi lehetőség tranzakciója

A lista- és próbaverzióközzétételi beállításokon kívül a tranzakciós közzétételi lehetőség is elérhető a közzétevők számára. Ez kihasználja a Microsoft globálisan elérhető kereskedelmi lehetőségeit, és lehetővé teszi a Microsoft számára, hogy a közzétevő nevében felhőalapú piactéri tranzakciókat tároljon.

## <a name="transact-general-overview"></a>Általános áttekintés lebonyolítása

A transact közzétételi lehetőség használatakor a Microsoft engedélyezi a harmadik féltől származó szoftverek értékesítését, valamint bizonyos ajánlattípusok üzembe helyezését az ügyfél Azure-előfizetésében. A közzétevőnek figyelembe kell vennie az infrastruktúra-díjak számlázását és a kiadó saját szoftverlicencelési díjait a számlázási modell és az ajánlattípus kiválasztásakor.

A Transact közzétételi lehetőség jelenleg támogatott a következő ajánlattípusok: Virtuális gépek, Azure-alkalmazások és SaaS-alkalmazások.


![[Nagyvállalati ajánlatok tranzakciója az Azure Marketplace-en]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

**Virtuális gépekhez és Azure-alkalmazásokhoz**

A virtuális gépek és az Azure-alkalmazások esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetése számítja fel.  Az infrastruktúra használati díjai nak ára és bemutatása a szoftverszolgáltató nak az ügyfél számláján szereplő licencdíjaitól elkülönítve történik.

**SaaS-alkalmazások esetén**

SaaS-alkalmazások esetén a közzétevőnek egyetlen költségelemként kell figyelembe vennie az Azure-infrastruktúra használati díjait és a szoftverlicencelési díjakat.  Ez az ügyfél számára átalánydíjként jelenik meg. Az Azure-infrastruktúra-használat kezelése és számlázása közvetlenül a partner.  A tényleges infrastruktúra-használati díjakat az ügyfél nem látja.  A kiadók általában úgy döntenek, hogy az Azure-infrastruktúra használati díjait a szoftverlicenc-díjba csomagolják.  A szoftverlicencelési díjak nem mérőszámok vagy felhasználásalapúak.

## <a name="transact-billing-models"></a>Tranzakciós számlázási modellek

A használt tranzakciós opciótól függően a közzétevő szoftverlicenc-díjai a következőképpen jelenhetnek meg:  

* Ingyenes: A szoftverlicencek díjmentesek. 

* Hozza magával saját licencét (BYOL): A szoftverlicencek minden vonatkozó díját közvetlenül a közzétevő és az ügyfél kezeli. A Microsoft csak az Azure infrastruktúra használati díjait továbbítja. (Csak virtuális gépek és Azure-alkalmazások esetén.)

* Használatalapú fizetés: A szoftverlicenc-díjak óránkénti, magonkénti (vCPU) díjszabásként jelennek meg az Azure-infrastruktúrán alapuló díjszabás alapján. Ez csak a virtuális gépek re és az Azure-alkalmazások.

* Előfizetési díjak: A szoftverlicenc-díjak havi vagy éves, ismétlődő díjként kerülnek bemutatásra, átalánydíjként vagy ülőhelyenként. Ez csak a SaaS-alkalmazásokra és az Azure-alkalmazásokra vonatkozik – Felügyelt alkalmazások.

* Ingyenes szoftverpróba: 30 vagy 90 napig ingyenes a szoftverlicencek díja.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ingyenes és bring-your-own-license (BYOL) árképzés

Az ingyenes vagy saját licencre vonatkozó licencajánlat közzétételekor a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A lista- és próbaverziós közzétételi lehetőségekhez hasonlóan a közzétevő a szoftverlicenc-díjak 100%-át megtartja. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Kiosztó-kirovó és előfizetési (webhelyalapú) díjszabás

A felosztó-kirovó vagy előfizetéses tranzakciós ajánlat közzétételekor a Microsoft biztosítja a szoftverlicenc-vásárlások, visszaküldések és visszaterhelések feldolgozásához szükséges technológiát és szolgáltatásokat. Ebben az esetben a közzétevő felhatalmazza a Microsoftot, hogy ilyen célból ügynökként járjon el. A kiadó lehetővé teszi a Microsoft számára, hogy megkönnyítse a szoftverlicencelési tranzakciót, miközben megtartja az eladóként, szolgáltatóként, forgalmazóként és licencadóként való kijelölésüket.

A Microsoft lehetővé teszi az ügyfelek számára, hogy megrendeljék, licenceljék és használják a kiadói szoftvereket, a Microsoft kereskedelmi piacterének és a kiadó végfelhasználói licencszerződésének feltételeinek megfelelően. A közzétevőknek meg kell adniuk végfelhasználói licencszerződésüket, vagy ki kell választaniuk az [általános szerződést](https://docs.microsoft.com/azure/marketplace/standard-contract) az ajánlat létrehozásakor.


### <a name="free-software-trials"></a>Ingyenes szoftverkísérletek

A közzétételi forgatókönyvek tranzakciós forgatókönyvek esetén a közzétevő 30 vagy 90 napig ingyenesen elérhetővé teheti a szoftverlicencet. Ez a diszkontálási képesség nem tartalmazza az Azure-infrastruktúra használatának költségét, amelyet a partnermegoldás használata hajt.

### <a name="private-offers"></a>Privát ajánlatok

Az ajánlattípusok és számlázási modellek használata mellett a megjelenítők privát ajánlatot is köthetnek, amely egyeztetett, ügyletspecifikus díjszabással vagy egyéni konfigurációkkal is kiegészítve. A privát ajánlatokat mind a 3 tranzakciós közzétételi lehetőség támogatja.

Ez a beállítás magasabb vagy alacsonyabb árszabást tesz lehetővé, mint a nyilvánosan elérhető ajánlat. A privát ajánlatok at lehet használni a kedvezmény, vagy adjunk hozzá egy prémium egy ajánlatot. Privát ajánlatok at lehet elérhetővé tenni egy vagy több ügyfél által fehér felsorolja az Azure-előfizetés az ajánlat szintjén.


### <a name="examples"></a>Példák

**Használatalapú fizetés** 

* Ha engedélyezi a kiosztó-kiosztó nad-be beállítást, akkor a következő költségstruktúrával rendelkezik.

|A licenc költsége  | $1.00 óránként  |
|---------|---------|
|Az Azure használati költsége (D1/1-Core)    |   $0.14 óránként     |
|*Az ügyfél számláját a Microsoft számlája*    |  *$1.14 óránként*       |

* Ebben a forgatókönyvben a Microsoft 1,14 USD/óra díjat számláz a közzétett virtuális géplemezkép használatáért.

|Microsoft-számlák  | $1.14 óránként  |
|---------|---------|
|A Microsoft a licencköltség 80%-át fizeti|   $0.80 óránként     |
|A Microsoft megtartja a licencköltség 20%-át  |  $0.20 óránként       |
|A Microsoft megtartja az Azure használati költségének 100%-át | $0.14 óránként |

**Hozd a saját licenc (BYOL)**

* Ha engedélyezi a BYOL beállítást, akkor a következő költségstruktúrával rendelkezik.

|A licenc költsége  | Az Ön által megtárgyalt és számlázott licencdíj  |
|---------|---------|
|Az Azure használati költsége (D1/1-Core)    |   $0.14 óránként     |
|*Az ügyfél számláját a Microsoft számlája*    |  *$0.14 óránként*       |

* Ebben a forgatókönyvben a Microsoft 0,14 USD/óra díjat számláz a közzétett virtuális géplemezkép használatához.

|Microsoft-számlák  | $0.14 óránként  |
|---------|---------|
|A Microsoft megtartja az Azure használati költségét    |   $0.14 óránként     |
|A Microsoft megtartja a licencköltség 0%-át   |  $0.00 óránként       |

**SaaS Alkalmazás-előfizetés**

Ezt a lehetőséget úgy kell beállítani, hogy a Microsofton keresztül értékesítsen, és díjszabása átalánydíjas, vagy felhasználónként havi vagy éves alapon történhet.
*    Ha engedélyezi az Eladás a Microsofton keresztül beállítást egy SaaS-ajánlathoz, akkor a következő költségstruktúrával rendelkezik.

|A licenc költsége       | $100.00 havonta  |
|--------------|---------|
|Az Azure használati költsége (D1/1-Core)    | Közvetlenül a kiadónak számlázva, nem az ügyfélnek |
|*Az ügyfél számláját a Microsoft számlája*    |  *100,00 $ havonta (megjegyzés: kiadó figyelembe kell venni minden felmerült vagy átmenő infrastruktúra költségeit a licencdíj)*  |

* Ebben az esetben a Microsoft 100,00 USD-t számláz a szoftverlicencért, és 80,00 USD-t fizet ki a közzétevőnek.
* Azok a partnerek, akik kvalifikálták magukat a Csökkentett piactér szolgáltatási díjára, 2019 májusától 2020 júniusáig csökkentett tranzakciós díjat kapnak a SaaS-ajánlatokon. Ebben az esetben a Microsoft 100,00 USD-t számláz a szoftverlicencért, és 90,00 USD-t fizet ki a közzétevőnek.

|Microsoft-számlák  | $100.00 havonta  |
|---------|---------|
|A Microsoft a licencköltség 80%-át fizeti <br> \*A Microsoft a licencköltség 90%-át fizeti önnek bármely minősített SaaS-alkalmazásért   |   $80.00 havonta <br> \*$90.00 havonta    |
|A Microsoft megtartja a licencköltség 20%-át <br> \*A Microsoft megtartja a licences költségének 10%-át bármely minősített SaaS-alkalmazás esetében.  |  $20.00 havonta <br> \*$10.00     |

* **Csökkentett piactéri szolgáltatási díj:** Egyes SaaS-termékek esetében, amelyeket Ön a kereskedelmi piactéren tesz közzé, a Microsoft a Marketplace szolgáltatási díját 20%-ról (a Microsoft kiadói szerződésében leírtak szerint) 10%-ra csökkenti.  Ahhoz, hogy a Termék jogosult legyen, a Microsoftnak legalább az egyik termékét az IP-társértékesítésre kész vagy az IP-társértékesítésre vonatkozó prioritásként kell megjelölnie. Ahhoz, hogy a havi kedvezményes Piactér-szolgáltatási díjat megkapd, a jogosultságnak legalább öt (5) munkanapon belül teljesítenie kell az előző naptári hónap vége előtt. A csökkentett Piactér-szolgáltatási díj nem vonatkozik a virtuális gépekre, a felügyelt alkalmazásokra vagy a kereskedelmi piactéren elérhetővé tett egyéb termékekre.  Ez a csökkentett piactéri szolgáltatási díj minősített ajánlatokszámára lesz elérhető, a Microsoft 2019.  Ezt követően a Marketplace szolgáltatási díj a normál összeghez fog visszatérni.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Vevői számlázás, fizetés, számlázás és beszedés

**Számlázás és kifizetés**

A Publisher az ügyfél által előnyben részesített számlázási módszert használhatja előfizetési vagy PAYGO szoftverlicenc-díjak leszállításához.

**Nagyvállalati szerződés** 

Ha az ügyfél előnyben részesített számlázási módszere a Microsoft nagyvállalati szerződése, akkor a szoftverlicenc-díjakat ezzel a számlázási módszerrel számlázunk, tételes költségként, az Azure-specifikus használati költségektől elkülönítve.

**Hitelkártyák és havi számla** 

Az ügyfelek hitelkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjakat a nagyvállalati szerződés forgatókönyvéhez hasonlóan, az Azure-specifikus használati költségektől elkülönítve számlázunk.

Ha például a vevő hitelkártyával vásárol:

|Leírás    |    Dátum  |
|----------|----------|
|Rendelési időszak   | Aug 15, 2018 - Aug 30, 2018 |
|Időszak vége (hónap)   | 2018. augusztus 30.Aug 30, 2018 |
|Számlázási dátum | 2018. szeptember 1. |
|Vevőfizetési dátum | 2018. szeptember 1. |
|Letéti időszak (csak hitelkártyák, 30 nap) | 2018. szeptember 1. |
|Begyűjtési időszak kezdete | 2018. szeptember 1. |
|A begyűjtési időszak vége (maximum 30 nap) | 2018. szeptember 30. |
|Kifizetés számítási dátuma (havonta 15-én) | 2018. október 1., 2018. |
|Kifizetés dátuma | 2018. október 15. |

Ha a vevő nagyvállalati szerződés alapján vásárol:

| Leírás |    Dátum  |
|----------|----------|
|Rendelési időszak | Aug 15, 2018 - Aug 30, 2018 |
|Időszak vége (negyedév) | 2018. szeptember 30. |
|Számlázási dátum | 2018. október 15. |
|Letéti időszak (csak hitelkártyák, 30 nap) | n/a |
|Begyűjtési időszak kezdete | 2018. október 15. |
|A begyűjtési időszak vége (maximum, 90 nap) | 2019. január 15.Jan 15, 2019 |
|Vevőfizetési dátum | 2018. december 30., dec. |
|Kifizetés számítási dátuma (havonta 15-én) | 2019. január 15.Jan 15, 2019 |
|Kifizetés dátuma | 2019. február 15.Feb 15, 2019 |

**Ingyenes hitelek és monetáris kötelezettségvállalás** 

Egyes ügyfelek úgy döntenek, hogy a nagyvállalati szerződésben szereplő pénzügyi kötelezettségvállalással előre kifizetik az Azure-t, vagy ingyenes krediteket kaptak az Azure-ral való használatra. Bár ezek a kreditek azure-használat kifizetésére használhatók fel, nem használhatók fel a kiadói szoftverek licencdíjainak kifizetésére.

**Számlázás és beszedés** 

A Publisher szoftverlicenc-számlázása az ügyfél által kiválasztott számlázási módszerrel jelenik meg, és a számlázási ütemtervet követi. A nagyvállalati szerződéssel nem rendelkező ügyfelek nek havonta kell fizetniük a marketplace-alapú szoftverlicencekért. A nagyvállalati szerződéssel rendelkező ügyfelek számlázása havonta történik egy negyedévente bemutatott számlán keresztül.

Ha előfizetéses vagy csak annyidíjszabási modellt választ ki, a Microsoft a közzétevő ügynökeként jár el, és a számlázás, a fizetés és a beszedés minden aspektusáért felelős.

### <a name="publisher-payout-and-reporting"></a>A kiadók kifizetése és jelentése

* A Microsoft által ügynökként beszedett szoftverlicencelési díjak20%-os tranzakciós díjat vonnak maga után, kivéve, ha másként rendelkeznek, és azadatokat a megjelenítő kifizetésének időpontjában vonják le.

* Az ügyfelek általában a nagyvállalati szerződés vagy a hitelkártyával engedélyezett használatalapú fizetéssel vásárolnak. A szerződés típusa határozza meg a számlázást, a számlázást, a beszedést és a kifizetés időzítését.

>[!NOTE] 
>A tranzakciós közzétételi lehetőségösszes jelentéskészítési és elemzési információja elérhető a Partnerközpont Felhőpartneri portál vagy Analytics szakaszának Elemzési részében.

#### <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

További információkért és jogi szabályzatokért tekintse meg a [Kiadói szerződést](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (amely elérhető a Felhőpartneri portálon).

Ha segítségre van szüksége a számlázással kapcsolatos kérdésekkel kapcsolatban, forduljon a [kereskedelmi piactér kiadói támogatási csoportjához.](https://aka.ms/marketplacepublishersupport)

## <a name="transact-requirements"></a>Tranzakciós követelmények

A különböző ajánlattípusok tranzakciós követelményeit ebben a szakaszban ismerteti.

### <a name="requirements-for-all-offer-types"></a>Az összes ajánlattípusra vonatkozó követelmények

- A Microsoft-fiók és a pénzügyi adatok szükségesek a tranzakciós közzétételi lehetőség, függetlenül az ajánlat árképzési modell.
- A kötelező pénzügyi adatok közé tartozik a kifizetési számla és az adózási profil.

A fiókok beállításáról további információt a [Partnerközpont-fiók kezelése című témakörben talál.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)


### <a name="requirements-for-specific-offer-types"></a>Az egyes ajánlattípusokra vonatkozó követelmények

A tranzakciós közzétételi lehetőség csak a következő piactéri ajánlattípusokkal használható: 

**Virtuális gép** 

Válasszon az ingyenes, saját licenccel rendelkező vagy felosztó-kirovó díjszabási modellek közül, és jelenjenek meg az ajánlat szintjén meghatározott suk-ként. Az ügyfél Azure-számláján a Microsoft a kiadószoftver licencdíjait az alapul szolgáló Azure-infrastruktúra díjaktól elkülönítve mutatja be. Az Azure-infrastruktúra díjait a kiadói szoftver használata határozza meg.

**Azure-alkalmazások: Megoldássablon vagy felügyelt alkalmazás** 

Egy vagy több virtuális gépet kell kiépítenie, és végig kell húznia a virtuális gép díjszabásának összegét. Felügyelt alkalmazások egyetlen csomag, egy átalánydíjas havi előfizetés választható, mint a díjszabási modell helyett a virtuális gép díjszabása. Bizonyos esetekben az Azure-infrastruktúra használati díjai a szoftverlicenc-díjaktól elkülönítve, de ugyanazon a kimutatáson kerülnek átadásra az ügyfélnek. Ha azonban egy felügyelt alkalmazásajánlatot konfigurál az ISV-infrastruktúra díjaihoz, az Azure-erőforrásokat a közzétevőnek számlázzák, és az ügyfél átalánydíjat kap, amely tartalmazza az infrastruktúra, a szoftverlicencek és a felügyeleti szolgáltatások költségét.

## <a name="next-steps"></a>További lépések

* Tekintse át a jogosultsági követelményeket a közzétételi lehetőségek ajánlattípus szerint szakaszban az ajánlat kiválasztásának és konfigurációjának véglegesítéséhez.
* Tekintse át a közzétételi minták at kirakat példákat, hogyan a megoldás leképezése egy ajánlat típusát és konfigurációját.
