---
title: Kereskedelmi piactéri számlázás | Azure Piactér
description: Ez a cikk a kereskedelmi piackereskedelmi vonatkozású témaköröket ismerteti.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279742"
---
# <a name="commercial-marketplace-billing"></a>Kereskedelmi piactéri számlázás

Ez a cikk a kereskedelmi piac kereskedelemmel kapcsolatos témáival foglalkozik:

- [Piactér közzétételi beállításai](#marketplace-publishing-options)
- [Általános áttekintés lebonyolítása](#transact-general-overview)
- [Tranzakciós számlázási modellek](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Piactér közzétételi beállításai

A kereskedelmi piactér számos közzétételi lehetőséget kínál a kiadók számára.

### <a name="list-and-trial-publishing-options"></a>Lista- és próbaverzióközzétételi beállítások

A kiadók promóciós és felhasználószerzési célokra használhatják a listát, a próbaverziót, és saját licenckiadási (BYOL) közzétételi lehetőségeket hozhatnak létre. Ezekkel a beállításokkal a Microsoft nem vesz részt közvetlenül a közzétevő szoftverlicenc-tranzakcióiban, és nincs társított tranzakciós díj. A kiadók felelősek a szoftverlicenc-tranzakció minden aspektusának támogatásáért, beleértve, de nem kizárólagosan: a megrendelést, a teljesítést, a mérést, a számlázást, a számlázást, a fizetést és a beszedést. A lista- és próbaverzióközzétételi lehetőségekkel a kiadók a kiadói szoftverek licencelési díjainak 100%-át az ügyféltől tartják meg.

### <a name="transact-publishing-option"></a>Közzétételi lehetőség tranzakciója

A lista- és próbaverzióközzétételi beállításokon kívül a tranzakciós közzétételi lehetőség is elérhető a közzétevők számára. Ez a beállítás kihasználja a Microsoft globálisan elérhető kereskedelmi lehetőségeit, és lehetővé teszi a Microsoft számára, hogy a közzétevő nevében felhőalapú piactéri tranzakciókat tároljon.

## <a name="transact-general-overview"></a>Általános áttekintés lebonyolítása

A transact közzétételi lehetőség használatakor a Microsoft engedélyezi a harmadik féltől származó szoftverek értékesítését, valamint bizonyos ajánlattípusok üzembe helyezését az ügyfél Azure-előfizetésében. A közzétevőnek figyelembe kell vennie az infrastruktúra-díjak számlázását és a kiadó saját szoftverlicencelési díjait a számlázási modell és az ajánlattípus kiválasztásakor.

A Transact közzétételi lehetőség jelenleg a következő ajánlattípusok: virtuális gépek, Azure-alkalmazások és SaaS-alkalmazások jelenleg támogatott.

![Tranzakció az Azure Piactéren](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Számlázási infrastruktúra költségei

#### <a name="for-virtual-machines-and-azure-applications"></a>Virtuális gépekhez és Azure-alkalmazásokhoz

Virtuális gépek és Az Azure-alkalmazások esetében az Azure-infrastruktúra használati díjait az ügyfél Azure-előfizetése számítja fel. Az infrastruktúra használati díjai nak ára és bemutatása a szoftverszolgáltató nak az ügyfél számláján szereplő licencdíjaitól elkülönítve történik.

#### <a name="for-saas-apps"></a>SaaS-alkalmazások esetén

SaaS-alkalmazások esetén a közzétevőnek egyetlen költségelemként kell figyelembe vennie az Azure-infrastruktúra használati díjait és a szoftverlicencelési díjakat. Ez az ügyfél számára átalánydíjként jelenik meg. Az Azure-infrastruktúra-használat kezelése és számlázása közvetlenül a partner. A tényleges infrastruktúra-használati díjakat az ügyfél nem látja. A kiadók általában úgy döntenek, hogy az Azure-infrastruktúra használati díjait a szoftverlicenc-díjba csomagolják. A szoftverlicencelési díjak nem mérőszámok vagy felhasználásalapúak.

## <a name="transact-billing-models"></a>Tranzakciós számlázási modellek

A használt tranzakciós opciótól függően a közzétevő szoftverlicenc-díjai a következőképpen jelenhetnek meg:

- *Ingyenes*: A szoftverlicencek díjmentesek.
- *Saját licenc (BYOL)* használata: A szoftverlicencek minden vonatkozó díját közvetlenül a közzétevő és az ügyfél kezeli. A Microsoft csak az Azure infrastruktúra használati díjait továbbítja. (Csak virtuális gépek és Azure-alkalmazások esetén.)
- *Használatalapú fizetés:* A szoftverlicenc-díjak óránkénti, magonkénti (vCPU) díjszabásként jelennek meg az Azure-infrastruktúrán alapuló díjszabás alapján. Ez a modell csak a virtuális gépek és az Azure-alkalmazások vonatkozik.
- *Előfizetési díjak*: A szoftverlicenc-díjak havi vagy éves, ismétlődő díjként kerülnek bemutatásra, átalánydíjként vagy ülőhelyenként. Ez a modell csak a SaaS-alkalmazásokra és az Azure-alkalmazásokra vonatkozik – felügyelt alkalmazások.
- *Ingyenes szoftverpróba:* 30 vagy 90 napig ingyenes a szoftverlicencek díja.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ingyenes és bring-your-own-license (BYOL) árképzés

Az ingyenes vagy saját licencre vonatkozó licencajánlat közzétételekor a Microsoft nem játszik szerepet a szoftverlicenc-díjak értékesítési tranzakciójának megkönnyítésében. A lista- és próbaverziós közzétételi lehetőségekhez hasonlóan a közzétevő a szoftverlicenc-díjak 100%-át megtartja.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Kiosztó-kirovó és előfizetési (webhelyalapú) díjszabás

A felosztó-kirovó vagy előfizetéses tranzakciós ajánlat közzétételekor a Microsoft biztosítja a szoftverlicenc-vásárlások, visszaküldések és visszaterhelések feldolgozásához szükséges technológiát és szolgáltatásokat. Ebben az esetben a közzétevő felhatalmazza a Microsoftot, hogy ilyen célból ügynökként járjon el. A kiadó lehetővé teszi a Microsoft számára, hogy megkönnyítse a szoftverlicencelési tranzakciót, miközben megtartja az eladóként, szolgáltatóként, forgalmazóként és licencadóként való kijelölésüket.

A Microsoft lehetővé teszi az ügyfelek számára, hogy megrendeljék, licenceljék és használják a kiadói szoftvereket, a Microsoft kereskedelmi piacterének és a kiadó végfelhasználói licencszerződésének feltételeinek megfelelően. A közzétevőknek meg kell adniuk a végfelhasználói licencszerződést, vagy ki kell választaniuk az [általános szerződést](https://docs.microsoft.com/azure/marketplace/standard-contract) az ajánlat létrehozásakor.

### <a name="free-software-trials"></a>Ingyenes szoftverkísérletek

A közzétételi forgatókönyvek tranzakciós forgatókönyvek esetén a közzétevő 30 vagy 90 napig ingyenesen elérhetővé teheti a szoftverlicencet. Ez a diszkontálási képesség nem tartalmazza az Azure-infrastruktúra használatának költségét, amelyet a partnermegoldás használata hajt.

### <a name="private-offers"></a>Privát ajánlatok

Az ajánlattípusok és számlázási modellek használata mellett a közzétevők privát ajánlatot is köthetnek, egyeztetett és ügyletspecifikus díjszabással vagy egyéni konfigurációkkal kiegészítve. A privát ajánlatokat mind a 3 tranzakciós közzétételi lehetőség támogatja.

Ez a beállítás magasabb vagy alacsonyabb árszabást tesz lehetővé, mint a nyilvánosan elérhető ajánlat. A privát ajánlatok segítségével kedvezményt vehet fel, vagy felárazáshoz. Privát ajánlatok at lehet elérhetővé tenni egy vagy több ügyfél által fehér felsorolja az Azure-előfizetés az ajánlat szintjén.

### <a name="examples"></a>Példák

#### <a name="pay-as-you-go"></a>Használatalapú fizetés

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

### <a name="bring-your-own-license-byol"></a>Hozd a saját licenc (BYOL)

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

### <a name="saas-app-subscription"></a>SaaS Alkalmazás-előfizetés

Ezt a lehetőséget úgy kell beállítani, hogy a Microsofton keresztül értékesítsen, és díjszabása átalánydíjas, vagy felhasználónként havi vagy éves alapon történhet.

• Ha engedélyezi az Eladás a Microsofton keresztül opciót egy SaaS-ajánlathoz, akkor a következő költségstruktúrával rendelkezik.

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

**Csökkentett piactéri szolgáltatási díj:** Egyes SaaS-termékek esetében, amelyeket Ön a kereskedelmi piactéren tesz közzé, a Microsoft a Marketplace szolgáltatási díját 20%-ról (a Microsoft kiadói szerződésében leírtak szerint) 10%-ra csökkenti.  Ahhoz, hogy a Termék jogosult legyen, a Microsoftnak legalább az egyik termékét az IP-társértékesítésre kész vagy az IP-társértékesítésre vonatkozó prioritásként kell megjelölnie. Ahhoz, hogy a havi kedvezményes Piactér-szolgáltatási díjat megkapd, a jogosultságnak legalább öt (5) munkanapon belül teljesítenie kell az előző naptári hónap vége előtt. A csökkentett Piactér-szolgáltatási díj nem vonatkozik a virtuális gépekre, a felügyelt alkalmazásokra vagy a kereskedelmi piactéren elérhetővé tett egyéb termékekre.  Ez a csökkentett piactéri szolgáltatási díj minősített ajánlatokszámára lesz elérhető, a Microsoft 2019.  Ezt követően a Marketplace szolgáltatási díj a normál összeghez fog visszatérni.
