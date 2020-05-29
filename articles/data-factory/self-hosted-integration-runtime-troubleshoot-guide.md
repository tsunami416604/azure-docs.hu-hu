---
title: A Azure Data Factory saját üzemeltetésű integrációs moduljának hibakeresése
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory a saját üzemeltetésű integrációs modul hibáit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f27132eb21d245d0d26de910abba088ba3b8efde
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170973"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory saját üzemeltetésű integrációs moduljának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-resolutions"></a>Gyakori hibák és megoldások

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Hibaüzenet: a saját üzemeltetésű Integration Runtime nem tud kapcsolódni a Cloud Service-hez

![Saját üzemeltetésű IR-kapcsolati probléma](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Ok 

A saját üzemeltetésű integrációs modul nem tud kapcsolódni a Data Factory szolgáltatáshoz (háttér). Ezt a problémát általában a tűzfal hálózati beállításai okozzák.

#### <a name="resolution"></a>Megoldás:

1. Győződjön meg arról, hogy fut-e az Integration Runtime szolgáltatás.
    
   ![A saját üzemeltetésű IR-szolgáltatás futási állapota](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Ha a szolgáltatás fut, folytassa a 3. lépéssel.

1. Ha nincs proxy konfigurálva a saját üzemeltetésű integrációs modulban (amely az alapértelmezett beállítás), futtassa a következő PowerShell-parancsot azon a gépen, amelyen a saját üzemeltetésű integrációs modul telepítve van:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A szolgáltatás URL-címe eltérő lehet a Data Factory helyétől függően. A szolgáltatás URL-címét az **ADF UI**  >  **Connections**  >  **Integration Runtimes**saját üzemeltetésű  >  **IR**  >  -**csomópontok**  >  **megtekintése szolgáltatás URL-** címének szerkesztése szakaszban találja.
            
    A várt válasz a következő:
            
    ![PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Ha nem kapja meg a várt választ, használja a következő módszerek egyikét az adott helyzetnek megfelelően:
            
    * Ha "a távoli név nem oldható fel" üzenet jelenik meg, a tartománynévrendszer (DNS) problémája van. A probléma megoldásához forduljon a hálózati csapathoz.
    * Ha "SSL/TLS-tanúsítvány nem megbízható" üzenetet kap, ellenőrizze, hogy a tanúsítvány megbízható-e https://wu2.frontend.clouddatahub.net/ a gépen, majd telepítse a nyilvános tanúsítványt a Tanúsítványkezelő használatával. A műveletnek csökkentenie kell a problémát.
    * Nyissa meg a **Windows**  >  **Eseménynapló (naplók)**  >  **alkalmazásait és szolgáltatásait tartalmazó naplókat**  >  **Integration Runtime** , és keresse meg a DNS, a tűzfalszabály vagy a vállalati hálózati beállítások által okozott hibát. (Ha ilyen hibát talál, kényszerítse a kapcsolatok bezárását.) Mivel minden vállalat testreszabott hálózati beállításokat tartalmaz, a problémák elhárításához forduljon a hálózati csapatához.

1. Ha a "proxy" konfigurálva van a saját üzemeltetésű integrációs modulban, ellenőrizze, hogy a proxykiszolgáló hozzáférhet-e a szolgáltatási végponthoz. A minta parancsokért lásd: [PowerShell, webes kérelmek és proxyk](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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
            
![2. PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Proxyval kapcsolatos megfontolások:
> *    Győződjön meg arról, hogy a proxykiszolgálót a biztonságos címzettek listára kell helyezni. Ha igen, győződjön meg arról, hogy [ezek a tartományok](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) a biztonságos címzettek listán vannak.
> *    Győződjön meg arról, hogy a "wu2.frontend.clouddatahub.net/" TLS/SSL-tanúsítvány megbízható-e a proxykiszolgálón.
> *    Ha Active Directory hitelesítést használ a proxyn, módosítsa a szolgáltatásfiókot arra a felhasználói fiókra, amely a proxyt "Integration Runtime szolgáltatás" néven éri el.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Hibaüzenet: a saját üzemeltetésű integrációs modul csomópontja/logikai a (z) rendszer inaktív/"fut (korlátozott)" állapotban van.

#### <a name="cause"></a>Ok 

Előfordulhat, hogy a saját üzemeltetésű integrált futásidejű csomópont **inaktív** állapotú, ahogy az alábbi képernyőképen is látható:

![Inaktív önkiszolgáló IR-csomópont](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ez a viselkedés akkor fordul elő, ha a csomópontok nem tudnak kommunikálni egymással.

#### <a name="resolution"></a>Megoldás:

1. Jelentkezzen be a csomópont által üzemeltetett virtuális gépre. Az **alkalmazások és szolgáltatások naplóiban**  >  **Integration Runtime**, nyissa meg Eseménynapló, és szűrje az összes hibát.

1. Győződjön meg arról, hogy a hibanapló a következő hibát tartalmazza-e: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Ha ezt a hibát látja, futtassa a következő parancsot a parancssorban: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Ha a következő hibaüzenetet kapja, lépjen kapcsolatba az informatikai részleggel a probléma megoldása érdekében. A sikeres Telnet után lépjen kapcsolatba Microsoft ügyfélszolgálata, ha továbbra is problémái vannak az integratív futtatókörnyezet csomópontjának állapotával.
        
   ![Parancssori hiba](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Győződjön meg arról, hogy a hibanapló tartalmazza-e a következőket:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. A probléma megoldásához próbálja meg a következő módszerek egyikét vagy mindkettőt:
    - Helyezze az összes csomópontot ugyanabba a tartományba.
    - Adja hozzá az IP-címet a gazdagépek leképezéséhez az összes üzemeltetett virtuális gép gazdagépének fájljaiban.


## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Adatforgalom teljesítményének feltérképezése útmutató](concepts-data-flow-performance.md)
