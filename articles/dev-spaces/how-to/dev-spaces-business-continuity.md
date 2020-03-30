---
title: Folyamatos üzletmenet és vészhelyreállítás
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Ismerje meg az Azure Dev Spaces és az Azure Kubernetes-szolgáltatások használatát az üzletmenet folytonosságának biztosításához és a vészhelyreállítás előkészítéséhez
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 37c0048bfa7e72b25eb56603fc027045eba25cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295827"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Üzletmenet-folytonosság és vészhelyreállítás az Azure Dev Spaces-ben

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Tekintse át az Azure Kubernetes-szolgáltatás (AKS) vész-helyreállítási útmutatóját

Az Azure Dev Spaces az Azure Kubernetes szolgáltatás (AKS) egyik szolgáltatása. Tisztában kell lennie az AKS vész-helyreállítási irányelveivel, és fontolja meg, hogy azok a fejlesztői tárolóhelyekhez használt AKS-fürtökre vonatkoznak-e. További információkért kérjük, olvassa el [az üzleti folytonossággal és a vészhelyreállítással kapcsolatos gyakorlati tanácsokat az Azure Kubernetes szolgáltatásban (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Fejlesztői terhelyek engedélyezése különböző régiókban lévő AKS-fürtökön

A dev-terek engedélyezése az AKS-fürtök különböző régiókban lehetővé teszi, hogy folytassa a fejlesztői terek használata után azonnal egy Azure-régió hiba.

Az AKS több régióra kiterjedő telepítésével kapcsolatos általános tudnivalókért olvassa el a Több régióra kiterjedő [telepítés megtervezése című témakört.](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Fejlesztői tárolók engedélyezése az Azure portalon keresztül

Válassza ki a **fejlesztői terek** menüpontot az Azure Portal egyes fürteinek beállításai között. Ezután válassza ki a lehetőséget, hogy a fejlesztői szóközök és mentse.

![Fejlesztői terek engedélyezése az Azure Portalon keresztül](../media/common/enable-dev-spaces.jpg)

Ismételje meg ezt a folyamatot minden fürt esetében.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Fejlesztői terek engedélyezése az Azure CLI-n keresztül

A fejlesztői szóközöket a parancssorból is engedélyezheti:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>A csapat alapkonfigurációjának üzembe helyezése az egyes fürtökben

A fejlesztői tárolóhelyek használatával általában a teljes alkalmazást a Kubernetes-fürt egy szülő fejlesztői területre telepíti. Alapértelmezés szerint `default` a rendszer a helyet használja. A kezdeti központi telepítés magában foglalja az összes szolgáltatást, valamint a külső erőforrásokat, amelyektől ezek a szolgáltatások függnek, például adatbázisokat vagy várólistákat. Ezt *alapvonalnak nevezzük.* Miután beállítottegy alapkonfigurációt a szülő fejlesztési területen, akkor az egyes szolgáltatások mentegése és hibakeresése a gyermekfejlesztői tereken belül.

Az alapszintű szolgáltatáskészlet legújabb verzióit több régióban lévő fürtökre kell telepítenie. Az alapszintű szolgáltatások ily módon történő frissítése biztosítja, hogy továbbra is használhatja a fejlesztői tárolókat, ha van egy Azure-régió hiba. Ha például az alapkonfigurációt ci/CD-folyamaton keresztül telepíti, módosítsa a folyamatot úgy, hogy az több fürtre telepítse a különböző régiókban.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Válassza ki a fejlesztői szóközökhöz használandó megfelelő AKS-fürtöt

Miután megfelelően konfigurálta a csoport alapkonfigurációját futtató biztonsági másolat fürtöt, bármikor gyorsan átválthat a biztonsági másolat fürtjére. Ezután újrafuttathatja az egyes szolgáltatásokat, amelyeken dolgozik a gyermekfejlesztői terekben.

Válasszon másik fürtöt a következő CLI paranccsal:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Az új fürtön rendelkezésre álló fejlesztői tereket a következő paranccsal sorathatja fel:

```cmd
azds space list
```

A következő paranccsal új fejlesztői területet hozhat létre, vagy kiválaszthat egy meglévő fejlesztői területet:

```cmd
azds space select -n <space name>
```

A parancsok futtatása után a kijelölt fürt és fejlesztési terület a későbbi CLI-műveletekhez, valamint az Azure Dev Spaces Visual Studio-kódbővítményhasználatával a Projektek hibakereséséhez lesz használva.

Visual Studio használata esetén a meglévő projekt által használt fürtöt az alábbi lépésekkel kapcsolhatja át:

1. Nyissa meg a projektet a Visual Studióban.
1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, és kattintson a **Tulajdonságok parancsra.**
1. A bal oldali ablaktáblában kattintson a **Hibakeresés gombra.**
1. A Hibakeresés tulajdonságai lapon kattintson a Profil legördülő **listára,** és válassza az **Azure dev spaces**lehetőséget.
1. Kattintson a **Módosítás** gombra.
1. A megjelenő párbeszédpanelen jelölje ki a használni kívánt AKS-fürtöt. Ha szükséges, válasszon egy másik fejlesztési területet dolgozni, vagy hozzon létre egy új fejlesztői helyet, válassza ki a megfelelő beállítást a **Tér** legördülő listából.

Miután kiválasztotta a megfelelő fürtöt és helyet, az F5 billentyű lenyomása a szolgáltatás fejlesztői szóközökben futtatásához.

Ismételje meg ezeket a lépéseket az eredeti fürt használatára konfigurált többi projekt esetében.

## <a name="access-a-service-on-a-backup-cluster"></a>Szolgáltatás elérése biztonsági másolat fürtön

Ha a szolgáltatást nyilvános DNS-név használatára állította be, akkor a szolgáltatás nak más URL-címe lesz, ha egy biztonsági másolat fürtén futtatja. A nyilvános DNS-nevek `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`formátuma mindig a formátumban van. Ha másik fürtre vált, a fürt GUID azonosítója és esetleg a régió megváltozik.

A fejlesztői tárolóhelyek mindig a `azds up`szolgáltatás megfelelő URL-címét jeleníti meg futáskor, vagy a Visual Studio Kimenet ablakában az **Azure Dev Spaces**alatt.

Az URL-címet a `azds list-uris` következő paranccsal is megtalálhatja:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Használja ezt az URL-címet a szolgáltatás elérésekor.
