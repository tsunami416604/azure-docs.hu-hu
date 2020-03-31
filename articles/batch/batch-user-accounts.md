---
title: Feladatok futtatása felhasználói fiókok alatt – Azure Batch
description: Hasznos lehet beállítani azt a felhasználói fiókot, amely alatt futtatni szeretné a feladatot. Ismerje meg a felhasználói fiókok típusait és konfigurálásukat.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: fee3dc764d2052185160a4ba6b3d70854c54eeac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252271"
---
> [!NOTE] 
> Az ebben a cikkben tárgyalt felhasználói fiókok biztonsági okokból különböznek a Távoli asztali protokoll (RDP) vagy a Secure Shell (SSH) felhasználói fiókjaitól. 
>
> A Linux virtuálisgép-konfigurációt SSH-n keresztül futtató csomóponthoz való csatlakozásról a [Távoli asztal használata Linux-alapú virtuális géphez az Azure-ban](../virtual-machines/virtual-machines-linux-use-remote-desktop.md)című témakörben található. Ha RDP-n keresztül windowsos csomópontokhoz szeretne csatlakozni, olvassa el [a Csatlakozás Windows Server virtuális géphez (Csatlakozás Windows Server virtuális géphez) (Csatlakozás Windows Server virtuális géphez) témakört.](../virtual-machines/windows/connect-logon.md)<br /><br />
> Ha rdp-n keresztül szeretne csatlakozni egy felhőszolgáltatás-konfigurációt futtató csomóponthoz, olvassa el a [Távoli asztali kapcsolat engedélyezése szerepkörhöz az Azure Cloud Servicesben című témakört.](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Feladatok futtatása a Batch felhasználói fiókok alatt

Az Azure Batch-ben egy feladat mindig egy felhasználói fiók alatt fut. Alapértelmezés szerint a feladatok általános jogú felhasználói fiókok alatt, rendszergazdai engedélyek nélkül futnak. Ezek az alapértelmezett felhasználói fiókbeállítások általában elegendőek. Bizonyos esetekben azonban hasznos lehet beállítani azt a felhasználói fiókot, amely alatt egy feladatot futtatni szeretne. Ez a cikk ismerteti a felhasználói fiókok típusait, és hogyan konfigurálhatja őket a forgatókönyvhöz.

## <a name="types-of-user-accounts"></a>A felhasználói fiókok típusai

Az Azure Batch kétféle felhasználói fiókot biztosít a futó feladatokhoz:

- **Automatikus felhasználói fiókok.** Az automatikus felhasználói fiókok beépített felhasználói fiókok, amelyeket a Batch szolgáltatás automatikusan hoz létre. Alapértelmezés szerint a feladatok automatikus felhasználói fiók alatt futnak. Beállíthatja egy feladat automatikus felhasználói specifikációját annak jelzésére, hogy melyik automatikus felhasználói fiók szerint kell futtatni a feladatot. Az automatikus felhasználó specifikációja lehetővé teszi a feladatot futtatni kívánt automatikus felhasználói fiók magassági szintjének és hatókörének megadását. 

- **Egy elnevezett felhasználói fiók.** A készlet létrehozásakor egy vagy több elnevezett felhasználói fiókot is megadhat egy készlethez. Minden felhasználói fiók a készlet minden csomópontján létrejön. A fióknév mellett meg kell adnia a felhasználói fiók jelszavát, a jogosultságszintet és a Linux-készletek esetében az SSH titkos kulcsot. Feladat hozzáadásakor megadhatja azt a megnevezett felhasználói fiókot, amely alatt a feladatnak futnia kell.

> [!IMPORTANT] 
> A Batch szolgáltatás 2017-01-01.4.0-s verziója olyan törésmódosítást vezet be, amely megköveteli, hogy frissítse a kódot az adott verzió hívásához. Ha a Batch egy régebbi verziójából telepíti át a kódot, vegye figyelembe, hogy a **runElevated** tulajdonság már nem támogatott a REST API- vagy batch-ügyfélkódtárakban. A magasságszint megadásához használja egy feladat új **userIdentity** tulajdonságát. Tekintse meg a Kód [frissítése a legújabb Batch-ügyfélkódra](#update-your-code-to-the-latest-batch-client-library) című szakaszban a Batch-kód frissítésével kapcsolatos gyors irányelveket, ha az ügyfélkódtárak valamelyikét használja.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Felhasználói fiókok hozzáférése fájlokhoz és könyvtárakhoz

Az automatikus felhasználói fiókok és a megnevezett felhasználói fiókok olvasási/írási hozzáféréssel rendelkeznek a feladat munkakönyvtárához, megosztott könyvtárához és többpéldányos feladatkönyvtárához. Mindkét típusú fiókok olvasási hozzáféréssel rendelkeznek az indítási és a feladat-előkészítési könyvtárakhoz.

Ha egy feladat ugyanazon fiók alatt fut, amelyet egy indítási feladat futtatásához használt, a feladat olvasási és írási hozzáféréssel rendelkezik az indítási feladatkönyvtárhoz. Hasonlóképpen, ha egy feladat ugyanazon a fiókon fut, amelyet egy feladat-előkészítési feladat futtatásához használt, a feladat olvasási és írási hozzáféréssel rendelkezik a feladat-előkészítési feladatkönyvtárhoz. Ha egy feladat a kezdési feladattól vagy a feladat-előkészítési feladattól eltérő fiókban fut, akkor a feladat csak olvasási hozzáféréssel rendelkezik a megfelelő könyvtárhoz.

A feladatokból származó fájlok és könyvtárak eléréséről a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md#files-and-directories)című témakörben talál további információt.

## <a name="elevated-access-for-tasks"></a>Emelt szintű hozzáférés a feladatokhoz 

A felhasználói fiók magasságszintje azt jelzi, hogy egy feladat emelt szintű hozzáféréssel fut-e. Az automatikus felhasználói fiókok és a megnevezett felhasználói fiókok is futtathatók emelt szintű hozzáféréssel. A magasságszint két lehetősége a következő:

- **Nem rendszergazda:** A feladat emelt szintű hozzáférés nélküli általános jogú felhasználóként fut. A Batch felhasználói fiókok alapértelmezett magasságszintje mindig **nem admin**.
- **Admin:** A feladat emelt szintű hozzáféréssel rendelkező felhasználóként fut, és teljes rendszergazdai engedélyekkel működik. 

## <a name="auto-user-accounts"></a>Automatikus felhasználói fiókok

Alapértelmezés szerint a feladatok a Batch alkalmazásban futnak automatikus felhasználói fiók alatt, emelt szintű hozzáféréssel nem rendelkező általános jogú felhasználóként és feladathatókörrel. Ha az automatikus felhasználó specifikációja feladathatókörhöz van konfigurálva, a Batch szolgáltatás csak az adott feladathoz hoz létre automatikus felhasználói fiókot.

A feladathatókör alternatívája a készlet hatóköre. Ha egy feladat automatikus felhasználói specifikációja készlethatókörre van konfigurálva, a feladat egy automatikus felhasználói fiók alatt fut, amely a készlet bármely feladata számára elérhető. A készlethatókörről a Feladat futtatása készlethatókörrel rendelkező automatikus felhasználóként című szakaszban talál további információt.   

Az alapértelmezett hatókör windowsos és Linuxos csomópontokon eltérő:

- A Windows-csomópontokon a feladatok alapértelmezés szerint feladathatókör alatt futnak.
- A Linux-csomópontok mindig készlethatókör alatt futnak.

Az automatikus felhasználó specifikációjának négy lehetséges konfigurációja van, amelyek mindegyike egy egyedi automatikus felhasználói fióknak felel meg:

- Nem rendszergazdai hozzáférés feladathatókörrel (az alapértelmezett automatikus felhasználóspecifikáció)
- Rendszergazdai (emelt szintű) hozzáférés feladathatókörrel
- Nem rendszergazdai hozzáférés készlethatókörrel
- Rendszergazdai hozzáférés készlethatókörrel

> [!IMPORTANT] 
> A feladathatókör alatt futó feladatok nak nincs tényleges hozzáférésük a csomópont más tevékenységeihez. A fiókhoz hozzáféréssel rendelkező rosszindulatú felhasználók azonban megkerülhetik ezt a korlátozást, ha rendszergazdai jogosultságokkal futó feladatot küld el, és más feladatkönyvtárakhoz fér hozzá. A rosszindulatú felhasználók rdp vagy SSH használatával is csatlakozhatnak egy csomóponthoz. Fontos, hogy megvédje a hozzáférést a Batch-fiók kulcsaihoz, hogy megakadályozza az ilyen forgatókönyv. Ha arra gyanakszik, hogy a fiókját feltörték, mindenképpen hozza létre újra a kulcsokat.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Feladat futtatása emelt szintű hozzáférésű automatikus felhasználóként

Beállíthatja az automatikus felhasználói specifikációt a rendszergazdai jogosultságokhoz, ha emelt szintű hozzáféréssel rendelkező feladatot kell futtatnia. Előfordulhat például, hogy egy indítási feladathoz emelt szintű hozzáférésre van szükség a szoftver csomópontra történő telepítéséhez.

> [!NOTE] 
> Általában a legjobb, ha csak szükség esetén használja a kiemelt hozzáférést. A legjobb gyakorlatok azt javasolják, hogy a kívánt eredmény eléréséhez szükséges minimális jogosultságot biztosítsa. Ha például egy indítási feladat szoftvert telepít az aktuális felhasználó számára, az összes felhasználó helyett, akkor elkerülheti a feladatokhoz való emelt szintű hozzáférést. Beállíthatja az automatikus felhasználói specifikációkészlet hatókörét és a nem rendszergazdai hozzáférést minden olyan feladathoz, amelynek ugyanazon fiók alatt kell futnia, beleértve az indítási feladatot is. 
>
>

A következő kódrészletek bemutatják, hogyan kell konfigurálni az automatikus felhasználó specifikációját. A példák a `Admin` magassági szintet `Task`és a hatókört a parancsra állították be. A feladathatókör az alapértelmezett beállítás, de a példa kedvéért itt szerepel.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Feladat futtatása készlethatókörrel rendelkező automatikus felhasználóként

Egy csomópont kiépítésekor két készletszintű automatikus felhasználói fiók jön létre a készlet minden csomópontján, egy emelt szintű hozzáféréssel, és egy emelt szintű hozzáférés nélkül. Ha az automatikus felhasználó hatókörét egy adott feladat készlethatókörére állítja be, a feladat a két készletszintű automatikus felhasználói fiók egyikében fut. 

Ha az automatikus felhasználó számára készlethatókör-hatókört ad meg, a rendszergazdai hozzáféréssel futó összes feladat ugyanazzal a készletszintű automatikus felhasználói fiókkal fut. Hasonlóképpen, a rendszergazdai engedélyek nélkül futó feladatok egyetlen készletszintű automatikus felhasználói fiók alatt is futnak. 

> [!NOTE] 
> A két gyűjtőszintű automatikus felhasználói fiók különálló fiók. Az egész készletre kiterjedő felügyeleti fiók alatt futó feladatok nem oszthatnak meg adatokat a normál fiók alatt futó feladatokkal, és fordítva. 
>
>

Az ugyanazon automatikus felhasználói fiók alatt való futtatás előnye, hogy a feladatok képesek megosztani az adatokat az ugyanazon a csomóponton futó más feladatokkal.

A feladatok közötti titkos kulcsok megosztása az egyik forgatókönyv, ahol a feladatok futtatása a két készlet szintű automatikus felhasználói fiókok egyike alatt hasznos. Tegyük fel például, hogy egy kezdő feladatnak ki kell építenie egy titkos kulcsot a csomópontra, amelyet más tevékenységek használhatnak. Használhatja a Windows Adatvédelmi API-t (DPAPI), de rendszergazdai jogosultságokat igényel. Ehelyett a titkos kulcsot felhasználói szinten védheti. Az ugyanazon felhasználói fiók alatt futó feladatok emelt szintű hozzáférés nélkül is hozzáférhetnek a titkos kulcsot.

Egy másik forgatókönyv, ahol a feladatokat egy készlethatókörrel rendelkező automatikus felhasználói fiók alatt szeretné futtatni, az MPI fájlmegosztás. Az MPI fájlmegosztás akkor hasznos, ha az MPI-feladat csomópontjainak ugyanazon a fájladaton kell dolgozniuk. A főcsomópont létrehoz egy fájlmegosztást, amelyhez a gyermekcsomópontok hozzáférhetnek, ha ugyanazon automatikus felhasználói fiók alatt futnak. 

A következő kódrészlet az automatikus felhasználó hatókörét állítja be egy feladat hatókörének készletére a Batch .NET-ben. A magasságszint kimarad, így a feladat a szabványos készletszintű automatikus felhasználói fiók alatt fut.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Elnevezett felhasználói fiókok

Készlet létrehozásakor megadhat elnevezett felhasználói fiókokat. A megnevezett felhasználói fiók nak megadott neve és jelszava van. Megadhatja egy elnevezett felhasználói fiók magasságszintjét. Linux-csomópontok esetén ssh titkos kulcsot is megadhat.

Egy elnevezett felhasználói fiók létezik a készlet összes csomópontján, és elérhető az ezeken a csomópontokon futó összes feladat számára. A készlethez tetszőleges számú elnevezett felhasználót definiálhat. Feladat vagy feladatgyűjtemény hozzáadásakor megadhatja, hogy a feladat a készletben definiált elnevezett felhasználói fiókok egyike alatt futjon.When you add a task or task collection, you can specify that the task runs under one of the named user accounts defined on the pool.

A névvel ellátott felhasználói fiók akkor hasznos, ha egy feladat összes feladatát ugyanazzal a felhasználói fiókkal szeretné futtatni, de elkülöníteni szeretné őket a más feladatokban egyidejűleg futó feladatoktól. Létrehozhat például egy elnevezett felhasználót minden feladathoz, és futtathatja az egyes feladatok feladatait az adott nevű felhasználói fiók alatt. Ezután minden feladat megoszthatja a titkos kulcsot a saját feladataival, de a más feladatokban futó feladatokkal nem.

Névvel ellátott felhasználói fiókkal is futtathat olyan feladatot, amely beállítja a külső erőforrásokra, például a fájlmegosztásokra vonatkozó engedélyeket. Egy elnevezett felhasználói fiókkal szabályozhatja a felhasználói identitást, és ezzel a felhasználói identitással állíthatja be az engedélyeket.  

A megnevezett felhasználói fiókok jelszó nélküli SSH-t engedélyeznek a Linux-csomópontok között. Használhat egy elnevezett felhasználói fiókot Linux-csomópontokkal, amelyektöbbpéldányos feladatokfuttatásához kell. A készlet minden csomópontja futtathat feladatokat a teljes készleten definiált felhasználói fiók alatt. A többpéldányos feladatokról további információt a [Többpéldányos\-feladatok használata MPI-alkalmazások futtatásához című témakörben talál.](batch-mpi.md)

### <a name="create-named-user-accounts"></a>Elnevezett felhasználói fiókok létrehozása

Ha névvel ellátott felhasználói fiókokat szeretne létrehozni a Batch alkalmazásban, adja hozzá a felhasználói fiókok gyűjteményét a készlethez. A következő kódrészletek bemutatják, hogyan hozhat létre elnevezett felhasználói fiókokat a .NET, java és Python nyelven. Ezek a kódrészletek azt mutatják be, hogyan hozhat létre rendszergazdai és nem rendszergazdai nevű fiókokat egy készletben. A példák a felhőszolgáltatás-konfiguráció használatával hoznak létre készleteket, de ugyanazt a megközelítést használja, amikor windowsos vagy Linux-készletet hoz létre a virtuális gép konfigurációjával.

#### <a name="batch-net-example-windows"></a>Batch .NET példa (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET példa (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Java kötegelt feldolgozás példa

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Példa a Python-sorozatra

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Feladat futtatása emelt szintű hozzáférésű elnevezett felhasználói fiók alatt

Ha egy feladatot emelt szintű felhasználóként szeretne futtatni, állítsa a feladat **UserIdentity** tulajdonságát egy `Admin`elnevezett felhasználói fiókra, amelyet úgy hoztak létre, hogy **az ElevationLevel** tulajdonsága értéke .

Ez a kódrészlet azt határozza meg, hogy a feladatnak elnevezett felhasználói fiók alatt kell futnia. Ez az elnevezett felhasználói fiók a készlet létrehozásakor lett definiálva a készletben. Ebben az esetben a megnevezett felhasználói fiók rendszergazdai engedélyekkel lett létrehozva:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>A kód frissítése a legújabb Batch ügyféltárra

A Batch szolgáltatás 2017-01-01.4.0-s verziója bevezet egy törésmódosítást, amely a korábbi verziókban elérhető **runElevated** tulajdonságot a userIdentity tulajdonságra **cseréli.** Az alábbi táblázatok egyszerű hozzárendelést biztosítanak, amelyaz ügyfélkód-kód korábbi verzióiból származó kód frissítéséhez használható.

### <a name="batch-net"></a>Batch .NET

| Ha a kód használ ...                  | Frissítse azt ....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`nincs megadva | Nincs szükség frissítésre                                                                                               |

### <a name="batch-java"></a>Batch Java

| Ha a kód használ ...                      | Frissítse azt ....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`nincs megadva | Nincs szükség frissítésre                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Ha a kód használ ...                      | Frissítse azt ....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated`nincs megadva | Nincs szükség frissítésre                                                                                                                                  |


## <a name="next-steps"></a>További lépések

* A Batch részletes áttekintését a [Nagyméretű párhuzamos számítási megoldások fejlesztése a Batch segítségével](batch-api-basics.md)című témakörben találja.
