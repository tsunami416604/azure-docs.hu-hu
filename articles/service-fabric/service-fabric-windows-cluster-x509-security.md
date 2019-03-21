---
title: Az Azure Service Fabric-fürt, a Windows biztonságos tanúsítványok használatával |} A Microsoft Docs
description: Egy Azure Service Fabric önálló vagy a helyi fürtön belül, valamint az ügyfelek és a fürt közötti biztonságos kommunikációhoz.
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
ms.openlocfilehash: 17dd2a8cf58066fda7f82ba53b048df8e9b89da8
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285244"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Windows különálló fürt védelme X.509-tanúsítványok használatával
Ez a cikk ismerteti a különböző csomópontokhoz, a különálló Windows-fürt közötti kommunikáció biztonságossá tételére. Emellett bemutatja, hogyan lehet X.509 tanúsítványok segítségével csatlakozzon a fürt azon ügyfelek hitelesítéséhez. Hitelesítés biztosítja, hogy csak a jogosult felhasználók a fürt és a központilag telepített alkalmazások elérésére, és végrehajthat felügyeleti feladatokat. Tanúsítvány biztonsági szabad engedélyezni a fürtön, a fürt létrehozásakor.  

Fürtbiztonság, például a csomópontok közötti biztonsági, ügyfél-csomópont biztonsági és szerepköralapú hozzáférés-vezérlés, a további információk: [fürtök – biztonsági helyzetek](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Mely tanúsítványokat van szüksége?
Első lépésként [a Service Fabric Windows Server csomag](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) a fürtben található csomópontok egyikére. A letöltött csomagban ClusterConfig.X509.MultiMachine.json fájl található. Nyissa meg a fájlt, és tekintse át a szakasz a biztonság a Tulajdonságok szakaszában:

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
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
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

Ez a szakasz ismerteti a tanúsítványok, a különálló Windows-fürt biztonságossá tétele kell. Ha megad egy fürttanúsítványt, állítsa a ClusterCredentialType _X509_. Ha megad egy külső kapcsolatok kiszolgálói tanúsítványának, állítsa a ServerCredentialType _X509_. Bár nem kötelező, azt javasoljuk, hogy megfelelően biztonságos fürt mindkét rendelkezik. Ha ezeket az értékeket *X509*, akkor is meg kell adnia a megfelelő tanúsítványait, vagy a Service Fabric kivételt jelez. Bizonyos esetekben érdemes, csak adja meg a _ClientCertificateThumbprints_ vagy a _ReverseProxyCertificate_. Adott esetben nem kell beállítani _ClusterCredentialType_ vagy _ServerCredentialType_ való _X509_.


> [!NOTE]
> A [ujjlenyomat](https://en.wikipedia.org/wiki/Public_key_fingerprint) az elsődleges identitás-tanúsítvány. A létrehozott tanúsítványok ujjlenyomatát, lásd: [a tanúsítvány ujjlenyomatának lekérését](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

Az alábbi táblázat a tanúsítványok, a fürt beállítása a van szüksége:

| **CertificateInformation beállítás** | **Leírás** |
| --- | --- |
| ClusterCertificate |Egy tesztkörnyezetben ajánlott. Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére. Két különböző tanúsítványok, az elsődleges és a egy másodlagos frissítéshez használható. Az ujjlenyomat szakaszban, és hogy a másodlagos ThumbprintSecondary változók be az elsődleges tanúsítvány ujjlenyomatát. |
| ClusterCertificateCommonNames |Éles környezetben ajánlott. Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére. Egy vagy két fürt közös tanúsítvány-nevet is használhat. Ez a tanúsítvány kibocsátójának ujjlenyomata a CertificateIssuerThumbprint felel meg. Ha egynél több tanúsítvány köznapi neve megegyezik a használ, több kibocsátójának ujjlenyomatai is megadhat.|
| ClusterCertificateIssuerStores |Éles környezetben ajánlott. Ez a tanúsítvány kiállítója és a fürt tanúsítvány felel meg. Megadhatja a kibocsátó köznapi név és a megfelelő Tárolónév alatt ez a szakasz a kibocsátó ujjlenyomatát alatt ClusterCertificateCommonNames megadása helyett.  Ez megkönnyíti helyettesítő fürt kiállítói tanúsítványokat. Ha egynél több fürt tanúsítványt használja több kiállítók adható meg. Egy üres IssuerCommonName listáinak X509StoreNames alatt megadott összes tanúsítvány a megfelelő áruházakból.|
| ServerCertificate |Egy tesztkörnyezetben ajánlott. Ezt a tanúsítványt az ügyfél számára való csatlakozás a fürthöz. Az egyszerűség kedvéért lehet váltani, ClusterCertificate és ServerCertificate ugyanazt a tanúsítványt használja. Két különböző kiszolgálói tanúsítványok, az elsődleges és a egy másodlagos frissítéshez használható. Az ujjlenyomat szakaszban, és hogy a másodlagos ThumbprintSecondary változók be az elsődleges tanúsítvány ujjlenyomatát. |
| ServerCertificateCommonNames |Éles környezetben ajánlott. Ezt a tanúsítványt az ügyfél számára való csatlakozás a fürthöz. Ez a tanúsítvány kibocsátójának ujjlenyomata a CertificateIssuerThumbprint felel meg. Ha egynél több tanúsítvány köznapi neve megegyezik a használ, több kibocsátójának ujjlenyomatai is megadhat. Az egyszerűség kedvéért lehet váltani, ClusterCertificateCommonNames és ServerCertificateCommonNames ugyanazt a tanúsítványt használja. Használhat egy vagy két kiszolgálótanúsítványok köznapi neve. |
| ServerCertificateIssuerStores |Éles környezetben ajánlott. Ezt a tanúsítványt a tanúsítvány kiállítója felel meg. Megadhatja a kibocsátó köznapi név és a megfelelő Tárolónév alatt ez a szakasz a kibocsátó ujjlenyomatát alatt ServerCertificateCommonNames megadása helyett.  Ez megkönnyíti helyettesítő kiszolgáló kiállítói tanúsítványokat. Több kiállítók is lehet. Ha egynél több tanúsítvány szerepel-e megadva. Egy üres IssuerCommonName listáinak X509StoreNames alatt megadott összes tanúsítvány a megfelelő áruházakból.|
| ClientCertificateThumbprints |Telepítse a tanúsítványok készletét a hitelesített ügyfelek. Számos különböző telepítve a gépeken engedélyezi a hozzáférést a fürthöz használni kívánt ügyféltanúsítványok rendelkezhet. Minden tanúsítvány ujjlenyomatát a CertificateThumbprint változó állítja be. Ha IsAdmin *igaz*, az ügyfél és a rajta telepített tanúsítvány hajthatja végre a rendszergazda felügyeleti tevékenységeket a fürtön. Ha a IsAdmin *hamis*, az ügyfél ezt a tanúsítványt és a felhasználói hozzáférés jogokkal, általában csak olvasható csak engedélyezett műveletek végezhetők. Szerepkörökkel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Az első ügyfél-tanúsítvány köznapi nevével a CertificateCommonName be. A CertificateIssuerThumbprint ezt a tanúsítványt kibocsátó ujjlenyomatát. Köznapi nevek és a kibocsátó kapcsolatos további tudnivalókért lásd: [tanúsítványok használata](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Éles környezetben ajánlott. Ez a tanúsítvány kiállítója és az ügyfél-tanúsítványt (felügyeleti és a nem rendszergazdai szerepkörök) felel meg. Megadhatja a kibocsátó köznapi név és a megfelelő Tárolónév alatt ez a szakasz a kibocsátó ujjlenyomatát alatt ClientCertificateCommonNames megadása helyett.  Ez megkönnyíti az helyettesítő ügyféltanúsítványokhoz kibocsátó. Több kiállítók lehet megadott, ha egynél több titkosítású ügyféltanúsítvány szükséges. Egy üres IssuerCommonName listáinak X509StoreNames alatt megadott összes tanúsítvány a megfelelő áruházakból.|
| ReverseProxyCertificate |Egy tesztkörnyezetben ajánlott. Ez nem kötelező a tanúsítvány lehet a megadott szeretné biztonságossá tenni a [fordított proxy](service-fabric-reverseproxy.md). Győződjön meg arról, hogy reverseProxyEndpointPort a NodeType van beállítva, ha ezt a tanúsítványt használja. |
| ReverseProxyCertificateCommonNames |Éles környezetben ajánlott. Ez nem kötelező a tanúsítvány lehet a megadott szeretné biztonságossá tenni a [fordított proxy](service-fabric-reverseproxy.md). Győződjön meg arról, hogy reverseProxyEndpointPort a NodeType van beállítva, ha ezt a tanúsítványt használja. |

Íme egy példa fürtkonfiguráció, ahol a fürt, a kiszolgáló és az ügyféltanúsítványok adtak meg. A fürt/kiszolgálói/reverseProxy tanúsítványok esetében az ujjlenyomatot, és a köznapi név nem lehet konfigurálni együtt ugyanazt a tanúsítványtípust.

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
        },
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

## <a name="certificate-rollover"></a>Tanúsítványváltás
Használatakor a tanúsítvány köznapi nevét ujjlenyomat helyett tanúsítványváltás fürt konfiguráció frissítése nem igényel. Kiállító ujjlenyomata frissítését győződjön meg arról, hogy az új ujjlenyomatot lista metszi a régi listájával. Először azt kell egy konfigurációs frissíthet az új kibocsátójának ujjlenyomatai együtt, és telepítse az új tanúsítványok (fürt/kiszolgáló tanúsítványa és kiállítói tanúsítványokat) a tárolóban. A régi kibocsátói tanúsítvány legalább két órán át, az új kibocsátói tanúsítvány telepítése után ne a tanúsítványtárolóban.
Ha kibocsátó tárolók, nincs konfiguráció frissítése szükséges végrehajtani kibocsátó váltását, majd használja. Az új kibocsátói tanúsítvány utóbbi lejárati dátummal a megfelelő tanúsítványtárolójába telepítette, és távolítsa el a régi kibocsátói tanúsítvány néhány óra múlva.

## <a name="acquire-the-x509-certificates"></a>Az X.509-tanúsítványok beszerzése
A fürtön belüli kommunikáció védelméhez, először a fürtcsomópontok X.509-tanúsítványokat szerezzenek be. Emellett korlátozni a jogosult felhasználók vagy gépek a fürthöz való csatlakozás, szüksége beszerzése és telepítése a tanúsítványokat az ügyfélgépekre.

Az éles számítási feladatokat futtató fürtök esetén használja a [hitelesítésszolgáltatói (CA)](https://en.wikipedia.org/wiki/Certificate_authority)-aláírva, a fürt védelme X.509-tanúsítvány. Ezek a tanúsítványok beszerzésével kapcsolatban további információkért lásd: [tanúsítvány beszerzése](https://msdn.microsoft.com/library/aa702761.aspx).

Tesztelési célokra használó fürtök választhat egy önaláírt tanúsítványt szeretne használni.

## <a name="optional-create-a-self-signed-certificate"></a>Nem kötelező: Önaláírt tanúsítvány létrehozása
Megfelelően védett önaláírt tanúsítvány létrehozásának egyik módja, hogy a Service Fabric SDK mappában a következő könyvtárban: C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure CertSetup.ps1 parancsfájllal. Szerkessze a fájlt, a tanúsítvány az alapértelmezett nevének módosításához. (Nézze meg CN = ServiceFabricDevClusterCert.) Futtassa ezt a szkriptet, `.\CertSetup.ps1 -Install`.

Most már exportálja a tanúsítványt egy .pfx fájlba jelszóval védett. Először kérje le a tanúsítvány ujjlenyomatát. 
1. Az a **Start** menüben futtatása **számítógép-tanúsítványok kezelése**. 

2. Nyissa meg a **helyi számítógép személyes tanúsítványtárolójában** mappát, és keresse meg a tanúsítványt hozott létre. 

3. Kattintson duplán a tanúsítványra megnyitásához, jelölje be a **részletek** lapra, és görgessen le a **ujjlenyomat** mező. 

4. Távolítsa el a szóközöket, és másolja az ujjlenyomat értékét az alábbi PowerShell-parancsot. 

5. Módosítsa a `String` védelmét, és futtassa a következő PowerShell megfelelő biztonságos jelszó érték:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. A gépen telepített tanúsítvány részleteinek megtekintéséhez futtassa a következő PowerShell-parancsot:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Azt is megteheti, ha rendelkezik Azure-előfizetéssel, kövesse a lépéseket a [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>A tanúsítványok telepítése
Miután tanúsítványok, akkor telepítse őket a fürtcsomópontokon. A csomópontok kell rendelkeznie a legújabb Windows PowerShell 3.x telepítve rajtuk. Ismételje meg ezeket a lépéseket minden egyes csomóponton fürt és a kiszolgálói tanúsítványokat és a másodlagos tanúsítványok.

1. A csomópontra másolja a .pfx-fájlt vagy fájlokat.

2. Nyissa meg rendszergazdaként egy PowerShell-ablakot, és a következő parancsokat. Cserélje le *$pswd* az a tanúsítvány létrehozásához használt jelszót. Cserélje le *$PfxFilePath* erre a csomópontra másolja a teljes elérési útját a .pfx.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Most állítsa be ezt a tanúsítványt a hozzáférés-vezérlés, hogy a Service Fabric-folyamat, amely a hálózati szolgáltatás fiók alatt fut, használhatja a következő szkript futtatásával. Adja meg a tanúsítvány ujjlenyomatának és **hálózati szolgáltatás** szolgáltatásfiók. Ellenőrizheti, hogy a tanúsítvány hozzáférés-vezérlési listák nyissa meg a tanúsítvány helyesen **Start** > **számítógép-tanúsítványok kezelése** és megnézzük **minden feladat**  >  **Titkos kulcsok kezelése**.
   
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
4. Ismételje meg az előző lépéseket minden egyes kiszolgálói tanúsítvány. Is használhatja ezeket a lépéseket az ügyféltanúsítványok telepítése a gépeken, amely engedélyezi a hozzáférést a fürthöz.

## <a name="create-the-secure-cluster"></a>A biztonságos fürt létrehozása
Miután konfigurálta a biztonság szakaszában tekintheti meg a ClusterConfig.X509.MultiMachine.json fájlt, folytassa a [a fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) szakasz a csomópontok beállítása és az önálló fürt létrehozása. Ne felejtse el a ClusterConfig.X509.MultiMachine.json fájlt használja, a fürt létrehozása közben. Például a parancs a következőhöz hasonlóan nézhet ki:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Miután a biztonságos önálló Windows, a fürt sikeresen fut, és beállítása a hitelesített ügyfelek csatlakozni hozzá, kövesse a szakasz a [csatlakozás a fürthöz PowerShell használatával](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) hozzá csatlakozni. Példa:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Ezt követően futtathatja más PowerShell-parancsok használata ehhez a fürthöz. Például futtathatja [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) a biztonságos fürtön lévő csomópontok listájának megjelenítéséhez.


A fürt eltávolításához a fürtön, amelybe letöltötte a Service Fabric-csomag a csomóponthoz csatlakozás, nyisson meg egy parancssort, és nyissa meg a csomag mappát. Most futtassa a következő parancsot:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nem megfelelő tanúsítvány konfigurálása megakadályozhatja, hogy a fürt üzembe helyezése során várható. Önálló diagnosztizálhatja a biztonsági problémákat, tekintse meg az eseménynaplóban megjelenítő csoport **alkalmazások és szolgáltatásnaplók** > **Microsoft-Service Fabric**.
> 
> 

