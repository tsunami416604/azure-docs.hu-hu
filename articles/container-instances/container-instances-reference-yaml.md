---
title: Azure Container Instances YAML-hivatkozás
description: A Azure Container Instances által támogatott YAML-fájlra mutató hivatkozás egy tároló csoport konfigurálásához
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179903"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-hivatkozás: Azure Container Instances

Ez a cikk az Azure Container Instances által támogatott YAML-fájl szintaxisát és tulajdonságait ismerteti egy [tároló csoport](container-instances-container-groups.md)konfigurálásához. YAML-fájl használatával adja meg a csoport konfigurációját az Azure CLI-ben az az [Container Create][az-container-create] paranccsal. 

A YAML-fájlok kényelmes módot biztosítanak egy tároló csoport konfigurálására a reprodukálható központi telepítésekhez. Ez egy rövid alternatíva egy [Resource Manager-sablon](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) vagy a Azure Container instances SDK-k használatával egy tároló csoport létrehozásához vagy frissítéséhez.

> [!NOTE]
> Ez a hivatkozás a Azure Container Instances REST API verziójának `2018-10-01`YAML-fájljaira vonatkozik.

## <a name="schema"></a>Séma 

A YAML-fájl sémája a következőt követi, beleértve a legfontosabb tulajdonságok kiemeléséhez szükséges megjegyzéseket is. A séma tulajdonságainak leírását a [tulajdonságértékek](#property-values) című szakaszban találja.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
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
      ports: # Exposed ports on the instance
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
```

## <a name="property-values"></a>Tulajdonságok értékei

A következő táblázatok ismertetik a sémában beállítani kívánt értékeket.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. ContainerInstance/containerGroups objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  name | Karakterlánc | Igen | A tároló csoport neve. |
|  apiVersion | Enum | Igen | 2018-10-01 |
|  location | Karakterlánc | Nem | Az erőforrás helye. |
|  címkék | object | Nem | Az erőforrás-címkék. |
|  identity | object | Nem | A tároló csoport identitása, ha be van állítva. - [ContainerGroupIdentity objektum](#ContainerGroupIdentity) |
|  properties | object | Igen | [ContainerGroupProperties objektum](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  type | Enum | Nem | A tároló csoporthoz használt identitás típusa A "SystemAssigned, UserAssigned" típus egy implicit módon létrehozott identitást és egy felhasználó által hozzárendelt identitást is tartalmaz. A "None" típus eltávolítja az összes identitást a tároló csoportjából. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, none |
|  userAssignedIdentities | object | Nem | A tároló csoporthoz társított felhasználói identitások listája. A felhasználói azonosító szótár kulcsára vonatkozó hivatkozások a következő formában lesznek Azure Resource Manager erőforrás-azonosítók: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  tárolók | array | Igen | A tároló csoportban lévő tárolók. - [Tároló objektum](#Container) |
|  imageRegistryCredentials | array | Nem | A rendszerkép beállításjegyzékbeli hitelesítő adatai, amelyek alapján a rendszer létrehozza a tároló csoportját. - [ImageRegistryCredential objektum](#ImageRegistryCredential) |
|  restartPolicy | Enum | Nem | A tároló csoportban lévő összes tárolóra vonatkozó újraindítási szabályzat. - `Always`Mindig újraindítás – `OnFailure` újraindítás hiba esetén – `Never` soha ne indítsa újra a rendszert. -Mindig, OnFailure, soha |
|  IP-cím | object | Nem | A tároló csoport IP-címének típusa. - [IP-cím objektum](#IpAddress) |
|  osType | Enum | Igen | A tároló csoportban lévő tárolók által igényelt operációs rendszer típusa. – Windows vagy Linux |
|  kötetek | array | Nem | Azoknak a köteteknek a listája, amelyeket tárolók csatlakoztatnak a tároló csoportba. - [Kötet objektum](#Volume) |
|  diagnosztika | object | Nem | Egy tároló csoport diagnosztikai adatai. - [ContainerGroupDiagnostics objektum](#ContainerGroupDiagnostics) |
|  networkProfile | object | Nem | Egy tároló csoport hálózati profiljának adatai. - [ContainerGroupNetworkProfile objektum](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Nem | Egy tároló csoport DNS-konfigurációs adatai. - [DnsConfiguration objektum](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Tároló objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  name | Karakterlánc | Igen | A tároló példány felhasználó által megadott neve. |
|  properties | object | Igen | A tároló példányának tulajdonságai. - [ContainerProperties objektum](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  server | Karakterlánc | Igen | A Docker-rendszerkép beállításjegyzék-kiszolgálója olyan protokoll nélkül, mint a "http" és a "https". |
|  username | Karakterlánc | Igen | A privát beállításjegyzék felhasználóneve. |
|  password | Karakterlánc | Nem | A privát beállításjegyzék jelszava. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IP-cím objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | Igen | A tároló csoporton elérhető portok listája. - [Port objektum](#Port) |
|  type | Enum | Igen | Meghatározza, hogy az IP-cím elérhető-e a nyilvános internethez vagy a privát VNET. -Nyilvános vagy privát |
|  IP | Karakterlánc | Nem | A nyilvános internethez elérhető IP-cím. |
|  dnsNameLabel | Karakterlánc | Nem | Az IP-címhez tartozó DNS-név címkéje. |


<a id="Volume" />

### <a name="volume-object"></a>Kötet objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  name | Karakterlánc | Igen | A kötet neve. |
|  AzureFile | object | Nem | Az Azure-fájl kötete. - [AzureFileVolume objektum](#AzureFileVolume) |
|  emptyDir | object | Nem | Az üres könyvtár kötete. |
|  secret | object | Nem | A titkos kötet. |
|  gitRepo | object | Nem | A git-tárház kötete. - [GitRepoVolume objektum](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nem | A Container Group log Analytics információi. - [LogAnalytics objektum](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  id | Karakterlánc | Igen | Egy hálózati profil azonosítója. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  Névkiszolgálók | array | Igen | A tároló csoport DNS-kiszolgálói. -string |
|  searchDomains | Karakterlánc | Nem | A DNS-keresési tartományok az állomásnév kereséséhez a tároló csoportban. |
|  options | Karakterlánc | Nem | A tároló csoport DNS-beállításai. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  image | Karakterlánc | Igen | A Container-példány létrehozásához használt rendszerkép neve. |
|  A parancs | array | Nem | A tároló-példányon belül végrehajtandó parancsok az exec formában. -string |
|  ports | array | Nem | A tároló példányon elérhető portok. - [ContainerPort objektum](#ContainerPort) |
|  environmentVariables | array | Nem | A tároló-példányban beállított környezeti változók. - [EnvironmentVariable objektum](#EnvironmentVariable) |
|  erőforrások | object | Igen | A Container-példány erőforrás-követelményei. - [ResourceRequirements objektum](#ResourceRequirements) |
|  volumeMounts | array | Nem | A tároló példány számára elérhető kötetek csatlakoztatása. - [VolumeMount objektum](#VolumeMount) |
|  livenessProbe | object | Nem | Az élettartam-mintavétel. - [ContainerProbe objektum](#ContainerProbe) |
|  readinessProbe | object | Nem | A készültségi mintavétel. - [ContainerProbe objektum](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Nem | A porthoz társított protokoll. -TCP vagy UDP |
|  port | integer | Igen | A portszám. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | Karakterlánc | Igen | A kötetként csatlakoztatni kívánt Azure-fájlmegosztás neve. |
|  readOnly | boolean | Nem | Az a jelző, amely azt jelzi, hogy a kötetként csatlakoztatott Azure-fájl írásvédett-e. |
|  storageAccountName | Karakterlánc | Igen | Az Azure-fájlmegosztást tartalmazó Storage-fiók neve. |
|  storageAccountKey | Karakterlánc | Nem | Az Azure-fájlmegosztás eléréséhez használt Storage-fiók elérési kulcsa. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  címtár | Karakterlánc | Nem | Célkönyvtár neve. A nem tartalmazhat ".." karaktert.  Ha a "." meg van adva, a kötet könyvtára lesz a git-tárház.  Ellenkező esetben, ha meg van adva, a kötet a megadott nevű alkönyvtárban található git-tárházat fogja tartalmazni. |
|  repository | Karakterlánc | Igen | Tárház URL-címe |
|  Változat | Karakterlánc | Nem | A megadott változat kivonatának véglegesítve. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | Karakterlánc | Igen | A log Analytics-munkaterület azonosítója |
|  workspaceKey | Karakterlánc | Igen | A log Analytics-munkaterület kulcsa |
|  logType | Enum | Nem | A használandó naplózási típus. -ContainerInsights vagy ContainerInstanceLogs |
|  metaadatok | object | Nem | A log Analytics metaadatai. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | Enum | Nem | A porthoz társított protokoll. -TCP vagy UDP |
|  port | integer | Igen | A tároló csoporton belül elérhető portszám. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  name | Karakterlánc | Igen | A környezeti változó neve. |
|  value | Karakterlánc | Nem | A környezeti változó értéke. |
|  Securevalue paraméterét | Karakterlánc | Nem | A biztonságos környezeti változó értéke. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  kérelmek | object | Igen | A tároló-példány erőforrás-kérései. - [ResourceRequests objektum](#ResourceRequests) |
|  Határértékeket | object | Nem | A tároló-példány erőforrás-korlátai. - [ResourceLimits objektum](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  name | Karakterlánc | Igen | A kötet csatlakoztatásának neve. |
|  mountPath | Karakterlánc | Igen | Az elérési út a tárolón belül, ahol a kötetet csatlakoztatni kell. Nem tartalmazhat kettőspontot (:). |
|  readOnly | boolean | Nem | Az a jelző, amely azt jelzi, hogy a kötet csatlakoztatása írásvédett-e. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | Nem | A [ContainerExec objektum](#ContainerExec) végrehajtási parancsa |
|  httpGet | object | Nem | A http-lekérési beállítások mintavételi [ContainerHttpGet objektum](#ContainerHttpGet) |
|  initialDelaySeconds | integer | Nem | A kezdeti késleltetés másodpercben. |
|  periodSeconds | integer | Nem | Az időtartam másodpercben. |
|  failureThreshold | integer | Nem | A hiba küszöbértéke. |
|  successThreshold | integer | Nem | A siker küszöbértéke. |
|  timeoutSeconds | integer | Nem | Az időkorlát másodpercben. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Igen | A tároló példányának GB-ban megadott memória-kérelem. |
|  cpu | number | Igen | A tároló-példány CPU-kérelme. |
|  gpu | object | Nem | A tároló-példány GPU-kérelme. - [GpuResource objektum](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Nem | A tároló példány GB-ban megadott memória-korlátja. |
|  cpu | number | Nem | A tároló-példány CPU-korlátja. |
|  gpu | object | Nem | A Container-példány GPU-korlátja. - [GpuResource objektum](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  A parancs | array | Nem | A tárolón belül végrehajtandó parancsok. -string |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  path | Karakterlánc | Nem | A mintavétel elérési útja. |
|  port | integer | Igen | A mintavételhez használandó portszám. |
|  séma | Enum | Nem | A séma. -http vagy https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource objektum

|  Name (Név) | Type | Szükséges | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | Igen | A GPU-erőforrás száma. |
|  sku | Enum | Igen | A GPU-erőforrás SKU-jának. -K80, P100, V100 |


## <a name="next-steps"></a>További lépések

Tekintse meg a [többtárolós csoport üzembe helyezése YAML-fájl használatával](container-instances-multi-container-yaml.md)című oktatóanyagot.

Tekintse át a YAML-fájl használatával történő üzembe helyezési példákat egy [virtuális hálózatban](container-instances-vnet.md) , vagy [egy külső kötet csatlakoztatásához](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

