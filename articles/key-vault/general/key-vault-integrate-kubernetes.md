---
title: Az Azure Key Vault integrálása a Kubernetesszel
description: Ebben az oktatóanyagban a titkokat az Azure Key vaultban érheti el, és beolvashatja az Kubernetes hüvelybe való csatlakoztatáshoz a Secrets Store Container Storage Interface (CSI) illesztőprogram használatával.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/25/2020
ms.openlocfilehash: bfcaf9d4b1d03457f2e4cddd2e0eaf9d9d58eee2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869184"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Oktatóanyag: az Azure Key Vault-szolgáltató konfigurálása és futtatása a Secrets Store CSI-illesztőprogramhoz a Kubernetes-ben

> [!IMPORTANT]
> A CSI-illesztőprogram egy nyílt forráskódú projekt, amelyet az Azure technikai támogatása nem támogat. Az oldal alján található GitHub-hivatkozásra kattintva jelentse be a CSI-illesztőprogram Key Vault integrálásával kapcsolatos összes visszajelzést és problémát. Ez az eszköz arra szolgál, hogy a felhasználók önállóan telepítsék a fürtöket, és visszajelzést gyűjtsenek a Közösségtől.

Ebben az oktatóanyagban a titkokat az Azure Key vaultban érheti el és kéri le, a Secrets Store Container Storage Interface (CSI) illesztőprogram használatával a titkokat a Kubernetes hüvelybe csatlakoztatni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy egyszerű szolgáltatásnevet, vagy használjon felügyelt identitásokat.
> * Helyezzen üzembe egy Azure Kubernetes-szolgáltatási (ak-) fürtöt az Azure CLI használatával.
> * Telepítse a Helm és a Secrets Store CSI-illesztőprogramot.
> * Hozzon létre egy Azure Key vaultot, és állítsa be a titkokat.
> * Hozzon létre egy saját SecretProviderClass objektumot.
> * Rendeljen hozzá egy egyszerű szolgáltatásnevet, vagy használjon felügyelt identitásokat.
> * Üzembe helyezheti a pod-t a kulcstartóból csatlakoztatott titkos kulcsokkal.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Az oktatóanyag elindítása előtt telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)-t.

## <a name="create-a-service-principal-or-use-managed-identities"></a>Egyszerű szolgáltatásnév létrehozása vagy felügyelt identitások használata

Ha felügyelt identitások használatát tervezi, a következő szakaszra léphet.

Hozzon létre egy egyszerű szolgáltatásnevet annak szabályozására, hogy mely erőforrások érhetők el az Azure Key vaultból. Az egyszerű szolgáltatásnév hozzáférését a hozzá rendelt szerepkörök korlátozzák. Ezzel a szolgáltatással szabályozhatja, hogy az egyszerű szolgáltatásnév hogyan kezelheti a titkos kulcsokat. A következő példában az egyszerű szolgáltatásnév neve *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
A művelet a kulcs/érték párok sorozatát adja vissza:

![A contosoServicePrincipal appId és jelszavát ábrázoló képernyőkép](../media/kubernetes-key-vault-1.png)

Másolja a **AppID** és a **jelszó** hitelesítő adatait a későbbi használatra.

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Azure Kubernetes szolgáltatásbeli (ak-) fürt üzembe helyezése az Azure CLI használatával

Nincs szükség a Azure Cloud Shell használatára. Az Azure CLI-vel telepített parancssor (Terminal) elegendő lesz. 

Fejezze be az [Azure Kubernetes Service-fürt üzembe helyezése az Azure CLI használatával](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)című szakaszt az "erőforráscsoport létrehozása", "" AK-fürt létrehozása "és" kapcsolódás a fürthöz "című részekben. 

> [!NOTE] 
> Ha egy egyszerű szolgáltatásnév helyett Pod-azonosítót kíván használni, a Kubernetes-fürt létrehozásakor ügyeljen arra, hogy a következő parancsban látható módon engedélyezze azt:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Állítsa a PATH környezeti változót](https://www.java.com/en/download/help/path.xml) a letöltött *kubectl.exe* fájlra.
1. A következő parancs használatával keresse meg a Kubernetes verzióját, amely az ügyfél és a kiszolgáló verzióját adja meg. Az ügyfél verziószáma a telepített *kubectl.exe* fájl, és a kiszolgáló verziója a fürtön futó Azure Kubernetes Services (ak).
    ```azurecli
    kubectl version
    ```
1. Győződjön meg arról, hogy a Kubernetes verziója 1.16.0 vagy újabb. A következő parancs frissíti a Kubernetes-fürtöt és a csomópont-készletet is. A parancs végrehajtása néhány percet is igénybe vehet. Ebben a példában az erőforráscsoport *contosoResourceGroup*, a Kubernetes-fürt pedig *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. A létrehozott AK-fürt metaadatainak megjelenítéséhez használja a következő parancsot. Másolja a **principalId**, a **clientId**, a **subscriptionId**és a **nodeResourceGroup** a későbbi használatra. Ha a KÉRDÉSes fürt nem lett engedélyezve a felügyelt identitásokkal, a **principalId** és a **clientId** null értékű lesz. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    A kimenet mindkét paraméter kiemelve jelenik meg:
    
    ![Képernyőkép az Azure CLI-ről a principalId és a clientId értékkel az Azure CLI-vel, a ](../media/kubernetes-key-vault-2.png) ![ subscriptionId és a nodeResourceGroup értékekkel kiemelve](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>A Helm és a Secrets Store CSI-illesztőprogram telepítése

A Secrets Store CSI-illesztőprogram telepítéséhez először telepítenie kell a [Helm](https://helm.sh/docs/intro/install/)-t.

A [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) illesztőprogram-felületén megtekintheti az Azure Key Vault-példányban tárolt titkos kódokat, majd az illesztőprogram felületével csatlakoztathatja a titkos tartalmat a Kubernetes hüvelybe.

1. Győződjön meg arról, hogy a Helm verziója v3 vagy újabb:
    ```azurecli
    helm version
    ```
1. Telepítse a Secrets Store CSI-illesztőprogramot és a Azure Key Vault szolgáltatót az illesztőprogramhoz:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Azure Key Vault létrehozása és a titkok beállítása

A saját kulcstartó létrehozásához és a titkos kulcsok beállításához kövesse az Azure CLI-vel való [Azure Key Vault titkának beállítása és beolvasása](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)című témakör utasításait.

> [!NOTE] 
> Nem kell használnia Azure Cloud Shell, vagy létre kell hoznia egy új erőforráscsoportot. Használhatja a korábban a Kubernetes-fürthöz létrehozott erőforráscsoportot.

## <a name="create-your-own-secretproviderclass-object"></a>Saját SecretProviderClass objektum létrehozása

Ha a Secrets Store CSI-illesztőprogramhoz tartozó, szolgáltatói specifikus paraméterekkel rendelkező egyéni SecretProviderClass-objektumot szeretne létrehozni, [használja ezt a sablont](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/test/bats/tests/azure_v1alpha1_secretproviderclass.yaml). Ez az objektum identitás-hozzáférést biztosít a kulcstartóhoz.

A minta SecretProviderClass YAML fájljában adja meg a hiányzó paramétereket. A következő paraméterek szükségesek:

* **userAssignedIdentityID**: az egyszerű szolgáltatásnév ügyfél-azonosítója
* **keyvaultName**: a kulcstartó neve
* **objektumok**: a csatlakoztatni kívánt titkos tartalom tárolója
    * **objectName**: a titkos tartalom neve
    * **objektumtípus**: az objektum típusa (titok, kulcs, tanúsítvány)
* **resourceGroup**: az erőforráscsoport neve
* **subscriptionId**: a kulcstartó előfizetés-azonosítója
* **tenantID**: a Key Vault bérlői azonosítója vagy CÍMTÁR-azonosítója

A frissített sablon a következő kódban látható. Töltse le YAML-fájlként, és töltse ki a kötelező mezőket. Ebben a példában a Key Vault **contosoKeyVault5**van. Két titkot, **secret1** és **secret2**tartalmaz.

> [!NOTE] 
> Ha felügyelt identitásokat használ, állítsa a **usePodIdentity** értéket *igaz*értékre, és állítsa be a **userAssignedIdentityID** értéket idézőjelek közé (**""**). 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
Az alábbi képen az az kulcstartó **show--Name contosoKeyVault5** konzol kimenete látható a kapcsolódó Kiemelt metaadatokkal:

![Az "az kulcstartó show--Name contosoKeyVault5" konzol kimenetét ábrázoló képernyőkép](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Szolgáltatásnév kiosztása vagy felügyelt identitások használata

### <a name="assign-a-service-principal"></a>Egyszerű szolgáltatás hozzárendelése

Ha egyszerű szolgáltatást használ, adjon meg engedélyeket a kulcstartó eléréséhez és a titkok beolvasásához. Rendelje hozzá az *olvasó* szerepkört, és adja meg az egyszerű szolgáltatásnév számára a kulcsok a kulcstartóból való *beszerzéséhez* szükséges engedélyeket a következő paranccsal:

1. Rendelje hozzá a szolgáltatásnevet a meglévő kulcstartóhoz. A **$AZURE _CLIENT_ID** paraméter az a **AppID** , amelyet az egyszerű szolgáltatásnév létrehozása után másolt.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    A parancs kimenete az alábbi képen látható: 

    ![A principalId értéket ábrázoló képernyőkép](../media/kubernetes-key-vault-5.png)

1. Adja meg az egyszerű szolgáltatás engedélyeit a titkok beszerzéséhez:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Ezzel konfigurálta az egyszerű szolgáltatásnevet a Key vaultban található titkos kódok olvasásához szükséges engedélyekkel. A **$AZURE _CLIENT_SECRET** az egyszerű szolgáltatásnév jelszava. Adja hozzá a szolgáltatás egyszerű hitelesítő adatait olyan titkos Kubernetes, amelyet a Secrets Store CSI-illesztőprogramja elérhet:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Ha telepíti a Kubernetes Pod-ot, és hibaüzenetet kap egy érvénytelen ügyfél-titkos AZONOSÍTÓról, akkor előfordulhat, hogy egy korábbi, lejárt vagy alaphelyzetbe állított ügyfél-titkos AZONOSÍTÓval rendelkezik. A probléma megoldásához törölje a *Secrets-Store-creds Secret-* et, és hozzon létre egy újat az aktuális ügyfél-titkos azonosítóval. A *Secrets-Store-creds*törléséhez futtassa a következő parancsot:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Ha elfelejtette a szolgáltatásnév ügyfél-titkos AZONOSÍTÓját, a következő paranccsal állíthatja alaphelyzetbe:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Felügyelt identitások használata

Ha felügyelt identitásokat használ, rendeljen meghatározott szerepköröket a létrehozott AK-fürthöz. 

1. Felhasználó által hozzárendelt felügyelt identitás létrehozásához, listázásához vagy olvasásához az AK-fürtnek hozzá kell rendelnie a [felügyelt identitás-kezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkört. Győződjön meg arról, hogy a **$clientId** a Kubernetes-fürt clientId. A hatókör esetében az Azure-előfizetési szolgáltatás alatt lesz, különösen az AK-fürt létrehozásakor létrejött csomópont-erőforráscsoport. Ez a hatókör gondoskodik arról, hogy csak az adott csoportba tartozó erőforrásokat érinti az alábbi szerepkörök. 

    ```azurecli
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Telepítse a Azure Active Directory (Azure AD) identitását az AK-ba.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Hozzon létre egy Azure AD-identitást. A kimenetben másolja a **clientId** és a **principalId** későbbi használatra.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Rendelje hozzá az *olvasó* szerepkört a Key Vault előző lépésében létrehozott Azure ad-identitáshoz, majd adja meg a személyazonossági engedélyeket a kulcstartóból származó titkos kulcsok beszerzéséhez. Használja az Azure AD-identitás **clientId** és **principalId** .
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>A pod üzembe helyezése csatlakoztatott titkos kulcsokkal a Key vaultból

A SecretProviderClass objektum konfigurálásához futtassa a következő parancsot:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Egyszerű szolgáltatásnév használata

Ha egyszerű szolgáltatást használ, használja a következő parancsot a Kubernetes-hüvelyek üzembe helyezéséhez a SecretProviderClass és a korábban konfigurált Secrets-Store-creds használatával. A központi telepítési sablonok:
* [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml) rendszerhez
* [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) rendszerhez

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Felügyelt identitások használata

Ha felügyelt identitásokat használ, hozzon létre egy *AzureIdentity* a fürtben, amely a korábban létrehozott identitásra hivatkozik. Ezután hozzon létre egy *AzureIdentityBinding* , amely a létrehozott AzureIdentity hivatkozik. Adja meg a paramétereket a következő sablonban, majd mentse a *podIdentityAndBinding. YAML*néven.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Futtassa a következő parancsot a kötés végrehajtásához:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Ezután üzembe helyezi a pod-t. A következő kód a telepítési YAML-fájl, amely az előző lépésben a pod Identity kötést használja. Mentse ezt a fájlt *podBindingDeployment. YAML*néven.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

Futtassa a következő parancsot a pod üzembe helyezéséhez:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>A pod-állapot és a titkos tartalom keresése 

Az üzembe helyezett hüvelyek megjelenítéséhez futtassa a következő parancsot:
```azurecli
kubectl get pods
```

A pod állapotának megtekintéséhez futtassa a következő parancsot:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Képernyőfelvétel: a Pod "Running" állapotát megjelenítő Azure CLI-kimenet, amely az összes eseményt "normál" értékre mutat. ](../media/kubernetes-key-vault-6.png)

A kimeneti ablakban az üzembe helyezett Pod-nek *futó* állapotban kell lennie. A lenti **események** szakaszban az összes eseménytípus *normál*formában jelenik meg.

Miután ellenőrizte, hogy a pod fut-e, ellenőrizheti, hogy a pod tartalmazza-e a Key Vault titkait.

A pod-ban található összes titok megjelenítéséhez futtassa a következő parancsot:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Egy adott titok tartalmának megjelenítéséhez futtassa a következő parancsot:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Ellenőrizze, hogy a titkos kód tartalma megjelenik-e.

## <a name="next-steps"></a>Következő lépések

A Key Vault helyreállításának biztosításához lásd:
> [!div class="nextstepaction"]
> [A Soft delete bekapcsolása](https://docs.microsoft.com/azure/key-vault/general/soft-delete-cli)
