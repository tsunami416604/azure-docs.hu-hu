---
title: Az Azure Service Fabric-alkalmazás központi telepítésének |} A Microsoft Docs
description: A FabricClient API-k használatával üzembe helyezése és távolíthat el alkalmazásokat a Service Fabricben.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 9b3641ddd9d27c0ffa18e62f317d7a8c8ecb6eb3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834931"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Üzembe helyezése, és távolítsa el a FabricClient használó alkalmazások
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Miután egy [alkalmazástípus a csomagolás][10], üzembe helyezését az Azure Service Fabric-fürt használatra kész. Központi telepítés a következő három lépésből áll:

1. Az alkalmazáscsomag feltöltése a lemezképtároló
2. Regisztrálja az alkalmazás típusát
3. Az alkalmazás-csomag eltávolítása a lemezképtároló
4. Az alkalmazás-példány létrehozása

Miután egy alkalmazás központi telepítése, és a egy példányt a fürt fut-e, törölheti az alkalmazáspéldány és az alkalmazás típusát. Teljes mértékben az alkalmazás eltávolítása a fürtből, a következő lépésekből áll:

1. Távolítsa el (vagy törli) a futó alkalmazás példánya
2. Törölje az alkalmazástípus regisztrációját, ha már nincs szüksége

Ha üzembe helyezéséhez és a helyi fejlesztési fürtön futó alkalmazások hibakeresése a Visual Studio használja, az előző lépések egy PowerShell-parancsfájl segítségével automatikusan kezelése történik.  Ez a szkript megtalálható a *parancsfájlok* mappát a projekt. Ez a cikk milyen a szkript módon, hogy ugyanazokat a műveleteket, Visual Studión kívül is elvégezheti a háttérben futó biztosít. 
 
## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Csatlakozhat a fürthöz, hozzon létre egy [FabricClient](/dotnet/api/system.fabric.fabricclient) példány bármely példák ebben a cikkben futtatása előtt. Példák egy helyi fejlesztési fürt vagy távoli fürtön vagy az Azure Active Directoryval, X509 védett fürthöz csatlakozik a tanúsítványokat, vagy a Windows Active Directory [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Szeretne csatlakozni a helyi fejlesztési fürtöt, futtassa a következőt:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Az alkalmazáscsomag feltöltése
Tegyük fel, hogy hozhat létre, és nevű alkalmazás becsomagolása *MyApplication* a Visual Studióban. Alapértelmezés szerint az alkalmazástípus neve szerepel az ApplicationManifest.xml "MyApplicationType".  Az alkalmazáscsomagot, amely tartalmazza a szükséges alkalmazás jegyzékfájlja, szolgáltatásjegyzékek és kód/config/data csomagok, található *C:\Users\&lt; felhasználónév&gt;\Documents\Visual Studio 2017\Projects\ MyApplication\MyApplication\pkg\Debug*.

Az alkalmazáscsomag feltöltése helyezi a belső Service Fabric összetevői által elérhető helyen. A Service Fabric az alkalmazáscsomag ellenőrzi az alkalmazáscsomag regisztrációja során. Azonban ha azt szeretné, az alkalmazáscsomagot helyileg ellenőrzése (azaz a feltöltése előtt), használja a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsmagot.

A [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API feltölti az alkalmazáscsomag a fürt lemezképtárolójába. 

Ha az alkalmazáscsomag nagy és/vagy sok fájl van, akkor [a tömörítés](service-fabric-package-apps.md#compress-a-package) , és másolja a lemezképtároló PowerShell használatával. A tömörítés csökkenti a méretét és a fájlok száma.

Lásd: [megismerheti a lemezképet tároló kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) kiegészítő információk a lemezképtároló és lemezképet tárolja a kapcsolati karakterláncot.

## <a name="register-the-application-package"></a>Az alkalmazáscsomag regisztrálása
Az alkalmazás típusát és verzióját deklarálva az alkalmazásjegyzékben, legyen az alkalmazáscsomag regisztrációja esetén használható. A rendszer beolvassa a csomag az előző lépésben feltöltött, ellenőrzi a csomagot, feldolgozza a csomag tartalmát és a feldolgozott csomag egy belső helyére másolja.  

A [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API regisztrál az alkalmazás írja be a fürt és a központi telepítés elérhetővé.

A [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API sikeresen regisztrált alkalmazástípusokat ismerteti. Ez az API segítségével határozható meg, hogy a regisztráció történik.

## <a name="remove-an-application-package-from-the-image-store"></a>A lemezképtároló alkalmazáscsomag eltávolítása
Ajánlott az alkalmazáscsomag törlése után az alkalmazás regisztrálása sikeres volt.  Rendszer-erőforrásokat szabadít alkalmazáscsomagok törlése a képet tárból.  A fel nem használt alkalmazáscsomagok lemezes tárolást használ fel, és alkalmazásteljesítménnyel kapcsolatos problémák vezet. Az alkalmazáscsomag törlése a lemezképet tároló használatával a [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API-t.

## <a name="create-an-application-instance"></a>Egy alkalmazás-példány létrehozása
Tetszőleges alkalmazástípus használatával sikeresen regisztrált egy alkalmazás példányosítható a [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API-t. Minden alkalmazás nevére kell kezdődnie az *"fabric:"* séma, és minden egyes alkalmazás-példány (fürtözött) egyedinek kell lennie. Az alkalmazásjegyzékben, a célalkalmazás típusa definiálva alapértelmezett szolgáltatások is jönnek létre.

Több alkalmazáspéldány is létrehozható egy regisztrált alkalmazástípus bármely adott verzióját. Mindegyik alkalmazáspéldány elkülönítve, a saját munkakönyvtár és a folyamatok készlete fut.

Megtekintéséhez, amely nevű alkalmazások és szolgáltatások futnak a fürthöz, futtassa a [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) és [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API-k.

## <a name="create-a-service-instance"></a>Szolgáltatáspéldány létrehozása
Egy szolgáltatás a szolgáltatás típus használatával példányosítható a [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API-t.  Ha a szolgáltatás alapértelmezett szolgáltatásként az alkalmazásjegyzékben deklarálva van, a szolgáltatás létrejön, amikor létrejön az alkalmazás.  Hívása a [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) egy szolgáltatás, amely már van példányosítva API hibakódot FabricErrorCode.ServiceAlreadyExists értéket tartalmazó FabricException típusú kivételt ad vissza.

## <a name="remove-a-service-instance"></a>Szolgáltatáspéldány eltávolítása
Amikor egy szolgáltatáspéldány már nincs rá szükség, eltávolíthatja a futását meghívásával alkalmazáspéldány a [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API-t.  

> [!WARNING]
> Ez a művelet nem vonható vissza, és a szolgáltatás állapota nem állítható helyre.

## <a name="remove-an-application-instance"></a>Egy alkalmazáspéldány eltávolítása
Egy alkalmazáspéldány már nincs rá szükség, amikor véglegesen eltávolíthatja név használatával a [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API-t. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatikusan eltávolítja az összes szolgáltatás, amely az alkalmazás is, véglegesen eltávolítja az összes szolgáltatás állapota tartozik.

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

## <a name="unregister-an-application-type"></a>Törölje az alkalmazástípus regisztrációját
Egy adott verzióját az alkalmazástípus már nincs rá szükség, ha akkor törölje regisztrációját, hogy az alkalmazást írja be a konkrét verziója a [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API-t. A lemezképtároló által felhasznált lemezterület regisztrációjának törlése a fel nem használt verziókat alkalmazástípusok kiadások. Egy az alkalmazástípus verziója nem regisztrált lehet mindaddig, amíg nem találhatók alkalmazások ellen, hogy az alkalmazástípus verziója példányosítása, és nincs függőben lévő alkalmazásfrissítések hivatkoznak az alkalmazás típusának megfelelő verziója.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Az ImageStoreConnectionString másolási-ServiceFabricApplicationPackage kéri
A Service Fabric SDK környezet már rendelkezik a megfelelő alapértelmezett beállítása. De ha szükséges, az összes parancsra vonatkozó ImageStoreConnectionString meg kell egyeznie a Service Fabric-fürt által használt érték. Az ImageStoreConnectionString találhatja meg a fürtjegyzék hitelesítő adatokat használja a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) és a Get-ImageStoreConnectionStringFromClusterManifest parancsok:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag a Service Fabric SDK PowerShell-modul részét képező kéri le a lemezképet tároló kapcsolati karakterláncot.  Az SDK modul importálásához futtassa:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


Az ImageStoreConnectionString megtalálható a fürtjegyzék:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Lásd: [megismerheti a lemezképet tároló kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) kiegészítő információk a lemezképtároló és lemezképet tárolja a kapcsolati karakterláncot.

### <a name="deploy-large-application-package"></a>Nagy alkalmazáscsomag telepítése
Probléma: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API túllépi az időkorlátot egy nagy alkalmazáscsomag (GB-os sorrendben).
Próbálja ki:
- Adja meg a próbáljon nagyobb időtúllépést [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metódus az `timeout` paraméter. Alapértelmezés szerint az időtúllépési érték 30 perc.
- Ellenőrizze a hálózati kapcsolat a forrásgép és a fürt között. Ha a kapcsolat lassú, fontolja meg egy jobb hálózati kapcsolattal rendelkező gép használatával.
Ha az ügyfélszámítógépen, mint a fürt egy másik régióban található, fontolja meg egy ügyfélszámítógépre egy közelebb vagy ugyanabban a régióban a fürttel.
- Ellenőrizze, hogy elérte-e külső szabályozás. Ha például az image store az azure storage használatára van konfigurálva, amikor feltöltése előfordulhat, hogy folyamatban.

Probléma: Töltse fel a csomag sikeresen befejeződött, de [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API túllépi az időkorlátot. Próbálja ki:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a lemezképtároló másolás előtt.
A tömörítés csökkenti a méretét, és végezze el a fájlok, számát, ami viszont forgalom mennyisége csökkenti, és működik, hogy a Service Fabric. Lehet, hogy a feltöltési művelet lassabb (különösen ha adja meg a tömörítés idő), de gyorsabb regisztrálása és az alkalmazástípus regisztrációját.
- Adja meg a próbáljon nagyobb időtúllépést [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API-t `timeout` paraméter.

### <a name="deploy-application-package-with-many-files"></a>Sok fájlt az alkalmazáscsomag telepítése
Probléma: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) túllépi az időkorlátot egy alkalmazáscsomag fájllal (több ezer sorrendben).
Próbálja ki:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a lemezképtároló másolás előtt. A tömörítés csökkenti a fájlok száma.
- Adja meg a próbáljon nagyobb időtúllépést [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) a `timeout` paraméter.

## <a name="code-example"></a>Mintakód
Az alábbi példa egy alkalmazáscsomagot átmásolja a lemezképtároló, látja el az alkalmazás típusát, alkalmazáspéldány hoz létre, létrehoz egy szolgáltatáspéldány, eltávolítja az alkalmazáspéldány, un-kiosztja az alkalmazástípus és törli a a lemezképtároló az alkalmazáscsomagot.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>További lépések
[Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

[A Service Fabric health bemutatása](service-fabric-health-introduction.md)

[Diagnosztika és hibaelhárítás a Service Fabric-szolgáltatás](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric-alkalmazás minta](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
