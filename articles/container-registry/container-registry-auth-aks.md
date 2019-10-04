---
title: Hitelesítés Azure Container Registry az Azure Kubernetes szolgáltatással
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure Kubernetes szolgáltatásban található lemezképekhez a privát tároló beállításjegyzékében egy Azure Active Directory egyszerű szolgáltatásnév használatával.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: dc5276227913d2da6e52ee3c0fb493b98e86688a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827759"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Hitelesítés Azure Container Registry az Azure Kubernetes szolgáltatással

Ha Azure Container Registryt (ACR) használ az Azure Kubernetes szolgáltatással (ak), akkor hitelesítési mechanizmust kell létrehoznia. Ez a cikk a két Azure-szolgáltatás közötti hitelesítés ajánlott konfigurációit részletezi.

Csak az alábbi hitelesítési módszerek egyikét kell konfigurálnia. A leggyakoribb módszer az, hogy [hozzáférést biztosítson az AK egyszerű szolgáltatásának használatával](#grant-aks-access-to-acr). Ha konkrét igényei vannak, igény szerint [a Kubernetes](#access-with-kubernetes-secret)-titkokat is megadhatja.

Ez a cikk azt feltételezi, hogy már létrehozott egy AK-fürtöt, és hozzáfér a fürthöz a `kubectl` parancssori ügyféllel. Ha ehelyett fürtöt szeretne létrehozni, és hozzáférést szeretne konfigurálni egy tároló-beállításjegyzékhez a fürt létrehozási ideje alatt, tekintse meg a következőt: [Tutorial: Helyezzen üzembe egy no__t-0 AK-fürtöt, vagy [végezzen hitelesítést Azure Container Registry az Azure Kubernetes szolgáltatásban](../aks/cluster-container-registry-integration.md).

## <a name="grant-aks-access-to-acr"></a>AK-hozzáférés biztosítása az ACR-hez

Ha AK-fürtöt hoz létre, az Azure egy egyszerű szolgáltatásnevet is létrehoz, amely támogatja a fürt más Azure-erőforrásokkal való működőképességét. Ezt az automatikusan létrehozott egyszerű szolgáltatást használhatja az ACR-beállításjegyzékkel való hitelesítéshez. Ehhez létre kell hoznia egy Azure AD-szerepkör- [hozzárendelést](../role-based-access-control/overview.md#role-assignments) , amely megadja a fürt egyszerű hozzáférését a tároló beállításjegyzékéhez.

Az alábbi szkripttel engedélyezheti az AK által generált egyszerű szolgáltatás lekéréses hozzáférését egy Azure Container registryhez. A parancsfájl `AKS_*` futtatása `ACR_*` előtt módosítsa a környezetét és változóit.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Hozzáférés a titkos Kubernetes

Bizonyos esetekben előfordulhat, hogy nem tudja hozzárendelni a szükséges szerepkört az automatikusan generált AK-szolgáltatáshoz, amely hozzáférést biztosít az ACR-hez. Előfordulhat például, hogy a szervezete biztonsági modellje miatt nem rendelkezik a megfelelő engedélyekkel a Azure Active Directory-bérlőben ahhoz, hogy szerepkört rendeljen az AK által generált egyszerű szolgáltatáshoz. Egy szerepkör egy egyszerű szolgáltatáshoz való hozzárendeléséhez az Azure AD-fióknak írási engedélyre van szüksége az Azure AD-bérlőhöz. Ha nincs engedélye, létrehozhat egy új szolgáltatásnevet, majd hozzáférést biztosíthat a tároló beállításjegyzékéhez egy Kubernetes-rendszerkép lekérési titok használatával.

A következő szkripttel hozzon létre egy új egyszerű szolgáltatást (a Kubernetes-rendszerkép lekérési titkának hitelesítő adatait fogja használni). A parancsfájl `ACR_NAME` futtatása előtt módosítsa a környezet változóját.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Most már tárolhatja a szolgáltatásnév hitelesítő adatait egy Kubernetes-Rendszerképbeli lekérési [titokban][image-pull-secret], amelyet az AK-fürt a tárolók futtatásakor fog hivatkozni.

A Kubernetes titkos kulcs létrehozásához használja a következő **kubectl** -parancsot. Cserélje `<acr-login-server>` le az kifejezést az Azure Container Registry teljes nevére (ez a "acrname.azurecr.IO" formátumban van). Cserélje `<service-principal-ID>` le `<service-principal-password>` a és a értéket az előző szkript futtatásával kapott értékekre. Cserélje `<email-address>` le a t bármilyen jól formázott e-mail-címre.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Most már használhatja a Kubernetes titkot a pod üzemelő példányokban, ha megadja a nevét (ebben az esetben "ACR-Auth") a `imagePullSecrets` paraméterben:

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
