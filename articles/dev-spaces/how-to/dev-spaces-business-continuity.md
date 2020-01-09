---
title: Folyamatos üzletmenet és vészhelyreállítás
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Ismerje meg, hogyan használható az Azure dev Spaces és az Azure Kubernetes Services az üzletmenet folytonosságának biztosításához és a vész-helyreállítás előkészítéséhez
keywords: 'Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 8a223e9610d2b243cd78bf8b674262d6438421a9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438522"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Üzletmenet-folytonosság és vész-helyreállítás az Azure dev Spaces szolgáltatásban

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>A vész-helyreállítási útmutatás áttekintése az Azure Kubernetes Service (ak) szolgáltatáshoz

Az Azure dev Spaces az Azure Kubernetes Service (ak) egyik funkciója. Tisztában kell lennie az AK-beli vész-helyreállítási irányelvekkel, és figyelembe kell venni, hogy azok a fejlesztői tárhelyekhez használt AK-fürtökre vonatkoznak-e. További információ: [ajánlott eljárások az üzletmenet folytonosságához és a vész-helyreállításhoz az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Fejlesztői szóközök engedélyezése a különböző régiókban található AK-fürtökön

A fejlesztői szóközöknek a különböző régiókban található AK-fürtökön való engedélyezése lehetővé teszi, hogy az Azure-régió meghibásodása után azonnal folytassa a dev Spaces használatát.

Az AK többrégiós üzembe helyezésével kapcsolatos általános információkért lásd: [a többrégiós telepítés tervezése](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Az Azure dev Spaces szolgáltatással kompatibilis AK-fürtök telepítésével kapcsolatos információkért lásd: [Kubernetes-fürt létrehozása Azure Cloud Shell használatával](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>A fejlesztői szóközök engedélyezése a Azure Portal használatával

Kattintson a **dev Spaces** navigációs elemre a Azure Portal egyes fürtjének tulajdonságai alatt. Ezután válassza a fejlesztői szóközök engedélyezésének lehetőségét.

![A fejlesztői szóközök engedélyezése Azure Portal használatával](../media/common/enable-dev-spaces.jpg)

Ismételje meg a folyamatot az egyes fürtökön.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>A dev Spaces használatának engedélyezése az Azure CLI-n keresztül

A dev Spaces is engedélyezhető a parancssorban:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>A csapat alaptervének üzembe helyezése minden fürtön

A dev Spaces használata esetén általában a teljes alkalmazást a Kubernetes-fürt szülő-fejlesztői területére telepíti. Alapértelmezés szerint a rendszer a `default` területet használja. A kezdeti üzembe helyezés magában foglalja az összes szolgáltatást, valamint a szolgáltatástól függő külső erőforrásokat, például az adatbázisokat vagy a várólistákat. Ezt az *alaptervnek*nevezzük. Miután beállította az alapkonfigurációt a szülő fejlesztői térben, megismételheti és hibakeresést végezhet a gyermek-fejlesztési tereken belüli egyes szolgáltatásokon.

Az alapkonfiguráció-készlet legújabb verzióit több régióban lévő fürtökre kell telepítenie. Az alapszolgáltatások frissítése így biztosíthatja, hogy továbbra is használhatja a fejlesztői helyeket, ha van Azure-régió meghibásodása. Ha például egy CI/CD folyamaton keresztül telepíti az alapkonfigurációt, módosítsa úgy a folyamatot, hogy az a különböző régiókban lévő több fürtre legyen telepítve.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Válassza ki a fejlesztői Szóközökhez használandó megfelelő AK-fürtöt

Miután megfelelően konfigurált egy biztonsági mentési fürtöt, amely a csapata alapkonfigurációját futtatja, bármikor gyorsan átválthat a tartalék fürtre. Ezután újra futtathatja azokat az egyes szolgáltatásokat, amelyeken dolgozik a fejlesztői terekben.

Válasszon másik fürtöt a következő CLI-paranccsal:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

A következő paranccsal listázhatja az elérhető fejlesztői helyeket az új fürtön:

```cmd
azds space list
```

Létrehozhat egy új fejlesztői területet a alkalmazásban, vagy kiválaszthat egy meglévő fejlesztői területet a következő paranccsal:

```cmd
azds space select -n <space name>
```

A parancsok futtatása után a rendszer a kiválasztott fürtöt és fejlesztési helyet fogja használni a későbbi CLI-műveletekhez, valamint az Azure dev Spaces-hez készült Visual Studio Code-bővítményt használó projektek hibakereséséhez.

Ha a Visual studiót használja, a következő lépésekkel válthat egy meglévő projekt által használt fürtöt:

1. Nyissa meg a projektet a Visual Studióban.
1. Kattintson a jobb gombbal a projekt nevére Megoldáskezelő majd kattintson a **Tulajdonságok** elemre.
1. A bal oldali ablaktáblán kattintson a **hibakeresés** elemre.
1. A hibakeresés tulajdonságai lapon kattintson a **profil** legördülő listára, és válassza az **Azure dev Spaces**elemet.
1. Kattintson a **módosítás** gombra.
1. A megjelenő párbeszédpanelen válassza ki a használni kívánt AK-fürtöt. Ha kívánja, válasszon egy másik fejlesztői helyet a működéséhez, vagy hozzon létre egy új fejlesztői területet a megfelelő beállítás kiválasztásával a **hely** legördülő listából.

Miután kiválasztotta a megfelelő fürtöt és területet, az F5 billentyű lenyomásával futtathatja a szolgáltatást a dev Spaces szolgáltatásban.

Ismételje meg ezeket a lépéseket minden más, az eredeti fürt használatára konfigurált projekt esetében.

## <a name="access-a-service-on-a-backup-cluster"></a>Szolgáltatás elérése egy tartalék fürtön

Ha úgy állította be a szolgáltatást, hogy a nyilvános DNS-nevet használja, akkor a szolgáltatás egy másik URL-címmel fog rendelkezni, ha egy biztonsági mentési fürtön futtatja. A nyilvános DNS-neveket mindig `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`formátumban kell megadni. Ha másik fürtre vált, a fürt GUID azonosítója és valószínűleg a régió módosul.

A dev Spaces mindig a szolgáltatás helyes URL-címét jeleníti meg a `azds up`futtatásakor, vagy a Visual Studióban, az **Azure dev Spaces**szakaszban található output (kimenet) ablakban.

Az URL-címet a `azds list-uris` parancs futtatásával is megtalálhatja:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Használja ezt az URL-címet a szolgáltatáshoz való hozzáféréskor.
