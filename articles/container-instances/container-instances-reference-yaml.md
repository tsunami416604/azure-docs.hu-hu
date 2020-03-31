---
title: YAML-hivatkozás a tárolócsoporthoz
description: Hivatkozás az Azure Container Instances által támogatott YAML-fájlhoz egy tárolócsoport konfigurálásához
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896569"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML-hivatkozás: Azure-tárolópéldányok

Ez a cikk ismerteti az Azure Container Instances által támogatott YAML-fájl szintaxisát és tulajdonságait egy [tárolócsoport](container-instances-container-groups.md)konfigurálásához. YAML-fájl használatával adja meg a csoport konfigurációját az [az container create][az-container-create] parancs az Azure CLI-ben. 

A YAML-fájl kényelmes módja a tárolócsoport reprodukálható központi telepítésekhez történő konfigurálásának. Ez egy tömör alternatívája egy [Resource Manager-sablon](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) vagy az Azure Container Instances SDK-k használatával egy tárolócsoport létrehozásához vagy frissítéséhez.

> [!NOTE]
> Ez a hivatkozás az Azure Container Instances REST `2018-10-01`API-verziójához szükséges YAML-fájlokra vonatkozik.

## <a name="schema"></a>Séma 

A YAML-fájl sémája következik, beleértve a kulcsfontosságú tulajdonságok kiemeléséhez fűzött megjegyzéseket is. A séma tulajdonságainak leírását a [Tulajdonságértékek](#property-values) szakaszban található.

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
```

## <a name="property-values"></a>Tulajdonság értékek

Az alábbi táblázatok a sémában beállítható értékeket ismertetik.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A tárolócsoport neve. |
|  apiVersion | Enum | Igen | 2018-10-01 |
|  location | sztring | Nem | Az erőforrás helye. |
|  címkét | objektum | Nem | Az erőforráscímkék. |
|  identity | objektum | Nem | A tárolócsoport identitása, ha be van állítva. - [ContainerGroupIdentity objektum](#ContainerGroupIdentity) |
|  properties | objektum | Igen | [ContainerGroupProperties objektum](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  type | Enum | Nem | A tárolócsoporthoz használt identitás típusa. A "SystemAssigned, UserAssigned" típus implicit módon létrehozott identitást és a felhasználó által hozzárendelt identitások csoportját is tartalmazza. A "Nincs" típus eltávolítja az identitásokat a tárolócsoportból. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | objektum | Nem | A tárolócsoporthoz társított felhasználói identitások listája. A felhasználói identitás szótárkulcs-hivatkozásai az Azure Resource Manager erőforrás-azonosítói lesznek az űrlapon: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  tárolók | tömb | Igen | A tárolócsoporton belüli tárolók. - [Tárolóobjektum](#Container) |
|  imageRegistryCredentials | tömb | Nem | A lemezkép-jegyzék hitelesítő adatai, amelyekből a tárolócsoport létrejön. - [ImageRegistryCredential objektum](#ImageRegistryCredential) |
|  újraindításPolicy | Enum | Nem | Indítsa újra a házirendet a tárolócsoporton belüli összes tárolóhoz. - `Always`Mindig indítsa `OnFailure` újra- `Never` Újraindítás hiba esetén- Soha ne indítsa újra. - Mindig, onfailure, soha |
|  ipAddress cím | objektum | Nem | A tárolócsoport IP-címtípusa. - [IpAddress objektum](#IpAddress) |
|  osType típus | Enum | Igen | A tárolócsoportban lévő tárolók által igényelt operációsrendszer-típus. - Windows vagy Linux |
|  volumes | tömb | Nem | Az ebben a tárolócsoportban lévő tárolók által csatlakoztatható kötetek listája. - [Kötet objektum](#Volume) |
|  diagnosztika | objektum | Nem | Egy tárolócsoport diagnosztikai információi. - [ContainerGroupDiagnostics objektum](#ContainerGroupDiagnostics) |
|  networkProfile | objektum | Nem | A tárolócsoport hálózati profiladatai. - [ContainerGroupNetworkProfile objektum](#ContainerGroupNetworkProfile) |
|  dnsConfig között | objektum | Nem | A tárolócsoport DNS-konfigurációs információi. - [DnsConfiguration objektum](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Tárolóobjektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A tárolópéldány felhasználó által megadott neve. |
|  properties | objektum | Igen | A tárolópéldány tulajdonságai. - [ContainerProperties objektum](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  kiszolgáló | sztring | Igen | A Docker rendszerkép-beállításjegyzék-kiszolgáló protokoll nélkül, például "http" és "https". |
|  felhasználónév | sztring | Igen | A magánrendszerleíró adatbázis felhasználóneve. |
|  jelszó | sztring | Nem | A privát rendszerleíró adatbázis jelszava. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  ports | tömb | Igen | A tárolócsoportban elérhető portok listája. - [Port objektum](#Port) |
|  type | Enum | Igen | Itt adható meg, hogy az IP-cím elérhető-e a nyilvános interneten vagy a privát virtuális hálózaton. - Nyilvános vagy privát |
|  Ip | sztring | Nem | A nyilvános internetnek kitett IP-cím. |
|  dnsNameLabel | sztring | Nem | Az IP DNS-névcímkéje. |


<a id="Volume" />

### <a name="volume-object"></a>Kötet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A kötet neve. |
|  azureFile fájl | objektum | Nem | Az Azure-fájl kötete. - [AzureFileVolume objektum](#AzureFileVolume) |
|  emptyDir | objektum | Nem | Az üres könyvtárkötet. |
|  titkos kód | objektum | Nem | A titkos kötet. |
|  gitRepo | objektum | Nem | A git-tárhangkötet. - [GitRepoVolume objektum](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objektum | Nem | Tárolócsoport-napló elemzési adatai. - [LogAnalytics objektum](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  id | sztring | Igen | A hálózati profil azonosítója. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Névszervereket | tömb | Igen | A tárolócsoport DNS-kiszolgálói. - karakterlánc |
|  searchDomains (Domains) kifejezés | sztring | Nem | A tárolócsoport állomásnév-kereséséhez tartozó DNS-kereső tartományok. |
|  Lehetőségek | sztring | Nem | A tárolócsoport DNS-beállításai. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  image | sztring | Igen | A tárolópéldány létrehozásához használt lemezkép neve. |
|  command | tömb | Nem | A tárolópéldányon belül exec formában végrehajtandó parancsok. - karakterlánc |
|  ports | tömb | Nem | A tárolópéldány elérhető portjai. - [ContainerPort objektum](#ContainerPort) |
|  környezetVáltozók | tömb | Nem | A tárolópéldányban beállított környezeti változók. - [EnvironmentVariable objektum](#EnvironmentVariable) |
|  resources | objektum | Igen | A tárolópéldány erőforrás-követelményei. - [ResourceRequirements objektum](#ResourceRequirements) |
|  kötetMounts | tömb | Nem | A kötetcsatlakoztatások a tárolópéldány számára érhetők el. - [VolumeMount objektum](#VolumeMount) |
|  livenessProbe (élőségSzonda) | objektum | Nem | Az élesség szonda. - [ContainerProbe objektum](#ContainerProbe) |
|  készenlétProbe | objektum | Nem | A készültségi szonda. - [ContainerProbe objektum](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Protokoll | Enum | Nem | A porthoz társított protokoll. - TCP vagy UDP |
|  port | egész szám | Igen | A portszám. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  megosztásneve | sztring | Igen | A kötetként csatlakoztatandó Azure-fájlmegosztás neve. |
|  readOnly | logikai | Nem | A jelző, amely jelzi, hogy az Azure-fájl megosztott kötetként van-e írásvédett. |
|  storageAccountName | sztring | Igen | Az Azure File share-t tartalmazó tárfiók neve. |
|  storageAccountKey | sztring | Nem | A tárfiók hozzáférési kulcs a hozzáférés az Azure File share eléréséhez használt. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  címtár | sztring | Nem | Célkönyvtár neve. Nem tartalmazhat"  Ha a "." van megadva, a kötetkönyvtár lesz a git tárház.  Ellenkező esetben, ha meg van adva, a kötet a megadott nevű alkönyvtárban a git-tárházat fogja tartalmazni. |
|  Tárház | sztring | Igen | Tárház URL-címe |
|  változat | sztring | Nem | Kivonat véglegesítése a megadott verzióhoz. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  workspaceId | sztring | Igen | A munkaterület azonosítója a naplóelemzéshez |
|  munkaterületkulcs | sztring | Igen | A munkaterületi kulcs a naplóelemzéshez |
|  logType (logType) | Enum | Nem | A használandó naplótípus. - ContainerInsights vagy ContainerInstanceLogs |
|  metaadatok | objektum | Nem | Metaadatok a naplóelemzéshez. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Protokoll | Enum | Nem | A porthoz társított protokoll. - TCP vagy UDP |
|  port | egész szám | Igen | A tárolócsoporton belül elérhetőportszám. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A környezeti változó neve. |
|  érték | sztring | Nem | A környezeti változó értéke. |
|  secureValue | sztring | Nem | A biztonságos környezeti változó értéke. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Kérelmek | objektum | Igen | A tárolópéldány erőforrás-kérelmei. - [ResourceRequests objektum](#ResourceRequests) |
|  Határok | objektum | Nem | A tárolópéldány erőforráskorlátai. - [ResourceLimits objektum](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  név | sztring | Igen | A kötetcsatlakoztatás neve. |
|  mountPath | sztring | Igen | Az az útvonal a tárolón belül, ahová a kötetet fel kell szerelni. Nem tartalmazhat kettőspontot (:). |
|  readOnly | logikai | Nem | A kötetcsatlakoztatás írásvédettjét jelző jelző. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  Exec | objektum | Nem | A mintavételre szolgáló végrehajtási parancs - [ContainerExec objektum](#ContainerExec) |
|  httpGet | objektum | Nem | A http mintavételezendő beállítások beszerezése - [ContainerHttpGet objektum](#ContainerHttpGet) |
|  initialDelaySeconds | egész szám | Nem | A kezdeti késleltetési másodperc. |
|  periodSeconds | egész szám | Nem | Az időszak másodperc. |
|  failureThreshold (hibaküszöb) | egész szám | Nem | A hibaküszöbérték. |
|  successThreshold (successThreshold) | egész szám | Nem | A siker küszöbe. |
|  időoutMásodperc | egész szám | Nem | Az időhosszabbítási másodpercek. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | szám | Igen | A tárolópéldány gb-os memóriakérése. |
|  Cpu | szám | Igen | A tárolópéldány PROCESSZOR-kérése. |
|  Gpu | objektum | Nem | A tárolópéldány GPU-kérelme. - [GpuResource objektum](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | szám | Nem | A tárolópéldány gb-ban megadott memóriakorlátja. |
|  Cpu | szám | Nem | A tárolópéldány processzorkorlátja. |
|  Gpu | objektum | Nem | A tárolópéldány GPU-korlátja. - [GpuResource objektum](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  command | tömb | Nem | A tárolón belül végrehajtandó parancsok. - karakterlánc |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>TárolóHttpGet objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  path | sztring | Nem | A szondához vezető út. |
|  port | egész szám | Igen | A megvizsgálandó portszáma. |
|  Rendszer | Enum | Nem | A rendszer. - http vagy https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource objektum

|  Név | Típus | Kötelező | Érték |
|  ---- | ---- | ---- | ---- |
|  count | egész szám | Igen | A GPU-erőforrás száma. |
|  Sku | Enum | Igen | A GPU-erőforrás termékváltozata. - K80, P100, V100 |


## <a name="next-steps"></a>További lépések

Tekintse meg az [oktatóanyagot Többtárolós csoport telepítése YAML-fájl használatával.](container-instances-multi-container-yaml.md)

Példa arra, hogy yAML-fájlt használ tárolócsoportok központi telepítéséhez [virtuális hálózatban](container-instances-vnet.md) vagy [külső kötetet csatlakoztatva.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

