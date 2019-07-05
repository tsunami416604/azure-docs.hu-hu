---
title: Marketplace-en kereskedelmi ügylet képességek és szempontok |} Az Azure
description: Ez a cikk ismerteti az ajánlattípusra Transact díjszabás, Számlázás, számlázás és küldenie jóváhagyásra cége kifizetési szempontjai.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: d266b314f19979578b7e7b8de4e7a7090200c9d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445454"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Kereskedelmi marketplace tranzakciós képességek és szempontok

Ez a cikk ismerteti a kereskedelmi Marketplace-en a következő kereskedelmi kapcsolatos témakörök

* Piactéri közzétételi lehetőség
* Általános áttekintés Transact
* A Transact-számlázási modellek
* A Transact-követelmények

## <a name="marketplace-publishing-options"></a>Piactéri közzétételi lehetőség

A következő közzétételi beállítások kereskedelmi marketplace kiadóknak érhetők el.

### <a name="list--trial-publishing-options"></a>Listán & próbaverziós közzétételi beállítások

Közzétevők használhatják a listából, a próbaverziót, és a közzétételi beállítások a BYOL promóciós és felhasználói megszerzése céljából. Ezekkel a beállításokkal a Microsoft nem közvetlenül részt vesznek a kiadó szoftver licenc tranzakciókat, és ott nem társított tranzakciós díj nélkül. A kiadók felelősek a szoftver licenc tranzakció, de nem kizárólagosan beleértve minden aspektusát támogatásához: order, teljesítése, mérési, Számlázás, Számlázás, fizetési és gyűjteményt. A listában, és próba közzétételi beállítások a közzétevők 100 %-a közzétevő szoftver licencelési díjak az ügyféltől összegyűjtött tartsa. 

### <a name="transact-publishing-option"></a>A Transact-közzétételi lehetőség

A lista és próba közzétételi beállítások mellett a transact-közzétételi lehetőség kiadóknak érhető el. Ez a kihasználja a Microsoft globálisan elérhető kereskedelmi funkcióit, és lehetővé teszi a Microsoft a gazdagép felhőbeli piactér tranzakciók nevében a közzétevő.

## <a name="transact-general-overview"></a>Általános áttekintés Transact

A transact-közzétételi lehetőség használatakor a Microsoft lehetővé teszi a harmadik féltől származó szoftverek értékesítésére, és bizonyos típusú Azure-előfizetéshez az ügyfél telepítését. A közzétevő figyelembe kell vennie a számlázás az infrastruktúra-díjak, és a közzétevő a saját szoftverfrissítési licencelési díjakat, amikor kiválasztja a számlázási modell, és a csomag típusa.

A Transact-közzétételi lehetőség jelenleg a következő típusú támogatja: Virtuális gépek, az Azure-alkalmazások és SaaS-alkalmazásokhoz.


![[Vállalati vevő foglalkozik, az Azure Marketplace-en]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>A számlázás az infrastruktúrára fordítandó költségeket.

**A virtuális gépek és az Azure-alkalmazások**

A virtuális gépek és Azure-alkalmazások az ügyfél Azure-előfizetéshez az Azure-infrastruktúra használati díjakat számítjuk fel.  Infrastruktúra-használati díjak díjszabása és a szoftver gyártójának licencelési díjak az ügyfél számlán külön-külön jelenik meg.

**Az SaaS-alkalmazásokhoz**

Az SaaS-alkalmazásokhoz a közzétevő kell vennie az Azure-infrastruktúra használati díjak és szoftver-licencelési díjak egyetlen költség elemként.  Azt jelzi, az ügyfél kezdeti. Az Azure-infrastruktúra használati felügyelt, és közvetlenül a partnernek számlázzuk.  Az ügyfél nem fogja látni a tényleges infrastruktúra használati díjak.  A kiadók általában a szoftver licenc díjszabása Azure-infrastruktúra használati díjak kötegeléséhez tilthatók le.  Szoftver-licencelési díjak nem díjköteles, vagy használatalapú.

## <a name="transact-billing-models"></a>A Transact-számlázási modellek

Attól függően, a tranzakciós beállítást használja a kiadói szoftverlicencdíj jelenítheti meg a következő:  

* Ingyenes: Szoftverlicencek számára díjmentes. 

* Hozott licences (BYOL): A vonatkozó díjakat szoftverlicencekre vonatkozó kezelhetők közvetlenül a közzétevő és az ügyfél között. A Microsoft Azure-infrastruktúra használati díjak csak áthalad. (Virtuális gépek és az Azure-alkalmazások csak.)

* Utólagos elszámolás: Egy óránkénti, magonként (vCPU) díjszabása alapján az Azure-infrastruktúra használt szoftverlicencdíj jelennek meg. Ez csak a virtuális gépek és Azure-alkalmazások vonatkozik.

* • Előfizetés díjszabása: Szoftverlicencdíj jelennek meg a havi vagy éves, ismétlődő egy alapján, átalánydíjjal történik, vagy munkaállomásonként üzenetként számítunk fel díjat. Ez csak érvényes SaaS-alkalmazások és az Azure-alkalmazások – felügyelt alkalmazás.

* A szoftver ingyenes próbaverziója: Díjmentesen szoftverlicencek 30 napon vagy 90 napos időszakban.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ingyenes, és bring-your-saját licenc (használata BYOL) díjszabása

Egy ingyenes vagy bring-your-saját licenc tranzakció ajánlat közzétételekor a Microsoft nem a szoftverlicencdíj az értékesítési tranzakció elősegítésében szerepet játszanak. A listából, és próba közzétételi beállítások, például a közzétevő tartja szoftverlicencdíj 100 %-ban. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Használatalapú fizetés és az előfizetés díjszabása (webhely-alapú)

WPay – mint-akkor-go és előfizetés díjszabása a használatalapú fizetés vagy előfizetés tranzakció ajánlat közzététele, a Microsoft biztosít a technológia és feldolgozni a szoftverlicenc-szolgáltatások vásárlása, adja vissza, és költséghelyi elszámolási műveleteket. Ebben a forgatókönyvben a közzétevő felhatalmazza a Microsoftot az ügynök névtereként ezekből a célokból. A közzétevő lehetővé teszi a Microsoft szoftverlicenc-tranzakció, miközben továbbra is az értékesítői, a szolgáltató, a terjesztőn és a kiszolgálólicenc megjelölésüknek megkönnyítése érdekében.

A Microsoft lehetővé teszi ügyfeleink számára sorrendben, a licenc és a közzétevő szoftver használata, hogy a Microsoft kereskedelmi Marketplace-en és a kiadó végfelhasználói licencszerződés feltételeit. A kiadók kell adja meg a végfelhasználói licencszerződés, vagy válassza ki a [általános szerződési](https://docs.microsoft.com/azure/marketplace/standard-contract) az ajánlat létrehozásakor.


### <a name="free-software-trials"></a>Ingyenes próbaverzió

A közzétételi forgatókönyvek transact, a közzétevő teheti a szoftver licenc érhető el ingyenes 30 napos időszakban vagy 90 nap. Ez a funkció engedmény nem tartalmaz határozzák meg a partneri megoldás használata Azure-infrastruktúra használati költségeinek.

### <a name="private-offers"></a>Saját ajánlatok

Használata mellett kínálnak a típusok és számlázási modellek, azok megfelelő bevételi lehetőségekként egy ajánlatot, közzétevők licencprogramot egy privát, kész, de az egyeztetett, az ügylet-specifikus díjszabás, vagy egyéni konfigurációk ajánlat. Saját ajánlatok által az összes támogatott 3 transact közzétételi beállítások.

Ez a beállítás lehetővé teszi, hogy magasabb vagy alacsonyabb díjszabás, mint a nyilvánosan elérhető ajánlatot. Saját ajánlatok kedvezményes segítségével, vagy adjon hozzá egy prémium. Saját ajánlatok listázása az Azure-előfizetésében az ajánlat szintjén fehér által az ügyfelek egy vagy több számára elérhetővé tehetők.


### <a name="examples"></a>Példák

**Pay-As-You-Go** 

* Ha engedélyezi a használatalapú fizetés a beállítást, akkor rendelkezik az alábbi struktúra költség.

|A licenc költsége  | 1,00 $ / óra  |
|---------|---------|
|Azure-használat költség (D1/1-mag)    |   0\.14 $ / óra     |
|*A Microsoft számlázzuk*    |  *1.14 $ / óra*       |

* Ebben a forgatókönyvben a Microsoft keresztül számláz az adott 1.14 $ óránkénti használatáért a közzétett Virtuálisgép-lemezkép.

|A Microsoft számlák  | 1\.14 $ / óra  |
|---------|---------|
|A Microsoft fizet 80 %-licenc költségei|   0,80 $ / óra     |
|A Microsoft tartja a 20 %-licenc költségei  |  0,20 $ / óra       |
|A Microsoft 100 %-a az Azure használati költségeinek tartja | 0\.14 $ / óra |

**Hozott licences (BYOL)**

* Ha engedélyezi a BYOL-beállítást, akkor rendelkezik az alábbi struktúra költség.

|A licenc költsége  | Egyeztetett és az Ön által számlázható licencdíját  |
|---------|---------|
|Azure-használat költség (D1/1-mag)    |   0\.14 $ / óra     |
|*A Microsoft számlázzuk*    |  *0.14 $ / óra*       |

* Ebben a forgatókönyvben a Microsoft keresztül számláz az adott $0.14 óránkénti használatáért a közzétett Virtuálisgép-lemezkép.

|A Microsoft számlák  | 0\.14 $ / óra  |
|---------|---------|
|A Microsoft Azure használati költségeinek tartja    |   0\.14 $ / óra     |
|A Microsoft tartja a 0 %-licenc költségei   |  0,00 USD / óra       |

**SaaS-alkalmazás-előfizetés**

Ezzel a beállítással kell konfigurálni, hogy a Microsoft keresztül, és átalánydíjat vagy havi vagy éves, felhasználónkénti ára.
• Ha engedélyezi az értékesítési keresztül egy SaaS-ajánlat a Microsoft lehetőséget, majd az alábbi költség struktúrával rendelkezik.

|A licenc költsége       | 100,00 $ / hó  |
|--------------|---------|
|Azure-használat költség (D1/1-mag)    | Közvetlenül a közzétevő, az ügyfél nem számítunk fel |
|*A Microsoft számlázzuk*    |  *100,00 $ / hó (Megjegyzés: közzétevő kell vennie az licencdíját felmerült vagy csatlakoztatott infrastruktúra költségeket)*  |

* Ebben a forgatókönyvben a Microsoft keresztül számláz az adott 100,00 dollár a szoftver licencét, és a közzétevőhöz $80,00 ki fizet.
* Partnereinkről, akik rendelkeznek a csökkentett Piactéri szolgáltatási díj a május 2019 június 2020-ig kínál a SaaS-csökkentett tranzakciós díj fog megjelenni. Ebben a forgatókönyvben a Microsoft keresztül számláz az adott 100,00 dollár a szoftver licencét, és a közzétevőhöz $90.00 ki fizet.

|A Microsoft számlák  | 100,00 $ / hó  |
|---------|---------|
|A Microsoft fizet 80 %-licenc költségei <br> \* A Microsoft fizet, 90 %-licenc költségei minden minősített SaaS-alkalmazásokhoz   |   80,00 $ / hó <br> \* 90.00 $ / hó    |
|A Microsoft tartja a 20 %-licenc költségei <br> \* A Microsoft biztosítja, hogy 10 %-a-licenc költségei minden minősített SaaS-alkalmazásokhoz.  |  20.00 $ / hó <br> \* $10.00     |

* **Csökkentett Piactéri szolgáltatási díj:** Az egyes SaaS-termékek, a kereskedelmi Marketplace-en közzétett, a Microsoft csökkenti a Piactéri szolgáltatási díj a 20 %-os (a Microsoft kiadói szerződésében leírt) 10 %-os.  Ahhoz, hogy ahhoz, hogy a termék, a termékek közül legalább ki kell jelölni a Microsoft által szerint IP közös értékesítési készen áll, vagy közös értékesítési IP előnyt élvez. A csökkentett Piactéri szolgáltatási díj kap a hónap, jogosult-e legalább öt (5) munkanapon naptári hónap vége előtt kell teljesülnie. Csökkentett díj nem vonatkozik a virtuális gépek, felügyelt alkalmazásokban vagy a kereskedelmi piactéren keresztül elérhetővé tett bármilyen egyéb termékek, a Piactéri szolgáltatás.  A csökkentett Piactéri szolgáltatási díj minősített ajánlatokra-el 2019. május 1. és 2020. június 30. között a Microsoft által gyűjtött licenc díjak.  Ezt követően a Piactéri szolgáltatási díj visszatér a szokásos összeg.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Ügyfélszámlázás, a fizetés, a számlázásra és a gyűjtemények

**Számlázási és fizetési**

Közzétevő segítségével az ügyfél előnyben részesített módszer számlázási előfizetés vagy ÁTTÉRÉSHEZ szoftverlicencdíj.

**Nagyvállalati Szerződés** 

Ha az ügyfél előnyben részesített számlázási módszert a Microsoft nagyvállalati szerződés, a szoftverlicencdíj díját az Ez metódus számlázásos részletezett költség, a Azure-ra vonatkozó használati díjak elkülönítve.

**Hitelkártya és a havi számla** 

Ügyfelek is fizethet, hitelkártya és a egy havi számla. Ebben az esetben a szoftverlicencdíj, lesznek számlázva a nagyvállalati szerződés forgatókönyv, mint részletezett költség, a Azure-ra vonatkozó használati díjak elkülönítve.

Például ha a vásárló megvásárolhatja a hitelkártya használatával:

|Leírás    |    Dátum  |
|----------|----------|
|Rendelés időszak   | Augusztus 15, 2018-2018. augusztus 30. |
|Előfizetési időszak vége (hónap)   | 2018. augusztus 30. |
|A Számlázás dátuma | 2018. Szeptembertől 1. |
|Ügyfél fizetési dátuma | 2018. Szeptembertől 1. |
|Így letétbe helyezheti a időszak (hitelkártyák csak 30 napig) | Szeptembertől 1, 2018 – 2018. Szeptembertől 30. |
|Gyűjtési időszak kezdete | 2018. Szeptembertől 1. |
|Gyűjtési időszak vége (maximális, 30 nap) | 2018. Szeptembertől 30. |
|Küldenie jóváhagyásra cége kifizetési kiszámítási dátuma (havonta a 15.) | 2018. október 1. |
|Küldenie jóváhagyásra cége kifizetési dátuma | 2018. október 15. |

Ha a vásárló megvásárolhatja a nagyvállalati szerződés használatával:

| Leírás |    Dátum  |
|----------|----------|
|Rendelés időszak | Augusztus 15, 2018-2018. augusztus 30. |
|Előfizetési időszak vége (negyedév) | 2018. Szeptembertől 30. |
|A Számlázás dátuma | 2018. október 15. |
|Így letétbe helyezheti a időszak (hitelkártyák csak 30 napig) | n/a |
|Gyűjtési időszak kezdete | 2018. október 15. |
|Gyűjtési időszak vége (maximális, 90 nap) | 2019. január 15. |
|Ügyfél fizetési dátuma | 2018. december 30. |
|Küldenie jóváhagyásra cége kifizetési kiszámítási dátuma (havonta a 15.) | 2019. január 15. |
|Küldenie jóváhagyásra cége kifizetési dátuma | 2019. február 15. |

**Ingyenes kreditek és a pénzügyi kötelezettségvállalás** 

Egyes ügyfelek úgy, hogy fizessen elő az Azure-ra vonatkozó pénzügyi kötelezettségvállalás, a nagyvállalati szerződés- vagy ingyenes krediteket rendelkeznek az Azure-ral való használathoz. Bár ezek a krediteket is használható Azure-használati díjat fizetni, hogy fizet a kiadói szoftverlicencdíj az nem használható.

**Számlázás és a gyűjtemények** 

Közzétevő software license számlázási egyike jelenik meg a kiválasztott ügyfél metódussal a számlázás, és a számlázási ütemterv követi. Nem teljesülnek nagyvállalati szerződéssel rendelkező ügyfelek marketplace szoftverlicencek havonta kell fizetnie. Nagyvállalati szerződéssel rendelkező ügyfeleink havonta számlázzuk negyedévente megjelenő számla keresztül.

Előfizetés vagy használatalapú díjszabási modellel ki van jelölve, amikor a Microsoft az ügynököt a közzétevő funkcionál, és felelős a számlázási, a fizetés és a gyűjtemény minden aspektusát.

### <a name="publisher-payout-and-reporting"></a>Közzétevő küldenie jóváhagyásra cége kifizetési és jelentéskészítés

* Licencelési szoftverdíjak az ügynök a Microsoft által gyűjtött hiányában egy 20 %-os tranzakciós díj vonatkozik, és vonjuk le a közzétevő küldenie jóváhagyásra cége kifizetési idején.

* Ügyfelek általában beszerzési használatával a nagyvállalati szerződés, vagy egy hitelkártya engedélyezve használatalapú szerződés. Szerződés típusa határozza meg a számlázás, Számlázás, gyűjtemény és küldenie jóváhagyásra cége kifizetési ütemezését.

>[!NOTE] 
>Az összes és a közzététel lehetőséget a transact-on keresztül az Insights Partnerközpont Cloud Partner portálra vagy Analytics szakaszának érhetők el.

#### <a name="billing-questions-and-support"></a>Számlázással kapcsolatos kérdések és támogatás

További információ és jogi szabályzatok: a [Kiadókra vonatkozó szerződése](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (a Cloud Partner Portalon érhető el).

Segítség a számlázási kérdésekhez, lépjen kapcsolatba [kereskedelmi marketplace publisher támogatási](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>A Transact-követelmények

Ebben a szakaszban a különböző típusú transact követelményei terjed ki.

### <a name="requirements-for-all-offer-types"></a>Az összes követelmények ajánlat típusok

- A Microsoft-fiók és a pénzügyi adatokat szükség, a transact-beállítást, függetlenül attól, hogy az ajánlat közzététele a díjszabási modell esetében.
- Kötelező pénzügyi adatokat küldenie jóváhagyásra cége kifizetési fiók- és adózási profilt tartalmaz.

Ezek a fiókok beállításával kapcsolatos további információkért lásd: [kezelheti a Partner Center fiók](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Meghatározott típusú követelményei

A transact-közzétételi lehetőség csak a következő típusú Marketplace-en való használatra érhető el: 

**Virtuális gép** 

Válassza ki az ingyenes, bring-your-saját licenc vagy pay-as-you-go-díjszabási modellek, és megjelenítheti, az ajánlat szintjén definiálva termékváltozatok. Az ügyfél Azure-számlán, a Microsoft szoftverlicencdíj az alapul szolgáló Azure-infrastruktúra díjak elkülönítve mutat be a közzétevő. Az Azure infrastruktúra-díjak a közzétevő szoftverek használatával alakítják.

**Az Azure-alkalmazások: A Megoldássablon vagy a felügyelt alkalmazás** 

Engedélyeznie kell egy vagy több virtuális gépeket, és lekéri a virtuális gépek díjszabását összege keresztül. Egyetlen csomagra a felügyelt alkalmazások a havi előfizetés alapján, átalánydíjjal választhatók, a díjszabás inkább modell, a virtuális gépek díjszabását. Bizonyos esetekben az Azure-infrastruktúra használati díjak a rendszer átadja az ügyfélnek külön-külön szoftverlicencdíjaktól, de a számlázási ugyanabban az utasításban. Azonban ha egy felügyelt alkalmazás konfigurálása az ISV-infrastruktúra költségeire, amely az Azure-erőforrások számlázása a közzétevőhöz, és az ügyfél fogad egy egybesimított díj, amely magában foglalja az infrastruktúrát, a szoftverlicencek és a felügyeleti szolgáltatások díját.

## <a name="next-steps"></a>További lépések

* Tekintse át a jogosult-e a kijelölés és a konfiguráció az Ön ajánlatát véglegesítéséhez ajánlat típushoz című által a közzétételi beállítások.
* Tekintse át a közzétételi minták storefront-példák hogyan megoldását képez le egy ajánlat típusát és a konfiguráció alapján.
