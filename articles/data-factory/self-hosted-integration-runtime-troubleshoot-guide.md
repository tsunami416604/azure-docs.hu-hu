---
title: A Azure Data Factory saját üzemeltetésű integrációs moduljának hibakeresése
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory a saját üzemeltetésű integrációs modul hibáit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907281"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul hibáinak megoldása

Ez a cikk a Azure Data Factory saját üzemeltetésű integrációs moduljának gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-resolutions"></a>Gyakori hibák és megoldások

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Hibaüzenet: a saját üzemeltetésű integrációs modul nem tud kapcsolódni a Cloud Service-hez.

- **Tünet**: 

    ![Saját üzemeltetésű IR-kapcsolati probléma](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **OK**: a saját üzemeltetésű integrációs modul nem tud kapcsolódni a (z) adatfeldolgozó szolgáltatáshoz (háttér). A leggyakrabban nem ez okozza a tűzfal hálózati beállításai miatt.

- **Megoldás**: 

    1. Ellenőrizze, hogy fut-e a Windows-szolgáltatás "Integration Runtime szolgáltatás".
    
        ![A saját üzemeltetésű IR-szolgáltatás futási állapota](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Ha a Windows-szolgáltatás a (z) [1] verzióban látható módon fut, kövesse az alábbi utasításokat a megfelelő módon:

        1. Ha a "proxy" nincs konfigurálva a saját üzemeltetésű integrációs modulban (az alapértelmezett beállítások nem a proxy konfigurációja), futtassa az alábbi PowerShell-parancsot azon a gépen, amelyen a saját üzemeltetésű integrációs modul telepítve van: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > A szolgáltatás URL-címe a saját adatelőállítói helytől függően változhat. A szolgáltatás URL-címe az ADF felhasználói felület – > Kapcsolatok – > Integration Runtimes – > Edit saját üzemeltetésű IR-> csomópontok – > View Service URL-címek szakaszban található.
            
            A várt válasz a következő:
            
            ![PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Ha a válasz eltér, kövesse az alábbi utasításokat a megfelelő módon:
            
            * Ha "a távoli név nem oldható fel" hibaüzenet jelenik meg, akkor probléma van a DNS-sel. Vegye fel a kapcsolatot a hálózati csapattal a DNS-feloldási probléma kijavítása érdekében! 
            * Ha "az SSL/TLS-tanúsítvány nem megbízható" hibaüzenet jelenik meg, ellenőrizze, hogy a "https://wu2.frontend.clouddatahub.net/" tanúsítványa megbízható-e a gépen, telepítse a nyilvános tanúsítványt a CERT Manager használatával, amely csökkenti a problémát.
            * Tekintse meg a Windows-> eseménynaplót (naplók) – > alkalmazások és szolgáltatások naplói – > Integration Runtime bármilyen meghibásodás esetén, amelyet többnyire a DNS, a tűzfalszabály és a vállalat hálózati beállításai okoznak (a kapcsolat kényszerített lezárása). A probléma megoldásához vegye fel a hálózati csapatát további troubleshot, mert minden vállalat testreszabott hálózati beállításokat tartalmaz.

        2. Ha a "proxy" konfigurálva van a saját üzemeltetésű integrációs modulban, ellenőrizze, hogy a proxykiszolgáló hozzáférhet-e a szolgáltatási végponthoz. A minta parancs esetében tekintse meg [ezt a témakört](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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
            
            ![2\. PowerShell-parancs válasza](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Proxyval kapcsolatos megfontolások:
            > * Ellenőrizze, hogy a proxykiszolgáló igényel-e engedélyezési feltételt. Ha igen, [ezek a tartományok](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) engedélyezési listán vannak.
            > * Győződjön meg arról, hogy a TLS/SSL-tanúsítvány a "wu2.frontend.clouddatahub.net/" megbízható a proxykiszolgálón.
            > * Ha a proxyban Active Directory-hitelesítést használ, akkor módosítsa a szolgáltatásfiókot arra a felhasználói fiókra, amely a proxyt "Integration Runtime szolgáltatásként" éri el.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Hibaüzenet: a saját üzemeltetésű integrációs modul csomópontja/logikai a (z) rendszer inaktív/"fut (korlátozott)" állapotban van.

- **OK**: a saját üzemeltetésű IR-csomópont inaktív állapotban jelenhet meg, ahogy az alábbi képernyőképen is látható:

    ![Inaktív önkiszolgáló IR-csomópont](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Ez akkor történik meg, amikor a csomópontok nem tudnak kommunikálni egymással. 

- **Megoldás**: 

    Jelentkezzen be a futtatott virtuális gépre, és nyissa meg az esemény nézetet az alkalmazások és szolgáltatások naplóiban – > Integration Runtime az összes hibanapló szűrése. 

     1. Ha a hibanapló tartalma a következő: 
    
        **Hibanapló**: System. ServiceModel., endpointnotfoundexception –: nem sikerült kapcsolódni a net. TCP://xxxxxxx.bwld.com: 8060/ExternalService. SVC/WorkerManager. A kapcsolódási kísérlet a 00:00:00.9940994 időtartamra tartott. 10061-as TCP-hibakód: nem sikerült kapcsolódni, mert a célszámítógép aktívan elutasította a 10.2.4.10:8060.  ---> System .net. Sockets. SocketException: nem hozható létre kapcsolat, mert a célszámítógép aktívan visszautasította azt 10.2.4.10:8060
    
           Rendszer .net. Sockets. socket. DoConnect (EndPoint endPointSnapshot, SocketAddress socketAddress)
           
           Rendszer .net. Sockets. socket. csatlakozás (EndPoint remoteEP)
           
           : System. ServiceModel. Channels. SocketConnectionInitiator. Kössük (URI URI, TimeSpan timeout)
    
        **Megoldás:** indítsa el a parancssort: telnet 10.2.4.10 8060
        
        Ha az alábbi hibaüzenet jelenik meg, kérje meg a segítségét a probléma megoldásához. A sikeres Telnet után forduljon a Microsoft ügyfélszolgálatához, ha továbbra is problémái vannak az IR-csomópont állapotával kapcsolatban.
        
        ![Parancssori hiba](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Ha a hibanapló tartalma a következő:
     
        **Hibanapló:** Nem lehet csatlakozni a Worker Managerhez: net. TCP://xxxxxx: 8060/ExternalService. SVC/nem létezik DNS-bejegyzés a gazdagép azranlcir01r1. Ilyen gazdagép nem ismert kivétel részletei: System. ServiceModel., Endpointnotfoundexception –: nem létezik DNS-bejegyzés a gazdagép xxxxx számára. ---> System .net. Sockets. SocketException: nincs ilyen gazdagép a System .net. DNS. GetAddrInfo (karakterlánc neve) helyen a System .net. DNS. InternalGetHostByName (karakterlánc-állomásnév, logikai includeIPv6) helyen a System .net. DNS. GetHostEntry (string hostNameOrAddress ) at System. ServiceModel. Channels. DnsCache. feloldás (URI URI)---a belső kivételek veremének végének nyomkövetése---Server stack Trace: at System. ServiceModel. Channels. DnsCache. feloldás (URI URI) 
    
        **Megoldás:** Az alábbi két művelet egyike segíthet a probléma megoldásában:
         1. Helyezze az összes csomópontot ugyanabba a tartományba.
         2. Adja hozzá az IP-címet az összes üzemeltetett virtuális gép gazdagép-fájljához.


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory-blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Az ADF-leképezés adatforgalmának teljesítmény-útmutatója](concepts-data-flow-performance.md)
