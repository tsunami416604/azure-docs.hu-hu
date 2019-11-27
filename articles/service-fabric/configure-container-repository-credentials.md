---
title: Azure Service Fabric – tároló-adattár hitelesítő adatainak konfigurálása | Microsoft Docs
description: Adattár hitelesítő adatainak konfigurálása a lemezképek a tároló beállításjegyzékből való letöltéséhez
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: c415739934e2318ea5287d5eed9f8235029b666f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405620"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Az alkalmazás adattárbeli hitelesítő adatainak konfigurálása a tároló lemezképének letöltéséhez

A tárolóregisztrációs adatbázis hitelesítésének konfigurálásához adja a hozzá a `RepositoryCredentials` elemet az ApplicationManifest.xml fájl `ContainerHostPolicies` eleméhez. Adja meg a myregistry.azurecr.io tárolóregisztrációs adatbázis fiókját és jelszavát, hogy a szolgáltatás le tudja tölteni a tároló rendszerképét az adattárból.

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

Javasoljuk, hogy Titkosítsa a tárház jelszavát egy olyan titkosítási-tanúsítvánnyal, amely a fürt minden csomópontjára telepítve van. Amikor a Service Fabric üzembe helyezi a szervizcsomagot a fürtön, a titkosítási tanúsítvánnyal fejti vissza a titkosított szöveget. Az Invoke-ServiceFabricEncryptText parancsmaggal hozhat létre titkosított szöveget a jelszóhoz, amelyet a rendszer hozzáad az ApplicationManifest.xml fájlhoz.
A tanúsítványokkal és a titkosítási szemantikagal kapcsolatos további információkért lásd: [Secret Management](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>A fürtre kiterjedő hitelesítő adatok konfigurálása

Service Fabric lehetővé teszi a teljes fürtre kiterjedő hitelesítő adatok konfigurálását, amelyek az alkalmazások alapértelmezett tárházbeli hitelesítő adataiként használhatók.

Ez a funkció engedélyezhető vagy letiltható úgy, hogy hozzáadja a `UseDefaultRepositoryCredentials` attribútumot, hogy a ApplicationManifest. xml fájlban `true` vagy `false` értékkel `ContainerHostPolicies`.

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

Service Fabric ezután az alapértelmezett adattár hitelesítő adatait használja, amelyek a `Hosting` szakaszban megadhatók a ClusterManifest.  Ha `UseDefaultRepositoryCredentials` `true`, a Service Fabric a következő értékeket olvassa be a ClusterManifest:

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (karakterlánc)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* A DefaultContainerRepositoryPasswordType (string)---a 6,4 futtatókörnyezettől kezdődően támogatott

Íme egy példa arra, hogy mi lehet a ClusterManifestTemplate. JSON fájl `Hosting` szakasza. A `Hosting` szakasz a fürt létrehozásakor vagy később is hozzáadható egy konfigurációs frissítésben. További információ: az [azure Service Fabric-fürt beállításainak módosítása](service-fabric-cluster-fabric-settings.md) és az [Azure Service Fabric alkalmazás-titkok kezelése](service-fabric-application-secret-management.md)

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>A virtuálisgép-méretezési csoport felügyelt identitásának kihasználása felügyelt Identitáskezelés (MSI) használatával

Service Fabric támogatja a tokenek használatát hitelesítő adatként a tárolók képeinek letöltéséhez.  Ez a szolgáltatás kihasználja a mögöttes virtuálisgép-méretezési csoport felügyelt identitását, hogy hitelesítse magát a beállításjegyzékben, így nincs szükség a felhasználói hitelesítő adatok kezeléséhez.  További információ: [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) az MSI-ről.  A szolgáltatás használatához a következő lépések szükségesek:

1.  Győződjön meg arról, hogy engedélyezve van-e a rendszerhez rendelt felügyelt identitás a virtuális géphez (lásd az alábbi képernyőképet)

    ![Virtuálisgép-méretezési csoport identitásának létrehozása](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Ezután engedélyeket adhat a virtuális géphez (SS) a lemezképek lekéréséhez/olvasásához a beállításjegyzékből.  Nyissa meg az ACR Access Control (IAM) elemét az Azure panelen, és adja meg a virtuális gép (SS) számára a megfelelő engedélyeket, ahogy az alábbi képen is látható:

    ![VM-tag hozzáadása az ACR-hez](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  A fenti lépések elvégzése után módosítsa a applicationmanifest. xml fájlt.  Keresse meg a "ContainerHostPolicies" címkével ellátott címkét, és adja hozzá a `‘UseTokenAuthenticationCredentials=”true”`attribútumot.

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
    > A jelző `UseDefaultRepositoryCredentials` True (igaz) értékre van állítva, `UseTokenAuthenticationCredentials` igaz értéke esetén a rendszer hibát okoz az üzembe helyezés során.

## <a name="next-steps"></a>Következő lépések

* További információ a [tároló-beállításjegyzék hitelesítéséről](/azure/container-registry/container-registry-authentication).
