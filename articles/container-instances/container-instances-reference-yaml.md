---
title: YAML-hivatkozás a tároló csoportjához
description: A Azure Container Instances által támogatott YAML-fájlra mutató hivatkozás egy tároló csoport konfigurálásához
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 5603f2e0f63c4f83a6d3761feb540abb8b8b7d5c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533491"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-hivatkozás: Azure Container Instances

Ez a cikk az Azure Container Instances által támogatott YAML-fájl szintaxisát és tulajdonságait ismerteti egy [tároló csoport](container-instances-container-groups.md)konfigurálásához. YAML-fájl használatával adja meg a csoport konfigurációját az Azure CLI-ben az az [Container Create][az-container-create] paranccsal. 

A YAML-fájlok kényelmes módot biztosítanak egy tároló csoport konfigurálására a reprodukálható központi telepítésekhez. Ez egy rövid alternatíva egy [Resource Manager-sablon](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) vagy a Azure Container instances SDK-k használatával egy tároló csoport létrehozásához vagy frissítéséhez.

> [!NOTE]
> Ez a hivatkozás a Azure Container Instances REST API `2018-10-01`verziójának YAML-fájljaira vonatkozik.

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

## <a name="property-values"></a>Tulajdonságértékek

A következő táblázatok ismertetik a sémában beállítani kívánt értékeket.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft. ContainerInstance/containerGroups objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A tároló csoport neve. |
|  apiVersion | Enum | Igen | 2018-10-01 |
|  location | sztring | Nem | Az erőforrás helye. |
|  tags | objektum | Nem | Az erőforrás-címkék. |
|  identitáskezelés | objektum | Nem | A tároló csoport identitása, ha be van állítva. - [ContainerGroupIdentity objektum](#ContainerGroupIdentity) |
|  properties | objektum | Igen | [ContainerGroupProperties objektum](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  type | Enum | Nem | A tároló csoporthoz használt identitás típusa A "SystemAssigned, UserAssigned" típus egy implicit módon létrehozott identitást és egy felhasználó által hozzárendelt identitást is tartalmaz. A "None" típus eltávolítja az összes identitást a tároló csoportjából. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, none |
|  userAssignedIdentities | objektum | Nem | A tároló csoporthoz társított felhasználói identitások listája. A felhasználói azonosító szótár kulcsára vonatkozó hivatkozások a következő formában lesznek Azure Resource Manager erőforrás-azonosítók: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  tárolók | tömb | Igen | A tároló csoportban lévő tárolók. - [Container objektum](#Container) |
|  imageRegistryCredentials | tömb | Nem | A rendszerkép beállításjegyzékbeli hitelesítő adatai, amelyek alapján a rendszer létrehozza a tároló csoportját. - [ImageRegistryCredential objektum](#ImageRegistryCredential) |
|  restartPolicy | Enum | Nem | A tároló csoportban lévő összes tárolóra vonatkozó újraindítási szabályzat. - `Always` mindig újraindítás – `OnFailure` újraindítás sikertelenül – `Never` soha ne indítsa újra a műveletet. -Mindig, OnFailure, soha |
|  IP-cím | objektum | Nem | A tároló csoport IP-címének típusa. - [IP](#IpAddress) -cím objektum |
|  osType | Enum | Igen | A tároló csoportban lévő tárolók által igényelt operációs rendszer típusa. – Windows vagy Linux |
|  volumes | tömb | Nem | Azoknak a köteteknek a listája, amelyeket tárolók csatlakoztatnak a tároló csoportba. - [kötet objektum](#Volume) |
|  diagnosztika | objektum | Nem | Egy tároló csoport diagnosztikai adatai. - [ContainerGroupDiagnostics objektum](#ContainerGroupDiagnostics) |
|  networkProfile | objektum | Nem | Egy tároló csoport hálózati profiljának adatai. - [ContainerGroupNetworkProfile objektum](#ContainerGroupNetworkProfile) |
|  dnsConfig | objektum | Nem | Egy tároló csoport DNS-konfigurációs adatai. - [DnsConfiguration objektum](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Tároló objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A tároló példány felhasználó által megadott neve. |
|  properties | objektum | Igen | A tároló példányának tulajdonságai. - [ContainerProperties objektum](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  kiszolgáló | sztring | Igen | A Docker-rendszerkép beállításjegyzék-kiszolgálója olyan protokoll nélkül, mint a "http" és a "https". |
|  felhasználónév | sztring | Igen | A privát beállításjegyzék felhasználóneve. |
|  jelszó | sztring | Nem | A privát beállításjegyzék jelszava. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IP-cím objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  ports | tömb | Igen | A tároló csoporton elérhető portok listája. - [port objektum](#Port) |
|  type | Enum | Igen | Meghatározza, hogy az IP-cím elérhető-e a nyilvános internethez vagy a privát VNET. -Nyilvános vagy privát |
|  IP | sztring | Nem | A nyilvános internethez elérhető IP-cím. |
|  dnsNameLabel | sztring | Nem | Az IP-címhez tartozó DNS-név címkéje. |


<a id="Volume" />

### <a name="volume-object"></a>Kötet objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A kötet neve. |
|  azureFile | objektum | Nem | Az Azure-fájl kötete. - [AzureFileVolume objektum](#AzureFileVolume) |
|  emptyDir | objektum | Nem | Az üres könyvtár kötete. |
|  titkos kód | objektum | Nem | A titkos kötet. |
|  gitRepo | objektum | Nem | A git-tárház kötete. - [GitRepoVolume objektum](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objektum | Nem | A Container Group log Analytics információi. - [LogAnalytics objektum](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  id | sztring | Igen | Egy hálózati profil azonosítója. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  Névkiszolgálók | tömb | Igen | A tároló csoport DNS-kiszolgálói. -string |
|  searchDomains | sztring | Nem | A DNS-keresési tartományok az állomásnév kereséséhez a tároló csoportban. |
|  beállítások | sztring | Nem | A tároló csoport DNS-beállításai. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  image | sztring | Igen | A Container-példány létrehozásához használt rendszerkép neve. |
|  command | tömb | Nem | A tároló-példányon belül végrehajtandó parancsok az exec formában. -string |
|  ports | tömb | Nem | A tároló példányon elérhető portok. - [ContainerPort objektum](#ContainerPort) |
|  environmentVariables | tömb | Nem | A tároló-példányban beállított környezeti változók. - [EnvironmentVariable objektum](#EnvironmentVariable) |
|  erőforrások | objektum | Igen | A Container-példány erőforrás-követelményei. - [ResourceRequirements objektum](#ResourceRequirements) |
|  volumeMounts | tömb | Nem | A tároló példány számára elérhető kötetek csatlakoztatása. - [VolumeMount objektum](#VolumeMount) |
|  livenessProbe | objektum | Nem | Az élettartam-mintavétel. - [ContainerProbe objektum](#ContainerProbe) |
|  readinessProbe | objektum | Nem | A készültségi mintavétel. - [ContainerProbe objektum](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  protokoll | Enum | Nem | A porthoz társított protokoll. -TCP vagy UDP |
|  port | egész szám | Igen | A portszám. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  shareName | sztring | Igen | A kötetként csatlakoztatni kívánt Azure-fájlmegosztás neve. |
|  ReadOnly | logikai | Nem | Az a jelző, amely azt jelzi, hogy a kötetként csatlakoztatott Azure-fájl írásvédett-e. |
|  storageAccountName | sztring | Igen | Az Azure-fájlmegosztást tartalmazó Storage-fiók neve. |
|  storageAccountKey | sztring | Nem | Az Azure-fájlmegosztás eléréséhez használt Storage-fiók elérési kulcsa. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  Directory | sztring | Nem | Célkönyvtár neve. A nem tartalmazhat ".." karaktert.  Ha a "." meg van adva, a kötet könyvtára lesz a git-tárház.  Ellenkező esetben, ha meg van adva, a kötet a megadott nevű alkönyvtárban található git-tárházat fogja tartalmazni. |
|  tárház | sztring | Igen | Tárház URL-címe |
|  változat | sztring | Nem | A megadott változat kivonatának véglegesítve. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  Munkaterület azonosítója | sztring | Igen | A log Analytics-munkaterület azonosítója |
|  workspaceKey | sztring | Igen | A log Analytics-munkaterület kulcsa |
|  logType | Enum | Nem | A használandó naplózási típus. -ContainerInsights vagy ContainerInstanceLogs |
|  metaadatok | objektum | Nem | A log Analytics metaadatai. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  protokoll | Enum | Nem | A porthoz társított protokoll. -TCP vagy UDP |
|  port | egész szám | Igen | A tároló csoporton belül elérhető portszám. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A környezeti változó neve. |
|  érték | sztring | Nem | A környezeti változó értéke. |
|  Securevalue paraméterét | sztring | Nem | A biztonságos környezeti változó értéke. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  kérelmek | objektum | Igen | A tároló-példány erőforrás-kérései. - [ResourceRequests objektum](#ResourceRequests) |
|  határértékeket | objektum | Nem | A tároló-példány erőforrás-korlátai. - [ResourceLimits objektum](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A kötet csatlakoztatásának neve. |
|  mountPath | sztring | Igen | Az elérési út a tárolón belül, ahol a kötetet csatlakoztatni kell. Nem tartalmazhat kettőspontot (:). |
|  ReadOnly | logikai | Nem | Az a jelző, amely azt jelzi, hogy a kötet csatlakoztatása írásvédett-e. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  exec | objektum | Nem | A [ContainerExec objektum](#ContainerExec) végrehajtási parancsa |
|  httpGet | objektum | Nem | A http-lekérési beállítások mintavételi [ContainerHttpGet objektum](#ContainerHttpGet) |
|  initialDelaySeconds | egész szám | Nem | A kezdeti késleltetés másodpercben. |
|  periodSeconds | egész szám | Nem | Az időtartam másodpercben. |
|  failureThreshold | egész szám | Nem | A hiba küszöbértéke. |
|  successThreshold | egész szám | Nem | A siker küszöbértéke. |
|  timeoutSeconds | egész szám | Nem | Az időkorlát másodpercben. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | Igen | A tároló példányának GB-ban megadott memória-kérelem. |
|  cpu | szám | Igen | A tároló-példány CPU-kérelme. |
|  gpu | objektum | Nem | A tároló-példány GPU-kérelme. - [GpuResource objektum](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | szám | Nem | A tároló példány GB-ban megadott memória-korlátja. |
|  cpu | szám | Nem | A tároló-példány CPU-korlátja. |
|  gpu | objektum | Nem | A Container-példány GPU-korlátja. - [GpuResource objektum](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  command | tömb | Nem | A tárolón belül végrehajtandó parancsok. -string |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  elérési útja | sztring | Nem | A mintavétel elérési útja. |
|  port | egész szám | Igen | A mintavételhez használandó portszám. |
|  séma | Enum | Nem | A séma. -http vagy https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource objektum

|  Név | Type (Típus) | Szükséges | Value (Díj) |
|  ---- | ---- | ---- | ---- |
|  count | egész szám | Igen | A GPU-erőforrás száma. |
|  SKU | Enum | Igen | A GPU-erőforrás SKU-jának. -K80, P100, V100 |


## <a name="next-steps"></a>Következő lépések

Tekintse meg a [többtárolós csoport üzembe helyezése YAML-fájl használatával](container-instances-multi-container-yaml.md)című oktatóanyagot.

Tekintse át a YAML-fájl használatával történő üzembe helyezési példákat egy [virtuális hálózatban](container-instances-vnet.md) , vagy [egy külső kötet csatlakoztatásához](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

