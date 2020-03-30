---
title: Önálló fürt verziójának frissítése
description: Frissítse az Azure Service Fabric-kódot, amely egy önálló Service Fabric-fürtöt futtat.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 489a90180454e2b4a9dad34730fbd3c4f235a2ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598102"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>A fürtön futó Service Fabric-verzió frissítése 

Minden modern rendszer, a frissítés képessége kulcsfontosságú a hosszú távú sikere a termék. Az Azure Service Fabric-fürt egy saját erőforrás. Ez a cikk ismerteti, hogyan frissítheti a Service Fabric önálló fürtön futó verzióját.

> [!NOTE]
> Győződjön meg arról, hogy a fürt mindig egy támogatott Service Fabric-verziót futtat. Amikor a Microsoft bejelenti a Service Fabric új verziójának kiadását, az előző verzió a bejelentés dátumától számított legalább 60 nap elteltével a támogatás megszűnése után a támogatás megszűnése jelzővel van ellátva. Új kiadások vannak jelentették be [a Service Fabric csapat blog](https://blogs.msdn.microsoft.com/azureservicefabric/). Az új kiadás ezen a ponton választható.
>
>

A fürt csak akkor frissítheti a fürtöt az új verzióra, ha éles stílusú csomópont-konfigurációt használ, ahol minden Egyes Service Fabric-csomópont egy külön fizikai vagy virtuális gépen van lefoglalva. Ha egy fejlesztési fürt, ahol több, mint egy Service Fabric-csomópont egyetlen fizikai vagy virtuális gépen, újra létre kell hoznia a fürtöt az új verzióval.

Két különböző munkafolyamat frissítheti a fürtöt a legújabb verzióra vagy egy támogatott Service Fabric-verzióra. Az egyik munkafolyamat a kapcsolattal rendelkező fürtök számára a legújabb verzió automatikus letöltéséhez. A másik munkafolyamat olyan fürtökhöz készült, amelyek nem rendelkeznek kapcsolattal a Service Fabric legújabb verziójának letöltéséhez.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>A fürt Service Fabric-verziójának automatikus frissítésének engedélyezése
Ha azt szeretné, hogy a fürt töltse le a Service `fabricClusterAutoupgradeEnabled` Fabric frissítéseit, amikor a Microsoft új verziót ad ki, állítsa a fürtkonfigurációt *true*értékre. Manuálisan válassza ki a Service Fabric támogatott verzióját, amelyen `fabricClusterAutoupgradeEnabled` a fürtöt be szeretné kapcsolni, állítsa a fürtkonfigurációt *false*értékűre.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>A legújabb kód és konfiguráció letöltéséhez kapcsolattal rendelkező fürtök frissítése
Ezekkel a lépésekkel frissítheti a fürtöt támogatott verzióra, ha a fürtcsomópontok internetkapcsolattal rendelkeznek a [Microsoft letöltőközponttal.](https://download.microsoft.com)

A [Microsoft letöltőközponttal](https://download.microsoft.com)kapcsolatban álló fürtök esetében a Microsoft rendszeres en rendszeresen ellenőrzi az új Service Fabric-verziók elérhetőségét.

Ha egy új Service Fabric-verzió érhető el, a csomag helyileg töltődik le a fürtbe, és kivan építve frissítésre. Továbbá, hogy tájékoztassa az ügyfelet az új verzióról, a rendszer egy explicit fürtállapot-figyelmeztetést jelenít meg, amely hasonló a következőkhöz:

"A jelenlegi fürtverzió [version #] támogatás véget ér [dátum]."

Miután a fürt a legújabb verziót futtatja, a figyelmeztetés eltűnik.

Amikor megjelenik a fürt állapotra vonatkozó figyelmeztetése, frissítse a fürtöt:

1. Csatlakozzon a fürthöz bármely olyan gépről, amely rendszergazdai hozzáféréssel rendelkezik a fürt csomópontjaiként felsorolt összes számítógéphez. A gép, amelyen ez a parancsfájl fut, nem kell a fürt része.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. A Service Fabric-verziók listájának beszereznie, amelyekre frissíthet.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Ehhez hasonló kimenetet kell kapnia:

    ![Service Fabric-verziók beszereznie][getfabversions]
3. Fürtfrissítés indítása elérhető verzióra a [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell paranccsal.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   A frissítés előrehaladásának figyeléséhez használja a Service Fabric Explorer t, vagy futtassa a következő PowerShell parancsot:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva. A Start-ServiceFabricClusterUpgrade parancs egyéni állapotházirendjének megadásához olvassa el a [Start-ServiceFabricClusterUpgrade dokumentációját.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade)

    A visszaállítást eredményező problémák megoldása után indítsa el újra a frissítést a korábban leírt lépések végrehajtásával.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Frissítse azokat a fürtöket, amelyek nem rendelkeznek *kapcsolattal* a legújabb kód és konfiguráció letöltéséhez
Ezekkel a lépésekkel frissítheti a fürtöt támogatott verzióra, ha a fürtcsomópontok nem rendelkeznek internetkapcsolattal a [Microsoft letöltőközponthoz.](https://download.microsoft.com)

> [!NOTE]
> Ha olyan fürtöt futtat, amely nem csatlakozik az internethez, figyelnie kell a [Service Fabric csapatblogját](https://blogs.msdn.microsoft.com/azureservicefabric/) az új kiadások megismeréséhez. A rendszer nem jelenít meg fürtállapot-figyelmeztetést, amely figyelmezteti az új kiadásokra.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatikus kiépítés és kézi kiépítés
A legújabb kódverzió automatikus letöltésének és regisztrációjának engedélyezéséhez állítsa be a Service Fabric update szolgáltatást. További információt az önálló csomag *Eszközök\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* [fájljában](service-fabric-cluster-standalone-package-contents.md)talál.

A kézi folyamathoz kövesse az alábbi utasításokat.

Módosítsa a fürtkonfigurációt úgy, hogy a következő tulajdonság *hamis* ra állítsa a konfigurációs frissítés megkezdése előtt:

```json
"fabricClusterAutoupgradeEnabled": false,
```

A használattal kapcsolatos részleteket a [Start-ServiceFabricConfigurationConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell című témakörben találja. A konfigurációs frissítés megkezdése előtt frissítse a "clusterConfigurationVersion" szolgáltatást a JSON-ban.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Fürtfrissítési munkafolyamat

1. [Futtassa a Get-ServiceFabricClusterUpgrade szolgáltatást](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) a fürt egyik csomópontjáról, és jegyezze fel a *TargetCodeVersion verziót.*

2. Futtassa az alábbiakat egy internethez csatlakoztatott gépről, hogy felsorolja az összes frissítéssel kompatibilis verziót az aktuális verzióval, és töltse le a megfelelő csomagot a kapcsolódó letöltési hivatkozásokról:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Csatlakozzon a fürthöz bármely olyan gépről, amely rendszergazdai hozzáféréssel rendelkezik a fürt csomópontjaiként felsorolt összes számítógéphez. A gép, amelyen ez a parancsfájl fut, nem kell a fürt része.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Másolja a letöltött csomagot a fürtképtárolóba.

5. Regisztrálja a másolt csomagot.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Fürtfrissítés indítása elérhető verzióra.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Figyelheti a frissítés előrehaladását a Service Fabric Intézőben, vagy futtathatja a következő PowerShell parancsot:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapotházirendjei nem teljesülnek, a frissítés visszalesz állítva. A Start-ServiceFabricClusterUpgrade parancs egyéni állapotházirendjének megadásához olvassa el a [Start-ServiceFabricClusterUpgrade dokumentációját.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade)

    A visszaállítást eredményező problémák megoldása után indítsa el újra a frissítést a korábban leírt lépések végrehajtásával.

## <a name="next-steps"></a>További lépések
* [Önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md)
* A [Service Fabric egyes fürtbeállításainak](service-fabric-cluster-fabric-settings.md)testreszabása .
* [Méretezheti a fürtöt be- és ki.](service-fabric-cluster-scale-up-down.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
