---
title: Számítógépek csatlakoztatása a Log Analytics átjáró használatával | Microsoft dokumentumok
description: Csatlakoztassa eszközeit és az Operations Manager által figyelt számítógépeket a Log Analytics átjáró segítségével, hogy adatokat küldjön az Azure Automation és log Analytics szolgáltatásnak, ha nem rendelkeznek internet-hozzáféréssel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298336"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Internet-hozzáféréssel nem rendelkező számítógépek csatlakoztatása az Azure Monitor Log Analytics átjárójának használatával

>[!NOTE]
>Ahogy a Microsoft Operations Management Suite (OMS) átvált a Microsoft Azure Monitorra, a terminológia változik. Ez a cikk az OMS-átjáróaz Azure Log Analytics-átjáró hivatkozik. 
>

Ez a cikk ismerteti, hogyan konfigurálhatja a kommunikációt az Azure Automation és az Azure Monitor a Log Analytics-átjáró használatával, ha a számítógépek, amelyek közvetlenül csatlakozik, vagy amelyek által figyelt Operations Manager nem rendelkezik internet-hozzáféréssel. 

A Log Analytics-átjáró egy HTTP-továbbító proxy, amely támogatja a HTTP-bújtatást a HTTP CONNECT paranccsal. Ez az átjáró adatokat küld az Azure Automation és a Log Analytics munkaterület az Azure Monitor nevében a számítógépek, amelyek nem tudnak közvetlenül csatlakozni az internethez. 

A Log Analytics-átjáró a következőket támogatja:

* Jelentés készítése az egyes ügynökök mögött beállított és az Azure Automation hibrid runbook-feldolgozók konfigurált ugyanazon Log Analytics-munkaterületekre.  
* Windows-számítógépek, amelyeken a Microsoft Monitoring Agent közvetlenül csatlakozik egy Log Analytics-munkaterülethez az Azure Monitorban.
* Linux-számítógépek, amelyeken a Log Analytics-ügynök Linux közvetlenül kapcsolódik a Log Analytics-munkaterülethez az Azure Monitorban.  
* System Center Operations Manager 2012 SP1 UR7-szel, Operations Manager 2012 R2 UR3-mal, vagy az Operations Manager 2016-os vagy újabb verziójában a Log Analytics szolgáltatással integrált felügyeleti csoport.  

Egyes informatikai biztonsági házirendek nem engedélyezik az internetkapcsolatot a hálózati számítógépek számára. Ezek a nem csatlakoztatott számítógépek lehetnek például pénztári eszközök vagy informatikai szolgáltatásokat támogató kiszolgálók. Ha ezeket az eszközöket az Azure Automation hez vagy egy Log Analytics-munkaterülethez szeretné csatlakoztatni, hogy kezelje és figyelje őket, konfigurálja őket úgy, hogy közvetlenül kommunikáljanak a Log Analytics-átjáróval. A Log Analytics-átjáró a nevükben fogadhat konfigurációs adatokat és továbbíthat adatokat. Ha a számítógépek úgy vannak konfigurálva a Log Analytics-ügynökkel, hogy közvetlenül csatlakozzanak egy Log Analytics-munkaterülethez, a számítógépek ehelyett a Log Analytics-átjáróval kommunikálnak.  

A Log Analytics átjáró adatokat továbbít az ügynökök közvetlenül a szolgáltatásba. Nem elemzi az átvitel során lévő adatokat, és az átjáró nem gyorsítótárazadatokat, ha elveszíti a kapcsolatot a szolgáltatással. Ha az átjáró nem tud kommunikálni a szolgáltatással, az ügynök továbbra is fut, és várólistára helyezi az összegyűjtött adatokat a figyelt számítógép lemezén. A kapcsolat visszaállításakor az ügynök elküldi a gyorsítótárazott adatokat az Azure Monitornak.

Ha egy Operations Manager felügyeleti csoport integrálva van a Log Analytics szolgáltatással, a felügyeleti kiszolgálók beállíthatók úgy, hogy csatlakozzanak a Log Analytics átjáróhoz a konfigurációs adatok fogadásához és az összegyűjtött adatok küldéséhez, az engedélyezett megoldástól függően .  Az Operations Manager ügynökei bizonyos adatokat küldenek a felügyeleti kiszolgálónak. Az ügynökök például küldhetnek Operations Manager-riasztásokat, konfigurációértékelési adatokat, példányterület-adatokat és kapacitásadatokat. Más nagy mennyiségű adatok, például az Internet Information Services (IIS) naplók, teljesítményadatok és biztonsági események közvetlenül a Log Analytics-átjáróba kerül. 

Ha egy vagy több Operations Manager-átjárókiszolgáló a peremhálózaton vagy egy elszigetelt hálózaton lévő nem megbízható rendszerek figyelésére van telepítve, ezek a kiszolgálók nem tudnak kommunikálni a Log Analytics-átjáróval.  Az Operations Manager átjárókiszolgálói csak felügyeleti kiszolgálónak jelenthetnek.  Ha egy Operations Manager-felügyeleti csoport úgy van beállítva, hogy kommunikáljon a Log Analytics-átjáróval, a proxykonfigurációs adatok automatikusan ellesznek osztva minden ügynök által felügyelt számítógépszámára, amely úgy van konfigurálva, hogy naplóadatokat gyűjtsön az Azure Monitorhoz, akkor is, ha a beállítás üres.

A közvetlenül csatlakoztatott vagy az Operations Management-csoportok, amelyek kommunikálnak a Log Analytics-munkaterület az átjárón keresztül, használja a hálózati terheléselosztás (NLB) átirányítása és elosztása a forgalom több átjárókiszolgálók között. Így ha az egyik átjárókiszolgáló leáll, a forgalom átirányításra kerül egy másik elérhető csomópontra.  

A Log Analytics-átjárót futtató számítógépnek a Log Analytics Windows-ügynöktől kell azonosítania azokat a szolgáltatásvégpontokat, amelyekkel az átjárónak kommunikálnia kell. Az ügynöknek is irányítania kell az átjárót, hogy ugyanazokba a munkaterületekre jelentsen, amelyekhez az ügynökök vagy az Operations Manager felügyeleti csoport az átjáró mögött konfigurálva van. Ez a konfiguráció lehetővé teszi, hogy az átjáró és az ügynök kommunikáljon a hozzárendelt munkaterülettel.

Egy átjáró többhelyű akár négy munkaterületet. Ez a Windows-ügynök által támogatott munkaterületek teljes száma.  

Minden ügynöknek hálózati kapcsolattal kell rendelkeznie az átjáróhoz, hogy az ügynökök automatikusan továbbíthassanak adatokat az átjáróra és az átjáróról. Ne telepítse az átjárót tartományvezérlőre. Az átjárókiszolgáló mögött található Linux-számítógépek nem használhatják a [burkolóparancsfájl telepítési](agent-linux.md#install-the-agent-using-wrapper-script) módszerét a Linux-alapú Log Analytics-ügynök telepítéséhez. Az ügynököt manuálisan kell letölteni, a számítógépre másolni és manuálisan telepíteni, mert az átjáró csak a korábban említett Azure-szolgáltatásokkal való kommunikációt támogatja.

Az alábbi ábrán a közvetlen ügynököktől az átjárón keresztül az Azure Automation és a Log Analytics felé áramló adatok láthatók. Az ügynök proxykonfigurációjának meg kell egyeznie azzal a porttal, amelyhez a Log Analytics-átjáró konfigurálva van.  

![Közvetlen ügynöki kommunikáció diagramja a szolgáltatásokkal](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán az Operations Manager felügyeleti csoportjából a Log Analytics szolgáltatásba történő adatfolyam látható.   

![Az Operations Manager kommunikációjának diagramja a Log Analytics szolgáltatással](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>A rendszer beállítása

A Log Analytics-átjáró futtatására kijelölt számítógépeknek a következő konfigurációval kell rendelkezniük:

* Windows 10, Windows 8.1 vagy Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 vagy Windows Server 2008
* Microsoft .NET-keretrendszer 4.5
* Legalább egy 4 magos processzor és 8 GB memória 
* [A Windows Log Analytics-ügynöke,](agent-windows.md) amely úgy van beállítva, hogy az átjárón keresztül kommunikáló ügynökökkel azonos munkaterületre jelentsen

### <a name="language-availability"></a>A nyelv elérhetősége

A Log Analytics átjáró a következő nyelveken érhető el:

- Kínai (egyszerűsített)
- Kínai (hagyományos)
- Cseh
- Holland
- Angol
- Francia
- Német
- Magyar
- Olasz
- Japán
- Koreai
- Lengyel
- Portugál (Brazília)
- Portugál (Portugália)
- Orosz
- Spanyol (nemzetközi)

### <a name="supported-encryption-protocols"></a>Támogatott titkosítási protokollok

A Log Analytics átjáró csak a Transport Layer Security (TLS) 1.0, 1.1 és 1.2-es támogatását támogatja.  Nem támogatja a Secure Sockets Layer (SSL) réteget.  A Log Analytics szolgáltatásba történő átvitel során az adatok biztonságának biztosítása érdekében konfigurálja úgy az átjárót, hogy legalább tls 1.2-t használjon. A TLS vagy Az SSL régebbi verziói sebezhetőek. Bár jelenleg lehetővé teszik a visszamenőleges kompatibilitást, ne használja őket.  

További információkért tekintse át [az Adatok biztonságos küldése a TLS 1.2 használatával](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)című, című további című információt. 

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynökkapcsolatok száma

Az alábbi táblázat azt mutatja be, hogy az átjárókiszolgálóval körülbelül hány ügynök kommunikálhat. A támogatás olyan ügynökökön alapul, akik körülbelül 6 másodpercenként töltenek fel körülbelül 200 KB adatot. Minden egyes tesztelt ügynök esetében az adatmennyiség körülbelül 2,7 GB naponta.

|Átjáró |Támogatott ügynökök (hozzávetőleges)|  
|--------|----------------------------------|  
|CPU: Intel Xeon processzor E5-2660 v3 \@ 2,6 GHz-es 2 mag<br> Memória: 4 GB<br> Hálózati sávszélesség: 1 Gbps| 600|  
|CPU: Intel Xeon processzor E5-2660 v3 \@ 2,6 GHz 4 mag<br> Memória: 8 GB<br> Hálózati sávszélesség: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>A Log Analytics-átjáró letöltése

A Log Analytics átjáró telepítőfájljának legújabb verzióját a Microsoft Letöltőközpontból ([letöltési hivatkozás)](https://go.microsoft.com/fwlink/?linkid=837444)vagy az Azure Portalról szerezheti be.

A Log Analytics-átjáró beszerezni az Azure Portalon, kövesse az alábbi lépéseket:

1. Böngésszen a szolgáltatások listájában, és válassza a **Log Analytics**lehetőséget. 
1. Jelöljön ki egy munkaterületet.
1. A munkaterület panel **Általános**területén válassza a **Rövid útmutató lehetőséget.** 
1. A **Munkaterülethez kapcsolódó Adatforrás kiválasztása**csoportban válassza a **Számítógépek**lehetőséget.
1. A **Direct Agent** panelen válassza a **Log Analytics-átjáró letöltése lehetőséget.**
 
   ![Képernyőkép a Log Analytics-átjáró letöltésének lépéseiről](./media/gateway/download-gateway.png)

vagy 

1. A munkaterület panel **Beállítások**területén válassza a **Speciális beállítások lehetőséget.**
1. Nyissa meg a Csatlakoztatott források**windows-kiszolgálók** **Connected Sources** > at, és válassza **a Log Analytics átjáró letöltése lehetőséget.**

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>A Log Analytics-átjáró telepítése a telepítővarázslóval

Ha átjárót szeretne telepíteni a telepítővarázslóval, kövesse az alábbi lépéseket. 

1. A célmappában kattintson duplán a **Log Analytics gateway.msi**fájlra.
1. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.

   ![Képernyőkép az Átjáró beállítása varázsló üdvözlőlapjáról](./media/gateway/gateway-wizard01.png)

1. A **Licencszerződés** lapon válassza az **Elfogadom a licencszerződés feltételeit,** hogy elfogadja a Microsoft szoftverlicenc-szerződését, majd válassza a **Tovább**gombot.
1. A **Port és a proxy** címlapján:

   a. Adja meg az átjáróhoz használandó TCP-portszámot. A telepítő ezen a portszámot használ egy bejövő szabály konfigurálásához a Windows tűzfalon.  Az alapértelmezett érték 8080.
      A portszám érvényes tartománya 1 és 65535 között van. Ha a bemenet nem tartozik ebbe a tartományba, hibaüzenet jelenik meg.

   b. Ha az átjárót kiszolgálónak proxyn keresztül kell kommunikálnia, adja meg azt a proxycímet, amelyhez az átjárónak csatlakoznia kell. Adja meg például a következőt: `http://myorgname.corp.contoso.com:80`.  Ha üresen hagyja ezt a mezőt, az átjáró megpróbál közvetlenül csatlakozni az internethez.  Ha a proxykiszolgáló hitelesítést igényel, adjon meg egy felhasználónevet és egy jelszót.

   c. Válassza a **Tovább lehetőséget.**

   ![Képernyőkép az átjáróproxy konfigurációjáról](./media/gateway/gateway-wizard02.png)

1. Ha nincs engedélyezve a Microsoft Update, megjelenik a Microsoft Update lap, és engedélyezheti azt. Jelöljön ki egy elemet, majd válassza a **Tovább**gombot. Ellenkező esetben folytassa a következő lépéssel.
1. A **Célmappa** lapon hagyja meg az alapértelmezett C:\Program Files\OMS átjáró mappát, vagy adja meg azt a helyet, ahová telepíteni szeretné az átjárót. Ezután válassza a **Tovább**gombot.
1. A **Telepítésre kész** lapon válassza a **Telepítés**lehetőséget. Ha a Felhasználói fiókok felügyelete telepítési engedélyt kér, válassza az **Igen**lehetőséget.
1. A telepítés befejezése után válassza a **Befejezés gombot.** A szolgáltatás futásának ellenőrzéséhez nyissa meg a services.msc beépülő modult, és ellenőrizze, hogy az **OMS Gateway** megjelenik-e a szolgáltatások listájában, és hogy az állapota **fut-e.**

   ![Képernyőkép a helyi szolgáltatásokról, amely azt mutatja, hogy az OMS-átjáró fut](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>A Log Analytics átjáró telepítése a parancssorból

Az átjáró letöltött fájlja egy Windows Installer csomag, amely támogatja a parancssorból vagy más automatizált módszerből történő csendes telepítést. Ha nem ismeri a Windows Installer szabványos parancssori beállításait, olvassa el a [Parancssori beállítások című témakört.](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)
 
Az alábbi táblázat kiemeli a telepítő által támogatott paramétereket.

|Paraméterek| Megjegyzések|
|----------|------| 
|PORTSZÁMA | TCP-port száma az átjáró figyeléséhez |
|Proxy | A proxykiszolgáló IP-címe |
|INSTALLDIR TELEPÍTÉSE | Teljesen minősített elérési út az átjárószoftver-fájlok telepítési könyvtárának megadásához |
|USERNAME | Hitelesítés proxykiszolgálóval történő hitelesítéshez |
|Jelszó | A proxyval történő hitelesítéshez szükséges felhasználói azonosító jelszava |
|LicenseAccepted | 1 érték megadása **a** licencszerződés elfogadásának ellenőrzéséhez |
|HASAUTH KÖZÖTT | **1-es** érték megadása FELHASZNÁLÓNÉV/JELSZÓ paraméterek megadása esetén |
|HASPROXY | **1-es** érték megadása a **PROXY** paraméter IP-címének megadásakor |

Ha csendben szeretné telepíteni az átjárót, és egy adott proxycímmel, portszámmal szeretné konfigurálni, írja be a következőt:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

A /qn parancssori kapcsoló valelrejti a beállítást, a /qb parancs a csendes telepítés során jeleníti meg a beállítást.  

Ha a proxyval való hitelesítéshez hitelesítő adatokat kell megadnia, írja be a következőt:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

A telepítés után a következő PowerShell-parancsmagok használatával ellenőrizheti, hogy a beállítások at elfogadták (kivéve a felhasználónevet és a jelszót):

- **Get-OMSGatewayConfig** – Azt a TCP-portot adja vissza, amelyen az átjáró figyel.
- **Get-OMSGatewayRelayProxy** – Annak a proxykiszolgálónak az IP-címét adja vissza, amellyel kommunikál.

## <a name="configure-network-load-balancing"></a>Hálózati terheléselosztás konfigurálása

Az átjáró t magas rendelkezésre állású hálózati terheléselosztás (NLB) használatával konfigurálhatja a Microsoft [Hálózati terheléselosztás (NLB),](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing) [az Azure Load Balancer](../../load-balancer/load-balancer-overview.md)vagy a hardveralapú terheléselosztók használatával. A terheléselosztó úgy kezeli a forgalmat, hogy átirányítja a kért kapcsolatokat a Log Analytics-ügynökökvagy az Operations Manager felügyeleti kiszolgálók tól a csomópontjai között. Ha egy átjárókiszolgáló leáll, a forgalom más csomópontokra lesz átirányítva.

### <a name="microsoft-network-load-balancing"></a>Microsoft hálózati terheléselosztás

A Windows Server 2016 hálózati terheléselosztási fürt jének tervezéséről és központi telepítéséről a [Hálózati terheléselosztás](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)témakörben olvashat. Az alábbi lépések a Microsoft hálózati terheléselosztási fürtjének konfigurálását ismertetik.  

1. Jelentkezzen be a hálózati terheléselosztási fürt höz rendszergazdai fiókkal rendelkező Windows-kiszolgálóra.  
2. Nyissa meg a Hálózati terheléselosztás kezelőjét a Kiszolgálókezelőben, kattintson **az Eszközök**menü hálózati **terheléselosztáskezelő parancsára.**
3. Ha egy Log Analytics-átjárókiszolgálót a Microsoft Monitoring Agent telepített kiszolgálóval szeretne csatlakoztatni, kattintson a jobb gombbal a fürt IP-címére, majd kattintson az **Állomás hozzáadása a fürthöz parancsra.** 

    ![Hálózati terheléselosztáskezelő – állomás hozzáadása a fürthöz](./media/gateway/nlb02.png)
 
4. Adja meg a csatlakoztatni kívánt átjárókiszolgáló IP-címét. 

    ![Hálózati terheléselosztáskezelő – állomás hozzáadása a fürthöz: Csatlakozás](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Az Azure Load Balancer tervezéséről és üzembe helyezéséről a [Mi az Azure Load Balancer?](../../load-balancer/load-balancer-overview.md) Az alapszintű terheléselosztó üzembe helyezéséhez kövesse az ebben a [rövid útmutatóban](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) ismertetett lépéseket, kivéve a **Háttérkiszolgálók létrehozása**című szakaszban ismertetett lépéseket.   

> [!NOTE]
> Az Azure Load Balancer konfigurálása az **alapszintű termékváltozat**használatával, megköveteli, hogy az Azure virtuális gépek egy rendelkezésre állási csoporthoz tartoznak. A rendelkezésre állási csoportokról a [Windows virtuális gépek azure-beli elérhetőségének kezelése](../../virtual-machines/windows/manage-availability.md)című témakörben olvashat bővebben. Ha meglévő virtuális gépeket szeretne hozzáadni egy rendelkezésre állási csoporthoz, olvassa el az [Azure Resource Manager virtuálisgép-rendelkezésre állási készletbeállítása.](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)
> 

A terheléselosztó létrehozása után létre kell hozni egy háttérkészletet, amely egy vagy több átjárókiszolgálóra osztja el a forgalmat. Kövesse a rövid útmutató ban leírt [lépéseket: Erőforrások létrehozása a terheléselosztóhoz](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)című szakaszban.  

>[!NOTE]
>Az állapotminta konfigurálásakor az átjárókiszolgáló TCP-portját kell használni. Az állapotminta dinamikusan hozzáadja vagy eltávolítja az átjárókiszolgálókat a terheléselosztó elforgatásából az állapotellenőrzésekre adott válaszuk alapján. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>A Log Analytics-ügynök és az Operations Manager felügyeleti csoport konfigurálása

Ebben a szakaszban megtudhatja, hogyan konfigurálhatja a közvetlenül csatlakoztatott Log Analytics-ügynököket, egy Operations Manager felügyeleti csoportot vagy az Azure Automation hibrid runbook-feldolgozókat a Log Analytics átjáróval az Azure Automation vagy a Log Analytics szolgáltatással való kommunikációhoz.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Önálló Log Analytics-ügynök konfigurálása

A Log Analytics-ügynök konfigurálásakor cserélje le a proxykiszolgáló értékét a Log Analytics átjárókiszolgáló IP-címére és portszámára. Ha több átjárókiszolgálót telepített egy terheléselosztó mögött, a Log Analytics-ügynök proxykonfigurációja a terheléselosztó virtuális IP-címe.  

>[!NOTE]
>A Log Analytics-ügynök telepítéséhez az átjáróra és a Log Analytics szolgáltatáshoz közvetlenül csatlakozó Windows-számítógépekre a [Windows-számítógépek csatlakoztatása az Azure-beli Log Analytics szolgáltatáshoz](agent-windows.md)című témakörben található. Linux-számítógépek csatlakoztatásához olvassa el [a Linux-számítógépek csatlakoztatása az Azure Monitorhoz](agent-linux.md). 
>

Miután telepítette az ügynököt az átjárókiszolgálóra, konfigurálja úgy, hogy jelentse az átjáróval kommunikáló munkaterületi vagy munkaterületi ügynököknek. Ha a Log Analytics Windows-ügynök nincs telepítve az átjáróra, a 300-as esemény az OMS-átjáró eseménynaplójába kerül, jelezve, hogy az ügynököt telepíteni kell. Ha az ügynök telepítve van, de nincs beállítva, hogy az azon keresztül kommunikáló ügynökökkel azonos munkaterületre jelentsen, a 105-ös esemény ugyanannak a naplónak van megírva, jelezve, hogy az átjárón lévő ügynököt úgy kell beállítani, hogy ugyanarra a munkaterületre jelentsen, mint az ügynökök, akik kommunikáljon az átjáróval.

A konfiguráció befejezése után indítsa újra az **OMS-átjáró** szolgáltatást a módosítások alkalmazásához. Ellenkező esetben az átjáró elutasítja azokat az ügynököket, amelyek kommunikálni próbálnak a Log Analytics szolgáltatással, és az OMS-átjáró eseménynaplójában jelenti a 105-ös eseményt. Ez akkor is megtörténik, ha munkaterületet ad hozzá vagy távolít el az átjárókiszolgáló ügynökkonfigurációjából.

Az Automation Hybrid Runbook Worker szolgáltatással kapcsolatos információkért [lásd: Erőforrások automatizálása az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó használatával.](../../automation/automation-hybrid-runbook-worker.md)

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Az Operations Manager konfigurálása, ahol minden ügynök ugyanazt a proxykiszolgálót használja

Az Operations Manager proxykonfigurációautomatikusan az Operations Manager nek jelentést készítő összes ügyintézőre vonatkozik, még akkor is, ha a beállítás üres.  

Az OMS Gateway használatához az Operations Manager támogatásához a következőkre van szüksége:

* A Microsoft Monitoring Agent (8.0.10900.0-s vagy újabb verzió) telepítve van az OMS-átjárókiszolgálón, és ugyanazzal a Log Analytics-munkaterületekkel van konfigurálva, amelyeknek a felügyeleti csoport be van állítva.
* Internetkapcsolat. Másik lehetőségként az OMS-átjárót az internethez csatlakozó proxykiszolgálóhoz kell csatlakoztatni.

> [!NOTE]
> Ha nem ad meg értéket az átjáróhoz, az üres értékek az összes ügyintézőhöz kerülnek.
>

Ha az Operations Manager felügyeleti csoport első alkalommal regisztrál egy Log Analytics-munkaterülettel, nem fogja látni a felügyeleti csoport proxykonfigurációjának megadását az Operatív konzolon. Ez a beállítás csak akkor érhető el, ha a felügyeleti csoport regisztrálva van a szolgáltatásban.  

Az integráció konfigurálásához frissítse a rendszerproxy-konfigurációt a Netsh használatával azon a rendszeren, amelyen az operatív konzolt futtatja, valamint a felügyeleti csoport összes felügyeleti kiszolgálóján. Kövesse az alábbi lépéseket:

1. Nyisson meg egy rendszergazda jogú parancssort:

   a. Válassza **a Start** és írja be a **cmd parancsot.**  

   b. Kattintson a jobb gombbal **a Parancssor elemre,** és válassza **a Futtatás rendszergazdaként parancsot.**  

1. Írja be a következő parancsot:

   `netsh winhttp set proxy <proxy>:<port>`

A Log Analytics szolgáltatással való integráció `netsh winhttp reset proxy`befejezése után távolítsa el a módosítást a futtatásával. Ezután az operatív konzolon használja a **Proxykiszolgáló konfigurálása** beállítást a Log Analytics átjárókiszolgáló megadásához. 

1. Az Operations Manager konzol **Operations Management Suite területén**válassza a **Kapcsolat**lehetőséget, majd válassza **a Proxykiszolgáló konfigurálása**lehetőséget.

   ![Képernyőkép az Operations Manager alkalmazásról, a Proxykiszolgáló konfigurálása beállítás idáig jelző](./media/gateway/scom01.png)

1. Válassza **a Proxykiszolgáló használata az Operations Management Suite eléréséhez** lehetőséget, majd adja meg a Log Analytics átjárókiszolgáló IP-címét vagy a terheléselosztó virtuális IP-címét. Ügyeljen arra, hogy az `http://`előtaggal kezdje.

   ![Képernyőkép az Operations Manager ről, amely a proxykiszolgáló címét mutatja](./media/gateway/scom02.png)

1. Válassza a **Finish** (Befejezés) elemet. Az Operations Manager felügyeleti csoport most úgy van beállítva, hogy az átjárókiszolgálón keresztül kommunikáljon a Log Analytics szolgáltatással.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Az Operations Manager konfigurálása, ahol bizonyos ügynökök proxykiszolgálót használnak

Nagy vagy összetett környezetekben előfordulhat, hogy csak adott kiszolgálók (vagy csoportok) a Log Analytics átjárókiszolgáló használata.  Ezeknél a kiszolgálóknál nem frissítheti közvetlenül az Operations Manager-ügynököt, mert ezt az értéket a felügyeleti csoport globális értéke felülírja.  Ehelyett felülbírálja az értékek lenyomásához használt szabályt.  

> [!NOTE] 
> Ezt a konfigurációs technikát akkor használja, ha több Log Analytics-átjárókiszolgálót szeretne engedélyezni a környezetében.  Például megkövetelheti, hogy adott Log Analytics-átjárókiszolgálókat regionális alapon adjanak meg.
>

Adott kiszolgálók vagy csoportok konfigurálása a Log Analytics átjárókiszolgáló használatára: 

1. Nyissa meg az Operations Manager konzolt, és válassza ki a **Authoring munkaterületet.**  
1. A Szerzői munkaterületen válassza a **Szabályok**lehetőséget. 
1. Az Operations Manager eszközsoron kattintson a **Hatókör** gombra. Ha ez a gomb nem érhető el, győződjön meg arról, hogy egy objektumot jelölt ki, nem egy mappát a **Figyelés** ablaktáblán. A **Hatókörkezelési csomag objektumai** párbeszédpanelen megjelenik a gyakori célzott osztályok, csoportok vagy objektumok listája. 
1. A **Keres mezőben** írja be az **Állapotfigyelő lapot,** és válassza ki a listából. Válassza **az OK gombot.**  
1. Keresse meg az **Advisor proxybeállítási szabályát.** 
1. Az Operations Manager eszköztáron válassza a **Felülbírálások** lehetőséget, majd **mutasson a Szabály felülbírálása\Az osztály: Állapotszolgáltatás egy adott objektumesetén,** és jelöljön ki egy objektumot a listából.  Vagy hozzon létre egy egyéni csoportot, amely tartalmazza a felülbírálási alkalmazni kívánt kiszolgálók állapotszolgáltatás-objektumát. Ezután alkalmazza a felülírást az egyéni csoportra.
1. A **Tulajdonságok felülbírálása** párbeszédpanelen jelölje be a **WebProxyAddress** paraméter melletti **Felülírás** oszlopot.  Az **Érték felülbírálása** mezőbe írja be a Log Analytics átjárókiszolgáló URL-címét. Ügyeljen arra, hogy az `http://`előtaggal kezdje.  

    >[!NOTE]
    > Nem kell engedélyeznie a szabályt. A Microsoft System Center Advisor Secure Reference Override felügyeleti csomag felülbírálása már automatikusan kezeli a Microsoft System Center Advisor Monitoring Server Group vezérlőcsomagjában.
    > 

1. Válasszon ki egy felügyeleti csomagot a **Cél kiválasztása felügyeleti csomag** listájából, vagy hozzon létre egy új, lezáratlan felügyeleti csomagot az **Új**lehetőség kiválasztásával. 
1. Ha elkészült, kattintson az **OK** gombra. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurálás az Automation hibrid runbook-feldolgozóihoz

Ha az Automation hybrid Runbook-feldolgozók a környezetben, kövesse az alábbi lépéseket az átjáró konfigurálásához a dolgozók támogatása.

Az egyes régiók URL-címének megkereséséhez tekintse meg az Automation [dokumentációjának A hálózat konfigurálása](../../automation/automation-hybrid-runbook-worker.md#network-planning) című szakaszát.

Ha a számítógép automatikusan hibrid Runbook-feldolgozóként van regisztrálva, például ha a frissítéskezelési megoldás engedélyezve van egy vagy több virtuális géphez, kövesse az alábbi lépéseket:

1. Adja hozzá a feladat futásidejű adatszolgáltatás URL-címeit a Log Analytics-átjáró engedélyezett állomáslistájához. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics átjárószolgáltatást a következő PowerShell-parancsmag használatával:`Restart-Service OMSGatewayService`

Ha a számítógép csatlakozik az Azure Automationhez a hibrid Runbook-feldolgozó regisztrációs parancsmag használatával, kövesse az alábbi lépéseket:

1. Adja hozzá az ügynökszolgáltatás regisztrációs URL-címét a Log Analytics-átjáró engedélyezett állomáslistájához. Például:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adja hozzá a feladat futásidejű adatszolgáltatás URL-címeit a Log Analytics-átjáró engedélyezett állomáslistájához. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics átjárószolgáltatást.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

A parancsmagokkal elvégezheti a feladatokat a Log Analytics-átjáró konfigurációs beállításainak frissítéséhez. A parancsmagok használata előtt győződjön meg arról, hogy:

1. Telepítse a Log Analytics átjárót (Microsoft Windows Installer).
1. Nyisson meg egy PowerShell-konzolablakot.
1. Importálja a modult a következő parancs beírásával:`Import-Module OMSGateway`
1. Ha az előző lépésben nem történt hiba, a modul importálása sikeresen megtörtént, és a parancsmagok használhatók. Adja meg`Get-Module OMSGateway`
1. Miután a parancsmagokkal módosította, indítsa újra az OMS átjáró szolgáltatást.

A 3. A hiba akkor fordulhat elő, ha a PowerShell nem találja a modult. A modul az OMS átjáró telepítési útvonalán található: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Parancsmag** | **Paraméterek** | **Leírás** | **Példa** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Kulcs |Leveszi a szolgáltatás konfigurációját |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Kulcs (kötelező) <br> Érték |A szolgáltatás konfigurációjának módosítása |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Leveszi a reléproxy címét |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Cím<br> Felhasználónév<br> Jelszó (biztonságos karakterlánc) |A továbbító proxy címének (és hitelesítő adatainak) beállítása |1. Állítsa be a továbbító proxy és a hitelesítő adatok:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Állítson be olyan továbbítóproxyt, amelynem igényel hitelesítést:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Törölje a relé proxy beállítás:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Beszerzi a jelenleg engedélyezett állomást (csak a helyileg konfigurált engedélyezett állomást, nem pedig az automatikusan letöltött engedélyezett állomásokat) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Állomás (kötelező) |Az állomás hozzáadása az engedélyezett listához |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Állomás (kötelező) |Eltávolítja az állomást az engedélyezett listáról |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Tárgy (kötelező) |Hozzáadja a tulajdonos ügyféltanúsítványt az engedélyezett listához |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Tárgy (kötelező) |Eltávolítja az ügyféltanúsítvány tulajdonosát az engedélyezett listáról |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Beszerzi a jelenleg engedélyezett ügyféltanúsítvány-tulajdonosokat (csak a helyileg konfigurált engedélyezett tantárgyakat, az engedélyezett tárgyakat nem automatikusan letöltve) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Hibaelhárítás

Az átjáró által naplózott események összegyűjtéséhez telepítenie kell a Log Analytics-ügynököt.

![Képernyőkép az Eseménynapló listájáról a Log Analytics átjárónaplójában](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics-átjáró eseményazonosítói és leírásai

Az alábbi táblázat a Log Analytics-átjárónapló-események eseményazonosítóit és leírását mutatja be.

| **Id** | **Leírás** |
| --- | --- |
| 400 |Minden olyan alkalmazáshiba, amely nem rendelkezik konkrét azonosítóval. |
| 401 |Rossz konfiguráció. Például listenPort = "szöveg" egész szám helyett. |
| 402 |Kivétel a TLS-kézfogási üzenetek elemzésében. |
| 403 |Hálózati hiba. Például nem tud csatlakozni a célkiszolgálóhoz. |
| 100 |Általános információk. |
| 101 |A szolgáltatás elindult. |
| 102 |A szolgáltatás leállt. |
| 103 |Http CONNECT parancs érkezett az ügyféltől. |
| 104 |Nem HTTP CONNECT parancs. |
| 105 |A célkiszolgáló nem szerepel az engedélyezett listában, vagy a célport nem biztonságos (443). <br> <br> Győződjön meg arról, hogy az OMS-átjárókiszolgáló MMA-ügynöke és az OMS-átjáróval kommunikáló ügynökök ugyanahhoz a Log Analytics-munkaterülethez csatlakoznak. |
| 105 |HIBA TcpConnection – Érvénytelen ügyféltanúsítvány: CN=Gateway. <br><br> Győződjön meg arról, hogy az OMS Gateway 1.0.395.0-s vagy újabb verzióját használja. Győződjön meg arról is, hogy az OMS-átjárókiszolgáló MMA-ügynöke és az OMS-átjáróval kommunikáló ügynökök ugyanahhoz a Log Analytics-munkaterülethez csatlakoznak. |
| 106 |Nem támogatott TLS/SSL protokollverzió.<br><br> A Log Analytics átjáró csak a TLS 1.0, TLS 1.1 és 1.2-es támogatott. Nem támogatja az SSL-t.|
| 107 |A TLS-munkamenet ellenőrizve lett. |

### <a name="performance-counters-to-collect"></a>Összegyűjtő teljesítményszámlálók

Az alábbi táblázat a Log Analytics-átjáró számára elérhető teljesítményszámlálókat mutatja be. A Számlálók hozzáadásához használja a Teljesítményfigyelőt.

| **Név** | **Leírás** |
| --- | --- |
| Log Analytics átjáró/aktív ügyfélkapcsolat |Aktív ügyfélhálózati (TCP) kapcsolatok száma |
| Log Analytics átjáró/hibaszám |A hibák száma |
| Log Analytics átjáró/csatlakoztatott ügyfél |Csatlakoztatott ügyfelek száma |
| Log Analytics átjáró/elutasítás száma |A TLS-érvényesítési hiba miatti elutasítások száma |

![Képernyőkép a Log Analytics átjárófelületéről, amely en teljesítményszámlálók láthatók](./media/gateway/counters.png)

## <a name="assistance"></a>Segítség

Amikor be van jelentkezve az Azure Portalon, segítséget kaphat a Log Analytics-átjáróval vagy bármely más Azure-szolgáltatással vagy -szolgáltatással kapcsolatban.
Ha segítségre van szüksége, jelölje ki a kérdőjel ikont a portál jobb felső sarkában, majd válassza az **Új támogatási kérelem**lehetőséget. Ezután töltse ki az új támogatási kérelem űrlapot.

![Képernyőkép egy új támogatási kérelemről](./media/gateway/support.png)

## <a name="next-steps"></a>További lépések

[Adatforrások hozzáadásával](../../azure-monitor/platform/agent-data-sources.md) adatokat gyűjthet a csatlakoztatott forrásokból, és tárolhatja az adatokat a Log Analytics-munkaterületen.
