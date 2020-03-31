---
title: Hitelesítés szolgáltatásnévvel
description: Hozzáférést biztosít a rendszerképek a privát tároló beállításjegyzékben egy Azure Active Directory egyszerű szolgáltatás használatával.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455410"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry hitelesítés egyszerű szolgáltatásokkal

Az Azure Active Directory (Azure AD) egyszerű szolgáltatás `docker push` `pull` használatával biztosíthatja a tárolórendszerképet és a tároló beállításjegyzékhez való hozzáférést. Egyszerű szolgáltatás használatával hozzáférést biztosíthat a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz.

## <a name="what-is-a-service-principal"></a>Mi az a szolgáltatásnév?

Az Azure AD szolgáltatástagok hozzáférést *biztosítanak* az Azure-erőforrásokhoz az előfizetésen belül. Az egyszerű szolgáltatásegy szolgáltatásegy szolgáltatás identitása ként is felfogható, ahol a "szolgáltatás" minden olyan alkalmazás, szolgáltatás vagy platform, amelynek szüksége van az erőforrások eléréséhez. Az egyszerű szolgáltatás csak a megadott erőforrásokhoz tartozó hozzáférési jogokkal konfigurálható. Ezután konfigurálja az alkalmazást vagy szolgáltatást, hogy az egyszerű szolgáltatás hitelesítő adatait használja az erőforrások eléréséhez.

Az Azure Container Registry környezetben létrehozhat egy Azure AD szolgáltatásnév lekéréses, leküldéses és lekéréses, vagy más engedélyeket a privát beállításjegyzék az Azure-ban. A teljes listát az [Azure Container Registry szerepkörei és engedélyei című témakörben található.](container-registry-roles.md)

## <a name="why-use-a-service-principal"></a>Miért érdemes egyszerű szolgáltatást használni?

Egy Azure AD szolgáltatás névszerint, a hatókörrel rendelkező hozzáférést biztosíthat a privát tároló beállításjegyzék. Hozzon létre különböző szolgáltatásnéveket az egyes alkalmazásokhoz vagy szolgáltatásokhoz, amelyek mindegyike személyre szabott hozzáférési jogokkal rendelkezik a beállításjegyzékhez. És mivel elkerülheti a hitelesítő adatok megosztását a szolgáltatások és az alkalmazások között, elforgathatja a hitelesítő adatokat, vagy visszavonhatja a hozzáférést csak a választott egyszerű szolgáltatás (és így az alkalmazás) számára.

Konfigurálja például úgy a webalkalmazást, hogy egy `pull` egyszerű szolgáltatást használjon, amely csak lemezkép-hozzáférést biztosít számára, míg a buildrendszer egy egyszerű szolgáltatást használ, amely mindkettőt `push` és `pull` a hozzáférést biztosítja. Ha az alkalmazás fejlesztése gazdát cserél, elforgathatja a szolgáltatásegyszerű hitelesítő adatait anélkül, hogy ez befolyásolná a buildrendszert.

## <a name="when-to-use-a-service-principal"></a>Mikor kell egyszerű szolgáltatást használni?

Az egyszerű szolgáltatás használatát kell használnia a beállításjegyzék-hozzáférés biztosításához **fej nélküli esetekben.** Ez minden olyan alkalmazás, szolgáltatás vagy parancsfájl, amelynek automatikus vagy más módon felügyelet nélkül kell leküldéses vagy lekéréses tárolórendszerképeket kell leküldéses vagy lekérése. Példa:

  * *Pull*: Tárolók üzembe helyezése a rendszerleíró adatbázisból vezénylési rendszerek, beleértve a Kubernetes, DC/OS és a Docker Swarm. A tároló-beállításjegyzékek ből is lehívhat kapcsolódó Azure-szolgáltatások, például [az Azure Kubernetes-szolgáltatás (AKS),](../aks/cluster-container-registry-integration.md) [az Azure Container Instances,](container-registry-auth-aci.md) [az App Service,](../app-service/index.yml) [a Batch,](../batch/index.yml)a [Service Fabric](/azure/service-fabric/)és mások.

  * *Leküldéses:* Tárolórendszerképek létrehozása és leküldéses őket egy beállításjegyzék folyamatos integrációs és üzembe helyezési megoldások, például az Azure Pipelines vagy a Jenkins használatával.

A rendszerleíró adatbázishoz való egyéni hozzáférés, például amikor manuálisan lekéri a tárolórendszerképet a fejlesztési munkaállomásra, azt javasoljuk, hogy a rendszerleíró adatbázis-hozzáféréshez (például [az acr bejelentkezéssel)][az-acr-login]saját [Azure AD-identitását](container-registry-authentication.md#individual-login-with-azure-ad) használja.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Mintaszkriptek

Az Azure CLI előző mintaparancsfájljait a GitHubon, valamint az Azure PowerShell verzióit is megtalálhatja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatással

Miután rendelkezik egy egyszerű szolgáltatással, amely hozzáférést biztosított a tároló beállításjegyzékéhez, konfigurálhatja a hitelesítő adatait a `docker login` "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz való hozzáféréshez, vagy megadhatja őket a paranccsal. Használja a következő értékeket:

* **Felhasználónév** - egyszerű szolgáltatásalkalmazás-azonosító (más néven *ügyfélazonosító)*
* **Jelszó** - egyszerű szolgáltatásjelszó (más néven *ügyféltitok)*

Minden érték az űrlap `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID azonosítója. 

> [!TIP]
> Egy egyszerű szolgáltatás jelszavát az az [ad sp alapadat-hitelesítő adatok](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) parancs futtatásával újragenerálhatja.
>

### <a name="use-credentials-with-azure-services"></a>Hitelesítő adatok használata az Azure-szolgáltatásokkal

Bármely Azure-szolgáltatás egyszerű szolgáltatása, amely egy Azure-tároló beállításjegyzékkel hitelesíti a szolgáltatás egyszerű hitelesítő adatait.  A beállításjegyzék rendszergazdai hitelesítő adatai helyett a szolgáltatásnév hitelesítő adatait használja a különböző esetekben.

A hitelesítő adatok használatával például lekéri a lemezképet egy Azure container registry az [Azure Container Instances](container-registry-auth-aci.md)rendszerbe.

### <a name="use-with-docker-login"></a>Használat a docker bejelentkezéssel

Futtatható `docker login` egy egyszerű szolgáltatás használatával. A következő példában az egyszerű szolgáltatásalkalmazás-azonosító a `$SP_APP_ID`környezeti változóban, `$SP_PASSWD`a jelszó pedig a változóban lesz átadható. A Docker-hitelesítő adatok kezeléséhez ajánlott eljárásokat a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancs hivatkozási.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Miután bejelentkezett, a Docker gyorsítótárazza a hitelesítő adatokat.

### <a name="use-with-certificate"></a>Használat tanúsítvánnyal

Ha hozzáadott egy tanúsítványt a szolgáltatásnévhez, tanúsítványalapú hitelesítéssel bejelentkezhet az Azure CLI-be, majd az [az acr bejelentkezési][az-acr-login] paranccsal hozzáférhet a beállításjegyzékhez. A cli használata további biztonságot jelent a tanúsítvány titkos kulcsként használata jelszó helyett. 

Önaláírt tanúsítvány hozható létre [egyszerű szolgáltatás létrehozásakor.](/cli/azure/create-an-azure-service-principal-azure-cli) Vagy adjon hozzá egy vagy több tanúsítványt egy meglévő egyszerű szolgáltatáshoz. Ha például a cikkben szereplő parancsfájlok egyikével hoz létre vagy frissít egy olyan szolgáltatásnévet, amely jogosult a lemezképek lekérése vagy leküldése a rendszerleíró adatbázisból, adjon hozzá egy tanúsítványt az [az ad sp hitelesítő adatok visszaállítása][az-ad-sp-credential-reset] parancshasználatával.

Az Azure CLI-be való bejelentkezéshez a szolgáltatásnév használatával [jelentkezzen be az Azure CLI-be,](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)a tanúsítványnak PEM formátumúnak kell lennie, és tartalmaznia kell a személyes kulcsot. Ha a tanúsítvány nem a kívánt formátumban van, használjon egy eszközt, például `openssl` konvertálja azt. Amikor az [az bejelentkezési][az-login] futtatni a CLI-be való bejelentkezéshez a szolgáltatásnév használatával, adja meg a szolgáltatásnév alkalmazásazonosítóját és az Active Directory-bérlői azonosítót is. A következő példa ezeket az értékeket környezeti változóként mutatja be:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Ezután futtassa [az acr bejelentkezést][az-acr-login] a rendszerleíró adatbázissal való hitelesítéshez:

```azurecli
az acr login --name myregistry
```

A CLI a rendszer a `az login` rankor létrehozott jogkivonatot használja a munkamenet hitelesítéséhez a beállításjegyzékkel.

## <a name="next-steps"></a>További lépések

* Tekintse meg a [hitelesítési áttekintést](container-registry-authentication.md) az Azure-tároló beállításjegyzékkel való hitelesítéshez.

* Egy példa egy Azure-kulcstartó használatával tárolja és lekéri a kiszolgálói hitelesítő adatokat egy tároló beállításjegyzék, tekintse meg az [oktatóanyag-hoz létrehoz egy tárolórendszerkép az ACR-feladatok használatával.](container-registry-tutorial-quick-task.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
