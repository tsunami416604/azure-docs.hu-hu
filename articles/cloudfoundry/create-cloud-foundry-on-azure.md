---
title: Pivotal Cloud Foundry klaszter létrehozása az Azure-ban
description: Ismerje meg, hogyan állíthatja be a Pivotal Cloud Foundry (PCF) fürt azure-beli üzembe ágyazásához szükséges paramétereket
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 5d4ac5435281f521c71556123f77d737ee6916e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161780"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Pivotal Cloud Foundry klaszter létrehozása az Azure-ban

Ez az oktatóanyag gyors lépéseket tartalmaz a Pivotal Cloud Foundry (PCF) fürt azure-beli kiépítéséhez és létrehozásához szükséges paraméterek létrehozásához. A Pivotal Cloud Foundry megoldás megkereséséhez végezzen keresést az Azure [Marketplace-en.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)

![Keresés a Pivotal Cloud Foundry-ban az Azure-ban](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Nyilvános SSH-kulcs létrehozása

A nyilvános biztonsági rendszerhéj (SSH) kulcs windowsos, Mac vagy Linux os használatával többféleképpen is létrehozható.

```Bash
ssh-keygen -t rsa -b 2048
```

További információt az [SSH-kulcsok használata a Windows azure-ban című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]
>
> Egyszerű szolgáltatás létrehozásához tulajdonosi fiók engedélyre van szükség. Parancsfájlt is írhat a szolgáltatásnév létrehozásának automatizálásához. Használhatja például az Azure CLI [az ad sp create-for-rbac szolgáltatást.](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)

1. Jelentkezzen be Azure-fiókjába.

    `az login`

    ![Azure CLI bejelentkezés](media/deploy/az-login-output.png )
 
    Másolja az "id" értéket **az előfizetés azonosítójaként,** és másolja a "tenantId" értéket, amelyet később használni szeretne.

2. Állítsa be az alapértelmezett előfizetést ehhez a konfigurációhoz.

    `az account set -s {id}`

3. Hozzon létre egy Azure Active Directory-alkalmazást a PCF-hez. Adjon meg egyedi alfanumerikus jelszót. Tárolja a jelszót, mint a **clientSecret** használni később.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Másolja a kimenet "appId" értékét **ügyfélazonosítóként,** amelyet később használhat.

    > [!NOTE]
    >
    > Válassza ki saját alkalmazás kezdőlapját és azonosító\:URI-ját, például http //www\.contoso.com.

4. Hozzon létre egy egyszerű szolgáltatás az új alkalmazásazonosítóval.

    `az ad sp create --id {appId}`

5. Állítsa be az egyszerű szolgáltatás jogosultságát közreműködői szerepkörre.

    `az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"`

    Vagy használhatja

    `az role assignment create --assignee {service-principal-name} --role "Contributor"`

    ![Egyszerű szolgáltatásszerepkör-hozzárendelés](media/deploy/svc-princ.png )

6. Ellenőrizze, hogy sikeresen tud-e bejelentkezni az egyszerű szolgáltatásba az alkalmazásazonosító, a jelszó és a bérlői azonosító használatával.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. .json fájl létrehozása a következő formátumban. Használja a korábban másolt **előfizetésazonosítót**, **tenantID**, **ügyfélazonosítót**és **ügyféltitkos** értékeket. Mentse a fájlt.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>A Pivotal Network token beszereznie

1. Regisztráljon vagy jelentkezzen be [pivotal hálózati](https://network.pivotal.io) fiókjába.
2. A lap jobb felső sarkában válassza ki a profil nevét. Válassza **a Profil szerkesztése lehetőséget.**
3. Görgessen a lap aljára, és másolja a **LEGACY API TOKEN** értéket. Ez az érték a később használt **pivotal hálózati jogkivonat** értéke.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>A felhőalapú öntödei fürt kiépítése az Azure-ban

Most már rendelkezik az összes paraméterszükséges kiépítése a [Pivotal Cloud Foundry klaszter az Azure-ban.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
Adja meg a paramétereket, és hozza létre a PCF-fürtöt.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>A telepítés ellenőrzése és bejelentkezés a Pivotal Ops Managerbe

1. A PCF-fürt telepítési állapotot jelenít meg.

    ![Az Azure telepítési állapota](media/deploy/deployment.png )

2. Válassza ki a **Központi telepítések hivatkozást** a bal oldali navigációs a PCF Ops Manager hitelesítő adatok beszerezéséhez. A következő lapon válassza a **központi telepítés nevét.**
3. A bal oldali navigációs sávon válassza a **Kimenetek hivatkozást** a PCF Ops Manager URL-címének, felhasználónevének és jelszavának megjelenítéséhez. The "OPSMAN-FQDN" value is the URL.
 
    ![Felhőöntöde telepítési kimenete](media/deploy/deploy-outputs.png )
 
4. Indítsa el az URL-címet egy webböngészőben. Adja meg a bejelentkezéshez az előző lépés hitelesítő adatait.

    ![Kulcsfontosságú bejelentkezési oldal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Ha az Internet Explorer böngészője "A webhely nem biztonságos" figyelmeztető üzenet miatt sikertelen, válassza a **További információkat,** és nyissa meg a weblapot. A Firefox esetében válassza **az Advance** lehetőséget, és adja hozzá a minősítést a folytatáshoz.

5. A PCF Ops Manager megjeleníti a telepített Azure-példányokat. Mostantól itt telepítheti és kezelheti alkalmazásait.
               
    ![Üzembe helyezett Azure-példány a Pivotal-ban](media/deploy/ops-mgr.png )
 
