---
title: Üzleti folytonosság és vészhelyreállítás helyreállítási Azure fejlesztési tárolóhelyek |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: article
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: jeconnoc
ms.openlocfilehash: 7ecb792e45aeab90f9ae315e5b31247966bcd184
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199199"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Üzleti folytonosság és vészhelyreállítás helyreállítási Azure fejlesztési tárolóhelyek

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Tekintse át a vészhelyreállítási útmutató az Azure Kubernetes Service (AKS)

Az Azure fejlesztési tárolóhelyek funkciója az Azure Kubernetes Service (AKS). Vegye figyelembe az aks-ben vész-helyreállítási irányelveket kell, és vegye figyelembe, hogy vonatkoznak az AKS-fürtök használhat olyan fejlesztői, szóközök. További információkért lásd az [ajánlott eljárások a helyreállításhoz üzleti folytonosság és vészhelyreállítás Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Az AKS-fürtök különböző régiókban lévő fejlesztési tárolóhelyek engedélyezése

Az AKS-fürtök különböző régiókban lévő fejlesztői, szóközök engedélyezése lehetővé teszi, hogy fejlesztési tárolóhelyek használatával egy Azure-régióban hiba után azonnal folytathatja.

Többrégiós üzemelő példányainak AKS kapcsolatos általános információkért lásd: [többrégiós üzembe helyezés tervezése](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multi-region-deployment)

AKS-fürt kompatibilis az Azure fejlesztési tárolóhelyek központi telepítésével kapcsolatos információkért lásd: [Azure Cloud Shellt használja a Kubernetes-fürt létrehozása](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>A fejlesztői, szóközök engedélyezése az Azure Portalon

Kattintson a **fejlesztési tárolóhelyek** navigációs elem mellett minden egyes fürt az Azure Portalon tulajdonságait. Ezután válassza ki a fejlesztői szóközt engedélyezni.

![Fejlesztői, szóközök engedélyezése az Azure Portalon](../media/common/enable-dev-spaces.jpg)

Ismételje meg ezt a folyamatot minden fürt számára.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Az Azure CLI-n keresztül fejlesztési tárolóhelyek engedélyezése

Fejlesztői szóközöket is engedélyezheti a parancssorban:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>A csapat alapkonfiguráció minden egyes fürt üzembe helyezése

Ha fejlesztési tárolóhelyek dolgozik, általában alkalmazást telepít központilag a teljes szülő fejlesztési adhatja a Kubernetes-fürtön. Alapértelmezés szerint a `default` területét. A kezdeti telepítés magában foglalja a minden szolgáltatásokat, valamint a külső erőforrások, amelyek ezeket a szolgáltatásokat függenek, például adatbázisok és üzenetsorok. Ez más néven a *alapkonfiguráció*. Alapterv a szülő fejlesztési lemezterületet állít be, miután ismételt futtatásával, és hibakeresési gyermek fejlesztési tárolóhelyek belüli egyes szolgáltatások.

Fürtök több régióban is üzembe kell helyeznie a alapkészletével szolgáltatások legújabb verzióját. Ily módon az alapvető szolgáltatások frissítése biztosítja, hogy folytathatja a fejlesztési tárolóhelyek használata, ha egy Azure-régió sikertelen. Például ha telepít egy CI/CD-folyamat segítségével az alapterv, módosítsa a folyamat úgy, hogy a különböző régiókban lévő több fürt helyezi üzembe.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Válassza ki a megfelelő AKS-fürt fejlesztési tárolóhelyek használata

Ha megfelelően konfigurálta a csapat alapkonfiguráció futó biztonsági mentési fürt, gyorsan válthat a biztonsági mentési fürthöz bármikor. Ezután futtathatja az egyes szolgáltatásokat, amelyek fejlesztői, szóközök dolgozik.

Válasszon egy másik fürtön a következő CLI-paranccsal:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Az elérhető fejlesztői címtereket az új fürtön a következő paranccsal listázhatja:

```cmd
azds space list
```

Hozzon létre egy új fejlesztői tárolóhelyet dolgozni, vagy válasszon egy már létező fejlesztési területet, a következő paranccsal:

```cmd
azds space select -n <space name>
```

A parancsok futtatása után a kijelölt fürt és a dev terület lesz használható a további parancssori felület műveletek, valamint a projektek, a Visual Studio Code-bővítmény használata az Azure fejlesztési tárolóhelyek hibakeresés.

Ha a Visual Studiót használja, válthat a fürtöt, az alábbi lépéseket egy meglévő projekt által használt:

1. Nyissa meg a projektjét a Visual Studióban.
1. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, és kattintson a **tulajdonságai**
1. A bal oldali ablaktáblán kattintson a **hibakeresése**
1. A hibakeresési tulajdonságok lapján kattintson a **profil** legördülő listában, és válassza a **Azure fejlesztési tárolóhelyek**.
1. Kattintson a **módosítása** gombra.
1. A megjelenő párbeszédpanelen jelölje ki az AKS-fürtöt, amelyet használni kíván. Ha szükséges, dolgozhat a különböző fejlesztési szóközzel válassza ki vagy hozzon létre egy új fejlesztői tárolóhelyet, a megfelelő beállítás kiválasztásával a **terület** legördülő listából.

Miután kiválasztotta a megfelelő fürt és a hely, akkor nyomja le az F5 futtatni a szolgáltatást a fejlesztési szóközöket.

Ismételje meg ezeket a lépéseket bármely más projektekhez az eredeti fürthöz használatára konfigurálva.

## <a name="access-a-service-on-a-backup-cluster"></a>Egy biztonsági mentési fürtön a szolgáltatás elérésére

Ha konfigurálta a szolgáltatást, hogy használjon egy nyilvános DNS-nevet, majd a a szolgáltatás az egy másik URL-címet rendelkezik, ha egy biztonsági mentési fürtön futtatja. Nyilvános DNS-nevek mindig a következő formátumban vannak `<space name>.s.<service name>.<cluster GUID>.<region>.aksapp.io`. Ha egy másik fürtre vált, módosíthatja a fürt GUID Azonosítóját, és esetleg a régióban.

Fejlesztői, szóközök mindig látható, a szolgáltatás URL-CÍMÉT, amikor fut `azds up`, vagy a kimeneti ablakban, a Visual studióban **Azure fejlesztési tárolóhelyek**.

Az URL-címet futtató úgy is megkeresheti a `azds list-uris` parancsot:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://mywebapi.d05afe7e006a4fddb73c.eastus.aksapp.io/  Available
```

Az URL-cím akkor használja, ha a szolgáltatás elérésével.