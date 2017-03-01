---
title: "Azure Container Service – gyakori kérdések | Microsoft Docs"
description: "Válaszok a Azure Container Service-szel, a Docker tárolóalapú alkalmazások futtatására szolgáló virtuálisgép-fürt létrehozását, konfigurálását és kezelését leegyszerűsítő szolgáltatással kapcsolatos gyakori kérdésekre."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Mesos, Azure, Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: c28391b752c071161796421aee63402899d2a0a4


---
# <a name="frequently-asked-questions-azure-container-service"></a>Gyakori kérdések: Azure Container Service


## <a name="orchestrators"></a>Vezénylők

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Mely tárolóvezénylőket támogatja az Azure Container Service? 

A következők élveznek támogatást: nyílt forráskódú DC/OS, Docker Swarm és Kubernetes. További információ: [Áttekintés](container-service-intro.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Támogatja a Docker Swarm módot? 

A Swarm mód jelenleg még nem élvez támogatást, de már a szolgáltatáshoz kapcsolódó ütemterv részét képezi. 

### <a name="does-azure-container-service-support-windows-containers"></a>Az Azure Container Service támogatja a Windows-tárolókat?  

Jelenleg a Linux-tárolók az összes vezénylővel támogatást élveznek. A Windows-tárolók Kubernetesszel való használatának támogatása előzetes verzióban érhető el.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Ajánlanak egy adott vezénylőt az Azure Container Service-ben? 
Nem ajánlunk általánosan egy adott vezénylőt. Ha már rendelkezik tapasztalattal a támogatott vezénylők egyikéhez kapcsolódóan, tapasztalataira építhet az Azure Container Service-ben is. Az adattrendek azonban azt jelzik, hogy a DC/OS kiválóan használható éles környezetben a Big Data és IoT számítási feladatokhoz, a Kubernetes a felhők natív számítási feladataihoz megfelelő, a Docker Swarm pedig közismerten integrálható Docker-eszközökkel, illetve egyszerűen elsajátítható a használata.

A forgatókönyvtől függően más Azure-szolgáltatásokkal is létrehozhat és kezelhet egyéni tárolómegoldásokat. Ilyen szolgáltatás például a [Virtual Machines](../virtual-machines/virtual-machines-linux-azure-overview.md), a [Service Fabric](../service-fabric/service-fabric-overview.md), a [Web Apps](../app-service-web/app-service-web-overview.md) és a [Batch](../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Mi a különbség az Azure Container Service és ASC Engine között? 
Az Azure Container Service egy SLA-alapú Azure-szolgáltatás, amely az Azure Portalon elérhető funkciókkal, Azure parancssori eszközökkel és Azure API-kkal rendelkezik. A szolgáltatás lehetővé teszi, hogy viszonylag kevés konfigurációs beállítás megadásával, gyorsan valósíthasson meg és kezelhessen általános, tárolóvezénylési eszközöket futtató fürtöket. 

Az [ACS Engine](http://github.com/Azure/acs-engine) egy nyílt forráskódú projekt, amely lehetővé teszi a kiemelt felhasználók számára a fürtkonfiguráció egyéni beállítását minden szinten. Az infrastruktúra és a szoftverek konfigurációjának módosíthatósága azzal jár, hogy nem kínálunk SLA-t az ACS Engine esetén. A támogatás kezelése a GitHubon található nyílt forráskódú projekt segítségével történik a hivatalos Microsoft-csatornák helyett. 

## <a name="cluster-management"></a>Fürtkezelés

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Hogyan hozhatok létre SSH-kulcsokat a fürtöm számára?

A fürt Linux rendszerű virtuális gépei felé történő hitelesítéshez szükséges nyilvános és titkos SSH RSA-kulcspár létrehozásához az operációs rendszer megszokott eszközeit használhatja. A lépéseket az [OS X és Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) vagy a [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) rendszerhez készült útmutatóban tekintheti meg. 

Ha az [Azure CLI 2.0 parancsait](container-service-create-acs-cluster-cli.md) használja egy Container Service-fürt központi telepítéséhez, az SSH-kulcsok automatikusan létrehozhatóak a fürt számára.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Hogyan hozhatok létre egyszerű szolgáltatást a Kubernetes-fürtöm számára?

Ha az Azure Container Service-ben szeretne Kubernetes-fürtöt létrehozni, szüksége lesz egy egyszerű Azure Active Directory-szolgáltatás azonosítójára és jelszavára. További információ: [Tudnivalók az Kubernetes-fürthöz tartozó egyszerű szolgáltatásról](container-service-kubernetes-service-principal.md).


Ha az [Azure CLI 2.0 parancsait](container-service-create-acs-cluster-cli.md) használja egy Kubernetes-fürt központi telepítéséhez, az egyszerű szolgáltatás hitelesítő adatai automatikusan létrehozhatók a fürt számára.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Hogyan növelhetem a főkiszolgálók számát a fürt létrehozása után? 
A fürt létrehozása után a főkiszolgálók száma állandó lesz, és nem módosítható. A fürt létrehozása során érdemes több főkiszolgálót kijelölni magas rendelkezésre állásra.


### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Hogyan növelhetem az ügynökök számát a fürt létrehozása után? 
Az Azure Portal vagy a parancssori eszközök használatával módosíthatja a fürtben található ügynökök számát. Lásd: [Azure Container Service-fürt méretezése](container-service-scale.md).


### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Mik a főkiszolgálóim és ügynökeim URL-címei? 
Az Azure Container Service-ben található fürterőforrások URL-címei a megadott DNS-név előtagján és a központi telepítéshez választott Azure-régió nevén alapulnak. A főcsomópont teljes tartományneve (FQDN) például az alábbi formátumú:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Kereshet gyakran használt URL-címeket a fürt számára az Azure Portalon, az Azure Resource Explorerben vagy más Azure-eszközben.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Hol találom a fürtömhöz tartozó SSH kapcsolati karakterláncot?

A kapcsolati karakterláncot az Azure Portalon vagy az Azure parancssori eszközök segítségével keresheti meg. 

1. A portálon lépjen a fürttelepítés erőforráscsoportjába.  

2. Kattintson az **Overview** (Áttekintés) elemre, majd kattintson a **Deployments** (Üzemelő példányok) hivatkozásra az **Essentials** (Alapvető erőforrások) részen. 

3. A **Deployment history** (Telepítési előzmények) panelen kattintson arra az üzemelő példányra, amelynek neve a **microsoft-acs** karakterlánccal kezdődik, és a központi telepítés dátumával folytatódik. Például: microsoft-acs-201701310000.  

4. A **Summary** (Összefoglalás) lapon az **Outputs** (Kimenetek) részen számos fürthivatkozás található <provided></provided>. Az **SSHMaster0** biztosítja a Container Service-fürt első főkiszolgálójához tartozó SSH kapcsolati karakterláncot. 

A fent említetteknek megfelelően Azure-eszközöket is használhat a főkiszolgálók teljes tartománynevének megkeresésére. Létesítsen SSH-kapcsolatot a főkiszolgálóval a főkiszolgáló teljes tartománynevét és a fürt létrehozásakor megadott felhasználónevet használva. Példa:

```bash
ssh userName@masterFQDN –A –p 22 
```

Több információ: [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md).




## <a name="next-steps"></a>Következő lépések

* [További információ](container-service-intro.md) az Azure Container Service-ről.
* Container Service-fürt központi telepítése a [portál](container-service-deployment.md) vagy az [Azure CLI 2.0](container-service-create-acs-cluster-cli.md) segítségével.


<!--HONumber=Feb17_HO4-->


