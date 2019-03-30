---
title: Egy Azure Service Fabric önálló fürt-verziójának frissítése |} A Microsoft Docs
description: Az Azure Service Fabric-kódot, amely futtatása a önálló Service Fabric-fürt frissítése.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 29d034be5999d0bc3f0a244cfa7a5658a4ecce32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661462"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Frissítés a Service Fabric-verzióra, amely a fürtön 

Minden olyan modern rendszerbe arra, hogy frissítse a termék hosszú távú sikeréhez kulcsfontosságú, hogy. Azure Service Fabric-fürt saját erőforrás. Ez a cikk ismerteti az önálló fürtön futó Service Fabric-verziójának frissítése.

> [!NOTE]
> Győződjön meg arról, hogy a fürt mindig fut a Service Fabric támogatott verziója. A kiadás a Service Fabric egy új verzióját a Microsoft bejelenti, ha az előző verziója legalább a közlemény 60 nap után támogatásuk van megjelölve. Új kiadásokkal már bejelentettünk [a Service Fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/). Ezen a ponton kiválasztása az új kiadásban érhető el.
>
>

A fürt válthat az új verzió csak akkor, ha használ egy éles stílusú csomópont-konfiguráció, ahol minden Service Fabric-csomópont egy külön fizikai vagy virtuális gép van lefoglalva. Ha rendelkezik egy fejlesztési fürtöt, ahol egynél több Service Fabric-csomópont van egyetlen fizikai vagy virtuális gépen, újra létre kell hoznia a fürtöt az új verzióval.

Két különböző munkafolyamatok frissíthetők a fürt a legújabb vagy támogatott a Service Fabric verziója. Egy munkafolyamat olyan fürtök, amelyek a legújabb verzió letöltéséhez automatikusan csatlakozik. A többi munkafolyamat olyan fürtök, amelyek nem rendelkeznek a kapcsolatot a Service Fabric legújabb verzió letöltéséhez.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Engedélyezze a Service Fabric-verziót a fürt automatikus frissítése
Állítsa be, hogy a fürt Service fabric-frissítések letöltése a Microsoft által kiadott egy új verziója, a `fabricClusterAutoupgradeEnabled` fürtkonfigurációhoz *igaz*. Manuálisan válassza ki a fürt a használni kívánt Service Fabric egy támogatott verzióját, állítsa be a `fabricClusterAutoupgradeEnabled` fürtkonfigurációhoz *hamis*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Töltse le a legújabb kódot és konfigurációs kapcsolati fürtök frissítése
Kövesse az alábbi lépéseket egy támogatott verziójára frissítse a fürtöt, ha a fürtcsomópontok rendelkezik internetkapcsolattal a [Microsoft Download Center](https://download.microsoft.com).

A fürtök, amely rendelkezik a [Microsoft Download Center](https://download.microsoft.com), a Microsoft rendszeresen új Service Fabric-verziók rendelkezésre állását ellenőrzi.

Ha egy új Service Fabric-verzió érhető el, a csomag helyileg töltött le a fürt és frissítés kiépítve. Emellett tájékoztatja az ügyfél az új verzió, a rendszer jelenít meg egy explicit fürt állapotfigyelési figyelmeztetése, amely a következőhöz hasonló:

"Az aktuális fürt verziója [verzió #] támogatás vége [date]."

Miután a fürt fut. a legújabb verzióra, a figyelmeztetés ami újbóli próbálkozással megszűnik.

Amikor megjelenik a fürt állapotának figyelmeztetés, a fürt frissítése:

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

    ```powershell
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

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Rendelkező fürtök frissítése *elérhetetlen* a legújabb kód és a konfiguráció letöltése
Kövesse az alábbi lépéseket egy támogatott verziójára frissítse a fürtöt, ha a fürtcsomópontok nem rendelkezik internetkapcsolattal a [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Futtatja egy fürtöt, amely nem csatlakozik az internethez, ha rendelkezik-e figyelni a [Service Fabric-csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric/) megismerheti az új verziók. A rendszer nem jelenít meg a fürt állapota figyelmeztetés, hogy riasztást küldjön, új kiadásokat.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatikus telepítés és a manuális kiépítése
Automatikus letöltés és a regisztrációs kód legfrissebb engedélyezéséhez állítsa be a Service Fabric-frissítés szolgáltatás. Útmutatásért lásd: *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* a a [különálló csomag](service-fabric-cluster-standalone-package-contents.md).

A manuális folyamat kövesse az alábbi utasításokat.

A fürt konfigurációját, a következő tulajdonság módosítása *hamis* egy konfigurációs frissítés megkezdése előtt:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Használattal kapcsolatos részletek, tekintse meg a [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell-parancsot. Ellenőrizze, hogy "clusterConfigurationVersion" a JSON-fájlban frissítse a konfigurációs frissítés megkezdése előtt.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>A fürt frissítésének munkafolyamata

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

    ```powershell
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

## <a name="next-steps"></a>További lépések
* [Önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md)
* Testre szabhatja az egyes [Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).
* [Fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
