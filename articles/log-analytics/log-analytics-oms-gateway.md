---
title: Csatlakozás az OMS-átjárót használó számítógépek |} A Microsoft Docs
description: Az eszközök és az Operations Manager által felügyelt számítógépek kapcsolódnak adatokat küldeni az Azure Automation és a Log Analytics szolgáltatást Ha nem rendelkeznek Internet-hozzáféréssel az OMS-átjárót.
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
ms.date: 05/16/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: a13057769bad7fcad5f95e49102adac234ebcdb4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868939"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Csatlakoztassa a számítógépet az OMS-átjáró Internet-hozzáférés nélkül
Ez a dokumentum ismerteti a kommunikáció konfigurálása az Azure Automation és a Log Analytics használatával az OMS-átjárót, ha közvetlenül csatlakoztatott, vagy az Operations Manager figyelt számítógépek nem rendelkeznek Internet-hozzáféréssel.  Az OMS-átjáró, amely, amely támogatja a HTTP-bújtatás a HTTP-csatlakozási paranccsal továbbítsa HTTP-proxyt, adatok gyűjtéséhez és küldhet az Azure Automation és a Log Analytics a felhasználók nevében.  

Az OMS-átjáró támogatja:

* Az Azure Automation hibrid Runbook-feldolgozók  
* A Log Analytics-munkaterülethez közvetlenül kapcsolódó Microsoft Monitoring Agent Windows-számítógépek
* A Linuxhoz készült OMS-ügynök Linux rendszerű számítógépek közvetlenül csatlakozik a Log Analytics-munkaterület  
* A System Center Operations Manager 2012 SP1 UR7, az Operations Manager 2012 R2 UR3, az Operations Manager 2016 és az Operations Manager verzióra 1801-re felügyeleti csoport integrálva a Log Analytics használatával.  

Ha az informatikai biztonsági szabályzatok nem teszi lehetővé a számítógépek csatlakozni az internethez, a terminálok (POS) eszközök vagy a kiszolgálók támogatása az IT-szolgáltatások, például a hálózat, de az Azure Automation és a Log Analytics kezelni és megfigyelni őket csatlakoztatni kell , az OMS-átjáró és a konfigurációs és a továbbítási adatok saját nevükben való közvetlen kommunikációhoz konfigurálható.  Ha ezeket a számítógépeket a Log Analytics-munkaterület közvetlenül kapcsolódhat az OMS-ügynök vannak konfigurálva, az összes számítógép inkább kommunikálni az OMS-átjáró.  Az átjáró átviszi az adatokat a az ügynököket a szolgáltatás közvetlenül, nem elemzi az adatokat átvitel közben.

Ha az Operations Manager felügyeleti csoport integrálva van a Log Analytics, a felügyeleti kiszolgálók konfigurálható csatlakozni az OMS-átjáró konfigurációs adatokat fogad és küld attól függően, a megoldás engedélyezte az összegyűjtött adatokat.  Operations Manager-ügynökök néhány adatot, például az Operations Manager riasztásokat, konfigurációelemzés, példányok és kapacitásadatait küldenek a felügyeleti kiszolgálónak. Egyéb nagy mennyiségű adatok, például az IIS-naplók, a teljesítmény és a biztonsági események közvetlenül az OMS-átjárót kell küldeni.  Ha egy vagy több Operations Manager átjáró kiszolgálók a DMZ-t vagy más elkülönített hálózat nem megbízható rendszerek figyeléséhez üzembe helyezett, nem tud kommunikálni az OMS-átjárót.  Az Operations Manager átjáró kiszolgálók csak jelenthetik a felügyeleti kiszolgálóra.  Ha az Operations Manager felügyeleti csoport kommunikálni az OMS-átjáró van konfigurálva, minden ügynök által felügyelt számítógépre, amely konfigurálva van adatok gyűjtése a Log Analytics, a rendszer automatikusan továbbítja a proxy konfigurációs adatok akkor is, ha a a beállítás nincs megadva.    

Magas rendelkezésre állás biztosításához közvetlenül csatlakoztatott vagy a Log Analytics-átjárón keresztül kommunikáló Operations Management csoport, használhatja a hálózati terheléselosztás és a forgalom elosztása több átjárókiszolgáló átirányításához.  Egy átjáró kiszolgáló leáll, ha a rendszer átirányítja a forgalmat egy másik csomópont érhető el.  

Az OMS-ügynököt a számítógépen, ahhoz, hogy a Szolgáltatásvégpontok, amelyek kommunikálni, és figyelheti a teljesítmény vagy események adatainak elemzése az OMS-átjárót kell azonosítani az OMS-átjáró szükséges.

Minden ügynök hálózati kapcsolat és az átjáró rendelkeznie kell, hogy az ügynökök automatikusan áthelyezhet adat és az átjáró. Az átjáró telepítése tartományvezérlőn nem ajánlott.

Az alábbi ábrán az adatfolyam a közvetlen ügynökök Azure Automation és a Log Analytics használatával az átjáró-kiszolgálót.  Ügynökök azok egyeznek az OMS-átjáró történő kommunikációra van beállítva, a szolgáltatás ugyanazt a portot proxykiszolgáló-konfigurációval kell rendelkeznie.  

![közvetlen ügynökkommunikációhoz a szolgáltatások diagramja](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán az adatfolyam egy Operations Manager felügyeleti csoportból a Log Analytics szolgáltatásba.   

![Az Operations Manager kommunikál a Log Analytics-diagram](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Előfeltételek

Az OMS-átjáró futtatására kijelölését, ha a számítógép az alábbiakkal kell rendelkeznie:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET-keretrendszer 4.5
* Legalább egy 4 magos processzor- és 8 GB memóriát 
* Windows készült OMS-ügynök 

### <a name="language-availability"></a>Nyelvi rendelkezésre állása

Az OMS-átjáró a következő nyelveken érhető el:

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
Az OMS-átjáró csak a Transport Layer Security (TLS) 1.0, 1.1 és 1.2-es támogatja.  Nem támogatja a Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynök-kapcsolatok száma
Az alábbi táblázat az átjáró-kiszolgálóval való kommunikációhoz ügynökök támogatott száma emeli ki.  Ez a támogatás közel 200KB méretű adatot feltölteni 6 másodpercenként ügynökök alapul. Tesztelt ügynök / adatmennyiség naponta körülbelül 2.7-es GB.

|Átjáró |Támogatott ügynökök száma KB.|  
|--------|----------------------------------|  
|-CPU: Intel XEON CPU E5-2660 v3 @ 2,6 GHz-es 2 mag<br> -Memória: 4 GB<br> -Hálózati sávszélesség: 1 GB/s| 600|  
|-CPU: Intel XEON CPU E5-2660 v3 @ 2,6 GHz-es 4 mag<br> -Memória: 8 GB<br> -Hálózati sávszélesség: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Az OMS-átjáró letöltése

Kétféleképpen az OMS-átjáró telepítőjének fájl legújabb verziójának beszerzéséhez.

1. Letölthető a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Töltse le az Azure Portalról.  Miután bejelentkezik az Azure Portalon:  

   1. Keresse meg a szolgáltatások listájában, és válassza ki **Log Analytics**.  
   2. Jelöljön ki egy munkaterületet.
   3. A munkaterület panel **általános**, kattintson a **gyors üzembe helyezés**.
   4. A **válasszon egy a munkaterülethez csatlakoztatandó adatforrást**, kattintson a **számítógépek**.
   5. Az a **közvetlen ügynök** panelen kattintson a **OMS-átjáró letöltése**.<br><br> ![OMS-átjáró letöltése](./media/log-analytics-oms-gateway/download-gateway.png)

vagy 

   1. A munkaterület panel **beállítások**, kattintson a **speciális beállítások**.
   2. Navigáljon a **csatlakoztatott források** > **Windows kiszolgálók** kattintson **OMS-átjáró letöltése**.

## <a name="install-the-oms-gateway"></a>Az OMS-átjáró telepítése

Az átjáró telepítéséhez hajtsa végre az alábbi lépéseket.  Ha egy korábbi verzióját telepítette, korábbi nevén *Log Analytics-továbbító*, erre a kiadásra lesz frissítve.  

1. Kattintson duplán a célmappa **OMS Gateway.msi**.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.<br><br> ![Átjárókiszolgáló telepítése varázsló](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. A a **licencszerződés** lapon jelölje be **elfogadom a licencszerződés feltételeit** fogadja el a végfelhasználói LICENCSZERZŐDÉST, majd **tovább**.
4. Az a **Port és a proxykiszolgáló címét** oldalon:
   1. Írja be az átjáró használni a TCP-port száma. A telepítő egy bejövő szabályt konfigurálja, a port számát, a Windows tűzfalon.  Az alapértelmezett érték: 8080-as.
      A portnak a számát, az érvényes értéktartomány: 1-65535. Ha a bemeneti nem esik a tartományba, hibaüzenet jelenik meg.
   2. Igény szerint ha a kiszolgáló, amelyen az átjáró telepítve van egy proxyn keresztül történő kommunikációhoz van szüksége, írja be a proxykiszolgáló címét, amelyen az átjáró kell csatlakoztatnia. Például: `http://myorgname.corp.contoso.com:80`.  Ha üres, akkor az átjáró megpróbálja közvetlenül csatlakozik az internethez.  Ha a proxykiszolgáló hitelesítést igényel, adjon meg egy felhasználónevet és jelszót.<br><br> ![Átjáró varázsló proxy konfigurálása](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Kattintson a **Tovább** gombra.
5. Ha nincs engedélyezve a Microsoft Update, a Microsoft Update lapon jelenik meg, ahol kiválaszthatja az engedélyezéshez. Jelölje ki, és kattintson a **tovább**. Ellenkező esetben folytassa a következő lépéssel.
6. Az a **célmappa** lapon hagyja bejelölve az alapértelmezett mappa: C:\Program Files\OMS átjáró, vagy írja be a helyet, ahol szeretné telepíteni az átjárót, és kattintson a **tovább**.
7. Az a **készen áll a telepítésre** kattintson **telepítése**. Felhasználói fiókok felügyeletének telepítése kér engedélyt jelenhet meg. Ha igen, kattintson a **Igen**.
8. Kattintson a telepítés után **Befejezés**. Ellenőrizheti, hogy a szolgáltatás fut, nyissa meg a services.msc beépülő modult, és ellenőrizze, hogy **OMS-átjáró** megjelenik a listán, a szolgáltatások, és azt állapota van **futó**.<br><br> ![Szolgáltatások – OMS-átjáró](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Hálózati terheléselosztás beállítása 
Beállíthatja, hogy az átjáró magas rendelkezésre álláshoz a hálózati terheléselosztás (NLB) a Microsoft hálózati terheléselosztás (NLB) vagy a hardveres terheléselosztók használatával.  A terheléselosztó forgalmat kezeli az OMS-ügynököket a kért kapcsolatokat, vagy az Operations Manager felügyeleti kiszolgálók átirányításával a csomópontok között. Egy átjáró kiszolgáló leáll, ha más csomópontokhoz átirányítja a forgalmat.

Ismerje meg, hogyan tervezhet és a egy Windows Server 2016 hálózati terheléselosztási fürt üzembe helyezése, lásd: [a hálózati terheléselosztás](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Az alábbi lépéseket a Microsoft hálózati terheléselosztási fürt konfigurálását ismertetik.  

1. Jelentkezzen be a Windows server, amely tagja a rendszergazdák rendszergazdai fiókkal az NLB-fürt.  
2. Hálózati terheléselosztás kezelőjének megnyitása a Kiszolgálókezelőben, kattintson a **eszközök**, és kattintson a **a hálózati terheléselosztás kezelője**.
3. Az OMS-átjáró kiszolgáló kapcsolódni a Microsoft Monitoring Agent telepítve van, kattintson a jobb gombbal a fürt IP-címét, és kattintson **gazdagép hozzáadása fürthöz**.<br><br> ![Hálózati terheléselosztás kezelője – betöltéséhez adja hozzá a gazdagépet a fürthöz](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Adja meg az IP-cím, az átjárókiszolgáló, amely kapcsolódni szeretne.<br><br> ![Hálózati terheléselosztás kezelője – a gazdagépet a fürthöz: csatlakozás](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Az OMS-ügynök és az Operations Manager felügyeleti csoport konfigurálása
A következő szakasz tartalmazza a közvetlenül csatlakoztatott OMS-ügynököket, az Operations Manager felügyeleti csoport vagy az Azure Automation hibrid Runbook-feldolgozók konfigurálása az Azure Automation és a Log Analytics kommunikálni az OMS-átjáró szükséges lépéseket.  

### <a name="configure-standalone-oms-agent"></a>Egyedülálló OMS-ügynök konfigurálása
Követelmények és lépések Windows-számítógépeken a Log Analytics szolgáltatásba való közvetlen csatlakozás az OMS-ügynök telepítésével kapcsolatos információk: [a Log Analyticshez való csatlakozáshoz Windows számítógépek](log-analytics-windows-agents.md) vagy a Linux rendszerű számítógépek lásd [csatlakoztatása Linux a Log Analytics számítógépek](log-analytics-quick-collect-linux-computer.md). Helyett adja meg a proxykiszolgáló az ügynök telepítése közben, cserélje le ezt az értéket az IP-címét az OMS-átjáró kiszolgálón vagy a port számát.  Ha hálózati terheléselosztót több átjárókiszolgáló telepített, akkor az OMS-ügynök proxykonfigurációt a hálózati Terheléselosztás virtuális IP-címét.  

Az Automation hibrid Runbook-feldolgozó kapcsolatos információkért lásd: [hibrid Runbook-feldolgozó üzembe helyezése](../automation/automation-hybrid-runbook-worker.md).

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
2. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Miután befejezte a Log Analytics-integráció, eltávolíthatja a módosítás futtatásával `netsh winhttp reset proxy` , majd a **Configure proxy server** lehetőség az operatív konzolon, az OMS-átjáró kiszolgáló megadása. 

1. Nyissa meg az Operations Manager konzolt és a **Operations Management Suite**, kattintson a **kapcsolat** majd **proxykiszolgáló konfigurálása**.<br><br> ![Az Operations Manager – proxykiszolgáló konfigurálása](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Válassza ki **proxykiszolgáló használata az Operations Management Suite eléréséhez** majd írja be az OMS-átjáró kiszolgáló IP-címét vagy az NLB virtuális IP-címét. Győződjön meg arról, hogy kezdjen a `http://` előtag.<br><br> ![Az Operations Manager – a proxykiszolgáló címe](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Kattintson a **Befejezés** gombra. Az Operations Manager felügyeleti csoportban most már a Log Analytics szolgáltatás az átjárókiszolgálón keresztül kommunikációra van konfigurálva.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurálja az Operations Manager - ügynökök adott proxykiszolgáló használata
Nagy vagy összetett környezetek esetében csak szüksége lehet adott kiszolgálók (vagy csoportokba) az OMS-átjáró kiszolgáló használatára.  Ezek a kiszolgálók, az Operations Manager-ügynök nem frissíthető közvetlenül a globális érték a felügyeleti csoport felülírja ezt az értéket.  Ehelyett kell elküldeni ezeket az értékeket használja szabály felülbírálása.  

> [!NOTE] 
> Konfigurációs ugyanezzel a módszerrel több OMS-átjáró kiszolgáló használatának engedélyezése a környezetben használható.  Ha például meg kell adni a régiók szerinti alapján adott OMS-átjáró kiszolgálókon lehet szükség.
>  

1. Nyissa meg az Operations Manager konzolt, és válassza ki a **szerzői műveletek** munkaterületen.  
2. Válassza a szerzői műveletek munkaterületének **szabályok** , és kattintson a **hatókör** gombra az Operations Manager eszköztárán. Ha ez a gomb nem érhető el, ellenőrizze, győződjön meg arról, hogy egy objektumot, nem pedig mappát jelölt a figyelés ablaktáblán. A **felügyeleti csomag objektumai** párbeszédpanel közös célzott osztályok, csoportok és objektumok listáját jeleníti meg. 
3. Típus **Állapotfigyelő szolgáltatás** a a **keressen** mezőben, majd válassza ki a listából.  Kattintson az **OK** gombra.  
4. Keresse meg a szabály **Advisor beállítás szabály** és az operatív konzol eszköztárán kattintson **felülbírálások** , majd mutasson a **bírálja felül a Rule\For osztály egy adott objektumához: Állapotfigyelő szolgáltatás**  , és válasszon ki egy adott objektumot a listából.  Igény szerint hozhat létre egyéni csoportja az állapotfigyelő szolgáltatás objektum a kiszolgálók, ez a felülbírálás a alkalmazni, és ezután alkalmazza a felülbírálást, ehhez a csoporthoz.
5. Az a **felülbírálás tulajdonságai** párbeszédpanelen jelölje be a a **felülbírálása** oszlop melletti a **WebProxyAddress** paraméter.  Az a **felülbírálás értéke** mezőben adja meg az URL-cím, az OMS-átjáró kiszolgáló biztosítsa, hogy először a a `http://` előtag.  

    >[!NOTE]
    > Nem kell engedélyezése a szabály már felügyelt automatikusan egy felülbírálással, a Microsoft System Center Advisor biztonságos hivatkozás felülbírálhatja felügyeleticsomag célzó a Microsoft System Center Advisor figyelési kiszolgáló csoport található.
    >   

6. Válasszon felügyeleti csomagot a **célhelyként szolgáló felügyeleti csomag** listában, vagy hozzon létre egy új, lezáratlan felügyeleti csomag kattintva **új**. 
7. Amikor végzett a változtatásokkal, kattintson az **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Az automation hibrid feldolgozó konfigurálása
Ha Automation hibrid Runbook-feldolgozók a környezetében, az alábbi lépéseket, ideiglenes manuális kerülő támogatja azokat az átjáró konfigurálásához adjon meg.

A következő lépésekben, ismernie kell az Automation-fiókot tartalmazó Azure-régióban. Keresse meg a helyet:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az Azure Automation szolgáltatást.
3. Válassza ki a megfelelő Azure Automation-fiókot.
4. Megtekintheti a régió alatt **hely**.<br><br> ![Az Azure-portál – Automation-fiók helye](./media/log-analytics-oms-gateway/location.png)  

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

1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeit az OMS gatewayen engedélyezett gazdagép-listába. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Indítsa újra az OMS-átjáró szolgáltatás a következő PowerShell-parancsmag segítségével: `Restart-Service OMSGatewayService`

Ha a számítógép egy előre telepített Azure Automation hibrid Runbook-feldolgozó regisztrációs parancsmag használatával, kövesse az alábbi lépéseket:

1. Az ügynök regisztrációs URL-címe adja hozzá az OMS gatewayen engedélyezett gazdagép-listába. Például:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeit az OMS gatewayen engedélyezett gazdagép-listába. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Indítsa újra az OMS-átjáró szolgáltatást.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok
Parancsmagok segítségével a feladatokat, melyek szükségesek ahhoz, hogy az OMS-átjáró konfigurációs beállításainak frissítése. Mielőtt azokat használni, ügyeljen arra, hogy:

1. Telepítse az OMS-átjáró (MSI).
2. Nyisson meg egy PowerShell-konzolablakot.
3. Importálja a modult, írja be ezt a parancsot: `Import-Module OMSGateway`
4. Ha nem történt hiba az előző lépésben, a modul importálása sikeresen megtörtént, és a parancsmag is használható. Típusa `Get-Module OMSGateway`
5. Miután változtatásokat parancsmagok segítségével, győződjön meg arról, újra kell indítania az átjárószolgáltatást.

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
Az átjáró által naplózott eseményeket gyűjtő, kell rendelkeznie az OMS-ügynök telepítve van.<br><br> ![Eseménynapló – OMS-átjáró naplójába](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS-átjáró eseményazonosítók és -leírások**

Az alábbi táblázat a eseményazonosítók és az OMS-átjáró alkalmazásnapló-események leírását.

| **Azonosító** | **Leírás** |
| --- | --- |
| 400 |Bármely alkalmazás hiba, amely nem rendelkezik meghatározott azonosító |
| 401 |Helytelen konfiguráció. Például: listenPort = "text" helyett egész szám |
| 402 |Kivétel történt a TLS-kézfogás üzenet elemzésekor |
| 403 |Hálózati hiba történt. Például: nem lehet csatlakozni a célkiszolgálóhoz |
| 100 |Általános információk |
| 101 |Szolgáltatás elindult |
| 102 |A szolgáltatás leállt |
| 103 |HTTP-csatlakozási parancsot kapott az ügyféltől |
| 104 |Nem egy HTTP-csatlakozási parancs |
| 105 |Célkiszolgáló nem szerepel az engedélyezési listán, vagy a céloldali port nem biztonságos portot (443) <br> <br> Győződjön meg arról, hogy az átjáró kiszolgálón az MMA-ügynök és az átjáróval kommunikáló ügynökök csatlakoztatva a Log Analytics munkaterületén. |
| 105 |Hiba TcpConnection – érvénytelen ügyféltanúsítványt: CN = átjáró <br><br> Győződjön meg arról, hogy: <br>    <br> &#149;Az átjáró használata verziószám 1.0.395.0 vagy nagyobb. <br> &#149;Az MMA-ügynök az átjárókiszolgálón és az átjáróval kommunikáló ügynökök csatlakoznak a Log Analytics munkaterületén. |
| 106 |Az OMS-átjáró csak a TLS 1.0, TLS 1.1 és 1.2-es támogatja.  Nem támogatja az SSL. Minden nem támogatott a TLS/SSL protokoll verziója, az OMS-átjáró event ID 106 állít elő.|
| 107 |A TLS-munkamenet jóvá lett hagyva. |

**Teljesítményszámlálók gyűjtése**

Az alábbi táblázat a rendelkezésre álló teljesítményszámlálók az OMS-átjáró számára. A számlálókat a Teljesítményfigyelő használata is hozzáadhat.

| **Name (Név)** | **Leírás** |
| --- | --- |
| OMS-átjáró aktív ügyfél kapcsolat |Aktív ügyfél (TCP) hálózati kapcsolatok száma |
| OMS-átjáró/hibák száma |Hibák száma |
| OMS-átjáró/csatlakozott ügyfél |Csatlakoztatott ügyfelek száma |
| OMS-átjáró vagy elutasítási száma |Bármely TLS-érvényesítési hiba miatt elutasítások száma |

![OMS-átjáró teljesítményszámlálói](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Segítség kérése
Ha be van jelentkezve az Azure Portalon, az OMS-átjárót vagy bármely más Azure-szolgáltatást a szolgáltatás a segítséget kér hozhat létre.
Segítségkérés, kattintson a kérdőjel a portál jobb felső sarkában található, és kattintson a **új támogatási kérelem**. Ezt követően fejezze be az új támogatási űrlap.

![Új támogatási kérelem](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>További lépések
[Adatforrások hozzáadása](log-analytics-data-sources.md) a csatlakoztatott forrásból gyűjthet adatokat, és tárolja a Log Analytics-munkaterületre.
