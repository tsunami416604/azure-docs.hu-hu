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
   ms.date="05/27/2016"
   ms.author="tomfitz"/>

# Az Azure Resource Manager áttekintése

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. Az Azure Resource Manager lehetővé teszi, hogy a megoldásában az erőforrásokkal egy csoportként dolgozzon. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablont használ, amely különböző, például tesztelési, átmeneti és üzemi környezetekben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően. 

## A Resource Manager használatának előnyei

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.
- A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.
- Deklaratív sablonok segítségével definiálhatja a telepítést.
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

## Erőforráscsoportok

Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja az alkalmazás összes erőforrását vagy csak azokat az erőforrásokat, amelyek logikailag egy csoportba tartoznak. Eldöntheti, hogyan kívánja kiosztani az erőforrásokat az erőforráscsoportokba a szervezetének legmegfelelőbb módon.

Néhány fontos tényezőt érdemes figyelembe venni az erőforráscsoport meghatározásakor:

1. A csoportban lévő összes erőforrásnak azonos életciklussal kell rendelkeznie. Egyszerre fogja őket telepíteni, frissíteni és törölni. Ha egy erőforrásnak, például egy adatbázis-kiszolgálónak különböző fejlesztési ciklusban kell léteznie, azt másik erőforráscsoportba kell elhelyezni.
2. Az egyes erőforrások csak egy erőforráscsoportban létezhetnek.
3. Az erőforráscsoporthoz bármikor hozzáadhat, vagy onnan eltávolíthat egy erőforrást.
4. Az erőforrásokat áthelyezheti az egyik erőforráscsoportból a másikba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
4. Az erőforráscsoportok tartalmazhatnak olyan erőforrásokat, amelyek különböző régiókban találhatók.
5. Az erőforráscsoport segítségével meghatározhatja a hozzáférés-vezérlési hatókört felügyeleti műveletekhez.
6. Egy erőforrást egy másik erőforráscsoportban található erőforráshoz akkor érdemes társítani, ha a két erőforrásnak kapcsolatba kell lépnie egymással, de nem rendelkeznek azonos életciklussal (például több alkalmazás csatlakozik egy adatbázishoz). További információ: [Erőforrások csatolása az Azure Resource Manager eszközben](resource-group-link-resources.md)

## Erőforrás-szolgáltatók

Az erőforrás-szolgáltató egy szolgáltatás, amely a Resource Manager eszközön keresztül telepíthető és felügyelhető erőforrásokat biztosítja. Mindegyik erőforrás-szolgáltató REST API-műveleteket biztosít az erőforrásokkal való munkához. Ha például egy Azure Key Vault eszközt kíván telepíteni kulcsok és titkos kulcsok tárolására, a **Microsoft.KeyVault** erőforrás-szolgáltatót fogja használni. Ez az erőforrás-szolgáltató egy **vaults** nevű erőforrástípust biztosít a kulcstároló létrehozásához, illetve egy **vaults/secrets** nevű erőforrástípust a titkos kulcs létrehozásához a kulcstárolóban. Az erőforrás-szolgáltatókról további információt a REST API műveleteik, például a [Key Vault REST API műveletek](https://msdn.microsoft.com/library/azure/dn903609.aspx) megtekintésével tudhat meg.

Az infrastruktúra telepítéséhez és kezeléséhez ismernie kell az erőforrás-szolgáltató adatait; például milyen erőforrástípusokat kínál, a REST API-műveletek verziószámait, a támogatott műveleteket, illetve a létrehozandó erőforrástípusok értékeinek megadásakor használandó sémát. Információk a támogatott erőforrás-szolgáltatókról: [Resource Manager szolgáltatók, régiók, API verziók és sémák](resource-manager-supported-services.md).

## Sablonalapú telepítés

A Resource Manager segítségével létrehozhat egyszerű sablonokat (JSON formátumban), amelyek meghatározzak az alkalmazás telepítését és konfigurációját. Ez a sablon Resource Manager sablonként is ismert és deklaratív lehetőséget biztosít a telepítés meghatározásához. A sablonok segítségével az alkalmazást ismételten telepítheti az alkalmazás életciklusa során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

A sablonon belül meghatározhatja az alkalmazás infrastruktúráját, az infrastruktúra konfigurálásának módját, illetve hogy az alkalmazás kódját hogyan teszi közzé az infrastruktúrában. Nem kell foglalkoznia a telepítés sorrendjével, mivel az Azure Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](resource-group-define-dependencies.md).

Amikor létrehoz egy megoldást a Piactérről, az automatikusan tartalmaz egy telepítési sablont. Nem szükséges teljesen új sablont létrehoznia, mivel kezdetben használhatja a sablont a megoldásához, majd testreszabhatja az adott igényeknek megfelelően. Egy meglévő erőforráscsoport sablonjának lekéréséhez exportálja az erőforráscsoport aktuális állapotát egy sablonba, vagy tekintse meg a sablont, amelyet az adott telepítéshez felhasználtak. Az exportált sablon megtekintése hasznos információkat nyújt a sablon szintaxisáról. Az exportált sablonok használatával kapcsolatos további információk: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).

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

További információ a portál használatáról: [Az Azure portál használata az Azure erőforrások kezeléséhez](./azure-portal/resource-group-portal.md).

Az Azure Resource Manager támogatja az eltérő eredetű erőforrás-megosztást (CORS). A CORS segítségével meghívhatja a Resource Manager REST API-t vagy egy Azure szolgáltatás REST API-ját egy másik tartományban található webalkalmazásból. CORS támogatás nélkül a webböngésző megakadályozná, hogy az egy adott tartományban található alkalmazások hozzáférhessenek más tartományban található erőforrásokhoz. A Resource Manager engedélyezi a CORS műveletet az összes érvényes hitelesítő adatokkal rendelkező kérés számára.

## További lépések

- A sablonok használatának egyszerű bemutatása: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).
- A sablonok létrehozásának részletes ismertetése: [Útmutató Resource Manager sablonokhoz](resource-manager-template-walkthrough.md).
- A sablonokban használható függvények ismertetése: [Sablonfüggvények](resource-group-template-functions.md)
- A Visual Studio és a Resource Manager együttes használatával kapcsolatos információ: [Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

Ismertető videó az áttekintésről:

[AZURE.VIDEO azure-resource-manager-overview]



<!--HONumber=Jun16_HO2--->


