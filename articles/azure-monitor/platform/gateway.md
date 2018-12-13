---
title: A Log Analytics-átjáró használatával számítógépek csatlakoztatása |} A Microsoft Docs
description: Az eszközök és az Operations Manager által figyelt számítógépek csatlakoztatása a Log Analytics-átjáróval adatokat küldeni az Azure Automation és a Log Analytics szolgáltatást Ha nem rendelkeznek Internet-hozzáféréssel.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 5294d5919b6d4d80c61e183866409123a9edbb60
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082663"
---
# <a name="connect-computers-without-internet-access-using-the-log-analytics-gateway"></a>Számítógépek csatlakoztatása a Log Analytics-átjáró Internet-hozzáférés nélkül
Ez a dokumentum ismerteti az Azure Automation szolgáltatással kommunikáció konfigurálása és a Log Analytics használatával az a Log Analytics-átjáró, amikor közvetlen csatlakoztatva, vagy az Operations Manager figyelt számítógépek nem rendelkeznek Internet-hozzáférés.  A Log Analytics-átjáró, amely, amely támogatja a HTTP-bújtatás a HTTP-csatlakozási paranccsal továbbítsa HTTP-proxyt, adatok gyűjtéséhez és küldhet az Azure Automation és a Log Analytics a felhasználók nevében.  

A Log Analytics-átjáró támogatja:

* Az Azure Automation hibrid Runbook-feldolgozók  
* A Log Analytics-munkaterülethez közvetlenül kapcsolódó Microsoft Monitoring Agent Windows-számítógépek
* A Linuxhoz készült Log Analytics-ügynököt a Linux rendszerű számítógépek közvetlenül csatlakozik a Log Analytics-munkaterület  
* A System Center Operations Manager 2012 SP1 UR7, az Operations Manager 2012 R2 UR3, az Operations Manager 2016 és az Operations Manager verzióra 1801-re felügyeleti csoport integrálva a Log Analytics használatával.  

Ha az informatikai biztonsági szabályzatok nem teszi lehetővé a számítógépek csatlakozni az internethez, a terminálok (POS) eszközök vagy a kiszolgálók támogatása az IT-szolgáltatások, például a hálózat, de az Azure Automation és a Log Analytics kezelni és megfigyelni őket csatlakoztatni kell , a Log Analytics átjáró és a konfigurációs és a továbbítási adatok saját nevükben való közvetlen kommunikációhoz konfigurálható.  Ha ezek a számítógépek közvetlenül csatlakozhat a Log Analytics-munkaterületet a Log Analytics-ügynökkel rendelkező vannak konfigurálva, az összes számítógép inkább kommunikál a Log Analytics-átjáró.  Az átjáró átviszi az adatokat a az ügynököket a szolgáltatás közvetlenül, nem elemzi az adatokat átvitel közben.

Ha az Operations Manager felügyeleti csoport integrálva van a Log Analytics, a felügyeleti kiszolgálók konfigurálható szeretne csatlakozni a Log Analytics-átjáró konfigurációs adatokat fogad és küld attól függően, a megoldás engedélyezte az összegyűjtött adatokat.  Operations Manager-ügynökök néhány adatot, például az Operations Manager riasztásokat, konfigurációelemzés, példányok és kapacitásadatait küldenek a felügyeleti kiszolgálónak. Egyéb nagy mennyiségű adatok, például az IIS-naplók, a teljesítmény és a biztonsági események közvetlenül a Log Analytics-átjáró érkeznek.  Ha egy vagy több Operations Manager átjáró kiszolgálók a DMZ-t vagy más elkülönített hálózat nem megbízható rendszerek figyeléséhez üzembe helyezett, egy Log Analytics-átjáró nem tud kommunikálni.  Az Operations Manager átjáró kiszolgálók csak jelenthetik a felügyeleti kiszolgálóra.  Ha az Operations Manager felügyeleti csoport van konfigurálva a Log Analytics-átjáróval folytatott kommunikációban, minden ügynök által felügyelt számítógépre, amely konfigurálva van még gyűjt adatokat a Log Analytics, a rendszer automatikusan továbbítja a proxy konfigurációs adatokat Ha a beállítás értéke üres.    

Magas rendelkezésre állás biztosításához közvetlenül csatlakoztatott vagy a Log Analytics-átjárón keresztül kommunikáló Operations Management csoport, használhatja a hálózati terheléselosztás és a forgalom elosztása több átjárókiszolgáló átirányításához.  Egy átjáró kiszolgáló leáll, ha a rendszer átirányítja a forgalmat egy másik csomópont érhető el.  

A Log Analytics-ügynököket a számítógépen, ahhoz, hogy azonosítsa a Szolgáltatásvégpontok szükséges kommunikálni, és monitorozása a Log Analytics-átjáró a teljesítmény vagy események adatainak elemzése a Log Analytics-átjáró szükséges.

Minden ügynök hálózati kapcsolat és az átjáró rendelkeznie kell, hogy az ügynökök automatikusan áthelyezhet adat és az átjáró. Az átjáró telepítése tartományvezérlőn nem ajánlott.

Az alábbi ábrán az adatfolyam a közvetlen ügynökök Azure Automation és a Log Analytics használatával az átjáró-kiszolgálót.  Ügynökök azok felel meg a Log Analytics-átjáró történő kommunikációra van beállítva, a szolgáltatás ugyanazt a portot proxykiszolgáló-konfigurációval kell rendelkeznie.  

![közvetlen ügynökkommunikációhoz a szolgáltatások diagramja](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán az adatfolyam egy Operations Manager felügyeleti csoportból a Log Analytics szolgáltatásba.   

![Az Operations Manager kommunikál a Log Analytics-diagram](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Előfeltételek

A Log Analytics-átjáró futtatására kijelölését, ha a számítógép az alábbiakkal kell rendelkeznie:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET-keretrendszer 4.5
* Legalább egy 4 magos processzor- és 8 GB memóriát 
* Log Analytics-ügynököket Windows 

### <a name="language-availability"></a>Nyelvi rendelkezésre állása

A Log Analytics-átjáró a következő nyelveken érhető el:

- kínai (egyszerűsített)
- kínai (hagyományos)
- cseh
- holland
- Angol
- francia
- német
- magyar
- olasz
- japán
- koreai
- lengyel
- portugál (brazíliai)
- portugál (általános)
- orosz
- Spanyol (nemzetközi)

### <a name="supported-encryption-protocols"></a>Támogatott titkosítási protokollokkal
A Log Analytics-átjáró csak a Transport Layer Security (TLS) 1.0, 1.1 és 1.2-es támogatja.  Nem támogatja a Secure Sockets Layer (SSL).  A Log Analytics az átvitt adatok biztonságának biztosítása érdekében, azt javasoljuk, hogy az átjáró használatához legalább konfigurálása Transport Layer Security (TLS) 1.2-es. Biztonsági rés található régebbi verziói a TLS/Secure Sockets Layer (SSL), és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**.  További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynök-kapcsolatok száma
Az alábbi táblázat az átjáró-kiszolgálóval való kommunikációhoz ügynökök támogatott száma emeli ki.  Ez a támogatás közel 200KB méretű adatot feltölteni 6 másodpercenként ügynökök alapul. Tesztelt ügynök / adatmennyiség naponta körülbelül 2.7-es GB.

|Átjáró |Támogatott ügynökök száma KB.|  
|--------|----------------------------------|  
|-CPU: Intel XEON v3 CPU E5-2660 \@ 2,6 GHz-es 2 mag<br> -Memória: 4 GB<br> -Hálózati sávszélesség: 1 GB/s| 600|  
|-CPU: Intel XEON v3 CPU E5-2660 \@ 2,6 GHz-es 4 mag<br> -Memória: 8 GB<br> -Hálózati sávszélesség: 1 GB/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>A Log Analytics-átjáró letöltése

Kétféleképpen a Log Analytics-átjáró telepítési fájl legújabb verziójának beszerzéséhez.

1. Letölthető a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

1. Töltse le az Azure Portalról.  Miután bejelentkezik az Azure Portalon:  

   1. Keresse meg a szolgáltatások listájában, és válassza ki **Log Analytics**.  
   1. Jelöljön ki egy munkaterületet.
   1. A munkaterület panel **általános**, kattintson a **gyors üzembe helyezés**.
   1. A **válasszon egy a munkaterülethez csatlakoztatandó adatforrást**, kattintson a **számítógépek**.
   1. Az a **közvetlen ügynök** panelen kattintson a **töltse le a Log Analytics-átjáró**.<br><br> ![Töltse le a Log Analytics-átjáró](./media/gateway/download-gateway.png)

vagy 

   1. A munkaterület panel **beállítások**, kattintson a **speciális beállítások**.
   1. Navigáljon a **csatlakoztatott források** > **Windows kiszolgálók** kattintson **töltse le a Log Analytics-átjáró**.

## <a name="install-the-log-analytics-gateway"></a>A Log Analytics-átjáró telepítése

Az átjáró telepítéséhez hajtsa végre az alábbi lépéseket.  Ha egy korábbi verzióját telepítette, korábbi nevén *Log Analytics-továbbító*, erre a kiadásra lesz frissítve.  

1. Kattintson duplán a célmappa **Log Analytics gateway.msi**.
1. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.<br><br> ![Átjárókiszolgáló telepítése varázsló](./media/gateway/gateway-wizard01.png)<br> 
1. A a **licencszerződés** lapon jelölje be **elfogadom a licencszerződés feltételeit** fogadja el a végfelhasználói LICENCSZERZŐDÉST, majd **tovább**.
1. Az a **Port és a proxykiszolgáló címét** oldalon:
   1. Írja be az átjáró használni a TCP-port száma. A telepítő egy bejövő szabályt konfigurálja, a port számát, a Windows tűzfalon.  Az alapértelmezett érték: 8080-as.
      A portnak a számát, az érvényes értéktartomány: 1-65535. Ha a bemeneti nem esik a tartományba, hibaüzenet jelenik meg.
   1. Igény szerint ha a kiszolgáló, amelyen az átjáró telepítve van egy proxyn keresztül történő kommunikációhoz van szüksége, írja be a proxykiszolgáló címét, amelyen az átjáró kell csatlakoztatnia. Például: `http://myorgname.corp.contoso.com:80`.  Ha üres, akkor az átjáró megpróbálja közvetlenül csatlakozik az internethez.  Ha a proxykiszolgáló hitelesítést igényel, adjon meg egy felhasználónevet és jelszót.<br><br> ![Átjáró varázsló proxy konfigurálása](./media/gateway/gateway-wizard02.png)<br>   
   1. Kattintson a **Tovább**gombra.
1. Ha nincs engedélyezve a Microsoft Update, a Microsoft Update lapon jelenik meg, ahol kiválaszthatja az engedélyezéshez. Jelölje ki, és kattintson a **tovább**. Ellenkező esetben folytassa a következő lépéssel.
1. Az a **célmappa** lapon hagyja bejelölve az alapértelmezett mappa: C:\Program Files\OMS átjáró, vagy írja be a helyet, ahol szeretné telepíteni az átjárót, és kattintson a **tovább**.
1. Az a **készen áll a telepítésre** kattintson **telepítése**. Felhasználói fiókok felügyeletének telepítése kér engedélyt jelenhet meg. Ha igen, kattintson a **Igen**.
1. Kattintson a telepítés után **Befejezés**. Ellenőrizheti, hogy a szolgáltatás fut, nyissa meg a services.msc beépülő modult, és ellenőrizze, hogy **Log Analytics-átjáró** megjelenik a listán, a szolgáltatások, és azt állapota van **futó**.<br><br> ![Szolgáltatások – Log Analytics-átjáró](./media/gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Hálózati terheléselosztás beállítása 
Beállíthatja, hogy az átjáró magas rendelkezésre álláshoz a hálózati terheléselosztás (NLB) a Microsoft hálózati terheléselosztás (NLB) vagy a hardveres terheléselosztók használatával.  A terheléselosztó forgalmat kezeli a csomópontok között a Log Analytics-ügynökök kért kapcsolatot, vagy az Operations Manager felügyeleti kiszolgálók átirányításával. Egy átjáró kiszolgáló leáll, ha más csomópontokhoz átirányítja a forgalmat.

Ismerje meg, hogyan tervezhet és a egy Windows Server 2016 hálózati terheléselosztási fürt üzembe helyezése, lásd: [a hálózati terheléselosztás](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Az alábbi lépéseket a Microsoft hálózati terheléselosztási fürt konfigurálását ismertetik.  

1. Jelentkezzen be a Windows server, amely tagja a rendszergazdák rendszergazdai fiókkal az NLB-fürt.  
1. Hálózati terheléselosztás kezelőjének megnyitása a Kiszolgálókezelőben, kattintson a **eszközök**, és kattintson a **a hálózati terheléselosztás kezelője**.
1. A Microsoft Monitoring Agent telepítve van egy Log Analytics-átjáró kiszolgáló kapcsolódni, kattintson a jobb gombbal a fürt IP-címét, és kattintson **gazdagép hozzáadása fürthöz**.<br><br> ![Hálózati terheléselosztás kezelője – betöltéséhez adja hozzá a gazdagépet a fürthöz](./media/gateway/nlb02.png)<br> 
1. Adja meg az IP-cím, az átjárókiszolgáló, amely kapcsolódni szeretne.<br><br> ![Hálózati terheléselosztás kezelője – a gazdagépet a fürthöz: csatlakozás](./media/gateway/nlb03.png) 
    
## <a name="configure-log-analytics-agent-and-operations-manager-management-group"></a>Log Analytics-ügynököket és az Operations Manager felügyeleti csoport konfigurálása
A következő szakasz tartalmazza a lépéseket, a közvetlenül csatlakoztatott Log Analytics-ügynökök, az Operations Manager felügyeleti csoport vagy az Azure Automation hibrid Runbook-feldolgozók konfigurálásáról a Log Analytics-átjáróval kommunikálni az Azure Automation és a Log Elemzés.  

### <a name="configure-standalone-log-analytics-agent"></a>Önálló Log Analytics-ügynök konfigurálása
Követelmények és lépések a Log Analytics-ügynök telepítése Windows-számítógépeket a Log Analytics szolgáltatásba való közvetlen csatlakozás ismertetése: [a Log Analyticshez való csatlakozáshoz Windows számítógépek](agent-windows.md) vagy a Linux rendszerű számítógépek lásd [ Linux rendszerű számítógépek csatlakoztatása a Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md). Helyett adja meg a proxykiszolgáló az ügynök telepítése közben, cserélje le ezt az értéket az IP-címét a Log Analytics-átjáró kiszolgáló vagy a port számát.  Ha hálózati terheléselosztót több átjárókiszolgáló telepített, akkor a Log Analytics proxykonfigurációjának a hálózati Terheléselosztás virtuális IP-címét.  

Az Automation hibrid Runbook-feldolgozó kapcsolatos információkért lásd: [hibrid Runbook-feldolgozó üzembe helyezése](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Az Operations Manager konfigurálása – az összes ügynöknek azonos proxykiszolgáló használata
Az Operations Manager hozzáadása az átjáró-kiszolgálót konfigurálja.  Az Operations Manager proxykonfigurációt a rendszer automatikusan alkalmazza az Operations Managerrel, jelentő összes ügynökhöz, még akkor is, ha a beállítás értéke üres.  

Az átjáró használatával támogatja az Operations Manager, kell rendelkeznie:

* A Microsoft Monitoring Agent (ügynök verziója – **8.0.10900.0** vagy újabb verzió) az átjárókiszolgáló telepítve és konfigurálva a Log Analytics-munkaterületek, amellyel kommunikálni szeretne.
* Az átjáró rendelkeznie kell internetkapcsolattal, vagy egy proxykiszolgáló, amely csatlakozni.

> [!NOTE]
> Ha nem ad meg egy értéket az átjáró, üres értékeket az ügynökök kerüljenek.
> 

Ha ez az első alkalommal regisztrál a Log Analytics-munkaterület az Operations Manager felügyeleti csoportban, adja meg a proxykiszolgáló-konfigurációt a felügyeleti csoport lehetőség nem érhető az operatív konzolon.  A felügyeleti csoportot sikeresen regisztrálni kell a szolgáltatásban ahhoz, hogy ez a lehetőség rendelkezésre álljon.  Az integráció és a felügyeleti csoportba tartozó minden felügyeleti kiszolgáló konfigurálásához Netsh használatával frissítenie kell a rendszerproxy-konfigurációt a rendszeren, amelyiken az Operatív konzolt futtatja.  

1. Nyisson meg egy emelt szintű parancssort.
   a. Lépjen a **Start** , és írja be **cmd**.
   b. Kattintson a jobb gombbal **parancssor** , és válassza ki futtató rendszergazda **.
1. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Miután befejezte a Log Analytics-integráció, eltávolíthatja a módosítás futtatásával `netsh winhttp reset proxy` , majd a **Configure proxy server** az operatív konzolon beállítással adható meg a Log Analytics-átjáró kiszolgáló. 

1. Nyissa meg az Operations Manager konzolt és a **Operations Management Suite**, kattintson a **kapcsolat** majd **proxykiszolgáló konfigurálása**.<br><br> ![Az Operations Manager – proxykiszolgáló konfigurálása](./media/gateway/scom01.png)<br> 
1. Válassza ki **proxykiszolgáló használata az Operations Management Suite eléréséhez** és írja be a Log Analytics-átjáró kiszolgálójának IP-címét vagy az NLB virtuális IP-címét. Győződjön meg arról, hogy kezdjen a `http://` előtag.<br><br> ![Az Operations Manager – a proxykiszolgáló címe](./media/gateway/scom02.png)<br> 
1. Kattintson a **Befejezés** gombra. Az Operations Manager felügyeleti csoportban most már a Log Analytics szolgáltatás az átjárókiszolgálón keresztül kommunikációra van konfigurálva.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurálja az Operations Manager - ügynökök adott proxykiszolgáló használata
Nagy vagy összetett környezetek esetében csak szüksége lehet adott kiszolgálók (vagy csoportokba) használata a Log Analytics-átjáró kiszolgáló.  Ezek a kiszolgálók, az Operations Manager-ügynök nem frissíthető közvetlenül a globális érték a felügyeleti csoport felülírja ezt az értéket.  Ehelyett kell elküldeni ezeket az értékeket használja szabály felülbírálása.  

> [!NOTE] 
> Konfigurációs ugyanezzel a módszerrel több Log Analytics-átjárókiszolgáló használatának engedélyezése a környezetben használható.  Ha például adott Log Analytics-átjáró kiszolgálókon meg kell adni a régiók szerinti alapján lehet szükség.
>  

1. Nyissa meg az Operations Manager konzolt, és válassza ki a **szerzői műveletek** munkaterületen.  
1. Válassza a szerzői műveletek munkaterületének **szabályok** , és kattintson a **hatókör** gombra az Operations Manager eszköztárán. Ha ez a gomb nem érhető el, ellenőrizze, győződjön meg arról, hogy egy objektumot, nem pedig mappát jelölt a figyelés ablaktáblán. A **felügyeleti csomag objektumai** párbeszédpanel közös célzott osztályok, csoportok és objektumok listáját jeleníti meg. 
1. Típus **Állapotfigyelő szolgáltatás** a a **keressen** mezőben, majd válassza ki a listából.  Kattintson az **OK** gombra.  
1. Keresse meg a szabály **Advisor beállítás szabály** és az operatív konzol eszköztárán kattintson **felülbírálások** , majd mutasson a **bírálja felül a Rule\For osztály egy adott objektumához: Állapotfigyelő szolgáltatás**  , és válasszon ki egy adott objektumot a listából.  Igény szerint hozhat létre egyéni csoportja az állapotfigyelő szolgáltatás objektum a kiszolgálók, ez a felülbírálás a alkalmazni, és ezután alkalmazza a felülbírálást, ehhez a csoporthoz.
1. Az a **felülbírálás tulajdonságai** párbeszédpanelen jelölje be a a **felülbírálása** oszlop melletti a **WebProxyAddress** paraméter.  Az a **felülbírálás értéke** mezőben adja meg az URL-cím, a Log Analytics átjáró kiszolgáló biztosítsa, hogy először a a `http://` előtag.  

    >[!NOTE]
    > Nem kell engedélyezése a szabály már felügyelt automatikusan egy felülbírálással, a Microsoft System Center Advisor biztonságos hivatkozás felülbírálhatja felügyeleticsomag célzó a Microsoft System Center Advisor figyelési kiszolgáló csoport található.
    >   

1. Válasszon felügyeleti csomagot a **célhelyként szolgáló felügyeleti csomag** listában, vagy hozzon létre egy új, lezáratlan felügyeleti csomag kattintva **új**. 
1. Amikor végzett a változtatásokkal, kattintson az **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Az automation hibrid feldolgozó konfigurálása
Ha Automation hibrid Runbook-feldolgozók a környezetében, az alábbi lépéseket, ideiglenes manuális kerülő támogatja azokat az átjáró konfigurálásához adjon meg.

A következő lépésekben, ismernie kell az Automation-fiókot tartalmazó Azure-régióban. Keresse meg a helyet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki az Azure Automation szolgáltatást.
1. Válassza ki a megfelelő Azure Automation-fiókot.
1. Megtekintheti a régió alatt **hely**.<br><br> ![Az Azure-portál – Automation-fiók helye](./media/gateway/location.png)  

Az alábbi táblázat segítségével azonosíthatja az egyes helyeken az URL-cím:

**Feladat futásidejű adatok URL-címei**

| **hely** | **URL-cím** |
| --- | --- |
| USA északi középső régiója |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japán |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net |

**Az ügynök szolgáltatás URL-címek**

| **hely** | **URL-cím** |
| --- | --- |
| USA északi középső régiója |ncus-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-agentservice-prod-1.azure-automation.net |
| Közép-India |cid-agentservice-prod-1.azure-automation.net |
| Japán |jpe-agentservice-prod-1.azure-automation.net |
| Ausztrália |ase-agentservice-prod-1.azure-automation.net |

Ha a számítógép regisztrálva van, a hibrid Runbook-feldolgozók automatikusan Pro opravy az Update Management megoldás használatával, kövesse az alábbi lépéseket:

1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeinek a gazdagépen engedélyezett a Log Analytics-átjáró-listája. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics-átjáró szolgáltatás a következő PowerShell-parancsmag segítségével: `Restart-Service OMSGatewayService`

Ha a számítógép egy előre telepített Azure Automation hibrid Runbook-feldolgozó regisztrációs parancsmag használatával, kövesse az alábbi lépéseket:

1. A Log Analytics-átjáró engedélyezett gazdagép listájában adja hozzá az ügynök regisztrációs URL-címe. Például:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeinek a gazdagépen engedélyezett a Log Analytics-átjáró-listája. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics-átjáró szolgáltatást.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok
Parancsmagok segítségével végezze el a feladatokat, amelyek szükségesek a Log Analytics-átjáró konfigurációs beállításainak frissítése. Mielőtt azokat használni, ügyeljen arra, hogy:

1. Telepítse a Log Analytics-átjáró (MSI).
1. Nyisson meg egy PowerShell-konzolablakot.
1. Importálja a modult, írja be ezt a parancsot: `Import-Module OMSGateway`
1. Ha nem történt hiba az előző lépésben, a modul importálása sikeresen megtörtént, és a parancsmag is használható. Típusa `Get-Module OMSGateway`
1. Miután változtatásokat parancsmagok segítségével, győződjön meg arról, újra kell indítania az átjárószolgáltatást.

Ha hibaüzenetet kap a 3. lépésben, a modul nem lett importálva. A hiba akkor fordulhat elő, ha nem található a modul PowerShell. Az átjáró telepítési elérési úton található: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **A parancsmag** | **Paraméterek** | **Leírás** | **Példa** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Kulcs |A szolgáltatás konfigurációjának beolvasása |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Kulcs (kötelező) <br> Érték |A szolgáltatás konfigurációjának módosítása |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Relay-(upstream) proxy címét |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Cím<br> Felhasználónév<br> Jelszó |Beállítja a relay (upstream) proxy címét (vagy hitelesítő adatok) |1. Állítsa be a relay-proxy és a hitelesítő adatokat:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. A relay proxy hitelesítést nem igénylő beállításait: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. A relay-proxybeállítást törléséhez:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Lekérdezi a jelenleg engedélyezett gazdagép (csak a helyileg konfigurált engedélyezett gazdagép nem tartalmaz a engedélyezett gazdagépek automatikusan letöltött) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |A gazdagép (kötelező) |Hozzáadja a gazdagépet, az engedélyezett listára |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |A gazdagép (kötelező) |A gazdagép eltávolítása az engedélyezett listára |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az engedélyezett listára vonatkoznak az ügyféltanúsítvány hozzáadása |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az ügyfél-tanúsítvány tulajdonosának távolít el az engedélyezett listára |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Lekérdezi a jelenleg engedélyezett ügyfél tanúsítványtulajdonosok (csak a helyileg konfigurált engedélyezett témák, nem tartalmaz engedélyezett témák automatikusan letöltött) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Hibaelhárítás
Az átjáró által naplózott eseményeket gyűjteni, is rendelkezik a Log Analytics-ügynököket telepíteni kell.<br><br> ![Eseménynapló – Log Analytics-átjáró napló](./media/gateway/event-viewer.png)

**Log Analytics-átjáró eseményazonosítók és -leírások**

Az alábbi táblázat bemutatja a eseményazonosítók és a Log Analytics-átjáró leírását alkalmazásnapló-események.

| **Azonosító** | **Leírás** |
| --- | --- |
| 400 |Bármely alkalmazás hiba, amely nem rendelkezik meghatározott azonosító |
| 401 |Helytelen konfiguráció. Például: listenPort = "text" helyett egész szám |
| 402 |Kivétel történt a TLS-kézfogás üzenet elemzésekor |
| 403 |Hálózati hiba történt. Például: nem lehet csatlakozni a célkiszolgálóhoz |
| 100 |Általános információk |
| 101 |Szolgáltatás elindult |
| 102 |Szolgáltatás leállt |
| 103 |HTTP-csatlakozási parancsot kapott az ügyféltől |
| 104 |Nem egy HTTP-csatlakozási parancs |
| 105 |Célkiszolgáló nem szerepel az engedélyezési listán, vagy a céloldali port nem biztonságos portot (443) <br> <br> Győződjön meg arról, hogy az átjáró kiszolgálón az MMA-ügynök és az átjáróval kommunikáló ügynökök csatlakoztatva a Log Analytics munkaterületén. |
| 105 |Hiba TcpConnection – érvénytelen ügyféltanúsítványt: CN = átjáró <br><br> Győződjön meg arról, hogy: <br>    <br> &#149;Az átjáró használata verziószám 1.0.395.0 vagy nagyobb. <br> &#149;Az MMA-ügynök az átjárókiszolgálón és az átjáróval kommunikáló ügynökök csatlakoznak a Log Analytics munkaterületén. |
| 106 |A Log Analytics-átjáró csak a TLS 1.0, TLS 1.1 és 1.2-es támogatja.  Nem támogatja az SSL. Minden nem támogatott a TLS/SSL protokoll verziója, a Log Analytics-átjáró event ID 106 állít elő.|
| 107 |A TLS-munkamenet jóvá lett hagyva. |

**Teljesítményszámlálók gyűjtése**

Az alábbi táblázat a Log Analytics-átjáró esetében rendelkezésre álló teljesítményszámlálók. A számlálókat a Teljesítményfigyelő használata is hozzáadhat.

| **Name (Név)** | **Leírás** |
| --- | --- |
| Log Analytics-aktív átjáró ügyfél-kapcsolat |Aktív ügyfél (TCP) hálózati kapcsolatok száma |
| Log Analytics-átjáró/hibák száma |Hibák száma |
| Log Analytics-átjáró/csatlakozott ügyfél |Csatlakoztatott ügyfelek száma |
| Log Analytics Gateway/elutasító száma |Bármely TLS-érvényesítési hiba miatt elutasítások száma |

![Log Analytics-átjáró teljesítményszámlálói](./media/gateway/counters.png)

## <a name="get-assistance"></a>Segítség kérése
Ha be van jelentkezve az Azure Portalon, az a Log Analytics-átjáró vagy bármely más Azure-szolgáltatást a szolgáltatás segítséget kér hozhat létre.
Segítségkérés, kattintson a kérdőjel a portál jobb felső sarkában található, és kattintson a **új támogatási kérelem**. Ezt követően fejezze be az új támogatási űrlap.

![Új támogatási kérelem](./media/gateway/support.png)

## <a name="next-steps"></a>További lépések
[Adatforrások hozzáadása](../../azure-monitor/platform/agent-data-sources.md) a csatlakoztatott forrásból gyűjthet adatokat, és tárolja a Log Analytics-munkaterületre.
