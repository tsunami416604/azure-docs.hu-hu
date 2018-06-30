---
title: Csatlakoztassa a számítógépet az OMS-átjáró |} Microsoft Docs
description: Az eszközök és az Operations Manager által felügyelt számítógépek kapcsolódnak az OMS-átjáró szeretnék adatokat küldeni az Azure Automation és Naplóelemzés szolgáltatás a, ha nem rendelkeznek Internet-hozzáféréssel.
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
ms.openlocfilehash: ecbc88ebaaa93215f85b57becc8a643dc3e168a0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129040"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Csatlakoztassa a számítógépet az OMS-átjáró internetkapcsolat nélkül
Ez a dokumentum ismerteti a kommunikáció konfigurálása az Azure Automation szolgáltatásban, és az OMS-átjáró, ha közvetlen Naplóelemzési csatlakoztatva, vagy az Operations Manager figyelt számítógépek nem rendelkeznek Internet-hozzáféréssel.  Az OMS-átjáró, amely, amely támogatja a HTTP-bújtatás a HTTP-csatlakozás parancs használatával továbbítsa HTTP-proxyt, adatokat gyűjteni, és elküldi a Azure Automation és Naplóelemzési részére azok nevében.  

Az OMS-átjáró támogatja:

* Azure Automation szolgáltatásbeli hibrid forgatókönyv-feldolgozók  
* Windows rendszerű számítógépek a Microsoft Monitoring Agenttel való közvetlenül csatlakozik a Naplóelemzési munkaterület
* Linux rendszerű számítógépek a Linux OMS-ügynököt közvetlenül csatlakozik a Naplóelemzési munkaterület  
* A System Center Operations Manager 2012 SP1 UR7, az Operations Manager 2012 R2 UR3, az Operations Manager 2016 és az Operations Manager verzióra 1801 felügyeleti csoport integrálva van a Naplóelemzési.  

Ha az IT-biztonsági házirendeknek nem teszi lehetővé a számítógépek csatlakozni az internethez, a pénztári (POS) eszközök és a kiszolgálók támogató informatikai szolgáltatásai, például a hálózat, de az Azure Automation vagy Naplóelemzési kezelni és megfigyelni őket csatlakoztatni kell , és közvetlenül kommunikáljanak az OMS-átjáró konfigurációs és adatokat továbbítson a részére azok nevében konfigurálható.  Ha ezeket a számítógépeket az OMS-ügynököt a Naplóelemzési munkaterület közvetlenül kapcsolódni vannak beállítva, minden számítógép helyette kommunikálni az OMS-átjáró.  Az átjáró átviszi az adatokat a az ügynököket a szolgáltatás közvetlenül, nem elemzi az adatokat átvitel közben bármelyikét.

Ha az Operations Manager felügyeleti csoport integrálva van a Naplóelemzési, a felügyeleti kiszolgálók beállítható úgy, hogy az OMS-átjárón, attól függően, hogy a megoldás engedélyezte az összegyűjtött adatok küldésére és fogadására a konfigurációs adatokat.  Operations Manager-ügynökök egyes adatok, például az Operations Manager riasztásokat, konfigurációelemzés, példánytér és kapacitás adatokat küldeni a felügyeleti kiszolgáló. Egyéb nagy mennyiségű adatok, például az IIS-napló, a teljesítmény és a biztonsági események közvetlenül az OMS-átjáró kerülnek.  Ha van telepítve, a nem megbízható rendszerek figyeléséhez DMZ vagy más elkülönített hálózat egy vagy több Operations Manager átjáró kiszolgálók, az OMS-átjáró nem tud kommunikálni.  Operations Manager átjáró kiszolgálók csak a felügyeleti kiszolgálóra is tud jelentéseket.  Ha az Operations Manager felügyeleti csoport az OMS-átjáró folytatott kommunikációhoz van konfigurálva, a proxy konfigurációs információkat terjesztése automatikusan történik minden ügynök által felügyelt számítógép, amely konfigurálva van a Naplóelemzési, gyűjt adatokat akkor is, ha a érték üres.    

Magas rendelkezésre állás biztosításához a közvetlenül csatlakoztatott vagy az átjárón keresztül Naplóelemzési kommunikációs műveletek felügyeleti csoportok, használhatja a hálózati terheléselosztás történő és a forgalom szét több átjárókiszolgáló.  Ha egy átjáró kiszolgáló leáll, a rendszer átirányítja a forgalmat egy másik csomópont érhető el.  

Az OMS-ügynököt ahhoz, hogy azonosítsa a kommunikációhoz, és figyelje az OMS-átjáró a teljesítmény vagy esemény adatok elemzése szükséges szolgáltatásvégpontjaira futtató az OMS-átjáró szükséges.

Minden ügynök hálózati kapcsolat az átjáróhoz rendelkeznie kell, hogy az ügynökök automatikusan adatátviteli irányuló és onnan az átjáró. Az átjáró telepítése egy tartományvezérlőn nem ajánlott.

Az alábbi ábrán látható adatfolyama közvetlen ügynököktől Azure Automation és Log Analytics-átjáró kiszolgáló használatával.  Ügynökök rendelkeznie kell a proxy konfigurációs felel meg ugyanazt a portot, az OMS-átjáró a szolgáltatás kommunikációra van beállítva.  

![közvetlen ügynökkommunikációhoz szolgáltatások diagramja](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán látható adatfolyama szolgáltatáshoz az Operations Manager felügyeleti csoportból.   

![Az Operations Manager kommunikáció Naplóelemzési diagramja](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Előfeltételek

Egy számítógép az OMS-átjáró futtatásához kijelölésekor, ezen a számítógépen az alábbiakkal kell rendelkeznie:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016-ot, a Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET-keretrendszer 4.5
* Legalább egy 4 magos processzor és 8 GB memóriát 
* A Windows OMS-ügynököt 

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

### <a name="supported-encryption-protocols"></a>A támogatott titkosítási protokollok
Az OMS-átjáró csak a Transport Layer Security (TLS) 1.0-s, 1.1-es és 1.2-es támogatja.  Nem támogatja a Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynök-kapcsolatok száma
A következő táblázat emeli ki az ügynökök egy átjáró kiszolgálóval való kommunikáció során támogatott számát.  Ez a támogatás feltöltése ~ 200KB adatot 6 másodpercenként ügynökök alapul. Tesztelt ügynök / adatmennyiség naponta körülbelül 2.7 GB-TAL.

|Átjáró |Támogatott ügynökök száma KB.|  
|--------|----------------------------------|  
|-CPU: Intel XEON CPU E5-2660 v3 @ 2.6-os GHz 2 magos<br> -Memória: 4 GB<br> -A hálózati sávszélesség: 1 GB/s| 600|  
|-CPU: Intel XEON CPU E5-2660 v3 @ 2.6-os GHz, 4 magos<br> -Memória: 8 GB<br> -A hálózati sávszélesség: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Töltse le az OMS-átjáró

Kétféleképpen az OMS-átjáró telepítési fájl letöltéséhez.

1. Letölthető a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Töltse le az Azure-portálon.  Miután bejelentkezik az Azure-portálon:  

   1. Keresse meg a szolgáltatások listájában, és válassza ki **Naplóelemzési**.  
   2. Jelöljön ki egy munkaterületet.
   3. A munkaterület panelen a **általános**, kattintson a **gyors üzembe helyezés**.
   4. A **válasszon számára a munkaterülethez csatlakoztatandó adatforrást**, kattintson a **számítógépek**.
   5. Az a **közvetlen ügynök** panelen kattintson a **OMS-átjáró letöltése**.<br><br> ![Töltse le az OMS-átjáró](./media/log-analytics-oms-gateway/download-gateway.png)

vagy 

   1. A munkaterület panelen a **beállítások**, kattintson a **speciális beállítások**.
   2. Navigáljon a **csatlakoztatott források** > **Windows kiszolgálók** kattintson **OMS-átjáró letöltése**.

## <a name="install-the-oms-gateway"></a>Az OMS-átjáró telepítése

Telepít egy átjárót, hajtsa végre a következő lépéseket.  Ha egy korábbi verzióját telepítette, korábbi nevén *napló Analytics továbbító*, frissíti az ebben a kiadásban.  

1. Kattintson duplán a célmappa **OMS Gateway.msi**.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.<br><br> ![Átjáró telepítővarázslója](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Az a **licencszerződés** lapon jelölje be **elfogadom a licencszerződés feltételeit** elfogadja a végfelhasználói LICENCSZERZŐDÉST, majd **következő**.
4. Az a **Port és a proxy címét** lap:
   1. Írja be az átjáró használandó TCP-portszámát. A telepítő konfigurálja egy bejövő forgalomra vonatkozó szabály ezen a portszámon, a Windows tűzfalat.  Az alapértelmezett érték: 8080-as.
      A portszám érvényes tartománya 1-65535. Ha a bemeneti adatok nem esik a tartományba, hibaüzenet jelenik meg.
   2. Ha a kiszolgáló, amelyen az átjáró telepítve van egy proxyn keresztül történő kommunikációhoz szükséges, megadhat a proxykiszolgáló címét, amelyen az átjáró kell kapcsolódnia. Például: `http://myorgname.corp.contoso.com:80`.  Ha üres, az átjáró megpróbál közvetlenül csatlakozik az internethez.  Ha a proxykiszolgálóhoz hitelesítés szükséges, adja meg a felhasználónevet és jelszót.<br><br> ![Átjáró varázsló proxy konfigurálása](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Kattintson a **Tovább** gombra.
5. Ha Ön nem rendelkezik a Microsoft Update szolgáltatást, a Microsoft Update lapon jelenik meg, ahol kiválaszthatja, az engedélyezéshez. Válasszon ki egy, és kattintson a **következő**. Ellenkező esetben folytassa a következő lépéssel.
6. Az a **célmappa** lapon vagy az alapértelmezett mappát C:\Program Files\OMS átjárót, vagy írja be a helyet, ahová telepíteni az átjárót, és kattintson a **következő**.
7. Az a **készen állnak a telepítésre** kattintson **telepítése**. Felhasználói fiókok felügyelete jelenhet meg kérelmező jogosultsága a telepítéséhez. Ha igen, kattintson a **Igen**.
8. Kattintson a telepítés után **Befejezés**. Ellenőrizheti, hogy a szolgáltatás fut, nyissa meg a "Services.msc" parancsot beépülő modul, és ellenőrizze, hogy **OMS átjáró** megjelennek a listában, szolgáltatások és az állapot van **futtató**.<br><br> ![Szolgáltatások – OMS-átjáró](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>A hálózati terheléselosztás konfigurálása 
Az átjárót a magas rendelkezésre állás, a hálózati terheléselosztás (NLB) a Microsoft hálózati terheléselosztó terheléselosztási (NLB) vagy a hardveres terheléselosztó használatával is konfigurálhatja.  A terheléselosztót irányítják át a kért kapcsolatokat az OMS-ügynököt vagy Operations Manager felügyeleti kiszolgálón a csomópontjai között forgalmat kezeli. Ha egy átjáró kiszolgáló leáll, a forgalom más csomópontok lekérdezi átirányítva.

Tervezéséhez és a Windows Server 2016-os hálózati terheléselosztási fürt telepítéséhez, lásd: [a hálózati terheléselosztás](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  A következő lépések a Microsoft hálózati terheléselosztási fürt konfigurálását ismertetik.  

1. Jelentkezzen be a Windows server-rendszergazdai jogosultságokkal rendelkező fiókkal a hálózati Terheléselosztási fürt tagja.  
2. Hálózati terheléselosztás kezelőjének megnyitása a Kiszolgálókezelőben, kattintson a **eszközök**, és kattintson a **hálózati terheléselosztás kezelője**.
3. Az OMS-átjárókiszolgáló kapcsolódni a Microsoft Monitoring Agent telepítése, kattintson a jobb gombbal a fürt IP-címet, és kattintson **gazdagép hozzáadása fürthöz**.<br><br> ![Hálózati terheléselosztás kezelője – betöltése vegye fel a gazdagépet a fürthöz](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Adja meg az IP-cím, az átjárókiszolgáló, amely csatlakozni szeretne.<br><br> ![Hálózati terheléselosztás kezelője – állomás hozzáadása fürthöz: csatlakozás](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>OMS-ügynököt, és az Operations Manager felügyeleti csoport konfigurálása
Az alábbi szakasz a közvetlenül csatlakoztatott OMS-ügynököt, az Operations Manager felügyeleti csoport vagy Azure Automation hibrid forgatókönyv-feldolgozók konfigurálásáról az OMS-átjáróval kommunikálni az Azure Automation vagy Naplóelemzési lépéseivel.  

### <a name="configure-standalone-oms-agent"></a>Önálló OMS-ügynököt konfigurálása
Követelmények és az OMS-ügynököt telepíteni a Windows rendszerű számítógépeken Log Analyticshez való közvetlen csatlakozás lépéseit ismertetése: [Log Analyticshez való csatlakozás Windows számítógépek](log-analytics-windows-agents.md) vagy a Linux rendszerű számítógépek lásd [csatlakozás Linux Naplóelemzési számítógépek](log-analytics-quick-collect-linux-computer.md). Helyett adja meg a proxykiszolgáló konfigurálása az ügynök közben, akkor cserélje le ezt az értéket az IP-címet az OMS-átjárókiszolgáló és a port számának.  Ha egy hálózati terheléselosztó mögött több átjárókiszolgáló telepített, akkor az OMS-ügynök proxykonfigurációt a hálózati Terheléselosztási virtuális IP-címét.  

Az automatizálási hibrid forgatókönyv-feldolgozó kapcsolatos információkért lásd: [telepítése hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Az Operations Manager konfigurálása - az ügynökök azonos proxykiszolgáló használata
Az Operations Manager adja hozzá az átjáró-kiszolgálót konfigurálja.  Az Operations Manager proxykonfigurációt a rendszer automatikusan alkalmazza minden ügynököt, és az Operations Manager jelentéskészítési, akkor is, ha az érték üres.  

Az Operations Manager támogatásához az átjáró használatához rendelkeznie kell:

* A Microsoft Monitoring Agent (ügynök verziója – **8.0.10900.0** vagy újabb) az átjárókiszolgáló telepítve, és a Naplóelemzési munkaterület, amellyel kommunikálni szeretne konfigurálni.
* Az átjáró internetkapcsolattal kell rendelkeznie, vagy a proxykiszolgálóhoz, amelyet csatlakoztatni.

> [!NOTE]
> Ha nem adja meg az átjáró értékét, üres értékek vannak leküldve összes ügynököt.
> 

Ha ez az első alkalommal a Naplóelemzési munkaterület regisztrál az Operations Manager felügyeleti csoportjának, a adja meg a proxykiszolgáló-konfigurációt a felügyeleti csoport nincs lehetőség az operatív konzolon érhető el.  A felügyeleti csoportot sikeresen regisztrálni kell a szolgáltatásban ahhoz, hogy ez a lehetőség rendelkezésre álljon.  Az integráció és a felügyeleti csoportba tartozó minden felügyeleti kiszolgáló konfigurálásához Netsh használatával frissítenie kell a rendszerproxy-konfigurációt a rendszeren, amelyiken az Operatív konzolt futtatja.  

1. Nyisson meg egy emelt szintű parancssort.
   a. Ugrás a **Start** és típus **cmd**.
   b. Kattintson a jobb gombbal **parancssor** , és jelölje ki futtató rendszergazda **.
2. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Log Analyticshez való integrációt befejezése után eltávolíthatja a módosítás futtatásával `netsh winhttp reset proxy` , majd a **proxy kiszolgáló konfigurálása** az operatív konzolon beállítással adhatja meg az OMS-átjárókiszolgáló. 

1. Nyissa meg az Operations Manager konzolt, és a **Operations Management Suite**, kattintson a **kapcsolat** , majd **Proxy kiszolgáló konfigurálása**.<br><br> ![Az Operations Manager-Proxy kiszolgáló konfigurálása](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Válassza ki **proxykiszolgáló használata az Operations Management Suite eléréséhez** és írja be az OMS-átjáró kiszolgáló IP-címét vagy a hálózati Terheléselosztási virtuális IP-címét. Győződjön meg arról, hogy a kiindulási pont a `http://` előtag.<br><br> ![Az Operations Manager – a proxykiszolgáló címét](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Kattintson a **Befejezés** gombra. Az Operations Manager felügyeleti csoportjának konfigurálva van a Naplóelemzési szolgáltatásnak az átjárókiszolgálón keresztül kommunikálnak.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurálja az Operations Manager - adott ügynökök proxykiszolgáló használatára
Nagy vagy összetett környezetek előfordulhat, hogy csak kívánt kiszolgálók (vagy a csoportok) az OMS-átjárókiszolgáló.  Az ezeken a kiszolgálókon az Operations Manager-ügynök nem frissíthető közvetlenül a felügyeleti csoport globális érték felülírja ezt az értéket.  Ehelyett kell bírálni a szabályt, majd ezeket az értékeket használja.  

> [!NOTE] 
> Ez a azonos konfigurációs módszer segítségével több OMS átjárókiszolgáló használatának engedélyezése a környezetben.  Például lehet szükség az adott OMS-átjárókiszolgáló régió alapon adható meg.
>  

1. Nyissa meg az Operations Manager konzolt, és válassza ki a **szerzői műveletek** munkaterületen.  
2. A szerzői műveletek területen válassza ki **szabályok** , és kattintson a **hatókör** az Operations Manager gombjára. Ha ez a gomb nem érhető el, ellenőrizze, hogy győződjön meg arról, hogy rendelkezik-e objektumot, nem pedig mappát jelölt ki a figyelés ablaktáblán. A **felügyeleti csomag objektumai** párbeszédpanel közös célzott osztályok, csoportok vagy objektumok listáját jeleníti meg. 
3. Típus **Állapotfigyelő szolgáltatás** a a **keressen** mezőben, majd válassza ki azt a listából.  Kattintson az **OK** gombra.  
4. Keresse meg a szabály **Advisor Proxy beállítás szabály** és az operatív konzol eszköztárán kattintson **felülbírálások** mutasson **bírálja felül a Rule\For osztály egy adott objektumához: Állapotfigyelő szolgáltatás**  és egy adott objektumot válasszon a listából.  Szükség esetén az állapotfigyelő szolgáltatás objektumot alkalmazza a felülbírálást, és ezután alkalmazza a felülbírálást a csoporthoz tartozó kiszolgálók tartalmazó egyéni csoportot hozhat létre.
5. Az a **felülbírálás tulajdonságai** párbeszédpanelen jelölje be a a **felülbírálása** oszlop a következő gombra a **WebProxyAddress** paraméter.  Az a **felülbírálás értékét** mezőbe írja be az URL-CÍMÉT az OMS-átjáró kiszolgálón győződjön meg arról, hogy a kiindulási pont a `http://` előtag.  

    >[!NOTE]
    > Nem kell azt már kezeli automatikusan egy felülbírálással, a Microsoft System Center Advisor biztonságos hivatkozás Override felügyeleti csomag célzó a Microsoft System Center Advisor figyelési kiszolgáló csoport tartalmazza a szabály engedélyezését.
    >   

6. Válassza a felügyeleti csomag a **célhelyként szolgáló felügyeleti csomag** listában, vagy hozzon létre egy új lezáratlan felügyeleti csomag kattintva **új**. 
7. A módosítások befejeztével kattintson **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Automatizálási hibrid feldolgozó konfigurálása
Ha a környezetben Automation hibrid forgatókönyv-feldolgozóival, az alábbi lépések nyújtanak ideiglenes, manuális lehetséges megoldások támogatásukhoz átjáró konfigurálása.

A következő lépésekben hasznos tudnivalók az Azure-régió, ahol az Automation-fiók található. A helyen található:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az Azure Automation szolgáltatás.
3. Válassza ki a megfelelő Azure Automation-fiók.
4. A régió szerinti megtekintéséhez **hely**.<br><br> ![Azure portál – Automation fiókjának helye](./media/log-analytics-oms-gateway/location.png)  

Az alábbi táblázatok segítségével azonosíthatók a URL-címe, mindegyik helyen.

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

**Ügynök szolgáltatás URL-címek**

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

Ha a számítógép regisztrálva van, a hibrid forgatókönyv-feldolgozók automatikusan javítását a frissítés felügyeleti megoldást használni, kövesse az alábbi lépéseket:

1. A feladat futási adataihoz szolgáltatás URL-címek hozzáadása az engedélyezett állomások listájához, az OMS-átjárón. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Indítsa újra az OMS-átjáró szolgáltatás a következő PowerShell-parancsmag segítségével: `Restart-Service OMSGatewayService`

Ha a számítógép előre telepített az Azure Automation a hibrid forgatókönyv-feldolgozó regisztrálása parancsmag használatával, kövesse az alábbi lépéseket:

1. Az ügynök regisztrációs URL-címe hozzá az OMS-átjárón gazdakiszolgálóhoz listájához. Például:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. A feladat futási adataihoz szolgáltatás URL-címek hozzáadása az engedélyezett állomások listájához, az OMS-átjárón. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Indítsa újra az OMS-átjáró szolgáltatás.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok
Parancsmag segítségével végezze el a feladatokat, amelyek szükségesek ahhoz, hogy az OMS-átjáró konfigurációs beállításainak frissítése. Mielőtt azokat használni, ügyeljen arra, hogy:

1. Az OMS-átjáró (MSI) telepítéséhez.
2. Nyisson meg egy PowerShell-konzol ablakot.
3. A modul importálásához, ezt a parancsot: `Import-Module OMSGateway`
4. Ha nincs hiba történt az előző lépésben, a modul importálása sikeres volt, és a parancsmag is használható. Típusa `Get-Module OMSGateway`
5. Módosítása után a parancsmagok segítségével, győződjön meg arról, hogy az átjáró szolgáltatás újraindítása.

Ha hibaüzenetet kap a 3. lépésben, a modul nem importálható. A hiba akkor fordulhat elő, ha nem található a modul PowerShell. Az átjáró telepítési elérési úton található: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **A parancsmag** | **Paraméterek** | **Leírás** | **Példa** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Kulcs |A szolgáltatás konfigurációjának beolvasása |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Kulcs (kötelező) <br> Érték |A szolgáltatás konfigurációját módosítja |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |A továbbító (fölérendelt) proxy címét |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Cím<br> Felhasználónév<br> Jelszó |Beállítja a továbbítási (fölérendelt) proxy címe (és hitelesítő adatok) |1. Állítsa be a továbbító proxy és a hitelesítő adat:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. A továbbító proxy hitelesítést nem igénylő beállításait: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Törölje a továbbítási proxybeállítást:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Lekérdezi a jelenleg engedélyezett fogadó (csak a helyileg konfigurált engedélyezett gazdagép, nem tartalmaz a megengedett gazdagépek automatikusan letöltött) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Állomás (kötelező) |Hozzáadja a gazdagépet, az engedélyezett bővítmények listájához |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Állomás (kötelező) |A gazdagép eltávolítása az engedélyezett bővítmények listájához |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Hozzáadja az ügyféltanúsítvány vonatkoznak az engedélyezett bővítmények listájához |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az ügyfél-tanúsítvány tulajdonosának eltávolítja az engedélyezett bővítmények listájához |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Lekérdezi a jelenleg engedélyezett ügyfél tanúsítványtulajdonosok (csak a helyileg konfigurált engedélyezett a következő témákat tárgyalja, nem tartalmaz engedélyezett témák automatikusan letöltött) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Hibaelhárítás
Az átjáró által naplózott eseményeket gyűjt, az OMS-ügynököt futtató is rendelkeznie kell.<br><br> ![Eseménynapló – OMS átjáró napló](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS átjáró esemény azonosítók és leírások**

Az alábbi táblázat a eseményazonosítók és az OMS-átjáró naplóeseményeket leírását.

| **Azonosító** | **Leírás** |
| --- | --- |
| 400 |Bármely alkalmazás hiba, amely nem rendelkezik egyedi azonosítója |
| 401 |Helytelen konfiguráció. Például: listenPort = "text" helyett egész szám |
| 402 |Kivétel fordult elő a TLS-kézfogás üzenet elemzése |
| 403 |Hálózati hiba történt. Például: nem lehet kapcsolódni a célkiszolgálón |
| 100 |Általános információk |
| 101 |Szolgáltatás elindult |
| 102 |A szolgáltatás leállt |
| 103 |HTTP-Csatlakozás parancsot kapott az ügyféltől |
| 104 |Nem egy HTTP-csatlakozás parancs |
| 105 |Célkiszolgáló nem engedélyezett listában vagy a célport nem biztonságos portja (443) <br> <br> Győződjön meg arról, hogy az átjárókiszolgálón az MMA ügynök és az ügynökök kommunikál az átjáró csatlakozik a ugyanazt a Naplóelemzési munkaterület. |
| 105 |Hiba TcpConnection – érvénytelen ügyféltanúsítvány: CN = átjáró <br><br> Győződjön meg arról, hogy: <br>    <br> &#149;Átjáró verziószámú 1.0.395.0 használ, vagy nagyobb. <br> &#149;Az átjárókiszolgálón az MMA ügynök és az ügynökök kommunikál az átjáró csatlakozik a ugyanazt a Naplóelemzési munkaterület. |
| 106 |Az OMS-átjáró csak akkor támogatja, a TLS 1.0, TLS 1.1-es és 1.2-es.  Nem támogatja az SSL. Nem támogatott a TLS/SSL protokoll bármilyen, az OMS-átjáró event ID 106 állít elő.|
| 107 |A TLS-munkamenet ellenőrzése megtörtént |

**Teljesítményszámlálók gyűjtése**

A következő táblázat a rendelkezésre álló teljesítményszámlálók az OMS-átjáró. A számlálókat a Teljesítményfigyelő használata is hozzáadhat.

| **Name (Név)** | **Leírás** |
| --- | --- |
| OMS-aktív átjáró Ügyfélkapcsolat |Aktív ügyfél (TCP) hálózati kapcsolatok száma |
| OMS átjáró/hibák száma |Hibák száma |
| OMS átjáró/csatlakoztatva ügyfél |Kapcsolódó ügyfelek száma |
| OMS átjáró/elutasítása száma |A TLS-érvényesítési hiba miatt elutasítások számát |

![OMS-átjáró teljesítményszámlálói](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Segítségkérés
Ha be van jelentkezve az Azure-portálon, a segítségért az OMS-átjáró vagy bármely más Azure-szolgáltatások vagy a szolgáltatás funkciója is létrehozhat.
Segítségkérés a kérdőjel szimbólum a portál jobb felső sarkában kattintson, és kattintson a **új támogatja a kérelem**. Ezután fejezze be az új támogatási kérelem űrlap.

![Új támogatási kérelem](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>További lépések
[Adatforrások hozzáadása](log-analytics-data-sources.md) adatokat gyűjteni a csatlakoztatott források és tárolja a Naplóelemzési munkaterület.
