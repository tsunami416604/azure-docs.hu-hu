---
title: "Az Azure Service Fabric-alkalmazás központi telepítése |} Microsoft Docs"
description: "A FabricClient API-k segítségével telepítheti és távolíthat el alkalmazásokat a Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: 6fdf65ac87273502edb1548ed6c1cf5c7bc25649
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Központi telepítése és eltávolítása a FabricClient használó alkalmazások
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Egyszer egy [alkalmazástípus csomagolás][10], az Azure Service Fabric-fürt szolgáltatássablonjaikat használatra kész. Központi telepítés a következő három lépést foglal magában:

1. Az image store az alkalmazáscsomag feltöltése
2. Az alkalmazástípus regisztrálása
3. Az alkalmazáscsomag eltávolítása a lemezképtárolóból
4. Az alkalmazáspéldány létrehozása

Miután egy alkalmazás lett telepítve, és egy példányát a fürtben fut, törölheti az alkalmazáspéldány és az alkalmazás típusa. Az alkalmazás teljes eltávolítása a fürt a következő lépésekből áll:

1. Távolítsa el (vagy törlése) futó alkalmazáspéldány
2. Az alkalmazástípus regisztrációjának törlése, ha már nincs szüksége

Telepítésével és a helyi fejlesztési fürtön lévő alkalmazások hibakeresése a Visual Studio használja, ha az előző lépések kezeli automatikusan egy PowerShell-parancsfájl segítségével.  Ez a parancsfájl megtalálható a *parancsfájlok* mappában található a projektet. Ez a cikk nyújt háttér milyen, hogy a parancsfájl módon, hogy a Visual Studio kívül ugyanazokat a műveleteket végezheti el. 
 
## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Csatlakozzon a fürthöz, hozzon létre egy [FabricClient](/dotnet/api/system.fabric.fabricclient) példány, mielőtt futtatja példák ebben a cikkben. Példák a helyi fejlesztési fürtök vagy egy távoli fürtöt vagy az Azure Active Directoryval, X509 védett fürt csatlakozik a tanúsítványokat, vagy a Windows Active Directory [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Szeretne csatlakozni a helyi fejlesztési fürtöt, futtassa az alábbi parancsot:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Az alkalmazáscsomag feltöltése
Tegyük fel, hogy most felépíti és nevű csomag *MyApplication* a Visual Studióban. Alapértelmezés szerint az alkalmazás szerepel a ApplicationManifest.xml típusneve "MyApplicationType".  Az alkalmazáscsomag, amely tartalmazza a szükséges alkalmazás jegyzékfájlja, a szolgáltatás jegyzékfájlban és a kód/config/adatok csomagok, található *C:\Users\&lt; felhasználónév&gt;\Documents\Visual Studio 2017\Projects\ MyApplication\MyApplication\pkg\Debug*.

Az alkalmazáscsomag feltöltése helyezi a belső Service Fabric összetevői által elérhető helyen. A Service Fabric ellenőrzi az alkalmazáscsomag alkalmazáscsomag a regisztráció során. Ha szeretné ellenőrizni a alkalmazáscsomagot helyileg (azaz feltöltés előtt), használja a [teszt-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsmag.

A [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API feltölti az alkalmazáscsomagot a fürt lemezképtárolóhoz. 

Ha az alkalmazáscsomag nagy és/vagy sok fájl van, akkor [a tömörítés](service-fabric-package-apps.md#compress-a-package) , és másolja az image store PowerShell használatával. A tömörítés csökkenti a méretét és a fájlok száma.

Lásd: [megérteni a lemezkép tárolási kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) az image store és a lemezkép kiegészítő információt tárolja a kapcsolati karakterlánc.

## <a name="register-the-application-package"></a>Az alkalmazáscsomag regisztrálása
Az alkalmazástípus és -verzió az alkalmazás jegyzékében használható válnak, amikor regisztrál az alkalmazáscsomag deklarálva. A rendszer beolvassa az előző lépésben feltöltött csomag, ellenőrzi a csomag, feldolgozza a csomag tartalmát, és a feldolgozott csomag belső rendszer helyre másolja.  

A [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API regiszterekben az alkalmazás írja be a fürt, és tegye elérhetővé a telepítésre.

A [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API sikeresen regisztrált alkalmazástípusokat ismerteti. Ez az API segítségével határozható meg, hogy a regisztrációs történik.

## <a name="remove-an-application-package-from-the-image-store"></a>Az alkalmazáscsomag eltávolítása a lemezképtárolóból
Ajánlott az alkalmazáscsomag eltávolítása után az alkalmazás regisztrálása sikeres volt.  Rendszererőforrások szabaddá alkalmazáscsomagok az image store való törlésekor.  Nem használt alkalmazáscsomagok használ fel a lemezes tárolás és alkalmazást teljesítményproblémák vezet. Az alkalmazáscsomag törlése a tároló a lemezképet a [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Hozzon létre egy alkalmazáspéldányt
Az alkalmazás regisztrálása sikeresen befejeződött a alkalmazás típussal a példányosítható a [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. Minden alkalmazás nevére kell kezdődnie az *"fabric:"* sémáját, és minden alkalmazáspéldányhoz (fürtözött) egyedinek kell lennie. A célalkalmazás típusa alkalmazás jegyzékében definiált alapértelmezett szolgáltatások is jönnek létre.

Több alkalmazáspéldányt is létrehozható egy regisztrált alkalmazástípus bármely adott verzióját. Minden egyes alkalmazáspéldány feladata elkülönítve, a saját munkakönyvtár és a folyamatok.

Megtekintéséhez, amely nevű alkalmazások és szolgáltatások futnak a fürthöz, futtassa a [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) és [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API-k.

## <a name="create-a-service-instance"></a>A szolgáltatáspéldány létrehozása
Adott szolgáltatásnak a szolgáltatás típus használatával példányosítható a [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API.  Ha a szolgáltatás alapértelmezett szolgáltatásként az alkalmazásjegyzékben van deklarálva, a szolgáltatás létrejön, amikor az alkalmazás létrejön.  Hívja a [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API-t a már létrehozott egy szolgáltatást fog visszaadni a FabricErrorCode.ServiceAlreadyExists értékű hiba kódot tartalmazó FabricException típusú kivételt.

## <a name="remove-a-service-instance"></a>A szolgáltatás példányának eltávolítása
Amikor egy szolgáltatáspéldány már nem szükséges, eltávolíthatja a futását meghívásával alkalmazáspéldány a [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Ez a művelet nem vonható vissza, és a szolgáltatás állapota nem állítható helyre.

## <a name="remove-an-application-instance"></a>Az alkalmazáspéldány eltávolítása
Az alkalmazáspéldány nincs szükség, ha véglegesen eltávolíthatja azt név használatával a [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatikusan eltávolítja az is, végleges eltávolítása az összes szolgáltatás állapota az alkalmazáshoz tartozó összes szolgáltatás.

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

## <a name="unregister-an-application-type"></a>Az alkalmazástípus regisztrációjának törlése
Amikor egy alkalmazás típus adott verziójának már nem szükséges, az alkalmazás típust használja, hogy adott verziójának kell regisztrációjának törlése a [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API. Az image store által felhasznált tárterület beállításjegyzékből való törlésekor alkalmazástípus nem használt verziók kiadását. Egy alkalmazástípus verziója mindaddig, amíg nincs alkalmazások létrehozásának ellen, hogy az alkalmazástípus verziója, és nincs függőben lévő alkalmazásfrissítések hivatkoznak, hogy az alkalmazástípus verziója lehet regisztrációjának törlése.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Másolás-ServiceFabricApplicationPackage kér egy előtaggal
A Service Fabric SDK környezet már rendelkezik a megfelelő alapértelmezett beállítása. De ha szükséges, az összes parancs előtaggal meg kell felelnie a Service Fabric fürt által használt érték. A előtaggal megtalálható a fürtjegyzékben le kérni a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) és Get-ImageStoreConnectionStringFromClusterManifest parancsokat:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmagot, amely a Service Fabric SDK PowerShell modulja részét képezi, a lemezkép tárolási kapcsolati karakterlánc beolvasása szolgál.  Az SDK modul importálásához futtassa:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


A előtaggal megtalálható a fürtjegyzékben:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Lásd: [megérteni a lemezkép tárolási kapcsolati karakterlánc](service-fabric-image-store-connection-string.md) az image store és a lemezkép kiegészítő információt tárolja a kapcsolati karakterlánc.

### <a name="deploy-large-application-package"></a>Nagy alkalmazáscsomag telepítése
Probléma: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) a nagy alkalmazáscsomagok (GB-os sorrendben) időtúllépése API.
Próbálja meg:
- Adjon meg egy nagyobb időkorlátjának [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metódus, a `timeout` paraméter. Alapértelmezés szerint az időtúllépés értéke 30 perc.
- Ellenőrizze a hálózati kapcsolat a forrásgép és fürt között. Ha a kapcsolat lassú, érdemes lehet a gépek nagyobb hálózati kapcsolattal rendelkező.
Ha az ügyfélszámítógép mint a fürt egy másik régióban van, érdemes lehet egy ügyfélszámítógépre egy szorosabb vagy ugyanabban a régióban a fürttel.
- Ellenőrizze, hogy ha hogy elérte-e külső szabályozás. Például ha az image store az azure storage használatára van konfigurálva, feltöltés előfordulhat, hogy szabályozva.

Probléma: Feltöltés csomag sikeresen befejeződött, de [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API túllépi az időkorlátot. Próbálja meg:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) az image store másolás előtt.
A tömörítés csökkenti a, és a fájlok, számát, amely pedig csökkenti a forgalom mennyiségét, és működnek, hogy a Service Fabric kell elvégezni. Lehet, hogy a feltöltési művelet lassabb (különösen ha a tömörítés idő), de a regisztrálása és az alkalmazástípus regisztrációjának gyorsabb.
- Adjon meg egy nagyobb időkorlátjának [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API-t `timeout` paraméter.

### <a name="deploy-application-package-with-many-files"></a>Sok fájlokkal alkalmazáscsomag telepítése
Probléma: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) sok fájlt (akár több ezer sorrendben) az alkalmazás csomagot időtúllépése.
Próbálja meg:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) az image store másolás előtt. A tömörítés csökkenti a fájlok száma.
- Adjon meg egy nagyobb időkorlátjának [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) a `timeout` paraméter.

## <a name="code-example"></a>Mintakód
Az alábbi példa alkalmazáscsomag másolja az image store, látja el az alkalmazás típusa, létrehoz egy alkalmazáspéldányt, létrehoz egy szolgáltatáspéldány, eltávolítja az alkalmazáspéldány, un-kiosztja az alkalmazástípus és törli a a lemezképtárolóból alkalmazáscsomagot.

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

## <a name="next-steps"></a>Következő lépések
[A Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

[A Service Fabric állapotának bemutatása](service-fabric-health-introduction.md)

[Diagnosztizálása és megoldása a Service Fabric-szolgáltatás](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric alkalmazás minta](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
