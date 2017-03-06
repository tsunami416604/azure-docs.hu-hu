---
title: "Proxy- és tűzfalbeállítások konfigurálása az Azure Log Analyticsben | Microsoft Docs"
description: "A proxy- és tűzfalbeállítások konfigurálása abban az esetben, ha az OMS-szolgáltatásoknak megadott portokat kell használniuk."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b55ebd80-efd4-4220-971b-c18aea1b1ab2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: banders;magoedte
translationtype: Human Translation
ms.sourcegitcommit: 6a527fa303f1e2bd06ac662e545d6b6a1d299fb4
ms.openlocfilehash: cd06dfd498540970dc8ed29650f4d9e3ca57939b
ms.lasthandoff: 02/10/2017


---
# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Proxy- és tűzfalbeállítások konfigurálása a Log Analytics rendszereben
A proxy- és tűzfalbeállítások a Log Analytics számára végzett konfigurálása a használt ügynöktípusoktól függően eltérő. Az alábbi részek közül tekintse át az Ön által használt ügynököknek megfelelőt.

## <a name="settings-for-the-oms-gateway"></a>Az OMS-átjáró beállításai

Ha az ügynökök nem rendelkeznek interneteléréssel, az adatokat beküldhetik az Ön saját hálózati erőforrásai használatával az OMS-átjáróra. Az átjáró begyűjti az adataikat, és elküldi azokat a nevükben az OMS szolgáltatásnak.

Az OMS-átjáróval kommunikáló ügynökök konfigurálásakor az átjáró teljes tartománynevét és egyéni portszámát állítsa be.

Az OMS-átjárónak interneteléréssel kell rendelkeznie. Az OMS-átjáróhoz használja ugyanazokat a proxyszolgáló- vagy tűzfalbeállításokat, amelyeket az ügynöktípusokhoz is használna. Az OMS-átjáróval kapcsolatos további információkért lásd a [számítógépek és eszközök az OMS-hez az OMS-átjáró használatával történő csatlakoztatását](log-analytics-oms-gateway.md) ismertető témakört.

## <a name="configure-settings-with-the-microsoft-monitoring-agent"></a>Beállítások konfigurálása Microsoft Monitoring Agent használata esetén
Ahhoz, hogy a Microsoft Monitoring Agent kapcsolódni és regisztrálni tudjon az OMS szolgáltatással, hozzáféréssel kell rendelkeznie az Ön tartományainak és URL-címeinek portszámához. Amennyiben az ügynök és az OMS szolgáltatás közötti kommunikációhoz proxykiszolgálót használ, győződjön meg arról, hogy elérhetők-e a megfelelő erőforrásokon. Ha tűzfal használatával korlátozza az internethez való hozzáférést, akkor a tűzfalat úgy kell beállítani, hogy engedélyezze a hozzáférést az OMS számára. Az alábbi táblázatok sorolják fel az OMS számára szükséges portokat.

| **Ügynök erőforrása** | **Portok** | **HTTPS-ellenőrzés kihagyása** |
| --- | --- | --- |
| \*.ods.opinsights.azure.com |443 |Igen |
| \*.oms.opinsights.azure.com |443 |Igen |
| \*.blob.core.windows.net |443 |Igen |
| \*.azure-automation.net |443 |Igen |
| ods.systemcenteradvisor.com |443 | |

Az alábbi eljárás segítségével konfigurálhatja a proxybeállításokat a Microsoft Monitoring Agent számára a Vezérlőpult használatával. Az eljárást minden egyes kiszolgáló esetén el kell végezni. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha ez az eset áll fenn, tekintse meg a következő eljárást [A proxybeállítások konfigurálása a Microsoft Monitoring Agent számára parancsfájl használatával](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script) című szakaszban.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>A proxybeállítások konfigurálása a Microsoft Monitoring Agent számára a Vezérlőpult használatával
1. Nyissa meg **Vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.<br>  
   ![proxybeállítások lap](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)
4. Válassza a **Use a proxy server** (Use a proxy server) lehetőséget, majd adja meg az URL-címet és portszámot, amennyiben szükséges – hasonlóan az itt látható példához. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.

Az alábbi eljárás segítségével hozhat létre olyan PowerShell-parancsfájlt, amelynek futtatásával megadhatja az összes olyan ügynök proxybeállításait, amely közvetlenül csatlakozik a kiszolgálókhoz.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>A proxybeállítások konfigurálása a Microsoft Monitoring Agent számára parancsfájl használatával
Másolja ki az alábbi mintát, frissítse az Ön környezetére jellemző adatokkal, mentse PS1 fájlnévkiterjesztéssel, és futtassa a parancsfájlt minden olyan számítógépen, amely közvetlenül csatlakozik az OMS szolgáltatáshoz.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)


## <a name="configure-settings-with-operations-manager"></a>Beállítások konfigurálása az Operations Managerrel
Ahhoz, hogy egy Operations Manager felügyeleti csoport csatlakozni és regisztrálni tudjon az OMS szolgáltatásban, a csoportnak hozzá kell férnie az Ön tartományainak és URL-címeinek portszámaihoz. Ha az Operations Manager felügyeleti kiszolgáló és az OMS szolgáltatás közötti kommunikációhoz proxykiszolgálót használ, meg kell győződnie arról, hogy elérhetők-e a megfelelő erőforrások. Ha tűzfal használatával korlátozza az internethez való hozzáférést, akkor a tűzfalat úgy kell beállítani, hogy engedélyezze a hozzáférést az OMS számára. Ha az Operations Manager felügyeleti kiszolgáló nincs is proxykiszolgáló mögött, az ügynökök még ott lehetnek. Ebben az esetben a proxykiszolgálót az ügynökökkel azonos módon kell konfigurálni annak érdekében, hogy a biztonsági és naplókezelési szolgáltatás adatait a rendszer elküldhesse az OMS-webszolgáltatás számára.

Ahhoz, hogy az Operations Manager-ügynökök kommunikálni tudjanak az OMS-szolgáltatással, az Operations Manager-infrastruktúrának (beleértve az ügynököket is) a megfelelő proxybeállításokkal és verzióval kell rendelkezniük. Az ügynökök proxybeállításai az Operations Manager konzolon vannak megadva. A használt verziónak a következők valamelyikének kell lennie:

* Operations Manager 2012 SP1 7. kumulatív frissítéssel vagy újabb
* Operations Manager 2012 R2 3. kumulatív frissítéssel vagy újabb

Az alábbi táblázatok sorolják fel a feladatokhoz kapcsolódó portokat.

> [!NOTE]
> Az alábbi források némelyike az Advisor és az Operational Insights terméket említi. Mindkettő az OMS korábbi verziója volt. A jövőben azonban változni fognak a felsorolt erőforrások.
>
>

Alább látható az ügynökerőforrások és -portok listája:<br>

| **Ügynök erőforrása** | **Portok** |
| --- | --- |
| \*.ods.opinsights.azure.com |443 |
| \*.oms.opinsights.azure.com |443 |
| \*.blob.core.windows.net\* |443 |
| ods.systemcenteradvisor.com |443 |

<br>
Itt látható a felügyeletikiszolgáló-erőforrások és -portok listája:<br>

| **Felügyeleti kiszolgáló erőforrása** | **Portok** | **HTTPS-ellenőrzés kihagyása** |
| --- | --- | --- |
| service.systemcenteradvisor.com |443 | |
| \*.service.opinsights.azure.com |443 | |
| \*.blob.core.windows.net |443 |Igen |
| data.systemcenteradvisor.com |443 | |
| ods.systemcenteradvisor.com |443 | |
| \*.ods.opinsights.azure.com |443 |Igen |
| \*.azure-automation.net |443 |Igen |

<br>
Itt látható az OMS és az Operations Manager-konzol erőforrásainak és portjainak listája.<br>

| **Az OMS és az Operations Manager-konzol erőforrása** | **Portok** |
| --- | --- |
| service.systemcenteradvisor.com |443 |
| \*.service.opinsights.azure.com |443 |
| \*.live.com |80-as és 443-as port |
| \*.microsoft.com |80-as és 443-as port |
| \*.microsoftonline.com |80-as és 443-as port |
| \*.mms.microsoft.com |80-as és 443-as port |
| login.windows.net |80-as és 443-as port |

<br>

Az alábbi eljárásokkal regisztrálhatja az Operations Manager felügyeleti csoportot az OMS szolgáltatással. Amennyiben a felügyeleti csoport és az OMS szolgáltatás közötti kommunikációs problémák merülnek fel, használja az ellenőrzési eljárásokat az OMS szolgáltatás irányában folyó adatátvitel hibáinak elhárításához.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Kivételek kérelmezése az OMS-szolgáltatásvégpontok számára
1. Használja a fent bemutatott első táblázatban szereplő adatokat annak biztosításához, hogy az Operations Manager felügyeleti kiszolgáló számára szükséges erőforrások minden Ön által használt tűzfalon keresztül elérhetők legyenek.
2. Használja a fent bemutatott második táblázatban szereplő adatokat annak biztosításához, hogy az Operations Manager és OMS rendszerbeli operatív konzol számára szükséges erőforrások minden Ön által használt tűzfalon keresztül elérhetők legyenek.
3. Ha az Internet Explorerrel használ proxykiszolgálót, akkor győződjön meg arról, hogy helyesen van-e beállítva, illetve megfelelően működik-e. Ennek ellenőrzéséhez nyisson meg egy biztonságos webkapcsolatot (HTTPS), például a [https://bing.com](https://bing.com) webhelyet. Ha a biztonságos kapcsolat nem működik a böngészőben, akkor valószínűleg nem fog működni az Operations Manager felügyeleti konzolján sem a felhőbeli webszolgáltatásokkal.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>A proxykiszolgáló konfigurálása az Operations Manager-konzolon
1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Bontsa ki a **Operational Insights** elemet, majd válassza ki az **Operational Insights Connection** (Operational Insights-kapcsolat) lehetőséget.<br>  
   ![Operations Manager – OMS-kapcsolat](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. Az OMS Connection (OMS-kapcsolat) nézetben kattintson a **Configure Proxy Server** (Proxykiszolgáló konfigurálása) lehetőségre.<br>  
   ![Operations Manager – OMS-kapcsolat – Proxykiszolgáló konfigurálása](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. Az Operational Insights Settings Wizard: Proxy Server (Operational Insights beállítási varázsló: Proxykiszolgáló) területen válassza a **Use a proxy server to access the Operational Insights Web Service** (Proxykiszolgáló használata az Operational Insights webszolgáltatás eléréséhez) lehetőséget, majd írja be az URL-címet a portszámmal együtt (például: **http://sajatproxy:80**).<br>  
   ![Operations Manager – OMS proxycíme](./media/log-analytics-proxy-firewall/proxy-om03.png)

### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>A hitelesítő adatokat megadása, ha a proxykiszolgáló hitelesítést igényel
 A proxykiszolgáló hitelesítő adatait és beállításait propagálni kell azokra a felügyelt számítógépekre, amelyek jelenteni fognak az OMS számára. Ezeknek a kiszolgálóknak a *Microsoft System Center Advisor Monitoring Server Group* (Microsoft System Center Advisor figyelésikiszolgáló-csoport) tagjainak kell lenniük. A csoportban lévő kiszolgálók beállításjegyzékében a hitelesítő adatok titkosítva tárolódnak.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. A **RunAs Configuration** (RunAs-konfiguráció) területen válassza a **Profiles** (Profilok) lehetőséget.
3. Nyissa meg a **System Center Advisor Run As Profile Proxy** (System Center Advisor RunAs-profiljának proxyja) profilt.<br>  
   ![a System Center Advisor RunAs-proxyprofilját ábrázoló kép](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. A futtatásiprofil-varázslóban kattintson az **Add** (Hozzáadás) lehetőségre egy futtató fiók használatához. Létrehozhat egy új futtató fiókot, vagy egy meglévő fiókot használhat. Ennek a fióknak rendelkeznie kell a megfelelő engedélyekkel a proxykiszolgálón való áthaladáshoz.<br>   
   ![a futtatóprofil-varázslót ábrázoló kép](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Egy fiók kezelésre való beállításához válassza az **A selected class, group, or object** (Egy kijelölt osztály, csoport vagy objektum) lehetőséget az objektumkeresési mező megnyitásához.<br>  
   ![a futtatóprofil-varázslót ábrázoló kép](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Keresse meg, majd válassza ki a **Microsoft System Center Advisor Monitoring Server Group** (Microsoft System Center Advisor figyelésikiszolgáló-csoport) nevű csoportot.<br>  
   ![az objektumkereső mezőt ábrázoló kép](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Kattintson a **OK** gombra a futtató fiók hozzáadására szolgáló mezőt.<br>  
   ![a futtatóprofil-varázslót ábrázoló kép](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Fejezze be a varázslót, és mentse a módosításokat.<br>  
   ![a futtatóprofil-varázslót ábrázoló kép](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)

### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Annak ellenőrzése, hogy az OMS felügyeleti csomagok letöltődtek-e
Ha megoldásokat adott hozzá az OMS rendszerhez, megtekintheti őket az Operations Manager-konzolon felügyeleti csomagként az **Administration** (Adminisztráció) területen. Keressen a *System Center Advisor* kifejezésre a csomagok gyors megtalálásához.<br>  
   ![letöltött felügyeleti csomagok](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png)  <br>  
Vagy a következő Windows PowerShell-parancsnak az Operations Manager felügyeleti kiszolgálón történő használatával is ellenőrizheti az OMS felügyeleti csomagokat.

   ```  
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
   ```  

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Annak ellenőrzése, hogy az Operations Manager küld-e adatokat az OMS szolgáltatásnak
1. Az Operations Manager felügyeleti kiszolgálón nyissa meg a Teljesítményfigyelőt (perfmon.exe), és válassza ki a **Performance Monitor** (Teljesítményfigyelő) lehetőséget.
2. Kattintson az **Add** (Hozzáadás) parancsra, majd válassza ki a **Health Service Management Groups** (Állapotfigyelő szolgáltatás felügyeleti csoportjai) lehetőséget.
3. Adja hozzá az összes olyan számlálót, amely a **HTTP** karakterekkel kezdődik.<br>  
   ![számlálók hozzáadása](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Ha az Operations Manager-konfiguráció helyes, akkor látható lesz az állapotfigyelő szolgáltatás felügyeleti számlálóinak eseményekkel és más adatelemekkel kapcsolat tevékenysége, amely az OMS rendszerben felvett felügyeleti csomagok, valamint a beállított naplógyűjtési házirend alapján jelenik meg.<br>  
   ![A Teljesítményfigyelő tevékenységeket jelenít meg](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)

## <a name="next-steps"></a>Következő lépések
* A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).
* A [naplókeresések](log-analytics-log-searches.md) megismerése a megoldások által összegyűjtött részletes információk megtekintéséhez.

