---
title: A Pivotal Cloud Foundry-fürt létrehozása az Azure-ban
description: Ismerje meg, hogyan állítható be az Azure-ban a Pivotal Cloud Foundry (PCF)-fürt üzembe helyezése szükséges paraméterek
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
ms.openlocfilehash: 9514118e1f29faab937ed01899b5947789ca9735
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101398"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>A Pivotal Cloud Foundry-fürt létrehozása az Azure-ban

Ebben az oktatóanyagban lépéseit gyors létrehozása és a paraméterek, az Azure-ban a Pivotal Cloud Foundry (PCF)-fürt üzembe helyezése kell létrehozni. A Pivotal Cloud Foundry-megoldás megkeresése, a keresés végrehajtása az Azure-ban [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Keresés a Pivotal Cloud Foundry az Azure-ban](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Nyilvános SSH-kulcs létrehozása

Többféleképpen is lehet hozzon létre egy secure shell-(SSH) nyilvános kulcsot a Windows, Mac vagy Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

További információkért lásd: [használata SSH-kulcsok az Azure-on Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]
>
> Hozzon létre egy egyszerű szolgáltatást, tulajdonos fiók engedéllyel kell. Automatizálhatja az egyszerű szolgáltatásnév létrehozása egy parancsfájlt is írhat. Például használhatja az Azure CLI [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Jelentkezzen be Azure-fiókjába.

    `az login`

    ![Azure CLI-azonosító](media/deploy/az-login-output.png )
 
    Másolja az "id" értéket, a **előfizetés-azonosító**, és másolja a "tenantId" értéket későbbi használatra.

2. Állítsa be az alapértelmezett előfizetést ehhez a konfigurációhoz.

    `az account set -s {id}`

3. A PCF egy Azure Active Directory-alkalmazás létrehozása. Adjon meg egy egyedi alfanumerikus jelszót. Store a jelszót, mint a **clientSecret** későbbi használat céljából.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    A kimenetben: az "appId" értéke másolja a **clientID** későbbi használat céljából.

    > [!NOTE]
    >
    > Válassza ki a saját alkalmazás kezdőlapját és azonosító URI-t, például http://www.contoso.com.

4. Egyszerű szolgáltatás létrehozása új app ID azonosítójával.

    `az ad sp create --id {appId}`

5. Állítsa be a jogosultsági szerepkört, az egyszerű szolgáltatás közreműködője.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Is használhatja, vagy

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Szolgáltatás egyszerű szerepkör-hozzárendelés](media/deploy/svc-princ.png )

6. Győződjön meg arról, hogy sikeresen bejelentkezhet az egyszerű szolgáltatás használatával, az alkalmazás-Azonosítóját, a jelszót és a bérlő azonosítója.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Hozzon létre egy .JSON kiterjesztésű fájlt a következő formátumban. Használja a **előfizetés-azonosító**, **tenantID**, **clientID**, és **clientSecret** korábban kimásolt értékekkel. Mentse a fájlt.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>A Pivotal hálózat token beszerzése

1. Regisztráljon vagy jelentkezzen be a [Pivotal hálózat](https://network.pivotal.io) fiókot.
2. Válassza ki a profil nevét a lap jobb felső sarkában. Válassza ki **profil szerkesztése**.
3. Görgessen a lap, és másolja a aljára a **ÖRÖKÖLT API TOKEN** értéket. Ez az érték a **Pivotal hálózat Token** érték, amely későbbi használatra.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Az Azure-ban a Cloud Foundry-fürt üzembe helyezése

Most, hogy minden a paramétereket kell rendelkezni a [Pivotal Cloud Foundry Azure-beli fürt](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Adja meg a paramétereket, és a PCF-fürt létrehozásához.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>A telepítés ellenőrzése, és jelentkezzen be a Pivotal az Ops Manager

1. A PCF-fürt egy központi telepítési állapotát jeleníti meg.

    ![Az Azure-telepítés állapota](media/deploy/deployment.png )

2. Válassza ki a **központi telepítések** hitelesítő adatainak lekérése a PCF az Ops Manager a bal oldali navigációs hivatkozás. Válassza ki a **üzemelő példány neve** a következő oldalon.
3. A bal oldali navigációs sávján válassza ki a **kimenetek** jeleníti meg az URL-címe, felhasználónév és jelszó a PCF az Ops Manager mutató hivatkozást. Az "OPSMAN-FQDN" érték: az URL-címet.
 
    ![Cloud Foundry üzembe helyezési kimenet](media/deploy/deploy-outputs.png )
 
4. Indítsa el az URL-CÍMÉT egy webböngészőben. Adja meg a hitelesítő adatokat, jelentkezzen be az előző lépésben.

    ![A Pivotal bejelentkezési oldal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Ha az Internet Explorer böngésző "Hely nem biztonságos" figyelmeztető üzenet miatt hiúsul meg, válassza ki a **bővebben** és a képernyőn látható weblapon. Firefox kiválasztása **előzetes** , és adja hozzá a tanúsítvány a folytatáshoz.

5. A PCF az Ops Manager megjeleníti a telepített Azure-példányokon. Most már telepítheti és kezelheti az alkalmazások itt.
               
    ![Pivotal üzembe helyezett Azure-példány](media/deploy/ops-mgr.png )
 
