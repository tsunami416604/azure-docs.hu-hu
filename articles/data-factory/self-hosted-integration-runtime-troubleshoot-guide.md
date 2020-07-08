---
title: A Azure Data Factory saját üzemeltetésű integrációs moduljának hibakeresése
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory a saját üzemeltetésű integrációs modul hibáit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315110"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory saját üzemeltetésű integrációs moduljának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-resolutions"></a>Gyakori hibák és megoldások

### <a name="error-message"></a>Hibaüzenet: 

`Self-hosted integration runtime can't connect to cloud service`

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

### <a name="error-message"></a>Hibaüzenet: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

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


## <a name="troubleshoot-connectivity-issue"></a>Kapcsolódási probléma megoldása

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>A saját üzemeltetésű IR és Data Factory vagy saját üzemeltetésű IR és adatforrás/fogadó közötti kapcsolódási problémák megoldása

A hálózati kapcsolat problémájának megoldásához tudnia kell, hogyan [gyűjtheti a hálózati nyomkövetést](#how-to-collect-netmon-trace), hogyan használhatja azt, és [elemezheti a netmon nyomkövetést](#how-to-analyze-netmon-trace) , mielőtt a netmon-eszközöket valós esetekben alkalmazza a saját üzemeltetésű integrációs modulból.

Időnként, ha a kapcsolódási problémákat (például az alábbiakat) a saját üzemeltetésű integrációs modul és a Data Factory között elhárítja: 

![HTTP-kérelem sikertelen](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Vagy a saját üzemeltetésű IR és adatforrás/fogadó között a következő hibákat tapasztaljuk:

**Hibaüzenet:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Hibaüzenet:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Megoldás:** A fenti problémák megoldásakor a további hibaelhárításhoz tekintse meg a következő utasításokat:

Végezze el a netmon nyomkövetést, és elemezze tovább.
- Először beállíthatja a szűrőt úgy, hogy a kiszolgálóról az ügyfél felé irányuló alaphelyzetbe állítson. Az alábbi példában látható, hogy a kiszolgáló oldalon Data Factory kiszolgáló található.

    ![A adatfeldolgozó kiszolgáló](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- A csomag alaphelyzetbe állítása után a következő TCP-vel keresheti meg a beszélgetést.

    ![Beszélgetés keresése](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Ezután a szűrő eltávolításával lekérheti az ügyfél és Data Factory kiszolgáló közötti konverziót.

    ![Beszélgetés beolvasása](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Az összegyűjtött netmon-nyomkövetés alapján megtudhatjuk, hogy a TTL (TimeToLive) összesen 64. A [cikkben](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) említett **alapértelmezett TTL-és ugrási határértékek** (az alább leírtak szerint) alapján láthatjuk, hogy ez a Linux rendszer, amely alaphelyzetbe állítja a csomagot, és a leválasztást okoz.

    Az alapértelmezett TTL-és ugrás-határértékek eltérőek lehetnek a különböző operációs rendszerek között:
    - Linux kernel 2,4 (kb. 2001): 255 TCP, UDP és ICMP rendszerhez
    - Linux kernel 4,10 (2015): 64 TCP, UDP és ICMP rendszerhez
    - Windows XP (2001): 128 TCP, UDP és ICMP esetén
    - Windows 10 (2015): 128 TCP, UDP és ICMP esetén
    - Windows Server 2008:128 TCP, UDP és ICMP esetén
    - Windows Server 2019 (2018): 128 TCP, UDP és ICMP rendszerhez
    - macOS (2001): 64 a TCP, az UDP és az ICMP protokollhoz

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Azonban a fenti példában a 64 helyett 61 jelenik meg, mert ha a hálózati csomag eléri a célhelyet, a különböző ugrásokra, például útválasztók/hálózati eszközökre kell mutatnia. Az útválasztók/hálózati eszközök száma a végső TTL-ben lesz levonva.
    Ebben az esetben láthatjuk, hogy az Alaphelyzetbe állítás a 64-es TTL-vel rendelkező Linux rendszerből is elvégezhető.

- A negyedik ugrást a saját üzemeltetésű IR-ből kell ellenőrizni annak ellenőrzéséhez, hogy az eszköz alaphelyzetbe állítása honnan származhat.
 
    *Hálózati csomag az A Linux rendszertől a TTL 64-> B TTL 64 mínusz 1 = 63-> C TTL 63 mínusz 1 = 62-> TTL 62 mínusz 1 = 61 saját üzemeltetésű IR*

- Ideális esetben a TTL 128 lesz, ami azt jelenti, hogy a Windows rendszer fut a Data Factory. Ahogy az alábbi példában is látható, a *128 – 107 = 21 ugrás*, ami azt jelenti, hogy a csomaghoz tartozó 21 ugrást a rendszer a TCP 3-kézfogás során Data Factory a saját üzemeltetésű IR-be küldi.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Ezért be kell vonnia a hálózati csapatot annak ellenőrzéséhez, hogy a negyedik Ugrás a saját üzemeltetésű integrációs modulból származik-e. Ha a tűzfal Linux rendszer, akkor ellenőrizze, hogy az eszköz miért állítja vissza a csomagot a TCP 3 kézfogás után. Ha azonban nem tudja, hol kell megvizsgálnia a vizsgálatot, próbálja meg a netmon-nyomkövetést a saját üzemeltetésű integrációs modulból és a tűzfalból összekapcsolni a problémás idő alatt, hogy kiderítse, melyik eszköz állíthatja alaphelyzetbe ezt a csomagot Ebben az esetben arra is szükség van, hogy a hálózati csapatot tovább folytassa.

### <a name="how-to-collect-netmon-trace"></a>Netmon-nyomkövetés gyűjtése

1.  Töltse le a netmon-eszközöket [erről a webhelyről](https://www.microsoft.com/en-sg/download/details.aspx?id=4865), és telepítse azt a kiszolgálói gépre (a problémával rendelkező kiszolgálóra) és az ügyfélre (például saját üzemeltetésű IR-re).

2.  Hozzon létre egy mappát, például a következő elérési úton: *D:\netmon*. Győződjön meg arról, hogy elegendő lemezterülettel rendelkezik a napló mentéséhez.

3.  Az IP-cím és a port adatainak rögzítése. 
    1. Indítson el egy parancssort.
    2. Válassza a Futtatás rendszergazdaként lehetőséget, és futtassa a következő parancsot:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Rögzítse a netmon nyomkövetését (hálózati csomag).
    1. Indítson el egy parancssort.
    2. Válassza a Futtatás rendszergazdaként lehetőséget, és futtassa a következő parancsot:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. A hálózat lap rögzítéséhez három különböző parancsot használhat:
        - A: RoundRobin file parancs (ez csak egy fájlt fog rögzíteni, és felülírja A régi naplókat).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - B. lehetőség: láncolt fájl parancs (ez az új fájlt fogja létrehozni, ha elérte a 200 MB-ot).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - C. lehetőség: ütemezett fájl parancs.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Nyomja le a **CTRL + C** billentyűkombinációt a netmon nyomkövetés rögzítésének leállításához.
 
> [!NOTE]
> Ha csak a netmon nyomkövetést szeretné összegyűjteni az ügyfélszámítógépen, kérje le a kiszolgáló IP-címét, hogy segítsen a nyomkövetés elemzésében.

### <a name="how-to-analyze-netmon-trace"></a>Netmon-nyomkövetés elemzése

Ha a **8.8.8.8 888** -as netmon-nyomkövetéssel próbálkozik a fent leírtak szerint, az alábbi nyomkövetés látható:

![netmon nyomkövetés 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon nyomkövetés 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Ez azt jelenti, hogy a **888**-es porton alapuló TCP-csatlakozás nem végezhető el a **8.8.8.8** -kiszolgáló oldalára, így két **SynReTransmit** további csomagot láthat. Mivel a Source **HOST2** nem tudott kapcsolódni az **8.8.8.8** -hez az első csomagban, továbbra is a kapcsolódást fogja végezni.

> [!TIP]
> - Kattintson a **szűrő**  ->  **szabványos szűrő**  ->  **címek**  ->  **IPv4-címek**betöltése lehetőségre.
> - Adja meg a bemeneti **IPv4.-címek = = 8.8.8.8** szűrőként, majd kattintson az **alkalmaz**gombra. Ezt követően csak a helyi gépről érkező kommunikáció jelenik meg a cél **8.8.8.8**.

![címek szűrése 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![címek szűrése 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Az alábbi példa egy jó forgatókönyv megjelenését mutatja be. 

- Ha a Telnet **8.8.8.8 53** a probléma nélkül működik, láthatja a TCP 3 kézfogást, majd a munkamenet a TCP 4 kézfogással fejeződik be.

    ![példa az 1. jó forgatókönyvre](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![helyes forgatókönyv 2. példa](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- A fenti TCP 3 kézfogás alapján a következő munkafolyamat látható:

    ![TCP 3 kézfogás munkafolyamata](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- A munkamenet és a munkafolyamata befejezéséhez a TCP 4 kézfogás a következőképpen fog megjelenni:

    ![TCP 4 kézfogás](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 kézfogás munkafolyamata](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>További lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Adatforgalom teljesítményének feltérképezése útmutató](concepts-data-flow-performance.md)
