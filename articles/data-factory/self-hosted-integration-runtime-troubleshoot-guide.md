---
title: Saját üzemeltetésű integrációs futásidejű hibák elhárítása az Azure Data Factoryban
description: Ismerje meg, hogyan háríthatja el az Azure Data Factory saját üzemeltetésű integrációs futásidejű problémáit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414889"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs futásidejű hibáinak elhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az Azure Data Factory saját üzemeltetésű integrációs futásidejű megoldási módszereit ismerteti.

## <a name="common-errors-and-resolutions"></a>Gyakori hibák és megoldások

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Hibaüzenet: A saját üzemeltetésű integrációs futásidő nem tud csatlakozni a felhőszolgáltatáshoz

![Saját üzemeltetésű infravörös kapcsolattal kapcsolatos probléma](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok 

A saját üzemeltetésű integrációs futásidejű nem tud csatlakozni a Data Factory szolgáltatás (háttérrendszer). Ezt a problémát általában a tűzfal hálózati beállításai okozzák.

#### <a name="resolution"></a>Megoldás:

1. Ellenőrizze, hogy fut-e az integrációs futtatóheti szolgáltatás.
    
   ![Saját üzemeltetésű infravörös szolgáltatás futási állapota](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Ha a szolgáltatás fut, folytassa a 3.

1. Ha nincs konfigurálva proxy a saját üzemeltetésű integrációs futásidőben (ez az alapértelmezett beállítás), futtassa a következő PowerShell-parancsot azon a számítógépen, amelyen telepítve van az önkiszolgáló integrációs futásidő:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A szolgáltatás URL-címe a Data Factory tartózkodási helyétől függően változhat. A szolgáltatás URL-címét az **ADF felhasználói felületkapcsolatok** > **integrációs** > **futásideje** > alatt**találja, saját üzemeltetésű infravörös** > **csomópontok** > megtekintése szolgáltatás**URL-címei**.
            
    A várt válasz a következő:
            
    ![PowerShell-parancsválasz](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Ha nem kapja meg a várt választ, használja az alábbi módszerek egyikét az adott helyzetnek megfelelően:
            
    * Ha "A távoli név nem oldható fel" üzenet jelenik meg, a DNS-rendszer rel van a probléma. A probléma megoldásához forduljon a hálózati csapathoz.
    * Ha "Ssl/tls cert is not trusted" üzenetet kap, https://wu2.frontend.clouddatahub.net/ ellenőrizze, hogy a tanúsítvány megbízható-e a számítógépen, majd telepítse a nyilvános tanúsítványt a Tanúsítványkezelő segítségével. Ez a művelet enyhíti a problémát.
    * Nyissa meg a **Windows** > **eseménynapló (naplók)** > **Alkalmazások és szolgáltatások naplóinak integrációs** > **futásidejűét,** és ellenőrizze, hogy vannak-e olyan hibák, amelyeket a DNS, a tűzfalszabály vagy a vállalati hálózati beállítások okoznak. (Ha ilyen hibát talál, erőszakkal zárja le a kapcsolatot.) Mivel minden vállalat testre szabott hálózati beállításokkal rendelkezik, a problémák megoldásához lépjen kapcsolatba a hálózati csapattal.

1. Ha a "proxy" konfigurálva van a saját üzemeltetésű integrációs futásidőben, ellenőrizze, hogy a proxykiszolgáló képes-e elérni a szolgáltatásvégpontot. Mintaparancsért lásd: [PowerShell, webes kérések és proxyk.](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

A várt válasz a következő:
            
![Powershell parancsválasz 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxy szempontok:
> *    Ellenőrizze, hogy a proxykiszolgálót fel kell-e venni a Megbízható címzettek listára. Ha igen, győződjön meg arról, hogy ezek a [tartományok](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) szerepelnek a Megbízható címzettek listán.
> *    Ellenőrizze, hogy a proxykiszolgálón megbízható-e az "wu2.frontend.clouddatahub.net/" TLS/SSL tanúsítvány.
> *    Ha Active Directory-hitelesítést használ a proxyn, módosítsa a szolgáltatásfiókot arra a felhasználói fiókra, amely "Integrációs futásidejű szolgáltatásként" érheti el a proxyt.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Hibaüzenet: A saját üzemeltetésű integrációs futtató/ logikai SHIR inaktív/ "Futó (Korlátozott)" állapotban van

#### <a name="cause"></a>Ok 

A saját üzemeltetésű integrált futásidejű csomópont **inaktív** állapotú lehet, ahogy az a következő képernyőképen látható:

![Inaktív, saját üzemeltetett infravörös csomópont](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ez a viselkedés akkor fordul elő, ha a csomópontok nem tudnak kommunikálni egymással.

#### <a name="resolution"></a>Megoldás:

1. Jelentkezzen be a csomópont által üzemeltetett virtuális gépbe. Az **Alkalmazások és szolgáltatások naplók integrációs** > **futásidejű**csoportban nyissa meg az Eseménynaplót, és szűrje az összes hibanaplót.

1. Ellenőrizze, hogy a hibanapló a következő hibát tartalmazza-e: 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
