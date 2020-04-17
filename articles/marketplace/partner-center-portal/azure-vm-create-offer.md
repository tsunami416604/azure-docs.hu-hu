---
title: Azure virtuálisgép-ajánlat létrehozása – Azure Piactér
description: Ismerje meg, hogyan hozhat létre virtuálisgép-ajánlatot a kereskedelmi piacon.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 161fd9276427db0d0d7d56da1bfc0bb8ccf52a52
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536658"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Azure virtuálisgép-ajánlat létrehozása

> [!IMPORTANT]
> Az Azure virtuális gép ajánlatainak kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a [Virtuálisgép-ajánlat létrehozása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) a Cloud Partner Portal ban az ajánlatok kezeléséhez.

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé egy Azure virtuálisgép-ajánlatot az [Azure Piactéren.](https://azuremarketplace.microsoft.com/) Mind a Windows- és Linux-alapú virtuális gépek, amelyek tartalmazzák az operációs rendszer, virtuális merevlemez (VHD), és akár 16 adatlemezek.

## <a name="introduction"></a>Introduction (Bevezetés)

### <a name="publishing-benefits"></a>Közzétételi előnyök

Az Azure Marketplace-en való közzététel a következő előnyökkel jár:

- A vállalat népszerűsítése a Microsoft márkanév használatával
- Több mint 100 millió Office 365- és Dynamics 365-felhasználó és több mint 200 000 szervezet elérése az Azure Marketplace-en keresztül
- Kiváló minőségű érdeklődőket szerezhet be ezekről a piacterekről
- A Szolgáltatások nak a Microsoft helyszíni és távértékesítési csapatai által támogatott támogatása

### <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, tekintse át a [virtuális gép ajánlatközzétételi útmutatóját](https://aka.ms/Virtualmachineofferpublishingguide) és az Azure virtuálisgép-anyagát:

- Rövid útmutatók
  - [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure gyorsindítási sablonjai](https://github.com/azure/azure-quickstart-templates)
- Oktatóanyagok
  - [Linux rendszerű virtuális gépek](https://aka.ms/LinuxVMtutorial)
  - [Windows rendszerű virtuális gépek](https://aka.ms/windowsvms)
- Példák
  - [Azure CLI-minták Linuxos virtuális gépekhez](https://aka.ms/linuxclisamples)
  - [Azure PowerShell Linuxos virtuális gépekhez](https://aka.ms/linuxpowershellsamples)
  - [Azure CLI-minták Windows virtuális gépekhez](https://aka.ms/windowsclisamples)
  - [Azure PowerShell Windows-alapú virtuális gépekhez](https://aka.ms/windowspowershellvmsamples)

### <a name="fundamentals-in-technical-knowledge"></a>A műszaki ismeretek alapjai

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges.

A mérnöki csapatnak meg kell értenie a következő Microsoft-technológiákat:

- Az [Azure-szolgáltatások](https://azure.microsoft.com/services/) alapvető ismerete
- [Azure-alkalmazások tervezése és megtervezése](https://azure.microsoft.com/solutions/architecture/)
- Az [Azure virtuális gépek,](https://azure.microsoft.com/services/virtual-machines/)az [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure Networking munkaismerete](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Azure virtuálisgép-ajánlat létrehozása

Az Azure virtuálisgép-ajánlat létrehozása előtt kereskedelmi piactéri fiókkal kell rendelkeznie a Partnerközpontban. Ha még nem hozott létre ilyet, olvassa el [a Kereskedelmi piactér-fiók létrehozása a Partnerközpontban című témakört.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

1. Jelentkezzen be a [Partnerközpontba](https://partner.microsoft.com/dashboard/home), majd a felső menüben válassza az **Irányítópult**lehetőséget.
2. A bal oldali navigációs sávon válassza a **Kereskedelmi piactér**lehetőséget, majd **az Áttekintés lehetőséget.**
3. Az **Áttekintés** lapon válassza a **+ Új ajánlat**lehetőséget, majd az Azure virtuális **gép**lehetőséget. Megjelenik **az Új ajánlat** párbeszédpanel.

![A Partnerközpont Áttekintés lapját mutatja be az Új ajánlat gombbal és az Azure Virtuálisgép-ajánlat beállítással.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

Adja meg **az ajánlatazonosítót.** Ez a fiókban lévő minden egyes ajánlat egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a piactéri ajánlat webcímében, valamint az Azure PowerShellben és az Azure CLI-ben, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásjeleket, de szóközöket nem, és legfeljebb 50 karakter ből állhat. Ha például itt adja meg a **tesztajánlat-1-et,** az ajánlat webcíme a . `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`
- Az ajánlatazonosító nem módosítható, miután a Létrehozás lehetőséget **választja.**

Adja meg **az ajánlat aliasát**. Ezt a nevet használjuk az ajánlathoz a Partnerközpontban. Ez a név nem használatos a piactéren, és eltér az ajánlat neve és egyéb értékek jelennek meg az ügyfelek számára.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és folytatásához.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="test-drive"></a>Tesztvezetés

Állítson be egy bemutatót (tesztvezetést), amely lehetővé teszi az ügyfelek számára, hogy vásárlás előtt kipróbálják az ajánlatot. Ha olyan bemutató környezetet szeretne létrehozni, amely lehetővé teszi az ügyfelek számára, hogy meghatározott ideig kipróbálják az ajánlatot, olvassa el az Ajánlat tesztelése a kereskedelmi piacon című [témakört.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Tesztvezetés engedélyezéséhez jelölje be a **Tesztvezetés engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztvezetést az ajánlatból, törölje a jelet a jelölőnégyzetből.

További tesztmeghajtó-erőforrások:

- [Gyakorlati tanácsok gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketingre vonatkozó ajánlott eljárások](https://aka.ms/TestDriveMarketingBestPractices)
- [Tesztmeghajtók – áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

### <a name="lead-management"></a>Érdeklődők kezelése

Amikor a Partner Center rel teszi közzé az ajánlatot a kereskedelmi piacon, csatlakoztassa azt az ügyfélkapcsolat-kezelés (CRM) rendszeréhez. Ez lehetővé teszi, hogy megkapja az ügyfél elérhetőségi adatait, amint valaki érdeklődést mutat vagy használja a terméket. Ha CRM-hez kell csatlakozni, ha engedélyezi **a Test Drive-ot** (lásd a korábbi szakaszt), ellenkező esetben nem kötelező.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A Partnerközpont a következő CRM rendszereket támogatja:
    - [Dynamics 365](https://aka.ms/Dyn365LeadMgmt) az ügyfelek elköteleződése érdekében
    - [Marketo](https://aka.ms/LeadMgmtMarketo)
    - [Salesforce](https://aka.ms/LeadMgmtSalesforce)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fenti felsorolásban, használja [az Azure Table](https://aka.ms/AzureTableLeadMgmt) vagy https [endpoint](https://aka.ms/LeadMgmtHTTPS) az ügyfél érdeklődői adatok tárolására. Ezután exportálja az adatokat a CRM-rendszerbe.

2. A Partnerközpontban történő közzétételkor csatlakoztassa az ajánlatot az érdeklődő célhelyéhez.
3. Ellenőrizze, hogy az érdeklődő célállomásához való csatlakozás megfelelően van-e konfigurálva. Miután közzétette a Partnerközpontban, érvényesítjük a kapcsolatot, és elküldjük Önnek a tesztérdeklődőt. Amíg az ajánlat előnézete előtt éles, tesztelheti az érdeklődői kapcsolatot, ha megpróbálja telepíteni az ajánlatot magát az előzetes verziós környezetben.
4. Győződjön meg arról, hogy az érdeklődő célállomásához való csatlakozás naprakész marad, hogy ne veszítse el az érdeklődőket.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen az oldalon meghatározhatja az ajánlata csoportosításához használt kategóriákat és iparágakat a piactéren, az alkalmazás verzióján és az ajánlatot támogató jogi szerződéseken.

### <a name="categories"></a>Kategóriák

Válasszon legalább egy és legfeljebb öt kategóriát. Ezek a kategóriák az ajánlat megfelelő piactérkeresési területeken való elhelyezésére szolgálnak. Az ajánlat leírásában ismertesse, hogyan támogatja az ajánlat ezeket a kategóriákat. A virtuálisgép-ajánlatok az Azure Marketplace **Számítási** kategóriájában jelennek meg.

### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat feltételeit. Erre két lehetősége van:

- Használja a saját felhasználási feltételeit
- A Microsoft kereskedelmi piactér általános szerződésének használata

#### <a name="use-your-own-terms-and-conditions"></a>Használja a saját felhasználási feltételeit

Saját egyéni feltételek megadásához írja be legfeljebb 10 000 karakternyi szöveget a **Felhasználási feltételek** mezőbe. Ha hosszabb leírásra van szüksége, adjon meg egy webcímet, amely rámutat arra, hogy hol találhatók a feltételek. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piactér általános szerződésének használata

Az ügyfelek beszerzési folyamatának egyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft standard szerződést kínál a kereskedelmi piacszámára. Amikor a szoftvert az általános szerződés alapján kínálja, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és önnek nem kell egyéni feltételeket létrehoznia.

Használja az Általános szerződés lehetőséget a **Microsoft kereskedelmi piacteréhez általános szerződés használata** jelölőnégyzet bejelölésével, majd az Elfogadás **lehetőséggel** az előugró ablakban (előfordulhat, hogy felfelé kell görgetnie a megtekintéséhez).

![A Partnerközpont Áttekintés lapját mutatja be az Új ajánlat gomb baloldalán, és a Tanácsadási szolgáltatásajánlatot választja.](media/use-standard-contract.png)

> [!NOTE]
> Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó általános szerződés használatával, nem használhatja a saját egyéni szerződési feltételeit. Vagy felajánlja a megoldást a Standard Szerződés **alapján, vagy** a saját feltételei alapján.

Ha többet szeretne megtudni a standard szerződésről, olvassa el a Microsoft kereskedelmi piactér általános szerződéscímű [témakörét.](https://docs.microsoft.com/azure/marketplace/standard-contract) Az általános [szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) pdf-ként tölthető le (győződjön meg arról, hogy az előugró ablakok blokkolása ki van kapcsolva).

##### <a name="standard-contract-amendments"></a>Általános szerződésmódosítások

Az általános szerződésmódosítások lehetővé teszik, hogy az egyszerűség kedvéért kiválassza az általános szerződési feltételeket, és megteremtse a termékek vagy vállalkozások feltételeit. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már felülvizsgálták és elfogadták a Microsoft általános szerződését. Kétféle módosítás áll rendelkezésre: univerzális és egyedi.

**Univerzális módosítások** – Ezek általánosan vonatkoznak a standard szerződés minden ügyfél számára. Az ajánlat minden vevőjének megjelennek a beszerzési folyamatban. Az ügyfeleknek el kell fogadniuk az Általános Szerződés feltételeit és a módosítás(oka)t, mielőtt felhasználhatják az ajánlatot. Ajánlatonként egyetlen általános módosítást is megadhat. Ebben a mezőben korlátlan számú karaktert írhat be. Ezek a feltételek az AppSource, az Azure Marketplace és/vagy az Azure Portalon jelennek meg az ügyfelek számára a felderítési és vásárlási folyamat során.

**Egyéni módosítások** – Ezek a standard szerződés speciális módosításai, amelyek az Azure-beli bérlői azonosítókon keresztül meghatározott ügyfelekre irányulnak. Kiválaszthatja a megcélozni kívánt bérlőt. Csak a bérlői ügyfelek kapnak egyéni módosítási feltételeket az ajánlat beszerzési folyamatában. Az ügyfeleknek el kell fogadniuk az Általános Szerződés feltételeit és a módosítás(oka)t, mielőtt felhasználhatják az ajánlatot.

Először válassza **az Egyéni módosítási kifejezések hozzáadása (Max 10)** lehetőséget. Ajánlatonként legfeljebb tíz egyéni módosítási feltételt adhat meg.

- **Egyéni módosítási feltételek** – Adja meg saját módosítási feltételeit az egyéni módosítási feltételek mezőben. Korlátlan számú karaktert adhat meg. Csak a bérlői azonosítók adja meg ezeket az egyéni feltételeket fogja látni ezeket az ajánlat vásárlási folyamat az Azure Portalon.
- **Bérlői azonosítók** (kötelező) – Minden egyéni módosítás legfeljebb 20 bérlői azonosítók. Ha hozzáad egy egyéni módosítást, meg kell adnia legalább egy bérlői azonosítót, amely azonosítja az ügyfelet az Azure-ban. az ügyfél megtalálja az Ön számára az Azure alatt, majd tulajdonságok. A címtárazonosító értéke a bérlőazonosító (például 50c464d3-4930-494c-963c-1e951d15360e). A szervezet ügyfél bérlői azonosítóját is megtalálhatja a Domain name webcímhasználatával a [Mi a Microsoft Azure és az Office 365-ös bérlői azonosítóm?](https://www.whatismytenantid.com/).
- **Leírás** (nem kötelező) – Adjon meg egy rövid leírást a bérlői azonosítóhoz, amely segít azonosítani a módosítással megcélzott ügyfelet.

> [!NOTE]
> Ez a két típusú módosítások verem egymásra. Az egyéni módosításokkal megcélzott ügyfelek a vásárlás során az Általános Szerződés általános módosítását is megkapják.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlat lista

Ezen a lapon megadhatja az ajánlat részleteit, például az ajánlat nevét, leírását, hivatkozásait és névjegyeit.

> [!NOTE]
> Az ajánlat listai tartalma (például a leírás, a dokumentumok, a képernyőképek és a használati feltételek) nem szükséges angol nyelven, amennyiben az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." _A Hasznos hivatkozás webcímét_ is megadhat, hogy a tartalmat az Ajánlat listatartalmában használttól eltérő nyelven kínálhassa.

### <a name="marketplace-details"></a>Piactér részletei

#### <a name="name"></a>Name (Név)

Az itt megadott név az ajánlathirdetés címeként jelenik meg az ügyfelek számára. Ez a mező előre ki van töltve az **Ajánlat alias** mezőjében az ajánlat létrehozásakor megadott szöveggel. A nevet később módosíthatja.

A név:

- Védjeggyel védhető (és védjegy- és szerzői jogi szimbólumokat is megadhat)
- Nem lehet több 50 karakternél hosszú
- Nem tartalmazhat nak emojikat.

#### <a name="search-results-summary"></a>Keresési eredmények összegzése

Az ajánlat rövid leírása. Ez legfeljebb 100 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

#### <a name="long-summary"></a>Hosszú összegzés

Adjon meg hosszabb leírást az ajánlatról. Ez legfeljebb 256 karakter hosszú lehet, és a piactér keresési eredményei között használatos.

#### <a name="description"></a>Leírás

Adja meg az ajánlat részletes leírását, legfeljebb 3000 karakterig. Ez jelenik meg az ügyfelek számára a kereskedelmi piactér listaáttekintésében.

A leírásban szerepeljen az alábbiak közül egy vagy több:

- Ajánlatának értéke és legfontosabb előnyei
- Kategória vagy iparági szövetségek, vagy mindkettő
- Alkalmazáson belüli vásárlási lehetőségek
- Minden szükséges közzététel

Íme néhány tipp az írás a leírás:

- Egyértelműen írja le az ajánlat értékajánlatát a leírás első néhány mondatában. Adja meg a következő elemeket:
  - Az ajánlat leírása.
  - Az ajánlat előnyeit élvező felhasználó típusa.
  - Az ajánlat által megoldott ügyféligények vagy problémák.
- Ne feledje, hogy az első néhány mondat megjelenhet a keresőmotor találatai között.
- Ne hagyatkozzon az ajánlat értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson az ajánlat által nyújtott értékre.
- Iparág-specifikus vagy előnyökön alapuló szavak használata.

Annak érdekében, hogy ajánlata leírása vonzóbbá legyen, használja a Rich Text szerkesztőt a leírás formázásához. A Rich Text szerkesztő lehetővé teszi számok, felsorolásjelek, félkövér, dőlt betűk és behúzások hozzáadását, hogy a leírás olvashatóbb legyen.

![A Partnerközpont Áttekintés lapját mutatja be az Új ajánlat gomb baloldalán, és a Tanácsadási szolgáltatásajánlatot választja.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Adatvédelmi irányelv hivatkozás

Adja meg a szervezet adatvédelmi szabályzatának url-címét. Győződjön meg arról, hogy ajánlata megfelel az adatvédelmi törvényeknek és előírásoknak. Érvényes adatvédelmi szabályzatot is közzé kell tennie a webhelyén.

### <a name="useful-links"></a>Hasznos hivatkozások

Nyújtson kiegészítő online dokumentumokat az ajánlatáról. Hivatkozás hozzáadásához válassza **a + Hivatkozás hozzáadása lehetőséget,** majd töltse ki a következő mezőket:

- **Név** – Az ügyfelek a név megjelennek a részletek oldalon.
- **Link (URL)** – Adjon meg egy hivatkozást az ügyfelek számára az online dokumentum megtekintéséhez.

### <a name="customer-support-links"></a>Ügyfélszolgálati hivatkozások

Adja meg a támogatási webhelyet, ahol az ügyfelek elérhetik a támogatási csapatot.

- Az Azure Global támogatási webhelye
- Az Azure Government támogatási webhelye

### <a name="partner-support-contact"></a>Partnertámogatási kapcsolattartó

Adja meg a Microsoft-partnerek elérhetőségi adatait, amelyeket akkor használhatnak fel, amikor az ügyfelek támogatási jegyet nyitnak. Ez nem jelenik meg a piacon.

- Name (Név)
- E-mail
- Telefon

### <a name="engineering-contact"></a>Műszaki kapcsolattartó

Adja meg a Microsoft elérhetőségi adatait, amelyeket akkor használhat, ha problémák merülnek fel az ajánlattal kapcsolatban, beleértve a minősítéssel kapcsolatos problémákat is. Ez nem jelenik meg a piacon.

- Name (Név)
- E-mail
- Telefon

### <a name="marketplace-media"></a>Marketplace-média

Adjon meg emblémákat és képeket az ajánlatához. Minden képnek PNG formátumúnak kell lennie. Az elmosódott képek a beküldött anyag elutasítását eredményezik.

#### <a name="marketplace-logos"></a>Marketplace-emblémák

Png-fájlok at az ajánlat logója a következő négy pixel méretben:

- **Kicsi** (48 x 48)
- **Közepes** (90 x 90)
- **Nagy** (216 x 216)
- **Széles** (255 x 115)

Mind a négy embléma kötelező, és a piactér i.

#### <a name="screenshots"></a>Képernyőképek

Adjon hozzá legfeljebb öt képernyőképet, amelyek megmutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie. A képernyőkép leírásához feliratot is hozzá kell adnia.

#### <a name="videos"></a>Videók

Akár öt videót is hozzáadhatsz, amelyek bemutatják az ajánlatodat. Ezeket külső videoszolgáltatáson kell üzemeltetni. Adja meg az egyes videók nevét, webcímét és a videó miniatűr PNG-képét 1280 x 720 képpont méretűen.

Az erőforrásokat a piacterek en-tőzsdei jegyzése című témakörben további [piactér-listákat ismertető témakörben ismer.](https://aka.ms/LdMgmtOfferListingBestPractices)

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

## <a name="preview"></a>Előzetes verzió

Az Előnézet lapon válasszon egy korlátozott **előnézeti közönséget** az ajánlat érvényesítéséhez, mielőtt élőben közzészeretnéd tenni a szélesebb piactéri közönség számára.

> [!IMPORTANT]
> Miután ellenőrizte az ajánlatot az Előzetes verzióban, válassza az **Élő ben való közzétételgombot,** ha közzé szeretné tenni az ajánlatot a kereskedelmi piactér nyilvános közönsége számára.

Az előzetes verzióközönséget az Azure-előfizetésazonosítók azonosítói azonosítják, és mindegyikhez egy opcionális leírás is szerepel. Egyik mezőt sem látják az ügyfelek. Az Azure-előfizetés-azonosítóját az Azure Portal **Előfizetések** lapján találja.

Adjon hozzá legalább egy Azure-előfizetés-azonosítót, akár egyenként (legfeljebb 10) vagy csv-fájl feltöltésével (legfeljebb 100). Ezekkel az előfizetési azonosítókkal megadhatja, hogy ki tekintheti meg az ajánlat előnézetét az élő közzététel előtt. Ha az ajánlat már él, továbbra is megadhat egy előnézeti közönséget az ajánlatmódosításvagy az ajánlat frissítéseinek teszteléséhez.

> [!NOTE]
> Az előnézeti közönség eltér a privát közönségtől. Az előnézeti közönség hozzáférhet az ajánlathoz, _mielőtt_ élőben közzéteté lenne a piactereken. Megtekinthetik és érvényesíthetik az összes tervet, beleértve azokat is, amelyek csak a privát közönség számára lesznek elérhetők, miután az ajánlat teljes mértékben megjelent a piacon. A privát közönség (amelyet a terv **díjszabása és rendelkezésre állása** lapon határoz meg) kizárólagos hozzáféréssel rendelkezik egy adott csomaghoz.

Válassza **a Vázlat mentése lehetőséget,** mielőtt továbblépne a következő szakaszra, a Tervezés áttekintése parancsra.

## <a name="plan-overview"></a>Terv – áttekintés

Ugyanazon ajánlaton belül különböző csomagbeállításokat adhat meg a Partnerközpontban. Ezeket a terveket korábban sk-nek nevezték. Egy ajánlathoz legalább egy csomag szükséges, amely a szerzési közönség, az Azure-régiók, a funkciók vagy a virtuális gép lemezképei tekintetében eltérő lehet.

A tervek létrehozása után a **Terv áttekintése** lapon a következők láthatók:

- Terv nevek
- Licencmodellek
- Közönség (állami vagy privát)
- Jelenlegi közzétételi állapot
- Elérhető műveletek

A Terv áttekintésében elérhető műveletek a terv aktuális állapotától függően változnak. Ezek például az alábbi jelentések lehetnek:

- **Piszkozat törlése** – Ha a terv állapota vázlat
- **Stop eladni terv** vagy **Sync privát közönség** - Ha a terv állapotát közzétett élőben

### <a name="create-new-plan"></a>Új terv létrehozása

Válassza a **+ Új terv létrehozása** lehetőséget a tetején. Megjelenik **az Új terv** párbeszédpanel.

A **Terv azonosítója** mezőben hozzon létre egy egyedi tervazonosítót az ajánlatban szereplő minden egyes tervhez. Ez az azonosító látható lesz a termék webcímében szereplő ügyfelek számára. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásjeleket és legfeljebb 50 karaktert használjon.

> [!NOTE]
> A csomagazonosító nem módosítható a **Létrehozás (Létrehozás) választódat**választ.

A **Terv neve** mezőbe írja be a terv nevét. Az ügyfelek ezt a nevet látják, amikor eldöntik, hogy melyik csomagot válasszák ki az ajánlaton belül. Hozzon létre egy egyedi nevet, amely egyértelműen rámutat az egyes tervek közötti különbségekre. Használhatja például a **Windows Server** rendszert **használatalapú fizetéses,** **BYOL,** **Speciális**és **Vállalati**csomagokkal.

Kattintson a **Létrehozás** gombra.

### <a name="plan-setup"></a>A terv beállítása

Állítsa be a csomag típusának magas szintű konfigurációját, hogy újrafelhasználja-e a technikai konfigurációt egy másik csomagból, és mely Azure-régiókban legyen elérhető a csomag. Az itt megadott beállítások határozzák meg, hogy mely mezők jelenjenek meg az ugyanazon terv többi lapján.

#### <a name="reuse-technical-configuration"></a>Műszaki konfiguráció újrafelhasználása

Ha egynél több azonos típusú tervvel rendelkezik, és a csomagok azonosak közöttük, akkor ezt **a tervet újrafelhasználhatja egy másik tervből.** Ez a beállítás lehetővé teszi, hogy válasszon egy másik, azonos típusú tervek et ehhez az ajánlathoz, és újra felhasználja a technikai konfigurációt.

> [!NOTE]
> Ha egy másik csomag technikai konfigurációját újra felhasználja, a teljes **Műszaki konfiguráció** lap eltűnik ebből a tervből. Ehhez a tervhez a másik csomag műszaki konfigurációs adatait is felhasználjuk, beleértve a jövőben elkészült frissítéseket is. Ez a beállítás nem módosítható a terv közzététele után.

#### <a name="azure-regions"></a>Azure-régiók

A csomag legalább egy Azure-régióban elérhetővé kell tenni.

Válassza ki az **Azure Global** lehetőséget, hogy a csomag elérhetővé az ügyfelek számára az összes nyilvános Azure-régiókban, amelyek kereskedelmi piactér integráció. További információt a [Földrajzi elérhetőség és pénznemtámogatás](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Válassza ki az **Azure Government** lehetőséget, hogy a csomag elérhető vé legyen az [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) régióban. Ez a régió ellenőrzött hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi szervezeteiből, valamint az azok kiszolgálására jogosult partnerekszámára. Ön, mint a közzétevő, felelős a megfelelőségi ellenőrzésekért, a biztonsági intézkedésekért és a gyakorlati tanácsokért. Az Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).

Az [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)általi közzététel előtt tesztelje és érvényesítse a csomagot a környezetben, mivel bizonyos végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez kérjen próbafiókot a [Microsoft Azure Government próbaverziójától.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Miután a csomag közzé, és elérhető egy adott Azure-régióban, nem távolíthatja el a régiót.

#### <a name="azure-government-certifications"></a>Azure Government-tanúsítványok

Ez a beállítás csak akkor látható, ha az **Azure-kormányzat** lehetőséget választja az **Azure-régiók alatt.**

Az Azure Government-szolgáltatások olyan adatokat kezelnek, amelyekre bizonyos kormányzati előírások és követelmények vonatkoznak. Például FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. Annak érdekében, hogy felhívják a figyelmet a programok tanúsítványaira, legfeljebb 100 hivatkozást adhat meg, amelyek leírják azokat. Ezek lehetnek közvetlenül a programon található hirdetésre mutató linkek, vagy a saját webhelyein való megfelelés leírására mutató hivatkozások. Ezek a hivatkozások csak az Azure Government-ügyfelek számára láthatók.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

### <a name="plan-listing"></a>A terv listája

Itt állíthatja be a terv listarészleteit. Ez a lap olyan konkrét információkat jelenít meg, amelyek az ugyanabban az ajánlatban szereplő tervek között eltérhetnek.

#### <a name="plan-name"></a>Terv neve

Ez előre ki van töltve azzal a névvel, amelyet a terv létrehozásakor adott. Ez a név jelenik meg a piactéren, mint a cím a terv, és legfeljebb 100 karakter.

#### <a name="plan-summary"></a>Terv összegzése

Adjon meg egy rövid összefoglalót a csomagról (nem az ajánlatról). Ez az összefoglaló legfeljebb 100 karakterből állhat.

#### <a name="plan-description"></a>Terv leírása

Írja le, mi teszi egyedivé ezt a szoftvertervet, valamint az ajánlaton belüli tervek közötti különbségeket. Ne írja le az ajánlatot, csak a tervet. A terv leírása legfeljebb 2000 karaktert tartalmazhat.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

### <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Ezen a lapon a következőket kell konfigurálnia:

- A tervek által
- Az ár óránként
- Azt jelzi, hogy a tervet mindenki számára láthatóvá kívánja-e tenni, vagy csak bizonyos ügyfelek (privát közönség) számára

#### <a name="markets"></a>Piacok

Minden tervnek legalább egy piacon elérhetőnek kell lennie. Jelölje be a jelölőnégyzetet minden olyan piacon, ahol a csomag megvásárolható nak kell lennie (a felhasználók ezeken a piacokon továbbra is telepíthetik az ajánlatot a **[Terv beállításában](#plan-setup)** kiválasztott összes Azure-régióra). Az **Adóelengedés** gomb azokat az országokat jeleníti meg, amelyekben a Microsoft az Ön nevében átutalja az értékesítési és használati adót. A Kínába való közzététel olyan csomagokra korlátozódik, amelyek **ingyenesek** vagy **saját licencet (BYOL) hoznak.**

Ha már beállította a csomag árait amerikai dollárban (USD), és egy másik piaci helyszínt adott meg, az új piac árát az aktuális árfolyamok alapján számítja ki a rendszer. Közzététel előtt mindig tekintse át az egyes piacok árát. A módosítások mentése után tekintse át az árakat az **Exportárak (xlsx)** hivatkozás használatával.

Amikor eltávolít egy piacot, az aktív központi telepítések használatával az ügyfelek nem hozhatnak létre új központi telepítéseket, és nem skálázhatják a meglévő központi telepítések. A meglévő központi telepítések nem érinti.

#### <a name="pricing"></a>Díjszabás

**Licencmodell** – Válassza **a Használat alapú havi számlázott csomag** lehetőséget a csomag díjszabásának konfigurálásához, vagy saját licenc **hez,** hogy az ügyfelek a meglévő licenccel használhassák ezt a csomagot.

Használatalapú havi számlázott díjcsomag esetén használja az alábbi három árképzési bejegyzési lehetőség egyikét:

- **Magonként** – Adja meg az egy magra jutó árat amerikai dollárban (USD). Kiszámítjuk az alapméretenkénti árat, és az aktuális árfolyam on keresztül váltunk át helyi pénznemre.
- **Magméretenként** – Adja meg az árakat magméretenként USD-ben. Az árakat az aktuális átváltási árfolyamon váltjuk át helyi pénznemre.
- **Piaconként és magméretenként** – Adja meg az árakat az egyes magméretekre az összes piac számára. Az árakat táblázatból importálhatja.

> [!NOTE]
> Az árképzési adatok exportálásának engedélyezéséhez mentse az árképzési módosításokat. Miután közzétették a terv ben lévő piac árát, később nem módosíthatók. Győződjön meg róla, hogy ezek az árak helyesek, mielőtt közzétenné az árképzési táblázatot, és áttekinti az árat az egyes piacokon.

#### <a name="free-trial"></a>Ingyenes próbaidőszak

Egy hónapos vagy három hónapos ingyenes próbaverziót kínálhat ügyfeleinek.

#### <a name="visibility"></a>Láthatóság

Az egyes terveket úgy tervezheti meg, hogy mindenki vagy csak egy előre kiválasztott célközönség számára látható legyen. Rendeljen tagságokat ebben a korlátozott közönségben az Azure-előfizetés-azonosítók használatával.

**Nyilvános** - A terv mindenki számára látható.

**Privát közönség** – A terv csak egy előre kiválasztott közönség számára láthatóvá tétele. Miután privát csomagként közzétette, frissítheti a célközönséget, vagy nyilvánosra módosíthatja. Miután nyilvánosságra hoz egy tervet, annak nyilvánosnak kell maradnia; Nem változtathatod vissza privátra.

**Korlátozott közönség (Azure-előfizetés-azonosítók)** – Az Azure-előfizetés-azonosítók használatával hozzárendeli azt a közönséget, amely hozzáfér ehhez a privát csomaghoz. Szükség esetén adjon meg egy leírást az azure-előfizetés-azonosítóhoz rendelt minden egyes Azure-előfizetés-azonosítóról. CsV-számolótábla importálása esetén legfeljebb 10 előfizetési azonosítót adhat hozzá manuálisan, illetve 20 000-et. Az Azure-előfizetés-azonosítók GUID azonosítókként jelennek meg, a betűknek pedig kisbetűsnek kell lenniük.

> [!NOTE]
> A privát vagy korlátozott közönség eltér az **Előnézet** lapon megadott előnézeti közönségtől. Az előnézeti közönség még a marketplace-en közzétett élő adás _előtt_ hozzáférhet az ajánlathoz. Bár a privát közönség választása csak egy adott csomagra vonatkozik, az előnézeti közönség az összes (privát vagy nem) csomagot megtekintheti érvényesítési célból.

#### <a name="hide-plan"></a>Terv elrejtése

Ha a virtuális gép célja, hogy csak közvetetten, ha hivatkozott egy másik megoldás sablon vagy felügyelt alkalmazás, jelölje be ezt a jelölőnégyzetet, hogy tegye közzé a virtuális gépet, de elrejteni az ügyfelek keresnek és böngészik közvetlenül.

> [!NOTE]
> A rejtett csomagok nem támogatják az előnézeti hivatkozásokat.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

### <a name="technical-configuration"></a>Műszaki konfiguráció

Adja meg a rendszerképeket és a csomaghoz kapcsolódó egyéb technikai tulajdonságokat. További információt az Azure virtuális gép technikai eszközének létrehozása című [témakörben talál.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Ez a lap nem jelenik meg, ha úgy állította be ezt a csomagot, hogy a **Terv beállítása** lapon egy másik csomagból származó csomagokat használjon fel újra.

#### <a name="operating-system"></a>Operációs rendszer

- **Operációs rendszer család** – Válasszon **Windows** vagy **Linux** operációs rendszerből
- **Kiadás** vagy **szállító** – Válassza ki a Windows-kiadást vagy a Linux-forgalmazót
- **Operációs rendszer hez való rövid név** – Válasszon egy barátságos operációs rendszer nevét. Ez a név látható a vevők számára

#### <a name="recommended-vm-sizes"></a>Ajánlott virtuális gép méretei

Legfeljebb hat ajánlott virtuálisgép-méretet választhat ki az Azure Marketplace-en való megjelenítéshez.

#### <a name="open-ports"></a>Portok megnyitása

Nyilvános vagy privát portokat nyithat egy telepített virtuális gépen.

#### <a name="storage-option-for-deployment"></a>Tárolási lehetőség a telepítéshez

**Lemeztelepítési beállítás** – Válassza ki, hogy milyen típusú lemeztelepítést használhatnak a felhasználók a virtuális gép használatakor. A Microsoft azt javasolja, hogy a központi telepítést csak a Felügyelt lemez központi telepítésére korlátozza.

#### <a name="properties"></a>Tulajdonságok

**Támogatott hálózatkezelés támogatása** – Jelölje ki, ha a virtuális gép támogatja a [gyorsított hálózatkezelést.](https://go.microsoft.com/fwlink/?linkid=2124513)

#### <a name="vm-images"></a>Virtuálisgép-rendszerképek

Adja meg a lemezverzióját és a SAS URI-t a virtuálisgép-lemezképekhez. Minden virtuálisgép-lemezhez legfeljebb 16 adatlemez t adhat hozzá. Tervenként csak egy új lemezkép-verziót adjon meg egy adott beküldésben. A kép közzététele után nem szerkesztheti, de törölheti. A verzió törlése megakadályozza, hogy az új és a meglévő felhasználók is telepítsék a törölt verzió új példányát.

- **A lemezverzió** a lemezkép által biztosított verzió.
- **SAS URI** az a hely, az Azure Storage, ahol az operációs rendszer virtuális merevlemez tárolta.
- Adatlemez-lemezképek is VHD SAS URI-k az Azure-tárolóban tárolt.
- Beküldött adatokonként csak egy képet adjon hozzá egy tervhez.

Függetlenül attól, hogy melyik operációs rendszert használja, csak a megoldáshoz szükséges minimális számú adatlemezt adja hozzá. Az ügyfelek nem tudják eltávolítani azokat a lemezeket, amelyek a lemezkép részét képezik a telepítés időpontjában, de a telepítés során vagy azt követően bármikor hozzáadhatnak lemezeket.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget, és térjen vissza a Tervezés **áttekintése gombra.**

## <a name="resell-through-csps"></a>Viszonteladás csp-ken keresztül

Bővítse ajánlata elérését azáltal, hogy elérhetővé teszi azt a [felhőszolgáltató](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) programban részt veendő partnerek számára. Az All Bring Your Own License (BYOL) csomagok automatikusan bevannak jelentkezve; választhatja a nem BYOL-csomagokat.

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

## <a name="test-drive"></a>Tesztvezetés

Állítson be egy bemutatót (tesztvezetést), amely lehetővé teszi az ügyfelek számára, hogy vásárlás előtt kipróbálják az ajánlatot. Ha olyan bemutató környezetet szeretne létrehozni, amely lehetővé teszi az ügyfelek számára, hogy meghatározott ideig kipróbálják az ajánlatot, olvassa el az Ajánlat tesztelése a kereskedelmi piacon című [témakört.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Tesztvezetés engedélyezéséhez jelölje be a Tesztmeghajtó engedélyezése jelölőnégyzetet az [Ajánlat beállítása](#test-drive) lapon. Ha el szeretné távolítani a tesztvezetést az ajánlatból, törölje a jelet a jelölőnégyzetből.

További tesztmeghajtó-erőforrások:

- Gyakorlati tanácsok gyakorlati tanácsok
- Marketingre vonatkozó ajánlott eljárások
- Tesztmeghajtók – áttekintés (PDF; győződjön meg arról, hogy az előugró ablakok blokkolása ki van kapcsolva).

A folytatás előtt válassza **a Vázlat mentése** lehetőséget.

## <a name="review-and-publish"></a>Véleményezés és közzététel

Miután elvégezte az ajánlat összes szükséges szakaszát, elküldheti azt véleményezésre és közzétételre.

A portál jobb felső sarkában válassza a **Véleményezés és közzététel**lehetőséget.

Ezen az oldalon a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát.
  - **Nincs elindítva** – A szakasz nem indult el, és ki kell tölteni.
  - **Hiányos** – A szakasz hibákat tartalmaz, amelyeket ki kell javítani, vagy további információkat kell megadnia. A jelen dokumentum korábbi szakaszaiban útmutatást talál a szakasz frissítéséről.
  - **Kész** – A szakasz befejeződött, és nincsenek hibák. Az ajánlat minden szakaszának teljesnek kell lennie az ajánlat elküldése előtt.
- **Megjegyzések a tanúsításhoz** – Adjon vizsgálati utasításokat a minősítő csoportnak az alkalmazás megfelelő tesztelésének biztosításához. Adjon meg olyan kiegészítő megjegyzéseket, amelyek hasznosak az alkalmazás megértéséhez.

Az ajánlat közzétételre való elküldéséhez válassza a **Véleményezés és közzététel**lehetőséget.

Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előzetes verziója elérhető az ellenőrzéshez és a jóváhagyáshoz. Ha közzé szeretné tenni az ajánlatot a nyilvánosság számára (vagy ha privát ajánlatot, privát közönségnek), nyissa meg a Partnerközpontot, keresse meg az ajánlat **Áttekintés lapját,** és válassza az **Élő**lehetőséget. Az ajánlat állapota a lap tetején jelenik meg, amikor a közzététel folyamatban van.

### <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

A közzétételi folyamat **manuális érvényesítési** lépése az ajánlat és a hozzá tartozó technikai eszközök átfogó felülvizsgálatát jelenti. Ha ez a folyamat hibákat tár fel az ajánlattal kapcsolatban, kap egy tanúsítási jelentést, amely részletezi a problémákat. Egyszerűen tegye le a szükséges javításokat, és tegye közzé újra az ajánlatot.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **Ajánlat áttekintése** lap az ajánlat közzétételéhez szükséges (befejezett és folyamatban lévő) lépések vizuális megjelenítését, valamint az egyes lépések végrehajtásának elvégzéséhez szükséges lépéseket jeleníti meg.

Ez az oldal hivatkozásokat tartalmaz az ajánlaton végzett műveletek végrehajtására a kijelölés alapján. Például:

- Ha az ajánlat piszkozat - [Ajánlattervezet törlése](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Ha az ajánlat élő - [Hagyja abba az ajánlat értékesítését](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Ha még nem fejezte be a kiadói kijelentkezést - [Közzététel megszakítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Példák piactéri

Ezek a példák bemutatják, hogyan jelenik meg az ajánlat az Azure Marketplace-en.

### <a name="azure-marketplace-offer-details"></a>Az Azure Piactér ajánlatrészletei

![Példa az Azure Piactér részleteire](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Az Azure Piactér keresési eredményei

![Példa az Azure Piactér keresési részleteiképernyőre](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Az Azure Piactér csomagjának részletei

![Példa az Azure Marketplace-csomag részleteiképernyőre](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Az Azure Portal ajánlatrészletei

![Példa az Azure Portal részletes képernyőjére](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Az Azure Portal keresési eredményei

![Példa az Azure Portal keresési eredményeinek képernyőjére](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Az Azure Portal csomag részletei

![Példa az Azure Portal terv részleteinek képernyőre](media/avm-create6.png)

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piacon](https://aka.ms/UpdateOfferCM)
