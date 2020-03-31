---
title: Alkalmazáscsomagok telepítése számítási csomópontokra - Azure Batch | Microsoft dokumentumok
description: Az Azure Batch alkalmazáscsomagok szolgáltatásával egyszerűen kezelhet több alkalmazást és verziót a Batch számítási csomópontokon történő telepítéshez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086225"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Alkalmazások üzembe helyezése csomópontok kiszámításához batch alkalmazáscsomagokkal

Az Azure Batch alkalmazáscsomagok funkciója egyszerű felügyeletet biztosít a feladatalkalmazások és azok üzembe helyezését a készlet számítási csomópontjaira. Az alkalmazáscsomagok segítségével feltöltheti és kezelheti a feladatok által futtatott alkalmazások több verzióját, beleértve a támogató fájlokat is. Ezután automatikusan üzembe helyezhet egy vagy több ilyen alkalmazást a készlet számítási csomópontjaira.

Ebben a cikkben megtudhatja, hogyan töltheti fel és kezelheti az alkalmazáscsomagokat az Azure Portalon. Ezután megtudhatja, hogyan telepítheti őket a készlet számítási csomópontjaira a [Batch .NET][api_net] könyvtárral.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat.
>
> Az alkalmazáscsomagok létrehozásához és kezeléséhez szükséges API-k a [Kötegkezelés .NET][api_net_mgmt] függvénytár részét képezik. Az alkalmazáscsomagok számítási csomópontra történő telepítéséhez szükséges API-k a [Batch .NET][api_net] függvénytár részét képezik. A hasonló funkciók a más nyelvekhez elérhető Batch API-kban találhatók. 
>
> Az itt ismertetett alkalmazáscsomagok funkció felülírja a szolgáltatás korábbi verzióiban elérhető Batch Apps funkciót.

## <a name="application-package-requirements"></a>Az alkalmazáscsomag követelményei
Az alkalmazáscsomagok használatához össze kell [kapcsolnia egy Azure Storage-fiókot](#link-a-storage-account) a Batch-fiókkal.

## <a name="about-applications-and-application-packages"></a>Alkalmazások és alkalmazáscsomagok –
Az Azure Batch-en belül egy *alkalmazás* egy verzióval rendelkezik bináris fájlok, amelyek automatikusan letölthetők a számítási csomópontok a készletben. Az *alkalmazáscsomag* a bináris fájlok egy *adott készletére* hivatkozik, és az alkalmazás adott *verzióját* képviseli.

![Alkalmazások és alkalmazáscsomagok magas szintű diagramja][1]

### <a name="applications"></a>Alkalmazások
A Batch alkalmazásban egy alkalmazás egy vagy több alkalmazáscsomagot tartalmaz, és megadja az alkalmazás konfigurációs beállításait. Egy alkalmazás megadhatja például a számítási csomópontokra telepítendő alapértelmezett alkalmazáscsomag-verziót, valamint azt, hogy a csomagok frissíthetők vagy törölhetők-e.

### <a name="application-packages"></a>Alkalmazáscsomagok
Az alkalmazáscsomag egy .zip fájl, amely tartalmazza az alkalmazás bináris fájljait és az alkalmazás futtatásához szükséges segédfájlokat. Minden alkalmazáscsomag az alkalmazás egy adott verzióját képviseli.

Az alkalmazáscsomagokat a készlet és a feladat szintjén adhatja meg. Egy vagy több csomagot megadhat, és (tetszés szerint) egy verziót, amikor készletet vagy feladatot hoz létre.

* **Készlet alkalmazáscsomagok** vannak telepítve a készlet *minden* csomópontjára. Az alkalmazások akkor kerülnek üzembe helyezésre, amikor egy csomópont csatlakozik egy készlethez, és amikor újraindítják vagy újraképezik.
  
    Készlet alkalmazás csomagok megfelelőek, ha a készlet összes csomópontja végrehajtja a feladat feladatait. Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomagot, és hozzáadhat vagy frissíthet egy meglévő készletcsomagját. Ha egy meglévő készlet alkalmazáscsomagjait frissíti, az új csomag telepítéséhez újra kell indítania a csomópontjait.
* **A feladatalkalmazás-csomagok** csak egy feladat futtatására ütemezett számítási csomópontra vannak telepítve, közvetlenül a feladat parancssorának futtatása előtt. Ha a megadott alkalmazáscsomag és verzió már a csomóponton van, a rendszer nem helyezi üzembe újra, és a meglévő csomagot használja.
  
    A feladatalkalmazás-csomagok akkor hasznosak megosztott készletkörnyezetekben, ahol különböző feladatok futnak egy készleten, és a készlet nem törlődik, ha egy feladat befejeződött. Ha a feladatnál a készletben kevesebb a tevékenység, mint a csomópont, az alkalmazáscsomagok használatával csökkentheti az adatátviteli igényt, mivel így a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyek ténylegesen futtatják a tevékenységeket.
  
    A feladatalkalmazás-csomagok egyéb előnyeinek kihasználható a nagy alkalmazásokat futtató, de csak néhány feladatra vonatkozó forgatókönyvek. Például egy előfeldolgozási szakasz vagy egy egyesítési feladat, ahol az előfeldolgozási vagy egyesítési alkalmazás nehézsúlyú, hasznos lehet a feladatalkalmazás-csomagok használata.

> [!IMPORTANT]
> A Batch-fiókon belüli alkalmazások és alkalmazáscsomagok számára és az alkalmazáscsomag maximális méretére vonatkozó korlátozások vannak érvényben. Ezek a korlátok értése: [Kvóták és korlátozások az Azure Batch-szolgáltatáshoz.](batch-quota-limit.md)
> 
> 

### <a name="benefits-of-application-packages"></a>Az alkalmazáscsomagok előnyei
Az alkalmazáscsomagok egyszerűsíthetik a kötegelt megoldás kódját, és csökkenthetik a feladatok által futtatott alkalmazások kezeléséhez szükséges többletterhelést.

Az alkalmazáscsomagok esetén a készlet indítási feladatának nem kell megadnia a csomópontokra telepítandó egyes erőforrásfájlok hosszú listáját. Nem kell manuálisan kezelnie az alkalmazásfájlok több verzióját az Azure Storage-ban, vagy a csomópontokon. És nem kell aggódnia a [SAS URL-ek](../storage/common/storage-dotnet-shared-access-signature-part-1.md) létrehozása, hogy hozzáférést biztosítson a storage-fiókjában lévő fájlokhoz. Batch működik a háttérben az Azure Storage alkalmazáscsomagok tárolására és üzembe helyezésére a számítási csomópontok.

> [!NOTE] 
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. Ha az indítási feladat túllépi ezt a korlátot, akkor az alkalmazáscsomagok használata egy másik lehetőség. Létrehozhat egy tömörített archívumot is, amely tartalmazza az erőforrásfájlokat, feltöltheti az Azure Storage-ba blobként, majd csomagolja ki a kezdő feladat parancssorából. 
>
>

## <a name="upload-and-manage-applications"></a>Alkalmazások feltöltése és kezelése
Az Azure [Portalon][portal] vagy a Batch Management API-k segítségével kezelheti az alkalmazáscsomagokat a Batch-fiókban. A következő néhány szakaszban először bemutatjuk, hogyan kapcsolhat össze egy tárfiókot, majd megbeszélik az alkalmazások és csomagok hozzáadását és a portállal való kezelését.

### <a name="link-a-storage-account"></a>Tárfiók összekapcsolása
Az alkalmazáscsomagok használatához először csatolnia kell egy [Azure Storage-fiókot](batch-api-basics.md#azure-storage-account) a Batch-fiókhoz. Ha még nem konfigurált egy storage-fiókot, az Azure Portalon megjelenik egy figyelmeztetés, amikor először kattint **alkalmazások** a Batch-fiókban.



!["Nincs konfigurálva tárfiók" figyelmeztetés az Azure Portalon][9]

A Batch szolgáltatás a társított Tárfiók segítségével tárolja az alkalmazáscsomagokat. Miután összekapcsolta a két fiókot, a Batch automatikusan telepítheti a csatolt tárfiókban tárolt csomagokat a számítási csomópontokra. Storage-fiók csatolásához a Batch-fiókhoz, kattintson a **Tárfiók** elemre a **Figyelmeztetés** ablakban, majd kattintson ismét **a Tárfiók** elemre.

![A tárfiók panel kiválasztása az Azure Portalon][10]

Azt javasoljuk, hogy hozzon létre egy storage-fiókot *kifejezetten* a Batch-fiókkal való használatra, és itt válassza ki. Miután létrehozott egy tárfiókot, majd összekapcsolhatja azt a Batch-fiókkal a **Tárfiók** ablak használatával.

> [!IMPORTANT] 
> - Jelenleg nem használhatja a [tűzfalszabályokkal](../storage/common/storage-network-security.md)konfigurált Azure Storage-fiókkal rendelkező alkalmazáscsomagokat.
> - Az Azure Storage-fiók **hierarchikus névtér** **beállítása engedélyezve** nem használható alkalmazáscsomagokhoz.

A Batch szolgáltatás az Azure Storage segítségével tárolja az alkalmazáscsomagokat blokkblobként. A blokkblob-adatokért a [szokásos módon számítunk fel díjat,][storage_pricing] és az egyes csomagok mérete nem haladhatja meg a maximális blokkblob-méretet. További információ: [Azure Storage méretezhetőség és teljesítménycélok tárfiókok.](../storage/blobs/scalability-targets.md) Ügyeljen arra, hogy fontolja meg az alkalmazáscsomagok méretét és számát, és rendszeresen távolítsa el az elavult csomagokat a költségek minimalizálása érdekében.

### <a name="view-current-applications"></a>Aktuális alkalmazások megtekintése
A Batch-fiókban lévő alkalmazások megtekintéséhez kattintson a bal oldali menü **Alkalmazások** menüpontjára a **Batch fiók**megtekintése kor .

![Alkalmazások csempe][2]

Ha ezt a menüt választja, megnyílik az **Alkalmazások** ablak:

![Alkalmazások listázása][3]

Ebben az ablakban a fiókban lévő egyes alkalmazások azonosítója és a következő tulajdonságok jelennek meg:

* **Csomagok**: Az alkalmazáshoz társított verziók száma.
* **Alapértelmezett verzió**: Az alkalmazás verziója telepítve van, ha nem jelez egy verziót, amikor megadja az alkalmazást egy készlethez. Ez a beállítás nem kötelező.
* **Frissítések engedélyezése**: Az az érték, amely meghatározza, hogy a csomag frissítések, törlések és kiegészítések engedélyezettek-e. Ha a beállítás **Nem,** a csomagfrissítések és a törlések le vannak tiltva az alkalmazásszámára. Csak új alkalmazáscsomag-verziók adhatók hozzá. Az alapértelmezett **érték**igen .

Ha szeretné látni az alkalmazáscsomag fájlszerkezetét a számítási csomóponton, keresse meg a Batch-fiókot a portálon. A Batch-fiókban keresse meg a **Készletek**lapot. Válassza ki azt a készletet, amely az önt érdeklő számítási csomópont(oka)t tartalmazza.

![Csomópontok a készletben][13]

Miután kiválasztotta a készletet, keresse meg azt a számítási csomópontot, amelyre az alkalmazáscsomag telepítve van. Innen az alkalmazáscsomag részletei az **alkalmazások** mappájában találhatók. A számítási csomópont további mappái más fájlokat is tartalmaznak, például indítási feladatokat, kimeneti fájlokat, hibakimenetet stb.

![Fájlok a csomóponton][14]

### <a name="view-application-details"></a>Alkalmazásrészleteinek megtekintése
Az alkalmazás részleteinek megtekintéséhez jelölje ki az alkalmazást az **Alkalmazások** ablakban.

![Alkalmazás részletei][4]

Az alkalmazás részleteiben a következő beállításokat konfigurálhatja az alkalmazáshoz.

* **Frissítések engedélyezése**: Adja meg, hogy az alkalmazáscsomagok frissíthetők vagy törölhetők-e. Lásd a cikk későbbi, "Alkalmazáscsomag frissítése vagy törlése" című részt.
* **Alapértelmezett verzió:** Adja meg a számítási csomópontokhoz telepítandó alapértelmezett alkalmazáscsomagot.
* **Megjelenítendő név:** Adjon meg egy rövid nevet, amelyet a Batch-megoldás akkor használhat, ha információkat jelenít meg az alkalmazásról, például egy olyan szolgáltatás felhasználói felületén, amelyet a Batch-en keresztül biztosít ügyfeleinek.

### <a name="add-a-new-application"></a>Új alkalmazás hozzáadása
Új alkalmazás létrehozásához adjon hozzá egy alkalmazáscsomagot, és adjon meg egy új, egyedi alkalmazásazonosítót. Az új alkalmazásazonosítóval hozzáadott első alkalmazáscsomag is létrehozza az új alkalmazást.

Kattintson **az Alkalmazások** > **hozzáadása gombra.**

![Új alkalmazáspanel az Azure Portalon][5]

Az **Új alkalmazás** ablak a következő mezőket tartalmazza az új alkalmazás- és alkalmazáscsomag beállításainak megadásához.

**Alkalmazásazonosító**

Ez a mező az új alkalmazás azonosítóját határozza meg, amelyre a szabványos Azure Batch-azonosító érvényesítési szabályok vonatkoznak. A kérelemazonosító megadására vonatkozó szabályok a következők:

* Windows-csomópontokon az azonosító alfanumerikus karakterek, kötőjelek és aláhúzásjelek tetszőleges kombinációját tartalmazhatja. Linux os csomópontokon csak alfanumerikus karakterek és aláhúzásjelek engedélyezettek.
* Legfeljebb 64 karakter lehet.
* A Batch-fiókban egyedinek kell lennie.
* A kis- és nagybetűk megőrzése és a kis- és nagybetűk megkülönböztetése.

**Verzió**

Ez a mező a feltöltendő alkalmazáscsomag verzióját határozza meg. A verziókarakterláncokra a következő érvényességi szabályok vonatkoznak:

* Windows-csomópontokon a verziókarakterlánc alfanumerikus karakterek, kötőjelek, aláhúzásjelek és időszakok tetszőleges kombinációját tartalmazhatja. Linux csomópontokon a verziókarakterlánc csak alfanumerikus karaktereket és aláhúzásjeleket tartalmazhat.
* Legfeljebb 64 karakter lehet.
* Az alkalmazáson belül egyedinek kell lennie.
* A kis- és nagybetűk megőrzése és a kis- és nagybetűk megkülönböztetése nem.

**Alkalmazáscsomag**

Ez a mező az alkalmazás bináris fájljait tartalmazó .zip fájlt és az alkalmazás végrehajtásához szükséges segédfájlokat adja meg. Kattintson a Keresse meg a **Fájl kiválasztása** vagy a mappa ikonra, és jelöljön ki egy .zip fájlt, amely tartalmazza az alkalmazás fájljait.

Miután kijelölt egy fájlt, kattintson az **OK** gombra az Azure Storage-ba való feltöltés megkezdéséhez. Amikor a feltöltési művelet befejeződött, a portál értesítést jelenít meg. A feltöltendő fájl méretétől és a hálózati kapcsolat sebességétől függően ez a művelet eltarthat egy ideig.

> [!WARNING]
> Ne zárja be az **Új alkalmazás** ablakot a feltöltési művelet befejezése előtt. Ezzel leállítja a feltöltési folyamatot.
> 
> 

### <a name="add-a-new-application-package"></a>Új alkalmazáscsomag hozzáadása
Ha egy meglévő alkalmazáshoz szeretne alkalmazáscsomag-verziót hozzáadni, jelöljön ki egy alkalmazást az **Alkalmazások** ablakban, és kattintson a Csomagok hozzáadása **gombra.** > **Add**

![Alkalmazáscsomag-panel hozzáadása az Azure Portalon][8]

Amint láthatja, a mezők megegyeznek az **Új alkalmazás** ablakmezőmezőivel, de az **Alkalmazásazonosító** mező le van tiltva. Az új alkalmazáshoz ugyanúgy adja meg az új csomag **verzióját,** keresse meg az **Alkalmazáscsomag** .zip fájlját, majd kattintson az **OK** gombra a csomag feltöltéséhez.

### <a name="update-or-delete-an-application-package"></a>Alkalmazáscsomag frissítése vagy törlése
Meglévő alkalmazáscsomag frissítéséhez vagy törléséhez nyissa meg az alkalmazás részleteit, kattintson a **Csomagok** **gombra,** kattintson a három pontra az alkalmazáscsomag módosítani kívánt sorában, és jelölje ki a végrehajtani kívánt műveletet.

![Csomag frissítése vagy törlése az Azure Portalon][7]

**Frissítés**

Ha a **Frissítés gombra**kattint, a **Frissítés csomag ablakai** jelennek meg. Ez az ablak hasonló az **Új alkalmazáscsomag** ablakhoz, azonban csak a csomagválasztó mező van engedélyezve, így új ZIP-fájlt adhat meg.

![Csomagpanel frissítése az Azure Portalon][11]

**Szabályzat**

Ha a **Törlés gombra**kattint, a rendszer kéri, hogy erősítse meg a csomagverzió törlését, és a Batch törli a csomagot az Azure Storage-ból. Ha törli egy alkalmazás alapértelmezett verzióját, az **alkalmazás alapértelmezett verzióbeállítása** törlődik.

![Alkalmazás törlése][12]

## <a name="install-applications-on-compute-nodes"></a>Alkalmazások telepítése számítási csomópontokon
Most, hogy megtanulta, hogyan kezelheti az alkalmazáscsomagokat az Azure Portalon, megvitathatjuk, hogyan telepítheti őket a csomópontok számítási és batch-feladatokkal való futtatásához.

### <a name="install-pool-application-packages"></a>Készletalkalmazás-csomagok telepítése
Ha egy alkalmazáscsomagot egy készlet összes számítási csomópontjára szeretne telepíteni, adjon meg egy vagy több *alkalmazáscsomag-hivatkozást* a készlethez. A készlethez megadott alkalmazáscsomagok minden számítási csomópontra telepítve lesznek, amikor az adott csomópont csatlakozik a készlethez, és amikor a csomópont újraindul vagy újraleszleésre kerül.

A Batch .NET csoportban adjon meg egy vagy több [CloudPool t.][net_cloudpool] [ApplicationPackageReferences][net_cloudpool_pkgref] új készlet vagy meglévő készlet létrehozásakor. Az [ApplicationPackageReference][net_pkgref] osztály egy alkalmazásazonosítót és egy készlet számítási csomópontjaira telepítandó verziót határoz meg.

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
> Ha egy alkalmazáscsomag központi telepítése bármilyen okból sikertelen, a Batch szolgáltatás használhatatlannak jelöli a [csomópontot,][net_nodestate]és az adott csomóponton nincsenek végrehajtásra ütemezett feladatok. Ebben az esetben újra kell **indítania** a csomópontot a csomag központi telepítésének újraindításához. A csomópont újraindítása lehetővé teszi a feladatütemezés t is a csomóponton.
> 
> 

### <a name="install-task-application-packages"></a>Feladatalkalmazás-csomagok telepítése
A készlethez hasonlóan *alkalmazáscsomag-hivatkozásokat* is megadhat egy feladathoz. Ha egy feladat egy csomóponton való futtatásra van ütemezve, a csomag letöltése és kibontása közvetlenül a feladat parancssorának végrehajtása előtt történik. Ha egy adott csomag és verzió már telepítve van a csomóponton, a csomag nem töltődik le, és a meglévő csomag használatos.

Feladatalkalmazás-csomag telepítéséhez konfigurálja a feladat [CloudTask .][net_cloudtask] [ApplicationPackageReferences][net_cloudtask_pkgref] tulajdonság:

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
A készlethez vagy feladathoz megadott csomagok letöltése és `AZ_BATCH_ROOT_DIR` kibontása a csomóponton belüli elnevezett könyvtárba történik. A Batch létrehoz egy környezeti változót is, amely tartalmazza az elnevezett könyvtár elérési útját. A feladatparancssorok ezt a környezeti változót használják, amikor az alkalmazásra hivatkoznak a csomóponton. 

Windows-csomópontokon a változó formátuma a következő:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux csomópontokon a formátum kissé eltérő. A pont (.), a kötőjelek (-) és a számjelek (#) a környezeti változóban aláhúzást mutatnak. Vegye figyelembe azt is, hogy az alkalmazásazonosító esete megmarad. Példa:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`és `version` olyan értékek, amelyek megfelelnek a központi telepítéshez megadott alkalmazás- és csomagverziónak. Ha például azt adta meg, hogy az *alkalmazásturmixgép* 2.7-es verzióját windowsos csomópontokra kell telepíteni, a feladatparancssorok ezt a környezeti változót fogják használni a fájlok eléréséhez:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux csomópontokon adja meg a környezeti változót ebben a formátumban. A pont (.), a kötőjelek (-) és a számjelek (#) összeolvasztása aláhúzással, és az alkalmazásazonosító esetének megőrzése:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Amikor feltölt egy alkalmazáscsomagot, megadhatja a számítási csomópontokra telepítendő alapértelmezett verziót. Ha egy alkalmazáshoz alapértelmezett verziót adott meg, az alkalmazásra való hivatkozáskor kihagyhatja a verzióutótagot. Megadhatja az alapértelmezett alkalmazásverziót az Azure Portalon, az **Alkalmazások** ablakban, ahogy az [alkalmazások feltöltése és kezelése](#upload-and-manage-applications)látható.

Ha például a "2.7" beállítást állítja be az *alkalmazáskeverő*alapértelmezett verziójaként, és a feladatok a következő környezeti változóra hivatkoznak, akkor a Windows-csomópontok a 2.7-es verziót hajtják végre:

`AZ_BATCH_APP_PACKAGE_BLENDER`

A következő kódrészlet egy példa feladatparancssort mutat be, amely elindítja a *turmixgép* alkalmazás alapértelmezett verzióját:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> A számítási csomópont környezetbeállításairól a [Gyártási csoport szolgáltatás áttekintése](batch-api-basics.md) című [témakörben](batch-api-basics.md#environment-settings-for-tasks) olvashat.
> 
> 

## <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése
Ha egy meglévő készlet már konfigurálva van egy alkalmazáscsomaggal, megadhat egy új csomagot a készlethez. Ha új csomaghivatkozást ad meg egy készlethez, a következők et kell alkalmazni:

* A Batch szolgáltatás telepíti az újonnan megadott csomagot minden új csomópontra, amely csatlakozik a készlethez, és minden olyan meglévő csomópontra, amely újraindul vagy újraleszleésre kerül.
* A készletben már a csomaghivatkozások frissítésekor lévő számítási csomópontok nem telepítik automatikusan az új alkalmazáscsomagot. Ezeket a számítási csomópontokat újra kell indítani vagy újra kell rendszereznie az új csomag fogadásához.
* Új csomag telepítésekor a létrehozott környezeti változók az új alkalmazáscsomag-hivatkozásokat tükrözik.

Ebben a példában a meglévő készlet 2.7-es verziója a *turmixgép* alkalmazás konfigurálva, mint az egyik [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. A készlet csomópontjainak 2.76b-es verzióval történő frissítéséhez adjon meg egy új [ApplicationPackageReference verziót][net_pkgref] az új verzióval, és véglegesítse a módosítást.

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

Most, hogy az új verzió konfigurálva van, a Batch szolgáltatás telepíti a 2.76b verziót bármely *új* csomópontra, amely csatlakozik a készlethez. A 2.76b telepítéséhez a készletben *lévő* csomópontokra indítsa újra vagy próbálja át őket. Vegye figyelembe, hogy az újraindított csomópontok megőrzik a korábbi csomagközponti telepítésekből származó fájlokat.

## <a name="list-the-applications-in-a-batch-account"></a>Az alkalmazások listázása Batch-fiókban
Az alkalmazásokat és csomagjaikat egy Batch-fiókban listázhatja az [ApplicationOperations][net_appops]segítségével. [ListApplicationSummaries][net_appops_listappsummaries] metódus.

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

## <a name="wrap-up"></a>Letekerés
Az alkalmazáscsomagok segítségével az ügyfelek kiválaszthatják a feladataikhoz készült alkalmazásokat, és megadhatja a feladatok kötegelt szolgáltatással történő feldolgozásakor használandó pontos verziót. Azt is lehetővé teheti az ügyfelek számára, hogy feltöltsék és nyomon kövessék saját alkalmazásaikat a szolgáltatásban.

## <a name="next-steps"></a>További lépések
* A [Batch REST API][api_rest] is támogatja az alkalmazáscsomagok. Például tekintse meg az [applicationPackageReferences][rest_add_pool_with_packages] elem [hozzáadása egy fiókhoz][rest_add_pool] információt arról, hogyan adja meg a telepített csomagok a REST API használatával. [Az alkalmazások][rest_applications] beszerzéséről a Batch REST API használatával kapcsolatos további részletekért tekintse meg az alkalmazások beszerzését.
* Megtudhatja, hogy miként kezelheti programozott módon [az Azure Batch-fiókokat és kvótákat a Batch Management .NET](batch-management-dotnet.md)használatával. A [Kötegkezelés .NET][api_net_mgmt] függvénytár lehetővé teszi a fiók létrehozásának és törlésének szolgáltatásait a Batch alkalmazáshoz vagy szolgáltatáshoz.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Alkalmazáscsomagok magas szintű diagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Alkalmazások csempe az Azure Portalon"
[3]: ./media/batch-application-packages/app_pkg_03.png "Alkalmazások panel az Azure Portalon"
[4]: ./media/batch-application-packages/app_pkg_04.png "Alkalmazásrészletei panel az Azure Portalon"
[5]: ./media/batch-application-packages/app_pkg_05.png "Új alkalmazáspanel az Azure Portalon"
[7]: ./media/batch-application-packages/app_pkg_07.png "Csomagok frissítése vagy törlése legördülő menüben az Azure Portalon"
[8]: ./media/batch-application-packages/app_pkg_08.png "Új alkalmazáscsomag-panel az Azure Portalon"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nincs csatolt tárfiók riasztás"
[10]: ./media/batch-application-packages/app_pkg_10.png "A tárfiók panel kiválasztása az Azure Portalon"
[11]: ./media/batch-application-packages/app_pkg_11.png "Csomagpanel frissítése az Azure Portalon"
[12]: ./media/batch-application-packages/app_pkg_12.png "Csomagvisszaigazolási párbeszédpanel törlése az Azure Portalon"
[13]: ./media/batch-application-packages/package-file-structure.png "A csomópontadatok kiszámítása az Azure Portalon"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Az Azure Portalon megjelenő számítási csomóponton megjelenő fájlok"
