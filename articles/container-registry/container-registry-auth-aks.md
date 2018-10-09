---
title: Az Azure Kubernetes Service-ből az Azure Container Registry a hitelesítéshez
description: Ismerje meg, hogyan biztosíthat hozzáférést a privát tárolójegyzékben található rendszerképek Azure Kubernetes Service egy Azure Active Directory egyszerű szolgáltatás használatával.
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: d08fc0cb8e3203a60cbd426145ec50bb3636e758
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857125"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Az Azure Kubernetes Service-ből az Azure Container Registry a hitelesítéshez

Amikor az Azure Container Registry (ACR) az Azure Kubernetes Service (AKS) használ, olyan hitelesítési mechanizmust kell hozható létre. Ez a cikk részletesen az alábbi két Azure-szolgáltatások közötti hitelesítés ajánlott konfigurációkat.

## <a name="grant-aks-access-to-acr"></a>Támogatás az AKS-hozzáférés az ACR-be

Amikor egy AKS-fürtöt hoz létre, az Azure is létrehoz egy egyszerű szolgáltatást az egyéb Azure-erőforrások a fürt működési követelmények támogatásához. Hitelesítés az ACR-beállításjegyzékből ezt automatikusan létrehozott szolgáltatásnév is használhat. Ehhez hozzon létre egy Azure ad-ben kell [szerepkör-hozzárendelés](../role-based-access-control/overview.md#role-assignments) , amely engedélyezi a fürt szolgáltatásnév hozzáférhessen a tárolóregisztrációs adatbázisba.

Használja a következő szkriptet az AKS által létrehozott szolgáltatás egyszerű hozzáférést egy Azure container registrybe. Módosítsa a `AKS_*` és `ACR_*` változók a parancsfájl futtatása előtt környezete számára.

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

Bizonyos esetekben előfordulhat, hogy nem kell a szükséges szerepkör hozzárendelése az automatikusan létrehozott AKS szolgáltatás egyszerű hozzáférést, az ACR-be. Például a szervezet biztonsági modell, mert nincs elegendő jogosultsága a az Azure AD-címtárát az AKS által létrehozott szolgáltatásnév, szerepkör hozzárendelése. Ebben az esetben hozzon létre egy új egyszerű szolgáltatást, majd hozzáférést, a container registry-Kubernetes kép lekérési titkos kulcs használatával.

A következő parancsfájl használatával hozzon létre egy új egyszerű szolgáltatást (fogja használni a saját hitelesítő adatait a kép lekérési Kubernetes titkos). Módosítsa a `ACR_NAME` változó a környezetnek a parancsfájl futtatása előtt.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a 'Reader' role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

A szolgáltatásnév hitelesítő adatainak most már tárolhat egy Kubernetes [kép lekérési titkos kulcs][image-pull-secret], amely az AKS-fürt fog hivatkozni, amikor a futó tárolók.

Használja a következő **kubectl** parancsot a Kubernetes titkos kódjának létrehozása. Cserélje le `<acr-login-server>` az Azure container registry (szerepel a formátum "acrname.azurecr.io") teljes nevét. Cserélje le `<service-principal-ID>` és `<service-principal-password>` az előző parancsfájl futtatásával beszerzett értékekkel. Cserélje le `<email-address>` bármely szabályos e-mail-címmel.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Mostantól használhatja a Kubernetes titkos pod telepítések esetén adja meg a nevét (ebben az esetben "acr-hitelesítés") található a `imagePullSecrets` paramétert:

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
