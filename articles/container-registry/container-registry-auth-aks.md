---
title: Az Azure Kubernetes Service-ből az Azure Container Registry a hitelesítéshez
description: Ismerje meg, hogyan biztosíthat hozzáférést a privát tárolójegyzékben található rendszerképek Azure Kubernetes Service egy Azure Active Directory egyszerű szolgáltatás használatával.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: marsma
ms.openlocfilehash: 36b66fdcd157e4c44fcd451c8cc07ba68242b583
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888762"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Az Azure Kubernetes Service-ből az Azure Container Registry a hitelesítéshez

Amikor az Azure Container Registry (ACR) az Azure Kubernetes Service (AKS) használ, olyan hitelesítési mechanizmust kell hozható létre. Ez a dokumentum részletesen két Azure-szolgáltatás közötti hitelesítéshez ajánlott konfigurációkat.

## <a name="grant-aks-access-to-acr"></a>Támogatás az AKS-hozzáférés az ACR-be

AKS-fürt létrehozásakor egy egyszerű szolgáltatást is létrehoz, amely a fürt működési követelmények az Azure-erőforrások kezeléséhez. Egyszerű szolgáltatás is használható a hitelesítés az ACR-beállításjegyzékbe. Ehhez egy szerepkör-hozzárendelés kell létrehozni a szolgáltatás egyszerű olvasási hozzáférést az ACR-erőforráshoz.

A következő minta segítségével végrehajtani a műveletet.

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
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Hozzáférés a Kubernetes titkos kulcs

Bizonyos esetekben az AKS által használt egyszerű szolgáltatás nem tudja felvenni a hatókörbe az ACR-beállításjegyzékbe. Ezekben az esetekben hozzon létre egy egyedi szolgáltatásnevet, és azt az ACR-beállításjegyzékbe a hatókört.

A következő szkriptet a szolgáltatásnév létrehozásához használható.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Egyszerű szolgáltatás hitelesítő adatai már tárolható kubernetes [kép lekérési titkos] [ image-pull-secret] és tárolók az AKS-fürt futtatásakor.

A következő parancs létrehozza a Kubernetes titkos. Cserélje le a kiszolgáló nevét az ACR bejelentkezési kiszolgáló, a felhasználónevet a szolgáltatásnév-azonosítót, és a jelszót a szolgáltatásnév jelszava.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

A Kubernetes titkos kulcsot is használható a pod üzembe helyezési használatával a `ImagePullSecrets` paraméter.

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
