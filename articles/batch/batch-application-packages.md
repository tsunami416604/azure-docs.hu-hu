---
title: Alkalmazáscsomag telepítése számítási csomópontokon – Azure Batch | Microsoft Docs
description: A Azure Batch alkalmazáscsomag funkciójának használatával egyszerűen kezelhet több alkalmazást és verziót a Batch számítási csomópontokon történő telepítéshez.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30301832381bdc7b5f001eec2c449c571f9fd671
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086225"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Alkalmazások üzembe helyezése számítási csomópontokhoz batch-alkalmazási csomagokkal

A Azure Batch alkalmazáscsomag szolgáltatásával egyszerűen kezelhető a feladatok alkalmazásai és azok üzembe helyezésük a készlet számítási csomópontjain. Az alkalmazáscsomag használatával a tevékenységek által futtatott alkalmazások több verzióját is feltöltheti és kezelheti, beleértve azok támogató fájljait is. Ezután automatikusan üzembe helyezhet egy vagy több alkalmazást a készlet számítási csomópontjain.

Ebből a cikkből megtudhatja, hogyan tölthet fel és kezelhet alkalmazás-csomagokat a Azure Portalban. Ezután megtudhatja, hogyan telepítheti őket a készlet számítási csomópontjain a [Batch .net][api_net] -kódtár használatával.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat.
>
> Az alkalmazáscsomag létrehozásának és kezelésének API-jai a [Batch Management .net][api_net_mgmt] könyvtár részét képezik. Az alkalmazáscsomag számítási csomópontra történő telepítésének API-jai a [Batch .net][api_net] -könyvtár részei. Az összehasonlítható funkciók a más nyelvekhez elérhető batch API-k. 
>
> Az itt leírt alkalmazáscsomag-szolgáltatás felülbírálja a szolgáltatás korábbi verzióiban elérhető batch Apps szolgáltatást.

## <a name="application-package-requirements"></a>Alkalmazáscsomag követelményei
Az alkalmazáscsomag használatához [egy Azure Storage-fiókot kell összekapcsolnia](#link-a-storage-account) a Batch-fiókkal.

## <a name="about-applications-and-application-packages"></a>Tudnivalók az alkalmazásokról és az alkalmazási csomagokról
Azure Batchon belül egy *alkalmazás* olyan verziószámú bináris fájlokra hivatkozik, amelyek automatikusan letöltődnek a készlet számítási csomópontjaira. Az *alkalmazáscsomag* a bináris fájlok egy adott *készletére* hivatkozik, és az alkalmazás egy adott *verzióját* jelöli.

![Alkalmazások és alkalmazáscsomag magas szintű diagramja][1]

### <a name="applications"></a>Alkalmazások
A Batch alkalmazásban egy vagy több alkalmazáscsomag található, és megadhatja az alkalmazás konfigurációs beállításait. Egy alkalmazás például megadhatja az alkalmazáscsomag alapértelmezett verzióját a számítási csomópontokon való telepítéshez, valamint azt is, hogy a csomagok frissíthetők vagy törölhetők.

### <a name="application-packages"></a>Alkalmazáscsomagok
Az alkalmazáscsomag egy. zip fájl, amely tartalmazza az alkalmazás futtatásához szükséges bináris fájlokat és a hozzájuk kapcsolódó fájlokat. Minden alkalmazáscsomag az alkalmazás egy adott verzióját jelöli.

Az alkalmazás csomagjait a készlet és a feladat szintjein is megadhatja. Készlet vagy feladat létrehozásakor megadhat egy vagy több ilyen csomagot és (opcionálisan) egy verziót.

* A **készlet alkalmazáscsomag** a készlet *minden* csomópontjára telepítve van. Az alkalmazások akkor lesznek telepítve, amikor egy csomópont egy készlethez csatlakozik, és újraindul, vagy alaphelyzetbe áll.
  
    A készlet alkalmazáscsomag akkor megfelelő, ha a készletben lévő összes csomópont végrehajt egy feladat feladatait. Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomag-csomagot, és hozzáadhatja vagy frissítheti a meglévő készlet csomagjait is. Ha frissíti egy meglévő készlet alkalmazáscsomag-csomagjait, újra kell indítania a csomópontokat az új csomag telepítéséhez.
* A **Task Application-csomagok** központi telepítése csak olyan számítási csomópontra történik, amely egy feladat futtatására van ütemezve, közvetlenül a Feladat parancssorának futtatása előtt. Ha a megadott alkalmazáscsomag és verzió már szerepel a csomóponton, a rendszer nem telepíti újra, és a meglévő csomagot használja.
  
    A feladat-alkalmazás csomagjai közös készletű környezetekben hasznosak, ahol a különböző feladatok egy készleten futnak, és a készlet nem törlődik a feladat befejezésekor. Ha a feladatnál a készletben kevesebb a tevékenység, mint a csomópont, az alkalmazáscsomagok használatával csökkentheti az adatátviteli igényt, mivel így a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyek ténylegesen futtatják a tevékenységeket.
  
    Más forgatókönyvek, amelyek kihasználhatják a feladat-alkalmazási csomagokat, olyan feladatok, amelyek nagy alkalmazást futtatnak, de csak néhány feladathoz. Például egy előfeldolgozási szakasz vagy egy egyesítési feladat, ahol az előfeldolgozási vagy az egyesítési alkalmazás a nehézsúlyú, hasznos lehet a Task Application-csomagok használata.

> [!IMPORTANT]
> A Batch-fiókokban és az alkalmazáscsomag maximális méretén belül korlátozások vonatkoznak az alkalmazások és az alkalmazási csomagok számára. A korlátokkal kapcsolatos részletekért tekintse [meg a Azure batch szolgáltatás kvótáit és korlátait](batch-quota-limit.md) .
> 
> 

### <a name="benefits-of-application-packages"></a>Az alkalmazáscsomag előnyei
Az alkalmazáscsomag leegyszerűsítheti a kódot a Batch-megoldásban, és csökkentheti a tevékenységek által futtatott alkalmazások kezeléséhez szükséges terhelést.

Az alkalmazáscsomag esetében a készlet indítási feladatának nem kell megadnia a csomópontokon telepítendő egyes erőforrás-fájlok hosszú listáját. Az alkalmazás fájljainak több verzióját sem kell manuálisan kezelnie az Azure Storage-ban vagy a csomópontjain. Emellett nem kell aggódnia a [sas URL-címeinek](../storage/common/storage-dotnet-shared-access-signature-part-1.md) létrehozásával, hogy hozzáférést biztosítson a Storage-fiókban található fájlokhoz. A Batch az Azure Storage háttérben működik az alkalmazáscsomag tárolásához és a számítási csomópontokon való üzembe helyezéséhez.

> [!NOTE] 
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. Ha az indítási tevékenység meghaladja ezt a korlátot, az alkalmazáscsomag használata egy másik lehetőség. Létrehozhatja az erőforrás-fájlokat tartalmazó tömörített archívumot is, feltöltheti blobként az Azure Storage-ba, majd kicsomagolhatja az indítási feladat parancssorából. 
>
>

## <a name="upload-and-manage-applications"></a>Alkalmazások feltöltése és kezelése
A Batch-fiókban lévő alkalmazáscsomag kezeléséhez használhatja a [Azure Portal][portal] vagy a Batch Management API-kat. A következő néhány szakaszban először bemutatjuk, hogyan lehet egy Storage-fiókot összekapcsolni, majd megbeszélni az alkalmazások és csomagok hozzáadását és a portálon való felügyeletét.

### <a name="link-a-storage-account"></a>Storage-fiók csatolása
Az alkalmazáscsomag használatához először egy [Azure Storage-fiókot](batch-api-basics.md#azure-storage-account) kell összekapcsolnia a Batch-fiókkal. Ha még nem konfigurálta a Storage-fiókot, a Azure Portal figyelmeztetést jelenít meg, amikor először rákattint az **alkalmazások** elemre a Batch-fiókban.



!["Nincs konfigurált Storage-fiók" figyelmeztetés Azure Portal][9]

A Batch szolgáltatás a társított Storage-fiókot használja az alkalmazáscsomag tárolásához. A két fiók összekapcsolása után a Batch automatikusan üzembe helyezheti a társított Storage-fiókban tárolt csomagokat a számítási csomópontokon. Ha egy Storage-fiókot szeretne csatolni a Batch-fiókhoz, akkor a **Figyelmeztetési** ablakban kattintson a **Storage-fiók** elemre, majd kattintson ismét a **Storage-fiók** lehetőségre.

![Válassza a Storage-fiók panelt Azure Portal][10]

Javasoljuk, hogy hozzon létre egy Storage-fiókot *kifejezetten* a Batch-fiókjával való használatra, és válassza ki itt. Miután létrehozta a Storage-fiókot, a **Storage-fiók** ablakban csatolhatja azt a Batch-fiókjához.

> [!IMPORTANT] 
> - Jelenleg nem használhat olyan Azure Storage-fiókkal rendelkező alkalmazáscsomag-csomagokat, amely [Tűzfalszabályok](../storage/common/storage-network-security.md)használatára van konfigurálva.
> - A **hierarchikus névtérrel** **rendelkező Azure Storage-fiókok** nem használhatók alkalmazás-csomagokhoz.

A Batch szolgáltatás az Azure Storage-t használja az alkalmazáscsomag blokkolási blobként való tárolására. A blob-adatblokkok esetében a [szokásos módon kell fizetni][storage_pricing] , és az egyes csomagok mérete nem haladhatja meg a Blobok maximális méretét. További információ: [Azure Storage skálázhatósági és teljesítménybeli célok a Storage-fiókokhoz](../storage/blobs/scalability-targets.md). Ügyeljen rá, hogy az alkalmazáscsomag mérete és száma, valamint a költségek csökkentése érdekében rendszeresen távolítsa el az elavult csomagokat.

### <a name="view-current-applications"></a>Aktuális alkalmazások megtekintése
Ha a Batch-fiókban szeretné megtekinteni az alkalmazásokat, kattintson a bal oldali menü **alkalmazások** menüjére, miközben megtekinti a **Batch-fiókot**.

![Alkalmazások csempe][2]

A menüpont kiválasztásával megnyílik az **alkalmazások** ablak:

![Alkalmazások listázása][3]

Ez az ablak a fiókban lévő egyes alkalmazások AZONOSÍTÓját, valamint a következő tulajdonságokat jeleníti meg:

* **Csomagok**: az alkalmazáshoz társított verziók száma.
* **Alapértelmezett verzió**: az alkalmazás verziója telepítve van, ha nem jelöli meg a készlethez tartozó alkalmazás megadásakor használt verziót. Ez a beállítás nem kötelező.
* **Frissítések engedélyezése**: az érték, amely megadja, hogy a csomagok frissítései, törlése és kiegészítései engedélyezettek-e. Ha ez a **nem**értékre van állítva, a csomagok frissítései és törlése le van tiltva az alkalmazásban. Csak új alkalmazáscsomag-verziók vehetők fel. Az alapértelmezett érték az **Igen**.

Ha szeretné megtekinteni az alkalmazáscsomag felépítését a számítási csomóponton, keresse meg a Batch-fiókját a portálon. A Batch-fiókban navigáljon a **készletek**elemre. Válassza ki azt a készletet, amely a kívánt számítási csomópont (oka) t tartalmazza.

![Készlet csomópontjai][13]

Miután kiválasztotta a készletet, navigáljon ahhoz a számítási csomóponthoz, amelyre az alkalmazáscsomag telepítve van. Innen az alkalmazáscsomag adatai az **alkalmazások** mappában találhatók. A számítási csomópont további mappái más fájlokat is tartalmazhatnak, például indítási feladatokat, kimeneti fájlokat, hibaüzeneteket stb.

![Csomóponton lévő fájlok][14]

### <a name="view-application-details"></a>Alkalmazás részleteinek megtekintése
Egy alkalmazás részleteinek megtekintéséhez válassza ki az alkalmazást az **alkalmazások** ablakban.

![Alkalmazás részletei][4]

Az alkalmazás részleteiben a következő beállításokat állíthatja be az alkalmazáshoz.

* **Frissítések engedélyezése**: megadhatja, hogy az alkalmazás csomagjai frissíthetők vagy törölhetők. A cikk későbbi részében tekintse meg a "alkalmazáscsomag frissítése vagy törlése" című részt.
* **Alapértelmezett verzió**: a számítási csomópontokra telepítendő alapértelmezett alkalmazáscsomag meghatározása.
* **Megjelenítendő név**: adjon meg egy rövid nevet, amelyet a Batch-megoldás használhat az alkalmazással kapcsolatos információk megjelenítéséhez, például egy olyan szolgáltatás felhasználói felületén, amelyet az ügyfeleknek a Batch használatával biztosít.

### <a name="add-a-new-application"></a>Új alkalmazás hozzáadása
Új alkalmazás létrehozásához adjon hozzá egy alkalmazáscsomag-csomagot, és adjon meg egy új, egyedi alkalmazás-azonosítót. Az új alkalmazás-AZONOSÍTÓval hozzáadott első alkalmazáscsomag szintén létrehozza az új alkalmazást.

Kattintson az **Alkalmazások** > **Hozzáadás** elemre.

![Új alkalmazás panel a Azure Portal][5]

Az **új alkalmazás** ablak a következő mezőket biztosítja az új alkalmazás-és alkalmazáscsomag beállításainak megadásához.

**Alkalmazás azonosítója**

Ez a mező adja meg az új alkalmazás AZONOSÍTÓját, amelyre a szabványos Azure Batch azonosító érvényesítési szabályok vonatkoznak. Az alkalmazás-AZONOSÍTÓk nyújtásának szabályai a következők:

* Windows-csomópontokon az azonosító alfanumerikus karakterek, kötőjelek és aláhúzások tetszőleges kombinációjával rendelkezhet. Linux-csomópontokon csak alfanumerikus karakterek és aláhúzások engedélyezettek.
* Legfeljebb 64 karakterből állhat.
* A Batch-fiókon belül egyedinek kell lennie.
* A kis-és nagybetűk megkülönböztetésének megőrzése és a kis-és nagybetűk megkülönböztetése.

**Verzió**

Ez a mező adja meg a feltöltött alkalmazáscsomag verzióját. A verzió sztringje a következő érvényesítési szabályok hatálya alá tartozik:

* Windows-csomópontokon a Version sztring alfanumerikus karakterek, kötőjelek, aláhúzások és időszakok tetszőleges kombinációját tartalmazhatja. Linux-csomópontokon a verzió sztringje csak alfanumerikus karaktereket és aláhúzást tartalmazhat.
* Legfeljebb 64 karakterből állhat.
* Egyedinek kell lennie az alkalmazáson belül.
* A kis-és nagybetűk megkülönböztetésének megtartása és kis-és nagybetűk megkülönböztetése.

**Alkalmazáscsomag**

Ez a mező azt a. zip-fájlt adja meg, amely tartalmazza az alkalmazás végrehajtásához szükséges bináris alkalmazásokat és az azokat támogató fájlokat. Kattintson a **fájl kiválasztása** vagy a mappa ikonra, és válassza ki az alkalmazás fájljait tartalmazó. zip-fájlt.

Miután kiválasztott egy fájlt, kattintson **az OK** gombra az Azure Storage-ba való feltöltés megkezdéséhez. Ha a feltöltési művelet befejeződött, a portál megjelenít egy értesítést. A feltöltött fájl méretétől és a hálózati kapcsolatok sebességétől függően ez a művelet hosszabb időt is igénybe vehet.

> [!WARNING]
> Ne zárjuk be az **új alkalmazás** ablakot a feltöltési művelet befejezése előtt. Ezzel leállítja a feltöltési folyamatot.
> 
> 

### <a name="add-a-new-application-package"></a>Új alkalmazáscsomag hozzáadása
Egy meglévő alkalmazás alkalmazáscsomag-verziójának hozzáadásához válasszon ki egy alkalmazást az **alkalmazások** Windowsban, és kattintson a **csomagok** > **Hozzáadás**gombra.

![Alkalmazáscsomag hozzáadása panel Azure Portal][8]

Amint láthatja, a mezők megegyeznek az **új alkalmazás** ablakával, de az **alkalmazás azonosítója** mező le van tiltva. Ahogy az új alkalmazás esetében, adja meg az új csomag **verzióját** , keresse meg az **alkalmazáscsomag** . zip fájlt, majd kattintson **az OK** gombra a csomag feltöltéséhez.

### <a name="update-or-delete-an-application-package"></a>Alkalmazáscsomag frissítése vagy törlése
Meglévő alkalmazáscsomag frissítéséhez vagy törléséhez nyissa meg az alkalmazás részleteit, kattintson a **csomagok**lehetőségre, kattintson a módosítani kívánt alkalmazáscsomag sorában található **három** pontra, majd válassza ki a végrehajtani kívánt műveletet.

![Csomag frissítése vagy törlése Azure Portal][7]

**Update**

Ha a **frissítés**gombra kattint, megjelenik a **frissítési csomag** ablak. Ez az ablak hasonló az **új alkalmazáscsomag** ablakhoz, azonban csak a csomag kiválasztása mező van engedélyezve, így megadhat egy új zip-fájlt a feltöltéshez.

![Frissítési csomag panelje Azure Portal][11]

**Törlés**

Ha a **Törlés**gombra kattint, a rendszer megkéri, hogy erősítse meg a csomag verziójának törlését, és a Batch törli a csomagot az Azure Storage-ból. Ha törli az alkalmazás alapértelmezett verzióját, a rendszer eltávolítja az **alapértelmezett verzió** beállítását az alkalmazáshoz.

![Alkalmazás törlése][12]

## <a name="install-applications-on-compute-nodes"></a>Alkalmazások telepítése számítási csomópontokon
Most, hogy megismerte, hogyan kezelheti az alkalmazás-csomagokat a Azure Portalkal, megtudhatja, hogyan helyezheti üzembe őket a számítási csomópontokon, és hogyan futtathatja azokat batch-feladatokkal.

### <a name="install-pool-application-packages"></a>Készlet alkalmazáscsomag telepítése
Egy adott készlet összes számítási csomópontján lévő alkalmazáscsomag telepítéséhez válasszon egy vagy több alkalmazáscsomag- *referenciát* a készlethez. A készlethez megadott alkalmazáscsomag minden számítási csomópontra telepítve van, amikor a csomópont csatlakozik a készlethez, és a csomópont újraindítása vagy rendszerképbe állítása történik.

A Batch .NET-ben válasszon ki egy vagy több [CloudPool][net_cloudpool]. Új készlet létrehozásakor vagy egy meglévő készlet [ApplicationPackageReferences][net_cloudpool_pkgref] . A [ApplicationPackageReference][net_pkgref] osztály a készlet számítási csomópontjain telepítendő alkalmazás-azonosítót és verziót határozza meg.

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
> Ha egy alkalmazáscsomag üzembe helyezése bármilyen okból meghiúsul, a Batch szolgáltatás [használhatatlanként][net_nodestate]jelöli meg a csomópontot, és a csomóponton nem ütemezhető feladatok végrehajtásra. Ebben az esetben a csomag központi telepítésének újraindításához **újra kell indítania** a csomópontot. A csomópont újraindítása azt is lehetővé teszi, hogy a feladatütemezés ismét elérhető legyen a csomóponton.
> 
> 

### <a name="install-task-application-packages"></a>Task Application-csomagok telepítése
A készlethez hasonlóan a tevékenységhez tartozó alkalmazáscsomag- *referenciák* is megadhatók. Ha egy adott tevékenység egy csomóponton való futásra van ütemezve, a csomag letöltése és kibontása közvetlenül a Feladat parancssorának végrehajtása előtt történik. Ha egy adott csomag és verzió már telepítve van a csomóponton, a csomag nem töltődik le, és a rendszer a meglévő csomagot használja.

A Task alkalmazáscsomag telepítéséhez konfigurálja a feladat [CloudTask][net_cloudtask]. [ApplicationPackageReferences][net_cloudtask_pkgref] tulajdonság:

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
A készlethez vagy feladathoz megadott csomagokat a rendszer letölti és kicsomagolja egy elnevezett könyvtárba a csomópont `AZ_BATCH_ROOT_DIR`ján belül. A Batch egy környezeti változót is létrehoz, amely a nevesített könyvtár elérési útját tartalmazza. A feladat parancssori sorai ezt a környezeti változót használják a csomóponton lévő alkalmazásra való hivatkozáskor. 

Windows-csomópontokon a változó formátuma a következő:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux-csomópontokon a formátum némileg eltérő. A pont (.), a kötőjel (-) és a Number Signs (#) a környezeti változóban található aláhúzásra van leképezve. Azt is vegye figyelembe, hogy a rendszer megőrzi az alkalmazás AZONOSÍTÓjának esetét. Például:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` és `version` olyan értékek, amelyek megfelelnek az üzembe helyezéshez megadott alkalmazás-és csomag-verziónak. Ha például az alkalmazás- *turmixgép* 2,7-es verzióját kell telepíteni Windows-csomópontokon, a feladat parancssora ezt a környezeti változót fogja használni a fájlokhoz való hozzáféréshez:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux-csomópontokon ebben a formátumban válassza a környezeti változót. A pontok (.), kötőjelek (-) és a számok jeleinek (#) lelapulása és az alkalmazás AZONOSÍTÓjának megőrzése:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Alkalmazáscsomag feltöltésekor megadhat egy alapértelmezett verziót a számítási csomópontokon való üzembe helyezéshez. Ha egy alkalmazáshoz alapértelmezett verziót adott meg, akkor kihagyhatja a verzió utótagját az alkalmazásra való hivatkozáskor. Megadhatja az alkalmazás alapértelmezett verzióját a Azure Portalban az **alkalmazások** ablakban, ahogyan az [alkalmazások feltöltése és kezelése](#upload-and-manage-applications)című részben látható.

Ha például a "2,7" értéket állítja be alapértelmezettként az Application *Blender*számára, és a feladatok a következő környezeti változóra hivatkoznak, akkor a Windows-csomópontok a 2,7-es verziót fogják végrehajtani:

`AZ_BATCH_APP_PACKAGE_BLENDER`

A következő kódrészlet egy példát mutat be, amely elindítja a *Blender* alkalmazás alapértelmezett verzióját:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> A számítási csomópontok környezeti beállításaival kapcsolatos további információkért lásd: [környezeti beállítások](batch-api-basics.md#environment-settings-for-tasks) a [Batch funkcióinak áttekintésében](batch-api-basics.md) .
> 
> 

## <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése
Ha egy meglévő készlet már konfigurálva van egy alkalmazáscsomag használatával, megadhat egy új csomagot a készlethez. Ha egy készlethez új csomag-referenciát ad meg, a következők érvényesek:

* A Batch szolgáltatás telepíti az újonnan megadott csomagot az összes olyan új csomóponton, amely csatlakozik a készlethez, illetve az újraindított vagy rendszerképbe telepített meglévő csomópontokon.
* A csomagok hivatkozásainak frissítésekor a készletben már szereplő számítási csomópontok nem telepítik automatikusan az új alkalmazáscsomag-csomagot. Ezeket a számítási csomópontokat újra kell indítani vagy rendszerképbe kell állítani az új csomag fogadásához.
* Új csomag telepítésekor a létrehozott környezeti változók az új alkalmazáscsomag-hivatkozásokat tükrözik.

Ebben a példában a meglévő készlet a [CloudPool][net_cloudpool]egyike konfigurált *Blender* -alkalmazás 2,7-es verziójával rendelkezik. [ApplicationPackageReferences][net_cloudpool_pkgref]. Ha frissíteni szeretné a készlet csomópontjait a b verziójú 2.76, egy új [ApplicationPackageReference][net_pkgref] kell megadnia az új verzióval, és véglegesíteni kell a változást.

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

Most, hogy az új verzió konfigurálva lett, a Batch szolgáltatás telepíti a 2.76-es verziót a készlethez csatlakozó *új* csomópontra. Ha a 2.76 b-t a készletben *már* szereplő csomópontokon szeretné telepíteni, indítsa újra vagy rendszerképbe. Vegye figyelembe, hogy az újraindított csomópontok megőrzik a korábbi csomagok központi telepítésének fájljait.

## <a name="list-the-applications-in-a-batch-account"></a>Batch-fiókban lévő alkalmazások listázása
Az alkalmazásokat és azok csomagjait a [ApplicationOperations][net_appops]használatával listázhatja egy batch-fiókban. [ListApplicationSummaries][net_appops_listappsummaries] metódus.

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

## <a name="wrap-up"></a>Becsomagolás
Az alkalmazáscsomag segítségével az ügyfelek számára kiválaszthatja a feladataikat, és megadhatja a pontos verziót, amelyet a feladatok a Batch-kompatibilis szolgáltatással végzett feldolgozásakor kell használni. Lehetősége van arra is, hogy az ügyfelek feltöltsék és nyomon kövessék saját alkalmazásaikat a szolgáltatásban.

## <a name="next-steps"></a>Következő lépések
* A [Batch REST API][api_rest] Emellett támogatást biztosít az alkalmazáscsomag működéséhez. Például tekintse meg a [készlet hozzáadása egy fiókhoz][rest_add_pool] című témakör [applicationPackageReferences][rest_add_pool_with_packages] elemét, amely arról nyújt tájékoztatást, hogyan kell megadnia a telepítendő csomagokat a REST API használatával. Az alkalmazás adatainak a Batch REST API használatával történő beszerzésével kapcsolatos további információkért lásd: [alkalmazások][rest_applications] .
* Ismerje meg, hogyan [felügyelheti Azure batch fiókokat és kvótákat a Batch Management .net-](batch-management-dotnet.md)tel. A [Batch Management .net][api_net_mgmt] -függvénytár lehetővé teheti a fiókok létrehozási és törlési funkcióit a Batch-alkalmazáshoz vagy-szolgáltatáshoz.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Alkalmazás-csomagok magas szintű diagramja"
[2]: ./media/batch-application-packages/app_pkg_02.png "Alkalmazások csempe Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Alkalmazások panel Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Az alkalmazás részletei panel Azure Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Új alkalmazás panel a Azure Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "A csomagok frissítése vagy törlése legördülő lista Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Új alkalmazáscsomag panel Azure Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nincs kapcsolódó Storage-fiókra vonatkozó riasztás"
[10]: ./media/batch-application-packages/app_pkg_10.png "Válassza a Storage-fiók panelt Azure Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Frissítési csomag panelje Azure Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "A csomag megerősítő párbeszédpanelének törlése Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Számítási csomópontok adatai Azure Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "A Azure Portalban megjelenő számítási csomóponton lévő fájlok"
