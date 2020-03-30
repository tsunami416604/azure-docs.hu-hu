---
title: Gyakori kérdések
description: Válaszok az Azure Container Registry szolgáltatással kapcsolatos gyakori kérdésekre
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: c0d51c9c31e4e6859eaedce371efeafaa5fd4f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403210"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Gyakori kérdések az Azure Container Registry beállításjegyzékkel kapcsolatban

Ez a cikk az Azure Container Registry gyakran feltett kérdéseket és ismert problémákat ismerteti.

## <a name="resource-management"></a>Erőforrás-kezelés

- [Létrehozhatok egy Azure-tároló beállításjegyzéket egy Resource Manager-sablon használatával?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Van biztonsági rés az ACR-ben lévő képek után késedelmök után?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hogyan konfigurálhatom a Kubernetes szolgáltatást az Azure Container Registry szolgáltatással?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hogyan szerezhetem be a tárolók beállításjegyzékének rendszergazdai hitelesítő adatait?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hogyan szerezhetek be rendszergazdai hitelesítő adatokat egy Erőforrás-kezelő sablonban?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A replikáció törlése tiltott állapottal sikertelen, bár a replikáció törlődik az Azure CLI vagy az Azure PowerShell használatával](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [A tűzfalszabályok frissítése sikeres, de nem lépnek érvénybe](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Létrehozhatok egy Azure Container Registry egy Resource Manager sablon használatával?

Igen. Itt van [egy sablon,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) amely segítségével hozzon létre egy rendszerleíró adatbázis.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Van biztonsági rés az ACR-ben lévő képek után késedelmök után?

Igen. Tekintse meg az [Azure Security Center,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)a [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) és az [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)dokumentációját.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hogyan konfigurálhatom a Kubernetes szolgáltatást az Azure Container Registry szolgáltatással?

Tekintse meg a [Kubernetes dokumentációját](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) és az [Azure Kubernetes-szolgáltatás lépéseit.](../aks/cluster-container-registry-integration.md)

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hogyan szerezhetem be a tárolók beállításjegyzékének rendszergazdai hitelesítő adatait?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiók célja, hogy egyetlen felhasználó hozzáférjen a rendszerleíró adatbázishoz, elsősorban tesztelési célokra. Nem javasoljuk, hogy a rendszergazdai fiók hitelesítő adatait több felhasználóval is megossza. Egyéni identitás ajánlott a felhasználók és a szolgáltatásnév nélküli forgatókönyvek. Lásd: [Hitelesítés – áttekintés.](container-registry-authentication.md)

A rendszergazdai hitelesítő adatok beszerzése előtt győződjön meg arról, hogy a beállításjegyzék rendszergazdai felhasználója engedélyezve van.

Hitelesítő adatok beszerezni az Azure CLI használatával:

```azurecli
az acr credential show -n myRegistry
```

Az Azure Powershell használata:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hogyan szerezhetek be rendszergazdai hitelesítő adatokat egy Erőforrás-kezelő sablonban?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiók célja, hogy egyetlen felhasználó hozzáférjen a rendszerleíró adatbázishoz, elsősorban tesztelési célokra. Nem javasoljuk, hogy a rendszergazdai fiók hitelesítő adatait több felhasználóval is megossza. Egyéni identitás ajánlott a felhasználók és a szolgáltatásnév nélküli forgatókönyvek. Lásd: [Hitelesítés – áttekintés.](container-registry-authentication.md)

A rendszergazdai hitelesítő adatok beszerzése előtt győződjön meg arról, hogy a beállításjegyzék rendszergazdai felhasználója engedélyezve van.

Az első jelszó beszerezése:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

A második jelszó beszerezése:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A replikáció törlése tiltott állapottal sikertelen, bár a replikáció törlődik az Azure CLI vagy az Azure PowerShell használatával

A hiba akkor jelenik meg, ha a felhasználó rendelkezik engedélyekkel a rendszerleíró adatbázishoz, de nem rendelkezik olvasószintű engedélyekkel az előfizetéshez. A probléma megoldásához rendelje hozzá a Reader engedélyeit az előfizetéshez a felhasználóhoz:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>A tűzfalszabályok frissítése sikeres, de nem lépnek érvénybe

A tűzfalszabályok módosításának propagálása némi időt vesz igénybe. A tűzfal beállításainak módosítása után várjon néhány percet, mielőtt ellenőrizné ezt a módosítást.


## <a name="registry-operations"></a>Regisztrációs adatbázis műveletei

- [Hogyan érhetem el a Docker Registry HTTP API V2-t?](#how-do-i-access-docker-registry-http-api-v2)
- [Hogyan törölhetem az összes olyan jegyzékfájlt, amelyre nem hivatkozik a tárházban lévő címke?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Miért nem csökken a beállításjegyzék-kvóta használat a lemezképek törlése után?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hogyan érvényesíthetem a tárhelykvóta változásait?](#how-do-i-validate-storage-quota-changes)
- [Hogyan hitelesíthetem magukat a beállításjegyzékben, amikor a CLI-t egy tárolóban futtatom?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Hogyan lehet engedélyezni a TLS 1.2-t?](#how-to-enable-tls-12)
- [Támogatja az Azure Container Registry a content trust szolgáltatást?](#does-azure-container-registry-support-content-trust)
- [Hogyan adhatok hozzáférést a lekéréses vagy leküldéses lemezképekhez a beállításjegyzék-erőforrás kezeléséhez szükséges engedély nélkül?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hogyan engedélyezhetem az automatikus képkarantént a rendszerleíró adatbázisban?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hogyan érhetem el a Docker Registry HTTP API V2-t?

Az ACR támogatja a Docker Registry HTTP API V2-t. Az API-k a. `https://<your registry login server>/v2/` Például: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hogyan törölhetem az összes olyan jegyzékfájlt, amelyre nem hivatkozik a tárházban lévő címke?

Ha a bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Powershell esetén:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Megjegyzés: A `-y` megerősítést a törlés iparancsban is hozzáadhatja.

További információ: [Tárolórendszerképek törlése az Azure Container Registry](container-registry-delete.md)alkalmazásban.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Miért nem csökken a beállításjegyzék-kvóta használat a lemezképek törlése után?

Ez a helyzet akkor fordulhat elő, ha az alapul szolgáló rétegekre más tárolórendszerképek hivatkoznak. Ha referenciák nélkül töröl egy lemezképet, a beállításjegyzék használata néhány percen belül frissül.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hogyan érvényesíthetem a tárhelykvóta változásait?

Hozzon létre egy 1 GB-os réteget, a következő docker-fájl használatával. Ez biztosítja, hogy a lemezkép olyan réteggel rendelkezzen, amelyet a beállításjegyzékben más lemezkép nem oszt meg.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

A docker CLI használatával hozhat létre és lökheti le a lemezképet a rendszerleíró adatbázisba.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Látnia kell, hogy a tárolási használat nőtt az Azure Portalon, vagy lekérdezheti a használatot a CLI használatával.

```azurecli
az acr show-usage -n myregistry
```

Törölje a lemezképet az Azure CLI vagy a portál használatával, és néhány perc alatt ellenőrizze a frissített használatot.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hogyan hitelesíthetem magukat a beállításjegyzékben, amikor a CLI-t egy tárolóban futtatom?

Az Azure CLI-tárolót a Docker-szoftvercsatorna csatlakoztatásával kell futtatnia:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

A tartályba `docker`telepítse a következőket:

```bash
apk --update add docker
```

Ezután hitelesítse magát a beállításjegyzékkel:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Hogyan lehet engedélyezni a TLS 1.2-t?

Engedélyezze a TLS 1.2-t bármely újabb docker-ügyfél használatával (18.03.0-s vagy újabb verzió). 

> [!IMPORTANT]
> 2020. január 13-tól kezdődően az Azure Container Registry a TLS 1.2 használatához a kiszolgálók és alkalmazások összes biztonságos kapcsolatára lesz szüksége. A TLS 1.0 és 1.1 támogatása megszűnik.

### <a name="does-azure-container-registry-support-content-trust"></a>Támogatja az Azure Container Registry a content trust szolgáltatást?

Igen, használhatja a megbízható rendszerképek et az Azure Container Registry, mivel a [Docker közjegyző](https://docs.docker.com/notary/getting_started/) integrálva van, és engedélyezhető. További információt a [Content Trust in Azure Container Registry (Tartalommegbízhatóság az Azure Container Registry alkalmazásban) ( Tartalommegbízhatóság az Azure Container Registry alkalmazásban ) témakörben talál.](container-registry-content-trust.md)


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Hol található az ujjlenyomat fájlja?

Alatt: `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`

* Az összes szerepkör nyilvános kulcsait és tanúsítványait `root.json`(a delegálási szerepkörök kivételével) a tárolóban tárolják.
* A nyilvános kulcsok és a delegálási szerepkör tanúsítványai a szülőszerepkör JSON-fájljában tárolódnak (például `targets.json` a `targets/releases` szerepkör esetében).

Javasoljuk, hogy ellenőrizze ezeket a nyilvános kulcsokat és tanúsítványokat a Docker és a közjegyző i docker- és közjegyzői ügyfél által végzett teljes TUF-ellenőrzés után.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hogyan adhatok hozzáférést a lekéréses vagy leküldéses lemezképekhez a beállításjegyzék-erőforrás kezeléséhez szükséges engedély nélkül?

Az ACR támogatja azokat az [egyéni szerepköröket,](container-registry-roles.md) amelyek különböző szintű engedélyeket biztosítanak. Pontosabban, `AcrPull` `AcrPush` és a szerepkörök lehetővé teszik a felhasználók számára, hogy lekéréses és/vagy leküldéses lemezképek engedélye nélkül a beállításjegyzék-erőforrás az Azure-ban.

* Azure portal: A beállításjegyzék -> Access Control (IAM) -> hozzáadás (kijelölése `AcrPull` vagy `AcrPush` a szerepkör).
* Azure CLI: Keresse meg a rendszerleíró adatbázis erőforrás-azonosítóját a következő parancs futtatásával:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Ezután hozzárendelheti a `AcrPull` vagy `AcrPush` szerepkört egy `AcrPull`felhasználóhoz (a következő példa használja):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Vagy rendelje hozzá a szerepkört egy, az alkalmazásazonosítója által azonosított szolgáltatáselvhez:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

A megbízott ezután hitelesítheti és hozzáférhet a lemezképekhez a beállításjegyzékben.

* Hitelesítés rendszerleíró adatbázisba:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Adattárak listázása:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Kép lekérése:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Csak a vagy `AcrPull` `AcrPush` a szerepkör használatával a hozzárendelt nem rendelkezik az Azure-beli beállításjegyzék-erőforrás kezeléséhez szükséges engedéllyel. Például, `az acr list` `az acr show -n myRegistry` vagy nem jeleníti meg a rendszerleíró adatbázist.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hogyan engedélyezhetem a rendszerleíró adatbázis automatikus képkarantén-beállításjegyzékét?

A képkarantén jelenleg az ACR előzetes funkciója. Engedélyezheti a rendszerleíró adatbázis karantén módját, így csak azok a lemezképek lesznek láthatók a normál felhasználók számára, amelyek sikeresen átmentek a biztonsági vizsgálaton. További információt az [ACR GitHub-tártárban talál.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)

## <a name="diagnostics-and-health-checks"></a>Diagnosztika és egészségügyi ellenőrzések

- [Ellenőrizze az állapotot`az acr check-health`](#check-health-with-az-acr-check-health)
- [a docker-lekérése a következő hibával sikertelen: net/http: a kérés megszakadt a kapcsolatra való várakozás közben (client.Timeout túllépve, amíg fejlécre vár)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [A docker-leküldések sikeresek, de a docker-lekérése hiba esetén sikertelen: jogosulatlan: hitelesítés szükséges](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`sikeres, de a docker-parancsok hiba esetén sikertelenek: jogosulatlan: hitelesítés szükséges](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [A docker-démon hibakeresési naplóinak engedélyezése és lehívása](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Előfordulhat, hogy az új felhasználói engedélyek nem lépnek azonnal a frissítés után](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [A hitelesítési adatok nem a megfelelő formátumban vannak megadva a közvetlen REST API-hívásokon](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Miért nem sorolja fel az Azure Portal az összes adattárat vagy címkét?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Miért nem az Azure Portalon nem lehet lekérni az adattárak vagy címkék?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Miért sikertelen a lekéréses vagy leküldéses kérelem nem engedélyezett művelet?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Hogyan gyűjthetek http-nyomokat a Windows rendszeren?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Ellenőrizze az állapotot`az acr check-health`

A gyakori környezeti és beállításjegyzék-problémák elhárításához olvassa el [az Azure-tárolók beállításjegyzékének ellenőrzése című témakört.](container-registry-check-health.md)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>a docker-lekérése a következő hibával sikertelen: net/http: a kérés megszakadt a kapcsolatra való várakozás közben (client.Timeout túllépve, amíg fejlécre vár)

 - Ha ez a hiba átmeneti probléma, akkor az újrapróbálkozás sikeres lesz.
 - Ha `docker pull` folyamatosan sikertelen, akkor probléma lehet a Docker démonnal. A probléma általában a Docker démon újraindításával mérsékelhető. 
 - Ha a Docker démon újraindítása után továbbra is megjelenik ez a probléma, akkor a probléma a számítógéphálózati kapcsolattal kapcsolatos problémák lehetnek. Annak ellenőrzéséhez, hogy a számítógép általános hálózata kifogástalan-e, futtassa a következő parancsot a végponti kapcsolat teszteléséhez. A `az acr` kapcsolódási ellenőrző parancsot tartalmazó minimális verzió: 2.2.9. Frissítse az Azure CLI-t, ha régebbi verziót használ.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Mindig rendelkeznie kell egy újrapróbálkozási mechanizmussal az összes Docker-ügyfélműveletnél.

### <a name="docker-pull-is-slow"></a>Docker pull lassú
[Ezzel az](http://www.azurespeed.com/Azure/Download) eszközzel tesztelheti a géphálózat letöltési sebességét. Ha a géphálózat lassú, fontolja meg az Azure virtuális gép használatát a beállításjegyzékkel azonos régióban. Ez általában gyorsabb hálózati sebességet biztosít.

### <a name="docker-push-is-slow"></a>A Docker push lassú
[Ezzel az](http://www.azurespeed.com/Azure/Upload) eszközzel tesztelheti a géphálózat feltöltési sebességét. Ha a géphálózat lassú, fontolja meg az Azure virtuális gép használatát a beállításjegyzékkel azonos régióban. Ez általában gyorsabb hálózati sebességet biztosít.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>A Docker-leküldések sikeresek, de a docker-lekérése hiba esetén sikertelen: jogosulatlan: hitelesítés szükséges

Ez a hiba a Docker démon Red Hat verziójával `--signature-verification` fordulhat elő, ahol alapértelmezés szerint engedélyezve van. A Docker démon beállításait a Red Hat Enterprise Linux (RHEL) vagy a Fedora esetében a következő parancs futtatásával ellenőrizheti:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Például a Fedora 28 Server a következő docker démonbeállításokkal rendelkezik:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Hiányzó `--signature-verification=false` esetén `docker pull` a következőhöz hasonló hiba lép fel:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

A hiba elhárítása:
1. Adja hozzá `--signature-verification=false` a lehetőséget a Docker `/etc/sysconfig/docker`démon konfigurációs fájljához. Példa:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Indítsa újra a Docker démonszolgáltatást a következő parancs futtatásával:
   
   ```bash
   sudo systemctl restart docker.service
   ```

A `--signature-verification` részleteket a `man dockerd`futással találhatja meg.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>Az acr bejelentkezés sikeres, de a docker hiba esetén sikertelen: jogosulatlan: hitelesítés szükséges

Győződjön meg arról, hogy az összes `docker push myregistry.azurecr.io/myimage:latest`kiskiszolgáló URL-címét használja, például akkor `myRegistry`is, ha a rendszerleíró erőforrás neve nagy- vagy vegyes, például .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>A Docker-démon hibakeresési naplóinak engedélyezése és lehívása  

Kezdjük `dockerd` `debug` a lehetőséggel. Először hozza létre a Docker démon`/etc/docker/daemon.json`konfigurációs fájl ( ) ha `debug` nem létezik, és adja hozzá a lehetőséget:

```json
{   
    "debug": true   
}
```

Ezután indítsa újra a démont. Például az Ubuntu 14.04 esetében:

```bash
sudo service docker restart
```

A részletek a [Docker dokumentációjában](https://docs.docker.com/engine/admin/#enable-debugging)találhatók. 

 * A naplók a rendszertől függően különböző helyeken is létrejöhetnek. Például az Ubuntu 14.04 esetében `/var/log/upstart/docker.log`ez .   
A részleteket a [Docker dokumentációjában](https://docs.docker.com/engine/admin/#read-the-logs) találja.    

 * A Windows-hoz való Docker esetében a naplók a %LOCALAPPDATA%/docker/ alatt jönnek létre. Előfordulhat azonban, hogy még nem tartalmazza az összes hibakeresési információt.   

   A teljes démonnapló eléréséhez további lépésekre lehet szükség:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Most már hozzáférhet a virtuális gép futó `dockerd`összes fájljához. A napló `/var/log/docker.log`a.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Előfordulhat, hogy az új felhasználói engedélyek nem lépnek azonnal a frissítés után

Ha új engedélyeket (új szerepköröket) ad egy egyszerű szolgáltatásnak, előfordulhat, hogy a módosítás nem lép azonnal érvénybe. Ennek két oka lehet:

* Az Azure Active Directory szerepkör-hozzárendelési késleltetése. Normális esetben gyors, de a terjedés idotartama miatt percekig is eltarthat.
* Engedélykésleltetés az ACR tokenkiszolgálón. Ez akár 10 percet is igénybe vehet. A mérséklés érdekében 1 perc elteltével újra hitelesítheti `docker logout` magát ugyanazzal a felhasználóval:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Az ACR jelenleg nem támogatja a felhasználók otthoni replikációs törlését. A megoldás az, hogy az otthoni replikáció létrehozása szerepeljen `"condition": false` a sablonban, de hagyja ki annak létrehozását az alábbi módon látható módon:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>A hitelesítési adatok nem a megfelelő formátumban vannak megadva a közvetlen REST API-hívásokon

Előfordulhat, hogy `InvalidAuthenticationInfo` hibát észlel, `curl` különösen az `-L` `--location` eszköz használatával a lehetőséggel (az átirányítások követéséhez).
Például a blob beolvasása `curl` beállítással és egyszerű hitelesítéssel: `-L`

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

a következő választ eredményezheti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A kiváltó ok `curl` az, hogy egyes implementációk követik átirányítások fejlécek az eredeti kérelemből.

A probléma megoldásához manuálisan kell követnie az átirányításokat a fejlécek nélkül. Nyomtassa ki `-D -` a `curl` válaszfejléceket a `Location` következő lehetőséggel, majd bontsa ki: a fejléc:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Miért nem sorolja fel az Azure Portal az összes adattárat vagy címkét? 

Ha a Microsoft Edge/IE böngészőt használja, legbőleg10 tárházban vagy címkén láthatja. Ha a rendszerleíró adatbázistöbb mint 100 adattárat vagy címkét használ, javasoljuk, hogy használja a Firefox vagy a Chrome böngészőt az összes listázásához.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Miért nem az Azure Portalon nem lehet lekérni az adattárak vagy címkék?

Előfordulhat, hogy a böngésző nem tudja elküldeni a kiszolgálónak az adattárak vagy címkék letöltésére vonatkozó kérelmet. Lehetnek különböző okok, mint például:

* A hálózati kapcsolat hiánya
* Tűzfal
* Hirdetésblokkolók
* DNS-hibák

Forduljon a hálózati rendszergazdához, vagy ellenőrizze a hálózati konfigurációt és a kapcsolatot. Próbálja `az acr check-health -n yourRegistry` meg futtatni az Azure CLI használatával, hogy ellenőrizze, hogy a környezet képes-e csatlakozni a tároló beállításjegyzékhez. Ezenkívül inkognitó- vagy privát munkamenetet is kipróbálhat a böngészőjében, hogy elkerülje az elavult böngészőgyorsítótárat vagy cookie-kat.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Miért sikertelen a lekéréses vagy leküldéses kérelem nem engedélyezett művelet?

Íme néhány forgatókönyv, ahol a műveletek esetleg nem engedélyezettek:
* A klasszikus nyilvántartások már nem támogatottak. Frissítsen egy támogatott [sus-ra](https://aka.ms/acr/skus) [az acr frissítés](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) vagy az Azure Portal használatával.
* A kép vagy tárház zárolható, így nem törölhető vagy frissíthető. Az aktuális attribútumok megtekintéséhez használhatja az [az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) parancsot.
* Egyes műveletek nem engedélyezettek, ha a rendszerkép karanténban van. További információ a [karanténról.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hogyan gyűjthetek http-nyomokat a Windows rendszeren?

#### <a name="prerequisites"></a>Előfeltételek

- Https visszafejtése fiddlerben:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Engedélyezze a Docker számára, hogy proxyt használjon a Docker ui-n keresztül:<https://docs.docker.com/docker-for-windows/#proxies>
- Ügyeljen arra, hogy visszatérjen, ha kész.  A Docker nem fog működni ezzel az engedélyezve, és a hegedűs nem fut.

#### <a name="windows-containers"></a>Windows-tárolók

A Docker-proxy konfigurálása 127.0.0.1:8888-ra

#### <a name="linux-containers"></a>Linux-tárolók

Keresse meg a Docker vm virtuális kapcsoló ip-címét:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurálja a Docker-proxyt az előző parancs és a 8888-as port kimenetére (például 10.0.75.1:8888)

## <a name="tasks"></a>Feladatok

- [Hogyan kötegelhetem a megszakítási futtatásokat?](#how-do-i-batch-cancel-runs)
- [Hogyan vehetem fel a .git mappát az acr build parancsba?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [A feladatok támogatják a GitLab forráseseményindítókat?](#does-tasks-support-gitlab-for-source-triggers)
- [Milyen git tárház-kezelési szolgáltatást támogat a feladatok?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hogyan kötegelhetem a megszakítási futtatásokat?

A következő parancsok megszakítják a megadott beállításjegyzék összes futó feladatát.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hogyan vehetem fel a .git mappát az acr build parancsba?

Ha helyi forrásmappát ad át a `az acr build` parancsnak, a `.git` mappa alapértelmezés szerint ki lesz zárva a feltöltött csomagból. A `.dockerignore` következő beállítással hozhat létre fájlt. Azt mondja a parancsot, `.git` hogy állítsa vissza az összes fájlt a feltöltött csomagban. 

`!.git/**`

Ez a beállítás `az acr run` a parancsra is vonatkozik.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>A feladatok támogatják a GitLab forráseseményindítókat?

Jelenleg nem támogatjuk a GitLab a forrás eseményindítók.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Milyen git tárház-kezelési szolgáltatást támogat a feladatok?

| Git szolgáltatás | Forrás környezete | Kézi felépítés | Automatikus létrehozás véglegesítési eseményindítón keresztül |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Igen | Igen |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Igen | Igen |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Igen | Nem |
| BitBucket között | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Igen | Nem |

## <a name="run-error-message-troubleshooting"></a>Hibaüzenet hibaelhárításának futtatása

| Hibaüzenet | Hibaelhárítási útmutató |
|---|---|
|Nem volt konfigurálva hozzáférés a virtuális géphez, ezért nem található előfizetés|Ez akkor fordulhat elő, ha az ACR-feladatban használja. `az login --identity` Ez egy átmeneti hiba, és akkor fordul elő, ha a felügyelt identitás szerepkör-hozzárendelése nem propagált. Várakozás néhány másodpercig, mielőtt újra megpróbálja működik.|

## <a name="cicd-integration"></a>CI/CD integráció

- [Körci](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub-műveletek](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>További lépések

* [További információ](container-registry-intro.md) az Azure Container Registry szolgáltatásról.
