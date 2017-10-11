---
title: "Alkalmazáscsomagok telepítse a számítási csomópontok - Azure Batch |} Microsoft Docs"
description: "Használja az alkalmazás csomagok szolgáltatása könnyedén kezelheti a több alkalmazás és a kötegelt telepítés verziók Azure Batch számítási csomópontjain."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: afcc04c80ec15872a22de5d5969a7ef6a583562f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>A számítási csomópontokat a kötegelt alkalmazáscsomagok alkalmazások központi telepítése

Az alkalmazás csomagok Azure-köteg szolgáltatása feladat alkalmazások kezelésének és a telepítés, a készlet számítási csomópontjain. Az alkalmazáscsomagok töltse fel, és kezelheti az alkalmazásokat, a feladatok futnak, beleértve a segédfájlok több verziója. Majd automatikusan telepítheti ezeket az alkalmazásokat a számítási csomópontok közül a készletben.

Meg ebből a cikkből megtudhatja, hogyan töltse fel és kezelheti az alkalmazáscsomagok az Azure portálon. Majd megtudhatja, hogy telepítse őket a készlet számítási csomópont hogyan a [Batch .NET] [ api_net] könyvtárban.

> [!NOTE]
> 
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat.
>
> Az API-k létrehozására és kezelésére alkalmazáscsomagok részei a [Batch Management .NET kódtárral] [[api_net_mgmt]] könyvtár. Az API-k, a számítási csomóponton alkalmazáscsomagok telepítésének részét képezik a [Batch .NET] [ api_net] könyvtárban.  
>
> Az itt leírt alkalmazás csomagok szolgáltatás írja felül a Batch-alkalmazások szolgáltatás a szolgáltatás előző verzióiban érhető el.
> 
> 

## <a name="application-package-requirements"></a>Csomag alkalmazáskövetelmények
Alkalmazáscsomagok használatához szüksége [egy Azure Storage-fiók csatolása](#link-a-storage-account) a Batch-fiókhoz.

Ez a szolgáltatás bemutatott [Batch REST API] [ api_rest] 2015-12-01.2.2 verziója és a megfelelő [Batch .NET] [ api_net] könyvtárverzió 3.1.0. Azt javasoljuk, hogy mindig használja a legújabb API-verzió az kötegelt használatakor.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat.
>
>

## <a name="about-applications-and-application-packages"></a>Alkalmazások és csomagok
Azure Batch belül egy *alkalmazás* rendszerverzióval ellátott bináris fájljai, a számítási csomópontok a készlet automatikusan letöltött készlete hivatkozik. Egy *alkalmazáscsomag* hivatkozik egy *meghatározott* e bináris fájljait és jelöli egy adott *verzió* az alkalmazás.

![Magas szintű diagramját, alkalmazások és csomagok][1]

### <a name="applications"></a>Alkalmazások
Egy alkalmazás kötegben tartalmaz egy vagy több alkalmazás a csomagok és az alkalmazás konfigurációs beállításait adja meg. Például egy alkalmazás telepítése számítási csomópontok, és hogy a csomagokat is frissíthető és nem törölhető az alapértelmezett alkalmazáscsomag verzióját adhat meg.

### <a name="application-packages"></a>Alkalmazáscsomagok
Alkalmazáscsomag, a bináris alkalmazásfájlokat tartalmazó .zip fájl és a fájlokat, amelyek szükségesek az alkalmazás futtatásához a feladatokhoz. Minden alkalmazáscsomag jelenti. az alkalmazás egy adott verziójához.

A készlet és a feladat szinten alkalmazáscsomagok adhatja meg. Egy készletet vagy feladat létrehozásakor megadhatja egy vagy több ezeket a csomagokat, és (opcionálisan) verzióval.

* **Tárolókészlet alkalmazáscsomagok** telepített *minden* csomópont a készletben. Alkalmazások vannak telepítve, amikor a csomópont csatlakozik egy készletet, és amikor újraindították vagy a lemezképet.
  
    Készlet alkalmazáscsomagok megfelelőek, amikor egy alkalmazáskészlet összes csomópontjának hajtható végre egy feladat tevékenységeit. Megadhat egy vagy több alkalmazáscsomagok készletet hoz létre, és adja hozzá, vagy egy meglévő készlet-csomagok frissítése. Ha egy meglévő készlet alkalmazáscsomagok frissíti, újra kell indítania a csomópontot az új csomag telepítése.
* **Tevékenység-alkalmazáscsomagok** csak egy feladat, csak a parancssor futtatása a feladatütemezés futtatása előtt futtatandó számítási csomópont van telepítve. Ha a megadott alkalmazáscsomag és verziója már a csomóponton, nem újra telepíteni, és a meglévő csomag használata.
  
    A feladat alkalmazáscsomagok olyan hasznos megosztott készlet környezetekben, ahol különböző feladat futtatása egy készletet, és a készlet nem törlődik, amikor a feladat befejezését. Ha a feladatnál a készletben kevesebb a tevékenység, mint a csomópont, az alkalmazáscsomagok használatával csökkentheti az adatátviteli igényt, mivel így a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyek ténylegesen futtatják a tevékenységeket.
  
    Más tevékenység alkalmazáscsomagok is előnyeit kihasználó forgatókönyvek, amelyek a nagyméretű alkalmazások futnak feladatok, de csak néhány feladatot. Például egy előre feldolgozásra szakaszban, vagy egy merge feladatra, ahol az előzetes feldolgozás vagy egyesítési alkalmazás nehéz, előfordulhat, hogy előnyt az alkalmazáscsomagok feladat.

> [!IMPORTANT]
> Nincsenek korlátozások, alkalmazások és a Batch-fiók alkalmazás csomagok számát és a csomag maximális mérete. Lásd: [kvótái és korlátai az Azure Batch szolgáltatás](batch-quota-limit.md) kapcsolatos részletekért.
> 
> 

### <a name="benefits-of-application-packages"></a>Az alkalmazáscsomagok előnyei
Alkalmazáscsomagok egyszerűsítheti a kötegelt megoldásban a kódot, és csökkentheti a terhelés, a feladatok által futtatott alkalmazásokat kezeléséhez szükségesek.

Alkalmazáscsomagok esetén az alkalmazáskészlet indítása feladat nem kell egyes Erőforrásfájlok csomópontjain telepítendő hosszú listáját adja meg. Nincs több verzió az alkalmazásfájlokat az Azure Storage vagy a csomóponton kézi kezelését. És nem kell aggódnia generálása [SAS URL-címek](../storage/common/storage-dotnet-shared-access-signature-part-1.md) a tárfiókban lévő fájlok eléréséhez. Kötegelt működik az Azure Storage alkalmazáscsomagok tárolására is, és telepítheti azokat a számítási csomópontok a háttérben.

> [!NOTE] 
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. A kezdő tevékenység meghaladja ezt a korlátot, majd az alkalmazás-csomagok használata esetén egy másik lehetőséget. Is létrehozhat az erőforrás-fájlokat tartalmazó tömörített archívum létrehozása, töltse fel az Azure Storage blob, és ezután bontsa ki a parancssorból a kezdő tevékenység. 
>
>

## <a name="upload-and-manage-applications"></a>Alkalmazások kezelését és feltöltését
Használhatja a [Azure-portálon] [ portal] vagy a [Batch Management .NET kódtárral](batch-management-dotnet.md) szalagtár kezelése a Batch-fiók alkalmazás csomagokat. A következő néhány szakaszokban először megmutatjuk, hogyan tárfiók hivatkozásra, majd további alkalmazásokat és a csomagok és kezelnie azokat a portállal ismertetik.

### <a name="link-a-storage-account"></a>A tárfiók csatolása
Alkalmazáscsomagok használatához először a Batch-fiókhoz kell kapcsolni egy Azure Storage-fiókot. Ha még nincs konfigurálva a Storage-fiók, az Azure portálon kattintson az első alkalommal figyelmeztetést jelenít meg a **alkalmazások** csempére a **a Batch-fiók** panelen.

> [!IMPORTANT]
> Jelenleg a Batch-támogatja *csak* a **általános célú** tárfióktípus 5, lépésben leírt [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account), a [kapcsolatos Azure Storage-fiókok](../storage/common/storage-create-storage-account.md). Egy Azure Storage-fiók összekötése a Batch-fiókhoz, kapcsolja *csak* egy **általános célú** storage-fiók.
> 
> 

!["Nincs beállítva tárfiók" figyelmeztetés Azure-portálon][9]

A Batch szolgáltatás a kapcsolódó tárfiók a alkalmazáscsomagok tárolására használja. A két fiók csatolta, miután kötegelt automatikusan telepítheti a csomagokat, a számítási csomópontok a csatolt tárfiók tárolja. A Batch-fiók a tárfiók csatolásához kattintson **tárolási Fiókbeállítások** a a **figyelmeztetés** panelt, és kattintson **Tárfiók** a a **Tárfiók** panelen.

![Storage-fiók panelen válassza az Azure-portálon][10]

Azt javasoljuk, hogy hozzon létre egy tárfiókot *kifejezetten* a Batch-fiókhoz való használatra, és jelölje ki itt. A storage-fiók létrehozásával kapcsolatos további információkért lásd: "Create a Storage-fiók" az [Azure Storage-fiókok](../storage/common/storage-create-storage-account.md). Miután létrehozott egy tárfiókot, majd társíthatja azt a Batch-fiók használatával a **Tárfiók** panelen.

> [!WARNING]
> A Batch szolgáltatás Azure tárhelyét használja az alkalmazáscsomagok blokkblobként tárolni. Ön [szokásos módon felszámított] [ storage_pricing] a blokk blob adatok. Győződjön meg arról, fontolja meg a méretét és az alkalmazáscsomagok számát, és bizonyos időközönként eltávolítja az elavult csomagok költségek minimalizálása érdekében.
> 
> 

### <a name="view-current-applications"></a>Aktuális alkalmazások megtekintése
A Batch-fiókhoz az alkalmazások megtekintéséhez kattintson a **alkalmazások** menüpont megjelenítése közben a bal oldali menüben a **a Batch-fiók** panelen.

![Alkalmazások csempe][2]

Ezzel a beállítással menü megnyitása a **alkalmazások** panel:

![Alkalmazások listája][3]

A **alkalmazások** panel megjeleníti minden egyes alkalmazás Azonosítójának fiókját és a következő tulajdonságokkal:

* **Csomagok**: A jelen alkalmazáshoz rendelt verziók számát.
* **Alapértelmezett verzió**: A telepített, ha nem jelzi azt egy verzió az alkalmazás egy készlet megadott verziója. Ez a beállítás nem kötelező.
* **Frissítések engedélyezése**: az érték, amely meghatározza, hogy frissíti a csomagot, törléseket és kiegészítéseit engedélyezettek. Ha a beállított érték **nem**, csomag frissítések és törlések le van tiltva az alkalmazás. Csak új alkalmazáscsomag-verziók is hozzáadhatók. Az alapértelmezett érték **Igen**.

### <a name="view-application-details"></a>Az alkalmazás részleteinek megtekintése
Az alkalmazás részleteit tartalmazó panel megnyitásához, válassza ki az alkalmazást a **alkalmazások** panelen.

![Az alkalmazás részletei][4]

Az alkalmazás részleteit megjelenítő panelen található az alkalmazás a következő beállításokat lehet megadni.

* **Frissítések engedélyezése**: Adja meg, hogy az alkalmazáscsomagok is frissíthető és nem törölhető. Tekintse meg a "Frissíteni vagy törölni egy alkalmazáscsomagot" a cikk későbbi részében.
* **Alapértelmezett verzió**: Adjon meg egy számítási csomópontjain telepítendő alapértelmezett alkalmazáscsomagot.
* **Megjelenített név**: Adjon meg egy rövid nevet a kötegelt megoldás lehet használni, amikor megjeleníti az alkalmazás adatait, például a felhasználói felületen, a szolgáltatás, amely a kötegelt keresztül az ügyfeleknek.

### <a name="add-a-new-application"></a>Új alkalmazás felvétele
Hozzon létre egy új alkalmazást, vegye fel egy alkalmazáscsomagot, és adjon meg egy új, egyedi azonosítót. Az első alkalmazáscsomag az új alkalmazás azonosítójával hozzáadott is létrehoz az új alkalmazás.

Kattintson a **Hozzáadás** a a **alkalmazások** panelt, és nyissa meg a **új alkalmazás** panelen.

![Új alkalmazás panel az Azure-portálon][5]

A **új alkalmazás** panelen a következő mezőket az új alkalmazások és alkalmazáscsomag beállításainak megadásához nyújt.

**Alkalmazásazonosító**

Ez a mező az új alkalmazást, amely Azure Kötegazonosító szabványos érvényesítési szabályok Azonosítóját adja meg. Az Alkalmazásazonosító biztosítani a szabályok a következők:

* Windows-csomópont a az azonosító az alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat tetszőleges kombinációját tartalmazhatja. Linux-csomópont csak alfanumerikus karaktereket és aláhúzás karaktereket tartalmazhatnak engedélyezettek.
* Nem lehet hosszabb 64 karakternél.
* A Batch-fiók belül egyedinek kell lennie.
* Egy nagybetűket és a nagybetűk között.

**Verzió**

Ez a mező határozza meg a feltölteni kívánt alkalmazáscsomag verzióját. Verzió-karakterlánc a következő ellenőrzési szabályok vonatkoznak:

* Windows-csomópont a verzió-karakterlánca az alfanumerikus karaktereket, kötőjeleket, aláhúzásjeleket és időszakok tetszőleges kombinációját tartalmazhatja. Linux csomópontján verzió-karakterlánca csak alfanumerikus karaktereket és aláhúzásjeleket tartalmazhat.
* Nem lehet hosszabb 64 karakternél.
* Az alkalmazáson belül egyedinek kell lennie.
* A rendszer megőrzi és a nagybetűk között.

**Alkalmazáscsomag**

A bináris alkalmazásfájlokat tartalmazó .zip fájl és a fájlokat, amelyek szükségesek az alkalmazás ebben a mezőben adja meg. Kattintson a **válasszon ki egy fájlt** mező vagy a mappa ikonra kattintva jelölje ki az alkalmazás fájlokat tartalmazó .zip-fájlt.

Miután kijelölt egy fájlt, kattintson **OK** Azure Storage való feltöltés indításához. A feltöltési művelet befejezésekor a portál egy értesítést jelenít meg, és a panel bezárása után. Attól függően, hogy a feltölteni kívánt fájl méretétől és a hálózati kapcsolat sebességét a művelet eltarthat egy ideig.

> [!WARNING]
> Ne zárja be a **új alkalmazás** panel a feltöltési művelet befejezése előtt. Ezzel megszűnik a feltöltési folyamat.
> 
> 

### <a name="add-a-new-application-package"></a>Új alkalmazás csomag hozzáadása
Alkalmazás új csomagverziójának egy meglévő alkalmazás hozzáadásához válassza ki az alkalmazás a **alkalmazások** panelen kattintson **csomagok**, kattintson a **Hozzáadás** megnyitásához a **Hozzáadás csomag** panelen.

![Adja hozzá az alkalmazás csomag panel Azure-portálon][8]

Ahogy látja, a mezők megegyeznek a **új alkalmazás** panelen, de a **alkalmazásazonosító** mezőben le van tiltva. Úgy, ahogy az új alkalmazás, adja meg a **verzió** az új csomag esetében keresse meg a **alkalmazáscsomag** .zip fájlt, majd kattintson az **OK** a csomag feltöltése.

### <a name="update-or-delete-an-application-package"></a>Frissítés vagy törlés alkalmazáscsomag
Frissítés, vagy törölje a meglévő alkalmazáscsomag, nyissa meg a az alkalmazás részleteit megjelenítő panelen, kattintson **csomagok** megnyitásához a **csomagok** panelen kattintson a **három pont** a a sor az alkalmazáscsomag, amelyet szeretne módosítani, és jelölje ki a végrehajtani kívánt műveletet.

![Frissítés vagy törlés csomag Azure-portálon][7]

**Update**

Amikor rákattint **frissítés**, a *csomag* panel jelenik meg. Ezen a panelen hasonlít a *új alkalmazáscsomag* panelen, azonban csak a csomag kiválasztási mezőben engedélyezve van, lehetővé téve adja meg az új ZIP-fájl feltöltése.

![Frissítési csomag panel az Azure-portálon][11]

**Törlés**

Amikor rákattint **törlése**, a rendszer felkéri a csomag verziószáma törlésének megerősítése és kötegelt törli a csomag az Azure Storage-ból. Ha törli egy alkalmazás alapértelmezett verziója a **alapértelmezett verzió** beállítást a rendszer eltávolítja az alkalmazáshoz.

![Alkalmazás törlése][12]

## <a name="install-applications-on-compute-nodes"></a>Alkalmazások telepítése a számítási csomópontok
Most, hogy megismerte az Azure-portálon az alkalmazáscsomagok kezelése, hogyan telepheti őket a számítási csomópontok és kötegelt feladatok futtathatók is tárgyaljuk.

### <a name="install-pool-application-packages"></a>Készlet alkalmazáscsomagok telepítése
Egy alkalmazás telepítéséhez minden számítási csomóponton a készletben, adjon meg egy vagy több alkalmazáscsomagot *hivatkozások* a készlet. Az alkalmazáscsomagok számára egy készlet megadott minden számítási csomóponton települnek, amikor a csomópont csatlakozik a készlethez, és amikor a csomópont újraindítása után, vagy lemezképet.

A Batch .NET, adjon meg egy vagy több [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] amikor létrehoz egy új készletet, vagy egy meglévő készlet. A [ApplicationPackageReference] [ net_pkgref] osztály megadja egy alkalmazás-Azonosítót és verziót telepíteni a készlet számítási csomópontjain.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

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
> Ha egy alkalmazás csomag központi telepítését a bármilyen okból nem sikerül, a Batch szolgáltatás jelöli meg a csomópont [használhatatlanná][net_nodestate], és nincsenek feladatok vannak ütemezve ezen a csomóponton. Ebben az esetben kell **indítsa újra a** a központi csomagtelepítés újraindítani a csomópontot. A csomópont újraindítása is lehetővé teszi, hogy a feladatütemezés ismét a csomóponton.
> 
> 

### <a name="install-task-application-packages"></a>A feladat alkalmazáscsomagok telepítése
Hasonló a készlethez, megadott alkalmazáscsomag *hivatkozások* feladathoz. Egy feladat ütemezése egy csomópontján futtatni, a csomag letöltése és kibontása csak a parancssor a feladat végrehajtása előtt. Ha a megadott csomag és verziója már telepítve van a csomópont, a csomag letöltése nem történik meg, és a meglévő csomag használata.

A feladat alkalmazáscsomag telepítéséhez úgy állítsa be a feladatnak [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] tulajdonság:

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

## <a name="execute-the-installed-applications"></a>A telepített alkalmazások végrehajtása
A csomagok egy készletet vagy feladathoz megadott letöltődnek és elnevezett címtárhoz az kibontotta a `AZ_BATCH_ROOT_DIR` a csomópont. Kötegelt is létrehoz egy környezeti változó, amely tartalmazza az elnevezett könyvtár elérési útját. A feladat parancssorokat e környezeti változó használatával való hivatkozáskor az alkalmazás a csomóponton. 

Windows-csomópont a változó a következő formátumban:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux-csomópont formátuma némileg eltérő. A pontok (.), kötőjelet (-) és a kettős kereszttel (#) vannak egybesimított-e az aláhúzás karaktereket tartalmazhatnak a környezeti változóban. Példa:

```
Linux:
AZ_BATCH_APP_PACKAGE_APPLICATIONID_version
```

`APPLICATIONID`és `version` olyan értékek, amelyek megfelelnek a telepítéshez megadott alkalmazás- és csomag verziója. Például, ha a megadott alkalmazás 2.7-es verzió *keverőgép* telepítendő Windows csomópont, a feladat parancssorokat használja ehhez a környezeti változóhoz a fájlok eléréséhez:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux-csomópont adja meg a környezeti változó a következő formátumban:

```
Linux:
AZ_BATCH_APP_PACKAGE_BLENDER_2_7
``` 

Amikor tölt fel egy alkalmazáscsomagot, megadhatja a központi telepítése a számítási csomópontok alapértelmezett változata. Ha egy alkalmazás alapértelmezett verziót adott meg, a verzió utótag kihagyhatja, ha az alkalmazás hivatkozik. Az alapértelmezett Alkalmazásverzió adhat meg az Azure portálon, az alkalmazások panel, ahogy az [alkalmazások kezelését és feltöltését](#upload-and-manage-applications).

Például, ha az alkalmazás alapértelmezett verziójaként "2.7" beállíthatja *keverőgép*, és a feladatok hivatkozik a következő környezeti változót, majd a Windows-csomópontok 2.7-es verzió hajtja végre:

`AZ_BATCH_APP_PACKAGE_BLENDER`

A következő kódrészlet egy példa a feladat parancssori indító alapértelmezett verzióját jeleníti meg a *keverőgép* alkalmazás:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Lásd: [környezeti beállítások feladatok](batch-api-basics.md#environment-settings-for-tasks) a a [Batch funkcióinak áttekintése](batch-api-basics.md) számítási csomópont környezet beállításaival kapcsolatos további információt.
> 
> 

## <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése
Ha egy meglévő készlet már be van állítva egy alkalmazási csomaggal rendelkező, a készlet új csomagot is megadhat. Ha megadja az új csomag leírását, a készletbe, a következő apply:

* A Batch szolgáltatás telepítheti az újonnan meghatározott csomag, az összes olyan új csomópont, amelyhez csatlakozni a készlet, illetve bármely létező csomópontján újraindították vagy lemezképet.
* A számítási csomópontot, amely már a készletben található csomaghivatkozásokhoz frissítésekor nem telepíti automatikusan az új alkalmazáscsomagot. Ezek a számítási csomópont újraindítása után kell lenniük, vagy az új csomag fogadásához lemezképet.
* Amikor új csomagot telepít, a létrehozott környezeti változók tükrözze az új alkalmazás csomaghivatkozásokhoz.

Ebben a példában a meglévő készlet 2.7-es verziójával rendelkezik a *keverőgép* egyik konfigurált alkalmazás a [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. A készlet csomópontok frissítése 2.76b verziójával, adjon meg egy új [ApplicationPackageReference] [ net_pkgref] új verziója, és véglegesítse a módosítás.

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

Most, hogy az új verzióra van beállítva, a Batch szolgáltatás 2.76b verziót telepíti, sem *új* csomópont, amelyhez csatlakozik a készlethez. 2.76b a csomópontokon telepítendő *már* a tárolókészletben, indítsa újra, vagy újból lemezképet létrehozni őket. Vegye figyelembe, hogy újraindított csomópontok tartsa meg a fájlok korábbi csomag telepítések.

## <a name="list-the-applications-in-a-batch-account"></a>A Batch-fiók alkalmazások felsorolása
Az alkalmazások és a csomagok Batch-fiók is listázhatja használatával a [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] metódust.

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

## <a name="wrap-up"></a>Burkolja
Az alkalmazáscsomagok válassza ki arra, hogy az alkalmazásokat, és adja meg a pontos verziót kell használni, ha engedélyezve van a szolgáltatással a feladatok feldolgozásában, az ügyfelek segítségével. Meg lehet adni az feltöltése és nyomon követheti a szolgáltatás fel saját alkalmazásaikba az ügyfelek képesek.

## <a name="next-steps"></a>Következő lépések
* A [Batch REST API] [ api_rest] is támogatja az alkalmazás csomagokkal végzett munkához. Például tekintse meg a [applicationPackageReferences] [ rest_add_pool_with_packages] elemében [a készlet hozzáadása partner] [ rest_add_pool] további információkat tudhat meg csomagok telepítése a REST API használatával. Lásd: [alkalmazások] [ rest_applications] alkalmazással kapcsolatos adatok beszerzése a Batch REST API használatával kapcsolatban.
* Megtudhatja, hogyan programozott módon [kezelése az Azure Batch fiókjainak és kvótáinak a Batch Management .NET kódtárral](batch-management-dotnet.md). A [Batch Management .NET kódtárral][api_net_mgmt] könyvtár engedélyezheti a fiók létrehozását és törlését funkciói a kötegelt alkalmazást vagy szolgáltatást.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/en-us/rest/api/batchservice/
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
[2]: ./media/batch-application-packages/app_pkg_02.png "Alkalmazások csempe az Azure portálon"
[3]: ./media/batch-application-packages/app_pkg_03.png "Alkalmazások panel az Azure-portálon"
[4]: ./media/batch-application-packages/app_pkg_04.png "Alkalmazás részletei panel az Azure-portálon"
[5]: ./media/batch-application-packages/app_pkg_05.png "Új alkalmazás panel az Azure-portálon"
[7]: ./media/batch-application-packages/app_pkg_07.png "Frissítés vagy törlés csomagok legördülő Azure-portálon"
[8]: ./media/batch-application-packages/app_pkg_08.png "Új alkalmazás csomag panel az Azure-portálon"
[9]: ./media/batch-application-packages/app_pkg_09.png "Csatolt tárolási fiók riasztás"
[10]: ./media/batch-application-packages/app_pkg_10.png "Storage-fiók panelen válassza az Azure-portálon"
[11]: ./media/batch-application-packages/app_pkg_11.png "Frissítési csomag panel az Azure-portálon"
[12]: ./media/batch-application-packages/app_pkg_12.png "Törlési megerősítés csomag Azure-portálon"
