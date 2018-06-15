---
title: Egy Windows Azure Service Fabric-fürt biztonságos tanúsítványok használatával |} Microsoft Docs
description: Az Azure Service Fabric önálló vagy a helyi fürtön belül, valamint az ügyfelek és a fürt közötti kommunikáció védelméhez.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 62d821894521c5dea8e7577b75d9590adc829263
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212415"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Biztonságos Windows önálló fürtben, X.509-tanúsítványok
Ez a cikk ismerteti a különálló Windows fürt különböző csomópontok közötti kommunikáció biztonságossá tételére. Azt is bemutatja, hogyan hitelesítheti a fürt X.509-tanúsítványok használatával csatlakozó ügyfelek. Hitelesítési biztosítja, hogy az csak a hitelesített felhasználóknak a fürt és a központilag telepített alkalmazások elérése és felügyeleti feladatok elvégzésére. Tanúsítvány biztonsági engedélyezni kell a fürt a fürt létrehozásakor.  

További információ a fürt biztonsági-csomópontok biztonsági, az ügyfél-csomópont biztonsági és a szerepköralapú hozzáférés-vezérlés, például: [fürtök biztonsági forgatókönyveinek](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Tanúsítványok van szüksége?
Kezdő-és [a Service Fabric Windows Server csomag](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) a fürtben található csomópontok egyikére. A letöltött csomagban ClusterConfig.X509.MultiMachine.json fájl található. Nyissa meg a fájlt, és tekintse át a szakasz a biztonsági tulajdonságok szakaszban:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Ez a szakasz ismerteti a tanúsítványokat, amelyekre szüksége van a különálló Windows-fürt védelmét. A fürt tanúsítványt ad meg, ha a ClusterCredentialType értékének beállítása _X509_. Ha egy külső kapcsolatok kiszolgálói tanúsítványának ad meg, állítsa a ServerCredentialType _X509_. Bár nem kötelező, javasoljuk, hogy ezek a tanúsítványok megfelelően védett fürt. Ha ezeket az értékeket állít *X509*, is meg kell a megfelelő tanúsítványok, vagy a Service Fabric kivételt jelez. Bizonyos esetekben érdemes megadása csak a _ClientCertificateThumbprints_ vagy a _ReverseProxyCertificate_. Adott esetben nem kell beállítani _ClusterCredentialType_ vagy _ServerCredentialType_ való _X509_.


> [!NOTE]
> A [ujjlenyomat](https://en.wikipedia.org/wiki/Public_key_fingerprint) tanúsítvány elsődleges azonosítója. Az Ön által létrehozott tanúsítványok ujjlenyomatát regisztrációval, lásd: [beolvasni a tanúsítvány ujjlenyomata](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

A következő táblázat, amelyekre szüksége van a fürt beállítása a tanúsítványokat sorolja fel:

| **CertificateInformation beállítás** | **Leírás** |
| --- | --- |
| ClusterCertificate |Egy tesztkörnyezetben ajánlott. Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére. Két különböző tanúsítványok, egy elsődleges és másodlagos, használhatja a frissítésre. Állítsa be az elsődleges tanúsítvány ujjlenyomatát a ujjlenyomat szakaszban, valamint a másodlagos ThumbprintSecondary változók. |
| ClusterCertificateCommonNames |Az éles környezetben ajánlott. Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére. Egy vagy két fürt közös kiszolgálótanúsítvány-nevek is használhatja. Ez a tanúsítvány kibocsátójának ujjlenyomata a CertificateIssuerThumbprint felel meg. Ha a ugyanazzal a névvel több tanúsítványt használ, több kibocsátó ujjlenyomatok is megadhat.|
| ClusterCertificateIssuerStores |Az éles környezetben ajánlott. Ez a tanúsítvány a fürt tanúsítvány kiállítója megfelel-e. Megadhatja a kibocsátó köznapi név és a megfelelő Tárolónév alatt ez a szakasz a kibocsátójának ujjlenyomata alatt ClusterCertificateCommonNames megadása helyett.  Ez megkönnyíti helyettesítő fürt kiállítói tanúsítványokat. Ha egynél több fürt tanúsítvánnyal több kiállítók adható meg. Egy üres IssuerCommonName whitelists X509StoreNames alatt megadott minden tanúsítvány megfelelő tárolókat.|
| ServerCertificate |Egy tesztkörnyezetben ajánlott. Ezt a tanúsítványt az ügyfél áll rendelkezésre, ha csatlakozik a fürthöz. Kényelmi célokat szolgál Ha szeretné, egy tanúsítvány használható ClusterCertificate és ServerCertificate. Két különböző kiszolgálói tanúsítványok, egy elsődleges és másodlagos, használhatja a frissítésre. Állítsa be az elsődleges tanúsítvány ujjlenyomatát a ujjlenyomat szakaszban, valamint a másodlagos ThumbprintSecondary változók. |
| ServerCertificateCommonNames |Az éles környezetben ajánlott. Ezt a tanúsítványt az ügyfél áll rendelkezésre, ha csatlakozik a fürthöz. Ez a tanúsítvány kibocsátójának ujjlenyomata a CertificateIssuerThumbprint felel meg. Ha a ugyanazzal a névvel több tanúsítványt használ, több kibocsátó ujjlenyomatok is megadhat. Kényelmi célokat szolgál Ha szeretné, egy tanúsítvány használható ClusterCertificateCommonNames és ServerCertificateCommonNames. Használhat egy vagy két kiszolgálótanúsítványok köznapi neve. |
| ServerCertificateIssuerStores |Az éles környezetben ajánlott. Ez a tanúsítvány kiállítója és a kiszolgálói tanúsítvány felel meg. Megadhatja a kibocsátó köznapi név és a megfelelő Tárolónév alatt ez a szakasz a kibocsátójának ujjlenyomata alatt ServerCertificateCommonNames megadása helyett.  Ez megkönnyíti helyettesítő server kiállítói tanúsítványokat. Több kiállítók lehet. Ha egynél több tanúsítvány szerepel-e megadva. Egy üres IssuerCommonName whitelists X509StoreNames alatt megadott minden tanúsítvány megfelelő tárolókat.|
| ClientCertificateThumbprints |Telepítse a tanúsítványok készletét a hitelesített ügyfelek. Akkor segítségével számos különböző ügyfél-tanúsítványok engedélyezi a hozzáférést a fürthöz használni kívánt számítógépeken telepítve van. Állítsa be a minden tanúsítvány ujjlenyomatát a CertificateThumbprint változóban. Ha IsAdmin *igaz*, az ügyfél és a tanúsítvány telepítve van-e teheti rendszergazda felügyeleti tevékenységek, a fürtön. Ha IsAdmin *hamis*, az ügyfél és a tanúsítvány a felhasználó-hozzáférési jogok, általában csak olvasható esetén engedélyezett műveletek végezhetők. A szerepkörök további információkért lásd: [szerepköralapú hozzáférés-vezérlést (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Az első ügyfél tanúsítvány köznapi nevének beállítása a CertificateCommonName. A CertificateIssuerThumbprint Ez a tanúsítvány kiállítójának ujjlenyomata. Gyakori nevei és a kiállító kapcsolatos további információkért lásd: [tanúsítványok együttműködve](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Az éles környezetben ajánlott. Ez a tanúsítvány kiállítója és az ügyféltanúsítvány (admin és a nem rendszergazdai szerepkörök) felel meg. Megadhatja a kibocsátó köznapi név és a megfelelő Tárolónév alatt ez a szakasz a kibocsátójának ujjlenyomata alatt ClientCertificateCommonNames megadása helyett.  Ez megkönnyíti az átfordulási ügyféltanúsítványokhoz kibocsátó. Több kiállítók lehet meg, ha egynél több ügyfél tanúsítvánnyal. Egy üres IssuerCommonName whitelists X509StoreNames alatt megadott minden tanúsítvány megfelelő tárolókat.|
| ReverseProxyCertificate |Egy tesztkörnyezetben ajánlott. Ez a tanúsítvány nem kötelező lehet meg, hogy szeretné-e biztonságos a [fordított proxy](service-fabric-reverseproxy.md). Győződjön meg arról, hogy reverseProxyEndpointPort a NodeType tulajdonságok értéke van beállítva, ha a tanúsítvány használatához. |
| ReverseProxyCertificateCommonNames |Az éles környezetben ajánlott. Ez a tanúsítvány nem kötelező lehet meg, hogy szeretné-e biztonságos a [fordított proxy](service-fabric-reverseproxy.md). Győződjön meg arról, hogy reverseProxyEndpointPort a NodeType tulajdonságok értéke van beállítva, ha a tanúsítvány használatához. |

Íme egy példa fürtkonfiguráció, ahol a fürt, a kiszolgáló és az ügyféltanúsítványok vannak-e megadva. A fürt/server/reverseProxy tanúsítványok ujjlenyomatát és köznapi neve nem lehet konfigurálni együtt ugyanazt a tanúsítványtípust.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Tanúsítványok leváltása
Használatakor a tanúsítvány egyszerű neve helyett egy ujjlenyomatot tanúsítványváltást konfigurációs Fürtfrissítés nem igényel. Kiállítójának ujjlenyomata frissítések győződjön meg arról, hogy az új ujjlenyomatot lista metszi a régi listájával. Először be kell elvégezni a konfiguráció frissítése az új kibocsátó ujjlenyomatok a, és telepítse az új tanúsítványok (fürt/kiszolgálótanúsítvány és kiállítói tanúsítványokat) tárolójában. A tanúsítványtároló a régi kibocsátói tanúsítvány ne legalább két órán keresztül, az új kibocsátói tanúsítvány telepítése után.
Ha kibocsátó tárolókat használ, majd konfiguráció frissítése kell végezhető el a tanúsítványok leváltása kibocsátó. Az új kibocsátói tanúsítvány telepítése ez utóbbi lejárati dátummal megfelelő tanúsítványtárolójába, és távolítsa el a régi kibocsátói tanúsítvány néhány óra múlva.

## <a name="acquire-the-x509-certificates"></a>Szerezzen be X.509-tanúsítványokat
A fürtön belüli kommunikáció védelméhez, először a fürtcsomópontok esetén, X.509-tanúsítványokat szerezzenek be. Továbbá a jogosult felhasználók vagy gépek fürthöz kapcsolat korlátozásához szüksége beszerzése és az ügyfél gépek tanúsítványok telepítése.

A termelési számítási feladatokhoz rendszert futtató fürtöket, használjon egy [hitelesítésszolgáltató-tanúsítvány](https://en.wikipedia.org/wiki/Certificate_authority)-védelméhez a fürt X.509 tanúsítvány aláírására használatos. Ezek a tanúsítványok beszerzéséről további információkért lásd: [tanúsítvány beszerzése](http://msdn.microsoft.com/library/aa702761.aspx).

Tesztelési célokra használó fürtök esetén dönthet úgy, önaláírt tanúsítvány használatára.

## <a name="optional-create-a-self-signed-certificate"></a>Választható lehetőség: Hozzon létre egy önaláírt tanúsítványt
Egy hozzon létre egy önaláírt tanúsítványt megfelelően védett módja a CertSetup.ps1 parancsfájl használata a következő könyvtárban: C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure a Service Fabric SDK mappában. Ez a tanúsítvány az alapértelmezett nevének módosításához fájl szerkesztésével. (Keresse meg az értéket, CN = ServiceFabricDevClusterCert.) Futtassa ezt a parancsfájlt, `.\CertSetup.ps1 -Install`.

Védett jelszóval egy .pfx fájlba exportálja a tanúsítványt. Első lépésként beolvasása a tanúsítvány ujjlenyomatát. 
1. Az a **Start** menü futtatása **számítógép-tanúsítványok kezelése**. 

2. Lépjen a **helyi számítógép személyes** mappa, és megtekintheti a tanúsítvány létrehozott. 

3. Kattintson duplán a tanúsítványra megnyitásához, jelölje be a **részletek** lapot, és görgessen le a **ujjlenyomat** mező. 

4. Távolítsa el a szóközöket, és az ujjlenyomat értékét másolja a következő PowerShell-parancsot. 

5. Módosítsa a `String` védelmét, és futtassa a következő PowerShell megfelelő biztonságos jelszó értéket:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. A számítógépen telepített tanúsítvány részleteinek megtekintéséhez futtassa a következő PowerShell-parancsot:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Azt is megteheti, ha Azure-előfizetéssel rendelkezik, kövesse a lépéseket [a Service Fabric-fürt létrehozása az Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>A tanúsítványok telepítése
Miután tanúsítványok, telepítheti azokat a fürtcsomópontokon. A csomópontok kell rendelkeznie a legújabb Windows PowerShell 3.x rajtuk. Ismételje meg ezeket a lépéseket minden egyes csomóponton fürt és a kiszolgálói tanúsítványokat és a másodlagos tanúsítványok.

1. A csomópont a .pfx fájl vagy fájlokat másolja.

2. Nyissa meg rendszergazdaként a PowerShell ablakot, és adja meg a következő parancsok. Cserélje le *$pswd* a, melyek a tanúsítvány létrehozásához használt jelszó. Cserélje le *$PfxFilePath* a teljes elérési útját a .pfx másolja ezt a csomópontot.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Most beállítása az ezt a tanúsítványt, hogy a Service Fabric folyamat, amely a hálózati szolgáltatás fiók alatt fut, használhatja a következő parancsfájl futtatásával. Adja meg a tanúsítvány ujjlenyomatának és **hálózati szolgáltatás** a szolgáltatási fiókhoz. Ellenőrizheti, hogy a tanúsítvány az ACL-ek nyissa meg a tanúsítvány helyesen **Start** > **számítógép-tanúsítványok kezelése** és megnézi **feladataival**  >  **Titkos kulcsok kezelése**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Ismételje az előző lépést minden egyes kiszolgáló-tanúsítvány. Is használhatja ezeket a lépéseket a gépeken, a fürt eléréséhez használni kívánt ügyféltanúsítványok telepítéséhez.

## <a name="create-the-secure-cluster"></a>A biztonságos fürt létrehozása
Miután konfigurálta a ClusterConfig.X509.MultiMachine.json fájl biztonsági részét, folytathatja a [a fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) szakasz a csomópontok, és hozza létre az önálló fürtöt. Fontos, hogy a ClusterConfig.X509.MultiMachine.json fájlt a fürt létrehozása során. Például a parancs nézhet ki például a következőket:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Miután a biztonságos önálló Windows sikeresen fut a fürt és a csatlakozáshoz a hitelesített ügyfelek beállítását, kövesse a szakasz a [Connect PowerShell-lel fürtre](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) a csatlakozáshoz. Példa:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Ezt követően futtathatja más PowerShell-parancsok használata ehhez a fürthöz. Például futtathatja [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) való megjelenítéséhez a biztonságos fürt a csomópontok listáját.


Távolítsa el a fürtöt, a csomópont a fürt, amelybe letöltötte a Service Fabric-csomag csatlakozzon, nyisson meg egy parancssort, és a csomag mappában. Most futtassa a következő parancsot:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Tanúsítvány nem megfelelő konfigurációs megakadályozhatja, hogy a fürt üzembe helyezése során várható. Önálló biztonsági problémák elemzéséhez, keresse meg az esemény Viewer csoport **alkalmazási és Szolgáltatásnaplójában** > **Microsoft-Service Fabric**.
> 
> 

