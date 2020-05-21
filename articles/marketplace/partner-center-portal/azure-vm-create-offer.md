---
title: Azure-beli virtuális gépek ajánlatának létrehozása az Azure Marketplace-en
description: Megtudhatja, hogyan hozhat létre virtuálisgép-ajánlatokat az Azure Marketplace-en a szükséges SKU-val.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: ab41d3619150ace8e55702792d5974060133d5c1
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726179"
---
# <a name="create-an-azure-virtual-machine-offer-in-azure-marketplace"></a>Azure-beli virtuális gépek ajánlatának létrehozása az Azure Marketplace-en

Ez a cikk bemutatja, hogyan hozhat létre és tehet közzé Azure-beli virtuális gépeket az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-en. A Windows-és Linux-alapú virtuális gépekre is vonatkozik, amelyek operációs rendszert, virtuális merevlemezt (VHD) és legfeljebb 16 adatlemezt tartalmaznak. A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

## <a name="introduction"></a>Introduction (Bevezetés)

### <a name="publishing-benefits"></a>Közzétételi előnyök

Az Azure Marketplace-en való közzététel a következő előnyöket nyújtja:

- Népszerűsítse vállalatát a Microsoft Brand használatával
- Az Azure Marketplace-en keresztül elérheti a 100 000 000-es Office 365-és Dynamics 365-felhasználókat és több mint 200 000 szervezetet
- Kiváló minőségű érdeklődők beszerzése ezekről a piactérekről
- A Microsoft és a kereskedelmi csoportok által támogatott szolgáltatások beszerzése

### <a name="before-you-begin"></a>Előkészületek

Ha még nem tette meg, tekintse át a [virtuálisgép-ajánlat közzétételi útmutatóját](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) és az Azure-beli virtuális gép anyagát:

- Gyors útmutatók
  - [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates)
- Oktatóanyagok
  - [Linux rendszerű virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Windows rendszerű virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Minták
  - [Azure CLI-minták Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI-minták Windows rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell Windows rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>A technikai ismeretek alapjai

Ezeknek az eszközöknek a tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.

A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

- Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése
- [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
- Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure hálózatkezelésének](https://azure.microsoft.com/services/?filter=networking#networking) gyakorlati ismerete

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**Azure-beli  >  **virtuális gép**lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-azure-vm.png)

> [!NOTE]
> Az ajánlat közzétételét követően a partner Centerben végzett módosítások csak az ajánlat ismételt közzététele után jelennek meg a kirakatokban. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító látható az ügyfelek számára a Piactéri ajánlat és a Azure PowerShell és az Azure CLI webcímen, ha van ilyen.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1** értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="test-drive"></a>Tesztelési meghajtó

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt kipróbálhatja a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

További tesztelési meghajtó erőforrásai:

- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketingre vonatkozó ajánlott eljárások](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tesztelési meghajtók – áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (ellenőrizze, hogy az előugró ablakok blokkolása ki van-e kapcsolva)

### <a name="lead-management"></a>Érdeklődők kezelése

Ha közzéteszi az ajánlatát a kereskedelmi piactéren a partner centerrel, kapcsolja össze azt az Ügyfélkapcsolat-kezelési (CRM) rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket. Ha engedélyezni szeretné a **tesztelési meghajtót** (lásd a korábbi szakaszt), a CRM-hez való csatlakozásra van szükség, ellenkező esetben nem kötelező.

1. Válasszon egy érdeklődési célt, ahová küldhetjük az érdeklődőket. A partner Center a következő CRM-rendszereket támogatja:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) ügyfél-engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fentiekben, az [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy a [https-végpont](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) használatával tárolhatja az ügyfél-érdeklődő adatait. Ezután exportálja az adatait a CRM-rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, érvényesítjük a kapcsolatot, és elküldünk egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja meg üzembe helyezni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhoz való kapcsolat frissül, így nem veszíti el az érdeklődőket.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

Ezen a lapon megadhatja az ajánlatnak a piactéren való csoportosításához használt kategóriákat és iparágakat, az alkalmazás verzióját, valamint az ajánlatát támogató jogi szerződéseket.

### <a name="categories"></a>Kategóriák

Válasszon legalább egyet, és legfeljebb öt kategóriát. Ezek a kategóriák az ajánlatnak a piactér megfelelő keresési területein való elhelyezésére szolgálnak. Az ajánlat leírásában ismertesse, hogy az ajánlat hogyan támogatja ezeket a kategóriákat. A virtuális gépekre vonatkozó ajánlatok az Azure Marketplace-en, a **számítási** kategóriában jelennek meg.

### <a name="legal"></a>Jogi tudnivalók

Meg kell adnia az ajánlat használati feltételeit. Erre két lehetősége van:

- Saját használati feltételek használata
- Szabványos szerződés használata a Microsoft kereskedelmi piactérről

#### <a name="use-your-own-terms-and-conditions"></a>Saját használati feltételek használata

A saját használati feltételeinek megadásához a **feltételek és kikötések** mezőben adja meg a legfeljebb 10 000 karakter hosszúságú szöveget. Ha további leírásra van szüksége, adjon meg egy webcímet, amely arra a pontra mutat, ahol a feltételek és kikötések találhatók. Aktív hivatkozásként jelenik meg az ügyfelek számára.

Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Szabványos szerződés használata a Microsoft kereskedelmi piactérről

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést biztosít a kereskedelmi piactéren. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

Használja a standard szerződést a **szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzet bejelölésével, majd **fogadja el** az előugró ablakban (Előfordulhat, hogy fel kell görgetni, hogy megjelenjen).

![A partneri központ áttekintés lapját mutatja be az új ajánlat gombbal és a tanácsadási szolgáltatás kiválasztott ajánlatával.](media/use-standard-contract.png)

> [!NOTE]
> Miután közzétette az ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. A standard szerződés keretében **vagy** a saját használati feltételeinek megfelelően kínálja megoldását.

A standard szintű szerződéssel kapcsolatos további tudnivalókért tekintse meg a Microsoft [kereskedelmi piactérre](https://docs.microsoft.com/azure/marketplace/standard-contract)vonatkozó standard szintű szerződést. A [standard szerződést](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlként is letöltheti (Győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

##### <a name="standard-contract-amendments"></a>Standard szintű szerződés módosításai

A standard szintű szerződések módosításai lehetővé teszik az egyszerűség általános szerződési feltételeinek kiválasztását, valamint a termék vagy vállalat használati feltételeinek létrehozását. Az ügyfeleknek csak akkor kell áttekinteniük a szerződés módosításait, ha már áttekintették és elfogadták a Microsoft standard szerződést. Kétféle módosítás érhető el: univerzális és egyéni.

**Univerzális módosítások** – ezeket a rendszer univerzálisan alkalmazza az összes ügyfélre vonatkozó standard szerződésre. Az ajánlat minden ügyfelének megjelenik a vásárlás folyamatában. Az ügyfeleknek el kell fogadniuk a standard szerződés feltételeit és a módosítás (oka) t, mielőtt felhasználhatják az ajánlatot. Ajánlathoz egyetlen általános módosítást is megadhat. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy Azure Portalban jelennek meg a felderítési és vásárlási folyamat során.

**Egyéni módosítások** – ezek a szabványos szerződések speciális módosításai, amelyek az Azure-bérlői azonosítók segítségével meghatározott ügyfelekre irányulnak. Kiválaszthatja a célként használni kívánt bérlőt. Az ajánlat vásárlási folyamatában csak a bérlő ügyfelei jelennek meg az egyéni módosítási feltételekkel. Az ügyfeleknek el kell fogadniuk a standard szerződés feltételeit és a módosítás (oka) t, mielőtt felhasználhatják az ajánlatot.

Először válassza az **Egyéni módosítási feltételek hozzáadása (legfeljebb 10)** lehetőséget. Ajánlatunk legfeljebb tíz egyéni módosítási kifejezést biztosíthat.

- **Egyéni módosítási feltételek** – adja meg a saját módosítási feltételeit az egyéni módosítási feltételek mezőben. Korlátlan számú karaktert adhat meg. Ezek az egyéni kifejezések esetében csak az Ön által megadott bérlői azonosítók ügyfelei láthatják ezeket a Azure Portalban az ajánlat vásárlási folyamatában.
- **Bérlői azonosítók** (kötelező) – minden egyéni módosítás legfeljebb 20 bérlői azonosítót célozhat meg. Ha egyéni módosítást ad hozzá, meg kell adnia legalább egy bérlői azonosítót, amely azonosítja az ügyfelet az Azure-ban. az ügyfelek az Azure-ban, majd a tulajdonságok területen találhatják meg az Ön számára. A címtár-azonosító értéke a bérlő azonosítója (például 50c464d3-4930-494c-963c-1e951d15360e). A szervezet bérlői AZONOSÍTÓját is megtalálhatja az ügyfélhez tartozó tartománynév-webcímek használatával, [Mi a Microsoft Azure és az Office 365-bérlő azonosítója?](https://www.whatismytenantid.com/).
- **Leírás** (nem kötelező) – adjon meg egy rövid leírást a BÉRLŐi azonosítóhoz, amely segít azonosítani a módosítással megcélzott ügyfelet.

> [!NOTE]
> Ez a két típusú módosítás egymásra épül. Az egyéni módosításokkal rendelkező ügyfelek a vásárlás során általános módosítást is kapnak a standard szerződéshez.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Ezen a lapon megadhatja az ajánlat részleteit, például az ajánlat nevét, leírását, hivatkozásait és névjegyeit.

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek és a használati feltételek) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." A _hasznos hivatkozásokat tartalmazó webes címeket_ is megadhatja, hogy az ajánlati tartalomban használt nyelvtől eltérő nyelven kínálja a tartalmat.

### <a name="marketplace-details"></a>Piactér – részletek

#### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címeként. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel is rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek)
- Nem lehet hosszabb 50 karakternél
- Nem szerepelhetnek hangulatjelek.

#### <a name="search-results-summary"></a>Keresési eredmények összegzése

Az ajánlat rövid leírása. Ez akár 100 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

#### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlat további leírását. Ez akár 256 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

#### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának webcímet (URL-címét). Győződjön meg arról, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Érvényes adatvédelmi szabályzatot is közzé kell tennie a webhelyén.

### <a name="useful-links"></a>Hasznos hivatkozások

Adja meg az ajánlatával kapcsolatos kiegészítő online dokumentumokat. Hivatkozás hozzáadásához válassza a **+ hivatkozás hozzáadása** lehetőséget, majd hajtsa végre a következő mezőket:

- **Név** – az ügyfelek a részleteket tartalmazó oldalon láthatják a nevet.
- **Hivatkozás (URL)** – adjon meg egy hivatkozást az ügyfelek számára az online dokumentum megtekintéséhez.

### <a name="customer-support-links"></a>Ügyfélszolgálati hivatkozások

Adja meg a támogatási webhelyet, ahol az ügyfelek elérheti a támogatási csapatot.

- Azure globális támogatási webhely
- Azure Government támogatási webhely

### <a name="partner-support-contact"></a>Partneri támogatási kapcsolattartó

Adja meg a Microsoft-partnerek számára a támogatási jegy megnyitásakor használandó kapcsolattartási adatokat. Ez nem jelenik meg a piactéren.

- Name
- E-mail
- Telefon

### <a name="engineering-contact"></a>Mérnöki kapcsolattartó

Adja meg a Microsoft számára az ajánlattal kapcsolatos problémák esetén használandó kapcsolattartási adatokat, beleértve a minősítéssel kapcsolatos problémákat is. Ez nem jelenik meg a piactéren.

- Name
- E-mail
- Telefon

### <a name="marketplace-media"></a>Piactéri adathordozó

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képek miatt a rendszer elutasítja a kérelmet.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner Center által használt szolgáltatást.

#### <a name="marketplace-logos"></a>Piactéri emblémák

Adja meg az ajánlat emblémájának PNG-fájlját a következő négy pixeles méretben:

- **Kicsi** (48 x 48)
- **Közepes** (90 x 90)
- **Nagyméretű** (216 x 216)
- **Széles** (255 x 115)

Mind a négy emblémát kötelező megadni, és a piactér-lista különböző helyein használatos.

#### <a name="screenshots"></a>Képernyőképek

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie. Minden képernyőképnek tartalmaznia kell egy feliratot.

#### <a name="videos"></a>Videók

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. Ezeket külső videó szolgáltatásban kell tárolni. Adja meg a videó nevét, webcímeit és miniatűr PNG-képét 1280 x 720 képpontban.

További információforrások a piactéren: ajánlott [eljárások a Piactéri ajánlatokhoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="preview"></a>Előnézet

Az Előnézet lapon válasszon egy korlátozott **előzetes** verziót az ajánlat érvényesítéséhez, mielőtt közzéteszi azt a piactéren elérhető szélesebb közönség számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után válassza az **élő** adás lehetőséget az ajánlat közzétételéhez a kereskedelmi piactér nyilvános célközönsége számára.

Az előnézeti közönséget az Azure-előfizetés AZONOSÍTÓjának GUID azonosítói határozzák meg, és mindegyikhez opcionális leírást is megadhat. Ezen mezők egyikét sem láthatja az ügyfelek. Az Azure-előfizetési azonosítót a Azure Portal **előfizetések** oldalán találja.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10) vagy egy CSV-fájl feltöltésével (legfeljebb 100). Az előfizetés-azonosítók hozzáadásával meghatározhatja, hogy ki láthatja el az ajánlatot az élő közzététel előtt. Ha az ajánlata már élő, akkor is megadhatja az előnézeti célközönséget az ajánlat változásainak vagy frissítéseinek teszteléséhez.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előnézeti közönség elérheti az ajánlatát, _mielőtt_ élőben közzétette a piactéren. Megtekinthetik és ellenőrizhetik az összes csomagot, beleértve azokat is, amelyek csak a privát közönség számára lesznek elérhetők, miután az ajánlata teljes mértékben közzé lett téve a piactéren. A saját célközönség (a csomag **díjszabása és rendelkezésre állása** lapon van meghatározva) kizárólagos hozzáféréssel rendelkezik egy adott csomaghoz.

A következő szakaszra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget, majd a terv áttekintését.

## <a name="plan-overview"></a>A terv áttekintése

A partner Centerben ugyanezen az ajánlaton belül különböző csomagokra vonatkozó lehetőségek is megadhatók. Ezeket a csomagokat korábban SKU-ként emlegetik. Egy ajánlathoz legalább egy csomag szükséges, amely a bevételi közönség, az Azure-régiók, a funkciók vagy a virtuálisgép-rendszerképek szempontjából eltérő lehet.

A csomagok létrehozása után a **terv áttekintése** lap a következőket jeleníti meg:

- Csomag neve
- Licencelési modellek
- Közönség (nyilvános vagy privát)
- Jelenlegi közzétételi állapot
- Elérhető műveletek

A terv áttekintésében elérhető műveletek a csomag aktuális állapotától függően változnak. Ezek például az alábbi jelentések lehetnek:

- **Piszkozat törlése** – ha a terv állapota vázlat
- Az **eladási terv** vagy a **privát célközönség szinkronizálásának** leállítása – ha a csomag állapota élőben van közzétéve

### <a name="create-new-plan"></a>Új csomag létrehozása

Válassza az **+ új csomag létrehozása** lehetőséget a felső részen. Megjelenik az **új terv** párbeszédpanel.

A **terv azonosítója** mezőben hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat minden egyes csomagjában. Ez az azonosító a termék webcímen lévő ügyfelek számára látható lesz. Csak kisbetűket és számokat, kötőjeleket vagy aláhúzásokat, illetve legfeljebb 50 karaktert használjon.

> [!NOTE]
> A terv azonosítója a **Létrehozás**gombra kattintva nem módosítható.

A **terv neve** mezőbe írja be a csomag nevét. Az ügyfelek ezt a nevet látják, amikor dönti el, hogy melyik tervet szeretné kiválasztani az ajánlaton belül. Hozzon létre egy egyedi nevet, amely egyértelműen rámutat az egyes tervek különbségeit. Használhatja például a **Windows Servert** az utólagos elszámolású, a **BYOL**, a **Advanced**és a **Enterprise** **csomagokkal**.

Kattintson a **Létrehozás** gombra.

### <a name="plan-setup"></a>Csomag beállítása

Állítsa be a terv típusának magas szintű konfigurációját, hogy az újrahasznosítsa-e egy másik csomag technikai konfigurációját, és hogy mely Azure-régiók számára legyen elérhető a terv. Az itt megadott beállítások határozzák meg, hogy mely mezők jelenjenek meg ugyanazon csomag más lapjain.

#### <a name="re-use-technical-configuration"></a>Technikai konfiguráció újbóli használata

Ha több azonos típusú csomaggal rendelkezik, és a csomagok azonosak egymás között, akkor kiválaszthatja, hogy a **terv újrahasznosítja a technikai konfigurációt egy másik csomagból**. Ezzel a beállítással kiválaszthatja az ajánlathoz tartozó más csomagok egyikét, és újra felhasználhatja a technikai konfigurációját.

> [!NOTE]
> Ha egy másik csomag technikai konfigurációját használja, a teljes **technikai konfiguráció** lap eltűnik ebből a csomagból. A csomagra vonatkozó technikai konfiguráció részleteit, beleértve a jövőbeli frissítéseket is, a tervhez is használni fogjuk. Ez a beállítás a terv közzététele után nem módosítható.

#### <a name="azure-regions"></a>Azure-régiók

A tervet legalább egy Azure-régióban elérhetővé kell tenni.

Válassza ki az **Azure globális** lehetőséget, hogy a tervet elérhetővé tegye az összes olyan globális Azure-régióban, amely kereskedelmi Piactéri integrációt tartalmaz. Részletekért lásd: a [földrajzi Elérhetőség és a pénznemek támogatása](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Válassza a **Azure Government** lehetőséget, hogy a terv elérhető legyen a [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) régióban. Ez a régió szabályozott hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi entitásai ügyfeleinek, valamint az azok kiszolgálására jogosult partnereinknek. Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).

A [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)való közzététel előtt tesztelje és érvényesítse a tervet a környezetben, mivel egyes végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez [Microsoft Azure Government próbaverzióból](https://azure.microsoft.com/global-infrastructure/government/request/)kérjen próbaverziós fiókot.

> [!NOTE]
> Miután közzétette és elérhetővé tette a csomagot egy adott Azure-régióban, nem távolíthatja el a régiót.

#### <a name="azure-government-certifications"></a>Azure Government minősítések

Ez a beállítás csak akkor látható, ha a **Azure Government**lehetőséget választotta.

Azure Government a szolgáltatások bizonyos kormányzati szabályozások és követelmények hatálya alá esnek. Például: FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 és CJIS. A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja azokat. Ezek lehetnek közvetlenül a programra mutató hivatkozások vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások. Ezek a hivatkozások csak Azure Government ügyfelek számára láthatók.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="plan-listing"></a>Csomag listázása

Itt konfigurálhatja a csomag részleteit. Ezen a lapon láthatók azok az információk, amelyek eltérőek lehetnek az adott ajánlat csomagjai között.

#### <a name="plan-name"></a>Csomag neve

Ez előre ki van töltve azzal a névvel, amelyet a létrehozáskor adott meg a tervben. Ez a név jelenik meg a piactéren a csomag címeként, és legfeljebb 100 karakter hosszú lehet.

#### <a name="plan-summary"></a>Csomag összegzése

Adja meg a csomag rövid összefoglalását (nem az ajánlatot). Ez az Összegzés legfeljebb 100 karakter hosszú lehet.

#### <a name="plan-description"></a>Csomag leírása

Írja le, hogy mi teszi ezt a csomagot egyedivé, valamint az ajánlaton belüli csomagok közötti különbségeket. Ne írja le az ajánlatot, csak a csomagot. A csomag leírása legfeljebb 2 000 karaktert tartalmazhat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Ezen a lapon a következőket konfigurálhatja:

- A csomag elérhetővé válik a következőben:
- Az óránkénti díj
- Azt határozza meg, hogy a terv mindenki számára elérhető legyen-e, vagy csak bizonyos ügyfelek számára (privát célközönség)

#### <a name="markets"></a>Piacok

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be az összes olyan piaci hely jelölőnégyzetét, ahol ez a csomag megvásárolható (ezen piacok felhasználói továbbra is üzembe helyezhetik az ajánlatot a **[csomag telepítése](#plan-setup)** során kiválasztott összes Azure-régióban). Az **átutalt adó** gomb azokat az országokat jeleníti meg, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és használati adót. A Kínában való közzététel csak olyan csomagokra korlátozódik, amelyek **ingyenesek** , vagy **saját licencet** (BYOL) is használhatnak.

Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A változtatások mentése után tekintse át az árakat az **exportálási díjak (xlsx)** hivatkozás használatával.

Ha eltávolít egy piacot, az aktív üzemelő példányok használatával az adott piacon lévő ügyfelek nem fognak tudni új központi telepítéseket létrehozni vagy bővíteni meglévő üzembe helyezéseit. A meglévő központi telepítéseket a rendszer nem érinti.

#### <a name="pricing"></a>Díjszabás

**Licencelési modell** – a csomag díjszabásának konfigurálásához vagy **a saját licencének** megadásához használja a **használati alapú havi számlázást** .

A használati alapú havi számlás csomag esetében a következő három díjszabási bejegyzés közül választhat:

- **/Mag** – a Egyesült Államok dollárban (USD) megadható díj. A díjszabást az alapszintű méret alapján számítjuk ki, a helyi pénznemre pedig az aktuális árfolyamot használva.
- **Alapméret** szerint – USD-ben biztosítjuk az árak alapméretét. Az árakat helyi pénznemre konvertáljuk az aktuális árfolyam alapján.
- **Piaci és alapméret** szerint – az összes piac alapméretének díjszabását adja meg. Az árakat egy táblázatból is importálhatja.

> [!NOTE]
> Mentse a díjszabási változásokat, hogy engedélyezze a díjszabási adatexportálást. Miután közzétette a csomag egy piacának árát, később nem módosítható. A díjszabási táblázat exportálásával győződjön meg arról, hogy ezek az árak közvetlenül a közzététel előtt vannak, és az árakat az egyes piacokon értékeli.

#### <a name="free-trial"></a>Ingyenes próbaidőszak

Az ügyfeleknek egy-vagy három hónapos ingyenes próbaverziót is kínálunk.

#### <a name="visibility"></a>Láthatóság

Megtervezheti, hogy minden egyes terv látható legyen mindenki számára, vagy csak egy kijelölt közönség számára. Tagságok társítása ebben a korlátozott célközönségben az Azure-előfizetési azonosítók használatával.

**Nyilvános** – a tervet mindenki láthatja.

**Privát célközönség** – a terv csak az előkiválasztott közönség számára látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy megváltoztathatja nyilvánosra. Miután megnyitotta a csomagot, nyilvánosnak kell maradnia; a magánjellegűre nem módosítható.

**Korlátozott célközönség (Azure-előfizetési azonosítók)** – hozzárendelheti azt a célközönséget, amely hozzáfér ehhez a privát csomaghoz az Azure-előfizetési azonosítók használatával. Igény szerint az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását is megadhatja. Akár 10 előfizetés-azonosítót is hozzáadhat manuálisan, vagy 20 000 CSV-táblázat importálása esetén. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisbetűsnek kell lenniük.

> [!NOTE]
> A privát vagy a korlátozott célközönség nem azonos az **Előnézet lapon megadott** előnézeti célközönséggel. Az előnézeti közönség hozzáférhet az ajánlathoz a piactéren közzétett élő állapot _előtt_ . Míg a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

#### <a name="hide-plan"></a>Terv elrejtése

Ha a virtuális gépet kizárólag közvetve kell használni, ha egy másik megoldási sablonon vagy felügyelt alkalmazáson keresztül hivatkoznak rá, jelölje be ezt a jelölőnégyzetet a virtuális gép közzétételéhez, de az ügyfelek által közvetlenül keresett és megkeresett adatok elrejtéséhez.

> [!NOTE]
> A rejtett csomagok nem támogatják az előzetes verziójú hivatkozásokat.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="technical-configuration"></a>Technikai konfiguráció

Adja meg a csomaghoz tartozó képeket és egyéb technikai tulajdonságokat. Részletekért lásd: [Azure-beli virtuális gép technikai eszközének létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Ez a lap nem jelenik meg, ha úgy konfigurálta ezt a csomagot, hogy a csomag **beállítása** lapon újrahasznosítsa a csomagokat egy másik tervből.

#### <a name="operating-system"></a>Operációs rendszer

- **Operációs rendszer családja** – válasszon **Windows** vagy **Linux** operációs rendszerből
- **Kiadás** vagy **szállító** – válassza ki a Windows kiadási vagy linuxos gyártót
- **Operációs rendszer felhasználóbarát neve** – válasszon egy felhasználóbarát operációsrendszer-nevet. Ez a név jelenik meg az ügyfelek számára

#### <a name="recommended-vm-sizes"></a>Ajánlott virtuálisgép-méretek

Az Azure piactéren megjelenítendő legfeljebb hat ajánlott virtuálisgép-méret közül választhat.

#### <a name="open-ports"></a>Portok megnyitása

Nyisson meg nyilvános vagy magánhálózati portot egy telepített virtuális gépen.

#### <a name="storage-option-for-deployment"></a>Tárolási lehetőség az üzembe helyezéshez

**Lemez központi telepítése lehetőség** – válassza ki, hogy a felhasználók milyen típusú központi telepítést használhatnak a virtuális gép használatakor. A Microsoft javasolja, hogy csak a felügyelt lemezes telepítésre korlátozza a központi telepítést.

#### <a name="properties"></a>Tulajdonságok

**Gyorsított hálózatkezelés támogatása** – válassza ki, hogy a virtuális gép támogatja-e a [gyorsított hálózatkezelést](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Virtuálisgép-rendszerképek

Adja meg a virtuális gépek rendszerképeinek a lemez verzióját és a SAS URI-JÁT. Minden virtuálisgép-rendszerképhez akár 16 adatlemezt is hozzáadhat. Egy adott beadványban csak egy új rendszerkép-verziót adjon meg. A rendszerkép közzététele után nem szerkesztheti, de törölheti is. A verziók törlésével megakadályozható, hogy az új és a meglévő felhasználók a törölt verzió új példányát is üzembe helyezzük.

- A **lemez verziója** a megadott rendszerkép verziója.
- Az **sas URI** az az Azure Storage-beli hely, ahol az operációs rendszer VHD-jét tárolja.
- Az adatlemez-lemezképek az Azure Storage-ban tárolt VHD SAS URI-k is.
- Egy csomagban való beküldéshez csak egy képet adjon hozzá.

A használt operációs rendszertől függetlenül csak a megoldáshoz szükséges adatlemezek minimális számát adja hozzá. Az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket az üzembe helyezéskor, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget, majd térjen vissza a **terv áttekintéséhez**.

## <a name="resell-through-csps"></a>Viszonteladás a CSP-n keresztül

A [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) program partnerei számára elérhetővé teszi az ajánlat elérhetőségét. A saját licenc-(BYOL-) csomagok automatikus használata automatikusan megtörténik; dönthet úgy is, hogy a nem BYOL terveket választja.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive"></a>Tesztelési meghajtó

Állítson be egy bemutatót (tesztvezetés), amely lehetővé teszi az ügyfeleknek az ajánlat megvásárlása előtt történő kipróbálását. Ha olyan bemutató környezetet szeretne létrehozni, amely lehetővé teszi, hogy az ügyfelek meghatározott időn keresztül próbálják meg az ajánlatot, tekintse meg [az ajánlat tesztelése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)című témakört.

A tesztelési meghajtó engedélyezéséhez jelölje be a test Drive engedélyezése jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

További tesztelési meghajtó erőforrásai:

- [Marketingre vonatkozó ajánlott eljárások](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="review-and-publish"></a>Áttekintés és közzététel

Miután elvégezte az összes szükséges szakaszt, elküldheti azt a felülvizsgálat és közzététel lehetőségre.

A portál jobb felső sarkában válassza a **felülvizsgálat és közzététel**lehetőséget.

Ezen az oldalon a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
  - **Nincs elindítva** – a szakasz nem indult el, és el kell végezni.
  - **Hiányos** – a szakasz olyan hibákat tartalmaz, amelyeknek rögzítettnek kell lennie, vagy további információkat kell megadnia. Tekintse meg a jelen dokumentum korábbi szakaszait, amely útmutatást nyújt a szakasz frissítéséhez.
  - **Befejezés** – a szakasz befejeződött, és nincsenek hibák. Az ajánlat elküldése előtt az ajánlat összes részének teljesnek kell lennie.
- **Megjegyzések a minősítéshez** – tesztelési utasítások megadása a minősítési csapatnak, hogy az alkalmazás megfelelően legyen tesztelve. Az alkalmazás megértéséhez hasznos kiegészítő megjegyzéseket is megadhat.

Az ajánlat közzétételre való elküldéséhez válassza a **felülvizsgálat és közzététel**lehetőséget.

Egy e-mailt küldünk, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Ha közzé szeretné tenni az ajánlatát a nyilvános (vagy privát ajánlat, privát közönség) számára, lépjen a partner Center webhelyre, keresse meg az ajánlat **Áttekintés** lapját, és válassza a **Go-Live**lehetőséget. Az ajánlat állapota megjelenik az oldal tetején, ha a közzététel folyamatban van.

### <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

A közzétételi folyamat **manuális érvényesítési** lépése az ajánlat és a hozzá kapcsolódó technikai eszközök átfogó áttekintését jeleníti meg. Ha ez a folyamat felfedi az ajánlatával kapcsolatos hibákat, a rendszer egy, a problémákat részletező minősítési jelentést fog kapni. Egyszerűen végezze el a szükséges helyesbítéseket, és tegye közzé újból az ajánlatot.

## <a name="offer-overview"></a>Ajánlat áttekintése

Az **ajánlat áttekintése** oldalon látható az ajánlat közzétételéhez szükséges lépések vizuális ábrázolása (a befejezett és a folyamatban lévők is), valamint az egyes lépések befejezésének időtartamát.

Ezen a lapon olyan hivatkozások találhatók, amelyek az ajánlaton végzett műveletekre vonatkozó műveleteket hajtanak végre. Például:

- Ha az ajánlat Piszkozat- [törlési ajánlat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Ha az ajánlat élő – [az ajánlat értékesítésének leállítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Ha az ajánlat előzetes verzióban érhető [el](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Ha még nem fejezte be a közzétevő kijelentkezését – [Közzététel megszakítása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace-példák

Ezek a példák azt mutatják be, hogyan jelennek meg az ajánlat az Azure Marketplace-en.

### <a name="azure-marketplace-offer-details"></a>Az Azure Marketplace ajánlatának részletei

![Példa az Azure Marketplace ajánlat részletek képernyőjére](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Az Azure Marketplace keresési eredményei

![Példa az Azure Marketplace keresési részleteinek képernyőjére](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Az Azure Marketplace-csomag részletei

![Példa az Azure Marketplace-csomag részleteit megjelenítő képernyőre](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure Portal ajánlat részletei

![Azure Portal ajánlat részletei képernyő – példa](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure Portal keresési eredmények

![Azure Portal keresési eredmények képernyő példája](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure Portal csomag részletei

![Azure Portal terv részletei képernyő – példa](media/avm-create6.png)

## <a name="next-step"></a>Következő lépés

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
