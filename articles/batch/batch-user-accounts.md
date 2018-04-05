---
title: A felhasználói fiókok feladatok futtatása az Azure Batch |} Microsoft Docs
description: Feladatok futtatása az Azure Batch felhasználói fiókok konfigurálása
services: batch
author: dlepow
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
ms.author: danlep
ms.openlocfilehash: 1b9c0514e93fa89f8776d830ef242fc4963a6f7b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Kötegelt a felhasználói fiókok feladatok futtatása

Mindig fusson a feladat, az Azure Batch egy felhasználói fiókkal. Alapértelmezés szerint a feladatok futtathatók általános jogú felhasználói fiókokat, a rendszergazdai engedélyekkel. Ezek az alapértelmezett felhasználói fiók beállítások általában elegendők. Bizonyos esetekben azonban célszerű tudják a felhasználói fiók, amely alatt a feladat futtatása szeretne konfigurálni. A cikkből megtudhatja, milyen felhasználói fiókok, és hogyan konfigurálhatók a forgatókönyvnek.

## <a name="types-of-user-accounts"></a>Felhasználói fiókok típusai

Az Azure Batch kétféle típusú felhasználói fiókokat biztosít az éppen futó feladatok:

- **Automatikus-felhasználói fiókokat.** Automatikus-felhasználói fiókok olyan beépített felhasználói fiókok, a Batch szolgáltatás automatikusan létrehozza. Alapértelmezés szerint feladatok automatikus felhasználói fiókkal futtassa. Konfigurálhatja az automatikus felhasználói specifikáció annak jelzésére, hogy mely automatikus-felhasználói fiók alatt egy feladat fusson a feladat. A felhasználói automatikus megadását lehetővé teszi, hogy a jogosultságszint-emelés szint és a auto-felhasználói fiók, amely a feladat hatókör megadását. 

- **Egy névvel ellátott felhasználói fiókot.** Megadhatja a készlet egy vagy több elnevezett felhasználói fiókot, a készlet létrehozásakor. Minden hozzáadott felhasználói fiókot a készlet minden egyes csomóponton jön létre. A fiók neve mellett, adja meg a felhasználói fiók jelszavát, jogosultságszint-emelési szinten, valamint a Linux-készletek, a titkos SSH-kulcsot. Ha hozzáad egy feladatot, megadhatja a névvel ellátott felhasználói fiókot, amely alatt ez a feladat futni.

> [!IMPORTANT] 
> A Batch szolgáltatás 2017-01-01.4.0 tartalmazza az használhatatlanná tévő változást, amely megköveteli, hogy a kód hívására, hogy verziót frissíti. Ha áttelepítése kód köteg egy korábbi verziójából származó, vegye figyelembe, hogy a **runElevated** tulajdonság már nem támogatott a REST API vagy kötegelt klienskódtárak segítségével. Az új **userIdentity** tulajdonság a feladatok jogosultságszint-emelés szintjének beállításához. Kifejezéseit leíró szakasza [frissítse a kódot a legújabb kötegelt ügyféloldali kódtár](#update-your-code-to-the-latest-batch-client-library) kapcsolatos frissítése a kötegelt kód használata a klienskódtárak segítségével egyik gyors útmutatást.
>
>

> [!NOTE] 
> A cikkben szereplő felhasználói fiókok nem támogatják az Remote Desktop Protocol (RDP) vagy a Secure Shell (SSH), a biztonsági okok miatt. 
>
> A csomópont futó Linux virtuálisgép-konfiguráció SSH-kapcsolaton keresztül csatlakoznak, lásd: [távoli asztal használata a Linux virtuális gépre az Azure-ban](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Csomópontok Windows rendszerű RDP-kapcsolaton keresztül csatlakoznak, lásd: [csatlakozás egy Windows Server virtuális gép](../virtual-machines/windows/connect-logon.md).<br /><br />
> Egy csomópontot, a felhőalapú szolgáltatás konfigurációja fut RDP-kapcsolaton keresztül csatlakoznak, lásd: [engedélyezése a távoli asztali kapcsolat az Azure Cloud Services szerepkör](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>

## <a name="user-account-access-to-files-and-directories"></a>Fájlok és könyvtárak felhasználóifiók-hozzáférés

Automatikus-felhasználói fiókkal és a nevű felhasználói fiók rendelkezik olvasási/írási hozzáférést a feladatütemezési munkakönyvtár, megosztott és többpéldányos feladatok könyvtárat. Mindkét típusú fiókok a indítási és a feladat előkészítése könyvtárakhoz olvasási hozzáféréssel rendelkezik.

Ha a feladat kezdési feladat futtatásához használt ugyanazon fiók alatt fut, a feladat rendelkezik olvasási és írási hozzáférése a kezdő tevékenység könyvtárba. Hasonlóképpen ha egy feladat a feladat előkészítése tevékenység futtatásához használt ugyanazon fiók alatt fut, a feladat olvasási és írási hozzáférése a feladat előkészítése tevékenység könyvtár rendelkezik. Ha egy feladat fut, mint a kezdő tevékenység vagy a feladat előkészítése tevékenységet egy másik fiókból, a feladat a megfelelő könyvtár csak olvasási hozzáféréssel rendelkezik.

A fájlok és könyvtárak feladat eléréséhez további információkért lásd: [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Emelt szintű hozzáférés feladatokhoz 

A felhasználói fiók jogosultságszint-emelés szintjét jelzi, hogy fut-e a feladat emelt szintű hozzáféréssel rendelkező. Automatikus-felhasználói fiókkal és a egy névvel ellátott felhasználói fiókot is futtassa emelt szintű hozzáférés. Jogosultságszint-emelés szint két lehetőségek állnak rendelkezésére:

- **NonAdmin:** a feladat fut, az általános jogú felhasználó emelt szintű hozzáférés nélkül. Az alapértelmezett jogosultságszint-emelés kötegelt felhasználói fiók szintje mindig **NonAdmin**.
- **Felügyeleti:** a feladat emelt szintű hozzáféréssel rendelkező felhasználóként fut, és teljes körű felügyeleti engedélyekkel működik. 

## <a name="auto-user-accounts"></a>Automatikus-felhasználói fiókok

Alapértelmezés szerint feladatokat futtató kötegben automatikus felhasználói fiókkal, az általános jogú felhasználó emelt szintű hozzáférés nélkül, és a feladat hatókörében. Amikor az auto-felhasználó specifikáció tevékenység hatókörében van konfigurálva, a Batch szolgáltatás ezt a feladatot csak egy automatikus-felhasználói fiókot hoz létre.

A feladat hatókörébe esetben készlet hatókör. Ha egy feladat automatikus felhasználói előírásának készlet hatókör van konfigurálva, a feladat érhető el a készletben található összes feladatütemezési automatikus felhasználói fiókkal fut. Készlet hatókör kapcsolatos további információkért lásd: című részre [feladat felhasználóként futtatja az automatikus-készlet hatókörű](#run-a-task-as-the-autouser-with-pool-scope).   

Az alapértelmezett hatókör nem azonos a Windows és Linux-csomópontok:

- Windows csomópontján feladatok futtathatók feladat hatókör alapértelmezés szerint.
- Linux-csomópontok a készlet hatókör mindig futnia.

Az automatikus felhasználói megadását, amelyek mindegyike megfelel egy egyedi automatikus-felhasználói fiókhoz négy lehetséges konfiguráció van:

- A nem rendszergazda hozzáférést feladat hatókörrel (az alapértelmezett automatikus felhasználói specifikáció)
- A feladat hatókörű (emelt szintű) rendszergazdai hozzáférés
- A nem rendszergazda hozzáférést készlet hatókörű
- Rendszergazdai hozzáférés készlet hatókörű

> [!IMPORTANT] 
> Feladat hatókör alatt futó feladatok nincs hozzáférése tényleges más feladatok csomóponton. Azonban egy rosszindulatú felhasználó hozzáfér a fiókjához sikerült megoldható, ez a korlátozás, amely rendszergazdai jogosultságokkal futtatja, és más tevékenység könyvtárak fér hozzá a feladat elküldése. Egy rosszindulatú felhasználó is használhatja az RDP és az SSH egy csomópont való kapcsolódáshoz. Fontos, hogy megakadályozza az ilyen esetben a kötegelt kulcsait való hozzáférés. Ha azt gyanítja, hogy a fiók sérült, ügyeljen arra, hogy a kulcsok újragenerálása.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Emelt szintű hozzáféréssel rendelkező automatikus-felhasználóként feladat futtatása

Rendszergazdai jogosultságokkal automatikus felhasználói előírásának konfigurálhatja, ha a feladat futtatásához emelt szintű hozzáféréssel rendelkező szükséges. A kezdő tevékenység Előfordulhat például, emelt szintű hozzáférés szoftver telepítéséhez a csomóponton.

> [!NOTE] 
> Általában célszerű emelt szintű hozzáférés csak szükség esetén. A bevált gyakorlat része a kívánt eredmény eléréséhez szükséges minimális jogosultság megadása. Például ha egy kezdő tevékenység szoftvereket telepít az aktuális felhasználó ahelyett, hogy a felhasználók esetleg elkerülheti a feladatok emelt szintű hozzáférés biztosítása. Beállíthatja, hogy a készlet hatókör és a nem rendszergazdai hozzáférést, amely ugyanazt a fiókot, beleértve a kezdő tevékenység futnia kell az összes tevékenység automatikus felhasználói megadását. 
>
>

Az alábbi kódtöredékek bemutatják, hogyan konfigurálása a felhasználói automatikus megadását. A példák állítsa a jogosultságszint-emelés szintet `Admin` és a hatókör `Task`. Feladat hatókörében az alapértelmezett beállítás, de megtalálható itt az példa.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Kötegelt Java

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Készlet hatókörű automatikus-felhasználóként feladat futtatása

Ha egy csomópont ki van építve, két készlet kiterjedő automatikus-felhasználói fiókok létrejöttek mindegyik csomópontján a készletben, egy emelt szintű hozzáféréssel rendelkező és egy emelt szintű hozzáférés nélkül. Az automatikus felhasználó hatókör beállítást egy adott tevékenység készlet hatókör fut a feladat során két készlet kiterjedő automatikus-felhasználói fiókot. 

Ha a készlet hatókör a auto-felhasználó, minden olyan feladat, amely ugyanazt a készlet kiterjedő automatikus-felhasználói fiókot futtatásához rendszergazdai jogosultságokkal futtassa. Ehhez hasonlóan rendszergazdai jogosultságok nélküli futtatott feladatok is futtathatók egy alkalmazáskészlet kiterjedő automatikus felhasználói fiókkal. 

> [!NOTE] 
> A két készlet kiterjedő automatikus-felhasználói fiókok külön fiók is. A készlet szintű rendszergazdai fiók alatt futó feladatok nem adatok megosztása a szabványos fiókkal, és ez fordítva is igaz futó feladatok. 
>
>

Azonos automatikus felhasználói fiók alatt fut előnye, hogy a feladatok képesek adatok megosztása más ugyanazon a csomóponton futó feladatok.

Egy forgatókönyvet, ahol az éppen futó feladatok a két készlet kiterjedő automatikus-felhasználói fiókok valamelyike akkor hasznos, titkos kulcsok tevékenységek közötti megosztása szolgáltatás. Tegyük fel, hogy a kezdő tevékenységre kell egy titkos kulcsot, a csomópont, amellyel más feladatok kiépítéséhez. Használhatja a Windows Data Protection API (DPAPI), de a rendszergazdai jogosultságokat igényel. Ehelyett a titkos kulcsot a felhasználói szintű védelmet biztosíthat. Az azonos felhasználói fiók alatt futó feladatok férhetnek hozzá a titkos kulcsot, emelt szintű hozzáférés nélkül.

Ahol lehetséges, hogy futtatni kívánt feladatokat egy automatikus-felhasználói fiókhoz tartozó készlet hatókörű egy másik helyzet lehet a Message Passing Interface (MPI) fájlmegosztást. Egy MPI fájlmegosztás akkor hasznos, ha a fájl ugyanazokat az adatokat a csomópontok a MPI feladat kell használnia. Az átjárócsomópont létrehoz egy fájlmegosztást, amely a gyermekcsomópontok hozzáférhet, ha azonos automatikus felhasználói fiók alatt futnak. 

A következő kódrészletet az automatikus felhasználó hatókör készlet hatókör meg olyan feladatra, a Batch .NET állítja be. A jogosultságszint-emelés szint nincs megadva, ezért a szabványos készlet kiterjedő automatikus-felhasználói fiók alatt fut a feladat.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Elnevezett felhasználói fiókok

Itt megadhatja, hogy a program elnevezett felhasználói fiókokat, amikor a készletet hoz létre. Egy névvel ellátott felhasználói fiók rendelkezik, egy nevet és jelszót. Megadhatja, hogy a jogosultságszint-emelés szintje egy névvel ellátott felhasználói fiókot. Linux-csomópontok titkos SSH-kulcsot is megadhatja.

Elnevezett felhasználói fiók létezik a készletben található összes csomóponton, és készen áll a azokat a csomópontokat futó összes feladatot. Nevesített felhasználók készlet tetszőleges számú adhatók meg. Egy feladat vagy tevékenység gyűjtemény hozzáadásakor megadhatja, hogy a feladat fut. a készlet a megadott elnevezett felhasználói fiókok közül.

Egy névvel ellátott felhasználói fiókot akkor hasznos, ha szeretne a feladatok az azonos felhasználói fiókhoz tartozó összes feladatok futtatása, de ezeket elszigeteli a más feladatok egy időben futó feladatok. Például minden feladat nevű felhasználót kell létrehozni, és az adott nevű felhasználói fiók minden feladat feladatok futtatásához. Minden feladat dolgozhat ugyanazon a titkos kulcs a saját feladatokhoz, de nem váltanak futó feladatok.

Elnevezett felhasználói fiók segítségével, amely olyan engedélyeket ad meg a külső erőforrások, például fájlmegosztásokat feladat futtatása. Elnevezett felhasználói fiókkal szabályozhatja a felhasználó identitását, és használhatja a felhasználói identitás engedélyek beállítása.  

Nevesített felhasználó fiókok lehetővé teszik a jelszó nélküli SSH Linux-csomópontok között. Többpéldányos feladatok futtatásához szükséges Linux csomópontok használható egy névvel ellátott felhasználói fiókot. A készlet minden egyes csomópont futtathatnak feladatokat a teljes készletében egy felhasználói fiókkal. Többpéldányos feladatokkal kapcsolatos további információkért lásd: [használata többszörös\-MPI-alkalmazások futtatására feladatok példány](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Elnevezett felhasználói fiókok létrehozása

Elnevezett felhasználói fiókok létrehozása a kötegelt, vegye fel a felhasználói fiókok gyűjteménye a készlethez. Az alábbi kódtöredékek bemutatják, hogyan elnevezett felhasználói fiókok létrehozása a .NET, Java és Python. Ezek kódrészletek létrehozását mutatják be mind a rendszergazda, és a nem rendszergazdai fiókok, a készlet neve. A példák segítségével a felhőalapú szolgáltatás konfigurációja címkészletek létrehozása, de a virtuálisgép-konfiguráció használata Windows vagy Linux készlet létrehozásakor használt sémának.

#### <a name="batch-net-example-windows"></a>Batch .NET típusú példát (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                                         
    virtualMachineSize: "small",                                                
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

#### <a name="batch-net-example-linux"></a>Batch .NET típusú példát (Linux)

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


#### <a name="batch-java-example"></a>Kötegelt Java – példa

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

#### <a name="batch-python-example"></a>Kötegelt Python – példa

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Emelt szintű hozzáférés egy névvel ellátott felhasználói fiókhoz tartozó feladat futtatása

Feladat futtatása egy rendszergazda jogú felhasználóként, állítsa be a feladat **UserIdentity** tulajdonság hoztak létre elnevezett felhasználói fiókhoz az **ElevationLevel** tulajdonsága `Admin`.

A kódrészletet határozza meg, hogy a feladat egy névvel ellátott felhasználói fiókkal fusson. A névvel ellátott felhasználói fiókot az készletében van definiálva, a készlet létrehozásakor. Ebben az esetben az elnevezett felhasználói fiók rendszergazdai jogosultságokkal rendelkező jött létre:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Frissítse a kódot a legújabb kötegelt ügyféloldali kódtár

A Batch szolgáltatás 2017-01-01.4.0 tartalmazza az használhatatlanná tévő változást, cseréje a **runElevated** tulajdonság érhető el a korábbi verzióiban a **userIdentity** tulajdonság. A következő táblázatok tartalmazzák, amelyek segítségével frissítse a kódot a klienskódtárak segítségével korábbi verzióiból egyszerű leképezéseket.

### <a name="batch-net"></a>Batch .NET

| Ha a kódot használja...                  | Frissítse úgy, hogy...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Nincs megadva | Nincs frissítés szükséges                                                                                               |

### <a name="batch-java"></a>Kötegelt Java

| Ha a kódot használja...                      | Frissítse úgy, hogy...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Nincs megadva | Nincs frissítés szükséges                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Ha a kódot használja...                      | Frissítse úgy, hogy...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin)) `                |
| `run_elevated=False`                      | `user_identity=user`, ahol <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin)) `             |
| `run_elevated` Nincs megadva | Nincs frissítés szükséges                                                                                                                                  |


## <a name="next-steps"></a>További lépések

### <a name="batch-forum"></a>Batch fórum

A [Azure Batch fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch) az MSDN webhelyen van remek kötegelt tárgyalja, és kérdése van a szolgáltatás. Központi a keresztül a hasznos rögzített bejegyzések, és a kötegelt megoldások létrehozása során felmerülő kérdéseit.