---
title: Azure Key Vault integrálása a Kubernetes
description: Ebben az oktatóanyagban a titkokat a Secrets Store CSI (Container Storage Interface) illesztőprogram segítségével érheti el és olvashatja be Azure Key Vault a Kubernetes hüvelybe való csatlakoztatáshoz.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637167"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Oktatóanyag: a Kubernetes-on található Secret Store CSI-illesztőprogram Azure Key Vault-szolgáltatójának konfigurálása és futtatása

Ebben az oktatóanyagban a titkokat a Secrets Store CSI (Container Storage Interface) illesztőprogram segítségével érheti el és olvashatja be Azure Key Vault a Kubernetes hüvelybe való csatlakoztatáshoz.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Egyszerű szolgáltatás létrehozása
> * Azure Kubernetes Service-fürt üzembe helyezése
> * A Helm és a Secrets Store CSI-illesztőprogramjának telepítése
> * Azure Key Vault létrehozása és a titkos kódok beállítása
> * Saját SecretProviderClass objektum létrehozása
> * A pod üzembe helyezése csatlakoztatott titkokkal Key Vault

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Az oktatóanyag elindítása előtt telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)-t.

## <a name="create-a-service-principal-or-use-managed-identities"></a>Egyszerű szolgáltatásnév létrehozása vagy felügyelt identitások használata

Ha felügyelt identitások használatát tervezi, a következő szakaszra léphet.

Hozzon létre egy egyszerű szolgáltatásnevet annak szabályozására, hogy milyen erőforrások érhetők el a Azure Key Vault. Az egyszerű szolgáltatásnév hozzáférését a hozzá rendelt szerepkörök korlátozzák. Ezzel a szolgáltatással szabályozhatja, hogy az egyszerű szolgáltatásnév hogyan kezelheti a titkos kulcsokat. Az alábbi példában az egyszerű szolgáltatásnév neve **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
A művelet a kulcs/érték párok sorozatát adja vissza:

![Kép](../media/kubernetes-key-vault-1.png)

Másolja le a **appID** és a **jelszót**. Ezeket a hitelesítő adatokat később kell megadnia.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Azure Kubernetes Service-fürt üzembe helyezése az Azure CLI-vel

Nincs szükség a Azure Cloud Shell használatára, az Azure CLI-vel telepített parancssort (Terminal) is végrehajtja. 

Kövesse ezt az [útmutatót](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) , és hajtsa végre a következő szakaszokat: **hozzon létre egy erőforráscsoportot**, **hozzon létre egy AK-fürtöt**, és **kapcsolódjon a fürthöz**.

**Megjegyzés:** Ha a pod Identity szolgáltatást egy egyszerű szolgáltatásnév helyett szeretné használni. Győződjön meg arról, hogy a Kubernetes-fürt létrehozásakor engedélyezi a következőket:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Állítsa a PATH környezeti változót](https://www.java.com/en/download/help/path.xml) a letöltött "kubectl. exe" fájlra.
1. Az alábbi parancs használatával keresse meg a Kubernetes verzióját. A parancs kimenete az ügyfél és a kiszolgáló verziója lesz. Az ügyfél verziója a telepített "kubectl. exe", miközben a kiszolgáló verziója azon Azure Kubernetes-szolgáltatások, amelyeken a fürt fut.
    ```azurecli
    kubectl version
    ```
1. Győződjön meg arról, hogy a Kubernetes verziója **v 1.16.0** vagy nagyobb. Ez a parancs a Kubernetes-fürtöt és a csomópont-készletet is frissíti. A végrehajtás eltarthat néhány percig. Ebben a példában az erőforráscsoport **contosoResourceGroup** , a Kubernetes-fürt pedig **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Jelenítse meg az alábbi parancs használatával létrehozott AK-fürt metaadatait. Másolja le a **principalId**, a **clientId**, a **subscriptionId**és a **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Ez a kimenet mindkét paraméter kiemelve.
    
    ![Rendszerkép ](../media/kubernetes-key-vault-5.png) ![](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>A Helm és a Secrets Store CSI-illesztőprogramjának telepítése

A Secrets Store CSI-illesztőprogram telepítéséhez a [Helm](https://helm.sh/docs/intro/install/) telepítése szükséges.

A [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) -illesztőprogram felülete lehetővé teszi, hogy egy Azure Key Vault-példányban tárolt titkos tartalmat vagy az illesztőprogram felületét használja a titkos tartalom (ok) Kubernetes hüvelybe való csatlakoztatásához.

1. Ellenőrizze a Helm verzióját, és győződjön meg róla, hogy a v3 vagy nagyobb:
    ```azurecli
    helm version
    ```
1. Telepítse a Secrets Store CSI-illesztőprogramot és a Azure Key Vault szolgáltatót az illesztőprogramhoz:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Azure Key Vault létrehozása és a titkos kódok beállítása

Kövesse ezt az [útmutatót](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) a saját Key Vault létrehozásához és a titkok beállításához.

**Megjegyzés:** Nem kell használnia a Azure Cloud Shell, vagy létre kell hoznia egy új erőforráscsoportot. A Kubernetes-fürthöz korábban létrehozott erőforráscsoport használata rendben van.

## <a name="create-your-own-secretproviderclass-object"></a>Saját SecretProviderClass objektum létrehozása

Használja ezt a [sablont](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) a saját egyéni SecretProviderClass objektum létrehozásához a Secrets Store CSI-illesztőprogram szolgáltatói specifikus paramétereinek biztosításához. Ez az objektum identitás-hozzáférést biztosít a Key Vaulthoz.

A megadott SecretProviderClass YAML-fájl használata. A hiányzó paramétereket kell kitöltenie. A következő paraméterek szükségesek:

1.  **userAssignedIdentityID:** Az egyszerű szolgáltatásnév ügyfél-azonosítója
1.  **keyvaultName:** Key Vault neve
1.  **objektumok:** Ez az objektum a csatlakoztatni kívánt titkos tartalmat fogja tartalmazni.
    1.  **objectName:** A titkos tartalom neve
    1.  **objektumtípus:** Objektumtípus (titok, kulcs, tanúsítvány)
1.  **resourceGroup:** Erőforráscsoport neve
1.  **subscriptionId:** A Key Vault előfizetés-azonosítója
1.  **tenantID:** A Key Vault bérlői azonosítója (azaz a címtár azonosítója)

Alább látható a frissített sablon, töltse le. YAML fájlként, és töltse ki a megfelelő kötelező mezőket. Ebben a példában a Key Vault a **contosoKeyVault5** , és két titka van, a **secret1** és a **secret2**.

**Megjegyzés:** Ha felügyelt identitásokat használ, a **usePodIdentity** mezőnek **igaz értékűnek** kell lennie, és a mező csak idézőjelekkel ( **""**) **userAssignedIdentityID** el. 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Alább látható az "az kulcstartó show--Name contosoKeyVault5" konzol kimenete a kapcsolódó Kiemelt metaadatokkal:

![Kép](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Szolgáltatásnév kiosztása vagy felügyelt identitások használata

### <a name="using-service-principal"></a>Egyszerű szolgáltatásnév használata

Egyszerű szolgáltatásnév használata esetén. Engedélyt kell adnia a szolgáltatásnév számára a Key Vault eléréséhez és a titkok beolvasásához. Rendelje hozzá az **"olvasó"** szerepkört, és adja meg az egyszerű szolgáltatás számára a " **Get"** titkot a Key Vault az alábbi lépések végrehajtásával:

1. Egyszerű szolgáltatásnév kiosztása meglévő Key Vaulthoz. A **$AZURE _CLIENT_ID** paraméter az a **AppID** , amelyet az egyszerű szolgáltatásnév létrehozása után másolt le.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Alább látható a parancs kimenete: 

    ![Kép](../media/kubernetes-key-vault-3.png)

1. Adja meg az egyszerű szolgáltatás engedélyeit a titkok beszerzéséhez:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Most, hogy úgy konfigurálta a szolgáltatásnevet, hogy jogosult legyen a Key Vault titkainak beolvasására. A **$AZURE _CLIENT_SECRET** az egyszerű szolgáltatásnév **jelszava** . Adja hozzá a szolgáltatás egyszerű hitelesítő adatait a Secrets Store CSI-illesztőprogram által elérhető titkos Kubernetes:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Megjegyzés:** Ha később hibaüzenetet kap, amikor az Kubernetes Pod-t egy érvénytelen ügyfél-titkos AZONOSÍTÓra telepíti. Lehet, hogy egy korábbi ügyfél-titkos azonosító lejárt vagy vissza lett állítva. A probléma megoldásához törölje a titkokat "Secrets-Store-creds" címkével, és hozzon létre egy újat az aktuális ügyfél-titkos AZONOSÍTÓval. Futtassa az alábbi parancsot a "titkok-Store-creds" törléséhez:
```azurecli
kubectl delete secrets secrets-store-creds
```

Ha elfelejtette a szolgáltatásnév ügyfél-titkos AZONOSÍTÓját, a következő paranccsal állíthatja alaphelyzetbe:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Felügyelt identitások használata

Felügyelt identitások használata esetén rendeljen meghatározott szerepköröket a létrehozott AK-fürthöz. 
1. Felhasználó által hozzárendelt felügyelt identitás létrehozásához/listázásához/beolvasásához az AK-fürtnek hozzá kell rendelnie a [felügyelt identitás közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört. Győződjön meg arról, hogy a **$clientId** a Kubernetes-fürt.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Telepítse a Azure Active Directory (Azure AD) identitását az AK-ba.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Hozzon létre egy Azure AD-identitást. Másolja le a **clientId** és a **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Rendelje hozzá az olvasó szerepkört az imént a Key Vault létrehozott Azure AD-identitáshoz. Ezután adja meg a személyazonosságot a Key Vault titkainak beszerzéséhez. Az imént létrehozott Azure-identitásból fogja használni a **clientId** és a **principalId** .
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>A pod üzembe helyezése csatlakoztatott titkokkal Key Vault

Az alábbi parancs a SecretProviderClass-objektumot konfigurálja:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Egyszerű szolgáltatásnév használata

Egyszerű szolgáltatásnév használata esetén. Az alábbi parancs telepíti a Kubernetes-hüvelyeket a konfigurált SecretProviderClass és a Secrets-Store-creds szolgáltatással. Itt látható a [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) és a [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) központi telepítésének sablonja.
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Felügyelt identitások használata

Felügyelt identitások használata esetén. Létre fog hozni egy **AzureIdentity** a fürtben, amely a korábban létrehozott identitásra hivatkozik. Ezután hozzon létre egy **AzureIdentityBinding** , amely hivatkozni fog a létrehozott **AzureIdentity** . Használja az alábbi sablont, töltse ki a megfelelő paramétereket, majd mentse a **podIdentityAndBinding. YAML**néven.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Futtassa a következő parancsot a kötés végrehajtásához:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

A következő a pod tényleges üzembe helyezése. Alább látható az üzembe helyezési YAML fájl, amely az utolsó lépés Pod Identity kötését fogja használni. Mentse ezt a fájlt **podBindingDeployment. YAML**néven.

```yml
kind: Pod
apiVersion: v1
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
### <a name="check-status-and-secret-content"></a>Állapot és titkos tartalom vizsgálatának engedélyezése 
Az üzembe helyezett hüvelyek megjelenítése:
```azurecli
kubectl get pods
```

A pod állapotának megtekintéséhez használja a következő parancsot:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Kép](../media/kubernetes-key-vault-4.png)

Az üzembe helyezett Pod "Running" állapotban kell lennie. Az "Events" (események) szakasz alján a bal oldali események összes típusa "NORMAL" besorolású.
Miután ellenőrizte, hogy fut-e a pod, ellenőrizheti, hogy a pod rendelkezik-e a Key Vault titkaival.

A hüvely összes titkának megjelenítése:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Tartalom lekérése egy adott titokból:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Ellenőrizze, hogy látható-e a titkos kulcs tartalma.

## <a name="next-steps"></a>Következő lépések

Győződjön meg arról, hogy a Key Vault helyreállítható:
> [!div class="nextstepaction"]
> [A Soft delete bekapcsolása](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
