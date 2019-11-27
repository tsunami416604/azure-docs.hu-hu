---
title: Új Azure apps-ajánlat létrehozása a kereskedelmi piactéren
description: Új Azure-alkalmazások létrehozása az Azure Marketplace-en, a AppSource-on vagy a Cloud Solution Provider (CSP) programon keresztül, a kereskedelmi piactér portál használatával a Microsoft partner Centerben.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d7e05f12c04136c8394dbcb27b7a950fc5ce85d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281271"
---
# <a name="create-an-azure-application-offer"></a>Azure-alkalmazásajánlat létrehozása

Az Azure-alkalmazások kereskedelmi piactéren való közzétételének lépéseit itt találja.

## <a name="azure-application-offer-type"></a>Azure-alkalmazás ajánlatának típusa

Ez a témakör az Azure-alkalmazások kínálatával kapcsolatos alapelveket ismerteti.  Ismernie kell ezeket a fogalmakat, mielőtt megkezdené egy új Azure-alkalmazás közzétételének folyamatát a piactéren.

### <a name="publishing-overview"></a>Közzététel áttekintése

Az Azure Marketplace-hez készült videó- [létrehozási megoldási sablonok és a felügyelt alkalmazások](https://channel9.msdn.com/Events/Build/2018/BRK3603) az Azure-alkalmazás ajánlatának típusa:

* Milyen ajánlati típusok érhetők el;
* Milyen technikai eszközök szükségesek;
* Azure Resource Manager sablon létrehozása;
* Az alkalmazás felhasználói felületének fejlesztése és tesztelése;
* Az alkalmazás ajánlatának közzététele;
* Az alkalmazás felülvizsgálati folyamata.

### <a name="types-of-azure-application-plans"></a>Az Azure-alkalmazások csomagjainak típusai

Kétféle Azure-alkalmazási csomag, felügyelt alkalmazás és megoldás-sablon található.

* A **megoldási sablon** az egyik fő módszer a megoldás közzétételére a piactéren. Ezt a csomagot akkor kell használni, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen (VM) túl.  A megoldás sablonnal több erőforrást is automatizálhat, beleértve a virtuális gépeket, a hálózatkezelést és a tárolási erőforrásokat az összetett IaaS-megoldások biztosításához.  A megoldási sablonok létrehozásával kapcsolatos további információkért tekintse meg a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) dokumentációját.

* A **felügyelt alkalmazás** a megoldási sablonokhoz hasonló, és egyetlen kulcsfontosságú különbség. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. A felügyelt alkalmazások segítségével egyszerűen hozhat létre és biztosíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfelei számára.  További információ a felügyelt alkalmazások előnyeiről és típusairól: az [Azure által felügyelt alkalmazások áttekintése](https://docs.microsoft.com/azure/managed-applications/overview).

Az összes Azure-alkalmazás legalább két fájlt tartalmaz egy `.zip` Archívum gyökérkönyvtárában:

* Egy [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)nevű Resource Manager-sablonfájl.  Ez az a sablon, amely meghatározza az ügyfél Azure-előfizetésében telepítendő erőforrásokat.  A Resource Manager-sablonokra vonatkozó példákért tekintse meg az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) katalógusát vagy a megfelelő [githubot: Azure Resource Manager Gyorsindítás sablonok](https://github.com/azure/azure-quickstart-templates) tárháza.

* A [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)nevű Azure-alkalmazás létrehozási élményének felhasználói felületi definíciója.  A felhasználói felületen elemeket ad meg, amelyek lehetővé teszik a felhasználók számára paraméterértékek megadását.

Minden új Azure-alkalmazás ajánlatának tartalmaznia kell egy [Azure-partner ügyfél-használati azonosítóját (GUID](??)).

### <a name="before-you-begin"></a>Előkészületek

Tekintse át a következő Azure-alkalmazás dokumentációját, amely rövid útmutatók, oktatóanyagok és minták áttekintését nyújtja.

* [Azure Resource Manager sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Gyorsútmutatók:

    * [Azure Gyorsindítás sablonok](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates)
    * [Alkalmazás-definíció közzététele](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [A Service Catalog alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Oktatóanyagok:

    * [Definíciós fájlok létrehozása](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Piactéren elérhető alkalmazás közzététele](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Minták

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Felügyelt alkalmazási megoldások](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>A technikai ismeretek alapjai

Az adategységek tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról.

A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákkal kapcsolatos ismereteket:

* Az Azure- [szolgáltatások](https://azure.microsoft.com/services/)alapszintű ismerete.
* [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/).
* Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)együttműködésének ismerete.
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)működésének ismerete.
* A [JSON](https://www.json.org/)működésének ismerete.

### <a name="suggested-tools"></a>Javasolt eszközök

Az Azure-alkalmazás kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Javasoljuk, hogy a következő eszközöket adja hozzá a fejlesztői környezethez:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) a következő kiterjesztésekkel:
    * Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Az elérhető eszközöket az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon tekintheti át.  Emellett a Visual Studio-t is használhatja a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="create-an-azure-application-offer"></a>Azure-alkalmazásajánlat létrehozása

Az Azure-alkalmazások ajánlatának létrehozása előtt először [létre kell hoznia egy partner Center-fiókot](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , és meg kell nyitnia a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), és ki kell választania az **Áttekintés** lapot.

>[!Note]
>Az ajánlat közzétételét követően a partner Centerben végzett ajánlat szerkesztése csak a rendszeren és a kirakatokban történik meg, az ismételt közzététel után.  Győződjön meg arról, hogy a módosítások elvégzése után beküldi az ajánlatot a közzétételhez.

### <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Válassza az **+ új ajánlat** gombot, majd válassza az **Azure-alkalmazás** menüpontot. Ekkor megjelenik az **új ajánlat** párbeszédpanel.

### <a name="offer-id-and-alias"></a>Ajánlat azonosítója és alias

* **Ajánlat azonosítója**: a fiókban található egyes ajánlatok egyedi azonosítója. Ez az azonosító látható lesz az ügyfelek számára a Piactéri ajánlat URL-címében, és Azure Resource Manager sablonokat (ha vannak ilyenek). <br> <br> Az ajánlat AZONOSÍTÓjának kisbetűs alfanumerikus karakternek kell lennie (beleértve a kötőjeleket és az aláhúzásokat, de nincs szóköz). Az érték 50 karakterre van korlátozva, és a Létrehozás gombra kattintva nem módosítható. <br> <br> Ha például itt adja meg `test-offer-1` itt, az ajánlat URL-címe `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`lesz. 

* **Ajánlat aliasa**: az ajánlatnak a partner Centerben való hivatkozásához használt név. Ez a név nem használható a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel. Ez az érték nem módosítható a **Létrehozás**gombra kattintva.

Miután megadta az **ajánlat-azonosítót** és az **ajánlati aliast**, válassza a **Létrehozás**lehetőséget. Ezután az ajánlat minden más részén dolgozhat.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **ajánlat beállítása** lap a következő információkat kéri. A mezők kitöltése után válassza a **Mentés** lehetőséget.

### <a name="test-drive"></a>Próbaüzem

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt megkeresi a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

A tesztelési meghajtó engedélyezéséhez jelölje be a **tesztvezetés engedélyezése** jelölőnégyzetet. Ezután be kell állítania egy bemutató környezetet a [Test Drive technikai konfigurációjában](#types-of-azure-application-plans) , hogy az ügyfelek meghatározott időn belül kipróbálják az ajánlatot. 

>[!Note]
>Mivel az összes Azure-alkalmazás egy Azure Resource Manager sablonnal lett implementálva, az Azure-alkalmazásokhoz konfigurálható tesztelési meghajtó csak [Azure Resource Manager-alapú tesztelési meghajtó](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)lehet.

#### <a name="additional-test-drive-resources"></a>További tesztelési meghajtó erőforrásai

- [A test Drive technikai ajánlott eljárásai](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A test Drive marketing ajánlott eljárásai](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [A test Drive áttekintése egy pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon megadhatja az ajánlatnak a piactéren, az alkalmazás verzióját és az ajánlatát támogató jogi szerződések alapján csoportosítani kívánt kategóriákat és iparágakat. A lap befejezése után válassza a **Mentés** lehetőséget.

### <a name="category"></a>Kategória

Válasszon ki legalább egyet, és legfeljebb három kategóriát, amelyek az ajánlatnak a piactér megfelelő keresési területeire való elhelyezésére szolgálnak. Ügyeljen arra, hogy az ajánlat leírásában ne adja meg, hogy az ajánlat hogyan támogatja ezeket a kategóriákat. 

### <a name="standard-marketplace-terms-and-conditions"></a>Standard szintű piactér – feltételek és kikötések

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft egy standard szintű szerződést biztosít, amely megkönnyíti a tranzakciót a piactéren.

Az egyéni használati feltételek és kikötések elvégzése helyett dönthet úgy, hogy a standard szintű szerződés keretében kínálja a szoftverét, amelyet csak egyszer kell kiadnia, és csak egyszer kell elfogadni.

A standard szintű szerződés a következő címen érhető el: https://go.microsoft.com/fwlink/?linkid=2041178

A standard szintű szerződés használatához jelölje be a **standard szintű szerződés használata** jelölőnégyzetet.

#### <a name="terms-of-use"></a>Használati feltételek

Ha nem jelöli be a **standard szintű szerződés használata?** jelölőnégyzetet, meg kell adnia a saját jogi feltételeit a **használati feltételek** mezőben. Akár 10 000 karaktert is megadhat, vagy ha a használati feltételek hosszabb leírást igényelnek, adja meg azt az URL-címet, ahol a további licencfeltételek találhatók. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket az alkalmazás kipróbálása előtt.

## <a name="offer-listing"></a>Ajánlati lista

Az ajánlati lista oldal megjeleníti azokat a nyelveket, amelyekben az ajánlat fel lesz sorolva. Jelenleg az egyetlen elérhető lehetőség az **angol (Egyesült Államok)** .

Meg kell határoznia a piactér részleteit (az ajánlat nevét, leírását, képeit stb.) az egyes nyelvekhez/piacokhoz. Válassza ki a nyelv/piac nevét az információ megadásához.

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek, a használati feltételek stb.) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelvű] verzióban érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

### <a name="name"></a>Name (Név)

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

### <a name="summary"></a>Összefoglalás

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter), amelyet a piactér keresési eredményeiben használhat.

### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlat hosszabb leírását (legfeljebb 256 karakter). A leírást a piactér keresési eredményeiben lehet használni.

### <a name="description"></a>Leírás

Adja meg az ajánlat hosszabb leírását (legfeljebb 3 000 karakter). Ez a leírás az ügyfelek számára jelenik meg a piactér listázásának áttekintésében. Adja meg az ajánlat értékeit, a főbb előnyöket, a kategóriát és/vagy az iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket és a szükséges közzétételeket. 

Néhány tipp a Leírás írásához:  

- A Leírás első néhány mondatában egyértelműen ismertesse ajánlata értékét. Adja meg a következő elemeket az érték-kiosztásban:
  - A termék leírása
  - A termékből származó előnyöket biztosító felhasználó típusa
  - Az ügyfélnek szüksége van a termék címére
- Ne feledje, hogy az első néhány mondat a keresőmotor eredményei között jelenhet meg.  
- Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez. Ehelyett a megadott értékre kell összpontosítania.  
- A lehető legnagyobb mértékben használja az iparági specifikus szókincset vagy a juttatás-alapú szövegezést. 
- HTML-címkék használatával formázhatja a leírást, és még vonzóbbá teheti őket.

### <a name="search-keywords"></a>Kulcsszavak keresése

Akár három keresési kulcsszót is megadhat, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmények érdekében próbálja meg használni ezeket a kulcsszavakat a leírásában is.

### <a name="support-urls"></a>Támogatási URL-címek

Ez a szakasz olyan hivatkozásokat tartalmaz, amelyek segítségével az ügyfelek megismerhetik az ajánlatával kapcsolatos további információkat.

#### <a name="privacy-policy-url"></a>Adatvédelmi szabályzat URL-címe

Adja meg a szervezete adatvédelmi szabályzatának URL-címét. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

#### <a name="useful-links"></a>Hasznos hivatkozások

Adjon meg opcionális kiegészítő online dokumentumokat a megoldásáról.  További hasznos hivatkozások hozzáadásához kattintson **a + hivatkozás hozzáadása**lehetőségre.

### <a name="contacts"></a>Kapcsolatok

Ebben a szakaszban meg kell adnia egy **támogatási kapcsolattartó** nevét, e-mail-címét és telefonszámát, valamint egy **mérnöki kapcsolattartót**. Ez az információ nem jelenik meg az ügyfelek számára, de a Microsoft számára elérhetővé válik, és a CSP-partnerek számára is biztosítható.

A **támogatási kapcsolattartó** szakaszban meg kell adnia azt a **támogatási URL-címet** is, ahol a CSP-partnerek megtalálják az ajánlat támogatását.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Ebben a szakaszban megadhatja azokat az emblémákat és képeket, amelyeket az ajánlat vásárlónak való megjelenítésekor használni fog. Minden képnek. png formátumúnak kell lennie.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlat emblémáját három méretben: **kis (48 x 48)** , **közepes (90 x 90)** és **nagyméretű (216 x 216)** .

#### <a name="hero"></a>Hero

A hős képe nem kötelező. Ha megadja az egyiket, a 815 x 290 képpont értéket kell mérnie.

#### <a name="screenshots"></a>Képernyőképek

Képernyőképeket adhat hozzá, amelyek bemutatják, hogyan működik az ajánlata. Legfeljebb öt képernyőképet adhat hozzá. Minden képernyőképnek 1280 x 720 képpont-nek kell lennie.

#### <a name="videos"></a>Videók

Opcionálisan akár öt videót is hozzáadhat az ajánlat bemutatásához. Ezeket a videókat a YouTube és/vagy a Vimeo szolgáltatásban kell üzemeltetni. Mindegyiknél írja be a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Előzetes verzió

Az **előnézet** lapon megadhat egy korlátozott **előzetes** verziót, amely az ajánlat érvényességének ellenőrzéséhez nyújt segítséget a piactér szélesebb közönsége számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után a **Go Live** -t kell választania, mielőtt az ajánlatát élőben közzé fogja tenni a piactér nyilvános célközönsége számára.

Az előnézeti közönséget az Azure-előfizetés AZONOSÍTÓjának GUID azonosítói határozzák meg, és mindegyikhez opcionális leírást kell párosítani.  Ezen mezők egyike sem látható az ügyfelek számára.

Akár 10 Azure-előfizetési azonosítót is hozzáadhat manuálisan, akár 100-ig, ha CSV-fájlt tölt fel.  Ezeknek az előfizetéseknek a hozzáadásával olyan célközönséget határozhat meg, amely a teljes közzététel előtt lehetővé teszi az ajánlat megtekintését.  Ha az ajánlat már élő, akkor is megadhatja az előnézeti közönséget, hogy tesztelje az ajánlat módosításait és frissítéseit.

>[!Note]
>Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége a piactéren való élő közzététel *előtt* jogosult az ajánlathoz való hozzáférésre. Dönthet úgy is, hogy létrehoz egy csomagot, és csak privát célközönség számára teszi elérhetővé (a csomag rendelkezésre állása lapon).  Az előzetes verzió célközönsége megtekintheti és érvényesítheti az összes csomagot, beleértve azokat a csomagokat is, amelyek csak akkor lesznek elérhetők a privát közönség számára, ha az ajánlata teljes mértékben közzé lett téve a piactéren.

## <a name="plan-overview"></a>A terv áttekintése

A **terv áttekintő** lapja lehetővé teszi, hogy ugyanazon az ajánlaton belül különböző csomag-beállításokat biztosítson. Ezek a csomagok (a Cloud Partner Portal SKU-ként) a csomag típusa (a megoldás sablonja vagy a felügyelt alkalmazás), a monetizálása vagy a célközönség szempontjából eltérőek lehetnek.  Állítson be legalább egy csomagot az ajánlat a piactéren való listázásához.

A létrehozást követően megtekintheti a csomag neveit, azonosítóit, a csomag típusát, a rendelkezésre állást (nyilvános vagy magánjellegű), az aktuális közzétételi állapotot és a lapon elérhető összes műveletet.

A **terv áttekintésében** elérhető **műveletek** a csomag aktuális állapotától függően változnak, és a következők lehetnek:

* Ha a terv állapota **Piszkozat** – delete Piszkozat.
* Ha a terv állapota **élő** – eladási terv leállítása vagy privát célközönség szinkronizálása.

### <a name="create-new-plan"></a>Új csomag létrehozása

***Csomag azonosítója*** – hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat egyes terveihez. Ez az azonosító a termék URL-címében szereplő ügyfelek számára jelenik meg.  Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon. Ehhez a csomag-AZONOSÍTÓhoz legfeljebb 50 karakter adható meg. Ez az azonosító nem módosítható a létrehozás kiválasztása után.

***Csomag neve*** – az ügyfelek ezt a nevet fogják látni, amikor dönti el, hogy melyik tervet kívánja kiválasztani az ajánlaton belül. Hozzon létre egyedi nevet az ajánlat minden csomagjának. A terv neve az adott ajánlat részét képező szoftvercsomagok megkülönböztetésére szolgál (például Ajánlat neve: Windows Server; csomagok: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Csomag beállítása

A **terv beállítása** lapon megadhatja a csomag típusának magas szintű konfigurációját, attól függetlenül, hogy újrahasználja-e a csomagokat egy másik csomagból, és hogy milyen felhők számára legyen elérhető a terv.  Az ezen a lapon megjelenő válaszok hatással lesznek arra, hogy mely mezők jelenjenek meg ugyanazon csomag más lapjain.

#### <a name="plan-type"></a>Csomag típusa

Ahogy az [Azure-alkalmazási csomagok típusaiban](#types-of-azure-application-plans)is szerepel, válassza ki, hogy a csomag tartalmaz-e megoldás sablont vagy felügyelt alkalmazást.

#### <a name="this-plan-reuses-packages"></a>A terv újrahasznosítja a csomagokat

Ha több azonos típusú csomaggal rendelkezik, és a csomagok azonosak egymás között, akkor kiválaszthatja, hogy a **terv újrahasznosítsa a csomagokat egy másik**csomagból.  Ha ezt a beállítást választja, az ajánlathoz tartozó más csomagok közül választhatja ki a csomagokat. 

>[!Note]
>Ha egy másik csomagból újrahasznál csomagokat, a teljes technikai konfiguráció lap eltűnik ebből a csomagból.  A csomagra vonatkozó technikai konfiguráció részleteit, beleértve a jövőbeli frissítéseket is, a tervhez is használni fogjuk. <br> <br> Ez a beállítás a terv közzétételekor nem módosítható.

#### <a name="cloud-availability"></a>Felhőbeli rendelkezésre állás

Ezt a csomagot legalább egy felhőben elérhetővé kell tenni. 

Válassza ki a **nyilvános Azure** lehetőséget, hogy a megoldás üzembe helyezhető legyen minden olyan nyilvános Azure-régióban, amely rendelkezik piactér-integrációval.  További információ a [földrajzi elérhetőségről](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Válassza a **Azure Government Cloud (felhő** ) lehetőséget a megoldás üzembe helyezéséhez a [Azure Government-felhőben](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), amely egy, az Egyesült Államok szövetségi, állami, helyi vagy törzsi és partnerei által az ilyen entitások kiszolgálására jogosult ügyfelek számára szabályozott hozzáféréssel rendelkező kormányzati Közösség.  Ön, mint közzétevő, felelős a felhőalapú Közösség kiszolgálása érdekében a megfelelőségi ellenőrzés, a biztonsági intézkedések és az ajánlott eljárásokért.  A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).  Mielőtt közzéteszi a [Azure Government](https://aka.ms/azuregovpublish), a Microsoft azt javasolja, hogy tesztelje és érvényesítse a megoldást a környezetben, mivel egyes végpontok eltérőek lehetnek. A megoldás előkészítéséhez és teszteléséhez kérjen egy próbaverziós fiókot erről a [hivatkozásról](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>A csomag egy adott felhőben elérhetőként való közzététele után a felhő nem távolítható el.

**Azure Government Felhőbeli tanúsítványok**

Ez a beállítás csak akkor látható, ha **Azure Government felhő** van kiválasztva a **felhő rendelkezésre állása**alatt.

Azure Government a szolgáltatások bizonyos kormányzati szabályozások és követelmények hatálya alá esnek, például a FedRAMP, a NIST 800,171 (DIB), a ITAR, az IRS 1075, a DoD L4 és a CJIS.  A programok minősítésének megismeréséhez akár 100 hivatkozást is megadhat, amely leírja a tanúsítványokat.  Ezek a hivatkozások vagy közvetlenül a programra mutató hivatkozások, vagy a saját webhelyein való megfelelőség leírására mutató hivatkozások.  Ezek a hivatkozások csak Azure Government Felhőbeli ügyfelek számára lesznek láthatók.

## <a name="plan-listing"></a>Csomag listázása

A **csomag listázása** lap a csomagra vonatkozó olyan listaelemeket jeleníti meg, amelyek eltérőek lehetnek az azonos ajánlat különböző csomagjai között.

### <a name="name"></a>Name (Név)

Előre kitöltve a saját neve alapján, amikor létrehozta a tervét.  Ez a név fog megjelenni a piactéren megjelenített "szoftvercsomag" címmel.  Legfeljebb 100 karaktert tartalmazhat.

### <a name="summary"></a>Összefoglalás

Adja meg a szoftveres csomag rövid összefoglalását.  Legfeljebb 100 karaktert tartalmazhat.

### <a name="description"></a>Leírás

Ez a leírás egy olyan lehetőség, amely ismerteti, hogy mi teszi ezt a csomagot egyedivé, és hogy az Ön által kínált egyéb szoftverek milyen eltéréseket biztosítanak. Legfeljebb 2 000 karaktert tartalmazhat.

A mezők befejezése után válassza a **Mentés** lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

A **rendelkezésre állás** lap csak a megoldási sablon csomagjai számára látható.  A tervet mindenki számára elérhetővé teheti, csak bizonyos ügyfelek (privát célközönségek) számára, valamint azt, hogy a tervet más megoldási sablon vagy csak felügyelt alkalmazások általi használatra szeretné-e ellátni.

### <a name="plan-audience"></a>Célközönség megtervezése

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Ehhez a korlátozott célközönséghez az Azure-előfizetési azonosítók használatával lehet tagságot rendelni.

**Adatvédelem/ez egy privát csomag (nem** kötelező jelölőnégyzet) – jelölje be ezt a jelölőnégyzetet, hogy a csomag magánjellegű legyen, és csak a választott célközönségnek legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatónak kell maradnia. (A csomag nem konfigurálható újra privát csomagként).

**Korlátozott célközönség (Azure-előfizetési azonosítók)** – rendelje hozzá az ehhez a privát csomaghoz hozzáférő célközönséget. A hozzáférés az Azure-előfizetési azonosítók használatával van hozzárendelve, hogy tartalmazza az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását. . Csv számolótábla-fájl importálása esetén legfeljebb 10 előfizetés-azonosító adható hozzá, vagy 20 000 ügyfél-előfizetés-azonosító.  Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisebbnek kell lenniük.

>[!Note]
>A magánjellegű célközönség (vagy a korlátozott célközönség) eltér az [**Előnézet lapon megadott**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) előnézeti célközönségtől.  Az előzetes verzió célközönsége a piactéren élőben közzétett ajánlat *előtt* jogosult az ajánlathoz való hozzáférésre. Míg a privát célközönség megjelölése csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

### <a name="hide-plan"></a>Terv elrejtése

Ha a megoldás sablonja kizárólag központilag telepíthető, ha egy másik megoldási sablon vagy egy felügyelt alkalmazás hivatkozik rá, jelölje be ezt a jelölőnégyzetet a megoldás sablonjának közzétételéhez, de az ügyfelek általi keresés és Tallózás közvetlenül a felhasználók elől való elrejtéséhez.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

A **díjszabás és rendelkezésre állás** lap csak a felügyelt alkalmazások csomagjai számára látható.  Konfigurálhatja azokat a piacokat, amelyekre ez a csomag elérhető lesz, a megoldás felügyeletének havi díja, valamint azt, hogy a tervet mindenki számára vagy csak bizonyos ügyfeleknek (privát célközönségnek) kívánja-e megjeleníteni.

### <a name="markets"></a>Piacok

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be annak a piaci helynek a jelölőnégyzetét, amely számára elérhetővé szeretné tenni ezt a csomagot. Egy keresőmező és egy gomb a "Tax átutalt" országok kiválasztásához, amelyben a Microsoft az Ön nevében értékesítési és használati adót alkalmaz, és a segítségére is felhasználható.

Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A díjszabást a módosítások mentése után a "export prics (xlsx)" hivatkozással tekintheti át.

### <a name="pricing"></a>Díjszabás

Adja meg a csomag havi árát.  Ez az ár a megoldás által központilag üzembe helyezett erőforrások esetében felmerülő bármely Azure-infrastruktúra vagy utólagos elszámolású szoftver költségein felül van.

A helyi pénznemben (USD = Egyesült Államok dollár) beállított díjak az összes kiválasztott piac helyi pénznemére lesznek átalakítva a telepítés során elérhető aktuális árfolyamok használatával. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot. 

>[!Note]
>Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását.

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások miatt a csomag közzététele után is változhatnak.  

>[!Note]
>Miután közzétette a csomag egy piacának árát, később nem módosítható.

### <a name="plan-audience"></a>Célközönség megtervezése

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Ehhez a korlátozott célközönséghez az Azure-előfizetési azonosítók használatával lehet tagságot rendelni.

**Adatvédelem/ez egy privát csomag (nem** kötelező jelölőnégyzet) – jelölje be ezt a jelölőnégyzetet, hogy a csomag magánjellegű legyen, és csak a választott célközönségnek legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatónak kell maradnia. (A csomag nem konfigurálható újra privát csomagként).

**Korlátozott célközönség (Azure-előfizetési azonosítók)** – rendelje hozzá az ehhez a privát csomaghoz hozzáférő célközönséget. A hozzáférés az Azure-előfizetési azonosítók használatával van hozzárendelve, hogy tartalmazza az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását. . Csv számolótábla-fájl importálása esetén legfeljebb 10 előfizetés-azonosító adható hozzá, vagy 20 000 ügyfél-előfizetés-azonosító.  Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisebbnek kell lenniük.

>[!Note]
>A magánjellegű célközönség (vagy a korlátozott célközönség) eltér az [**Előnézet lapon megadott**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) előnézeti célközönségtől.  Az előzetes verzió célközönsége a piactéren élőben közzétett ajánlat *előtt* jogosult az ajánlathoz való hozzáférésre. Míg a privát célközönség megjelölése csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

## <a name="technical-configuration"></a>Technikai konfiguráció 

A **technikai konfiguráció** lapon feltöltheti az üzembe helyezési csomagot, amely lehetővé teszi az ügyfelek számára a terv üzembe helyezését.

>[!Note]
>Ez a lap nem jelenik meg, ha úgy konfigurálta ezt a csomagot, hogy a terv **beállítása** lapon újra használhassa a csomagokat.

### <a name="package-details"></a>Csomag részletei

A **csomag részletei** lapra kattintva szerkesztheti a technikai konfiguráció vázlatos verzióját.

***Verzió*** – a technikai konfiguráció aktuális verziójának kiosztása.  Minden alkalommal növelje ezt a verziót, amikor módosításokat tesz közzé ezen az oldalon. A verziónak `{integer}.{integer}.{integer}`formátumúnak kell lennie.

***Csomagfájl*** (`.zip`) – Ez a csomag tartalmazza az ehhez a tervhez szükséges összes sablonfájlt, valamint a `.zip` fájlként csomagolt további erőforrásokat is.

Az összes Azure alkalmazáscsomag-csomagnak tartalmaznia kell ezt a két fájlt a `.zip` Archívum gyökérkönyvtárában:

* Egy [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)nevű Resource Manager-sablonfájl.  Ez a sablon automatizálja az erőforrások üzembe helyezését az ügyfeleknek az Azure-előfizetésben.  A Resource Manager-sablonokra vonatkozó példákért tekintse meg az [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) katalógusát vagy a megfelelő [githubot: Azure Resource Manager Gyorsindítás sablonok](https://github.com/azure/azure-quickstart-templates) tárháza.

* A [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)nevű Azure-alkalmazás létrehozási élményének felhasználói felületi definíciója.

Az Azure-alkalmazások minden új ajánlatának tartalmaznia kell egy [Azure-partner ügyfél-használati jóváírási](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID azonosítót is.

### <a name="previously-published-packages"></a>Korábban közzétett csomagok 

A **korábban közzétett csomagok** lapra kattintva megtekintheti a technikai konfiguráció összes közzétett verzióját.

## <a name="technical-configuration-managed-application-plans-only"></a>Technikai konfiguráció (csak felügyelt alkalmazási csomagok)

A felügyelt alkalmazások csomagjai további bonyolultságot mutatnak a **technikai konfiguráció** lapon a fent ismertetett **verzió** és **csomagfájl** mezőkön kívül. 

### <a name="enable-just-in-time-jit-access"></a>Igény szerinti (JIT) hozzáférés engedélyezése

Ezzel a beállítással engedélyezheti az igény szerinti (JIT) hozzáférést ehhez a csomaghoz.  A JIT-hozzáférés lehetővé teszi, hogy a hibaelhárítás és a karbantartás érdekében emelt szintű hozzáférést kérjen egy felügyelt alkalmazás erőforrásaihoz. Mindig csak olvasási hozzáférése van az erőforrásokhoz, de egy adott időszakra vonatkozóan nagyobb hozzáférés érhető el.  További információ: a [Azure Managed Applications igény szerinti hozzáférésének engedélyezése és kérése](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Ha szeretné megkövetelni, hogy a felügyelt alkalmazás felhasználói a fiókjának állandó hozzáférését adják meg, hagyja meg ezt a beállítást, ha nincs bejelölve.

>[!Note]
>A funkció támogatásához frissítse a `createUiDefinition.json` fájlt.  

### <a name="deployment-mode"></a>Üzembe helyezési mód

Válassza ki, hogy a csomag telepítésekor a **teljes** vagy **növekményes telepítési módot** kívánja-e konfigurálni: 

* **Teljes módban**az ügyfél által az alkalmazás újratelepítése a felügyelt erőforráscsoport erőforrásainak eltávolítását eredményezi, ha az erőforrások nincsenek meghatározva a `mainTemplate.json`ban. 
* A **növekményes módban**az alkalmazás újratelepítése változatlanul hagyja a meglévő erőforrásokat.

További információ az üzembe helyezési módokról: [Azure Resource Manager telepítési módok](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>Értesítési végpont URL-címe

Adjon meg egy HTTPS-webhook-végpontot, hogy értesítést kapjon a csomag felügyelt alkalmazás példányain lévő összes szifilisz-műveletről.

### <a name="customize-allowed-customer-actions"></a>Engedélyezett felhasználói műveletek testreszabása

Ezzel a beállítással adhatja meg, hogy az ügyfelek milyen műveleteket hajthatnak végre a felügyelt erőforrásokon az alapértelmezés szerint elérhető "`*/read`" műveleteken felül. 

Sorolja fel azokat a további műveleteket, amelyeknek lehetővé szeretné tenni az ügyfél számára, hogy itt hajtsa végre ezt a műveletet pontosvesszővel elválasztva.  További információ: [Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Az elérhető műveletekért lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Ha például engedélyezni szeretné a felhasználóknak a virtuális gépek újraindítását, adja hozzá `Microsoft.Compute/virtualMachines/restart/action` az engedélyezett műveletekhez.

### <a name="global-azure--azure-government-cloud"></a>Globális Azure/Azure Government felhő

Adja meg, hogy ki kell-e kezelni a felügyelt alkalmazáshoz tartozó felügyeleti hozzáférést minden támogatott felhőben.  Azokat a felhasználókat, csoportokat vagy alkalmazásokat, amelyeket a felügyelt erőforráscsoport számára engedélyezni kíván, Azure Active Directory (HRE) identitások használatával azonosíthatók.

***Azure Active Directory bérlő azonosítója*** – a HRE bérlői azonosító (más néven CÍMTÁR-azonosító), amely tartalmazza azon felhasználók, csoportok vagy alkalmazások identitását, amelyekhez engedélyeket kíván adni.  Az HRE-bérlő AZONOSÍTÓját a Azure Portal [Azure Active Directory tulajdonságainál](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)találja.

***Engedélyek*** – adja meg annak a felhasználónak, csoportnak vagy alkalmazásnak az Azure Active Directory objektumazonosítóét, amelynek engedélyt szeretne adni a felügyelt erőforráscsoport számára. Azonosítsa a felhasználót a résztvevő azonosítója alapján, amely a [Azure Portal Azure Active Directory felhasználók](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)paneljén található.

Minden egyes rendszerbiztonsági tag esetében válassza ki az egyik Azure AD beépített szerepkört a listából (tulajdonos vagy közreműködő). A kiválasztott szerepkör leírja, hogy a rendszerbiztonsági tag milyen engedélyeket fog tartalmazni az ügyfél-előfizetés erőforrásaiban. További információkért lásd az [Azure-erőforrások beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  A szerepköralapú hozzáférés-vezérléssel (RBAC) kapcsolatos további információkért lásd: Ismerkedés a [RBAC szolgáltatással a Azure Portalban](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Bár a felhőben legfeljebb 100 engedélyezést adhat hozzá, általában könnyebb létrehozni egy Active Directory felhasználói csoportot, és megadhatja annak AZONOSÍTÓját a "résztvevő azonosítója" értékkel.  Ez lehetővé teszi, hogy a csomag telepítése után további felhasználókat vegyen fel a felügyeleti csoportba, és csökkentse a terv frissítésének szükségességét, hogy további engedélyeket adjon hozzá.

### <a name="policy-settings"></a>Szabályzatbeállítások

Alkalmazza az [Azure-szabályzatokat](https://docs.microsoft.com/azure/governance/policy/overview) a felügyelt alkalmazásra a telepített megoldás megfelelőségi követelményeinek meghatározásához.  A szabályzatdefiníciókról és a paraméterértékek formátumáról tekintse meg a következő dokumentumot: [Azure Policy-minták](https://docs.microsoft.com/azure/governance/policy/samples/index).  Legfeljebb öt házirendet konfigurálhat, és az egyes házirendek közül csak egy példányt lehet beállítani.  Egyes házirendek további paramétereket igényelnek.  A naplózási házirendekhez a szabványos SKU szükséges.  A szabályzat neve legfeljebb 50 karakter hosszú lehet.

## <a name="co-sell"></a>Közös értékesítés

Az ajánlat közzétételéhez teljes mértékben nem kötelező megadni a beadás lapon található információkat. A közös értékesítésre kész és az IP közös értékesítésre kész állapotot kell elérni. Az Ön által megadott információkat a Microsoft értékesítési csapatai fogják használni, hogy a megoldásra vonatkozó további információkat kapjanak az ügyfelek igényeinek megfelelő kiértékelése során. Nem érhető el közvetlenül az ügyfeleknek.

További információ a lap befejezéséről: [közös értékesítés lehetőség a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Próbaüzem

A **tesztvezetés** lapon egy bemutatót (vagy "tesztelési meghajtót") állíthat be, amely lehetővé teszi az ügyfeleknek, hogy a megvásárlása előtt kipróbálják az ajánlatot. További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) című cikkben talál.  Ha már nem szeretne tesztelési meghajtót biztosítani az ajánlatához, térjen vissza az **ajánlat beállítása** lapra, és törölje a **Test Drive engedélyezése**jelölőnégyzet jelölését.

### <a name="technical-configuration"></a>Technikai konfiguráció

Az Azure-alkalmazások eleve a Azure Resource Manager tesztelési meghajtó típusát használják.  További információ: [Azure Resource Manager Test Drive technikai konfigurációja](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) .

### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Ha a tesztelési meghajtót az Ön nevében szeretné üzembe helyezni, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést. (Power BI tesztelési meghajtók esetében nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges): adja meg az előfizetés azonosítóját, amely hozzáférést biztosít az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](https://docs.microsoft.com/azure/billing/billing-create-subscription) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztása esetén megjelenik az előfizetési azonosító (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező): adja meg a Azure Active Directory (ad) [bérlői azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a * * tulajdonságok elemet, majd keresse **meg a felsorolt** 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címével is megkeresheti a következő helyen: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges): adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad** -alkalmazás azonosítója (kötelező): adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás ügyfél-titka** (kötelező): adja meg az Azure ad-alkalmazás [ügyfél-titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. (Ne navigáljon el az oldalról az érték másolása előtt, különben nem fog hozzáférni az értékhez.)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

### <a name="test-drive-listings-optional"></a>Tesztelési meghajtók listázása (nem kötelező)

A test Drive (tesztelési meghajtó) **lapon található** tesztelési **meghajtók listázása** lehetőség megjeleníti azokat a nyelveket (és piacokat), ahol a tesztelési meghajtó elérhető, jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a tesztvezetés részleteit (Leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célokat, a felderített funkciókat, valamint minden olyan releváns információt, amellyel a felhasználó eldöntheti, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges): ismertesse, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező): részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók: videók hozzáadása** (nem kötelező): a videókat feltöltheti a YouTube vagy a Vimeo weboldalára, és itt hivatkozhat egy hivatkozással és egy miniatűr képpel (533 x 324 képpont), így az ügyfelek megtekinthetik az információk áttekintését, így könnyebben megismerhetik a tesztelési meghajtót, beleértve a az ajánlat funkcióinak sikeres használata és az előnyeiket kiemelő forgatókönyvek megismerése.
  - **Név** (kötelező)
  - **URL-cím (csak YouTube vagy Vimeo)** (kötelező)
  - **Miniatűr (533 x 324 px)** : a képfájlnak png formátumúnak kell lennie.

A mezők befejezése után válassza a **Mentés** lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

Miután elvégezte az ajánlat összes szükséges szakaszt, válassza a **Közzététel** elemet a portál jobb felső sarkában. A rendszer átirányítja a **felülvizsgálat és közzététel** lapra. 

Ha első alkalommal teszi közzé ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak befejezési állapotát.
    - *Nincs elindítva* – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
    - *Hiányos* – azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információkat kell megadni. Lépjen vissza a szakasz (ok) ra, és frissítse azt.
    - *Complete (Befejezés* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.
- Adjon meg tesztelési útmutatást a minősítési csapatnak, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazás megértéséhez hasznos kiegészítő megjegyzések mellett.
- Küldje el az ajánlatot közzétételre a **Submit (Küldés**) gombra kattintva. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz. Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvános közzétételére (vagy ha egy privát ajánlatra a privát közönség számára).

### <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

A közzétételi folyamat **manuális érvényesítési** lépése az ajánlat és a hozzá kapcsolódó technikai eszközök (különösen a Azure Resource Manager sablon) átfogó áttekintését jelenti, a problémák általában lekéréses kérelmek (PR)-hivatkozásként jelennek meg. A következő témakörből megtudhatja, hogyan tekintheti meg és válaszolhatja meg ezeket a jogcímeket: [felülvizsgálati visszajelzések feldolgozása](./azure-apps-review-feedback.md).

Ha a közzétételi lépések közül egy vagy több hibát észlelt, ki kell javítania őket, és újra közzé kell tennie az ajánlatot.

## <a name="next-steps"></a>Következő lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
