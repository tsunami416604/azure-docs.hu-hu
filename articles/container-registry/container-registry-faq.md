---
title: Az Azure Container Registry – gyakori kérdések
description: Az Azure Container Registry szolgáltatással kapcsolatos gyakori kérdésekre vonatkozó válaszokat
services: container-registry
author: sajayantony
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 5/13/2019
ms.author: sajaya
ms.openlocfilehash: 86efb6b655405500f994a5a5ec7acbd18c645004
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957850"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry kapcsolatos gyakori kérdések

Ez a cikk gyakori kérdéseket és az Azure Container Registry kapcsolatos ismert problémák foglalkozik.

## <a name="resource-management"></a>Erőforrás-kezelés

- [Létrehozhat egy Resource Manager-sablon használatával az Azure container registry?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Biztonsági rés keresése a rendszerképeket az ACR van?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hogyan konfigurálható a Kubernetes az Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hogyan szerezhetem be a rendszergazdai hitelesítő adatait a container Registry?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hogyan szerezhetem be a rendszergazdai hitelesítő adatait a Resource Manager-sablonnal?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Replikáció törlése tiltott állapot sikertelen lesz, bár a replikáció törlése az Azure CLI-vel vagy az Azure PowerShell használatával](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Létrehozhat egy Azure Container Registry használatával a Resource Manager-sablonnal?

Igen. Íme [sablon](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) , használatával hozzon létre egy beállításjegyzéket.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Biztonsági rés keresése a rendszerképeket az ACR van?

Igen. Tekintse meg a dokumentációjának [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) és [Aqua](http://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hogyan konfigurálható a Kubernetes az Azure Container Registry?

A dokumentációban [Kubernetes](http://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) és lépések [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hogyan szerezhetem be a rendszergazdai hitelesítő adatait a container Registry?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiókkal egy egyetlen felhasználó hozzáférni a beállításjegyzékhez, elsősorban a tesztelési célokra tervezték. A rendszergazdai fiók hitelesítő adatai több felhasználókkal való megosztás nem ajánlott. Egyéni identitás-felhasználók és az egyszerű szolgáltatások távfelügyelt forgatókönyvek esetén ajánlott. Lásd: [hitelesítési áttekintés](container-registry-authentication.md).

Rendszergazdai hitelesítő adatok elérése, előtt ellenőrizze, a beállításjegyzék rendszergazdai felhasználói engedélyezve van.

Beszerezni a hitelesítő adatokat az Azure CLI használatával:

```azurecli
az acr credential show -n myRegistry
```

Az Azure Powershell használatával:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hogyan szerezhetem be a rendszergazdai hitelesítő adatait a Resource Manager-sablonnal?

> [!IMPORTANT]
> A rendszergazdai felhasználói fiókkal egy egyetlen felhasználó hozzáférni a beállításjegyzékhez, elsősorban a tesztelési célokra tervezték. A rendszergazdai fiók hitelesítő adatai több felhasználókkal való megosztás nem ajánlott. Egyéni identitás-felhasználók és az egyszerű szolgáltatások távfelügyelt forgatókönyvek esetén ajánlott. Lásd: [hitelesítési áttekintés](container-registry-authentication.md).

Rendszergazdai hitelesítő adatok elérése, előtt ellenőrizze, a beállításjegyzék rendszergazdai felhasználói engedélyezve van.

Az első jelszót lekérése:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

A második jelszó lekérése:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Replikáció törlése tiltott állapot sikertelen lesz, bár a replikáció törlése az Azure CLI-vel vagy az Azure PowerShell használatával

Amikor a felhasználó rendelkezik a szükséges engedélyekkel a beállításjegyzék, de az előfizetés nem rendelkezik az olvasó szintű engedélyekkel a hiba észlelésekor. A probléma megoldásához olvasói engedélyekkel az előfizetéshez kell rendelni a felhasználóhoz:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

## <a name="registry-operations"></a>Regisztrációs adatbázis műveletei

- [Hogyan férhetek hozzá a Docker Registry HTTP API v2-es?](#how-do-i-access-docker-registry-http-api-v2)
- [Hogyan törölhetek nem minden címke egy által hivatkozott összes jegyzékek?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Miért nem a beállításjegyzék kvótahasználat csökkenti a rendszerképek törlését követően?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hogyan ellenőrzése a storage kvótamódosítások?](#how-do-i-validate-storage-quota-changes)
- [Hogyan hitelesíthetem a beállításjegyzék-tárolóban a parancssori felület futtatásakor?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [A TLS 1.2-es verzió csak konfigurációs, és a TLS 1.2-es verzió engedélyezése kínál az Azure Container Registry?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry támogatja a tartalom megbízható?](#does-azure-container-registry-support-content-trust)
- [Hogyan adhat a pull- vagy leküldéses lemezképei a beállításjegyzék erőforrás kezelése engedély nélkül?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hogyan engedélyezhetem a beállításjegyzék automatikus kép karantén](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hogyan férhetek hozzá a Docker Registry HTTP API v2-es?

ACR támogatja a Docker Registry HTTP API v2-es. Az API-k címen elérhető `https://<your registry login server>/v2/`. Például: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hogyan törölhetek nem minden címke egy által hivatkozott összes jegyzékek?

Ha a bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Megjegyzés: Hozzáadhat `-y` a megerősítő kihagyja a Törlés parancs.

További információkért lásd: [törlése az Azure Container Registry a tárolólemezképek](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Miért nem a beállításjegyzék kvótahasználat csökkenti a rendszerképek törlését követően?

Ez a helyzet akkor fordulhat elő, ha más tárolórendszerképek továbbra is a mögöttes rétegekben is hivatkozik. Ha töröl egy olyan rendszerképre nem hivatkozik, a rendszerleíró adatbázis használattal frissít, néhány perc múlva.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hogyan ellenőrzése a storage kvótamódosítások?

Hozzon létre egy rendszerképet egy 1 GB-os réteg az alábbi docker-fájl használatával. Ez biztosítja, hogy a kép egy réteget, amely nem osztozik a beállításjegyzék egyéb képet.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Hozhat létre, és a rendszerkép leküldése a beállításjegyzékbe a docker parancssori felület használatával.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Láthatja, hogy a tárhelyhasználat nőtt az Azure Portalon, vagy lekérdezheti, ha a parancssori felületről használati kell lennie.

```bash
az acr show-usage -n myregistry
```

Törli a lemezképet az Azure CLI vagy a portál használatával, és ellenőrizze a frissített használati néhány perc múlva.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hogyan hitelesíthetem a beállításjegyzék-tárolóban a parancssori felület futtatásakor?

A Docker-szoftvercsatorna csatlakoztatja az Azure CLI-tároló futtatásához szükséges:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Telepítse a tárolóban, `docker`:

```bash
apk --update add docker
```

A beállításjegyzék majd hitelesítéséhez:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>A TLS 1.2-es verzió csak konfigurációs, és a TLS 1.2-es verzió engedélyezése kínál az Azure Container Registry?

Igen. Minden legutóbbi docker-ügyfél használatával engedélyezze a TLS (18.03.0 verzió vagy újabb). 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry támogatja a tartalom megbízható?

Igen, az megbízható rendszerképek az Azure Container Registry, mivel a [Docker közjegyző](https://docs.docker.com/notary/getting_started/) integrálva van, és engedélyezhető. További információkért lásd: [tartalom megbízik az Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Hol található a fájlban található az ujjlenyomat?

A `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Nyilvános kulcsok és tanúsítványok (kivéve a delegálás szerepkörök) szerepkörök vannak tárolva a `root.json`.
* Nyilvános kulcsok és tanúsítványok a delegálás szerepkör vannak tárolva a JSON-fájlt a fölérendelt szerepkör (például `targets.json` számára a `targets/releases` szerepkör).

Ezeket a nyilvános kulcsok és tanúsítványok ellenőrzése a Docker és a közjegyző-ügyfél által végzett átfogó TUF ellenőrzése után, ajánlott.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hogyan adhat a pull- vagy leküldéses lemezképei a beállításjegyzék erőforrás kezelése engedély nélkül?

Támogatja az ACR [egyéni szerepkörök](container-registry-roles.md) , adja meg a különböző szintű engedélyekkel. Pontosabban a `AcrPull` és `AcrPush` szerepkörök engedélyezése a felhasználóknak lekéréses és/vagy leküldéses lemezképek kezelése az Azure-ban a beállításjegyzék erőforrás engedélye nélkül.

* Az Azure Portalon: A beállításjegyzék hozzáférés-vezérlés (IAM) -> Hozzáadás -> (kiválasztása `AcrPull` vagy `AcrPush` a szerepkörhöz).
* Azure CLI: Keresse meg az erőforrás-Azonosítóját a beállításjegyzékben a következő parancs futtatásával:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Ezután hozzárendelhet a `AcrPull` vagy `AcrPush` egy felhasználói szerepkört (az alábbi példában `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Vagy a szerepkör hozzárendelése alkalmazás Azonosítóval meghatározott egyszerű szolgáltatásnév:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

A megbízott majd is képes hitelesíteni, és a regisztrációs adatbázisban található rendszerképek eléréséhez.

* A beállításjegyzék hitelesítéséhez:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Az adattárak listázása:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Képet betölteni:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Csak a használnak a `AcrPull` vagy `AcrPush` szerepkör, a megbízott nem rendelkezik engedéllyel a beállításjegyzék-erőforrás Azure-ban kezelheti. Ha például `az acr list` vagy `az acr show -n myRegistry` a beállításjegyzék nem jelennek meg.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hogyan engedélyezhetem a beállításjegyzék automatikus kép karantén?

Kép karantén jelenleg előzetes verziójú funkció az ACR. A beállításjegyzék karantén módot, csak a biztonsági ellenőrzés sikeresen megfelelt képeket legyenek láthatók a normál felhasználók számára engedélyezheti. További információkért lásd: a [ACR GitHub-adattárat](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics"></a>Diagnosztika

- [docker pull hibaüzenettel meghiúsul: net/http: kérelem megszakadt a kapcsolat (fejlécek várakozás közben túllépte Client.Timeout) való várakozás közben](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [a docker leküldéses sikeresen befejeződik, de docker pull hibaüzenettel meghiúsul: nem engedélyezett: hitelesítés szükséges](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Engedélyezze, és a docker-démont, hibakeresési naplók lekérése](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Új felhasználói engedélyeket nem lehet érvényes frissítése után azonnal](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Hitelesítési adatok nem adja meg a megfelelő formátumban a közvetlen REST API-hívások](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Miért nem az Azure Portalon található összes tárházak vagy címkék?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull hibaüzenettel meghiúsul: net/http: kérelem megszakadt a kapcsolat (fejlécek várakozás közben túllépte Client.Timeout) való várakozás közben

 - Ha ez a hiba átmeneti jellegű probléma, majd próbálkozzon újra lesz sikeres. 
 - Ha `docker pull` folyamatosan, sikertelen, akkor lehetséges, hogy a docker-démon problémájára. A probléma általában enyhíthetők a docker-démon újraindítása. 
 - Docker-démon újraindítása után jelenik meg a probléma továbbra is, ha a probléma néhány hálózati kapcsolati problémák a gép lehet. Ellenőrizze, hogy kifogástalan állapotban-e a gépen általános hálózati, próbálkozzon egy parancs például `ping www.bing.com`.
 - Az összes docker-Ügyfélműveletek mindig kell egy újrapróbálkozási mechanizmust.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>a docker leküldéses sikeresen befejeződik, de docker pull hibaüzenettel meghiúsul: nem engedélyezett: hitelesítés szükséges

Ez a hiba akkor fordulhat elő, Red Hat verziójával, docker-démont, ahol `--signature-verification` alapértelmezés szerint engedélyezve van. A Red Hat Enterprise Linux (RHEL) és a Fedora docker démon lehetőségeit a következő parancs futtatásával ellenőrizheti:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Például a Fedora 28 kiszolgáló rendelkezik az alábbi docker-démon lehetőségek:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

A `--signature-verification=false` hiányzik, `docker pull` egy hasonló hibaüzenettel meghiúsul:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

A hiba megoldásához:
1. A beállítás hozzáadásához `--signature-verification=false` a docker-démon konfigurációs fájl `/etc/sysconfig/docker`. Példa:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Indítsa újra a docker-démon szolgáltatás a következő parancs futtatásával:

  ```bash
  sudo systemctl restart docker.service
  ```

A részletek `--signature-verification` futtatásával található `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Engedélyezze, és a docker-démont, hibakeresési naplók lekérése  

Indítsa el `dockerd` együtt a `debug` lehetőséget. Először hozza létre a docker-démon konfigurációs fájl (`/etc/docker/daemon.json`) Ha nem létezik, és adja hozzá a `debug` lehetőséget:

```json
{   
    "debug": true   
}
```

Ezután indítsa újra a démon. Ha például az Ubuntu 14.04:

```bash
sudo service docker restart
```

A részletek megtalálhatók a [Docker – dokumentáció](https://docs.docker.com/engine/admin/#enable-debugging). 

 * A naplók előfordulhat, hogy hozható létre, különböző helyeken, attól függően, a rendszer. Ha például az Ubuntu 14.04 rendelkezik `/var/log/upstart/docker.log`.   
Lásd: [Docker – dokumentáció](https://docs.docker.com/engine/admin/#read-the-logs) részleteiről.    

 * A Docker a Windows a naplók % LOCALAPPDATA%/docker/ jönnek létre. Azonban minden a hibakeresési információkat még nem tartalmazza.   

   Ahhoz, hogy hozzáférhessen a démon teljes naplót, szükség lehet néhány további lépést:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Most már hozzáfér a virtuális gép fut, a fájlok `dockerd`. A napló jön létre a `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Új felhasználói engedélyeket nem lehet érvényes frissítése után azonnal

Új megadása egy egyszerű szolgáltatást, a módosítás (új szerepkörök) engedélyekkel esetleg nem lépnek érvénybe azonnal. Két lehetséges oka van:

* Az Azure Active Directory szerepkör hozzárendelés késése. Általában a gyors, de propagálás késedelem miatt percet igénybe vehet.
* Engedély késleltetés token ACR-kiszolgálón. Ez akár 10 percig is eltarthat. Csökkentése érdekében, hogy is `docker logout` és hitelesítse ismét ugyanazt a felhasználót az 1 perc múlva:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR jelenleg nem támogatja a felhasználók által az otthoni replikáció törlése. A megoldás az, hogy az otthoni replikációs tartalmazza a sablon létrehozása, de a teszthálózatban hozzáadásával `"condition": false` alább látható módon:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Hitelesítési adatok nem adja meg a megfelelő formátumban a közvetlen REST API-hívások

Felmerülhet egy `InvalidAuthenticationInfo` hiba, különösen akkor használja a `curl` a beállítás az eszköz `-L`, `--location` (az átirányítások követése).
Ha például beolvasása a blob használatával `curl` a `-L` beállítás és az alapszintű hitelesítés:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

a következő választ eredményezhet:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A hiba okát, hogy néhány `curl` megvalósításokhoz fejlécekkel átirányítások követése az eredeti kérelemből.

A probléma megoldása érdekében kövesse az átirányítások manuálisan a fejlécek nélkül kell. A válaszfejlécek, a nyomtatási a `-D -` lehetőség a `curl` és bontsa ki a: a `Location` fejléc:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Miért nem az Azure Portalon található összes tárházak vagy címkék? 

Ha a Microsoft Edge böngészőt használ, legfeljebb 100 tárházak vagy felsorolt címkék láthatja. Ha a tárolójegyzék több mint 100 tárházak vagy címkék, azt javasoljuk, hogy a Firefox vagy a Chrome böngészőben közzéteheti az összes használja.

## <a name="tasks"></a>Tevékenységek

- [Hogyan kötegelhetek Mégse futtatások?](#how-do-i-batch-cancel-runs)
- [Hogyan a .git mappában szerepeljen az acr-összeállító parancs?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Hogyan kötegelhetek Mégse futtatások?

A következő parancsokat a megadott beállításjegyzék minden futó feladat megszakítása

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hogyan a .git mappában szerepeljen az acr-összeállító parancs?

Ha a felhasználó egy helyi forrás mappát a `az acr build` parancs, a `.git` mappa alapértelmezés szerint nem tartozik a feltöltött csomagban. Létrehozhat egy `.dockerignore` fájl az alábbi beállítás. Azt jelzi, hogy a parancs visszaállítása található összes fájlt `.git` a feltöltött csomagban. 

```
!.git/**
```

Ez a beállítás is vonatkozik a `az acr run` parancsot.

## <a name="cicd-integration"></a>CI/CD-integráció

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)


## <a name="next-steps"></a>További lépések

* [További](container-registry-intro.md) kapcsolatos Azure Container Registrybe.
