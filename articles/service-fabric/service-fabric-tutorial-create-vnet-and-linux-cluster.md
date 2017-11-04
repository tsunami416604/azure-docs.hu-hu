---
title: "A Linux Service Fabric-fürt létrehozása az Azure-ban |} Microsoft Docs"
description: "Ismerje meg, hogy egy meglévő Azure virtuális hálózatra, Azure parancssori felület használatával történő Linux Service Fabric-fürt központi telepítése."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 33a3474ed91194efbaf2ef96957ad268f43a717e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Az Azure virtuális hálózat Service Fabric Linux fürt központi telepítése
Ez az oktatóanyag egy sorozat része. Megtudhatja, hogyan telepítheti Linux Service Fabric-fürt be egy meglévő Azure virtuális hálózatot (VNET), és részterv net az Azure parancssori felület használatával. Amikor végzett, hogy a fürt fut a felhőben, amely központilag telepíthető alkalmazások. A Windows PowerShell fürt létrehozásához lásd: [biztonságos Windows-fürt létrehozása az Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása az Azure-ban az Azure parancssori felület
> * A biztonságos Service Fabric-fürt létrehozása az Azure-ban az Azure parancssori felület
> * A fürt egy X.509 tanúsítvánnyal biztonságos
> * Csatlakozzon a fürthöz Service Fabric parancssori felület használatával
> * A fürt eltávolítása

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * Biztonságos fürt létrehozása az Azure-on
> * [A Service Fabric az API Management központi telepítését](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Telepítse a [háló CLI szolgáltatás](service-fabric-cli.md)
- Telepítse a [Azure CLI 2.0](/cli/azure/install-azure-cli)

Az alábbi eljárások öt csomópontból Service Fabric-fürtök létrehozása. Használja az Azure Service Fabric-fürt futtatásával felmerülő költség kiszámításához a [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Bejelentkezés az Azure-ba, és jelölje ki az előfizetését
Ez az útmutató az Azure parancssori felület használja. Amikor egy új munkamenetet indít el, jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.
 
Futtassa az alábbi parancsfájlt az Azure-fiókjával bejelentkezhet jelölje ki az előfizetését:

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy új erőforráscsoportot, a telepítéshez, és adjon neki egy nevet és egy helyet.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>A hálózati topológia központi telepítéséhez
Következő lépésként állítsa be a hálózati topológia, amely az API Management és a Service Fabric-fürt telepítése. A [network.json] [ network-arm] Resource Manager-sablon létrehozására van beállítva a virtuális hálózathoz (VNET), és egy alhálózat és a hálózati biztonsági csoport (NSG) a Service Fabric és alhálózati és NSG-t az API Management . Ismerje meg a Vneteket, alhálózatok, és tájékozódhat az NSG-k [Itt](../virtual-network/virtual-networks-overview.md).

A [network.parameters.json] [ network-parameters-arm] paraméterfájl NSG-ket, hogy a Service Fabric és az API Management és az alhálózatok nevét tartalmazza.  Az API Management telepítve van a [oktatóanyag következő](service-fabric-tutorial-deploy-api-management.md). Ez az útmutató a paraméterértékek nem kell módosítani. A Service Fabric Resource Manager-sablonok használja ezeket az értékeket.  Ha itt módosítja az az értékeket, módosítania kell azokat a jelen oktatóanyagban használt többi Resource Manager sablon és a [központi telepítése az API Management oktatóanyag](service-fabric-tutorial-deploy-api-management.md). 

Töltse le a következő Resource Manager sablon és a Paraméterek:
- [Network.JSON][network-arm]
- [Network.Parameters.JSON][network-parameters-arm]

A következő parancsfájl használatával telepíti a Resource Manager-sablonnal és paraméterfájlokkal a hálózati telepítéshez fájlokat:

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>A Service Fabric-fürt központi telepítése
Ha a hálózati erőforrások végzett központi telepítése, a következő lépés a Service Fabric-fürt központi telepítése a virtuális hálózat, az alhálózat és a Service Fabric-fürt számára kijelölt NSG. A Resource Manager-sablon egy meglévő VNET és alhálózat (korábban ebben a cikkben telepített) egy fürt telepítése szükséges.  További információkért lásd: [fürt létrehozása az Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md). Az oktatóanyag adatsorozathoz a sablon nevét a virtuális Hálózatot, alhálózatot és az előző lépésben beállított NSG használandó előre konfigurálva.  

Töltse le a következő Resource Manager sablon és a Paraméterek:
- [linuxcluster.JSON][cluster-arm]
- [linuxcluster.Parameters.JSON][cluster-parameters-arm]

A sablon használatához a biztonságos fürtök létrehozásához.  A fürt tanúsítvány csomópontok kommunikáció biztosításához és a fürt felügyeleti végpontok egy felügyeleti ügyfél hitelesítésére használt X.509 tanúsítvány.  A fürt tanúsítvány is lehetővé teszi az SSL protokoll a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül. Az Azure Key Vault segítségével kezelheti az Azure Service Fabric-fürtök tanúsítványait.  A fürt telepítésekor az Azure-ban, a Service Fabric-fürtök létrehozásáért felelős az Azure erőforrás-szolgáltató kéri le a tanúsítványokat a Key Vault, és telepíti azokat a virtuális gépek fürtön. 

Egy hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon a fürt-tanúsítványt, vagy a tesztelési célokra, hozzon létre egy önaláírt tanúsítványt. A fürt tanúsítványt kell:

- tartalmazza a titkos kulcsot.
- a kulcscseréhez használt, amely exportálható egy személyes információcsere (.pfx) fájl hozható létre.
- a tulajdonos nevét, amely megfelel a tartományt, amelyikhez a Service Fabric-fürt eléréséhez használt rendelkezik. A megfelelő szükség SSL a fürt HTTPS felügyeleti végpontokat és a Service Fabric Explorerben talál. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) származó nem szerezze be a. cloudapp.azure.com tartomány. Be kell szereznie egy egyéni tartománynevet a fürt számára. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynevet, amelyekkel a fürt számára.

Töltse ki a üres paraméterek a *linuxcluster.parameters.json* fájl az adott környezethez:

|Paraméter|Érték|
|---|---|
|adminPassword|Jelszó #1234|
|adminUserName|vmadmin|
|Fürtnév|mysfcluster|

Hagyja a **certificateThumbprint**, **certificateUrlValue**, és **sourceVaultValue** paraméter üres, létrehozhat egy önaláírt tanúsítványt.  Ha egy meglévő kulcstároló korábban feltöltött tanúsítványt használni kívánt, töltse ki azokat a paraméterértékek.

Az alábbi parancsfájl használ a [az ú fürt létrehozása](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) parancs és a sablon telepítése egy új fürtöt az Azure-ban. A parancsmag is létrehoz egy új kulcstartó az Azure-ban, a key vault ad hozzá egy új önaláírt tanúsítványt, és letölti a fájlt helyileg. A többi paraméter használatával adhat meg egy meglévő tanúsítvány és/vagy a kulcstartót a [az ú fürt létrehozása](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) parancsot.

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz
Csatlakozzon a fürthöz, a Service Fabric parancssori felület használatával `sfctl cluster select` parancsot a kulcs használatával.  Vegye figyelembe, csak a **--nem ellenőrizze** beállítás megadása egy önaláírt tanúsítványt.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Ellenőrizze, hogy kapcsolódik-e, és a fürt állapota kifogástalan használatával a `sfctl cluster health` parancsot.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyag adatsorozat további cikkeit az újonnan létrehozott fürt használja. Ha még nem azonnal áthelyezése a következő cikk be, előfordulhat, hogy törölni kívánja a fürt ezzel járó költségek elkerülése érdekében. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki az előfizetés-azonosító, amelynél el szeretné távolítani a fürt.  Miután bejelentkezett az előfizetés-azonosító található a [Azure-portálon](http://portal.azure.com). Törölje az erőforráscsoportot és használó fürt erőforrásait a [az csoport törlése](/cli/azure/group?view=azure-cli-latest#az_group_delete) parancsot.

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * VNET létrehozása az Azure-ban az Azure parancssori felület
> * A biztonságos Service Fabric-fürt létrehozása az Azure-ban az Azure parancssori felület
> * A fürt egy X.509 tanúsítvánnyal biztonságos
> * Csatlakozzon a fürthöz Service Fabric parancssori felület használatával
> * A fürt eltávolítása

A következő előzetes az alábbi oktatóanyag áttekintésével megismerheti, hogyan méretezni a fürtön.
> [!div class="nextstepaction"]
> [A fürt méretezése](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
