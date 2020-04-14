---
title: Új Azure Apps-ajánlat létrehozása a kereskedelmi piacon
description: Új Azure Apps-ajánlat létrehozása az Azure Piactéren, az AppSource-ban vagy a Felhőszolgáltató (CSP) programon keresztül történő listázáshoz vagy értékesítéshez a Microsoft Partner Center Kereskedelmi piactér portálján keresztül.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 260193b3c71e29a32e0589f50864ffcca2e9a67c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262264"
---
# <a name="create-an-azure-application-offer"></a>Azure-alkalmazásajánlat létrehozása

Az Azure-alkalmazásajánlat kereskedelmi piacon való közzétételének lépéseit itt ismerteti.

## <a name="azure-application-offer-type"></a>Az Azure-alkalmazásajánlat típusa

Ez a témakör ismerteti az Azure-alkalmazásajánlatok alapjait.  Ezeket a fogalmakat ismernie kell, mielőtt elkezdene egy új Azure-alkalmazásajánlatot közzétenni a Marketplace-en.

### <a name="publishing-overview"></a>Közzététel – áttekintés

A [videobuilding solution templates és a managed applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) az Azure-alkalmazásajánlat típusának bemutatása:

* Milyen ajánlattípusok érhetők el;
* Milyen technikai eszközökre van szükség;
* Azure Resource Manager-sablon készítése;
* Az alkalmazás felhasználói felületének fejlesztése és tesztelése;
* Az alkalmazásajánlat közzététele;
* A kérelem felülvizsgálati folyamata.

### <a name="types-of-azure-application-plans"></a>Az Azure-alkalmazáscsomagok típusai

Kétféle Azure-alkalmazáscsomagok, felügyelt alkalmazások és megoldássablonok.

* **A megoldássablon** a megoldások marketplace-en való közzétételének egyik fő módja. Ez a csomagtípus akkor használatos, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egyetlen virtuális gépen (VM) kívül.  A megoldássablonnal automatizálhatja több erőforrás, például virtuális gépek, a hálózatkezelés és a tárolási erőforrások biztosítását az összetett IaaS-megoldások biztosításához.  A megoldássablonok létrehozásáról az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) dokumentációjában olvashat bővebben.

* **A felügyelt alkalmazás** hasonló a megoldássablonokhoz, egy kulcskülönbséggel. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. A Felügyelt alkalmazások segítségével egyszerűen hozhat létre és szállíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfeleinek.  A felügyelt alkalmazások előnyeiről és típusairól az [Azure által felügyelt alkalmazások áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/managed-applications/overview)

Minden Azure-alkalmazás legalább két fájlt tartalmaz `.zip` az archívum gyökérmappájában:

* A [MainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)nevű Erőforrás-kezelő sablonfájl.  Ez a sablon határozza meg az ügyfél Azure-előfizetésében üzembe helyezhető erőforrásokat.  Példák a Resource Manager-sablonok, tekintse meg az [Azure gyorsindítási sablonok gyűjtemény](https://azure.microsoft.com/resources/templates/) vagy a megfelelő [GitHub: Azure Resource Manager gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates) társzáma.

* A [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)nevű Azure-alkalmazás létrehozási felületének felhasználói felületdefiníciója.  A felhasználói felületen elemeket ad meg, amelyek lehetővé teszik a felhasználók számára paraméterértékek megadását.

Minden új Azure-alkalmazásajánlatnak tartalmaznia kell egy [Azure-partner ügyfélhasználati guid azonosítót.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)

### <a name="before-you-begin"></a>Előkészületek

Tekintse át az alábbi Azure-alkalmazás dokumentációt, amely rövid útmutatókat, oktatóanyagokat és mintákat tartalmaz.

* [Az Azure Resource Manager-sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Gyorsútmutatók:

    * [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure gyorsindítási sablonjai](https://github.com/azure/azure-quickstart-templates)
    * [Alkalmazásdefiníció közzététele](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Szolgáltatáskatalógusban elérhető alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Oktatóanyagok:

    * [Definíciós fájlok létrehozása](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Piactéren elérhető alkalmazás közzététele](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Minták:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Felügyelt alkalmazási megoldások](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>A műszaki ismeretek alapjai

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges.

A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat:

* Az [Azure-szolgáltatások](https://azure.microsoft.com/services/)alapvető ismerete.
* [Azure-alkalmazások tervezése és megtervezése.](https://azure.microsoft.com/solutions/architecture/)
* Az [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), az Azure [Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure Networking munkaismerete.](https://azure.microsoft.com/services/?filter=networking#networking)
* Az [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)munkaismerete.
* A [JSON](https://www.json.org/)munkaismerete.

### <a name="suggested-tools"></a>Javasolt eszközök

Az Azure-alkalmazás kezeléséhez válasszon az alábbi parancsfájl-kezelési környezetek egyikét vagy mindkettőt:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Javasoljuk, hogy adja hozzá a következő eszközöket a fejlesztői környezethez:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio-kód](https://code.visualstudio.com/) a következő bővítményekkel:
    * Bővítmény: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Kiterjesztés: [Szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Kiterjesztés: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Tekintse át a rendelkezésre álló eszközöket az [Azure Developer Tools](https://azure.microsoft.com/tools/) lapon.  A Visual Studio használata esetén is a [Visual Studio Piactér .](https://marketplace.visualstudio.com/)

## <a name="create-an-azure-application-offer"></a>Azure-alkalmazásajánlat létrehozása

Az Azure-alkalmazásajánlat létrehozása előtt létre kell [hoznia egy Partnerközpont-fiókot,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) és meg kell nyitnia a [kereskedelmi piactér irányítópultját,](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)és az **Áttekintés** lapon kell megnyitnod.

>[!Note]
>Az ajánlat közzététele után a Partnerközpontban tett ajánlat szerkesztése csak az újbóli közzétételt követően frissül a rendszerben és a kirakatban.  Kérjük, győződjön meg arról, hogy a módosítások után benyújtja a közzétételi ajánlatot.

### <a name="create-a-new-offer"></a>Új ajánlat létrehozása

Válassza a **+ Új ajánlat** gombot, majd az Azure **Alkalmazás** menüelemet. Megjelenik **az Új ajánlat** párbeszédpanel.

### <a name="offer-id-and-alias"></a>Ajánlatazonosító és alias

* **Ajánlatazonosító:** A fiókban lévő minden egyes ajánlat egyedi azonosítója. Ez az azonosító látható lesz az ügyfelek számára a piactéri ajánlat és az Azure Resource Manager-sablonok URL-címében (ha van ilyen). <br> <br> Az ajánlatazonosítónak kisalfanumerikus karakternek kell lennie (beleértve a kötőjeleket és az aláhúzásjeleket, de nem térköz). Legfeljebb 50 karakter ből állhat, és a Létrehozás után nem módosítható. <br> <br> Ha például ide `test-offer-1` írja be, az `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`ajánlat URL-címe a . 

* **Ajánlat alias**: A partnerközpontban az ajánlatra való hivatkozáshoz használt név. Ez a név nem lesz használva a piactéren, és eltér az ajánlat nevét és egyéb értékeket, amelyek megjelennek az ügyfelek számára. Ez az érték nem módosítható a **Létrehozás (Létrehozás) lehetőséget**követően.

Miután megadta **az ajánlatazonosítóját** és **az Ajánlat aliasát,** válassza a **Létrehozás lehetőséget.** Ezután az ajánlat minden más részén dolgozhat.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **Ajánlat beállítási** lapja a következő információkat kéri. A mezők **kitöltése** után válassza a Mentés lehetőséget.

### <a name="test-drive"></a>Tesztvezetés

A tesztvezetés nagyszerű módja annak, hogy bemutassa ajánlatát a potenciális ügyfeleknek azáltal, hogy lehetőséget ad nekik arra, hogy "vásárlás előtt próbáljanak ki", ami fokozott konverziót és magasan képzett érdeklődők létrehozását eredményezi. [További információ a tesztvezetésekről.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

A tesztvezetés engedélyezéséhez jelölje be a **Tesztmeghajtó engedélyezése** jelölőnégyzetet. Ezután be kell állítania egy demonstrációs környezetet a [Tesztmeghajtó műszaki konfigurációkonfigurálásában,](#types-of-azure-application-plans) hogy az ügyfelek meghatározott ideig kipróbálhatják az ajánlatot. 

>[!Note]
>Mivel az összes Azure-alkalmazás egy Azure Resource Manager-sablon használatával valósul meg, az Azure-alkalmazásokhoz konfigurálható tesztmeghajtók egyetlen típusa az [Azure Resource Manager alapú tesztmeghajtó.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)

#### <a name="additional-test-drive-resources"></a>További tesztmeghajtó-erőforrások

- [A tesztvezetés műszaki gyakorlati gyakorlatai](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [A Test Drive marketingjének bevált módszerei](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Tesztvezetés – áttekintés egy pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Csatlakoztassa az érdeklődőkezelést

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információt az [Érdeklődőkezelés áttekintése című témakörben talál.](./commercial-marketplace-get-customer-leads.md)

Ne feledje, hogy **mentse,** mielőtt a következő szakasz!

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon meghatározhatja az ajánlat csoportosításához használt kategóriákat és iparágakat a piactéren, az alkalmazás verzióján és az ajánlatot támogató jogi szerződéseken. A lap befejezése után válassza a **Mentés** gombot.

### <a name="category"></a>Kategória

Válasszon ki legalább egy és legfeljebb három kategóriát, amelyek segítségével az ajánlatot a megfelelő piactérkeresési területekre helyezi. Ügyeljen arra, hogy hívja ki, hogy az ajánlat támogatja ezeket a kategóriákat az ajánlat leírása. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>A Microsoft kereskedelmi piacterének általános szerződése

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Ajánlat lista

Az Ajánlat listalap azokat a nyelveket jeleníti meg, amelyeken az ajánlat megjelenik. Jelenleg az **angol (Egyesült Államok)** az egyetlen elérhető lehetőség.

Meg kell adnia a piactér adatait (ajánlat neve, leírása, képek stb.) az egyes nyelvekhez/piacokhoz. Válassza ki az információ megadásához szükséges nyelvet/piacnevet.

> [!NOTE]
> Az ajánlat tartalma (például leírás, dokumentumok, képernyőképek, használati feltételek stb.) nem szükséges angol nyelven, amennyiben az ajánlat leírása a következő mondattal kezdődik: "Ez az alkalmazás csak [nem angol nyelven] érhető el." Az is elfogadható, hogy *egy hasznos link URL-t* kínál tartalmat más nyelven, mint amelyet az ajánlat lista tartalmát.

### <a name="name"></a>Név

Az itt megadott név az ajánlathirdetés címeként jelenik meg az ügyfelek számára. Ez a mező előre ki van töltve az **Ajánlat aliashoz** az ajánlat létrehozásakor megadott szöveggel, de ezt az értéket módosíthatja. Ez a név védjeggyel is ellátott lehet (és ön védjegy- vagy szerzői jogi szimbólumokat is tartalmazhat). A név nem lehet több 50 karakternél, és nem tartalmazhat hangulatjeleket.

### <a name="summary"></a>Összefoglalás

Adja meg az ajánlat rövid leírását (legfeljebb 100 karakter), amely a piactér keresési eredményei között is használható.

### <a name="long-summary"></a>Hosszú összegzés

Adjon meg hosszabb leírást az ajánlatról (legfeljebb 256 karakter). A leírás használható a piactér keresési eredményei között.

### <a name="description"></a>Leírás

Adjon meg hosszabb leírást az ajánlatról (legfeljebb 3000 karakter). Ez a leírás jelenik meg az ügyfelek számára a piactéri lista áttekintése. Adja meg az ajánlat értékajánlatát, a legfontosabb előnyöket, a kategória- és/vagy iparági szövetségeket, az alkalmazáson belüli vásárlási lehetőségeket és a szükséges közzétételeket. 

Néhány tipp a leírás megírásához:  

- Egyértelműen írja le az ajánlat értékajánlatát a leírás első néhány mondatában. Az értékajánlatban a következő elemek szerepeljenek:
  - A termék leírása
  - A termék előnyeit élvező felhasználó típusa
  - A termék által megszólított vásárlói igények vagy fájdalom
- Ne feledje, hogy az első néhány mondat megjelenhet a keresőmotor találatai között.  
- Ne hagyatkozzon a termék értékesítéséhez szükséges funkciókra és funkciókra. Ehelyett összpontosítson a szállít.  
- A lehető legnagyobb mértékben használjon iparág-specifikus szókincset vagy haszonalapú megfogalmazást. 
- Fontolja meg a HTML-címkék használatát a leírás formázásához és vonzóbbá.

### <a name="search-keywords"></a>Kulcsszavak keresése

Tetszés szerint legfeljebb három keresési kulcsszót adhat meg, hogy az ügyfelek megtalálják az ajánlatot a piacon. A legjobb eredmény érdekében próbálja meg ezeket a kulcsszavakat is használni a leírásában.

### <a name="support-urls"></a>Támogatási URL-ek

Ebben a szakaszban hivatkozásokat adhat meg, amelyek segítségével az ügyfelek jobban megismerhetik az ajánlatot.

#### <a name="privacy-policy-url"></a>Adatvédelmi irányelvek URL-címe

Adja meg a szervezet adatvédelmi irányelveinek URL-címét. Ön felelős annak biztosításáért, hogy alkalmazása megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint hogy érvényes adatvédelmi szabályzatot biztosítson.

#### <a name="useful-links"></a>Hasznos hivatkozások

Adjon meg opcionális kiegészítő online dokumentumokat a megoldásról.  További hasznos hivatkozások hozzáadása a **+ Hivatkozás hozzáadása gombra**kattintva .

### <a name="contacts"></a>Kapcsolatok

Ebben a szakaszban meg kell adnia egy **támogatási partner** és egy mérnöki partner nevét, e-mail címét és **telefonszámát.** Ez az információ nem jelenik meg az ügyfelek számára, de elérhető lesz a Microsoft számára, és a kripta-partnerek rendelkezésére állhat.

A **Támogatás kapcsolatrészben** meg kell adnia a **támogatási URL-címet** is, ahol a CSP-partnerek támogatást találhatnak az ajánlathoz.

### <a name="marketplace-images"></a>Marketplace-képek

Ebben a szakaszban olyan emblémákat és képeket adhat meg, amelyeket az ajánlat ügyfélnek való megjelenítésekor fog használni. Minden képnek .png formátumúnak kell lennie.

#### <a name="store-logos"></a>Áruházi emblémák

Adja meg az ajánlat logóját három méretben: **Kicsi (48 x 48)**, **Közepes (90 X 90)** és **Nagy (216 x 216)**.

#### <a name="hero"></a>Hős

A hőskép nem kötelező. Ha ön beszerez egy, ez must mér 815 x betű 290 fénykép.

#### <a name="screenshots"></a>Képernyőképek

Képernyőképek hozzáadása, amelyek bemutatják az ajánlat működését. Legfeljebb öt képernyőképet adhat hozzá. Minden képernyőképnek 1280 x 720 képpontnak kell lennie.

#### <a name="videos"></a>Videók

Az ajánlatot jól látható anamnézisben legfeljebb öt videót adhatsz hozzá. Ezeket a videókat a YouTube-on és/vagy a Vimeo-n kell üzemeltetni. Mindegyikhez adja meg a videó nevét, URL-címét és a videó miniatűr képét (1280 x 720 képpont).

#### <a name="additional-marketplace-listing-resources"></a>További piactér-listaforrások

- [Gyakorlati tanácsok a piactéri ajánlatlistákhoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Előzetes verzió

Az **Előnézet** lapon korlátozott **előnézeti közönséget** határozhat meg az ajánlat érvényesítéséhez, mielőtt élőben közzétenné az ajánlatot a szélesebb piactéri közönség számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után az ajánlat megtekintése után **az Élő adásban** lehetőséget kell választania, mielőtt az ajánlat élőben megjelenik a piactér nyilvános közönsége számára.

Az előzetes verzióközönséget az Azure-előfizetésazonosítók azonosítói azonosítják, és mindegyikhez egy nem kötelező leírás is szerepel.  Egyik mező sem látható a vevők számára.

Akár 10 Azure-előfizetési azonosítót manuálisan, vagy akár 100-at is hozzáadhat CSV-fájl feltöltésekor.  Az előfizetések hozzáadásával olyan közönséget határoz meg, amely a teljes közzététel előtt előzetes hozzáférést biztosít az ajánlathoz.  Ha az ajánlat már él, továbbra is megadhat egy előnézeti közönséget az ajánlat módosításainak vagy frissítéseinek teszteléséhez.

>[!Note]
>Az előnézeti közönség eltér a privát közönségtől. Az előnézeti közönség hozzáférhet az ajánlathoz, *mielőtt* élőben közzétesszük őket a piactereken. Dönthet úgy is, hogy létrehoz egy tervet, és csak egy privát közönség számára teszi elérhetővé (a Terv rendelkezésre állása lapon).  Az előnézeti közönség láthatja és érvényesítheti az összes tervet, beleértve azösszes olyan tervet is, amely csak a privát közönség számára lesz elérhető, miután az ajánlata teljes mértékben megjelent a piacon.

## <a name="plan-overview"></a>Terv – áttekintés

A **Terv áttekintése** lapon különböző tervbeállításokat adhat meg ugyanazon az ajánlaton belül. Ezek a csomagok (a felhőpartner-portálon található SKI-k) a terv típusa (megoldássablon és felügyelt alkalmazás), a monetization vagy a közönség tekintetében eltérőek lehetnek.  Konfiguráljon legalább egy csomagot az ajánlat marketplace-en való listázásához.

A létrehozás után látni fogja a tervneveket, azonosítókat, tervtípusát, elérhetőségét (nyilvános vagy privát), az aktuális közzétételi állapotot és az ezen a lapon elérhető műveleteket.

**A** Terv **áttekintésében** elérhető műveletek a terv aktuális állapotától függően változnak, és a következőket tartalmazhatják:

* Ha a terv állapota **Vázlat** – Vázlat törlése.
* Ha a csomag állapota **Élő** – Stop értékesítési terv vagy Privát közönség szinkronizálása.

### <a name="create-new-plan"></a>Új terv létrehozása

***Tervazonosító*** – Hozzon létre egy egyedi tervazonosítót az ajánlatban szereplő minden egyes tervhez. Ez az azonosító látható lesz a vásárlók számára a termék URL-címében.  Csak kisbetűs, alfanumerikus karaktereket, kötőjeleket vagy aláhúzásjeleket használjon. Ehhez a csomagazonosítóhoz legfeljebb 50 karakter engedélyezett. Ez az azonosító nem módosítható a létrehozás kiválasztása után.

***Terv neve*** – Az ügyfelek ezt a nevet fogják látni, amikor eldöntik, hogy melyik csomagot válasszák ki az ajánlaton belül. Hozzon létre egy egyedi ajánlat nevét az ajánlatban szereplő minden csomaghoz. A terv neve az ugyanazon ajánlat részét használó szoftvercsomagok (pl. Ajánlat neve: Windows Server; csomagok: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>A terv beállítása

A **Tervezés beállítása** lapon beállíthatja a terv típusának magas szintű konfigurációját, azt, hogy újrafelhasználja-e a csomagokat egy másik csomagból, és milyen felhőkben legyen elérhető a terv.  Az ezen a lapon található válaszok hatással lesznek arra, hogy mely mezők jelenjenek meg az ugyanazon terv többi lapján.

#### <a name="plan-type"></a>Terv típusa

Az [Azure-alkalmazáscsomagok típusai](#types-of-azure-application-plans)című dokumentumban ismertetett módon válassza ki, hogy a csomag tartalmaz-e megoldássablont vagy felügyelt alkalmazást.

#### <a name="this-plan-reuses-packages"></a>Ez a terv újrafelhasználja a csomagokat

Ha egynél több azonos típusú csomaggal rendelkezik, és a csomagok azonosak közöttük, akkor ezt **a tervet egy másik csomag csomagjainak újrafelhasználása után**választhatja.  Ha ezt a lehetőséget választja, kiválaszthatja az ajánlathoz hasonló típusú csomagok újrafelhasználásához hasonló típusú többi csomagot. 

>[!Note]
>Ha egy másik csomagból származó csomagokat újra felhasznál, a teljes Műszaki konfiguráció lap eltűnik ebből a tervből.  Ehhez a tervhez a másik csomag műszaki konfigurációs adatait is felhasználjuk, beleértve a jövőben elkészült frissítéseket is. <br> <br> A terv közzététele után ez a beállítás nem módosítható.

#### <a name="cloud-availability"></a>Felhő elérhetősége

Ezt a csomagot legalább egy felhőben elérhetővé kell tenni. 

Válassza ki a **Nyilvános Azure** lehetőséget, hogy a megoldás telepíthető legyen az ügyfelek számára az összes nyilvános Azure-régióban, amelyek Marketplace-integrációval rendelkeznek.  További információ a [földrajzi elérhetőségről.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Válassza ki az **Azure Government Cloud** opciót, hogy a megoldás telepíthető legyen az Azure Government [Cloud-](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)ban, egy kormányzati közösségi felhőben, amely ellenőrzött hozzáféréssel rendelkezik az Egyesült Államok szövetségi, állami, helyi vagy törzsi ügyfelei számára, és az entitások kiszolgálására jogosult partnerek számára.  Ön, mint a közzétevő, felelős a megfelelőségi vezérlőkért, a biztonsági intézkedésekért és a felhőalapú közösség kiszolgálásához ajánlott eljárásokért.  Az Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).  Az Azure [Government](https://aka.ms/azuregovpublish)általi közzététel előtt a Microsoft azt javasolja, hogy tesztelje és érvényesítse a megoldást a környezetben, mivel bizonyos végpontok eltérőek lehetnek. A megoldás szakaszba hozásához és teszteléséhez kérjen próbafiókot erről a [linkről.](https://azure.microsoft.com/global-infrastructure/government/request/)

>[!Note]
>Ha egy csomag elérhetővé válik egy adott felhőben, a felhő nem távolítható el.

**Az Azure Government Felhőminősítései**

Ez a beállítás csak akkor látható, ha az **Azure Government Cloud** a Felhő **elérhetősége**alatt van kiválasztva.

Az Azure Government szolgáltatásai olyan adatokat kezelnek, amelyekre bizonyos kormányzati előírások és követelmények vonatkoznak, például a FedRAMP, a NIST 800.171 (DIB), az ITAR, az IRS 1075, a DoD L4 és a CJIS.  Annak érdekében, hogy felhívják a figyelmet a programok tanúsítványaira, legfeljebb 100, a minősítéseket leíró hivatkozást adhat meg.  Ezek a linkek lehetnek közvetlenül a programon található hirdetésre mutató linkek, vagy a saját webhelyein való megfelelés leírására mutató hivatkozások.  Ezek a hivatkozások csak az Azure Government Cloud-ügyfelek számára lesznek láthatók.

## <a name="plan-listing"></a>A terv listája

A **Terv listaelem** lapja megjeleníti a tervspecifikus listainformációkat, amelyek különbözőek lehetnek az ugyanazon ajánlathoz tartozó különböző tervek között.

### <a name="name"></a>Név

A név alapján előre kitöltött csomag a létrehozáskor.  Ez a név jelenik meg a piactéren megjelenő "Szoftvercsomag" címeként.  Legfeljebb 100 karaktert tartalmazhat.

### <a name="summary"></a>Összefoglalás

Rövid encika összegzése a szoftvercsomagról.  Legfeljebb 100 karaktert tartalmazhat.

### <a name="description"></a>Leírás

Ez a leírás lehetőséget ad arra, hogy elmagyarázza, mi teszi ezt a szoftvertervet egyedivé, és miben különbözik az ajánlaton belüli egyéb szoftvertervektől. Legfeljebb 2000 karaktert tartalmazhat.

A mezők **kitöltése** után válassza a Mentés lehetőséget.

## <a name="availability"></a>Rendelkezésre állás

A **Rendelkezésre állás** lap csak a megoldássablon-tervek nél látható.  A tervet mindenki számára láthatóvá teheti, csak bizonyos ügyfelek (privát közönség) számára, és azt, hogy a tervet elrejtse-e más megoldássablon vagy felügyelt alkalmazások számára.

### <a name="plan-audience"></a>Célközönség megtervezése

Lehetősége van úgy konfigurálni az egyes terveket, hogy mindenki vagy csak egy általad választott közönség számára látható legyen. Ebben a korlátozott közönségben az Azure-előfizetés-azonosítók használatával rendelhet tagságot.

**Adatvédelem / Ez egy privát csomag** (Opcionális jelölőnégyzet) - Jelölje be ezt a jelölőnégyzetet, hogy a terv privát és látható csak a korlátozott közönség az Ön által választott. A privát csomagként való közzétételt követően frissítheted a célközönséget, vagy dönthetsz úgy, hogy a csomagot mindenki számára elérhetővé tesszük. Ha egy tervet mindenki számára láthatóként tesz közzé, annak mindenki számára láthatónak kell maradnia. (A terv nem konfigurálható újra magáncsomagként).

**Korlátozott közönség (Azure-előfizetés-azonosítók)** – Rendelje hozzá azt a közönséget, amely hozzáfér ehhez a privát csomaghoz. Az Access azure-előfizetés-azonosítók használatával van hozzárendelve, és minden hozzárendelt Azure-előfizetés-azonosító leírását meg kell adni. Legfeljebb 10 előfizetési azonosító adható hozzá, vagy 20 000 ügyfél előfizetési azonosítója .csv számolótábla-fájl importálása esetén.  Az Azure-előfizetésazonosítók GUID azonosítókként jelennek meg, a betűknek pedig kisbetűsnek kell lenniük.

>[!Note]
>A privát közönség (vagy a korlátozott célközönség) eltér az [**Előnézet**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) lapon megadott előnézeti közönségtől.  Az előnézeti közönség hozzáférhet az *ajánlathoz, mielőtt* az ajánlatot élőben közzétették volna a piactéren. Bár a privát közönség megjelölése csak egy adott csomagra vonatkozik, az előnézeti közönség az összes (privát vagy nem) csomagot megtekintheti érvényesítési célból.

### <a name="hide-plan"></a>Terv elrejtése

Ha a megoldássablon csak közvetetten telepíthető, ha egy másik megoldássablonon vagy felügyelt alkalmazáson kívül hivatkozik, jelölje be ezt a jelölőnégyzetet a megoldássablon közzétételéhez, de közvetlenül az ügyfelek elől való elrejtéséhez.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

A **Díjszabás és rendelkezésre állás** lap csak a felügyelt alkalmazáscsomagok nál látható.  Beállíthatja azokat a piacokat, amelyeken ez a csomag elérhető lesz, a megoldás kezelésének havi árát, valamint azt, hogy a tervet mindenki számára láthatóvá tegye-e, vagy csak bizonyos ügyfelek (privát közönség) számára.

### <a name="markets"></a>Piacok

Minden tervnek legalább egy piacon elérhetőnek kell lennie. Jelölje be a jelölőnégyzetet minden olyan piacon, ahol ezt a csomagot elérhetővé szeretné tenni. A súgó ban szerepel egy keresőmező és gomb az "Adóelengedési" országok kiválasztásához, amelyekben a Microsoft az Ön nevében átutalja az értékesítési és használati adót.

Ha már beállította a terv árait amerikai dollárban (USD), és egy másik piaci helyszínt ad meg, az új piac árát az aktuális árfolyamok alapján számítjuk ki. Közzététel előtt mindig tekintse át az egyes piacok árát. Az árak felülvizsgálhatók az "Export árak (xlsx)" hivatkozással a módosítások mentése után.

### <a name="pricing"></a>Díjszabás

Adja meg a csomag havi árát.  Ez az ár az Azure-infrastruktúrán vagy a használatalapú használatalapú szoftverköltségeken felül értendő, amelyek a megoldás által üzembe helyezett erőforrásoknál merülnek fel.

A helyi pénznemben (USD = amerikai dollár) meghatározott árakat a rendszer a beállítás során rendelkezésre álló aktuális árfolyamok alapján az összes kiválasztott piac helyi pénznemére váltja át. A közzététel előtt ellenőrizze ezeket az árakat az árképzési táblázat exportálásával és az egyes piacok árainak áttekintésével. Ha egyéni árakat szeretne beállítani egy adott piacon, módosítsa és importálja az árképzési táblázatot. 

>[!Note]
>Az árképzési adatok exportálásának engedélyezéséhez először mentenie kell az árképzési módosításokat.

A közzététel előtt alaposan tekintse át az árakat, mivel a terv közzététele után bizonyos korlátozások vonatkoznak arra, hogy mi változhat.  

>[!Note]
>Ha a csomagban megjelenik egy piac ára, később nem módosítható.

### <a name="plan-audience"></a>Célközönség megtervezése

Lehetősége van úgy konfigurálni az egyes terveket, hogy mindenki vagy csak egy általad választott közönség számára látható legyen. Ebben a korlátozott közönségben az Azure-előfizetés-azonosítók használatával rendelhet tagságot.

**Adatvédelem / Ez egy privát csomag** (Opcionális jelölőnégyzet) - Jelölje be ezt a jelölőnégyzetet, hogy a terv privát és látható csak a korlátozott közönség az Ön által választott. A privát csomagként való közzétételt követően frissítheted a célközönséget, vagy dönthetsz úgy, hogy a csomagot mindenki számára elérhetővé tesszük. Ha egy tervet mindenki számára láthatóként tesz közzé, annak mindenki számára láthatónak kell maradnia. (A terv nem konfigurálható újra magáncsomagként).

**Korlátozott közönség (Azure-előfizetés-azonosítók)** – Rendelje hozzá azt a közönséget, amely hozzáfér ehhez a privát csomaghoz. Az Access azure-előfizetés-azonosítók használatával van hozzárendelve, és minden hozzárendelt Azure-előfizetés-azonosító leírását meg kell adni. Legfeljebb 10 előfizetési azonosító adható hozzá, vagy 20 000 ügyfél előfizetési azonosítója .csv számolótábla-fájl importálása esetén.  Az Azure-előfizetésazonosítók GUID azonosítókként jelennek meg, a betűknek pedig kisbetűsnek kell lenniük.

>[!Note]
>A privát közönség (vagy a korlátozott célközönség) eltér az [**Előnézet**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) lapon megadott előnézeti közönségtől.  Az előnézeti közönség hozzáférhet az *ajánlathoz, mielőtt* az ajánlatot élőben közzétették volna a piactéren. Bár a privát közönség megjelölése csak egy adott csomagra vonatkozik, az előnézeti közönség az összes (privát vagy nem) csomagot megtekintheti érvényesítési célból.

## <a name="technical-configuration"></a>Műszaki konfiguráció 

A **műszaki konfiguráció** lapon feltöltheti a központi telepítési csomagot, amely lehetővé teszi az ügyfelek számára a csomag üzembe helyezését.

>[!Note]
>Ez a lap nem lesz látható, ha úgy állította be ezt a tervet, hogy a **Terv beállítása** lapon egy másik csomagból származó csomagokat használjon fel újra.

### <a name="package-details"></a>A csomag részletei

A **Csomag részletei** allap lehetővé teszi a technikai konfiguráció vázlatverziójának szerkesztését.

***Verzió*** – A műszaki konfiguráció aktuális verziójának hozzárendelése.  A lap minden egyes módosításakor növekményezheti ezt a verziót. A verziónak formátumban `{integer}.{integer}.{integer}`kell lennie.

***Csomagfájl*** `.zip`( ) - Ez a csomag tartalmazza a tervhez szükséges összes sablonfájlt, `.zip` valamint a fájlként csomagolt további erőforrásokat.

Minden Azure-alkalmazáscsomag-csomagnak tartalmaznia kell ezt `.zip` a két fájlt az archívum gyökérmappájában:

* A [MainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)nevű Erőforrás-kezelő sablonfájl.  Ez a sablon automatizálja az erőforrások üzembe helyezését az ügyfelek Azure-előfizetésben.  Példák a Resource Manager-sablonok, tekintse meg az [Azure gyorsindítási sablonok gyűjtemény](https://azure.microsoft.com/documentation/templates/) vagy a megfelelő [GitHub: Azure Resource Manager gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates) társzáma.

* A [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)nevű Azure-alkalmazás létrehozási felületének felhasználói felületdefiníciója.

Minden új Azure-alkalmazásajánlatnak tartalmaznia kell egy [Azure-partner ügyfélhasználati](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) guid azonosítót is.

### <a name="previously-published-packages"></a>Korábban közzétett csomagok 

A **Korábban közzétett csomagok** allap lehetővé teszi a műszaki konfiguráció összes közzétett verziójának megtekintését.

## <a name="technical-configuration-managed-application-plans-only"></a>Műszaki konfiguráció (csak felügyelt alkalmazástervek)

A felügyelt alkalmazástervek a fent ismertetett **Verzió-** és **Csomagfájl-mezőkön** túl további összetettséggel rendelkeznek a **Műszaki konfiguráció** lapon. 

### <a name="enable-just-in-time-jit-access"></a>Just-in-time (JIT) hozzáférés engedélyezése

Ezzel a beállítással engedélyezheti a just-in-time (JIT) hozzáférést ehhez a tervhez.  JIT-hozzáférés lehetővé teszi, hogy kérjen emelt szintű hozzáférést a felügyelt alkalmazások erőforrásait hibaelhárítás vagy karbantartás. Mindig csak olvasható hozzáféréssel rendelkezik az erőforrásokhoz, de egy adott időszakban nagyobb hozzáféréssel rendelkezhet.  További információt az Azure Felügyelt alkalmazások hoz való azonnali hozzáférés engedélyezése és kérése című [témakörben talál.](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)  Ha azt szeretné, hogy a kezelt alkalmazás fogyasztói állandó hozzáférést biztosítsanak a fiókhoz, hagyja bejelölve ezt a beállítást.

>[!Note]
>Ügyeljen arra, `createUiDefinition.json` hogy frissítse a fájlt annak érdekében, hogy támogassa ezt a funkciót.  

### <a name="deployment-mode"></a>Telepítési mód

Adja meg, hogy **a csomag** telepítésekor a teljes vagy **a növekményes telepítési módot** kívánja-e konfigurálni: 

* **Teljes módban**az alkalmazás ügyfél általi átcsoportosítása a kezelt erőforráscsoport erőforrásainak eltávolítását eredményezi, `mainTemplate.json`ha az erőforrások nincsenek definiálva a ban. 
* **Növekményes módban**az alkalmazás átcsoportosítása változatlanul hagyja a meglévő erőforrásokat.

A telepítési módokról az [Azure Resource Manager telepítési módjai](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)ban olvashat bővebben.

### <a name="notification-endpoint-url"></a>Értesítés végpontjának URL-címe

Adjon meg egy HTTPS Webhook-végpontot, amely értesítéseket kap a csomagverzió felügyelt alkalmazáspéldányain végzett összes CRUD-műveletről.

### <a name="customize-allowed-customer-actions"></a>Az engedélyezett vevői műveletek testreszabása

Ezzel a beállítással megadhatja, hogy az ügyfelek az`*/read`alapértelmezés szerint elérhető " " műveletek mellett mely műveleteket hajthatják végre a felügyelt erőforrásokon. 

Itt pontosvesszővel elválasztva sorolja fel azokat a további műveleteket, amelyeket engedélyezni szeretne az ügyfélnek.  További információt az [Azure-erőforrások hozzárendelések megtagadásának ismertetése című](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)témakörben talál.  Az elérhető műveletekről az [Azure Resource Manager erőforrás-szolgáltató műveletei ben](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)található. Ha például engedélyezni szeretné a fogyasztóknak `Microsoft.Compute/virtualMachines/restart/action` a virtuális gépek újraindítását, adja hozzá az engedélyezett műveletekhez.

### <a name="global-azure--azure-government-cloud"></a>Globális Azure / Azure Government Cloud

Adja meg, hogy ki rendelkezik felügyeleti hozzáféréssel a felügyelt alkalmazáshoz az egyes támogatott felhőben.  A felügyelt erőforráscsoportnak engedélyt kérő felhasználók, csoportok vagy alkalmazások azonosítása az Azure Active Directory (AAD) identitások használatával történik.

***Azure Active Directory-bérlői azonosító*** – Az AAD-bérlői azonosító (más néven címtárazonosító), amely tartalmazza a felhasználók, csoportok vagy alkalmazások identitását, amelyeknek engedélyeket szeretne adni.  Az AAD-bérlői azonosítót az Azure Portalon, az [Azure Active Directory tulajdonságai](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)ban találja.

***Engedélyek*** – adja hozzá az Azure Active Directory-objektum azonosítóját annak a felhasználónak, csoportnak vagy alkalmazásnak, amelyengedélyt szeretne adni a felügyelt erőforráscsoportnak. Azonosítsa a felhasználót a fő azonosítójuk alapján, amely az Azure Active Directory felhasználói nak paneljén található [az Azure Portalon.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)

Minden fő, válassza ki az egyik az Azure AD beépített szerepkörök a listából (tulajdonos vagy közreműködő). A kiválasztott szerepkör leírja, hogy a főtag milyen engedélyekkel rendelkezik az ügyfél-előfizetés erőforrásaira vonatkozóan. További információkért lásd az [Azure-erőforrások beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  A szerepköralapú hozzáférés-vezérlésről (RBAC) az [RBAC első lépései az Azure Portalon](https://docs.microsoft.com/azure/role-based-access-control/overview)című témakörben talál további információt.

>[!Note]
>Bár felhőnként legfeljebb 100 engedélyt adhat hozzá, általában egyszerűbb létrehozni egy Active Directory felhasználói csoportot, és megadni az azonosítóját az "Egyszerű azonosító" alatt.  Ez lehetővé teszi, hogy a terv üzembe helyezése után további felhasználókat adjon hozzá a felügyeleti csoporthoz, és csökkentse a terv frissítésének szükségességét, csak hogy további engedélyeket adjon hozzá.

### <a name="policy-settings"></a>Szabályzatbeállítások

Az [Azure-szabályzatok](https://docs.microsoft.com/azure/governance/policy/overview) alkalmazása a felügyelt alkalmazásmegfelelőségi követelmények meghatározásához a telepített megoldás.  A szabályzatdefiníciókról és a paraméterértékek formátumáról tekintse meg a következő dokumentumot: [Azure Policy-minták](https://docs.microsoft.com/azure/governance/policy/samples/index).  Legfeljebb öt házirendet állíthat be, és az egyes házirendek beállításnak csak egy példányát.  Egyes házirendek további paramétereket igényelnek.  A standard termékváltozat szükséges a naplózási házirendek.  A házirend neve legfeljebb 50 karakter ből állhat.

## <a name="co-sell"></a>Együttes értékesítés

A Cosell lapon történő tájékoztatás teljes mértékben opcionális az ajánlat közzétételéhez. A Co-sell Ready és az IP Co-sell Ready státusz eléréséhez szükséges. A megadott adatokat a Microsoft értékesítési csapatai arra használják fel, hogy többet tudjanak meg a megoldásról, amikor kiértékelik, hogy azok megfelelnek-e az ügyfelek igényeinek. Nem érhető el közvetlenül az ügyfelek számára.

A lap kitöltésével kapcsolatos további információkért lásd: [Társértékesítés lehetőség a Partnerközpontban.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)

## <a name="test-drive"></a>Tesztvezetés

A **Tesztmeghajtó** lapon beállíthat egy bemutatót (vagy "tesztmeghajtót"), amely lehetővé teszi az ügyfelek számára, hogy kipróbálják az ajánlatot, mielőtt elkötelezné magukat a megvásárlás mellett. További információ: [Mi az a Tesztmeghajtó?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Ha már nem szeretne tesztvezetést biztosítani az ajánlatához, térjen vissza az **Ajánlat beállítási** oldalára, és törölje a jelet **a Tesztvezetés engedélyezése**jelölőnégyzetből.

### <a name="technical-configuration"></a>Műszaki konfiguráció

Az Azure Applications eredendően használja az Azure Resource Manager tesztmeghajtó típusát.  További [információ: Az Azure Resource Manager tesztmeghajtóműszaki konfigurációja.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)

### <a name="deployment-subscription-details"></a>Üzembe helyezési előfizetés részletei

A Test Drive az Ön nevében történő üzembe helyezéséhez hozzon létre és adjon meg egy külön, egyedi Azure-előfizetést. (Nem szükséges a Power BI tesztmeghajtóihoz).

- **Azure-előfizetés-azonosító** (az Azure Resource Manager és a Logic alkalmazások esetén szükséges): Adja meg az előfizetés-azonosítót, amely hozzáférést biztosít az Azure-fiókszolgáltatásaihoz az erőforrás-használati jelentések hez és a számlázáshoz. Azt javasoljuk, hogy fontolja meg [egy külön Azure-előfizetés tesztelésére,](https://docs.microsoft.com/azure/billing/billing-create-subscription) ha még nem rendelkezik. Az Azure-előfizetés-azonosítóját az Azure [Portalra](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **Előfizetések** lapjára való navigálással találhatja meg. A lapon megjelenik az előfizetésazonosítója (pl. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-bérlőazonosító** (kötelező): Adja meg az Azure Active Directory (AD) [bérlői azonosítóját.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Az azonosító megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)válassza a bal oldali menü Active Directory lapját, válassza a **Tulajdonságok lehetőséget, majd keresse meg a felsorolt **címtárazonosító** számot (pl. 50c464d3-4930-494c-963c-1e951d15360e). A tartománynév URL-címével is megkeresheti a szervezet [https://www.whatismytenantid.com](https://www.whatismytenantid.com)bérlői azonosítóját a következő címen: .

- **Azure AD-bérlői név** (dinamikus 365 esetén szükséges): Adja meg az Azure Active Directory (AD) nevét. Ennek a névnek a megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure AD alkalmazásazonosító** (kötelező): Adja meg az Azure Active Directory (AD) [alkalmazásazonosítóját.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Az azonosító megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)válassza a bal oldali menü Active Directory lapját, válassza az **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazásazonosítószámot** (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD alkalmazásügyfél-titkos** (kötelező): Adja meg az Azure [AD-alkalmazásügyfél-titkos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) Ennek az értéknek a megkereséséhez jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) Válassza az **Azure Active Directory** fülét a bal oldali menüben, válassza az **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a tesztmeghajtó alkalmazást. Ezután válassza **a Tanúsítványok és titkos kulcsok**lehetőséget, válassza az Új **ügyféltitok**lehetőséget, adjon meg egy leírást, válassza a **Soha** **a Lejáratok**csoportban a Soha lehetőséget, majd válassza **a Hozzáadás lehetőséget.** Győződjön meg róla, hogy másolja le az értéket. (Ne navigáljon el az oldalról az érték másolása előtt, különben nem lesz hozzáférése az értékhez.)

Ne feledje, hogy **mentse,** mielőtt a következő szakasz!

### <a name="test-drive-listings-optional"></a>Tesztvezetés listái (nem kötelező)

A **Test Drive listázási lista** a **Tesztmeghajtó** lapon található beállítás on-át jeleníti meg azokat a nyelveket (és piacokat), ahol a tesztvezetés elérhető, jelenleg az angol (Egyesült Államok) az egyetlen elérhető hely. Ezenkívül ezen az oldalon megjelenik a nyelvspecifikus listaállapot, valamint a hozzáadott dátum/idő. Meg kell határoznia a tesztvezetés részleteit (leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): Írja le a tesztvezetést, a bemutatást, a felhasználó által kísérletezendő célokat, a feltárandó funkciókat és minden olyan releváns információt, amely segít a felhasználónak eldönteni, hogy beszerezze-e az ajánlatot. Ebben a mezőben legfeljebb 3000 karakter nyi szöveg adható meg. 

- **Hozzáférés-információk** (az Azure Resource Manager és logic tesztmeghajtók esetén szükséges): Magyarázza el, hogy mit kell tudnia az ügyfélnek a tesztmeghajtó eléréséhez és használatához. Végigvezethet egy forgatókönyvet az ajánlat használatáról, és pontosan azt, hogy az ügyfélnek mit kell tudnia a funkciók eléréséhez a tesztvezetés során. Ebben a mezőben legfeljebb 10 000 karakternyi szöveg adható meg.

- **Használati útmutató** (kötelező): A tesztvezetés élményének részletes áttekintése. A felhasználói kézikönyvnek pontosan azt kell kiterjednie, hogy mit szeretne az ügyfélnek a tesztvezetés megtapasztalásából, és referenciaként kell szolgálnia az esetleges kérdéseikhez. A fájlnak PDF formátumúnak kell lennie, és feltöltés után el kell nevezni (legfeljebb 255 karakter).

- **Videók hozzáadása** (nem kötelező): A videókat fel lehet tölteni a YouTube-ra vagy a Vimeo-ra, és itt hivatkozással lehet hivatkozni egy linkre és egy miniatűr képre (533 x 324 képpont), hogy az ügyfél megtekinthesse az információk átjárását, hogy jobban megérthesse a tesztvezetést, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyveket.
  - **Név** (kötelező)
  - **URL (csak YouTube vagy Vimeo)** (kötelező)
  - **Thumbnail (533 x 324 px)**: A képfájlnak PNG formátumúnak kell lennie.

A mezők **kitöltése** után válassza a Mentés lehetőséget.

## <a name="publish"></a>Közzététel

### <a name="submit-offer-to-preview"></a>Ajánlat küldése előnézetbe

Miután elvégezte az ajánlat összes szükséges szakaszát, válassza a **közzététel** lehetőséget a portál jobb felső sarkában. A bizottság átirányítja a **Véleményezés és közzététel** lapra. 

Ha ez az első alkalom, hogy közzéteszi ezt az ajánlatot, a következőket teheti:

- Tekintse meg az ajánlat egyes szakaszainak készültségi állapotát.
    - *Nincs elindítva* - azt jelenti, hogy a szakaszt nem érintette meg, és be kell fejezni.
    - *Hiányos* - azt jelenti, hogy a szakasz hibákat, amelyeket ki kell javítani, vagy több információt kell megadni. Kérjük, menjen vissza a szakasz(ok) és frissítse azt.
    - *Kész* - azt jelenti, hogy a szakasz befejeződött, az összes szükséges adatot megadta, és nincsenek hibák. Az ajánlat minden szakaszának teljes állapotban kell lennie az ajánlat elküldése előtt.
- Adjon vizsgálati utasításokat a minősítő csoportnak, hogy az alkalmazás megfelelő tesztelése mellett adjon meg olyan kiegészítő megjegyzéseket is, amelyek hasznosak az alkalmazás megértéséhez.
- Küldje el az ajánlatot közzétételre a **Küldés**lehetőség kiválasztásával. Küldünk Önnek egy e-mailt, hogy tudd, ha az ajánlat előnézeti verziója elérhető, hogy áttekintsd és jóváhagyd. Térjen vissza a Partnerközpontba, és válassza a **Go-live** lehetőséget az ajánlat nyilvános közzétételéhez (vagy ha privát ajánlat, a privát közönség számára).

### <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

A közzétételi folyamat **manuális érvényesítési** lépése az ajánlat és a hozzá tartozó technikai eszközök (különösen az Azure Resource Manager-sablon) átfogó áttekintését jelenti, a problémák általában lekéréses kérelem (PR) hivatkozásként jelennek meg. A psz-ek megtekintésének és az azokra való válaszadásnak a magyarázata a [Véleményezési visszajelzések kezelése](./azure-apps-review-feedback.md)című témakörben látható.

Ha hibákat észlelt egy vagy több közzétételi lépésben, ki kell javítania őket, és újra közzé kell tennie az ajánlatot.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
