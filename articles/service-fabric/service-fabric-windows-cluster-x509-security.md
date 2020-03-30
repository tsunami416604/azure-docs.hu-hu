---
title: Fürt biztonságossá tétele a Windows rendszeren tanúsítványok használatával
description: Biztonságos kommunikáció egy Azure Service Fabric önálló vagy helyszíni fürtön belül, valamint az ügyfelek és a fürt között.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75613924"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Önálló fürt biztonságossá tétele Windows rendszeren X.509-tanúsítványokkal
Ez a cikk azt ismerteti, hogy miként biztosíthatja az önálló Windows-fürt különböző csomópontjai közötti kommunikációt. Azt is ismerteti, hogyan hitelesítheti a fürthöz x.509-es tanúsítványokkal csatlakozó ügyfeleket. A hitelesítés biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és az üzembe helyezett alkalmazásokhoz, és felügyeleti feladatokat hajthassanak végre. A tanúsítványbiztonságot engedélyezni kell a fürtön a fürt létrehozásakor.  

A fürtbiztonságról, például a csomópont-csomópont biztonságról, az ügyfél-csomópont biztonságról és a szerepköralapú hozzáférés-vezérlésről a [Fürtbiztonsági forgatókönyvek](service-fabric-cluster-security.md)című témakörben talál további információt.

## <a name="which-certificates-do-you-need"></a>Milyen tanúsítványokra van szüksége?
Először [is töltse le a Windows Server Service Fabric csomagot](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) a fürt egyik csomópontjára. A letöltött csomagban található egy ClusterConfig.X509.MultiMachine.json fájl. Nyissa meg a fájlt, és tekintse át a szakaszbiztonsági szakaszt a tulajdonságok szakaszban:

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

Ez a szakasz az önálló Windows-fürt védelméhez szükséges tanúsítványokat ismerteti. Ha fürttanúsítványt ad meg, állítsa a ClusterCredentialType értékét _X509_értékre. Ha külső kapcsolatokhoz kiszolgálói tanúsítványt ad meg, állítsa a ServerCredentialType típust _X509-re_. Bár nem kötelező, azt javasoljuk, hogy mindkét tanúsítványt egy megfelelően biztonságos fürthöz. Ha ezeket az értékeket *X509*értékre állítja, akkor meg kell adnia a megfelelő tanúsítványokat is, különben a Service Fabric kivételt okoz. Bizonyos esetekben előfordulhat, hogy csak a _ClientCertificateujjlenyomatokat_ vagy a _ReverseProxyCertificate -et_szeretné megadni. Ezekben az esetekben nem kell a _ClusterCredentialType_ vagy _a ServerCredentialType típust_ _X509-re_állítania.


> [!NOTE]
> Az [ujjlenyomat](https://en.wikipedia.org/wiki/Public_key_fingerprint) a tanúsítvány elsődleges identitása. A létrehozott tanúsítványok ujjlenyomatának megkereséséről a [Tanúsítvány ujjlenyomatának beolvasása](https://msdn.microsoft.com/library/ms734695.aspx)című témakörben talál.
> 
> 

Az alábbi táblázat felsorolja a fürt beállításához szükséges tanúsítványokat:

| **Tanúsítványinformáció beállítása** | **Leírás** |
| --- | --- |
| Fürttanúsítvány |Tesztkörnyezethez ajánlott. Ez a tanúsítvány a fürt csomópontjai közötti kommunikáció biztosításához szükséges. A frissítéshez két különböző tanúsítványt, egy elsődleges és egy másodlagos tanúsítványt használhat. Állítsa be az alaptanúsítvány ujjlenyomatát az Ujjlenyomat részben, a másodlagost pedig a ThumbprintSecondary változókban. |
| ClusterCertificateCommonNames |Éles környezethez ajánlott. Ez a tanúsítvány a fürt csomópontjai közötti kommunikáció biztosításához szükséges. Egy vagy két fürttanúsítvány közös nevét használhatja. A CertificateIssuerThumbprint a tanúsítvány kibocsátójának ujjlenyomatának felel meg. Ha egynél több azonos nevű tanúsítványt használ, több kiállító ujjlenyomatát is megadhatja.|
| ClusterCertificateIssuerStores |Éles környezethez ajánlott. Ez a tanúsítvány a fürttanúsítvány kibocsátójának felel meg. Ebben a szakaszban megadhatja a kiállító köznapi nevét és a megfelelő üzletnevet, és nem adja meg a kiállító ujjlenyomatát a ClusterCertificateCommonNames csoportban.  Ez megkönnyíti a fürt kiállítói tanúsítványainak gördülékenysé letétet. Több kibocsátó is megadható, ha egynél több fürttanúsítványt használ. Egy üres IssuerCommonName whitelists minden tanúsítványt a megfelelő tárolja meghatározott X509StoreNames.|
| Kiszolgálótanúsítvány |Tesztkörnyezethez ajánlott. Ez a tanúsítvány akkor jelenik meg az ügyfél számára, amikor megpróbál csatlakozni ehhez a fürthöz. Az egyszerűség kedvéért használhatja ugyanazt a tanúsítványt a ClusterCertificate és a ServerCertificate számára. A frissítéshez két különböző kiszolgálói tanúsítványt, egy elsődleges és egy másodlagos tanúsítványt használhat. Állítsa be az alaptanúsítvány ujjlenyomatát az Ujjlenyomat részben, a másodlagost pedig a ThumbprintSecondary változókban. |
| ServerCertificateCommonNames |Éles környezethez ajánlott. Ez a tanúsítvány akkor jelenik meg az ügyfél számára, amikor megpróbál csatlakozni ehhez a fürthöz. A CertificateIssuerThumbprint a tanúsítvány kibocsátójának ujjlenyomatának felel meg. Ha egynél több azonos nevű tanúsítványt használ, több kiállító ujjlenyomatát is megadhatja. Az egyszerűség kedvéért használhatja ugyanazt a tanúsítványt a ClusterCertificateCommonNames és a ServerCertificateCommonNames esetében. Egy vagy két kiszolgálótanúsítvány közös nevét használhatja. |
| ServerCertificateIssuerStores |Éles környezethez ajánlott. Ez a tanúsítvány a kiszolgálói tanúsítvány kibocsátójának felel meg. Ebben a szakaszban megadhatja a kiállító köznapi nevét és a megfelelő üzletnevet, és nem adja meg a kiállító ujjlenyomatát a ServerCertificateCommonNames csoportban.  Ez megkönnyíti a kiszolgáló kiállítói tanúsítványainak gördülékenysé letétbe. Több kibocsátó is megadható, ha egynél több kiszolgálói tanúsítványt használ. Egy üres IssuerCommonName whitelists minden tanúsítványt a megfelelő tárolja meghatározott X509StoreNames.|
| ClientCertificateUjjlenyomatok |Telepítse ezt a tanúsítványkészletet a hitelesített ügyfelekre. Számos különböző ügyféltanúsítvány tvethet a fürthöz való hozzáférést engedélyezni kívánt gépekre. Állítsa be az egyes tanúsítványok ujjlenyomatát a CertificateThumbprint változóban. Ha az IsAdmin értéke *igaz,* az ügyfél, amelyen ez a tanúsítvány telepítve van, rendszergazdai felügyeleti tevékenységeket végezhet a fürtön. Ha az IsAdmin *hamis,* a tanúsítvánnyal rendelkező ügyfél csak a felhasználói hozzáférési jogokhoz engedélyezett műveleteket hajthatvégre, általában írásvédett. A szerepkörökről további információt a [Szerepköralapú hozzáférés-vezérlés (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac)című témakörben talál. |
| ClientCertificateCommonNames |Állítsa be a CertificateCommonName első ügyféltanúsítványának köznapi nevét. A CertificateIssuerThumbprint a tanúsítvány kibocsátójának ujjlenyomata. A gyakori nevekről és a kibocsátóról a Tanúsítványok munka című témakörben olvashat [bővebben.](https://msdn.microsoft.com/library/ms731899.aspx) |
| ClientCertificateIssuerStores |Éles környezethez ajánlott. Ez a tanúsítvány megfelel az ügyféltanúsítvány kibocsátójának (rendszergazdai és nem rendszergazdai szerepkörök). Ebben a szakaszban megadhatja a kiállító köznapi nevét és a megfelelő üzletnevet, és nem adja meg a kiállító ujjlenyomatát a ClientCertificateCommonNames csoportban.  Ez megkönnyíti az ügyfélkiállítói tanúsítványok átütemezését. Több kibocsátó is megadható, ha egynél több ügyféltanúsítványt használ. Egy üres IssuerCommonName whitelists minden tanúsítványt a megfelelő tárolja meghatározott X509StoreNames.|
| ReverseProxyCertificate (Fordított proxytanúsítvány) |Tesztkörnyezethez ajánlott. Ez a választható tanúsítvány akkor adható meg, ha biztosítani szeretné a [fordított proxyt.](service-fabric-reverseproxy.md) Ha ezt a tanúsítványt használja, győződjön meg arról, hogy a reverseProxyEndpointPort be van állítva a nodeTypes típusokban. |
| ReverseProxyCertificateCommonNames |Éles környezethez ajánlott. Ez a választható tanúsítvány akkor adható meg, ha biztosítani szeretné a [fordított proxyt.](service-fabric-reverseproxy.md) Ha ezt a tanúsítványt használja, győződjön meg arról, hogy a reverseProxyEndpointPort be van állítva a nodeTypes típusokban. |

Íme egy példa a fürtkonfigurációra, ahol a fürt, a kiszolgáló és az ügyfél tanúsítványok at adták meg. Fürt/kiszolgáló/reverseProxy tanúsítványok esetén az ujjlenyomat és a köznapi név nem konfigurálható együtt ugyanarra a tanúsítványtípusra.

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

## <a name="certificate-rollover"></a>Tanúsítvány váltása
Ha ujjlenyomat helyett tanúsítványnév használata, a tanúsítványváltáshoz nincs szükség fürtkonfigurációs frissítésre. A kibocsátó ujjlenyomat-frissítésesetén győződjön meg arról, hogy az új ujjlenyomatlista metszi a régi listát. Először egy konfigurációs frissítést kell végeznie az új kiállító ujjlenyomataival, majd telepítenie kell az új tanúsítványokat (fürt/kiszolgáló tanúsítványa és kiállítói tanúsítványai) a tárolóba. A régi kiállítói tanúsítványt az új kiállítói tanúsítvány telepítését követően legalább két órán keresztül tartsa a tanúsítványtárolóban.
Ha kiállítói tárolókat használ, akkor a kiállítói tanúsítványváltáshoz nem kell konfigurációs frissítést végrehajtani. Telepítse az új kiállítói tanúsítványt, amelynek lejárati dátuma utóbbi a megfelelő tanúsítványtárolóban, és néhány óra múlva távolítsa el a régi kiállítói tanúsítványt.

## <a name="acquire-the-x509-certificates"></a>Az X.509 tanúsítványok megszerzése
A fürtön belüli kommunikáció biztonságossá tétele érdekében először be kell szereznie a fürtcsomópontok X.509 tanúsítványait. Ezenkívül a fürthöz való csatlakozás engedélyezett gépekre/felhasználókra való korlátozásához be kell szereznie és telepítenie kell az ügyfélgépek tanúsítványait.

Az éles számítási feladatokat futtató fürtök esetében használjon [hitelesítésszolgáltatót (CA)](https://en.wikipedia.org/wiki/Certificate_authority)-aláírással elévülve x.509-es tanúsítvánnyal a fürt védelméhez. A tanúsítványok beszerzéséről a Tanúsítvány [beszerzése című](https://msdn.microsoft.com/library/aa702761.aspx)témakörben talál további információt.

A tesztelési célokra használt fürtökön dönthet önaláírt tanúsítvány használatára.

## <a name="optional-create-a-self-signed-certificate"></a>Nem kötelező: Önaláírt tanúsítvány létrehozása
A megfelelően biztonságos önaláírt tanúsítványok létrehozásának egyik módja a CertSetup.ps1 parancsfájl használata a Service Fabric SDK mappában a C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure könyvtárban. A tanúsítvány alapértelmezett nevének módosításához módosítsa ezt a fájlt. (Keresse meg a CN=ServiceFabricDevClusterCert értéket.) Futtassa `.\CertSetup.ps1 -Install`ezt a parancsfájlt .

Most exportálja a tanúsítványt egy .pfx fájlba védett jelszóval. Először is, szerezd meg a tanúsítvány ujjlenyomatát. 
1. A **Start** menüben **futtassa a Számítógép-tanúsítványok kezelése parancsot.** 

2. Nyissa meg a **Helyi számítógép\Személyes** mappát, és keresse meg a létrehozott tanúsítványt. 

3. Dupla kattintással nyissa meg a tanúsítványt, válassza a **Részletek** lapot, és görgessen le az **Ujjlenyomat** mezőig. 

4. Távolítsa el a szóközöket, és másolja a hüvelykujj-értéket a következő PowerShell-parancsba. 

5. Módosítsa `String` az értéket megfelelő biztonságos jelszóra a védelem érdekében, és futtassa a következőket a PowerShellben:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. A számítógépre telepített tanúsítvány részleteinek megtekintéséhez futtassa a következő PowerShell-parancsot:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Ha Azure-előfizetéssel rendelkezik, kövesse a [Service Fabric-fürt létrehozása](service-fabric-cluster-creation-via-arm.md)az Azure Resource Manager használatával című részben leírt lépéseket.

## <a name="install-the-certificates"></a>A tanúsítványok telepítése
Miután rendelkezik a tanúsítványokkal, telepítheti őket a fürtcsomópontokra. A csomópontoknak a legújabb Windows PowerShell 3.x-et kell telepíteniük. Ismételje meg ezeket a lépéseket az egyes csomópontokon a fürt- és kiszolgálótanúsítványok, valamint a másodlagos tanúsítványok esetében is.

1. Másolja a .pfx fájlt vagy fájlokat a csomópontra.

2. Nyisson meg egy PowerShell-ablakot rendszergazdaként, és írja be a következő parancsokat. Cserélje le *$pswd* a tanúsítvány létrehozásához használt jelszóra. Cserélje le *$PfxFilePath* a csomópontra másolt .pfx teljes elérési útjára.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Most állítsa be a hozzáférés-vezérlést ezen a tanúsítványon, hogy a Service Fabric folyamat, amely fut a Hálózati szolgáltatás fiók, használhatja a következő parancsfájl futtatásával. Adja meg a tanúsítvány és a **HÁLÓZATI SZOLGÁLTATÁS** ujjlenyomatát a szolgáltatásfiókhoz. A tanúsítványon található ACL-k helyességét a Tanúsítvány **kezelés** > **megkezdése számítógép-tanúsítványok** ban való megnyitásával és az **Összes feladat** > **a személyes kulcsok kezelése című**dokumentumban lévő dokumentumban ellenőrizheti.
   
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
4. Ismételje meg az előző lépéseket minden kiszolgálói tanúsítványesetében. Ezekkel a lépésekkel telepítheti az ügyféltanúsítványokat azon gépekre, amelyeken engedélyezni szeretné a fürthöz való hozzáférést.

## <a name="create-the-secure-cluster"></a>A biztonságos fürt létrehozása
A ClusterConfig.X509.MultiMachine.json fájl biztonsági szakaszának konfigurálása után folytassa [a Fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) szakaszban a csomópontok konfigurálásához és az önálló fürt létrehozásához. Ne felejtse el használni a ClusterConfig.X509.MultiMachine.json fájlt a fürt létrehozása kor. A parancs például a következőkre néz ki:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Miután a biztonságos önálló Windows-fürt sikeresen futott, és beállította a hitelesített ügyfelek et, hogy csatlakozzanak hozzá, kövesse a [Csatlakozás fürthöz a PowerShell használatával](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) a powershell használatával a csatlakozáslépése című szakaszlépéseit. Példa:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Ezután más PowerShell-parancsok futtatásával dolgozhat a fürthöz. Futtathatja például a [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) szolgáltatást a biztonságos fürt csomópontjainak listájának megjelenítéséhez.


A fürt eltávolításához csatlakozzon annak a fürtnek a csomópontjához, amelyen letöltötte a Service Fabric csomagot, nyisson meg egy parancssort, és lépjen a csomagmappába. Most futtassa a következő parancsot:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> A helytelen tanúsítványkonfiguráció megakadályozhatja, hogy a fürt a telepítés során feljöjjön. A biztonsági problémák öndiagnosztizálásához tekintse meg az Eseménynapló csoport **Alkalmazások és szolgáltatások Naplózza** > a**Microsoft-Service Fabric**.
> 
> 

