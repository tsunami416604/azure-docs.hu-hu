---
title: Azure Service Fabric – Tárolótár hitelesítő adatainak konfigurálása
description: Tárház hitelesítő adatainak konfigurálása a rendszerképek tárolóbeállításjegyzékből való letöltéséhez
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934978"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Tárház hitelesítő adatainak konfigurálása az alkalmazás számára a tárolórendszerképek letöltéséhez

Konfigurálja a tároló `RepositoryCredentials` beállításjegyzék-hitelesítését az `ContainerHostPolicies` alkalmazásjegyzék szakaszához való hozzáadással. Adja hozzá a fiókot és a jelszót a tároló rendszerleíró adatbázisához (*myregistry.azurecr.io* az alábbi példában), amely lehetővé teszi a szolgáltatás számára a tárolórendszerkép letöltését a tárházból.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Javasoljuk, hogy titkosítsa a tárház jelszavát egy olyan titkosítási tanúsítvány használatával, amely a fürt összes csomópontjára telepítve van. Amikor a Service Fabric üzembe helyezi a szervizcsomagot a fürtön, a titkosítási tanúsítvánnyal fejti vissza a titkosított szöveget. Az Invoke-ServiceFabricEncryptText parancsmaggal hozhat létre titkosított szöveget a jelszóhoz, amelyet a rendszer hozzáad az ApplicationManifest.xml fájlhoz.
További [témakör: Titkos kezelés](service-fabric-application-secret-management.md) a tanúsítványokról és a titkosítási szemantikáról.

## <a name="configure-cluster-wide-credentials"></a>Fürtszintű hitelesítő adatok konfigurálása

A Service Fabric lehetővé teszi a fürtszintű hitelesítő adatok konfigurálását, amelyek az alkalmazások alapértelmezett tárház-hitelesítő adatokként használhatók.

Ez a szolgáltatás engedélyezhető vagy `UseDefaultRepositoryCredentials` letiltható, ha hozzáadja az attribútumot az ApplicationManifest.xml fájlhoz `ContainerHostPolicies` egy vagy `true` `false` értékkel.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

A Service Fabric ezután az alapértelmezett tárház hitelesítő adatait használja, amelyek a `Hosting` csoportban a ClusterManifest-ben adhatók meg.  Ha `UseDefaultRepositoryCredentials` `true`a , a Service Fabric beolvassa a következő értékeket a ClusterManifest:If is , Service Fabric reads the following values from the ClusterManifest:

* DefaultContainerRepositoryAccountName (karakterlánc)
* DefaultContainerRepositoryPassword (karakterlánc)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (karakterlánc)

Íme egy példa arra, hogy `Hosting` mit lehet hozzáadni a ClusterManifestTemplate.json fájl szakaszán belül. A `Hosting` szakasz hozzáadható a fürt létrehozásakor vagy később egy konfigurációs frissítésben. További információ: [Az Azure Service Fabric fürtbeállításainak módosítása](service-fabric-cluster-fabric-settings.md) és [az Azure Service Fabric-alkalmazások titkos adatainak kezelése](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Jogkivonatok használata rendszerleíró hitelesítő adatként

A Service Fabric támogatja a jogkivonatok hitelesítő adatok használatával a tárolók lemezképeinek letöltéséhez.  Ez a szolgáltatás az alapul szolgáló virtuálisgép-méretezési készlet *felügyelt identitását* használja a beállításjegyzék hitelesítéséhez, így nincs szükség a felhasználói hitelesítő adatok kezelésére.  További [információ: Felügyelt identitások az Azure-erőforrásokhoz.](../active-directory/managed-identities-azure-resources/overview.md)  A szolgáltatás használatához a következő lépésekre van szükség:

1. Győződjön meg arról, hogy *a rendszer hozzárendelt felügyelt identitás* engedélyezve van a virtuális gép.

    ![Azure Portal: Virtuálisgép-méretezési csoport identitásának létrehozása](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Engedélyek et adhat a virtuálisgép-méretezési készlethez a lemezképek lekérése/olvasása a beállításjegyzékből. Az Azure Container Registry hozzáférés-vezérlési (IAM) paneljén az Azure Portalon, adjon hozzá egy *szerepkör-hozzárendelést* a virtuális géphez:

    ![Virtuálisgép-egyszerű felhasználó hozzáadása az ACR-hez](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Ezután módosítsa az alkalmazásjegyzéket. A `ContainerHostPolicies` szakaszban adja hozzá `‘UseTokenAuthenticationCredentials=”true”`az attribútumot .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > A `UseDefaultRepositoryCredentials` jelző értéke `UseTokenAuthenticationCredentials` igaz, míg igaz hibát okoz a telepítés során.

## <a name="next-steps"></a>További lépések

* További információk a [tárolóbeállítás-beállításjegyzék-hitelesítésről.](../container-registry/container-registry-authentication.md)
