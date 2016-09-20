<properties
   pageTitle="Az Azure Resource Manager áttekintése | Microsoft Azure"
   description="Ismerteti, hogyan használja az Azure Resource Manager eszközt erőforrások telepítésére, felügyeletére és hozzáférés-vezérlésére az Azure portálon."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="tomfitz"/>

# Az Azure Resource Manager áttekintése

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. Az Azure Resource Manager lehetővé teszi, hogy a megoldásában az erőforrásokkal egy csoportként dolgozzon. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablont használ, amely különböző, például tesztelési, átmeneti és üzemi környezetekben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően. 

## Terminológia

Ha új felhasználója az Azure Resource Managernek, találkozhat néhány olyan kifejezéssel, amelyet még nem ismer.

- **erőforrás** – Egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Általános erőforrás például a következő: virtuális gép, tárfiók, webalkalmazás, adatbázis, virtuális hálózat, de számos további fajtája is létezik.
- **erőforráscsoport** – Egy olyan tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja az alkalmazás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket egy csoportba gyűjtöttek. Eldöntheti, hogyan kívánja kiosztani az erőforrásokat az erőforráscsoportokba a szervezetének legmegfelelőbb módon. Lásd: [erőforráscsoportok](#resource-groups).
- Az **erőforrás-szolgáltató** egy olyan szolgáltatás, amely a Resource Manager eszközön keresztül telepíthető és felügyelhető erőforrásokat biztosítja. Mindegyik erőforrás-szolgáltató műveleteket biztosít a telepített erőforrásokkal folytatott munkához. Gyakori erőforrás-szolgáltató például a virtuális gép típusú erőforrást támogató Microsoft.Compute, a tárfiók típusú erőforrást támogató Microsoft.Storage és a webalkalmazásokhoz kapcsolódó erőforrásokat támogató Microsoft.Web. Lásd: [erőforrás-szolgáltatók](#resource-providers).
- **Resource Manager-sablon** – Egy JavaScript Object Notation- (JSON-) fájl, amely egy vagy több, az erőforráscsoportra telepítendő erőforrást határoz meg. A telepített erőforrások közti függőségeket is meghatározza. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó. Lásd: [sablonalapú telepítés](#template-deployment).
- **deklaratív szintaxis** – Egy olyan szintaxis, amellyel anélkül fejtheti ki az alkotói szándékot, hogy programozási parancsok sorozatát kellene megírnia ehhez. A Resource Manager-sablon a deklaratív szintaxis egy példája. A fájlban meghatározhatja az Azure-ra telepíteni kívánt infrastruktúra tulajdonságait. 

## A Resource Manager használatának előnyei

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.
- A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.
- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.
- Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.
- Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.
- Címkékkel láthatja el az erőforrásokat, hogy logikusan rendszerezhesse az összes erőforrást az előfizetésében.
- Tisztázhatja a szervezete számlázását a teljes csoport vagy az azonos címkével rendelkező erőforrások csoportjának összegzett költségeinek megtekintésével.  

A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modellt használta, és további információkat kíván megtudni a változásokról, tekintse meg [A Resource Manager telepítés és a hagyományos telepítés ismertetése](resource-manager-deployment-model.md) című cikket.

## Útmutatás

A következő javaslatok segítségével teljes mértékben kihasználhatja a Resource Manager előnyeit a megoldásaival való munka során.

1. Az infrastruktúrát a Resource Manager-sablonok deklaratív szintaxisán keresztül határozhatja meg és telepítheti imperatív parancsok helyett.
2. Meghatározhatja az összes telepítési és konfigurációs lépést a sablonban. Nem szükséges manuális lépéseket megadnia a megoldás beállításához.
3. Imperatív parancsok futtatásával kezelheti az erőforrásokat, például elindíthat vagy leállíthat egy alkalmazást vagy gépet.
4. Az azonos életciklussal rendelkező erőforrásokat egy erőforráscsoportba rendezheti. Címkék segítségével tetszés szerint rendezheti az erőforrásokat.

További javaslatok: [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md) (Az Azure Resource Manager-sablonok létrehozásának ajánlott eljárásai).

## Erőforráscsoportok

Néhány fontos tényezőt érdemes figyelembe venni az erőforráscsoport meghatározásakor:

1. A csoportban lévő összes erőforrásnak azonos életciklussal kell rendelkeznie. Egyszerre fogja őket telepíteni, frissíteni és törölni. Ha egy erőforrásnak, például egy adatbázis-kiszolgálónak különböző fejlesztési ciklusban kell léteznie, azt másik erőforráscsoportba kell elhelyezni.
2. Az egyes erőforrások csak egy erőforráscsoportban létezhetnek.
3. Az erőforráscsoporthoz bármikor hozzáadhat, vagy onnan eltávolíthat egy erőforrást.
4. Az erőforrásokat áthelyezheti az egyik erőforráscsoportból a másikba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
4. Az erőforráscsoportok tartalmazhatnak olyan erőforrásokat, amelyek különböző régiókban találhatók.
5. Az erőforráscsoport segítségével meghatározhatja a hozzáférés-vezérlési hatókört felügyeleti műveletekhez.
6. Egy erőforrás akkor léphet interakcióba egy másik erőforráscsoportban található erőforrással, amikor a két erőforrás kapcsolódik, de nem ugyanaz az életciklusuk (például amikor egy webalkalmazás csatlakozik egy adatbázishoz).

## Erőforrás-szolgáltatók

Mindegyik erőforrás-szolgáltató erőforrás-készleteket és műveleteket biztosít a technikai területen folytatott munkához. Ha például kulcsokat és titkos kulcsokat kíván tárolni, a **Microsoft.KeyVault** erőforrás-szolgáltatót fogja használni. Ez az erőforrás-szolgáltató egy **vaults** nevű erőforrástípust biztosít a kulcstároló létrehozásához, illetve egy **vaults/secrets** nevű erőforrástípust a titkos kulcs létrehozásához a kulcstárolóban. Műveleteket is elérhetővé tesz a [Key Vault Rest API-műveleteken](https://msdn.microsoft.com/library/azure/dn903609.aspx) keresztül. Hívhatja közvetlenül a REST API-t, vagy használhat [Key Vault PowerShell-parancsmagokat](https://msdn.microsoft.com/library/dn868052.aspx) és a [Key Vault Azure parancssori felületét](./key-vault/key-vault-manage-with-cli.md) a kulcstartó kezelésére. Számos programozási nyelvet használhat az erőforrásokkal folytatott munkához. További információ: [SDK-k és példák](#sdks-and-samples). 

Az infrastruktúra telepítéséhez és kezeléséhez ismernie kell az erőforrás-szolgáltató adatait; például milyen erőforrástípusokat kínál, a REST API-műveletek verziószámait, a támogatott műveleteket, illetve a létrehozandó erőforrástípusok értékeinek megadásakor használandó sémát. Információk a támogatott erőforrás-szolgáltatókról: [Resource Manager szolgáltatók, régiók, API verziók és sémák](resource-manager-supported-services.md).

## Sablonalapú telepítés

A Resource Manager segítségével létrehozhat egyszerű sablonokat (JSON formátumban), amelyek meghatározzak az alkalmazás telepítését és konfigurációját. A sablonok segítségével az alkalmazást ismételten telepítheti az alkalmazás életciklusa során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz. Az Azure Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](resource-group-define-dependencies.md).

Amikor létrehoz egy megoldást a portálról, az automatikusan tartalmaz egy telepítési sablont. Nem szükséges teljesen új sablont létrehoznia, mivel kezdetben használhatja a sablont a megoldásához, majd testreszabhatja az adott igényeknek megfelelően. Egy meglévő erőforráscsoport sablonjának lekéréséhez exportálja az erőforráscsoport aktuális állapotát egy sablonba, vagy tekintse meg a sablont, amelyet az adott telepítéshez felhasználtak. Az exportált sablon megtekintése hasznos információkat nyújt a sablon szintaxisáról. Az exportált sablonok használatával kapcsolatos további információk: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).

Nem kell meghatároznia a teljes infrastruktúrát egyetlen sablonban. Gyakran érdemes felosztani a telepítési követelményeket konkrét, célspecifikus sablonokra. Ezeket a sablonokat egyszerűen újból felhasználhatja különböző megoldásokhoz. Egy adott megoldás telepítéséhez hozzon létre egy fő sablont, amely összekapcsolja az összes szükséges sablont. További információ: [Kapcsolt sablonok használata az Azure Resource Manager eszközben](resource-group-linked-templates.md).

A sablonokat az infrastruktúra frissítésére is használhatja. Például hozzáadhat egy új erőforrást az alkalmazáshoz, és hozzáadhat konfigurációs szabályokat a már telepített erőforrásokhoz. Ha a sablon egy új erőforrás létrehozását határozza meg, de az erőforrás már létezik, az Azure Resource Manager az új eszköz létrehozása helyett frissítést végez. Az Azure Resource Manager frissíti a meglévő eszközt, hogy az állapota olyan legyen, mintha új lenne. Vagy megadhatja, hogy a Resource Manager törölje az összes olyan erőforrást, amely nincs meghatározva a sablonban. A telepítéskor elérhető lehetőségek közötti különbségek ismertetése: [Alkalmazás telepítése Azure Resource Manager sablonnal](resource-group-template-deploy.md). 

Meghatározhat paramétereket a sablonban, hogy a telepítés testreszabható és rugalmas legyen. Például megadhat olyan paraméterértékeket, amelyek a telepítést a tesztkörnyezetre igazítják. A paraméterek meghatározásával ugyanazt a sablont használhatja a telepítéshez az alkalmazás teljes környezetében.

A Resource Manager bővítményeket biztosít olyan forgatókönyvek esetére, amikor további műveletek szükségesek, például egy adott szoftver telepítése, amelyet nem tartalmaz a beállítás. Ha már használ valamilyen konfigurációfelügyeleti szolgáltatást, mint a DSC, Chef vagy Puppet, bővítmények segítségével folytathatja a munkát az adott szolgáltatással.

Végül a sablon az alkalmazás forráskódjának részévé válik. Elhelyezheti a forráskódraktárban, és frissítheti az alkalmazás továbbfejlesztésekor. A sablont a Visual Studio eszközben szerkesztheti.

További információ a sablonok meghatározásáról: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md).

A sablonok létrehozásának részletes ismertetése: [Útmutató Resource Manager sablonokhoz](resource-manager-template-walkthrough.md).

Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](solution-dev-test-environments.md). 

## Címkék

A Resource Manager biztosít egy címkézési funkciót, amellyel a felügyeleti vagy számlázási követelményeinek megfelelően kategorizálhatja az erőforrásokat. Érdemes címkéket használni, ha összetett erőforráscsoport- és erőforrás-gyűjteménnyel rendelkezik, és egyedi módon kívánja vizuálisan megjeleníteni az eszközöket. Például elláthat címkével olyan erőforrásokat, amelyek hasonló szerepet töltenek be a szervezetben, vagy ugyanahhoz a részleghez tartoznak. Címkék nélkül a felhasználók a szervezetben létrehozhatnak több olyan erőforrást is, amelyeket később nehéz lehet azonosítani és felügyelni. Előfordulhat például, hogy törölni szeretné egy adott projekt összes erőforrását, de azok az erőforrások nem rendelkeznek a projekt címkéjével, ezért manuálisan kell megkeresnie őket. A címkézés hasznos módja a felesleges költségek csökkentéséhez az előfizetésében. 

Az erőforrásoknak nem kell megegyező erőforráscsoportban lenniük, hogy azonos címkével lássa el őket. Létrehozhat saját címkerendszert, hogy a szervezetben lévő összes felhasználó ugyanolyan címkéket használjon, hogy a felhasználók ne használjanak véletlenül némileg eltérő címkéket (például „részleg” helyett „részl”).

Címkékkel kapcsolatos további információ: [Címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md). Létrehozhat egy [testreszabott házirendet](#manage-resources-with-customized-policies), amely követelményként határozza meg a címkék hozzáadását az erőforrásokhoz a telepítés során.

## Hozzáférés-vezérlés

A Resource Manager segítségével szabályozhatja, hogy ki rendelkezzen hozzáféréssel egy adott művelethez a szervezetben. Az OAuth funkciót és a szerepköralapú hozzáférés-vezérlést (RBAC) natív módon integrálja a felügyeleti platformba, valamint a hozzáférés-vezérlést az összes szolgáltatásra alkalmazza az erőforráscsoportban. Hozzáadhat felhasználókat előre meghatározott platformokhoz és erőforrás-specifikus szerepkörökhöz, és alkalmazhatja ezeket a szerepköröket egy előfizetésre, erőforráscsoportra vagy erőforrásra a hozzáférés korlátozásához. Például az SQL DB Contributor nevű előre meghatározott szerepkör felhasználásával engedélyezheti a felhasználóknak az adatbázisok felügyeletét, de megtilthatja az adatbázis-kiszolgálók vagy biztonsági házirendek kezelését. Adja hozzá azokat a felhasználókat a szervezetben az SQL DB Contributor szerepkörhöz, akiknek ilyen típusú hozzáférésre van szükségük, és alkalmazza a szerepkört az előfizetésre, az erőforráscsoportra vagy erőforrásra.

A Resource Manager automatikusan naplózza a felhasználói műveleteket ellenőrzés céljából. További információ a vizsgálati naplók használatáról: [Naplózási műveletek a Resource Manager eszközzel](resource-group-audit.md).

A szerepköralapú hozzáférés-vezérléssel kapcsolatos további információk: [Azure szerepköralapú hozzáférés-vezérlés](./active-directory/role-based-access-control-configure.md). Az [RBAC: Beépített szerepkörök](./active-directory/role-based-access-built-in-roles.md) témakörben találja a beépített szerepkörök és az engedélyezett műveletek listáját. A beépített szerepkörök közé tartoznak általános szerepkörök, például Tulajdonos, Olvasó, Közreműködő; illetve további szolgáltatásspecifikus szerepkörök is, mint a Virtuális gép közreműködő, Virtuális hálózat közreműködő és SQL biztonságkezelő (csak néhányat említve az elérhető szerepkörök közül).

Kifejezetten zárolhatja a kritikus erőforrásokat is, megakadályozva, hogy a felhasználók törölhessék vagy módosíthassák azokat. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

Gyakorlati tanácsok: [Biztonsági szempontok az Azure Resource Manager használatához](best-practices-resource-manager-security.md)

## Erőforrások kezelése testreszabott házirendekkel

A Resource Manager lehetővé teszi, hogy létrehozzon testreszabott házirendeket az erőforrások kezeléséhez. A létrehozható házirendek típusa különböző helyzetekre terjedhet ki, mint például egy elnevezési konvenció kényszerítése az erőforrásokon, a telepíthető erőforrások típusainak és példányainak korlátozása, egy adott típusú erőforrás futtatására engedéllyel rendelkező régiók korlátozása vagy a címkeérték megkövetelése az erőforrásokon a számlázás rendezéséhez részleg alapján. A házirendek segítségével csökkentheti a költségeket és biztosíthatja az egységességet az előfizetésében. További információ: [Erőforrások kezelése és hozzáférés szabályozása házirendekkel](resource-manager-policy.md).

## Konzisztens felügyeleti réteg

A Resource Manager teljes mértékben kompatibilis műveleteket biztosít Azure PowerShell eszközön, a Mac, Linux és Windows eszközökhöz készült Azure CLI-n, az Azure portálon vagy REST API-n keresztül. Az Önnek legjobban megfelelő felületet használhatja, és gyorsan, átlátható módon válthat a felületek között. A portál a portálon kívül végrehajtott műveletekről is megjelenít értesítéseket.

További információ a PowerShell eszközről: [Az Azure PowerShell használata a Resource Manager eszközzel](powershell-azure-resource-manager.md) és [Azure Resource Manager parancsmagok](https://msdn.microsoft.com/library/azure/dn757692.aspx).

További információ az Azure CLI eszközről: [A Mac, Linux és Windows eszközökhöz készült Azure CLI használata az Azure Resource Manager eszközzel](xplat-cli-azure-resource-manager.md).

További információ a REST API felületről: [Azure Resource Manager REST API-referencia](https://msdn.microsoft.com/library/azure/dn790568.aspx). A telepített erőforrások REST műveleteinek megtekintése: [Erőforrások megtekintése és módosítása az Azure Resource Explorer használatával](resource-manager-resource-explorer.md).

További információ a portál használatáról: [Deploy resources with Resource Manager templates and Azure portal](resource-group-template-deploy-portal.md) (Erőforrások üzembe helyezése Resource Manager-sablonok és az Azure Portal segítségével).

Az Azure Resource Manager támogatja az eltérő eredetű erőforrás-megosztást (CORS). A CORS segítségével meghívhatja a Resource Manager REST API-t vagy egy Azure szolgáltatás REST API-ját egy másik tartományban található webalkalmazásból. CORS támogatás nélkül a webböngésző megakadályozná, hogy az egy adott tartományban található alkalmazások hozzáférhessenek más tartományban található erőforrásokhoz. A Resource Manager engedélyezi a CORS műveletet az összes érvényes hitelesítő adatokkal rendelkező kérés számára.

## SDK-k

Az Azure SDK-k több nyelven és többféle platformon elérhetőek.
A nyelvi implementációk mindegyike elérhető az ökoszisztéma-csomagkezelőn és a GitHubon keresztül.

Az ezekben az SDK-kban található programkódok az Azure RESTful API-specifikációkból jöttek létre.
Ezek a specifikációk nyílt forráskódúak, és a Swagger 2.0 specifikáción alapulnak.
Az SDK-kód létrehozása egy AutoRest nevű, nyílt forráskódú projekten keresztül történik.
Az AutoRest alakítja át a RESTful API-specifikációkat többnyelvű ügyfélkódtárakká.
Ha javítani szeretné az SDK-ban található, létrejött programkódok bármelyik aspektusát, szabadon elérhető, széles körben használt API-specifikációformátumon alapuló eszközök készlete áll a rendelkezésre az SDK-k létrehozásához.

Itt találhatóak az Open Source SDK-adattáraink. Szívesen vesszük a visszajelzéseket, a hibabejelentéseket és a lekérési kérelmeket.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [AZURE.NOTE]Ha az SDK nem biztosítja a szükséges funkciót, hívhatja közvetlenül az [Azure REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)-t is.

## Példák

### .NET

- [Azure-erőforrások és -erőforráscsoportok kezelése](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [SSH-t használó virtuális gép telepítése sablon alapján](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java

- [Azure-erőforrások kezelése](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
- [Azure-erőforráscsoportok kezelése](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [SSH-t használó virtuális gép telepítése sablon alapján](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js

- [Azure-erőforrások és -erőforráscsoportok kezelése](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [SSH-t használó virtuális gép telepítése sablon alapján](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python

- [Azure-erőforrások és -erőforráscsoportok kezelése](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [SSH-t használó virtuális gép telepítése sablon alapján](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby

- [Azure-erőforrások és -erőforráscsoportok kezelése](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
- [SSH-t használó virtuális gép telepítése sablon alapján](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


A példákon felül a katalógus példái között is kereshet.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## Következő lépések

- A sablonok használatának egyszerű bemutatása: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).
- A sablonok létrehozásának részletes ismertetése: [Útmutató Resource Manager sablonokhoz](resource-manager-template-walkthrough.md).
- A sablonokban használható függvények ismertetése: [Sablonfüggvények](resource-group-template-functions.md)
- A Visual Studio és a Resource Manager együttes használatával kapcsolatos információ: [Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
- A VS Code és a Resource Manager együttes használatával kapcsolatos információk: [Working with Azure Resource Manager Templates in Visual Studio Code](resource-manager-vs-code.md) (Azure Resource Manager-sablonok használata a Visual Studio Code-ban).

Ismertető videó az áttekintésről:

[AZURE.VIDEO azure-resource-manager-overview]



<!--HONumber=sep16_HO1-->


