---
title: "A Windows Server önálló Azure Service Fabric fürt frissítése |} Microsoft Docs"
description: "Frissítse az Azure Service Fabric-kód és/vagy a konfigurációs rendszert futtató önálló Service Fabric-fürt, beleértve a fürt frissítési mód beállítása."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: c95c1827d0433dcb61eace34e7a905a5610c7781
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Az önálló Azure Service Fabric-fürt Windows Server frissítése 
> [!div class="op_single_selector"]
> * [Azure-fürttel](service-fabric-cluster-upgrade.md)
> * [Önálló fürthöz](service-fabric-cluster-upgrade-windows-server.md)
>
>

Bármely, modern rendszer frissítése nem a hosszú távú sikeres, a termék kulcs. Az Azure Service Fabric-fürt saját erőforrás. Ez a cikk ismerteti, hogyan biztosíthatja, hogy a fürt mindig fut a Service Fabric-kódot és konfigurációk támogatott verzióit.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>A Service Fabric-verzió a fürtön futó vezérlő
A Service Fabric frissítéseinek letöltését, amikor a Microsoft által kiadott új verziójára a fürt beállításához beállítása fabricClusterAutoupgradeEnabled fürt *igaz*. Válassza ki a Service Fabric-on szeretné lebonyolítani a fürt által támogatott verzióját, adja meg a fabricClusterAutoupgradeEnabled fürtkonfiguráció *hamis*.

> [!NOTE]
> Győződjön meg arról, hogy a fürt mindig a Service Fabric támogatott verzióját futtatja. A Microsoft a Service Fabric verziójának megjelenésével időről, ha az előző verzió legalább a közlemény számított 60 nap után végéhez van megjelölve. Új kiadásokat történik bejelentés [a Service Fabric csapat blogjában](https://blogs.msdn.microsoft.com/azureservicefabric/). Ezen a ponton kiválasztása az új kiadási érhető el.
>
>

A fürt frissítheti az új verzió csak akkor, ha használ egy éles stílusú csomópont-konfiguráció, ahol minden Service Fabric-csomópont van lefoglalva, külön fizikai vagy virtuális gépen. Ha a fejlesztési fürtöt, ahol egynél több Service Fabric-csomópont nem egyetlen fizikai vagy virtuális gépen, kell újból létrehoznia az új verzióját futtató fürtökre.

Két, különböző munkafolyamatokat a fürt frissítsen a legújabb verzióra, vagy a Service Fabric támogatott verziója. Egy munkafolyamat automatikusan a legújabb verzió letöltéséhez és fürtöknél van. A más munkafolyamat van fürtök, amelyek nem rendelkeznek a kapcsolatot a Service Fabric legújabb verziójának letöltéséhez.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Fürtökben, amelyek kapcsolódik, töltse le a legfrissebb kódot és konfiguráció frissítése
Ezen lépések segítségével frissítse a fürt valamelyik támogatott verzióra, ha a fürtcsomópontok kapcsolódik az internethez való a [Microsoft Download Center](http://download.microsoft.com).

A fürtök, amely kapcsolódik a [Microsoft Download Center](http://download.microsoft.com), a Microsoft rendszeres időközönként új Service Fabric-verziók rendelkezésre állását ellenőrzi.

Ha egy új Service Fabric-verzió érhető el, a csomag tölt le helyileg, a fürtre és frissítés kiépítve. Emellett az új verzió az ügyfél tájékoztatja, a rendszer jeleníti meg egy explicit fürt állapotfigyelési figyelmeztetése a következőhöz hasonló:

"Az aktuális fürt verziója [verzió #] támogatás [dátum] megszűnik."

Miután a fürt a legújabb verziója fut, a figyelmeztetés eltűnik.

#### <a name="cluster-upgrade-workflow"></a>A fürt frissítésének munkafolyamata
Amikor megjelenik a fürt állapotfigyelési figyelmeztetése, tegye a következőket:

1. Csatlakozzon a fürthöz, a fürtben csomópontként felsorolt összes gép rendszergazdai jogosultságokkal rendelkező bármely számítógépről. Ezt a parancsfájlt a gép nem rendelkezik a fürt részeként.

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

2. A Service Fabric-verziók frissíthet listájának lekérdezése.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Kimenettel kell kapnia hasonló:

    ![A Service Fabric-verziók beolvasása][getfabversions]
3. A fürt egy elérhető verziójára való frissítést használatával indítsa el a [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) Windows PowerShell-parancsot.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   A frissítés állapotának figyeléséhez, használhatja a Service Fabric Explorer vagy a következő PowerShell-parancsot:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Egyéni házirendek a Start-ServiceFabricClusterUpgrade parancs megadásához a dokumentációban [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

A problémák, amelyek a visszaállítási kijavítása után kezdeményezése a frissítés ismét a korábban leírt lépéseket.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Frissítés fürtöknél *kapcsolat* letölteni a legújabb kód és a konfiguráció
Ezen lépések segítségével frissítse a fürt egy támogatott verzióra, ha a fürtcsomópontok nincs internetkapcsolat, hogy a [Microsoft Download Center](http://download.microsoft.com).

> [!NOTE]
> Ha fut a fürtön, amely nem kapcsolódik az internethez, akkor figyelése a Service Fabric-csapat blogja tájékozódhat az új kiadásokat. A rendszer nem jeleníti meg a fürt állapotfigyelési figyelmeztetése riasztásokat az új kiadásokat.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Az automatikus és manuális létesítési kiépítése
Automatikus letöltés és nyilvántartási kód legfrissebb engedélyezéséhez állítsa be a Service Fabric-frissítés szolgáltatás. Útmutatásért lásd a belső Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt a [önálló csomag](service-fabric-cluster-standalone-package-contents.md).
A manuális folyamat kövesse az alábbi utasításokat.

A fürt konfigurációját, és a következő tulajdonság módosítása *hamis* egy konfigurációs frissítés megkezdése előtt:

        "fabricClusterAutoupgradeEnabled": false,

A használat részleteiről lásd: a [Start-ServiceFabricClusterConfigurationUpgrade PowerShell-paranccsal](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Ne felejtse el frissíteni a "clusterConfigurationVersion" a JSON-ban, a konfiguráció frissítés megkezdése előtt.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>A fürt frissítésének munkafolyamata

1. Futtassa a Get-ServiceFabricClusterUpgrade a fürt a csomópontok egyikét, és jegyezze fel a TargetCodeVersion.

2. Futtassa a következő listán minden frissítés-kompatibilis verzió a jelenlegi verzióval, és a megfelelő csomag letölteni a letöltési hivatkozás internetkapcsolat gépből:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Csatlakozzon a fürthöz, a fürtben csomópontként felsorolt összes gép rendszergazdai jogosultságokkal rendelkező bármely számítógépről. Ezt a parancsfájlt a gép nem rendelkezik a fürt részeként.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. A letöltött csomagot átmásolja a fürt lemezképtárolóhoz.

5. A másolt csomag regisztrálása.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Indítsa el a fürt frissítését elérhető verziójára.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Figyelheti a feladat előrehaladását a frissítés a Service Fabric Explorer, vagy futtassa a következő PowerShell-parancsot:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Adja meg a Start-ServiceFabricClusterUpgrade parancs egyéni állapotházirendeket, olvassa el a dokumentációt a [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

A problémák, amelyek a visszaállítási kijavítása után kezdeményezése a frissítés ismét a korábban leírt lépéseket.


## <a name="upgrade-the-cluster-configuration"></a>A fürt konfigurációjának frissítése
A konfigurációs frissítés elindítása előtt az új fürt konfigurációs JSON tesztelheti az önálló csomag a következő PowerShell-parancsfájl futtatásával:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Vagy ha ezt a parancsfájlt:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Bizonyos konfigurációk nem lehet frissíteni, például a végpontok, a fürt nevét, a csomópont IP stb. Az új fürt konfigurációs JSON szemben a régit tesztelni, és hibákat jelez a PowerShell-ablakban, ha probléma van.

A Fürtfrissítés konfigurációs frissítéséhez futtassa a Start-ServiceFabricClusterConfigurationUpgrade. A konfigurációs frissítés frissítési tartomány által feldolgozott frissítési tartományban.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Fürtök tanúsítvány konfiguráció frissítése  
A fürt tanúsítvány fürtcsomópontok közötti hitelesítéshez használt. A tanúsítványok leváltása extra körültekintően kell hajtható végre, mert hiba megakadályozza a fürtcsomópontok közötti kommunikáció.

Technikai szempontból négy beállítások támogatottak:  

* Egyetlen tanúsítvány frissítés: A frissítési elérési út (elsődleges) -> tanúsítvány B (elsődleges) tanúsítvány -> tanúsítvány C (elsődleges) ->...

* Kettős tanúsítvány frissítés: A frissítési elérési út (elsődleges) -> Tanúsítvány tanúsítvány-(elsődleges) és a B (másodlagos) -> tanúsítvány B (elsődleges) -> tanúsítvány B (elsődleges), és C (másodlagos) -> tanúsítvány C (elsődleges) ->...

* Tanúsítvány típusa frissítés: a tanúsítvány ujjlenyomata-alapú konfigurációs <> – Köznapinév-alapú tanúsítvány konfigurálását. Például a tanúsítvány ujjlenyomata (elsődleges) és az ujjlenyomat B (másodlagos) tanúsítvány Köznapinév c ->

* A tanúsítvány kiállítójának ujjlenyomata frissítés: A frissítési elérési út a tanúsítvány CN = A, IssuerThumbprint et1 = (elsődleges) tanúsítvány CN -> = A, IssuerThumbprint et1, et2 = (elsődleges) tanúsítvány CN -> A, IssuerThumbprint = et2 = (elsődleges).


## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan szabhatja testre az egyes [Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).
* Megtudhatja, hogyan [a fürt vagy horizontális](service-fabric-cluster-scale-up-down.md).
* További tudnivalók [alkalmazásfrissítések](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
