---
title: Azure Service Fabric – a Service Fabric Application kulcstartó-referenciák használata
description: Ez a cikk azt ismerteti, hogyan használható a Service-Fabric KeyVaultReference támogatása az alkalmazás titkos kulcsaihoz.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545483"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Service Fabric alkalmazások KeyVaultReference támogatása (előzetes verzió)

A felhőalapú alkalmazások készítése során gyakran előforduló kihívás az alkalmazás által igényelt titkok biztonságos tárolása. Előfordulhat például, hogy a tároló adattárának hitelesítő adatait szeretné tárolni a kulcstartóban, és hivatkozni rá az alkalmazás jegyzékfájljában. Service Fabric a KeyVaultReference Service Fabric felügyelt identitást használ, és megkönnyíti a kulcstartó-titkok hivatkozását. A cikk további részében részletesen ismertetjük a Service Fabric KeyVaultReference használatát, és néhány tipikus használatot tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

- Az alkalmazás felügyelt identitása (MIT)
    
    Service Fabric KeyVaultReference-támogatás az alkalmazás felügyelt identitását használja, ezért a KeyVaultReferences használatára tervezett alkalmazások felügyelt identitást használnak. Ezt a [dokumentumot](concepts-managed-identity.md) követve engedélyezheti az alkalmazás felügyelt identitását.

- Központi titkok tárolója (CSS).

    A központi titkok tárolója (CSS) Service Fabric titkosított helyi titkok gyorsítótára. A CSS egy helyi titkos tároló-gyorsítótár, amely a memóriában titkosított bizalmas adatokat, például jelszavakat, jogkivonatokat és kulcsokat tárol. A beolvasott KeyVaultReference a CSS-ben vannak gyorsítótárazva.

    Adja hozzá az alábbit a fürtkonfiguráció `fabricSettings` a KeyVaultReference támogatásához szükséges összes funkció engedélyezéséhez.

    ```json
    "fabricSettings": 
    [
        ...
    {
                "name":  "CentralSecretService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Javasoljuk, hogy használjon külön titkosítási tanúsítványt a CSS-hez. A "CentralSecretService" szakaszban adhatja hozzá.
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Ahhoz, hogy a módosítások életbe lépjenek, módosítania kell a frissítési házirendet, hogy az egyes csomópontokon a frissítés folyamata során az Service Fabric-futtatókörnyezet kényszerített újraindítását is megadja. Ez az újraindítás biztosítja, hogy az újonnan engedélyezett rendszerszolgáltatás elindult, és minden csomóponton fusson. Az alábbi kódrészletben a forceRestart az alapvető beállítás; a többi beállításhoz használja a meglévő értékeket.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Az alkalmazás felügyelt identitás-hozzáférési engedélyének megadása a kulcstartóhoz

    Ebből a [dokumentumból](how-to-grant-access-other-resources.md) megtudhatja, hogyan biztosíthatja a felügyelt identitások hozzáférését a kulcstartóhoz. Azt is vegye figyelembe, hogy ha rendszerhez rendelt felügyelt identitást használ, a felügyelt identitás csak az alkalmazás telepítése után jön létre.

## <a name="keyvault-secret-as-application-parameter"></a>Kulcstartó titkos kódja Application paraméter
Tegyük fel, hogy az alkalmazásnak a kulcstartóban tárolt háttér-adatbázis jelszavát kell beolvasnia, Service Fabric a KeyVaultReference-támogatás megkönnyíti a használatát. Az alábbi példa beolvassa `DBPassword` titkos kulcsot a kulcstartóból Service Fabric KeyVaultReference-támogatással.

- Szakasz hozzáadása a Settings. xml fájlhoz

    `DBPassword` paraméter megadása `KeyVaultReference` és érték típussal `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- A ApplicationManifest. xml fájl új szakaszának hivatkozása `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- KeyVaultReference használata az alkalmazásban

    A szolgáltatás-példányon Service Fabric a KeyVaultReference paramétert az alkalmazás felügyelt identitásával fogja feloldani. A `<Section  Name=dbsecrets>` alatt felsorolt paraméterek az EnvironmentVariable SecretPath által mutatott mappában lesznek. Az C# alábbi kódrészlet bemutatja, hogyan olvashatja el a DBPassword az alkalmazásban.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > A Container forgatókönyv esetén a csatlakoztatási pont segítségével szabályozhatja, hogy a `secrets` hová legyenek csatlakoztatva.

## <a name="keyvault-secret-as-environment-variable"></a>Kulcstartó titka környezeti változóként

Service Fabric környezeti változók mostantól támogatják a KeyVaultReference típusát, az alábbi példa bemutatja, hogyan köthető egy környezeti változó a kulcstartóban tárolt titkos kulcshoz.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Kulcstartó titka tároló-adattár jelszava
A KeyVaultReference egy támogatott típusú tároló-RepositoryCredentials, az alábbi példa azt szemlélteti, hogyan használható a kulcstároló-hivatkozás a tároló-adattár jelszavaként.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Gyakori kérdések
- A felügyelt identitást engedélyezni kell a KeyVaultReference-támogatáshoz, az alkalmazás aktiválása sikertelen lesz, ha a KeyVaultReference a felügyelt identitás engedélyezése nélkül használja.

- Ha rendszerhez rendelt identitást használ, az csak az alkalmazás telepítése után jön létre, és körkörös függőséget hoz létre. Az alkalmazás üzembe helyezését követően megadhatja a rendszerhez rendelt identitás-hozzáférési engedélyt a kulcstartóhoz. A rendszerhez rendelt identitást a {cluster}/{Application Name}/{servicename} találja.

- A kulcstartónak ugyanahhoz az előfizetéshez kell esnie, mint a Service Fabric-fürtnek. 

## <a name="next-steps"></a>Következő lépések

* [Azure kulcstartó – dokumentáció](https://docs.microsoft.com/azure/key-vault/)
