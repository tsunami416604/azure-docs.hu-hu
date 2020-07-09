---
title: Azure-beli virtuális gépek ajánlatának létrehozása az Azure Marketplace-en
description: Megtudhatja, hogyan hozhat létre virtuálisgép-ajánlatot az Azure Marketplace-en a szükséges SKU-val.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: mingshen
ms.date: 06/17/2020
ms.openlocfilehash: fe53de2c81f9e9df5fd9270951f943b0b3505f1e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110980"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Azure-beli virtuális gépek ajánlatának létrehozása az Azure Marketplace-en

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé Azure-beli virtuális gépeket az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-en. A Windows-alapú és a Linux-alapú virtuális gépeket is tartalmazza, amelyek egy operációs rendszert, egy virtuális merevlemezt (VHD) és legfeljebb 16 adatlemezt tartalmaznak. 

Mielőtt elkezdené, [hozzon létre egy kereskedelmi piactér-fiókot a partner Centerben](create-account.md). Győződjön meg arról, hogy a fiók regisztrálva van a kereskedelmi piactér programban.

## <a name="introduction"></a>Introduction (Bevezetés)

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Az Azure Marketplace-en való közzététel előnyei

Ha közzéteszi az ajánlatait az Azure Marketplace-en, a következőket teheti:

- Népszerűsítse vállalatát a Microsoft Brand segítségével.
- Több mint 100 000 000 Office 365-és Dynamics 365-felhasználó és több mint 200 000 szervezet érhető el.
- Kiváló minőségű érdeklődőket érhet el ezekből a piactérekről.
- Szerezze be a Microsoft Field Sales and Sales csapatai által támogatott szolgáltatásokat.

### <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, tekintse át a [virtuálisgép-ajánlat közzétételi útmutatóját](../marketplace-virtual-machines.md) és az Azure-beli virtuális gép anyagát:

- Gyors útmutatók
  - [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates)
- Oktatóanyagok
  - [Linux rendszerű virtuális gépek](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows rendszerű virtuális gépek](../../virtual-machines/windows/tutorial-manage-vm.md)
- Példák
  - [Azure CLI-minták Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/powershell-samples.md)
  - [Azure CLI-minták Windows rendszerű virtuális gépekhez](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell Windows rendszerű virtuális gépekhez](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>A technikai ismeretek alapjai

Az ajánlatok tervezésének, kiépítésének és tesztelésének folyamata időt vesz igénybe, és az Azure platformon és az ajánlat létrehozásához használt technológiákban is szaktudást igényel.

A mérnöki csapatának alapvető ismeretekkel kell rendelkeznie a következő Microsoft-technológiákkal kapcsolatban:

- [Azure-szolgáltatások](https://azure.microsoft.com/services/)
- [Azure-alkalmazások tervezése és architektúrája](https://azure.microsoft.com/solutions/architecture/)
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és [Azure hálózatkezelés](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali panelen válassza a **kereskedelmi piactér**  >  **áttekintése**elemet.
3. Az **Áttekintés** lapon válassza az **új ajánlat**Azure-beli  >  **virtuális gép**lehetőséget.

    ![Képernyőkép a bal oldali ablaktábla menüpontok és az "új ajánlat" gomb megjelenítéséhez.](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végrehajtott módosítások csak az ajánlat ismételt közzététele után jelennek meg az Azure piactéren. A módosítások végrehajtása után ügyeljen arra, hogy mindig újra tegye közzé az ajánlatokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító az Azure Marketplace-ajánlathoz tartozó webcímek ügyfelei számára látható, Azure PowerShell és az Azure CLI-ben, ha van ilyen.
- Csak kisbetűket és számokat használjon. Az azonosító kötőjeleket és aláhúzást tartalmazhat, de nem tartalmaz szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Az ajánlat alias a partner Centerben az ajánlathoz használt név.

- Ez a név nem használható az Azure Marketplace-en. Eltér az ajánlat nevétől és az ügyfelek számára megjelenített egyéb értéktől.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="test-drive"></a>Tesztverzió

A *tesztvezetés* nagyszerű lehetőség arra, hogy ajánlatot nyújtson a potenciális ügyfeleknek. Lehetővé teszi számukra a "kipróbálás előtt" lehetőséget, amely növelheti a konverziót, és nagymértékben minősített érdeklődőket hozhat. További információ: [Mi az a test Drive?](../what-is-test-drive.md).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a teszt meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

További tesztelési meghajtó erőforrásai:

- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketingre vonatkozó ajánlott eljárások](../what-is-test-drive.md)
- [A test Drive – áttekintés letöltése](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-fájl (ellenőrizze, hogy ki van-e kapcsolva az előugró ablak blokkolása).

### <a name="customer-leads"></a>Ügyfél-érdeklődők

Ha közzéteszi az ajánlatot a kereskedelmi piactéren a partner centerrel, kapcsolja össze azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket. Ha egy tesztelési meghajtót szeretne engedélyezni, a CRM-hez való csatlakozásra van szükség (lásd az előző szakaszt). Ellenkező esetben a CRM-hez való csatlakozás nem kötelező.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A partner Center a következő CRM-rendszereket támogatja:
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) ügyfél-engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a listán, az [Azure Table Storage](commercial-marketplace-lead-management-instructions-azure-table.md) vagy egy [https-végpont](commercial-marketplace-lead-management-instructions-https.md) használatával tárolhatja az ügyfél-érdeklődő adatait. Ezután exportálja az adatait a CRM-rendszerbe.

1. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
1. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, a Microsoft ellenőrzi a kapcsolatot, és elküld egy tesztelési érdeklődőt. Noha az ajánlat előzetes megtekintése előtt is elérhetővé válik, az érdeklődői kapcsolatok teszteléséhez próbálja meg saját maga is üzembe helyezni az ajánlatot az előnézeti környezetben.
1. Győződjön meg arról, hogy a vezető célhoz való kapcsolat frissül, hogy ne veszítse el az érdeklődőket.

1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon megadhatja azokat a kategóriákat, amelyek az ajánlat az Azure Marketplace-en, az alkalmazás verziószámán és az ajánlatát támogató jogi szerződések szerint csoportosíthatók.

### <a name="category"></a>Kategória

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a piactér megfelelő keresési területein helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában. A következők szerint válasszon:

- Legalább egy és legfeljebb két kategória, beleértve az elsődleges és a másodlagos kategóriát is (opcionális).
- Legfeljebb két alkategória lehet minden elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható**lehetőséget.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](../gtm-offer-listing-best-practices.md). A virtuális gép az Azure Marketplace-en mindig a **számítási** kategóriában jelenik meg.

### <a name="legal"></a>Jogi tudnivalók

Ajánlati feltételeket és kikötéseket kell megadnia ügyfeleinek. Erre két lehetősége van:

- **Saját használati feltételek használata**

   A saját használati feltételeinek megadásához a **feltételek és kikötések** mezőben adja meg a legfeljebb 10 000 karakter hosszúságú szöveget. Ha további leírásra van szüksége, adjon meg egy webcímet a feltételek és kikötések számára. Az ügyfelek aktív hivatkozásként jelennek meg.

   Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

- **Szabványos szerződés használata a Microsoft kereskedelmi piactérről**

   Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést biztosít a kereskedelmi piactéren. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell beolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

   Használja a standard szerződést a **szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzet bejelölésével, majd az előugró ablakban válassza az **elfogadás** lehetőséget (Előfordulhat, hogy a megtekintéshez fel kell görgetni).

   ![Képernyőfelvétel a partneri Központ jogi paneljéről a "standard szintű szerződés használata a Microsoft kereskedelmi piactérről" jelölőnégyzetből.](media/use-standard-contract.png)

  > [!NOTE]
  > Miután közzétette az ajánlatot a kereskedelmi piactérre vonatkozó standard szerződés használatával, nem használhatja a saját egyéni használati feltételeit. A standard szerződéssel vagy a saját használati feltételeivel is kínálhat megoldást.

  További információ: [standard szintű szerződés a Microsoft kereskedelmi piactéren](../standard-contract.md). Töltse le a [standard szintű szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlját (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása).

  **Standard szintű szerződés módosításai**

  A standard szintű szerződések módosításai lehetővé teszik az egyszerűség általános szerződési feltételeinek kiválasztását, valamint a termék vagy vállalat használati feltételeinek létrehozását. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a szabványos Microsoft-szerződést. Két típusú módosítás létezik:

  * **Univerzális módosítások**: ezeket a módosításokat a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az ajánlat minden ügyfelének megjelenik a vásárlás folyamatában. Az ügyfeleknek el kell fogadniuk a standard szerződés és a módosítások feltételeit, mielőtt felhasználhatják az ajánlatot. Ajánlathoz egyetlen általános módosítást is megadhat. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy a Azure Portal a felderítési és vásárlási folyamat során az ügyfelek számára jelennek meg.

  * **Egyéni módosítások**: a standard szerződés ezen speciális módosításai az Azure-bérlők azonosítói alapján meghatározott ügyfelekre vonatkoznak. Kiválaszthatja a célként használni kívánt bérlőt. Az ajánlat vásárlási folyamatában csak a bérlő ügyfelei jelennek meg az egyéni módosítási feltételekkel. Az ügyfeleknek el kell fogadniuk a standard szerződés és a módosítások feltételeit, mielőtt felhasználhatják az ajánlatot.

    1. Először válassza az **Egyéni módosítási feltételek hozzáadása (legfeljebb 10)** lehetőséget. Ajánlatunk legfeljebb tíz egyéni módosítási kifejezést biztosíthat. Tegye a következőket:

       a. Adja meg saját módosítási feltételeit az **Egyéni módosítási feltételek** mezőben. Korlátlan számú karaktert adhat meg. Csak az ezen egyéni feltételekhez megadott bérlői azonosítók ügyfelei láthatják az ajánlat vásárlási folyamatát a Azure Portalban.

       b. Szükséges Adja meg a **bérlői azonosítókat**. Minden egyéni módosítás legfeljebb 20 bérlői azonosítót célozhat meg. Ha egyéni módosítást ad hozzá, meg kell adnia legalább egy bérlői azonosítót, amely azonosítja az ügyfelet az Azure-ban. Az ügyfél az Azure-ban az **Azure Active Directory**tulajdonságok lehetőség kiválasztásával találja meg ezt  >  **Properties**. A címtár-azonosító értéke a bérlő azonosítója (például 50c464d3-4930-494c-963c-1e951d15360e). A szervezet bérlői AZONOSÍTÓját is megtalálhatja az ügyfélhez tartozó tartománynév-webcímek használatával, [Mi a Microsoft Azure és az Office 365-bérlő azonosítója?](https://www.whatismytenantid.com/).

       c. Választható Adjon meg egy rövid **leírást** a BÉRLŐi azonosítóhoz, amely segít azonosítani a módosítással megcélzott ügyfelet.

        > [!NOTE]
        > Ez a két típusú módosítás egymással párosítva van. Azok az ügyfelek, akik egyéni módosításokat céloznak meg, a vásárlás során általános módosításokat is megkapnak a standard szerződésben.

    1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Az **ajánlati lista** lapon megadhatja az ajánlat részleteit, például az ajánlat nevét, a leírását, a hivatkozásokat és a névjegyeket.

> [!NOTE]
> Az ajánlat tartalmait, például a leírást, a dokumentumokat, a képernyőképeket és a használati feltételeket nem kell angolul megadnia, feltéve, hogy az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a (z) verzióban érhető el \<non-English language> ." Megadhat egy URL-címet is, amely egy olyan webhelyre mutató hivatkozást tartalmaz, amely nem az ajánlatban szereplő tartalomban használt tartalommal rendelkezik.

### <a name="marketplace-details"></a>Piactér – részletek

#### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címeként. Ez a mező az ajánlat **aliasa** mezőben megadott névvel van feltöltve az ajánlat létrehozásakor. A nevet később módosíthatja. A név:

- Lehet védjeggyel ellátott. A védjegyek és a szerzői jogi szimbólumok is megadhatók.
- Legfeljebb 50 karakterből állhat.
- Nem szerepelhetnek hangulatjelek.

#### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását, amely az Azure Marketplace keresési eredményei között jelenik meg. Akár 100 karaktert is tartalmazhat.

#### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlatát, hogy megjelenjen az Azure Marketplace keresési eredményei között. Akár 256 karaktert is tartalmazhat.

#### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának webcímet (URL-címét). Győződjön meg arról, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Érvényes adatvédelmi szabályzatot is közzé kell tennie a webhelyén.

### <a name="useful-links"></a>Hasznos hivatkozások

Adja meg az ajánlatával kapcsolatos kiegészítő online dokumentumokat. Hivatkozás hozzáadásához válassza a **hivatkozás hozzáadása**lehetőséget, majd hajtsa végre a következő mezőket:

- **Név**: az ügyfelek a részleteket tartalmazó oldalon fogják látni a nevet.
- **Hivatkozás (URL)**: adjon meg egy hivatkozást, amely lehetővé teszi az ügyfelek számára az online dokumentum megtekintését.

### <a name="customer-support-links"></a>Ügyfélszolgálati hivatkozások

Adja meg a támogatási webhelyet, ahol az ügyfelek elérheti a támogatási csapatot.

- Azure globális támogatási webhely
- Azure Government támogatási webhely

### <a name="partner-support-contact"></a>Partneri támogatási kapcsolattartó

Adja meg a Microsoft-partnerek számára a támogatási jegy megnyitásakor használandó kapcsolattartási adatokat. Ez az információ nem szerepel az Azure Marketplace-en.

- Name
- E-mail
- Telefon

### <a name="engineering-contact"></a>Mérnöki kapcsolattartó

Adja meg a Microsoft számára az ajánlattal kapcsolatos problémák esetén használandó kapcsolattartási adatokat, beleértve a minősítéssel kapcsolatos problémákat is. Ez az információ nem szerepel az Azure Marketplace-en.

- Name
- E-mail
- Telefon

### <a name="azure-marketplace-media"></a>Az Azure Marketplace adathordozója

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képek miatt a rendszer elutasítja a kérelmet.

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="azure-marketplace-logos"></a>Azure Marketplace-logók

Adja meg az ajánlat emblémájának PNG-fájlját a következő négy képdimenzióval:

- **Kicsi** (48 &times; 48 képpont)
- **Közepes** (90 &times; 90 képpont)
- **Nagyméretű** (216 &times; 216 képpont)
- **Széles** (255 &times; 115 képpont)

Mind a négy emblémát meg kell adni, és különböző Azure Marketplace-listákban jelennek meg.

#### <a name="screenshots"></a>Képernyőképek

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 &times; 720 képpont méretűnek és PNG formátumúnak kell lennie. Minden képernyőképnek tartalmaznia kell egy feliratot.

#### <a name="videos"></a>Videók

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. A videókat külső videó szolgáltatásban kell tárolni. Adja meg a videó nevét, webcímeit és miniatűr PNG-képét, amely 1280 &times; 720 képpont.

További információforrások a piactéren: ajánlott [eljárások a Piactéri ajánlatokhoz](../gtm-offer-listing-best-practices.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="preview"></a>Előnézet

Válassza a **Preview (előnézet** ) fület, majd egy korlátozott **előzetes** verzióra kiválaszthatja az ajánlat érvényességét, mielőtt közzéteszi azt a szélesebb körű kereskedelmi piactéren.

> [!IMPORTANT]
> Miután ellenőrizte az ajánlatát az **előzetes** verziójú ablaktáblán, válassza az **élő** verzió lehetőséget az ajánlat közzétételéhez a kereskedelmi piactér nyilvános célközönsége számára.

Az előnézeti közönséget az Azure-előfizetés AZONOSÍTÓjának GUID azonosítói határozzák meg, és mindegyikhez opcionális leírást is megadhat. Ezen mezők egyikét sem láthatja az ügyfelek. Az Azure-előfizetés AZONOSÍTÓját a Azure Portal **előfizetések** oldalán találja.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10 azonosítóval) vagy egy CSV-fájl feltöltésével (legfeljebb 100 azonosítóval). Az előfizetés-azonosítók hozzáadásával meghatározhatja, hogy ki láthatja el az ajánlatot az élő közzététel előtt. Ha az ajánlata már aktív, akkor továbbra is megadhatja az előnézeti célközönséget az ajánlat változásainak és frissítéseinek teszteléséhez.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége az Azure Marketplace-en való élő közzététel _előtt_ elérheti az ajánlatát. Az előzetes verzió célközönsége megtekintheti és érvényesítheti az összes csomagot, beleértve azokat is, amelyek csak a privát közönség számára lesznek elérhetők, miután az ajánlatát teljes mértékben közzétette az Azure Marketplace-en. A saját célközönség (a csomag **díjszabása és a rendelkezésre állási** ablaktáblában definiált) kizárólagos hozzáféréssel rendelkezik egy adott csomaghoz.

A következő szakasz folytatásához válassza a **Piszkozat mentése** elemet.

## <a name="plan-overview"></a>A terv áttekintése

A partner centeren belül a különböző csomagokra vonatkozó lehetőségek is megadhatók. Ezeket a csomagokat korábban SKU-ként emlegetik. Az ajánlathoz legalább egy csomag szükséges, amely a bevételi közönség, az Azure-régió, a funkciók vagy a virtuálisgép-rendszerképektől függően változhat.

A csomagok létrehozása után válassza a **terv áttekintés** fület a megjelenítéshez:

- Csomag neve
- Licencelési modellek
- Közönség (nyilvános vagy privát)
- Jelenlegi közzétételi állapot
- Elérhető műveletek

A **terv áttekintő** ablaktábláján elérhető műveletek a csomag aktuális állapotától függően változhatnak.

- Ha a terv állapota vázlat, válassza a **Piszkozat törlése**lehetőséget.
- Ha a terv állapota élőben van közzétéve, válassza az **eladási terv leállítása** vagy a **privát célközönség szinkronizálása**lehetőséget.

### <a name="create-a-new-plan"></a>Új csomag létrehozása

Válassza a felül az **új csomag létrehozása** lehetőséget. Megjelenik az **új terv** párbeszédpanel.

A **terv azonosítója** mezőben hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjában. Ez az azonosító a termék webcímen lévő ügyfelek számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásokat, illetve legfeljebb 50 karaktert használjon.

> [!NOTE]
> A terv azonosítója a **Létrehozás**gombra kattintva nem módosítható.

A **terv neve** mezőbe írja be a csomag nevét. Az ügyfelek akkor látják ezt a nevet, amikor döntik el, hogy melyik tervet kívánják kiválasztani az ajánlaton belül. Hozzon létre egy egyedi nevet, amely egyértelműen rámutat a csomagok közötti különbségekre. Például megadhatja a **Windows Servert** utólagos *Pay-as-you-go*elszámolású, *BYOL*, *speciális*és *vállalati* csomagokkal.

Válassza a **Létrehozás** lehetőséget.

### <a name="plan-setup"></a>Csomag beállítása

Állítsa be a terv típusának magas szintű konfigurációját, adja meg, hogy az újrahasznosítsa-e egy másik csomag technikai konfigurációját, és azonosítsa azokat az Azure-régiókat, ahol a terv elérhető. Az itt megadott beállítások határozzák meg, hogy mely mezők jelenjenek meg ugyanazon csomag más ablaktábláján.

#### <a name="reuse-a-technical-configuration"></a>Technikai konfiguráció újbóli használata

Ha több azonos típusú csomaggal rendelkezik, és a csomagok azonosak egymás között, akkor kiválaszthatja, hogy a **terv újrahasznosítja a technikai konfigurációt egy másik csomagból**. Ez a beállítás lehetővé teszi, hogy az ajánlathoz tartozó más csomagok valamelyikét kiválassza, és lehetővé teszi a technikai konfigurációjának újbóli használatát.

> [!NOTE]
> Ha egy másik csomag technikai konfigurációját használja, a teljes **technikai konfiguráció** lap eltűnik ebből a csomagból. A csomagra vonatkozó technikai konfiguráció részleteit, beleértve a jövőbeli frissítéseket is, a tervhez is használni fogjuk. Ez a beállítás a terv közzététele után nem módosítható.

#### <a name="azure-regions"></a>Azure-régiók

A tervet legalább egy Azure-régióban elérhetővé kell tenni.

Válassza ki az **Azure globális** lehetőséget, hogy a tervet elérhetővé tegye az összes olyan globális Azure-régióban, amely kereskedelmi Piactéri integrációt tartalmaz. További információ: a [földrajzi Elérhetőség és a pénznemek támogatása](../marketplace-geo-availability-currencies.md).

Válassza a **Azure Government** lehetőséget, hogy a terv elérhető legyen a [Azure Government](../../azure-government/documentation-government-welcome.md) régióban. Ez a régió szabályozott hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi szerveinek ügyfeleinek, valamint az azok kiszolgálására jogosult partnereinknek. Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az USA-ban található).

Mielőtt közzéteszi a [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md), tesztelje és érvényesítse a tervet a környezetben, mert bizonyos végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez kérjen egy próbaverziós fiókot a [Microsoft Azure Government próbaverzió](https://azure.microsoft.com/global-infrastructure/government/request/) oldaláról.

> [!NOTE]
> Miután közzétette és elérhetővé tette a csomagot egy adott Azure-régióban, nem távolíthatja el a régiót.

#### <a name="azure-government-certifications"></a>Azure Government minősítések

Ez a beállítás csak akkor látható, ha az előző szakaszban az Azure-régiót választotta **Azure Government** .

Azure Government szolgáltatások kezelik bizonyos kormányzati előírások és követelmények hatálya alá eső adatmennyiséget. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek lehetnek közvetlenül a programra mutató hivatkozások vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="plan-listing"></a>Csomag listázása

Ebben a szakaszban a csomag listázási részleteit konfigurálja. Ez a panel a konkrét információkat jeleníti meg, amelyek eltérőek lehetnek az azonos ajánlatban található többi csomagtól.

#### <a name="plan-name"></a>Csomag neve

Ez a mező a létrehozáskor a tervhez megadott névvel van feltöltve. Ez a név jelenik meg az Azure Marketplace-en a csomag címeként. Legfeljebb 100 karakter hosszú lehet.

#### <a name="plan-summary"></a>Csomag összegzése

Adja meg a csomag rövid összefoglalását, nem az ajánlatot. Ez az Összegzés legfeljebb 100 karakter hosszú lehet.

#### <a name="plan-description"></a>Csomag leírása

Írja le, hogy mi teszi ezt a csomagot egyedivé, és ismertesse az ajánlaton belüli csomagok közötti különbségeket. Csak a csomag leírása, nem az ajánlat. A csomag leírása legfeljebb 2 000 karaktert tartalmazhat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Ezen az ablaktáblán a következőket konfigurálja:

- Azok a piacok, ahol ez a csomag elérhető.
- Az óránkénti díj.
- Azt határozza meg, hogy a terv mindenki számára elérhető legyen-e, vagy csak bizonyos ügyfelek számára (privát közönség számára).

#### <a name="markets"></a>Piacok

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be az összes olyan piaci hely jelölőnégyzetét, ahol a csomag megvásárolható. (Ezekben a piacokon a felhasználók továbbra is üzembe helyezhetik az ajánlatot a ["csomag beállítása"](#plan-setup) szakaszban kiválasztott összes Azure-régióban.) Az **átutalt adó** gomb azokat az országokat/régiókat jeleníti meg, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és használati adót. A Kínában való közzététel olyan csomagokra korlátozódik, amelyek *ingyenesek* vagy *saját licencek* (BYOL).

Ha már beállította a csomag árát az amerikai dollár (USD) pénznemben, és újabb piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A módosítások mentése után tekintse át a díjszabást az **export Prices (xlsx)** lehetőség kiválasztásával.

Ha eltávolít egy piacot, az adott piacon aktív üzemelő példányokat használó ügyfelek nem hozhatnak létre új központi telepítéseket, és nem méretezhetők a meglévő telepítések. A meglévő központi telepítések nincsenek hatással a rendszerre.

#### <a name="pricing"></a>Díjszabás

A **licencelési modell**esetében válassza a **használati alapú havi számlázott csomag** lehetőséget a csomag díjszabásának konfigurálásához, vagy válassza a **saját licenc** használata lehetőséget, hogy az ügyfelek ezt a csomagot használják meglévő licenccel.

A használati alapú havi számlás csomag esetében a következő három díjszabási bejegyzés közül választhat:

- **/Mag**: az USD-es alapszintű díjszabást adja meg. A Microsoft a jelenlegi árfolyam alapján kiszámítja a díjszabást az alapméret szerint, és helyi pénznemre alakítja.
- **/Alapméret**: az USD-ben elérhető alapméretet biztosító díjszabás. A Microsoft kiszámítja a díjszabást, és helyi pénznemre konvertálja az aktuális árfolyam használatával.
- **/Piac és alapméret**: az összes piac alapméretének díjszabását adja meg. Az árakat importálhatja egy táblázatból.

> [!NOTE]
> Mentse a díjszabási változásokat, hogy engedélyezze a díjszabási adatexportálást. Miután közzétette a csomag egy piacának árát, később nem módosítható. Annak érdekében, hogy a közzététel előtt a díjak megfelelőek legyenek, exportálja a díjszabási táblázatot, és tekintse át az árakat az egyes piacokon.

#### <a name="free-trial"></a>Ingyenes próbaverzió

Az ügyfeleknek egy-vagy három hónapos *ingyenes próbaverziót* is kínálunk.

#### <a name="visibility"></a>Láthatóság

Megtervezheti, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy kijelölt közönség számára. Az Azure-előfizetési azonosítók használatával társíthat tagságokat ebben a korlátozott célközönségben.

**Nyilvános**: a terv mindenki számára megtekinthető.

**Privát célközönség**: a terv csak az előkiválasztott közönség számára látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy megváltoztathatja nyilvánosra. Miután megnyitotta a csomagot, nyilvánosnak kell maradnia. Nem módosítható egy privát csomagra.

> [!NOTE]
> A privát vagy korlátozott célközönség nem azonos az **előnézeti ablaktáblán** meghatározott előnézeti célközönséggel. Az előzetes verzió célközönsége az Azure Marketplace-en való élő közzététel _előtt_ elérheti az ajánlatát. Bár a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes magán-és nyilvános csomagot megtekintheti érvényesítés céljából.

**Korlátozott célközönség (Azure-előfizetési azonosítók)**: rendelje hozzá azt a célközönséget, amely hozzáférhet ehhez a privát csomaghoz az Azure-előfizetési azonosítók használatával. Igény szerint az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását is megadhatja. Akár 10 előfizetés-azonosítót is hozzáadhat manuálisan vagy akár 20 000 azonosítóhoz, ha CSV-táblázatot importál. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és minden betűnek kisbetűsnek kell lennie.

>[!Note]
>A privát ajánlatok nem támogatottak a Cloud Solution Provider program (CSP) viszonteladóján keresztül létesített Azure-előfizetések esetében.


#### <a name="hide-a-plan"></a>Csomag elrejtése

Ha a virtuális gépet csak közvetetten kell használni, ha egy másik megoldási sablonon vagy felügyelt alkalmazáson keresztül hivatkozik rá, jelölje be ezt a jelölőnégyzetet a virtuális gép közzétételéhez, de az olyan ügyfelektől, akik közvetlenül keresnek vagy böngészhetnek.

> [!NOTE]
> A rejtett csomagok nem támogatják az előzetes verziójú hivatkozásokat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="technical-configuration"></a>Technikai konfiguráció

Adja meg a csomaghoz társított képeket és egyéb technikai tulajdonságokat. További információ: Azure-beli [virtuális gép technikai eszközének létrehozása](create-azure-container-technical-assets.md).

> [!NOTE]
> A **technikai konfiguráció** lap nem jelenik meg, ha úgy konfigurálta ezt a csomagot, hogy a csomagok újrafelhasználását egy másik csomagból a **terv beállítása** lapon.

#### <a name="operating-system"></a>Operációs rendszer

Az **operációs rendszer** panelen tegye a következőket:

- **Operációsrendszer-család**esetén válassza ki a **Windows** vagy **Linux** operációs rendszert.
- A **kiadás** vagy a **szállító**esetében válassza a Windows kiadás vagy a Linux gyártó elemet.
- Az operációs rendszer **felhasználóbarát neve**mezőben adjon meg egy felhasználóbarát operációsrendszer-nevet. Ez a név jelenik meg az ügyfelek számára.

#### <a name="recommended-vm-sizes"></a>Ajánlott virtuálisgép-méretek

Az Azure piactéren megjelenítendő legfeljebb hat ajánlott virtuálisgép-méret közül választhat.

#### <a name="open-ports"></a>Portok megnyitása

Nyisson meg nyilvános vagy magánhálózati portot egy telepített virtuális gépen.

#### <a name="storage-option-for-deployment"></a>Tárolási lehetőség az üzembe helyezéshez

A **lemez központi telepítése beállításnál**válassza ki, hogy az ügyfelek milyen típusú központi telepítést használhatnak a virtuális géphez. A Microsoft javasolja, hogy csak a **felügyelt lemezes telepítésre** korlátozza a központi telepítést.

#### <a name="properties"></a>Tulajdonságok

A **gyorsított hálózatkezelés támogatásához**válassza ki, hogy a virtuális gép támogatja-e a [gyorsított hálózatkezelést](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>VM-lemezképek

Adja meg a lemez verziószámát és a virtuálisgép-lemezképek megosztott elérési aláírásának (SAS) URI-JÁT. Minden virtuálisgép-rendszerképhez akár 16 adatlemezt is hozzáadhat. Egy adott beadványban csak egy új rendszerkép-verziót adjon meg. A rendszerkép közzététele után nem szerkesztheti, de törölheti is. Egy verzió törlése megakadályozza, hogy az új és a meglévő felhasználók is üzembe lehessen helyezni a törölt verzió új példányát.

- **Lemez verziója**: a megadott lemezkép verziója.
- **Sas URI**: az Azure Storage-fiók azon helye, ahová az operációs rendszer VHD-jét tárolta. A SAS URI beszerzésével kapcsolatos további információkért lásd: [a megosztott hozzáférési aláírás URI-ja](get-sas-uri.md)a virtuálisgép-rendszerképhez.
- Az adatlemez-lemezképek az Azure Storage-fiókokban tárolt VHD közös hozzáférési aláírási URI-k is.
- Egy csomagban való beküldéshez csak egy képet adjon hozzá.

A használt operációs rendszertől függetlenül csak a megoldás által igényelt adatlemezek minimális számát adja hozzá. Az üzembe helyezés során az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget, és térjen vissza a **terv áttekintéséhez**.

## <a name="resell-through-csps"></a>Viszonteladás a CSP-n keresztül

A [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) program partnerei számára elérhetővé teszi az ajánlat elérhetőségét. Az összes saját Licences (BYOL) csomag automatikusan bekerül a programba. Dönthet úgy is, hogy nem BYOL terveket is választ.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive"></a>Tesztverzió

Állítson be egy bemutatót vagy egy *tesztelési meghajtót*, amely lehetővé teszi, hogy az ügyfelek a megvásárlásuk előtt meghatározott időn belül kipróbálják az ajánlatot. Az ügyfelek számára bemutató környezet létrehozásához tekintse meg [a kereskedelmi piactéren elérhető tesztelési ajánlatokat](test-drive.md).

A tesztelési meghajtó engedélyezéséhez jelölje be a **Test Drive engedélyezése** jelölőnégyzetet az **ajánlat beállítása** ablaktáblán. Ha el szeretné távolítani a teszt meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

További tesztelési meghajtó erőforrásai:

- [Marketingre vonatkozó ajánlott eljárások](../what-is-test-drive.md)
- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- A [Test Drive áttekintése](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-fájl (ellenőrizze, hogy ki van-e kapcsolva az előugró ablak blokkolása)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="review-and-publish"></a>Áttekintés és közzététel

Miután elvégezte az ajánlat összes szükséges részét, elküldheti azt véleményezésre és közzétételre.

A jobb felső sarokban válassza a **felülvizsgálat és közzététel**lehetőséget.

Ezen az ablaktáblán a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát:

  - **Nem indult el**: a szakasz nem indult el, és el kell végezni.
  - **Hiányos**: a szakasz olyan hibákat tartalmaz, amelyeknek rögzítettnek kell lennie, vagy további információkat kell megadnia. A hiányos információk frissítésével kapcsolatos útmutatásért tekintse meg a cikk korábbi szakaszait.
  - **Kész**: a szakasz befejeződött, és nincsenek hibák. Az ajánlat elküldése előtt az ajánlat összes részének teljesnek kell lennie.
- Adja meg a minősítési csapat **minősítését** , hogy az alkalmazás megfelelően legyen tesztelve. Ide tartoznak a tesztelési utasítások és a kiegészítő megjegyzések, amelyek segítségével a csapat megismerheti az alkalmazást.

Az ajánlat közzétételre való elküldéséhez válassza a **felülvizsgálat és közzététel**lehetőséget.

A Microsoft e-mailben értesítést küld arról, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Ha közzé szeretné tenni az ajánlatot a nyilvános (vagy privát ajánlatként, közzéteheti azt egy privát célközönségben), lépjen a partner Center webhelyre, lépjen az ajánlat **Áttekintés** lapjára, majd válassza a **Go-Live**lehetőséget. Az ajánlat állapota az oldal tetején jelenik meg, amikor a közzététel folyamatban van.

### <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

A közzétételi folyamat **manuális érvényesítési** lépése az ajánlat és a hozzá kapcsolódó technikai eszközök átfogó áttekintését jeleníti meg. Ha ez a folyamat felfedi az ajánlatával kapcsolatos hibákat, a rendszer egy, a problémákat részletező minősítési jelentést fog kapni. Egyszerűen végezze el a szükséges helyesbítéseket, és tegye közzé újból az ajánlatot.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **ajánlat áttekintése** lapon láthatók azok a lépések, amelyek az ajánlat közzétételéhez szükségesek, valamint az egyes lépések végrehajtásának elvégzéséhez szükséges lépések.

Ez az oldal olyan hivatkozásokat is tartalmaz, amelyek segítenek az ajánlattal való együttműködésben az állapottól függően:

- Ha az ajánlat egy Piszkozat: [törlési ajánlat törlése](update-existing-offer.md#delete-a-draft-offer))
- Ha az ajánlat élő: [az ajánlat eladásának leállítása](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető el: [Go-Live](publishing-status.md#publisher-approval))
- Ha még nem fejezte be a közzétevő kijelentkezését: [Közzététel megszakítása](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace-példák

Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk az Azure Marketplace piactéren:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat az Azure piactéren.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Nagyméretű embléma
2. Price
3. Kategóriák
4. használati feltételei
5. Adatvédelmi szabályzat címe (hivatkozás)
6. Ajánlat neve
7. Leírás
8. Hasznos hivatkozások
9. Képernyőképek/videók

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk az Azure Marketplace keresési eredményei között:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat az Azure piactér keresési eredményei között.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Kis embléma
2. Ajánlat neve
3. Keresési eredmények összegzése
4. Próbaverzió

<br>Íme egy példa az Azure Marketplace-csomag részleteire:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Bemutatja az Azure Marketplace-csomag részleteit.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Csomag neve és összegzése
2. VIRTUÁLIS gépek méretének ajánlása
3. Díjszabás megtervezése

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Azure Portalban:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Bemutatja, hogyan jelennek meg az ajánlat a Azure Portalban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Name
2. Leírás
3. Hasznos hivatkozások
4. Képernyőképek/videók

<br>Az alábbi példa bemutatja, hogyan jelennek meg az ajánlati információk a Azure Portal keresési eredmények között:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Azure Portal keresési eredmények között.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Kis embléma
2. Ajánlat neve
3. Keresési eredmények összegzése

<br>Íme egy példa a Azure Portal terv részleteire:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="A Azure Portal terv részleteit mutatja be.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Csomag neve
2. Csomag leírása

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](update-existing-offer.md)
