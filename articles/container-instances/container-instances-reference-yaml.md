---
title: YAML-hivatkozás a tároló csoportjához
description: A Azure Container Instances által támogatott YAML-fájlra mutató hivatkozás egy tároló csoport konfigurálásához
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084760"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-hivatkozás: Azure Container Instances

Ez a cikk az Azure Container Instances által támogatott YAML-fájl szintaxisát és tulajdonságait ismerteti egy [tároló csoport](container-instances-container-groups.md)konfigurálásához. YAML-fájl használatával adja meg a csoport konfigurációját az Azure CLI-ben az az [Container Create][az-container-create] paranccsal. 

A YAML-fájlok kényelmes módot biztosítanak egy tároló csoport konfigurálására a reprodukálható központi telepítésekhez. Ez egy rövid alternatíva egy [Resource Manager-sablon](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) vagy a Azure Container instances SDK-k használatával egy tároló csoport létrehozásához vagy frissítéséhez.

> [!NOTE]
> Ez a hivatkozás a Azure Container Instances REST API verziójának YAML-fájljaira vonatkozik `2019-12-01` .

## <a name="schema"></a>Séma 

A YAML-fájl sémája a következőt követi, beleértve a legfontosabb tulajdonságok kiemeléséhez szükséges megjegyzéseket is. A séma tulajdonságainak leírását a [tulajdonságértékek](#property-values) című szakaszban találja.

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Tulajdonságértékek

A következő táblázatok ismertetik a sémában beállítani kívánt értékeket.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. ContainerInstance/containerGroups objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Igen | A tároló csoport neve. |
|  apiVersion | Enum | Igen | 2018-10-01 |
|  location | sztring | No | Az erőforrás helye. |
|  tags | object | Nem | Az erőforrás-címkék. |
|  identity | object | Nem | A tároló csoport identitása, ha be van állítva. - [ContainerGroupIdentity objektum](#containergroupidentity-object) |
|  properties | object | Igen | [ContainerGroupProperties objektum](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  típus | Enum | Nem | A tároló csoporthoz használt identitás típusa A "SystemAssigned, UserAssigned" típus egy implicit módon létrehozott identitást és egy felhasználó által hozzárendelt identitást is tartalmaz. A "None" típus eltávolítja az összes identitást a tároló csoportjából. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, none |
|  userAssignedIdentities | object | Nem | A tároló csoporthoz társított felhasználói identitások listája. A felhasználói azonosító szótár kulcsára vonatkozó hivatkozások a következő formában lesznek Azure Resource Manager erőforrás-azonosítók: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  tárolók | array | Igen | A tároló csoportban lévő tárolók. - [Tároló objektum](#container-object) |
|  imageRegistryCredentials | array | Nem | A rendszerkép beállításjegyzékbeli hitelesítő adatai, amelyek alapján a rendszer létrehozza a tároló csoportját. - [ImageRegistryCredential objektum](#imageregistrycredential-object) |
|  restartPolicy | Enum | Nem | A tároló csoportban lévő összes tárolóra vonatkozó újraindítási szabályzat. - `Always` Mindig újraindítás – `OnFailure` Újraindítás hiba esetén – `Never` Soha ne indítsa újra a rendszert. -Mindig, OnFailure, soha |
|  IP-cím | object | Nem | A tároló csoport IP-címének típusa. - [IP-cím objektum](#ipaddress-object) |
|  osType | Enum | Igen | A tároló csoportban lévő tárolók által igényelt operációs rendszer típusa. – Windows vagy Linux |
|  volumes | array | Nem | Azoknak a köteteknek a listája, amelyeket tárolók csatlakoztatnak a tároló csoportba. - [Kötet objektum](#volume-object) |
|  diagnosztika | object | Nem | Egy tároló csoport diagnosztikai adatai. - [ContainerGroupDiagnostics objektum](#containergroupdiagnostics-object) |
|  networkProfile | object | Nem | Egy tároló csoport hálózati profiljának adatai. - [ContainerGroupNetworkProfile objektum](#containergroupnetworkprofile-object) |
|  dnsConfig | object | Nem | Egy tároló csoport DNS-konfigurációs adatai. - [DnsConfiguration objektum](#dnsconfiguration-object) |
| SKU | Enum | Nem | A Container Group standard vagy dedikált SKU |
| encryptionProperties | object | Nem | Egy tároló csoport titkosítási tulajdonságai. - [EncryptionProperties objektum](#encryptionproperties-object) | 
| initContainers | array | Nem | A tárolók csoportjához tartozó init-tárolók. - [InitContainerDefinition objektum](#initcontainerdefinition-object) |




### <a name="container-object"></a>Tároló objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Igen | A tároló példány felhasználó által megadott neve. |
|  properties | object | Igen | A tároló példányának tulajdonságai. - [ContainerProperties objektum](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>ImageRegistryCredential objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  kiszolgáló | sztring | Igen | A Docker-rendszerkép beállításjegyzék-kiszolgálója olyan protokoll nélkül, mint a "http" és a "https". |
|  username | sztring | Igen | A privát beállításjegyzék felhasználóneve. |
|  jelszó | sztring | No | A privát beállításjegyzék jelszava. |




### <a name="ipaddress-object"></a>IP-cím objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  ports | array | Igen | A tároló csoporton elérhető portok listája. - [Port objektum](#port-object) |
|  típus | Enum | Igen | Meghatározza, hogy az IP-cím elérhető-e a nyilvános internethez vagy a privát VNET. -Nyilvános vagy privát |
|  IP | sztring | No | A nyilvános internethez elérhető IP-cím. |
|  dnsNameLabel | sztring | No | Az IP-címhez tartozó DNS-név címkéje. |




### <a name="volume-object"></a>Kötet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Igen | A kötet neve. |
|  azureFile | object | Nem | Az Azure-fájl kötete. - [AzureFileVolume objektum](#azurefilevolume-object) |
|  emptyDir | object | Nem | Az üres könyvtár kötete. |
|  titkos kód | object | Nem | A titkos kötet. |
|  gitRepo | object | Nem | A git-tárház kötete. - [GitRepoVolume objektum](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nem | A Container Group log Analytics információi. - [LogAnalytics objektum](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  id | sztring | Igen | Egy hálózati profil azonosítója. |




### <a name="dnsconfiguration-object"></a>DnsConfiguration objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Névkiszolgálók | array | Igen | A tároló csoport DNS-kiszolgálói. -string |
|  searchDomains | sztring | No | A DNS-keresési tartományok az állomásnév kereséséhez a tároló csoportban. |
|  beállítások | sztring | No | A tároló csoport DNS-beállításai. |


### <a name="encryptionproperties-object"></a>EncryptionProperties objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | sztring    | Igen   | A kulcstartó alap URL-címe. |
| Kulcsnév   | sztring    | Igen   | A titkosítási kulcs neve. |
| Verziószám    | sztring    | Igen   | A titkosítási kulcs verziója. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| name  | sztring |  Igen | Az init-tároló neve. |
| properties    | object    | Igen   | Az init-tároló tulajdonságai. - [InitContainerPropertiesDefinition objektum](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  image | sztring | Igen | A Container-példány létrehozásához használt rendszerkép neve. |
|  command | array | Nem | A tároló-példányon belül végrehajtandó parancsok az exec formában. -string |
|  ports | array | Nem | A tároló példányon elérhető portok. - [ContainerPort objektum](#containerport-object) |
|  environmentVariables | array | Nem | A tároló-példányban beállított környezeti változók. - [EnvironmentVariable objektum](#environmentvariable-object) |
|  resources | object | Igen | A Container-példány erőforrás-követelményei. - [ResourceRequirements objektum](#resourcerequirements-object) |
|  volumeMounts | array | Nem | A tároló példány számára elérhető kötetek csatlakoztatása. - [VolumeMount objektum](#volumemount-object) |
|  livenessProbe | object | Nem | Az élettartam-mintavétel. - [ContainerProbe objektum](#containerprobe-object) |
|  readinessProbe | object | Nem | A készültségi mintavétel. - [ContainerProbe objektum](#containerprobe-object) |




### <a name="port-object"></a>Port objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  protokoll | Enum | Nem | A porthoz társított protokoll. -TCP vagy UDP |
|  port | egész szám | Igen | A portszám. |




### <a name="azurefilevolume-object"></a>AzureFileVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  shareName | sztring | Igen | A kötetként csatlakoztatni kívánt Azure-fájlmegosztás neve. |
|  readOnly | boolean | Nem | Az a jelző, amely azt jelzi, hogy a kötetként csatlakoztatott Azure-fájl írásvédett-e. |
|  storageAccountName | sztring | Igen | Az Azure-fájlmegosztást tartalmazó Storage-fiók neve. |
|  storageAccountKey | sztring | No | Az Azure-fájlmegosztás eléréséhez használt Storage-fiók elérési kulcsa. |




### <a name="gitrepovolume-object"></a>GitRepoVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  címtár | sztring | No | Célkönyvtár neve. A nem tartalmazhat ".." karaktert.  Ha a "." meg van adva, a kötet könyvtára lesz a git-tárház.  Ellenkező esetben, ha meg van adva, a kötet a megadott nevű alkönyvtárban található git-tárházat fogja tartalmazni. |
|  tárház | sztring | Igen | Tárház URL-címe |
|  változat | sztring | No | A megadott változat kivonatának véglegesítve. |



### <a name="loganalytics-object"></a>LogAnalytics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sztring | Igen | A log Analytics-munkaterület azonosítója |
|  workspaceKey | sztring | Igen | A log Analytics-munkaterület kulcsa |
|  logType | Enum | Nem | A használandó naplózási típus. -ContainerInsights vagy ContainerInstanceLogs |
|  metaadatok | object | Nem | A log Analytics metaadatai. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition objektum

| Név  | Típus  | Kötelező  | Érték |
|  ---- | ---- | ---- | ---- |
| image | sztring    | No    | Az init-tároló képe. |
| command   | array | Nem    | Az init-tárolón belül végrehajtandó parancs exec formában. -string |
| environmentVariables | array  | Nem |Az init-tárolóban beállított környezeti változók. - [EnvironmentVariable objektum](#environmentvariable-object)
| volumeMounts |array   | Nem    | Az init-tároló számára elérhető kötetek csatlakoztatása. - [VolumeMount objektum](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  protokoll | Enum | Nem | A porthoz társított protokoll. -TCP vagy UDP |
|  port | egész szám | Igen | A tároló csoporton belül elérhető portszám. |




### <a name="environmentvariable-object"></a>EnvironmentVariable objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Igen | A környezeti változó neve. |
|  érték | sztring | No | A környezeti változó értéke. |
|  Securevalue paraméterét | sztring | No | A biztonságos környezeti változó értéke. |




### <a name="resourcerequirements-object"></a>ResourceRequirements objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  kérelmek | object | Igen | A tároló-példány erőforrás-kérései. - [ResourceRequests objektum](#resourcerequests-object) |
|  határértékeket | object | Nem | A tároló-példány erőforrás-korlátai. - [ResourceLimits objektum](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  name | sztring | Igen | A kötet csatlakoztatásának neve. |
|  mountPath | sztring | Igen | Az elérési út a tárolón belül, ahol a kötetet csatlakoztatni kell. Nem tartalmazhat kettőspontot (:). |
|  readOnly | boolean | Nem | Az a jelző, amely azt jelzi, hogy a kötet csatlakoztatása írásvédett-e. |




### <a name="containerprobe-object"></a>ContainerProbe objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  exec | object | Nem | A [ContainerExec objektum](#containerexec-object) végrehajtási parancsa |
|  httpGet | object | Nem | A http-lekérési beállítások mintavételi [ContainerHttpGet objektum](#containerhttpget-object) |
|  initialDelaySeconds | egész szám | Nem | A kezdeti késleltetés másodpercben. |
|  periodSeconds | egész szám | Nem | Az időtartam másodpercben. |
|  failureThreshold | egész szám | Nem | A hiba küszöbértéke. |
|  successThreshold | egész szám | Nem | A siker küszöbértéke. |
|  timeoutSeconds | egész szám | Nem | Az időkorlát másodpercben. |




### <a name="resourcerequests-object"></a>ResourceRequests objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | Igen | A tároló példányának GB-ban megadott memória-kérelem. |
|  CPU | szám | Igen | A tároló-példány CPU-kérelme. |
|  GPU | object | Nem | A tároló-példány GPU-kérelme. - [GpuResource objektum](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | Nem | A tároló példány GB-ban megadott memória-korlátja. |
|  CPU | szám | Nem | A tároló-példány CPU-korlátja. |
|  GPU | object | Nem | A Container-példány GPU-korlátja. - [GpuResource objektum](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  command | array | Nem | A tárolón belül végrehajtandó parancsok. -string |




### <a name="containerhttpget-object"></a>ContainerHttpGet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  path | sztring | No | A mintavétel elérési útja. |
|  port | egész szám | Igen | A mintavételhez használandó portszám. |
|  séma | Enum | Nem | A séma. -http vagy https |




### <a name="gpuresource-object"></a>GpuResource objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  count | egész szám | Igen | A GPU-erőforrás száma. |
|  SKU | Enum | Igen | A GPU-erőforrás SKU-jának. -K80, P100, V100 |


## <a name="next-steps"></a>További lépések

Tekintse meg a [többtárolós csoport üzembe helyezése YAML-fájl használatával](container-instances-multi-container-yaml.md)című oktatóanyagot.

Tekintse át a YAML-fájl használatával történő üzembe helyezési példákat egy [virtuális hálózatban](container-instances-vnet.md) , vagy [egy külső kötet csatlakoztatásához](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

