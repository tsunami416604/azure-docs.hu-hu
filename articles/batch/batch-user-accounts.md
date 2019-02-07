---
title: A felhasználói fiókok – Azure Batch a feladatok futtatása |} A Microsoft Docs
description: Felhasználói fiókok feladatok futtatásához az Azure Batchben konfigurálása
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: ba64ecc334d93c8ff973345cfd10ed12436d3fb6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813908"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>A felhasználói fiókok feladatok futtatása a Batchben

Az Azure Batch-feladat mindig a felhasználói fiók alatt fut. Alapértelmezés szerint a feladatok futnak, a normál felhasználói fiókok rendszergazdai engedélyek nélkül. Ezek alapértelmezett felhasználói fiókot a beállítások általában elegendők. Bizonyos esetekben azonban hasznos lehet a felhasználói fiók, amelyben a feladat futtatása szeretne konfigurálni. A cikkből megtudhatja, milyen típusú felhasználói fiókokat, és hogyan konfigurálhatja őket a forgatókönyvhöz.

## <a name="types-of-user-accounts"></a>Felhasználói fiókok típusai

Az Azure Batch két típusú felhasználói fiókok feladatok futtatásához biztosít:

- **Automatikus felhasználói fiókokat.** Automatikus – felhasználói fiókok a Batch szolgáltatás által automatikusan létrehozott beépített felhasználói fiókok lesznek. Alapértelmezés szerint a feladatok automatikus felhasználói fiókkal futtassa. Konfigurálhatja az automatikus felhasználói specifikáció egy tevékenység jelzésére, hogy melyik automatikus felhasználói fiók alatt egy feladat fusson. Az automatikus felhasználó-specifikáció lehetővé teszi, hogy adja meg a jogosultságszint-emelési szint és a hatókör auto-felhasználói fiók, amely futtatja a feladatot. 

- **Egy névvel ellátott felhasználói fiókot.** Megadhat egy készlet egy vagy több névvel ellátott felhasználói fiókokat a készlet létrehozásakor. Minden felhasználói fiók jön létre a készlet minden csomópontján. A fiók neve mellett, adja meg a felhasználói fiók jelszavát, a jogosultságszint-emelési szinten, valamint a Linux-készletek, a titkos SSH-kulcs. Amikor hozzáad egy feladatot, megadhatja a nevesített felhasználói fiók, amelyben a feladat futhat.

> [!IMPORTANT] 
> A Batch szolgáltatás verziója 2017-01-01.4.0 vezet be, amely megköveteli, hogy a kódot annak azt a verziót frissíti használhatatlanná tévő változást. Ha Ön egy régebbi verzióját a Batch-áttelepítése kódokat, vegye figyelembe, hogy a **runElevated** tulajdonság már nem támogatott a a REST API vagy a Batch-klienskódtárakkal. Az új **userIdentity** jogosultságszint-emelési szintet adjon meg egy feladat tulajdonságát. Szakaszában [a kód frissítése a legújabb Batch ügyféloldali kódtárral való](#update-your-code-to-the-latest-batch-client-library) a Batch-kód frissítése, ha használja a klienskódtárak egyik gyors szakaszát.
>
>

> [!NOTE] 
> A felhasználói fiókokat, a cikkben leírtak szerint nem támogatja a távoli asztal protokoll (RDP) vagy a Secure Shell (SSH), biztonsági okokból. 
>
> Egy csomópont futtató Linux rendszerű virtuálisgép-konfiguráció SSH-n keresztül kapcsolódni, lásd: [távoli asztal használata Linux virtuális géphez az Azure-ban](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). A csomópontokon futó Windows RDP-Kapcsolaton keresztül csatlakozni, lásd: [Windows Server virtuális Géphez való csatlakozás](../virtual-machines/windows/connect-logon.md).<br /><br />
> Egy csomópont a felhőszolgáltatás konfigurációjában futó RDP-n keresztül kapcsolódni, lásd: [távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Felhasználói fiók számára hozzáférést kell a fájlok és könyvtárak

Automatikus – felhasználói fiókkal és a egy névvel ellátott felhasználói fiók rendelkezik olvasási/írási hozzáférést a feladat működő könyvtárba, megosztott és többpéldányos tevékenységek könyvtárat. Mindkét típusú fiókok a indítási és a feladat előkészítése könyvtárakhoz olvasási hozzáféréssel rendelkezik.

Ha egy feladat az indítási tevékenység futtatásához használt ugyanazon fiók alatt fut, a feladat rendelkezik olvasási és írási hozzáférése a kezdő tevékenység könyvtárának. Hasonlóképpen ha egy tevékenység fut egy feladat-előkészítési tevékenység futtatásához használt fiókba, a feladat a feladat-előkészítési tevékenység könyvtárának olvasási és írási hozzáféréssel rendelkezik. Ha egy tevékenység fut egy másik fiókból, mint az indítási tevékenység vagy a feladat-előkészítési tevékenység, a feladat a megfelelő könyvtár csak olvasási hozzáféréssel rendelkezik.

A feladat a fájlok és könyvtárak elérése további információkért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Emelt szintű hozzáférési feladatok 

A felhasználói fiók jogosultságszint-emelési szint azt jelzi, hogy egy tevékenység fut-e emelt szintű hozzáféréssel rendelkező. Emelt szintű hozzáférés automatikus – felhasználói fiókkal és a egy névvel ellátott felhasználói fiókot is futtatható. A jogosultságszint-emelési szint két lehetőségek közül választhat:

- **Nonadmin elemet:** Emelt szintű hozzáférés nélkül normál felhasználóként futtatja a tevékenységet. Egy Batch-felhasználói fiókhoz alapértelmezett jogosultságszint-emelési szintje nem mindig **nonadmin elemet**.
- **Rendszergazdai:** A feladat fut, a felhasználó emelt szintű hozzáféréssel rendelkező, és teljes körű rendszergazdai engedélyekkel működik. 

## <a name="auto-user-accounts"></a>Automatikus felhasználói fiókok

Alapértelmezés szerint feladatok futtatása a Batchben automatikus felhasználói fiókkal, emelt szintű hozzáférés nélkül, és a feladat hatókörrel rendelkező normál felhasználóként. Az automatikus felhasználói specifikációt konfigurálásakor feladat hatókör a Batch szolgáltatás létrehozza ezt a feladatot csak egy automatikus-felhasználói fiókot.

A tulajdonos alternatív feladat hatókörhöz készlet hatóköre. Ha az automatikus felhasználói specifikáció egy tevékenység a készlet hatóköre van konfigurálva, futtatja a tevékenységet a készletben lévő minden tevékenység számára elérhető automatikus felhasználói fiókkal. Készlet hatóköre kapcsolatos további információkért lásd: a készlet hatóköre auto-felhasználóként részre feladat futtatása.   

Az alapértelmezett hatókör nem egyezik a Windows és Linux-csomópontok:

- A Windows-csomópontok, feladatok feladat hatókör alatt alapértelmezés szerint futnak.
- Linux-csomópontok a készlet hatóköre mindig futni.

Nincsenek négy lehetséges konfigurációk automatikus felhasználói meghatározását, amelyek mindegyike megfelel egy egyedi auto-felhasználói fiókhoz:

- A nem rendszergazda hozzáférést a tevékenység hatóköre (az alapértelmezett felhasználói automatikus specifikáció)
- A feladat hatóköre (emelt szintű) rendszergazdai hozzáférés
- A nem rendszergazda jogosultságú hozzáférés a készlet hatóköre
- Rendszergazdai hozzáférés a készlet hatóköre

> [!IMPORTANT] 
> Feladat hatókör alatt futó feladatok a csomópont nem rendelkezik szabvánnyá hozzáférést egyéb feladatokhoz. Azonban a fiók hozzáféréssel rendelkező rosszindulatú felhasználók sikerült kerülő Ez a korlátozás nélkül elküld egy feladatot, amely rendszergazdai jogosultságokkal fut, és egyéb feladat címtárak fér hozzá. Egy rosszindulatú felhasználó is használhatja az RDP vagy SSH-csomóponthoz szeretne csatlakozni. Fontos, hogy az ilyen esetben a Batch-fiók kulcsai való hozzáférés védelme. Ha azt gyanítja, hogy a fiók lehet, hogy sérült a biztonsága, mindenképpen a kulcsok újragenerálása.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Egy feladat futtatásához emelt szintű hozzáféréssel rendelkező auto-felhasználóként

Amikor szüksége van a feladat futtatásához emelt szintű hozzáféréssel rendelkező konfigurálhatja úgy az automatikus felhasználói-specifikációt rendszergazdai jogosultságokkal. Az indítási tevékenység például előfordulhat, hogy kell a csomóponton a szoftver telepítéséhez emelt szintű hozzáférés.

> [!NOTE] 
> Általában célszerű használni az emelt szintű hozzáférés csak szükség esetén. A bevált gyakorlat a kívánt eredmények eléréséhez szükséges, a minimális jogosultság megadása. Például ha az indítási tevékenység szoftvereket telepít az aktuális felhasználó helyett minden felhasználót, előfordulhat, hogy tudni kerülje a emelt szintű hozzáférési feladatok. Konfigurálhatja az automatikus felhasználói specifikáció készlet hatókörrel és a nem rendszergazdai hozzáférés az összes tevékenység, amely ugyanazt a fiókot, beleértve az indítási tevékenység futtatásához szükséges. 
>
>

Az alábbi kódrészletek bemutatják, hogyan konfigurálhatja az automatikus felhasználó-specifikáció. A példák állítsa a jogosultságszint-emelési szintet `Admin` és a hatókör `Task`. A feladat hatókör az alapértelmezett beállítás, de része itt az a példában.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch – Java

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Feladatok futtatása a készlet hatóköre auto-felhasználóként

Ha egy csomópont ki van építve, két készlet kiterjedő automatikus felhasználói fiókok jönnek létre minden egyes csomóponton a a készlet, egy emelt szintű hozzáféréssel rendelkező és a egy emelt szintű hozzáférés nélkül. E két készlet kiterjedő automatikus felhasználói fiókok alapján a feladat az auto-felhasználói hatókör beállítása egy adott tevékenység a készlet hatókörhöz futtatja. 

Ha automatikus-felhasználó számára – rendszergazdai jogosultságokkal futtassa ugyanazon készlet kiterjedő automatikus felhasználói fiók alatt futó összes tevékenység a készlet hatókör ad meg. Rendszergazdai jogosultságok nélkül futó feladatok hasonlóan is futtathat egyetlen készlet kiterjedő automatikus felhasználói fiók alatt. 

> [!NOTE] 
> A két készlet kiterjedő automatikus felhasználói fiók is külön fiókokat. A készlet teljes rendszergazdai fiókban futó feladatok nem oszthat meg adatokat feladatok futtatása a standard szintű fiók alatt, és fordítva. 
>
>

Az azonos auto-felhasználói fiók alatt futó előnye, hogy a feladatok meg tudják megoszthatják az adatokat más ugyanazon a csomóponton futó feladatok.

Titkos kódok tevékenységek közötti megosztás engedélyezve egy forgatókönyvet, ahol alatt a két készlet kiterjedő automatikus felhasználói fiókok feladatok futtatásához hasznos. Tegyük fel, hogy az indítási tevékenység kell kiépíteni az alakzatot a csomópont, amellyel más feladatok egy titkos kulcsot. Használhatja a Windows Data Protection API (DPAPI), de futtatásához rendszergazdai jogosultság szükséges. Ehelyett védheti meg a titkos kulcsot, felhasználói szinten. Az azonos felhasználói fiókban futó feladatok férhetnek hozzá a titkos kulcsot, emelt szintű hozzáférés nélkül.

Egy másik forgatókönyv, ahol lehetséges, hogy futtatni kívánt feladatok automatikus felhasználói fiókkal a készlet hatóköre Message Passing Interface (MPI) fájlmegosztás. Az MPI-fájlmegosztások akkor hasznos, ha a csomópontok az MPI-feladatban kell a fájl ugyanazokon az adatokon. A fő csomópontot hoz létre a fájlmegosztást, amelyet a gyermekcsomópontokat érhető el, ha a ugyanazon automatikus – felhasználói fiókkal futtatnak. 

A következő kódrészletet az auto-felhasználói hatókör készlet hatóköre egy feladat a Batch .NET-re állítja. A jogosultságszint-emelési szint nincs megadva, így a feladat fut, a standard készlet kiterjedő automatikus felhasználói fiókban.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Névvel ellátott felhasználói fiókok

Nevesített felhasználó fiókok definiálhat egy készlet létrehozásakor. Névvel ellátott felhasználói fiók rendelkezik egy nevet és jelszót. Megadhatja a jogosultságszint-emelési szintjét egy névvel ellátott felhasználói fiókot. Linux-csomópontokat, a titkos SSH-kulcs is megadhatja.

Egy névvel ellátott felhasználói fiókot a készlet minden csomópontján létezik, és a csomópontokon futó összes tevékenység számára elérhető. Nevesített felhasználók készlet tetszőleges számú adhat meg. Amikor hozzáad egy feladat vagy tevékenység gyűjtemény, megadhatja, hogy a feladat fut. a készlet a megadott névvel ellátott felhasználói fiókot egy.

Egy névvel ellátott felhasználói fiókot akkor hasznos, ha ugyanazt a felhasználói fiók alatt egy feladat minden feladatot futtathat, azonban elkülöníti azokat az egyéb feladatok egyidejűleg futtatott feladatok szeretné. Például hozzon létre egy megadott felhasználónak az egyes feladatokhoz, és futtassa az egyes feladatok az adott nevű felhasználói fiók. Minden egyes feladat ezután megoszthatja egy titkos kulcsot a saját feladatok, de nem más feladatokat futtatott feladatok.

Használhatja egy névvel ellátott felhasználói fiókot egy feladatot, amely a külső erőforrások, például fájlmegosztásokat engedélyeket állít be. Névvel ellátott felhasználói fiókkal szabályozhatja a felhasználó identitását, és a felhasználói identitás az engedélyek beállítása.  

Nevesített felhasználó fiókok lehetővé teszik a jelszó nélküli SSH Linux-csomópontok között. Többpéldányos tevékenységek futtatásához szükséges Linux-csomópontokat tartalmazó egy névvel ellátott felhasználói fiókot is használhatja. A készlet minden csomópontján feladatokat futtat a teljes készlet egy felhasználói fiókkal. Többpéldányos tevékenységek kapcsolatos további információkért lásd: [használata többszörös\-feladatok futtatásához az MPI-alkalmazások példány](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Névvel ellátott felhasználói fiókok létrehozása

Hozzon létre nevű felhasználói fiókot a Batch szolgáltatásban, a felhasználói fiókok gyűjtemény hozzáadása a készlethez. Az alábbi kódtöredékek bemutatják, hogyan nevű felhasználói fiók létrehozása a .NET, Java és Python. Ezek a kódrészletek bemutatják, hogyan hozhat létre a rendszergazda és a nem rendszergazdai fiókok, készletek nevű. A példák a felhőszolgáltatás-konfigurációt használó készletekben létrehozása, de ugyanezzel a módszerrel használhatja, ha a készletet hoz létre egy Windows vagy Linux rendszerű virtuálisgép-konfiguráció használatával.

#### <a name="batch-net-example-windows"></a>A Batch .NET típusú példát (Windows)

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

#### <a name="batch-net-example-linux"></a>A Batch .NET típusú példát (Linux)

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


#### <a name="batch-java-example"></a>Batch Java-példában

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

#### <a name="batch-python-example"></a>A Batch Python-példát

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Emelt szintű hozzáféréssel rendelkező nevű felhasználói fiók alatt feladat futtatása

Olyan feladatot futtat egy emelt szintű felhasználóként, állítsa a tevékenység **UserIdentity** tulajdonsággal egy elnevezett felhasználói fiókhoz, amellyel létrehozták az **ElevationLevel** tulajdonság `Admin`.

Ez a kódrészlet Megadja, hogy a feladat nevű felhasználói fiók alatt fusson. Ez a nevű felhasználói fiók a készleten lett definiálva, a készlet létrehozásakor. Ebben az esetben a nevű felhasználói fiók rendszergazdai jogosultságokkal rendelkezik lett létrehozva:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>A kód frissítése a legújabb Batch ügyféloldali kódtár

A Batch szolgáltatás verziója 2017-01-01.4.0 bemutatja a használhatatlanná tévő változást, és cserélje le a **runElevated** tulajdonság a korábbi verziókban érhető el a **userIdentity** tulajdonság. A következő táblázatokban egy egyszerű leképezés, amely használatával a kód korábbi verzióit a klienskódtár frissítése.

### <a name="batch-net"></a>Batch .NET

| Ha a kódot használ...                  | Frissítse úgy, hogy...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Nincs megadva | Nincs frissítés szükséges                                                                                               |

### <a name="batch-java"></a>Batch – Java

| Ha a kódot használ...                      | Frissítse úgy, hogy...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Nincs megadva | Nincs frissítés szükséges                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Ha a kódot használ...                      | Frissítse úgy, hogy...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` Nincs megadva | Nincs frissítés szükséges                                                                                                                                  |


## <a name="next-steps"></a>További lépések

* A Batch részletesebb áttekintéséért lásd: [Develop nagy léptékű párhuzamos számítási megoldások Batch segítségével történő](batch-api-basics.md).
