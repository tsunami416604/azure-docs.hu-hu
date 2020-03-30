---
title: Azure Service Fabric – A Service Fabric alkalmazás KeyVault-hivatkozásainak használata
description: Ez a cikk bemutatja, hogyan használhatja a service-fabric KeyVaultReference támogatja az alkalmazás titkos kulcsokat.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545483"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference támogatás Service Fabric-alkalmazásokhoz (előzetes verzió)

A felhőalapú alkalmazások létrehozása kor gyakori kihívás, hogyan biztonságosan tárolja az alkalmazás által igényelt titkos kulcsokat. Például érdemes lehet a tárolótár hitelesítő adatait keyvaultban, és hivatkozik az alkalmazás jegyzékfájlban. A Service Fabric KeyVaultReference a Service Fabric felügyelt identitását használja, és megkönnyíti a keyvault titkos kulcsainak hivatkozását. A cikk további részletezi, hogyan kell használni a Service Fabric KeyVaultReference, és tartalmaz néhány tipikus használat.

## <a name="prerequisites"></a>Előfeltételek

- Felügyelt identitás alkalmazáshoz (MIT)
    
    A Service Fabric KeyVaultReference támogatása az alkalmazás felügyelt identitását használja, ezért a KeyVaultReferences használatára planaktív alkalmazásoknak felügyelt identitást kell használniuk. Kövesse ezt a [dokumentumot](concepts-managed-identity.md) az alkalmazás felügyelt identitásának engedélyezéséhez.

- Központi Titkok Store (CSS).

    A Central Secrets Store (CSS) a Service Fabric titkosított helyi titkos kulcsok gyorsítótára. A CSS egy helyi titkos tároló gyorsítótár, amely bizalmas adatokat, például jelszót, jogkivonatokat és kulcsokat titkosítva tárol a memóriában. KeyVaultReference, miután lehívása, a gyorsítótárba CSS.

    Adja hozzá az alábbi `fabricSettings` a fürt konfigurációja alatt, hogy az összes szükséges funkciók KeyVaultReference támogatás.

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
    > Javasoljuk, hogy külön titkosítási tanúsítványt használjon a CSS-hez. Hozzáadhatja a "CentralSecretService" szakaszban.
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Ahhoz, hogy a módosítások érvénybe lépjenek, a frissítési szabályzatot is módosítania kell, hogy a fürtön keresztül a frissítés előrehaladtával a Service Fabric futásidejű állapotának erőteljes újraindítását adja meg. Ez az újraindítás biztosítja, hogy az újonnan engedélyezett rendszerszolgáltatás elindul, és fut az egyes csomópontokon. Az alábbi kódrészletben a forceRestart az alapvető beállítás; a beállítások hátralévő részében használja a meglévő értékeket.
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
- Az alkalmazás felügyelt identitás-hozzáférési engedélyének megadása a keyvault-hoz

    Hivatkozzon erre a [dokumentumra,](how-to-grant-access-other-resources.md) hogy hogyan adhat felügyelt identitás-hozzáférést a keyvault.Reference this document to see how to grant managed identity access to keyvault. Azt is vegye figyelembe, ha a rendszer hozzárendelt felügyelt identitás, a felügyelt identitás jön létre csak az alkalmazás telepítése után.

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault titkos kulcsa alkalmazásparaméterként
Tegyük fel, hogy az alkalmazásnak el kell olvasnia a háttér-adatbázis keyvaultban tárolt jelszavát, a Service Fabric KeyVaultReference támogatása megkönnyíti. Az alábbi `DBPassword` példa beolvassa a keyvault titkos service fabric keyvaultreference támogatás használatával.

- Szakasz hozzáadása a settings.xml fájlhoz

    Paraméter `DBPassword` definiálása típussal `KeyVaultReference` és értékkel`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Hivatkozás az ApplicationManifest.xml új szakaszára a következőben:`<ConfigPackagePolicies>`

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

- A KeyVaultReference használata az alkalmazásban

    A service Fabric szolgáltatás példányosítása feloldja a KeyVaultReference paramétert az alkalmazás felügyelt identitásának használatával. Az alatt `<Section  Name=dbsecrets>` felsorolt paraméterek egy fájlleszak az EnvironmentVariable SecretPath által mutatott mappában. A C# kódrészlet alatt mutatják be, hogyan kell olvasni a DBPassword-t az alkalmazásban.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > A tároló forgatókönyv, használhatja a MountPoint `secrets` szabályozhatja, ahol a lesz csatlakoztatva.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault titkos környezeti változóként

A Service Fabric környezeti változói mostantól támogatják a KeyVaultReference típust, az alábbi példa bemutatja, hogyan köthető egy környezeti változó a KeyVault-ban tárolt titkos kulcshoz.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault titkos tárolótár jelszóként
KeyVaultReference egy támogatott típusú tároló repositoryCredentials, az alábbi példa bemutatja, hogyan kell használni a keyvault hivatkozás tárolótár jelszóként.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>GYIK
- Felügyelt identitás engedélyezni kell a KeyVaultReference támogatás, az alkalmazás aktiválása sikertelen lesz, ha KeyVaultReference a felügyelt identitás engedélyezése nélkül használja.

- Ha rendszer hozzárendelt identitást használ, az csak az alkalmazás üzembe helyezése után jön létre, és ez körkörös függőséget hoz létre. Az alkalmazás üzembe helyezése után a rendszer hozzárendelt identitás-hozzáférési engedélyt adhat a keyvault. A rendszer hozzárendelt identitását {cluster}/{application name}/{servicename} név alapján találhatja meg.

- A keyvault kell ugyanabban az előfizetésben, mint a szolgáltatás-háló fürt. 

## <a name="next-steps"></a>További lépések

* [Az Azure KeyVault dokumentációja](https://docs.microsoft.com/azure/key-vault/)
