---
title: A Windows Server önálló Azure Service Fabric-fürt frissítése |} A Microsoft Docs
description: Frissítse az Azure Service Fabric-kód és/vagy a konfigurációt, amely egy önálló Service Fabric-fürt a fürt frissítési mód beállításával együtt futtatja.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 2190978b2583b2f5d8a1b10431c65fd24fe6842d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228147"
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Frissítse az önálló Azure Service Fabric-fürt Windows Serveren 
> [!div class="op_single_selector"]
> * [Azure-fürtben](service-fabric-cluster-upgrade.md)
> * [Önálló fürt](service-fabric-cluster-upgrade-windows-server.md)
>
>

Minden olyan modern rendszerbe arra, hogy frissítse a termék hosszú távú sikeréhez kulcsfontosságú, hogy. Azure Service Fabric-fürt saját erőforrás. Ez a cikk bemutatja, hogyan biztosíthatja, hogy a a fürt mindig fut-e a támogatott verziók a Service Fabric-kódra és konfigurációkat.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Vezérlő a Service Fabric verziója, amely a fürt fut
A fürt Service fabric-frissítések letöltése a Microsoft által kiadott új verzió beállításához fabricClusterAutoupgradeEnabled fürt konfigurációjának beállítása *igaz*. Válassza ki a fürt a használni kívánt Service Fabric egy támogatott verzióját, állítsa a fabricClusterAutoupgradeEnabled fürtkonfiguráció *hamis*.

> [!NOTE]
> Győződjön meg arról, hogy a fürt mindig fut a Service Fabric támogatott verziója. A kiadás a Service Fabric egy új verzióját a Microsoft bejelenti, ha az előző verziója legalább a közlemény 60 nap után támogatásuk van megjelölve. Új kiadásokkal már bejelentettünk [a Service Fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/). Ezen a ponton kiválasztása az új kiadásban érhető el.
>
>

A fürt válthat az új verzió csak akkor, ha használ egy éles stílusú csomópont-konfiguráció, ahol minden Service Fabric-csomópont egy külön fizikai vagy virtuális gép van lefoglalva. Ha rendelkezik egy fejlesztési fürtöt, ahol egynél több Service Fabric-csomópont van egyetlen fizikai vagy virtuális gépen, újra létre kell hoznia a fürtöt az új verzióval.

Két különböző munkafolyamatok frissíthetők a fürt a legújabb vagy támogatott a Service Fabric verziója. Egy munkafolyamat olyan fürtök, amelyek a legújabb verzió letöltéséhez automatikusan csatlakozik. A többi munkafolyamat olyan fürtök, amelyek nem rendelkeznek a kapcsolatot a Service Fabric legújabb verzió letöltéséhez.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Töltse le a legújabb kódot és konfigurációs kapcsolati fürtök frissítése
Kövesse az alábbi lépéseket egy támogatott verziójára frissítse a fürtöt, ha a fürtcsomópontok rendelkezik internetkapcsolattal a [Microsoft Download Center](https://download.microsoft.com).

A fürtök, amely rendelkezik a [Microsoft Download Center](https://download.microsoft.com), a Microsoft rendszeresen új Service Fabric-verziók rendelkezésre állását ellenőrzi.

Ha egy új Service Fabric-verzió érhető el, a csomag helyileg töltött le a fürt és frissítés kiépítve. Emellett tájékoztatja az ügyfél az új verzió, a rendszer jelenít meg egy explicit fürt állapotfigyelési figyelmeztetése, amely a következőhöz hasonló:

"Az aktuális fürt verziója [verzió #] támogatás vége [date]."

Miután a fürt fut. a legújabb verzióra, a figyelmeztetés ami újbóli próbálkozással megszűnik.

#### <a name="cluster-upgrade-workflow"></a>A fürt frissítésének munkafolyamata
Amikor megjelenik a fürt állapotfigyelési figyelmeztetése, tegye a következőket:

1. A fürtben csomópontként felsorolt összes gép rendszergazdai hozzáféréssel rendelkező bármely gépről csatlakozhat a fürthöz. Ez a szkript futtatott a gép nem kell lennie a fürt része.

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

2. A Service Fabric-verziók frissíthet listájának beolvasása.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Kimenetet kell kapni ehhez hasonló:

    ![A Service Fabric-verzió beolvasása][getfabversions]
3. Egy elérhető verzióra a fürtfrissítések indítása a [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell-parancsot.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   A frissítés állapotának figyelése, a Service Fabric Explorert használjuk, vagy futtassa a következő PowerShell-parancsot:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Egyéni házirendek a Start-ServiceFabricClusterUpgrade parancs megadásához dokumentációjában talál [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Után a visszaállítás eredményezett problémák kijavításához kezdeményezni a frissítés újra korábban ismertetett ugyanazokat a lépéseket követve.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Rendelkező fürtök frissítése *elérhetetlen* a legújabb kód és a konfiguráció letöltése
Kövesse az alábbi lépéseket egy támogatott verziójára frissítse a fürtöt, ha a fürtcsomópontok nem rendelkezik internetkapcsolattal a [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Ha egy fürtöt, amely nem csatlakozik az internethez rendszert használ, akkor új kiadásokkal megismerheti a Service Fabric-csapat blogjának figyelése. A rendszer nem jelenít meg a fürt állapota figyelmeztetés, hogy riasztást küldjön, új kiadásokat.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatikus telepítés és a manuális kiépítése
Automatikus letöltés és a regisztrációs kód legfrissebb engedélyezéséhez állítsa be a Service Fabric-frissítés szolgáltatás. Útmutatásért lásd a belső Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt a [különálló csomag](service-fabric-cluster-standalone-package-contents.md).
A manuális folyamat kövesse az alábbi utasításokat.

A fürt konfigurációját, a következő tulajdonság módosítása *hamis* egy konfigurációs frissítés megkezdése előtt:

        "fabricClusterAutoupgradeEnabled": false,

Használattal kapcsolatos részletek, tekintse meg a [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell-parancsot. Ellenőrizze, hogy "clusterConfigurationVersion" a JSON-fájlban frissítse a konfigurációs frissítés megkezdése előtt.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>A fürt frissítésének munkafolyamata

1. Futtatás [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) a fürt, és vegye figyelembe a csomópontok közül a *TargetCodeVersion*.

2. Futtassa a következő internethez csatlakozó gépről az aktuális verzióval minden frissítés-kompatibilis verzió listázása, és a megfelelő csomag letöltését a letöltési hivatkozás:

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. A fürtben csomópontként felsorolt összes gép rendszergazdai hozzáféréssel rendelkező bármely gépről csatlakozhat a fürthöz. Ez a szkript futtatott a gép nem kell lennie a fürt része.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. A fürt lemezképtárolójába másolja a letöltött csomag.

5. A másolt csomag regisztrálása.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Indítsa el a fürt frissítése elérhető verzióra.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   A frissítés a Service Fabric Explorer az előrehaladását nyomon követheti, vagy futtathatja a következő PowerShell-parancsot:

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapotházirendeket nem teljesülnek, a frissítés vissza lesz állítva. Egyéni házirendek a Start-ServiceFabricClusterUpgrade parancs megadásához dokumentációjában talál [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Után a visszaállítás eredményezett problémák kijavításához kezdeményezni a frissítés újra korábban ismertetett ugyanazokat a lépéseket követve.


## <a name="upgrade-the-cluster-configuration"></a>A fürt konfigurációjának frissítése
A konfiguráció frissítése kezdeményez, mielőtt a különálló csomag a következő PowerShell-parancsfájl futtatásával tesztelheti az új fürt konfigurációs JSON-fájlt:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Vagy használja ezt a parancsfájlt:

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Bizonyos konfigurációk nem lehet frissíteni, például a végpontok, fürt nevét, IP-Címét, stb. Az új fürt JSON konfigurációs van tesztelve lett a régit, és a PowerShell-ablakban hibát jelez, ha probléma van.

A fürt konfiguráció frissítése a frissítéshez futtassa [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). A konfiguráció frissítése frissítési tartomány által feldolgozott frissítési tartományban.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Fürt tanúsítvány-konfiguráció frissítése  
A fürttanúsítvány fürtcsomópontok közötti hitelesítéshez használatos. A tanúsítványváltás extra körültekintően kell hajtható végre, mert a sikertelen blokkolja a fürt csomópontok közötti kommunikációt.

Technikailag négy beállítások támogatottak:  

* Egy tanúsítvány frissítése: A frissítés elérési útja (elsődleges) -> tanúsítvány B (elsődleges) tanúsítvány tanúsítvány C (elsődleges) -> ->...

* Kettős tanúsítvány frissítése: A frissítés elérési útja (elsődleges) -> Tanúsítvány tanúsítvány-(elsődleges), és B (másodlagos) -> tanúsítvány B (elsődleges) -> (elsődleges) tanúsítvány B és C (másodlagos) > tanúsítvány C (elsődleges) ->...

* Tanúsítvány típusa frissítés: tanúsítvány ujjlenyomat-alapú konfigurációs <> – Köznapinév-alapú tanúsítvány konfigurálását. Ha például a tanúsítvány-ujjlenyomat (elsődleges) és ujjlenyomatát B (másodlagos) -> tanúsítvány CommonName c

* Tanúsítvány-kibocsátó ujjlenyomatát frissítés: A frissítési útvonalat a tanúsítvány CN = A, IssuerThumbprint et1 = (elsődleges) tanúsítvány CN -> A, IssuerThumbprint = et1, et2 = (elsődleges) tanúsítvány CN -> A, IssuerThumbprint = et2 = (elsődleges).


## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan szabhatja testre az egyes [Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md).
* Ismerje meg [alkalmazásfrissítések](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
