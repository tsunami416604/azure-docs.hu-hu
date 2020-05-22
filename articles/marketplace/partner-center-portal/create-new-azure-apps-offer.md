---
title: Azure-beli alkalmazás-ajánlat létrehozása – Microsoft kereskedelmi piactér
description: Ismerje meg az új Azure-alkalmazás létrehozásának lépéseit és szempontjait a partner Center kereskedelmi piactér portálján. Azure-alkalmazásait az Azure Marketplace-en vagy a Cloud Solution Provider (CSP) programon keresztül is listázhatja vagy értékesítheti.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 2885df9e64bdbfa21eaaa38f423b90554f767cd7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773270"
---
# <a name="create-an-azure-application-offer"></a>Azure-alkalmazásajánlat létrehozása

Ez a cikk ismerteti a kereskedelmi piactéren új Azure-alkalmazási ajánlat létrehozásának lépéseit és szempontjait. Az Azure-alkalmazások új ajánlatának létrehozása előtt ismernie kell ezeket a fogalmakat.

Mielőtt új Azure-alkalmazást szeretne közzétenni, [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , és ellenőrizze, hogy a fiókja regisztrálva van-e a kereskedelmi piactér programban.

## <a name="before-you-begin"></a>Előkészületek

Az Azure-alkalmazások tervezésével, létrehozásával és tesztelésével az Azure platform és az ajánlat létrehozásához használt technológiák technikai ismerete szükséges. A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákkal kapcsolatos ismereteket:

* Az Azure- [szolgáltatások](https://azure.microsoft.com/services/)alapszintű ismerete.
* [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/).
* Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)együttműködésének ismerete.
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)működésének ismerete.
* A [JSON](https://www.json.org/)működésének ismerete.

### <a name="technical-documentation-and-resources"></a>Technikai dokumentáció és források

Tekintse át a következő erőforrásokat, amikor előkészíti az Azure-alkalmazás ajánlatát a kereskedelmi piactéren.

* [Azure Resource Manager sablonok ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

* Gyorsútmutatók:

    * [Azure-gyorssablonok](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure gyorsindítási sablonok](https://github.com/azure/azure-quickstart-templates)
    * [Alkalmazásdefiníció közzététele](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Szolgáltatáskatalógusban elérhető alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Oktatóanyagok:

    * [Definíciós fájlok létrehozása](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Piactéren elérhető alkalmazás közzététele](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Minták

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Felügyelt alkalmazási megoldások](https://docs.microsoft.com/azure/managed-applications/sample-projects)

Az Azure Marketplace-hez készült videó- [létrehozási megoldási sablonok és a felügyelt alkalmazások](https://channel9.msdn.com/Events/Build/2018/BRK3603) átfogó bevezetést biztosítanak az Azure-alkalmazás ajánlatának típusára:

* Milyen ajánlati típusok érhetők el;
* Milyen technikai eszközök szükségesek;
* Azure Resource Manager sablon létrehozása;
* Az alkalmazás felhasználói felületének fejlesztése és tesztelése;
* Az alkalmazás ajánlatának közzététele;
* Az alkalmazás felülvizsgálati folyamata.

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

Az elérhető eszközöket az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon tekintheti át. Emellett a Visual Studio-t is használhatja a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="types-of-azure-application-plans"></a>Az Azure-alkalmazások csomagjainak típusai

Kétféle Azure-alkalmazási csomag létezik: megoldási sablonok és felügyelt alkalmazások.

* A **megoldási sablon** az egyik fő módszer a megoldás közzétételére a piactéren. Akkor használja ezt a díjcsomag-típust, ha a megoldás további üzembe helyezést és konfigurációs automatizálást igényel egy virtuális gépen (VM) túl. A megoldás sablonnal több erőforrást is automatizálhat, beleértve a virtuális gépeket, a hálózatkezelést és a tárolási erőforrásokat az összetett IaaS-megoldások biztosításához.  A megoldási sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

* A **felügyelt alkalmazás** a megoldási sablonokhoz hasonló, és egyetlen kulcsfontosságú különbség. A felügyelt alkalmazások esetében az erőforrások üzembe helyezése egy erőforráscsoportban történik, amelyet az alkalmazás közzétevője felügyel. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. A megoldás folyamatos támogatásának költségeit a közzétevő határozza meg. A felügyelt alkalmazások segítségével egyszerűen hozhat létre és biztosíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfelei számára.  További információ a felügyelt alkalmazások előnyeiről és típusairól: az [Azure által felügyelt alkalmazások áttekintése](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="technical-requirements"></a>Technikai követelmények

Az összes Azure-alkalmazás legalább két fájlt tartalmaz az Archívum gyökérkönyvtárában `.zip` :

* Egy [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)nevű Resource Manager-sablonfájl.  Ez a sablon határozza meg az ügyfél Azure-előfizetésében telepítendő erőforrásokat.  A Resource Manager-sablonokra vonatkozó példákért tekintse meg az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) katalógusát vagy a megfelelő [githubot: Azure Resource Manager Gyorsindítás sablonok](https://github.com/azure/azure-quickstart-templates) tárháza.

* A [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)nevű Azure-alkalmazás létrehozási élményének felhasználói felületi definíciója.  A felhasználói felületen elemeket ad meg, amelyek lehetővé teszik a felhasználók számára paraméterértékek megadását.

Minden új Azure-alkalmazás ajánlatának tartalmaznia kell egy [Azure-partner ügyfél-használati azonosítóját (GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)). 

Az egyes alkalmazáscsomag közzétételi követelményeinek megismeréséhez tekintse meg a [megoldási sablon ajánlat közzétételi követelményeit](../marketplace-solution-templates.md) és a [felügyelt alkalmazás-ajánlat közzétételi követelményeit](../marketplace-managed-apps.md)ismertető témakört.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

>[!NOTE]
>Az ajánlat közzétételét követően a partner Centerben lévő módosítások nem jelennek meg a kirakatokban, amíg újra nem teszi közzé az ajánlatot. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie az ajánlatot.

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).

1. A bal oldali menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.

1. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **Azure-alkalmazás**lehetőséget.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-azure-app.png)

1. Az **új ajánlat** lapon adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

     * Ez az azonosító látható az ügyfelek számára a Piactéri ajánlathoz tartozó webcímek és Azure Resource Manager sablonok esetében, ha van ilyen.
     * Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

1. Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

     * Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
     * Az ajánlat aliasa nem módosítható a **Létrehozás**gombra kattintva.

1. Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

Az **ajánlat beállítása** oldalon megadhatja az ajánlathoz tartozó tesztelési meghajtót és érdeklődői felügyeletet. 

### <a name="test-drive"></a>Tesztelési meghajtó

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a vásárlás előtt kipróbálhatja a "kipróbálás előtt" lehetőséget, ami növeli a konverziót és a magas minősítésű érdeklődők generációját. [További információ a tesztelési meghajtókról](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Ha egy tesztelési meghajtót egy meghatározott ideig szeretne engedélyezni, jelölje be a **Test Drive engedélyezése** jelölőnégyzetet. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből. A test Drive-környezet konfigurálása a [tesztelési meghajtó technikai konfigurációja](#test-drive-technical-configuration) szakaszban, a témakör későbbi részében.

További információ: az [ajánlat tesztelése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive). A [Test Drive ajánlott eljárásairól](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) is olvashat, és letöltheti a [tesztelési meghajtók áttekintése PDF-fájlt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (ellenőrizze, hogy ki van-e kapcsolva az előugró ablak blokkolása)

>[!Note]
>Mivel az összes Azure-alkalmazás egy Azure Resource Manager sablonnal lett implementálva, az Azure-alkalmazásokhoz elérhető egyetlen, [Azure Resource Manager-alapú tesztelési](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)meghajtó.

## <a name="lead-management"></a>Érdeklődők kezelése

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

További információ: az [ólom kezelése – áttekintés](./commercial-marketplace-get-customer-leads.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="properties"></a>Tulajdonságok

A **Tulajdonságok** lapon megadhatja, hogy az ajánlat a piactéren, az alkalmazás verziója és az ajánlatát támogató jogi szerződések alapján csoportosítsa az ajánlatot.

Válasszon ki legalább egy legfeljebb három kategóriát, hogy az ajánlatot a megfelelő Piactéri keresési területeken helyezze el. Ügyeljen arra, hogy az ajánlata milyen módon támogassa ezeket a kategóriákat az ajánlat leírásában.

### <a name="legal"></a>Jogi tudnivalók

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Ajánlati lista

Ezen a lapon kezelhetők a kereskedelmi Marketplace-ajánlathoz tartozó másolatok és lemezképek. 

### <a name="marketplace-details"></a>Piactér – részletek

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek és a használati feltételek) nem kötelező angol nyelven lennie, amennyiben az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." Azt is elfogadható, hogy egy *hasznos hivatkozási URL-címet* adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

#### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően. Ez a mező előre fel van töltve az ajánlat- **aliashoz** megadott szöveggel az ajánlat létrehozásakor, de ez az érték módosítható. Ez a név lehet védjeggyel ellátott (és előfordulhat, hogy védjegyeket vagy szerzői jogi szimbólumokat is tartalmaz). A név nem lehet hosszabb 50 karakternél, és nem tartalmazhat hangulatjelek.

#### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását, amely legfeljebb 100 karakter hosszú lehet. Ez a leírás a keresési eredményekben is használható.

#### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlat hosszabb leírását, amely legfeljebb 256 karakter hosszú lehet. Ez a leírás a keresési eredményekben is használható.

#### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Kulcsszavak keresése

Akár három keresési kulcsszót is megadhat, amelyek segítségével az ügyfelek megtalálják az ajánlatot a piactéren. A legjobb eredmény érdekében ezeket a kulcsszavakat is használhatja a leírásában.

#### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának URL-címét. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

### <a name="useful-links"></a>Hasznos hivatkozások

Adjon hozzá hivatkozásokat a megoldás választható kiegészítő online dokumentumaihoz a **+ hivatkozás hozzáadása**lehetőség kiválasztásával.

### <a name="contact-information"></a>Kapcsolattartási adatok

Adja meg a **támogatási kapcsolattartó**, a **mérnöki kapcsolattartó**és a **CSP-program kapcsolatának**nevét, e-mail-címét és telefonszámát. Ez az információ nem jelenik meg az ügyfelek számára, de a Microsoft számára elérhetővé válik, és a CSP-partnerek számára biztosítva lehet. Egyes névjegyek további információkat igényelhetnek.

### <a name="marketplace-media"></a>Piactéri adathordozó

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képek miatt a rendszer elutasítja a kérelmet.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner Center által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlat emblémájának PNG-fájlját a következő három pixeles méretben:

- **Kicsi** (48 x 48)
- **Közepes** (90 x 90)
- **Nagyméretű** (216 x 216)
- **Széles** (255 x 115)

Mindhárom emblémát meg kell adni, és a lista különböző helyein használják.

#### <a name="screenshots"></a>Képernyőképek

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie. Minden képernyőképnek tartalmaznia kell egy feliratot.

#### <a name="videos"></a>Videók

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. Ezeket külső videó szolgáltatásban kell tárolni. Adja meg a videó nevét, webcímeit és miniatűr PNG-képét 1280 x 720 képpontban.

#### <a name="additional-marketplace-listing-resources"></a>További erőforrások listázása a piactéren

- [Ajánlott eljárások a piactér ajánlati listáihoz](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="preview-audience"></a>Előnézet célközönsége

Az Előnézet lapon válasszon egy korlátozott **előzetes** verziót az ajánlat érvényesítéséhez, mielőtt közzéteszi azt a piactéren elérhető szélesebb közönség számára.

> [!IMPORTANT]
> Az ajánlat előzetes verzióban való ellenőrzése után válassza az **élő** adás lehetőséget az ajánlat közzétételéhez a kereskedelmi piactér nyilvános célközönsége számára.

Az előnézeti közönséget az Azure-előfizetés AZONOSÍTÓjának GUID azonosítói határozzák meg, és mindegyikhez opcionális leírást is megadhat. Ezen mezők egyikét sem láthatja az ügyfelek. Az Azure-előfizetési azonosítót a Azure Portal **előfizetések** oldalán találja.

Adjon hozzá legalább egy Azure-előfizetési azonosítót egyenként (legfeljebb 10) vagy egy CSV-fájl feltöltésével (legfeljebb 100). Az előfizetés-azonosítók hozzáadásával meghatározhatja, hogy ki láthatja el az ajánlatot az élő közzététel előtt. Ha az ajánlata már élő, akkor is megadhatja az előnézeti célközönséget az ajánlat változásainak vagy frissítéseinek teszteléséhez.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előnézeti közönség elérheti az ajánlatát, _mielőtt_ élőben közzétette a piactéren. Megtekinthetik és ellenőrizhetik az összes csomagot, beleértve azokat is, amelyek csak a privát közönség számára lesznek elérhetők, miután az ajánlata teljes mértékben közzé lett téve a piactéren. A saját célközönség (a csomag **díjszabása és rendelkezésre állása** lapon van meghatározva) kizárólagos hozzáféréssel rendelkezik egy adott csomaghoz.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration"></a>Technikai konfiguráció

Csak akkor hajtsa végre ezt a szakaszt, ha az ajánlat olyan felügyelt alkalmazást tartalmaz, amely mérési eseményeket bocsát ki a Marketplace mérési szolgáltatás API használatával. Adja meg a **Azure Active Directory bérlői azonosítót** és a **Azure Active Directory alkalmazás azonosítóját** , amelyet a szolgáltatás mérési események kibocsátásakor használ majd.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="technical-configuration-offer-level"></a>Technikai konfiguráció (ajánlati szint)

>[!Note]
>Az ajánlat szintű technikai részletek megadása nem kötelező.  Ezeket az adatokat csak akkor kell konfigurálnia, ha felügyelt alkalmazást tesz közzé mért számlázással, és rendelkezik olyan szolgáltatással, amely egy Azure AD biztonsági jogkivonattal lesz hitelesítve. További információ: [hitelesítési stratégiák](./marketplace-metering-service-authentication.md) a különböző hitelesítési beállításokban.

A technikai konfiguráció meghatározza a szolgáltatás azonosításához használt részleteket (bérlői azonosítót és az alkalmazás AZONOSÍTÓját), amely mérési eseményeket bocsát ki egy felügyelt alkalmazás számára a [Marketplace-mérési szolgáltatás API-jai](./marketplace-metering-service-apis.md)segítségével.  Adja meg azt az identitást, amelyet a szolgáltatás a mérési események kibocsátásakor használni fog.

* **Azure ad-bérlő azonosítója** (kötelező): Azure Portalon belül [létre kell hoznia egy Azure Active Directory (ad) alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) , hogy a két szolgáltatás közötti kapcsolat ellenőrizhető legyen egy hitelesített kommunikáció mögött. A [bérlő azonosítójának](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)megkereséséhez lépjen a Azure Active Directoryra, és válassza a **Tulajdonságok**lehetőséget, majd keresse meg a felsorolt **címtár-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure ad-alkalmazás azonosítója** (kötelező): az alkalmazás- [azonosítóra](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) és egy hitelesítési kulcsra is szüksége lesz. Az értékek beszerzéséhez lépjen a Azure Active Directory, és válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e). A hitelesítési kulcs megkereséséhez lépjen a **Beállítások** elemre, és válassza a **kulcsok**lehetőséget. Meg kell adnia egy leírást és egy időtartamot, és ezután meg kell adni egy számértéket.

>[!Note]
>Az Azure-alkalmazás azonosítója a közzétevő-AZONOSÍTÓhoz lesz társítva, és csak ebben a közzétevő fiókban lehet újra felhasználni.

>[!Note]
>Erre a konfigurációra akkor van szükség, ha a [Batch-használati eseményt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event)szeretné használni.  Ha a [használati eseményt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event)szeretné elküldeni, a [példány metaadatainak szolgáltatásával](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lekérheti a [JSON webes jogkivonat (JWT) tulajdonosi jogkivonatát](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app).

## <a name="plan-overview"></a>A terv áttekintése

Ezen a lapon megadhatja a különböző csomagokra vonatkozó lehetőségeket ugyanazon az ajánlaton belül. Ezek a csomagok (a Cloud Partner Portal SKU-ként) a csomag típusa (a megoldás sablonja vagy a felügyelt alkalmazás), a monetizálása vagy a célközönség szempontjából eltérőek lehetnek.  Állítson be legalább egy csomagot az ajánlat a piactéren való listázásához.

A létrehozást követően megtekintheti a csomag neveit, azonosítóit, a csomag típusát, a rendelkezésre állást (nyilvános vagy magánjellegű), az aktuális közzétételi állapotot és a lapon elérhető összes műveletet.

A **terv áttekintésében** elérhető **műveletek** a csomag aktuális állapotától függően változnak, és a következők lehetnek:

* Ha a terv állapota **Piszkozat** – delete Piszkozat.
* Ha a terv állapota **élő** – eladási terv leállítása vagy privát célközönség szinkronizálása.

### <a name="create-new-plan"></a>Új csomag létrehozása

***Csomag azonosítója*** – hozzon létre egy egyedi díjcsomag-azonosítót az ajánlat egyes terveihez. Ez az azonosító a termék URL-címében szereplő ügyfelek számára jelenik meg.  Csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon. Ehhez a csomag-AZONOSÍTÓhoz legfeljebb 50 karakter adható meg. Ez az azonosító nem módosítható a létrehozás kiválasztása után.

***Csomag neve*** – az ügyfelek ezt a nevet fogják látni, amikor dönti el, hogy melyik tervet kívánja kiválasztani az ajánlaton belül. Hozzon létre egyedi nevet az ajánlat minden csomagjának. A terv neve az adott ajánlat részét képező szoftvercsomagok megkülönböztetésére szolgál (például az ajánlat neve: Windows Server; csomagok: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Csomag beállítása

Ezen a lapon beállíthatja a csomag típusának magas szintű konfigurációját, hogy az újrahasznosítsa-e a csomagokat egy másik csomagból, és milyen felhők legyenek elérhetők a tervben. Az ezen a lapon megjelenő válaszok hatással lesznek arra, hogy mely mezők jelenjenek meg ugyanazon csomag más lapjain.

#### <a name="plan-type"></a>Csomag típusa
Válassza ki az ajánlathoz tartozó csomag típusát. A **megoldás sablonjait** teljes mértékben az ügyfél kezeli. A **felügyelt alkalmazási** csomag lehetővé teszi, hogy a kiadók az alkalmazást az ügyfél nevében kezeljék. Részletekért lásd: [Az Azure-alkalmazási csomagok típusai](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Technikai konfiguráció újbóli használata

Ha több azonos típusú csomaggal rendelkezik, és a csomagok azonosak egymás között, akkor kiválaszthatja, hogy a **terv újrahasznosítsa a csomagokat egy másik**csomagból.  Ha ezt a beállítást választja, az ajánlathoz tartozó más csomagok közül választhatja ki a csomagokat. 

>[!Note]
>Ha egy másik csomagból újrahasznál csomagokat, a teljes technikai konfiguráció lap eltűnik ebből a csomagból. A csomagra vonatkozó technikai konfiguráció részleteit, beleértve a jövőbeli frissítéseket is, a tervhez is használni fogjuk.<br><br>Ez a beállítás a terv közzététele után nem módosítható.

#### <a name="azure-regions-cloud"></a>Azure-régiók (felhő)

A tervet legalább egy Azure-régióban elérhetővé kell tenni.

Válassza ki az **Azure globális** lehetőséget, hogy a tervet elérhetővé tegye az összes olyan globális Azure-régióban, amely kereskedelmi Piactéri integrációt tartalmaz. Részletekért lásd: a [földrajzi Elérhetőség és a pénznemek támogatása](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Válassza a **Azure Government** lehetőséget, hogy a terv elérhető legyen a [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) régióban. Ez a régió szabályozott hozzáférést biztosít az Egyesült Államok szövetségi, állami, helyi vagy törzsi entitásai ügyfeleinek, valamint az azok kiszolgálására jogosult partnereinknek. Ön, mint közzétevő, felelős a megfelelőségi ellenőrzésért, a biztonsági intézkedésekért és az ajánlott eljárásokhoz. A Azure Government fizikailag elkülönített adatközpontokat és hálózatokat használ (csak az Egyesült Államokban található).

A [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)való közzététel előtt tesztelje és érvényesítse a tervet a környezetben, mivel egyes végpontok eltérőek lehetnek. A csomag beállításához és teszteléséhez [Microsoft Azure Government próbaverzióból](https://azure.microsoft.com/global-infrastructure/government/request/)kérjen próbaverziós fiókot.

>[!NOTE]
>Miután közzétette és elérhetővé tette a csomagot egy adott Azure-régióban, nem távolíthatja el a régiót.

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

### <a name="availability-solution-template-plans-only"></a>Rendelkezésre állás (csak megoldási sablonra vonatkozó csomagok)

A tervet mindenki számára elérhetővé teheti, csak bizonyos ügyfelek (privát célközönségek) számára, valamint azt, hogy a tervet más megoldási sablon vagy csak felügyelt alkalmazások általi használatra szeretné-e ellátni.

#### <a name="plan-visibility"></a>Terv láthatósága

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Ehhez a korlátozott célközönséghez az Azure-előfizetési azonosítók használatával lehet tagságot rendelni.

Válassza **ezt a csomagot** , hogy a terv magánjellegű legyen, és csak az Ön által választott korlátozott közönség számára legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatóvá kell maradnia. nem konfigurálható újra privát csomagként.

Ha a csomag magánjellegű, adjon meg egy **Azure-előfizetési azonosítót** és annak leírását. Minden olyan célközönség, amely hozzáfér ehhez a privát csomaghoz. A hozzáférés az Azure-előfizetési azonosítók használatával van hozzárendelve, hogy tartalmazza az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását. Egy CSV-fájl importálásával akár 10 ügyfél-előfizetési azonosítót is hozzáadhat egyenként vagy 20 000. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisbetűsnek kell lenniük.

>[!Note]
>A privát vagy a korlátozott célközönség nem azonos az **Előnézet lapon megadott** előnézeti célközönséggel. Az előnézeti közönség hozzáférhet az ajánlathoz a piactéren közzétett élő állapot _előtt_ . Míg a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

#### <a name="hide-plan"></a>Terv elrejtése

Ha a megoldás sablonja kizárólag központilag telepíthető, ha egy másik megoldási sablon vagy egy felügyelt alkalmazás hivatkozik rá, jelölje be ezt a jelölőnégyzetet a megoldás sablonjának közzétételéhez, de az ügyfelek általi keresés és Tallózás közvetlenül a felhasználók elől való elrejtéséhez.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Díjszabás és rendelkezésre állás (csak felügyelt alkalmazási csomagok)

Ezzel a csomaggal konfigurálhatja **a csomagot** , a megoldás felügyeletének havi **díjszabását** , valamint a **terv láthatóságát** , ha csak bizonyos ügyfelek látják (privát célközönség).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

#### <a name="markets"></a>Piacok

Minden csomagnak legalább egy piacon elérhetőnek kell lennie. Jelölje be annak a piaci helynek a jelölőnégyzetét, amely számára elérhetővé szeretné tenni ezt a csomagot. Egy keresőmező és egy gomb a "Tax átutalt" országok/régiók kiválasztásához, amelyekben a Microsoft az Ön nevében az értékesítést és a használati adót használja fel, a segítségét is tartalmazza.

Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A közzététel előtt mindig tekintse át az egyes piacok árát. A díjszabást a módosítások mentése után a "export prics (xlsx)" hivatkozással tekintheti át.

#### <a name="pricing"></a>Díjszabás

Adja meg a csomag havi árát.  Ez az ár a megoldás által központilag üzembe helyezett erőforrások esetében felmerülő bármely Azure-infrastruktúra vagy utólagos elszámolású szoftver költségein felül van.

A havi díj mellett a nem standard egységek fogyasztásának díjszabását is megadhatja [mért számlázás](./azure-app-metered-billing.md)használatával.  Ha kívánja, a havonta megadott árat nulla értékre állíthatja, és kizárólag a mért számlázást kell fizetnie. 

Az USD-ben beállított árak (USD = Egyesült Államok dollár) az összes kiválasztott piac helyi pénznemére lesznek átalakítva, az aktuális árfolyamok alapján, a mentéskor. A közzététel előtt érvényesítse ezeket az árakat a díjszabási táblázat exportálásával, és tekintse át az egyes piacokon érvényes díjakat. Ha egyéni árakat szeretne beállítani egyedi piacon, módosítsa és importálja a díjszabási táblázatot. 

>[!Note]
>Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását.

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások miatt a csomag közzététele után is változhatnak.  

>[!Note]
>Miután közzétette a csomag egy piacának árát, később nem módosítható.

#### <a name="plan-visibility"></a>Terv láthatósága

Beállíthatja, hogy az egyes tervek mindenki számára láthatók legyenek, vagy csak egy adott célközönségnek. Ehhez a korlátozott célközönséghez az Azure-előfizetési azonosítók használatával lehet tagságot rendelni.

Válassza **ezt a csomagot** , hogy a terv magánjellegű legyen, és csak az Ön által választott korlátozott közönség számára legyen látható. Miután közzétette saját csomagként, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatóvá kell maradnia. nem konfigurálható újra privát csomagként.

Ha a csomag magánjellegű, adjon meg egy **Azure-előfizetési azonosítót** és annak leírását. Minden olyan célközönség, amely hozzáfér ehhez a privát csomaghoz. A hozzáférés az Azure-előfizetési azonosítók használatával van hozzárendelve, hogy tartalmazza az egyes hozzárendelt Azure-előfizetési AZONOSÍTÓk leírását. Egy CSV-fájl importálásával akár 10 ügyfél-előfizetési azonosítót is hozzáadhat egyenként vagy 20 000. Az Azure-előfizetési azonosítók GUID azonosítóként jelennek meg, és a betűknek kisbetűsnek kell lenniük.

>[!Note]
>A privát vagy a korlátozott célközönség nem azonos az **Előnézet lapon megadott** előnézeti célközönséggel. Az előnézeti közönség hozzáférhet az ajánlathoz a piactéren közzétett élő állapot _előtt_ . Míg a privát célközönség választása csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége az összes csomagot (magán vagy nem) megtekintheti érvényesítési célból.

### <a name="technical-configuration"></a>Technikai konfiguráció 

Ezen a lapon töltheti fel a központi telepítési csomagot, amely lehetővé teszi az ügyfelek számára a terv üzembe helyezését.

>[!Note]
>Ez a lap nem jelenik meg, ha úgy konfigurálta ezt a csomagot, hogy a terv **beállítása** lapon újra használhassa a csomagokat.

#### <a name="package-details"></a>Csomag részletei

Ezen a lapon szerkesztheti a technikai konfiguráció vázlatos verzióját.

**Verzió** – a technikai konfiguráció aktuális verziójának kiosztása.  Minden alkalommal növelje ezt a verziót, amikor módosításokat tesz közzé ezen az oldalon. A verziónak formátumúnak kell lennie `{integer}.{integer}.{integer}` .

**Csomagfájl** (. zip) – Ez a csomag tartalmazza az ehhez a csomaghoz szükséges összes sablonfájlt, valamint a további, fájlként csomagolt erőforrásokat `.zip` .

Az összes Azure alkalmazáscsomag-csomagnak tartalmaznia kell ezt a két fájlt az Archívum gyökérkönyvtárában `.zip` :

* Egy [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)nevű Resource Manager-sablonfájl. Ez a sablon automatizálja az erőforrások üzembe helyezését az ügyfeleknek az Azure-előfizetésben.  A Resource Manager-sablonokra vonatkozó példákért tekintse meg az [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) katalógusát vagy a megfelelő [githubot: Azure Resource Manager Gyorsindítás sablonok](https://github.com/azure/azure-quickstart-templates) tárháza.
* A [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)nevű Azure-alkalmazás létrehozási élményének felhasználói felületi definíciója.

A maximálisan támogatott fájlméretek a következők:

* Legfeljebb 1 GB a tömörített `.zip` archiválási méret összesen
* Legfeljebb 1 GB az archívumban található összes egyes tömörítetlen fájlhoz `.zip`  

Az Azure-alkalmazások minden új ajánlatának tartalmaznia kell egy [Azure-partner ügyfél-használati jóváírási](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID azonosítót is.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

A felügyelt alkalmazások csomagjai további információkat igényelnek ezen a lapon.

#### <a name="previously-published-packages"></a>Korábban közzétett csomagok

A **korábban közzétett csomagok** almenü lehetővé teszi, hogy megtekintse a technikai konfiguráció összes közzétett verzióját.

#### <a name="enable-just-in-time-jit-access"></a>Igény szerinti (JIT) hozzáférés engedélyezése

Ezzel a beállítással engedélyezheti az igény szerinti (JIT) hozzáférést ehhez a csomaghoz.  A JIT-hozzáférés lehetővé teszi, hogy a hibaelhárítás és a karbantartás érdekében emelt szintű hozzáférést kérjen egy felügyelt alkalmazás erőforrásaihoz. Mindig csak olvasási hozzáférése van az erőforrásokhoz, de egy adott időszakra vonatkozóan nagyobb hozzáférés érhető el.  További információ: a [Azure Managed Applications igény szerinti hozzáférésének engedélyezése és kérése](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Ha szeretné megkövetelni, hogy a felügyelt alkalmazás felhasználói a fiókjának állandó hozzáférését adják meg, hagyja meg ezt a beállítást, ha nincs bejelölve.

>[!Note]
>Ügyeljen rá, hogy a `createUiDefinition.json` szolgáltatás támogatásához frissítse a fájlt.  

#### <a name="deployment-mode"></a>Üzembe helyezési mód

Válassza ki, hogy a csomag telepítésekor a **teljes** vagy **növekményes telepítési módot** kívánja-e konfigurálni: 

* **Teljes módban**az ügyfél által az alkalmazás újratelepítése a felügyelt erőforráscsoport erőforrásainak eltávolítását eredményezi, ha az erőforrások nincsenek meghatározva a alkalmazásban `mainTemplate.json` . 
* A **növekményes módban**az alkalmazás újratelepítése változatlanul hagyja a meglévő erőforrásokat.

További információ az üzembe helyezési módokról: [Azure Resource Manager telepítési módok](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

#### <a name="notification-endpoint-url"></a>Értesítési végpont URL-címe

Adjon meg egy HTTPS-webhook-végpontot, hogy értesítést kapjon a csomag felügyelt alkalmazás példányain lévő összes szifilisz-műveletről.

#### <a name="customize-allowed-customer-actions"></a>Engedélyezett felhasználói műveletek testreszabása

Ezzel a beállítással adhatja meg, hogy az ügyfelek milyen műveleteket hajthatnak végre a felügyelt erőforrásokon a " `*/read` " alapértelmezés szerint elérhető műveletek mellett. 

Sorolja fel azokat a további műveleteket, amelyeknek lehetővé szeretné tenni az ügyfél számára, hogy itt hajtsa végre ezt a műveletet pontosvesszővel elválasztva.  További információ: [Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Az elérhető műveletekért lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Ha például engedélyezni szeretné a felhasználóknak a virtuális gépek újraindítását, adja hozzá `Microsoft.Compute/virtualMachines/restart/action` az engedélyezett műveleteket.

#### <a name="global-azure--azure-government-cloud"></a>Globális Azure/Azure Government felhő

Adja meg, hogy ki kell-e kezelni a felügyelt alkalmazáshoz tartozó felügyeleti hozzáférést minden támogatott felhőben. Azokat a felhasználókat, csoportokat vagy alkalmazásokat, amelyeket a felügyelt erőforráscsoport számára engedélyezni kíván, Azure Active Directory (HRE) identitások használatával azonosíthatók.

**Azure Active Directory bérlő azonosítója** – a HRE bérlői azonosító (más néven CÍMTÁR-azonosító), amely tartalmazza azon felhasználók, csoportok vagy alkalmazások identitását, amelyekhez engedélyeket szeretne adni. Az HRE-bérlő AZONOSÍTÓját a Azure Portal [Azure Active Directory tulajdonságainál](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)találja.

**Engedélyek** – adja meg annak a felhasználónak, csoportnak vagy alkalmazásnak az Azure Active Directory-objektumazonosítóát, amelyhez engedélyeket szeretne adni a felügyelt erőforráscsoport számára. Azonosítsa a felhasználót a résztvevő azonosítója alapján, amely a [Azure Portal Azure Active Directory felhasználók](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)paneljén található.

Minden egyes rendszerbiztonsági tag esetében válassza ki az egyik Azure AD beépített szerepkört a listából (tulajdonos vagy közreműködő). A kiválasztott szerepkör leírja, hogy a rendszerbiztonsági tag milyen engedélyeket fog tartalmazni az ügyfél-előfizetés erőforrásaiban. További információkért lásd az [Azure-erőforrások beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). A szerepköralapú hozzáférés-vezérléssel (RBAC) kapcsolatos további információkért lásd: Ismerkedés a [RBAC szolgáltatással a Azure Portalban](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Bár a felhőben legfeljebb 100 engedélyezést adhat hozzá, általában könnyebb létrehozni egy Active Directory felhasználói csoportot, és megadhatja annak AZONOSÍTÓját a "résztvevő azonosítója" értékkel. Ez lehetővé teszi, hogy a csomag telepítése után további felhasználókat vegyen fel a felügyeleti csoportba, és csökkentse a terv frissítésének szükségességét, hogy további engedélyeket adjon hozzá.

#### <a name="policy-settings"></a>Szabályzatbeállítások

Alkalmazza az [Azure-szabályzatokat](https://docs.microsoft.com/azure/governance/policy/overview) a felügyelt alkalmazásra a telepített megoldás megfelelőségi követelményeinek meghatározásához. A szabályzatdefiníciókról és a paraméterértékek formátumáról tekintse meg a következő dokumentumot: [Azure Policy-minták](https://docs.microsoft.com/azure/governance/policy/samples/index). Legfeljebb öt házirendet konfigurálhat, és az egyes házirendek közül csak egy példányt lehet beállítani. Egyes házirendek további paramétereket igényelnek. A naplózási házirendekhez a szabványos SKU szükséges. A szabályzat neve legfeljebb 50 karakter hosszú lehet.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="co-sell-with-microsoft"></a>Együttes értékesítés a Microsofttal

A közös értékesítés lapon található információk megadása teljesen opcionális az ajánlat közzétételéhez. A közös értékesítésre kész és az IP közös értékesítésre kész állapotot kell elérni. Az Ön által megadott információkat a Microsoft értékesítési csapatai fogják használni, hogy a megoldásra vonatkozó további információkat kapjanak az ügyfelek igényeinek megfelelő kiértékelése során. Nem érhető el közvetlenül az ügyfeleknek.

Ezen a lapon további részleteket a következő témakörben talál: [közös értékesítés lehetőség a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="resell-through-csps"></a>Viszonteladás a CSP-n keresztül

A [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) program partnerei számára elérhetővé teszi az ajánlat elérhetőségét. Ez lehetővé teszi, hogy a viszonteladók eladják az ajánlatot az ügyfelek számára, és kötegelt megoldásokat hozzanak létre.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="test-drive"></a>Tesztelési meghajtó

Állítson be egy bemutatót (tesztvezetés), amely lehetővé teszi az ügyfeleknek az ajánlat megvásárlása előtt történő kipróbálását. Ha olyan bemutató környezetet szeretne létrehozni, amely lehetővé teszi, hogy az ügyfelek meghatározott időn keresztül próbálják meg az ajánlatot, tekintse meg [az ajánlat tesztelése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)című témakört.

A tesztelési meghajtó engedélyezéséhez jelölje be a **Test Drive engedélyezése** jelölőnégyzetet az [ajánlat telepítése](#test-drive) lapon. Ha el szeretné távolítani a tesztelési meghajtót az ajánlatból, törölje a jelet a jelölőnégyzetből.

### <a name="test-drive-technical-configuration"></a>Tesztelési meghajtó technikai konfigurációja

- **Azure ad** -alkalmazás azonosítója (kötelező): adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Annak engedélyezéséhez, hogy a tesztvezetés az Ön nevében legyen üzembe helyezhető, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést (Power BI tesztelési meghajtókhoz nem szükséges).

* **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges) – adja meg az előfizetés azonosítóját, hogy hozzáférést biztosítson az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](https://docs.microsoft.com/azure/billing/billing-create-subscription) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztása esetén megjelenik az előfizetési azonosító (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **Azure ad-bérlő azonosítója** (kötelező) – adja meg a Azure Active Directory (ad) [bérlői azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok**elemet, majd keresse **meg a listában** szereplő 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címével is megkeresheti a következő helyen: [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges) – adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.
* **Azure ad** -alkalmazás azonosítója (kötelező) – adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), majd a bal oldali navigációs menüben válassza a Active Directory fület, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory alkalmazás-ügyfél titka** (kötelező) – adja meg az Azure ad-alkalmazás [ügyfelének titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali navigációs menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. A lap elhagyása előtt készítsen másolatot az értékről.)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="marketplace-listing-optional"></a>Marketplace-lista (nem kötelező)

A test Drive-élmény leírása.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Leírás** (kötelező) – írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célkitűzéseket, a felderített funkciókat, valamint minden olyan releváns információt, amely segít a felhasználónak eldönteni, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 
* **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges) – megtudhatja, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.
* **Felhasználói kézikönyv** (kötelező) – részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).
* **Videók: videók hozzáadása** (nem kötelező) – a videók a YouTube vagy a Vimeo webhelyre tölthetők fel, és a hivatkozás és a miniatűr képét (533 x 324 képpont) is feltölthetik, így az ügyfél megtekintheti az információk áttekintését, így könnyebben megismerheti a tesztelési meghajtót, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyveket.
  * **Név** (kötelező)
  * **Címe** (csak YouTube vagy Vimeo esetén szükséges)
  * **Miniatűr** (a képfájlnak PNG formátumban kell lennie, és 533 x 324 px).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="publish"></a>Közzététel

Ha befejezte az ajánlat összes szükséges szakaszt, válassza a **felülvizsgálat és közzététel** elemet a portál jobb felső sarkában.

Tekintse át az ajánlat egyes szakaszainak befejezési állapotát.
    - *Nincs elindítva* – azt jelenti, hogy a szakasz nem lett megérintve, és el kell végezni.
    - *Hiányos* – azt jelenti, hogy a szakasznak meg kell oldania a hibákat, vagy további információkat kell megadni. Térjen vissza a szakasz (ok) hoz, és frissítse azt.
    - *Complete (Befejezés* ) – azt jelenti, hogy a szakasz elkészült, minden szükséges adattal rendelkezik, és nincsenek hibák. Az ajánlat minden részének teljes állapotban kell lennie ahhoz, hogy el tudja küldeni az ajánlatot.

Ha első alkalommal teszi közzé ezt az ajánlatot, megadhatja a minősítési csapat tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve, valamint az alkalmazások megértéséhez szükséges kiegészítő megjegyzések mellett.

Válassza a **Submit (Küldés** ) lehetőséget az ajánlat közzétételre való elküldéséhez. Küldünk Önnek egy e-mailt, amelyből megtudhatja, hogy az ajánlat előzetes verziója elérhető-e a felülvizsgálathoz és jóváhagyáshoz.

Térjen vissza a partneri központba, és válassza a **Go-Live** lehetőséget az ajánlat nyilvános közzétételére (vagy ha egy privát ajánlatra a privát közönség számára).

### <a name="errors-and-review-feedback"></a>Hibák és visszajelzések áttekintése

A közzétételi folyamat **manuális érvényesítési** lépése az ajánlat és a hozzá kapcsolódó technikai eszközök (különösen a Azure Resource Manager sablon) átfogó áttekintését jelenti, a problémák általában lekéréses kérelmek (PR)-hivatkozásként jelennek meg. A következő témakörből megtudhatja, hogyan tekintheti meg és válaszolhatja meg ezeket a jogcímeket: [felülvizsgálati visszajelzések feldolgozása](./azure-apps-review-feedback.md).

Ha a közzétételi lépések közül egy vagy több hibát tartalmaz, javítsa ki azokat az ajánlat ismételt közzététele előtt.

## <a name="next-steps"></a>További lépések

* [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
