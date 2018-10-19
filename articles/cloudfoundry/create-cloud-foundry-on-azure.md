---
title: Cloud Foundry létrehozása az Azure-ban
description: Megtudhatja, hogyan állíthatja be a Cloud Foundry PCF-fürt Azure-beli üzembe helyezéséhez szükséges paramétereket
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250627"
---
# <a name="create-cloud-foundry-on-azure"></a>Cloud Foundry létrehozása az Azure-ban

Ez az útmutató a Pivotal Cloud Foundry PCF-fürt Azure-beli üzembe helyezéséhez szükséges paraméterek létrehozásának és előállításának lépéseit tartalmazza.  A Pivotal Cloud Foundry megoldást kereséssel megtalálhatja az Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)-en.

![Helyettesítő képszöveg](media/deploy/pcf-marketplace.png "A Pivotal Cloud Foundry keresése az Azure-ban")


## <a name="generate-an-ssh-public-key"></a>Nyilvános SSH-kulcs létrehozása

A nyilvános SSH-kulcsok létrehozásának többféle módja is elérhető a Windows, Mac vagy Linux rendszeren.

```Bash
ssh-keygen -t rsa -b 2048
```
- Az Ön környezetére vonatkozó [utasítások]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) megtekintéséhez kattintson ide.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]
>
> Az egyszerű szolgáltatások létrehozásához tulajdonosi fiókengedélyekre van szükség.  Emellett írhat egy szkriptet az egyszerű szolgáltatás létrehozásának automatizálásához. Például használhatja az [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) Azure CLI-szkriptet.

1. Jelentkezzen be Azure-fiókjába.

    `az login`

    ![Helyettesítő képszöveg](media/deploy/az-login-output.png "Azure CLI-bejelentkezés")
 
    Másolja ki az „id” értékét, amelyet később a **subscription ID** és a **tenantId** értékeként használhat.

2. Állítsa be az alapértelmezett előfizetést ehhez a konfigurációhoz.

    `az account set -s {id}`

3. Hozzon létre egy AAD-alkalmazást a PCF-hez, és adjon meg egy csak betűket és számokat tartalmazó egyedi jelszót.  Tárolja el a jelszót, amelyet később a **clientSecret** értékeként használhat.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Ezt követően másolja ki a kimenetben szereplő „appId” értékét, amelyet később a **ClientID** értékeként használhat.

    > [!NOTE]
    >
    > Válassza ki saját alkalmazásának a kezdőlapját és azonosító URI-ját.  Pl.: http://www.contoso.com.

4. Hozzon létre egy egyszerű szolgáltatást az új „appId” azonosítóval.

    `az ad sp create --id {appId}`

5. Állítsa be az egyszerű szolgáltatás jogosultságát **közreműködői** szerepkörre.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Vagy használhatja a következőt…

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Helyettesítő képszöveg](media/deploy/svc-princ.png "Egyszerű szolgáltatás szerepkör-hozzárendelése")

6. Győződjön meg arról, hogy az appId, a password és a tenantId segítségével sikeresen be tud jelentkezni az egyszerű szolgáltatásba.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Hozzon létre egy .json-fájlt a következő formátumban a fenti **subscription ID**, **tenantId**, **clientID** és **clientSecret** érték használatával.  Mentse a fájlt.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>A Pivotal Network-token beszerzése

1. Jelentkezzen be [Pivotal Network](https://network.pivotal.io)-fiókjába.
2. Az oldal jobb felső részén kattintson a profil nevére, majd válassza az **Edit Profile** (Profil szerkesztése) lehetőséget.
3. Görgessen le a lap aljára, és másolja ki a **LEGACY API TOKEN** (ÖRÖKÖLT API-TOKEN) elem értékét.  Ez lesz az Ön **Pivotal Network-tokenje**, amelynek értékére a későbbiekben szüksége lesz.

## <a name="provision-your-cloud-foundry-on-azure"></a>A Cloud Foundry üzembe helyezése az Azure-ban

1. Most már minden paraméterrel rendelkezik a [Pivotal Cloud Foundry Azure-fürt](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) üzembe helyezéséhez.
2. Adja meg a paramétereket, és hozza létre saját PCF-fürtjét.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Az üzemelő példány ellenőrzése és bejelentkezés a Pivotal Ops Managerbe

1. A PCF-fürtön meg kell jelennie az üzembe helyezés állapotának.

    ![Helyettesítő képszöveg](media/deploy/deployment.png "Azure-beli üzemelő példány állapota")

2. A bal oldali navigációs menüben kattintson az **Üzemelő példányok** hivatkozásra a PCF Ops Manager hitelesítő adatainak lekéréséhez, majd a következő oldalon kattintson az **Üzemelő példány neve** elemre.
3. A bal oldali navigációs sávon kattintson a **Kimenetek** hivatkozásra a PCF Ops Manager URL-címének, felhasználónevének és jelszavának megjelenítéséhez.  Az „OPSMAN-FQDN” értéke az URL-cím lesz.
 
    ![Helyettesítő képszöveg](media/deploy/deploy-outputs.png "A Cloud Foundry-környezet kimenete")
 
4. Nyissa meg az URL-címet egy böngészőben, és a bejelentkezéshez adja meg az előző lépésben szereplő hitelesítő adatokat.

    ![Helyettesítő képszöveg](media/deploy/pivotal-login.png "A Pivotal bejelentkezési oldala")
         
    > [!NOTE]
    >
    > Ha az Internet Explorer nem tudja megnyitni az URL-címet, és a nem biztonságos webhely üzenetet kapja, válassza a „További információ”, majd a „Továbblépés a weblapra” lehetőséget.  Firefox esetén kattintson a „Speciális” gombra, majd vegye fel a tanúsítványt a listára.

5. A PCF Ops Managerben megjelennek az üzembe helyezett Azure-példányok. Most már innen végezheti az alkalmazások üzembe helyezését és kezelését!
               
    ![Helyettesítő képszöveg](media/deploy/ops-mgr.png "Üzembe helyezett Azure-példány a Pivotalban")
 
