---
title: Hitelesítés a Kubernetes-fürtről
description: Megtudhatja, hogyan biztosíthat egy Kubernetes-fürt az Azure-tároló beállításjegyzékében lévő rendszerképekhez való hozzáférést egy egyszerű szolgáltatás használatával létrehozott lekéréses titok létrehozásával
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154893"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Lemezképek lekérése egy Azure-tároló beállításjegyzékéből egy Kubernetes-fürtbe

Az Azure-tároló beállításjegyzékét bármely Kubernetes-fürthöz, beleértve a "helyi" Kubernetes-fürtöket, például [a minikube-t](https://minikube.sigs.k8s.io/) és a [kind-ot](https://kind.sigs.k8s.io/)tárolórendszerképek forrásaként használhatja. Ez a cikk bemutatja, hogyan hozhat létre egy Kubernetes lekéréses titkos azure-beli egyszerű azure-szolgáltatásnév alapján. Ezután a titkos kulcsot használja a rendszerképek lekérése egy Azure-tároló beállításjegyzékegy Kubernetes-telepítés.

> [!TIP]
> Ha a felügyelt [Azure Kubernetes-szolgáltatást](../aks/intro-kubernetes.md)használja, [integrálhatja a fürtöt](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) egy cél Azure-tároló beállításjegyzékkel a rendszerképlekérésekhez. 

Ez a cikk feltételezi, hogy már létrehozott egy privát Azure-tároló beállításjegyzéket. Emellett egy Kubernetes-fürtnek is futnia `kubectl` kell, és a parancssori eszközön keresztül elérhetőnek kell lennie.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Ha nem menti vagy emlékszik az egyszerű szolgáltatásjelszóra, alaphelyzetbe állíthatja azt az [ad sp hitelesítő adatok visszaállítása][az-ad-sp-credential-reset] paranccsal:

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Ez a parancs egy új, érvényes jelszót ad vissza a szolgáltatásnévhez.

## <a name="create-an-image-pull-secret"></a>Képlekéréses titok létrehozása

A Kubernetes egy *képlekéréses titkos képet* használ a beállításjegyzékhitelesítéshez szükséges adatok tárolására. Az Azure-tároló beállításjegyzék lekéréses titkos kulcsának létrehozásához adja meg az egyszerű szolgáltatásazonosítót, a jelszót és a beállításjegyzék URL-címét. 

Hozzon létre egy képlekéréses titkos kulcsot a következő `kubectl` paranccsal:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
ahol:

| Érték | Leírás |
| :--- | :--- |
| `secret-name` | A kép neve lekéréses titok, például *acr-secret* |
| `namespace` | Kubernetes névtér, hogy a titkos <br/> Csak akkor szükséges, ha a titkos kulcsot az alapértelmezett névtéren kívül más névtérbe szeretné helyezni. |
| `container-registry-name` | Az Azure-tároló beállításjegyzékének neve |
| `service-principal-ID` | A Kubernetes által a rendszerleíró adatbázis eléréséhez használt szolgáltatásnév azonosítója |
| `service-principal-password` | Egyszerű szolgáltatás jelszó |

## <a name="use-the-image-pull-secret"></a>A kép lekéréses titkos

Miután létrehozta a rendszerkép lekéréses titkos, kubernetes podok és központi telepítések létrehozásához használhatja. Adja meg a titkos `imagePullSecrets` fájl alatt található titkos név nevét. Példa:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

Az előző példában `your-awesome-app:v1` a rendszerkép neve lekérése az Azure-tároló beállításjegyzékből, és `acr-secret` a neve a lekéréses titkos kulcsot a beállításjegyzék eléréséhez létrehozott. A pod telepítésekor a Kubernetes automatikusan lekéri a lemezképet a beállításjegyzékből, ha az még nem található meg a fürtön.


## <a name="next-steps"></a>További lépések

* A szolgáltatásnévi tagok és az Azure Container Registry használatával kapcsolatos további információkért tekintse meg az [Azure Container Registry hitelesítésszolgáltatás-felelősökkel című témakört.](container-registry-auth-service-principal.md)
* További információ a képlekéréses titkokról a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset