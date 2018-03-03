---
title: "A hitelesítést az Azure Tárolószolgáltatás Azure tároló beállításjegyzék"
description: "Megtudhatja, hogyan biztosít hozzáférést a személyes tárolót beállításjegyzék lemezképeihez az Azure Tárolószolgáltatás egy Azure Active Directory szolgáltatás egyszerű."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: a115df87feea0c9f7987e0c65f6f880325d88ca2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>A hitelesítést az Azure Tárolószolgáltatás Azure tároló beállításjegyzék

Amikor a Azure tároló beállításjegyzék (ACR) Azure tároló szolgáltatás (AKS) használ, olyan hitelesítési módszert kell létrehozni. Ez a dokumentum részletesen az ajánlott konfigurációk a két Azure szolgáltatás közötti hitelesítéshez.

## <a name="grant-aks-access-to-acr"></a>ACR GRANT AKS elérésére

Egy AKS fürt létrehozásakor egy egyszerű szolgáltatás is létrejön, fürt működési követelmények az Azure-erőforrások kezeléséhez. A szolgáltatás egyszerű is egy ACR beállításjegyzék-val történő hitelesítéshez használható. Ehhez az szükséges, szerepkör-hozzárendelés kell létrehozni a szolgáltatás egyszerű olvasási jogot a ACR erőforráshoz. 

Az alábbi minta a művelet végrehajtásához használható.

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

## <a name="access-with-kubernetes-secret"></a>Hozzáférés az Kubernetes titkos kulcs

Bizonyos esetekben a szolgáltatás egyszerű AKS által használt hatóköre nem módosítható a ACR beállításjegyzék. Ezekben az esetekben a egyedi szolgáltatásnevet létrehozni, és csak a ACR beállításjegyzék hatókör azt.

A következő parancsfájl segítségével az egyszerű szolgáltatásnév létrehozása. 

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

A szolgáltatás egyszerű hitelesítő adatait most tárolható egy Kubernetes [kép lekéréses titkos] [ image-pull-secret] és hivatkozott AKS-fürtben lévő tárolók futtatásakor. 

A következő parancs létrehozza a Kubernetes titkos. Cserélje le a kiszolgáló nevét a ACR bejelentkezési kiszolgálóval, a felhasználó nevét a résztvevő-azonosító és a jelszó és a szolgáltatás egyszerű jelszóval.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

A Kubernetes titkos kulcsot használó pod telepítés is használható a `ImagePullSecrets` paraméter. 

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
