---
title: A számítási csomópontokon – Azure Batch alkalmazáscsomagokat telepíteni |} A Microsoft Docs
description: A számítási csomópontok az alkalmazáscsomagokat kezelő funkciója az Azure Batch segítségével könnyedén kezelheti a több alkalmazás és a kötegelt telepítési-verziók használata.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/15/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fd3eccf3de5d46520dc5a50cab66667c875799e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454607"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Alkalmazások-Batch-alkalmazáscsomagokkal számítási csomópontokra üzembe helyezése

Az alkalmazáscsomagokat kezelő funkciója Azure Batch-feladat alkalmazások egyszerű kezelését és a készlet számítási csomópontjain való üzembe helyezésükben biztosít. Az alkalmazáscsomagokkal feltöltheti és kezelheti a tevékenységek futtatni, beleértve a kiegészítő fájlt az alkalmazások több verzióját. Ezután automatikusan telepítheti ezeket az alkalmazásokat a számítási csomópontok közül legalább egyet a készletben.

Ebből a cikkből megismerheti, hogyan töltheti fel és kezelheti az alkalmazáscsomagok az Azure Portalon. Ezután megismerheti, hogyan, hogy telepítse őket a készlet számítási csomópontjait a számítógépen a [Batch .NET] [ api_net] könyvtár.

> [!NOTE]
> 
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat.
>
> Az API-kat hozhat létre és kezelhet az alkalmazáscsomagok részét képezik a [Batch Management .NET használatával] [ api_net_mgmt] könyvtár. Az API-k egy számítási csomóponton az alkalmazáscsomagok telepítésének részét képezik a [Batch .NET] [ api_net] könyvtár. A rendelkezésre álló Batch API-k más nyelven hasonló funkciók találhatók. 
>
> Az itt ismertetett csomagok funkcióval felülírja a Batch-alkalmazások szolgáltatás a szolgáltatás korábbi verzióiban érhető el.
> 
> 

## <a name="application-package-requirements"></a>Csomag alkalmazáskövetelmények
Az alkalmazáscsomagok használatához kell [Azure Storage-fiókot kapcsol](#link-a-storage-account) a Batch-fiókjához.

## <a name="about-applications-and-application-packages"></a>Alkalmazásokkal és alkalmazáscsomagokkal kapcsolatban
Azure Batch-belül egy *alkalmazás* rendszerverzióval ellátott bináris fájljai, amely képes automatikusan letölti a készletben lévő számítási csomópontok készletét jelenti. Egy *alkalmazáscsomag* hivatkozik egy *meghatározott* bináris fájljait és jelöli egy adott *verzió* az alkalmazás.

![Alkalmazások és az alkalmazáscsomagok összeállítás áttekintő jellegű diagramja][1]

### <a name="applications"></a>Alkalmazások
Batch-alkalmazásokhoz tartalmaz egy vagy több alkalmazás a csomagok és az alkalmazás konfigurációs beállításokat ad meg. Például egy alkalmazás is adja meg az alapértelmezett alkalmazáscsomag-verzió a számítási csomópontok, és hogy frissíthető vagy törölhető a hozzá tartozó csomagok telepítéséhez.

### <a name="application-packages"></a>Alkalmazáscsomagok
Alkalmazáscsomag egy .zip fájlt, amely tartalmazza az alkalmazás bináris fájljainak és a feladatok az alkalmazás futtatásához szükséges fájlok. Minden egyes alkalmazás-csomag az alkalmazás egy adott verzióját jelöli.

Az alkalmazáscsomagokat a készletek és a tevékenységek szintjén is megadhat. Egy készlet vagy feladat létrehozásakor megadhat egy vagy több ezeket a csomagokat, és (opcionálisan) egy verziót.

* **Az alkalmazáscsomagok tárolókészlet** telepített *minden* csomópont a készletben. Alkalmazások vannak üzembe helyezve, amikor egy csomópont csatlakozik a készlethez, és amikor újraindítanak vagy rendszerképét alaphelyzetbe állítják.
  
    Készletszintű alkalmazáscsomagokat akkor megfelelő, ha a készlet minden csomópontján hajtsa végre a feladatok. Egy vagy több alkalmazáscsomagot is megadhat, ha készletet hoz létre, és adja hozzá, vagy egy meglévő készlet-csomagok frissítése. Ha frissít egy meglévő készletszintű alkalmazáscsomagokat, újra kell indítani az új csomag telepítése a csomópontokat.
* **Tevékenység-alkalmazáscsomagok** csak egy számítási csomóponton a tevékenység parancssorának futtatása előtt egy feladatot futtatott, ütemezett vannak telepítve. Ha a megadott alkalmazáscsomag és verzió már a csomóponton, nem újratelepítése van, és a meglévő csomag segítségével történik.
  
    Alkalmazáscsomagok hasznosak megosztott készletes környezetekben, ahol különböző feladatok egy készletben futnak, és a készletet a feladat elvégzésekor nem törlődik. Ha a feladatnál a készletben kevesebb a tevékenység, mint a csomópont, az alkalmazáscsomagok használatával csökkentheti az adatátviteli igényt, mivel így a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyek ténylegesen futtatják a tevékenységeket.
  
    Más alkalmazáscsomagok is kihasználó forgatókönyvek nagyméretű alkalmazások esetén futó feladatok, de csak néhány feladatot. Például egy előfeldolgozási szakaszban vagy egyesítési feladat, a előfeldolgozásához vagy egyesítési kérelem esetén nagy terhelést jelentő adatbázisokhoz, előfordulhat, hogy előnyt az alkalmazáscsomagok.

> [!IMPORTANT]
> Az alkalmazások és alkalmazáscsomagok Batch-fiókon belül számától és a csomag maximális mérete a korlátozások vonatkoznak. Lásd: [kvóták és korlátozások az Azure Batch szolgáltatás](batch-quota-limit.md) korlátokkal kapcsolatos részletekért.
> 
> 

### <a name="benefits-of-application-packages"></a>Az alkalmazáscsomagok előnyei
Az alkalmazáscsomagokat a Batch-megoldás kódja leegyszerűsítheti és csökkentheti a tevékenységek által futtatott alkalmazások kezelésére a terhelését.

Az alkalmazáscsomagokkal a készlet indítási tevékenységet nem kell egyéni erőforrásfájlokat a csomópontjaira történő telepítéshez hosszú listáját adja meg. Nem kell manuálisan kezelheti az alkalmazásfájlokat az Azure Storage-ban, vagy a csomóponton több verzióját. És nem kell aggódnia generálása [SAS URL-címek](../storage/common/storage-dotnet-shared-access-signature-part-1.md) biztosíthat hozzáférést a fájlokat a Storage-fiókban. A Batch az Azure Storage, az alkalmazáscsomagok tárolására, és a számítási csomópontokra való telepítéséhez a háttérben működik.

> [!NOTE] 
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. Ha az indítási tevékenység ezt a korlátot túllépi, majd az alkalmazáscsomagok használatával egy másik lehetőséget. Az erőforrás-fájljait tartalmazó tömörített archívumot is létrehozhat, töltse fel az Azure Storage-blob és majd bontsa ki azt a parancssorból, az indítási tevékenység. 
>
>

## <a name="upload-and-manage-applications"></a>Töltse fel és kezelhet olyan alkalmazásokat
Használhatja a [az Azure portal] [ portal] vagy a Batch Management API-k kezelése az alkalmazáscsomagokat a Batch-fiókban. A következő néhány szakaszban először bemutatjuk, hogyan egy Storage-fiókot, majd bemutatjuk a további alkalmazásokat és a csomagok és kezelnie azokat a portállal.

### <a name="link-a-storage-account"></a>A Storage-fiók
Az alkalmazáscsomagok használatához először rendelnie egy [Azure Storage-fiók](batch-api-basics.md#azure-storage-account) a Batch-fiókjához. Ha nem konfigurálta még egy tárfiókot, az Azure Portalon kattintson az első alkalommal figyelmeztetést jelenít meg **alkalmazások** Batch-fiókban.



!["Nincs beállítva tárfiók" figyelmeztetés az Azure Portalon][9]

A Batch szolgáltatás a társított Storage-fiók segítségével tárolja az alkalmazáscsomagokat. Miután a két fiók társított, a Batch automatikusan telepítheti a csomagok tárolva a társított Storage-fiók a számítási csomópontokra. A Storage-fiókot kapcsol a Batch-fiókhoz, kattintson a **tárfiók** a a **figyelmeztetés** ablakot, és kattintson **Tárfiók** újra.

![Az Azure Portalon válassza ki a tárfiók panel][10]

Javasoljuk, hogy hozzon létre egy Storage-fiók *kifejezetten* a Batch-fiókhoz való használatra, és Itt jelölje ki. Miután létrehozott egy tárfiókot, majd kapcsolat, a Batch-fiók használatával a **Tárfiók** ablak.

> [!NOTE] 
> Jelenleg nem használható az alkalmazáscsomagok az Azure Storage-fiókhoz konfigurált [tűzfalszabályok](../storage/common/storage-network-security.md).
> 

A Batch szolgáltatás az Azure Storage segítségével tárolja az alkalmazáscsomagokat blokkblobok formájában. Ön [felszámítva, normál] [ storage_pricing] a blokkblob hivatkozását az adatokat, és egyes csomagok mérete nem haladhatja meg a [maximális block blob mérete](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Mindenképpen vegye figyelembe a méretét és az alkalmazáscsomagok számát, és bizonyos időközönként eltávolítja az elavult csomagok költségek csökkentése érdekében.
> 
> 

### <a name="view-current-applications"></a>Jelenlegi kérelmek megtekintése
A Batch-fiókhoz az alkalmazások megtekintéséhez kattintson a **alkalmazások** menüpontja megtekintése közben a bal oldali menüben a **Batch-fiók**.

![Alkalmazások csempe][2]

A menüelem kiválasztásával megnyílik az **alkalmazások** ablakban:

![Alkalmazások listázása][3]

Ebben az ablakban minden alkalmazás azonosítója a fiók és a következő tulajdonságokat jeleníti meg:

* **Csomagok**: A jelen alkalmazáshoz rendelt verziók számát.
* **Alapértelmezett verzió**: Az alkalmazás verziója telepítve van, ha nem azt jelzik a verzió, adja meg az alkalmazás-készlet. Ez a beállítás nem kötelező.
* **Engedélyezi a frissítéseket**: Az érték, amely meghatározza, hogy csomag frissítései, törléseket és kiegészítéseit engedélyezettek. Ha a beállított érték **nem**, csomag frissítések és törlések le vannak tiltva, az alkalmazás. Csak új alkalmazáscsomag-verziók is hozzáadhatók. Az alapértelmezett érték az **Igen**.

### <a name="view-application-details"></a>Az alkalmazás részleteinek megtekintése
Az alkalmazás részleteit, jelölje ki az alkalmazás a **alkalmazások** ablak.

![Az alkalmazás részletei][4]

Az alkalmazás adatokat az alkalmazás a következő beállításokat lehet megadni.

* **Engedélyezi a frissítéseket**: Adja meg, hogy az alkalmazáscsomagok is frissíthető és nem törölhető. Lásd: "Frissíteni vagy törölni egy alkalmazáscsomagot" a cikk későbbi részében.
* **Alapértelmezett verzió**: Adjon meg egy alapértelmezett alkalmazáscsomag üzembe helyezése számítási csomópontokra.
* **Megjelenített név**: Adjon meg egy rövid nevet, amelyet a Batch-megoldás használhat, amikor megjeleníti az alkalmazás adatait, például a felhasználói felületen, a szolgáltatás, amely a Batch keresztül az ügyfeleknek.

### <a name="add-a-new-application"></a>Egy új alkalmazás hozzáadása
Hozzon létre egy új alkalmazást, adjon hozzá egy alkalmazáscsomagot, és adjon meg egy új, egyedi alkalmazás. Az első alkalmazáscsomag adja hozzá az új alkalmazás azonosítójával is az új alkalmazást hoz létre.

Kattintson az **Alkalmazások** > **Hozzáadás** elemre.

![Új alkalmazás panel az Azure Portalon][5]

A **új alkalmazás** ablak a beállításait az új alkalmazás és a virtuálisalkalmazás-csomag a következő mezőket is tartalmazza.

**Alkalmazás azonosítója**

Ez a mező az új alkalmazás, amely a standard szintű Azure Kötegazonosító ellenőrzési szabályok hatálya alá tartozik Azonosítóját határozza meg. A szabályok, amelyek biztosítják egy Alkalmazásazonosítót az alábbiak szerint:

* Windows-csomópontokon a azonosító alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat tetszőleges kombinációját is tartalmazhat. A Linux-csomópontok csak alfanumerikus karaktereket és aláhúzásjeleket tartalmazhat használata engedélyezett.
* Nem tartalmazhat több mint 64 karakternél.
* A Batch-fiókon belül egyedinek kell lennie.
* Egy nagybetűket és a kis-és nagybetűket.

**Verzió**

Ez a mező határozza meg a feltöltendő alkalmazáscsomag verziója. Verzió-karakterláncot a következő érvényesítési szabályok a következők:

* A Windows-csomópontok a verzió-karakterlánc alfanumerikus karaktereket, kötőjeleket, aláhúzásjeleket és időszakok tetszőleges kombinációját is tartalmazhat. A Linux-csomópontokat a verzió-karakterlánc csak betűket, számokat és aláhúzásjeleket tartalmazhat.
* Nem tartalmazhat több mint 64 karakternél.
* Az alkalmazáson belül egyedinek kell lennie.
* A nagybetűket és a kis-és nagybetűket.

**Virtuálisalkalmazás-csomag**

Az alkalmazás bináris fájljainak magában foglaló .zip fájl és támogató fájlokat, amelyek szükségesek az alkalmazás végrehajtása ebben a mezőben adja meg. Kattintson a **válasszon ki egy fájlt** be vagy tallózással keresse meg, és válassza ki az olyan .zip fájlba, az alkalmazás fájljait tartalmazza a mappa ikont.

Miután kijelölt egy fájlt, kattintson a **OK** az Azure Storage a feltöltés elindításához. A feltöltési művelet befejeződése után a portálon egy értesítést jelenít meg. Attól függően, hogy a fájl mérete és a hálózati kapcsolat sebességét a művelet eltarthat egy ideig.

> [!WARNING]
> Ne zárja be a **új alkalmazás** ablak a feltöltési művelet befejezése előtt. Ezzel leállítja a feltöltési folyamat.
> 
> 

### <a name="add-a-new-application-package"></a>Egy új alkalmazás-csomag hozzáadása
Egy alkalmazás-csomag verziója egy meglévő alkalmazás hozzáadásához válassza az alkalmazás a **alkalmazások** windows, és kattintson **csomagok** > **Hozzáadás**.

![Adja hozzá az application package panel az Azure Portalon][8]

Amint láthatja, a mezők szabályainak megfelelően a **új alkalmazás** ablakában, de a **alkalmazásazonosító** box le van tiltva. Mint az új alkalmazást, adja meg a **verzió** az új csomag, keresse meg a **Virtuálisalkalmazás-csomag** .zip fájlt, majd kattintson a **OK** a csomag feltöltése.

### <a name="update-or-delete-an-application-package"></a>Update vagy delete alkalmazáscsomaggal
Frissítéséhez, vagy törölje a meglévő alkalmazáscsomag, nyissa meg az alkalmazás részleteit, kattintson **csomagok**, kattintson a **három** , amelyet szeretne módosítani, és válassza ki az alkalmazáscsomag sorába a művelet végrehajtására vonatkozó szándékát.

![Frissíteni vagy törölni a csomagot az Azure Portalon][7]

**Update**

Amikor rákattint **frissítés**, a **frissítési csomag** ablak jelenik meg. Ebben az ablakban hasonlít a **új alkalmazáscsomagot** ablakban, azonban csak a csomag kijelölési mező engedélyezve van, lehetővé téve, hogy adjon meg egy új ZIP-fájlt a feltöltéshez.

![Frissítési csomag panel az Azure Portalon][11]

**Törlés**

Amikor rákattint **törlése**, a rendszer felkéri a csomag verziójának a törlés jóváhagyásához és Batch törli a csomagot az Azure Storage-ból. Ha törli egy alkalmazás alapértelmezett verzióját az **alapértelmezett verzió** beállítást a rendszer eltávolítja az alkalmazáshoz.

![Alkalmazás törlése ][12]

## <a name="install-applications-on-compute-nodes"></a>Alkalmazások telepítése számítási csomópontokon
Most, hogy megismerte, hogyan kezelheti az alkalmazáscsomagokat és az Azure portal, hogyan helyezhet üzembe számítási csomópontokat, és futtathatja őket a Batch-feladatok is megbeszélhetünk.

### <a name="install-pool-application-packages"></a>Készletszintű alkalmazáscsomagokat telepíteni
Telepíti az alkalmazáscsomagot a készlet összes számítási csomóponton, adjon meg egy vagy több alkalmazáscsomagot *hivatkozások* a készlet. Az alkalmazáscsomagok, amelyek egy készlet megadott települnek minden számítási csomóponton, amikor a csomópont csatlakozik a készlethez, és amikor a csomópontot újraindítanak vagy rendszerképét alaphelyzetbe állítják.

A Batch .NET-ben, adjon meg egy vagy több [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] új készlet létrehozásakor, vagy egy meglévő készlet. A [ApplicationPackageReference] [ net_pkgref] osztály megadja, hogy egy alkalmazás-Azonosítót és verziót telepíteni egy készlet számítási csomópontjain.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Ha egy alkalmazás csomag központi telepítésének bármilyen okból nem sikerül, a Batch szolgáltatás jelöli meg a csomópont [használhatatlan][net_nodestate], és nincsenek feladatok ütemezve lesznek a végrehajtásra ezen a csomóponton. Ebben az esetben érdemes **indítsa újra a** a csomag rendszerbe állítása újraindítani a csomópontot. A csomópont újraindítása is lehetővé teszi, hogy a feladatütemezés ismét a csomóponton.
> 
> 

### <a name="install-task-application-packages"></a>Alkalmazáscsomagok telepítése
Alkalmazáscsomag megad egy készlet hasonlóan *hivatkozások* feladathoz. Feladat ütemezve van egy csomóponton, ha a csomag letöltött és kibontott előtt a tevékenység parancssorát. Ha a megadott csomag és verziója már telepítve van a csomóponton, a csomag letöltése nem történik, és a meglévő csomag segítségével történik.

Egy feladat alkalmazáscsomag telepítése, konfigurálása a feladat [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] tulajdonság:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>A telepített alkalmazások
A csomagokat, amelyek egy készlet vagy a feladathoz megadott letöltődnek és ki kell olvasni, egy névvel ellátott címtárhoz a `AZ_BATCH_ROOT_DIR` uzlu. A Batch is létrehoz egy környezeti változó, amely a nevesített könyvtár elérési útját tartalmazza. A feladat parancssorait használja ezt a környezeti változót, ha a hivatkozó az alkalmazás a csomóponton. 

A Windows-csomópontok a változó a következő formátumban:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

A Linux-csomópontok formátuma kissé eltérő. Pontokat (.), kötőjeleket (-) és a kettős kereszteket (#), aláhúzásjeleket, a környezeti változóban van egybesimított. Továbbá vegye figyelembe, hogy a rendszer megőrzi-e a kis-és az alkalmazás azonosítója. Példa:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` és `version` olyan értékek, amelyek a központi telepítés megadott alkalmazások és csomagok verziószámnak. Például, ha a megadott alkalmazás 2.7-es verzió *blender* telepíteni kell a Windows-csomópontok, a feladat parancssorait a környezeti változó használna a fájlok eléréséhez:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux-csomópontokat a következő formátumban adja meg a környezeti változó. Simítja egybe a pontokat (.), kötőjeleket (-) és a kettős keresztet (#), aláhúzásjeleket, és a kis-és az Alkalmazásazonosítót megőrzése:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Ha feltölt egy alkalmazáscsomagot, megadhat egy alapértelmezett verziót, a számítási csomópontok üzembe. Ha megadta, hogy egy alkalmazás alapértelmezett verzióját, a verzió utótag kihagyhatja, ha az alkalmazás hivatkozik. Adhat meg az alapértelmezett alkalmazás verziószáma az Azure Portalon a **alkalmazások** ablakban látható módon [töltheti fel és kezelheti az alkalmazások](#upload-and-manage-applications).

Például, ha azt állítja "2.7-es" az alkalmazás alapértelmezett verzióját *blender*, és a tevékenységek hivatkozni a következő környezeti változót, majd a Windows-csomópontok 2.7-es verzió fogja végrehajtani:

`AZ_BATCH_APP_PACKAGE_BLENDER`

A következő kódrészlet azt mutatja be egy példa a feladat parancssori, amely elindítja az alapértelmezett verzióját az *blender* alkalmazás:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Lásd: [környezeti beállítások tevékenységekhez](batch-api-basics.md#environment-settings-for-tasks) a a [Azure Batch funkcióinak áttekintése](batch-api-basics.md) számítási csomópont környezeti beállítások további információt.
> 
> 

## <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése
Ha egy meglévő készlet már konfigurálva van az alkalmazáscsomag, megadhat egy új csomagot a készlet. Ha megad egy új csomagreferenciát-készlet esetén a következők érvényesek:

* A Batch szolgáltatás telepíti az új csomag az új csomópontok a készletre való és bármely meglévő csomópontján, amely újraindítanak vagy rendszerképét alaphelyzetbe állítják.
* A számítási csomópontok, amelyek már a készletben található csomaghivatkozásokhoz frissítésekor nem telepíti automatikusan az új alkalmazáscsomagot. Ezek a számítási csomópontok kell lennie újraindítják vagy alaphelyzetbe állítják fogadni az új csomagot.
* Új csomag telepítésekor a létrehozott környezeti változókat az új alkalmazáscsomag-hivatkozás jelenik meg.

Ebben a példában a meglévő készlet rendelkezik 2.7-es verzióját a *blender* egyik konfigurált alkalmazás annak [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. A készlet csomópontjain 2.76b verzióra frissítéséhez adja meg egy új [ApplicationPackageReference] [ net_pkgref] az új verzió, a változtatás véglegesítése.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Most, hogy az új verzióra van beállítva, a Batch szolgáltatás telepíti-e bármelyik verzió 2.76b *új* csomópont, amely csatlakozik a készlethez. A csomópontokon, amelyek 2.76b telepítéséhez *már* indítsa újra a készlethez, vagy újból lemezképet létrehozni őket. Vegye figyelembe, hogy a számítógép újraindítva csomópontok megőrzése a fájlokat az előző csomag központi telepítések.

## <a name="list-the-applications-in-a-batch-account"></a>A Batch-fiókokban az alkalmazások listáját
Listázhatja az alkalmazások és a egy Batch-fiókban lévő csomagjaikat használatával a [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metódust.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Belefoglalni
Az alkalmazáscsomagokkal segíthet az ügyfeleknek, válassza ki arra, hogy az alkalmazásokat, és adja meg a pontos verziót kell használni, amikor a Batch-kompatibilis szolgáltatással a feladatok feldolgozásában. Meg lehet adni arra, hogy az ügyfelei számára, hogy töltse fel, és nyomon követheti a saját alkalmazások a service-ben.

## <a name="next-steps"></a>További lépések
* A [Batch REST API] [ api_rest] használatát is támogatja az alkalmazáscsomagok dolgozhat. Lásd a [applicationPackageReferences] [ rest_add_pool_with_packages] elemében [készlet hozzáadása fiókhoz] [ rest_add_pool] információ megadása a csomagok telepítése a REST API használatával. Lásd: [alkalmazások] [ rest_applications] alkalmazással kapcsolatos információk beszerzése a Batch REST API-val kapcsolatban.
* Ismerje meg, hogyan programozott módon [Azure Batch-fiókok és kvóták kezelése a Batch Management .NET használatával](batch-management-dotnet.md). A [Batch Management .NET használatával] [ api_net_mgmt] könyvtár fiók létrehozását és törlését funkcióit a Batch-alkalmazás vagy szolgáltatás is engedélyezheti.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Csomagok magas szintű diagramja"
[2]: ./media/batch-application-packages/app_pkg_02.png "Alkalmazások csempe az Azure Portalon"
[3]: ./media/batch-application-packages/app_pkg_03.png "Alkalmazások panel az Azure Portalon"
[4]: ./media/batch-application-packages/app_pkg_04.png "Alkalmazás részletei panel az Azure Portalon"
[5]: ./media/batch-application-packages/app_pkg_05.png "Új alkalmazás panel az Azure Portalon"
[7]: ./media/batch-application-packages/app_pkg_07.png "Frissítése vagy törlése az Azure Portalon legördülő csomagok"
[8]: ./media/batch-application-packages/app_pkg_08.png "Új application package panel az Azure Portalon"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nincs társított tárolási fiók miatti riasztás"
[10]: ./media/batch-application-packages/app_pkg_10.png "Az Azure Portalon válassza ki a tárfiók panel"
[11]: ./media/batch-application-packages/app_pkg_11.png "Frissítési csomag panel az Azure Portalon"
[12]: ./media/batch-application-packages/app_pkg_12.png "Törlési csomag jóváhagyás az Azure Portalon"
