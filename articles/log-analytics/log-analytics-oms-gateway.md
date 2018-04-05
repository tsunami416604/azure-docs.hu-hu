---
title: Csatlakoztassa a számítógépet az OMS-átjáró használatával |} Microsoft Docs
description: Az eszközök és az Operations Manager által felügyelt számítógépek kapcsolódnak az OMS-átjáró szeretnék adatokat küldeni az Azure Automation és Naplóelemzés szolgáltatás a, ha nem rendelkeznek internet-hozzáféréssel.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Az OMS-átjáró használatával csatlakoznak az internetkapcsolattal nem rendelkező számítógépek
Ez a dokumentum ismerteti a kommunikáció konfigurálása az Azure Automation szolgáltatásban, és az OMS-átjáró, ha közvetlenül a Naplóelemzési csatlakoztatott számítógépek, vagy az Operations Manager által felügyelt számítógépek nem rendelkeznek interneteléréssel. Az OMS-átjáró esetében egy HTTP-továbbító a proxy, amely támogatja a HTTP-csatlakozás paranccsal tunneling HTTP. Az adatok gyűjtéséhez, és elküldi a Azure Automation és Naplóelemzési internetkapcsolattal nem rendelkező számítógépek nevében.  

Az OMS-átjáró támogatja:

* Azure Automation szolgáltatásbeli hibrid forgatókönyv-feldolgozók  
* Windows rendszerű számítógépek a Microsoft Monitoring Agenttel való közvetlenül csatlakozik a Naplóelemzési munkaterület
* Linux rendszerű számítógépek a Linux OMS-ügynököt közvetlenül csatlakozik a Naplóelemzési munkaterület  
* A System Center Operations Manager 2012 SP1 UR7, az Operations Manager 2012 R2 UR3, az Operations Manager 2016 és az Operations Manager verzióra 1801 felügyeleti csoport integrálva van a Naplóelemzési  

Ha az IT-biztonsági házirendeknek tiltása bizonyos számítógépeken a hálózat csatlakozik az internethez (például a pénztári eszközök vagy informatikai szolgáltatásokat támogató kiszolgálókat), de az Azure Automation vagy Naplóelemzési kezelni és megfigyelni őket, csatlakoztatni kell azokat beállítható úgy, hogy az OMS-átjáró közvetlenül kommunikálnak.

 Ha ezeket a számítógépeket az OMS-ügynököt a Naplóelemzési munkaterület közvetlenül kapcsolódni vannak beállítva, minden számítógép helyette kommunikálni az OMS-átjáró. Az OMS-átjáró adatátvitelt az ügynököktől származó a szolgáltatás közvetlenül. Az adatok azt nem elemzése, amíg az átvitel során.

Ha az Operations Manager felügyeleti csoport integrálva van a Naplóelemzési, a felügyeleti kiszolgálók beállítható úgy, hogy az OMS-átjárón összegyűjtött adatok küldésére és fogadására a konfigurációs adatokat. Operations Manager-ügynökök egyes adatok, például az Operations Manager riasztásokat konfigurációelemzés, példánytér vagy kapacitás adatokat küldenek a felügyeleti kiszolgálónak. Egyéb nagy mennyiségű adatok, például IIS naplókat, a Teljesítményadatok és a biztonsági események, közvetlenül az OMS-átjáró kerülnek.  

Ha egy vagy több Operations Manager átjáró kiszolgálók üzembe helyezett DMZ vagy más elszigetelt hálózaton nem megbízható rendszerek figyeléséhez, nem tudnak kommunikálni egy OMS-átjárót. Operations Manager átjáró kiszolgálók csak a felügyeleti kiszolgálóra is tud jelentéseket. 

Az OMS-átjáróval kommunikálni az Operations Manager felügyeleti csoport van konfigurálva, ha a proxy konfigurációs adatokat a rendszer automatikusan továbbítja minden ügynök által felügyelt számítógép használatával gyűjt adatokat, a Naplóelemzési van konfigurálva, akkor is, ha az érték üres.    

A magas rendelkezésre állás biztosításához közvetlenül csatlakoztatott vagy Naplóelemzési az OMS-átjárón keresztül kommunikáló Operations felügyeleti csoportok, használhatja a hálózati terheléselosztás történő és a forgalom szét több OMS-átjárókiszolgáló. Ha egy átjáró kiszolgáló leáll, a rendszer átirányítja a forgalmat egy másik csomópont érhető el.  

Azt javasoljuk, hogy az OMS-ügynököt telepít a számítógépen, amelyen az OMS-átjáró szoftver, megfigyeli azt, és a teljesítmény- vagy esemény elemzéséhez. Emellett az ügynök segít az OMS-átjáró kommunikálnia kell szolgáltatásvégpontjaira azonosítása.

Minden ügynököt, hogy azok automatikusan adatátviteli irányuló és onnan az átjáró rendelkeznie kell az átjáró hálózati kapcsolatot. Nem ajánlott, egy átjáró telepítése egy tartományvezérlőn.

Az alábbi ábrán látható adatfolyama közvetlen ügynököktől Azure Automation és Log Analytics-átjáró kiszolgáló használatával. Az ügynök a proxy konfigurációját, amely az OMS-átjárót a szolgáltatással való kommunikációra használja ugyanazt a portot kell használnia.  

![közvetlen ügynökkommunikációhoz szolgáltatások diagramja](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán látható adatfolyama szolgáltatáshoz az Operations Manager felügyeleti csoportból.   

![Az Operations Manager kommunikáció Naplóelemzési diagramja](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Előfeltételek

Ha kijelöl egy számítógép az OMS-átjáró futtatásához, ellenőrizze, hogy a következő összetevőket:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET-keretrendszer 4.5
* Legalább egy 4 magos processzor és 8 GB memóriát 

### <a name="language-availability"></a>Nyelvi rendelkezésre állása

Az OMS-átjáró a következő nyelveken érhető el:

- Kínai (egyszerűsített)
- Kínai (hagyományos)
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
- portugál (Brazília)
- portugál (Portugália)
- orosz
- Spanyol (nemzetközi)

### <a name="supported-encryption-protocols"></a>A támogatott titkosítási protokollok
Az OMS-átjáró támogatja a csak Transport Layer Security (TLS) 1.0-s, 1.1-es és 1.2-es.  Nem támogatja a Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynök-kapcsolatok száma
A következő táblázat ismerteti az átjárókiszolgáló szolgáltatással kommunikáló ügynökök támogatott száma. Ez a támogatás az ügynökök körülbelül 200 KB adatot 6 másodpercenként feltöltése alapul. Tesztelt ügynök / adatmennyiség naponta körülbelül 2.7 GB-TAL.

|Átjáró |Támogatott ügynökök hozzávetőleges száma|  
|--------|----------------------------------|  
|-CPU: Intel XEON CPU E5-2660 v3 @ 2.6-os GHz 2 magos<br> -Memória: 4 GB<br> -A hálózati sávszélesség: 1 GB/s| 600|  
|-CPU: Intel XEON CPU E5-2660 v3 @ 2.6-os GHz, 4 magos<br> -Memória: 8 GB<br> -A hálózati sávszélesség: 1 GB/s| 1000|  

## <a name="download-the-oms-gateway"></a>Töltse le az OMS-átjáró

Kétféleképpen az OMS-átjáró telepítési fájl letöltéséhez.

1. Töltse le a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Töltse le az Azure-portálról. Miután bejelentkezik az Azure-portálon, a következő lépéseket:  

   1. Keresse meg a szolgáltatások listájában, és válassza ki **Naplóelemzési**.  
   2. Jelöljön ki egy munkaterületet.
   3. A munkaterület panelen a **általános**, jelölje be **gyors üzembe helyezés**.
   4. A **válasszon számára a munkaterülethez csatlakoztatandó adatforrást**, jelölje be **számítógépek**.
   5. Az a **közvetlen ügynök** panelen, jelölje be **OMS-átjáró letöltése**.
   
    ![Töltse le az OMS-átjáró](./media/log-analytics-oms-gateway/download-gateway.png)

– VAGY – 

   1. A munkaterület panelen a **beállítások**, jelölje be **speciális beállítások**.
   2. Válassza ki **csatlakoztatott adatforrások** > **Windows kiszolgálók**. Válassza ki **OMS-átjáró letöltése**.

## <a name="install-the-oms-gateway"></a>Az OMS-átjáró telepítése

Telepít egy átjárót, a következő lépéseket. Ha az előző verzióját telepítette, korábbi nevén *napló Analytics továbbító*, frissíti az ebben a kiadásban.  

1. Válassza ki a célmappát, **OMS Gateway.msi**.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.

 ![Átjáró telepítővarázslója](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. Az a **licencszerződés** lapon jelölje be **elfogadom a licencszerződés feltételeit**. Ezután kattintson a **Tovább** gombra.

4. Az a **Port és a proxy címét** lap:

   a. Írja be az átjáró használandó TCP-portszámát. A telepítő konfigurálja egy bejövő forgalomra vonatkozó szabály ezen a portszámon, a Windows tűzfalat. Az alapértelmezett érték: 8080-as. A portszám érvényes tartománya 1 és 65535 között. Ha a bemeneti adatok nem esik a tartományba, hibaüzenet jelenik meg.

   b. Ha a kiszolgáló, amelyen az átjáró telepítve van egy proxyn keresztül történő kommunikációhoz szükséges, megadhat a proxy, amelyhez az átjáró kell kapcsolódnia. Erre példa lehet `http://myorgname.corp.contoso.com:80`, az alábbi képernyőfelvételen látható módon. Ha ezt a mezőt üresen hagyja, az átjáró megpróbál közvetlenül csatlakozik az internethez.  Ha a proxykiszolgálóhoz hitelesítés szükséges, adjon meg egy felhasználónevet és jelszót.
   
    ![Átjáró varázsló proxy konfigurálása](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Kattintson a **Tovább** gombra.

5. Ha nincs engedélyezve a Microsoft Update, a Microsoft Update lap jelenik meg, és ha szeretné engedélyezni. Jelöljön ki egy elemet, majd válassza ki **következő**. Ellenkező esetben folytassa a következő lépéssel.

6. Az a **célmappa** lap, vagy hagyja az alapértelmezett mappába **C:\Program Files\OMS átjáró** vagy írja be a helyet, ahová az OMS-átjáró telepítéséhez. Ezután kattintson a **Tovább** gombra.

7. Az a **készen állnak a telepítésre** lapon jelölje be **telepítése**. Felhasználói fiókok felügyelete jelenhet meg kérelmező jogosultsága a telepítéséhez. Ha kérelmet kap engedélyt, válassza ki a **Igen**.

8. A telepítés befejezése után válassza ki a **Befejezés**. Ellenőrizheti, hogy fut-e a szolgáltatás nyissa meg a "Services.msc" parancsot beépülő modult, ügyelve arra, hogy **OMS átjáró** szolgáltatások listájában jelenik meg, és hogy annak állapotát **futtató**.

    ![Szolgáltatások – OMS-átjáró](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>A hálózati terheléselosztás konfigurálása 
Hálózati terheléselosztási (NLB) segítségével konfigurálhatja az átjárót a magas rendelkezésre állás. Használja a Microsoft hálózati terheléselosztás vagy hardveres terheléselosztókat.  A terheléselosztót irányítják át a kért kapcsolatokat az OMS-ügynököt vagy Operations Manager felügyeleti kiszolgálón a csomópontjai között forgalmat kezeli. Ha egy átjáró kiszolgáló leáll, a forgalom más csomópontok lekérdezi átirányítva.

Tervezéséhez és a Windows Server 2016-os hálózati terheléselosztási fürt telepítéséhez, lásd: [a hálózati terheléselosztás](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  A következő lépések a Microsoft hálózati terheléselosztási fürt konfigurálását ismertetik.  

1.  Jelentkezzen be egy rendszergazdai fiókot a Windows server, a hálózati Terheléselosztási fürt tagja.  

2.  A Kiszolgálókezelőben nyissa meg a hálózati terheléselosztás kezelőjét. Válassza ki **eszközök**, majd válassza ki **hálózati terheléselosztás kezelője**.

3. Az OMS-átjárókiszolgáló kapcsolódni a Microsoft Monitoring Agent telepítése, kattintson a jobb gombbal a fürt IP-címet, majd válassza ki **gazdagép hozzáadása fürthöz**.

 ![Hálózati terheléselosztás kezelője – betöltése vegye fel a gazdagépet a fürthöz](./media/log-analytics-oms-gateway/nlb02.png)

4. Adja meg az IP-cím, az átjárókiszolgáló, amelyhez kapcsolódni szeretne.

    ![Hálózati terheléselosztás kezelője – állomás hozzáadása fürthöz: csatlakozás](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Az OMS-ügynököt, és az Operations Manager felügyeleti csoport konfigurálása
Ebben a cikkben a következő szakaszok közvetlenül csatlakoztatott OMS-ügynököt, az Operations Manager felügyeleti csoport vagy Azure Automation hibrid forgatókönyv-feldolgozók konfigurálásáról az OMS-átjáróval kommunikálni az Azure Automation vagy Naplóelemzési lépéseket tartalmazza.  

Követelmények és az OMS-ügynököt a Windows rendszerű számítógépeken, amely közvetlenül csatlakozik a Log Analytics telepítésének lépéseit ismertetése: [adatokat gyűjteni a környezetében a Naplóelemzési](log-analytics-concept-hybrid.md#prerequisites).

 Linux-számítógépek esetén lásd: [Naplóelemzési csatlakozás Linux rendszerű számítógépeket](log-analytics-quick-collect-linux-computer.md). Az automatizálási hibrid forgatókönyv-feldolgozó kapcsolatos információkért lásd: [telepítése hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Az önálló OMS-ügynök konfigurálása
Az ügynök (amely ebben az esetben az átjáró) proxykiszolgálót használni konfigurálásával kapcsolatos további információkért lásd: [adatokat gyűjteni a környezetében a Naplóelemzési](log-analytics-concept-hybrid.md#prerequisites). Ha egy hálózati terheléselosztó mögött több átjárókiszolgáló telepített, akkor az OMS-ügynök proxykonfigurációt a hálózati Terheléselosztási virtuális IP-címe:

![Microsoft Monitoring Agent tulajdonságai – Proxybeállítások](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Adja meg az Operations Manager: az ügynökök azonos proxykiszolgáló használata
Az Operations Manager adja hozzá az átjáró-kiszolgálót konfigurálja.  Az Operations Manager proxykonfigurációt a rendszer automatikusan alkalmazza minden ügynököt, és az Operations Manager hoznak, még akkor is, ha a beállítás értéke üres.  

Az Operations Manager támogatásához az OMS-átjáró használatához a következő összetevőket kell teljesülniük:

* A Microsoft Monitoring Agent (ügynök verziója **8.0.10900.0** vagy újabb verzió) telepítve az átjárókiszolgáló és a Naplóelemzési munkaterület, amellyel kommunikálni szeretne konfigurálni.

* Egy átjáró, amely nem rendelkezik internetkapcsolattal vagy a proxy-kiszolgálóval, amelyet.

> [!NOTE]
> Ha nem adja meg az átjáró értékét, üres értékek vannak leküldve összes ügynököt.
> 

Ha az első alkalommal az Operations Manager felügyeleti csoportjának regisztrálás van a Naplóelemzési munkaterület, lehetőséget adja meg a proxykiszolgáló-konfigurációt a felügyeleti csoport nem az operatív konzolon érhető el. 

A felügyeleti csoportot ki kell sikeresen regisztrálva a szolgáltatásban érhető el ez a beállítás előtt. A rendszer proxy konfigurációját a Netsh segítségével ugyanarra a rendszerre, amelyen futtatja az operatív konzol és az összes felügyeleti kiszolgáló a felügyeleti csoport frissítése.

1. Nyisson meg egy rendszergazda jogú parancssort.

    a. Ugrás a **Start**. Írja be **cmd**.
    
    b. Kattintson a jobb gombbal **parancssor**. Válassza ki **Futtatás rendszergazdaként**.
    
2. Adja meg a következő parancsot, majd válassza ki **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Miután elvégezte a Log Analyticshez való integrációt, a módosítás futtatásával eltávolíthatja `netsh winhttp reset proxy`. Ezután a **proxy kiszolgáló konfigurálása** az operatív konzolon beállítással adhatja meg az OMS-átjárókiszolgáló. 

1. Nyissa meg az Operations Manager konzolt. A **Operations Management Suite**, jelölje be **kapcsolat**. Válassza ki **Proxy kiszolgáló konfigurálása**.

    ![Az Operations Manager – a proxykiszolgáló konfigurálása](./media/log-analytics-oms-gateway/scom01.png)

2. Válassza ki **proxykiszolgáló használata az Operations Management Suite eléréséhez**. Írja be az OMS-átjáró kiszolgáló IP-címét vagy a hálózati Terheléselosztási virtuális IP-címét. Győződjön meg arról, hogy a kiindulási pont a `http://` előtag.

    ![Az Operations Manager – a proxykiszolgáló címét](./media/log-analytics-oms-gateway/scom02.png)

3. Válassza a **Finish** (Befejezés) elemet. Az Operations Manager felügyeleti csoportjának konfigurálva van a Naplóelemzési szolgáltatásnak az átjárókiszolgálón keresztül kommunikálnak.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Adja meg az Operations Manager: egyedi ügynökök proxykiszolgáló használatára
Nagy vagy összetett környezetek előfordulhat, hogy csak kívánt kiszolgálók (vagy a csoportok) az OMS-átjárókiszolgáló. Az ezeken a kiszolgálókon nem frissíthető, az Operations Manager-ügynök közvetlenül, mert ez az érték felülírják a globális érték a következő felügyeleti csoporthoz. Helyette bírálja felül a szabályt, amellyel leküldéses ezeket az értékeket.  

> [!NOTE] 
> Ez a azonos konfigurációs módszer segítségével több OMS átjárókiszolgáló használatának engedélyezése a környezetben. Például szüksége lehet adott OMS-átjárókiszolgáló régió alapon adható meg.
>  

1. Nyissa meg az Operations Manager konzolt, majd válassza ki a **szerzői műveletek** munkaterületen.

2. Válassza a szerzői műveletek munkaterületének **szabályok**. Válassza ki a **hatókör** az Operations Manager gombjára. Ha ez a gomb nem érhető el, ellenőrizze, hogy győződjön meg arról, hogy rendelkezik-e egy objektumot, nem pedig mappát jelölt a **figyelés** ablaktáblán. A **felügyeleti csomag objektumai** párbeszédpanel közös célzott osztályok, csoportok vagy objektumok listáját jeleníti meg. 

3. az a **keressen** mezőbe írja be **Állapotfigyelő szolgáltatás**. Ezután válassza ki azt a listából. Kattintson az **OK** gombra.  

4. Keresse meg a szabály **Advisor Proxy beállítás szabály**. Az operatív konzol eszköztárán válassza **felülbírálások**. Válassza ki **bírálja felül a Rule\For osztály egy adott objektumához: Állapotfigyelő szolgáltatás**. 

5. Válassza ki, egy adott objektumot a listából. 

6. (Választható) Hozzon létre egy egyéni, amely tartalmazza az állapotfigyelő szolgáltatás objektum, amelyhez ez a felülbírálás alkalmazni kívánt kiszolgálót. Ehhez a csoporthoz, majd a felülbírálás majd alkalmazhatja.

7. Az a **felülbírálás tulajdonságai** párbeszédpanelen jelölje be a a **felülbírálása** oszlop a következő gombra a **WebProxyAddress** paraméter.  Az a **felülbírálás értékét** mezőbe írja be a kiszolgáló URL-CÍMÉT az OMS-átjáró, győződjön meg arról, hogy az kezdődik-e a `http://` előtag.  

    >[!NOTE]
    > Nem kell engedélyezni a szabályt. Azt már kezeli automatikusan egy felülbírálással, amely rendelkezik a Microsoft System Center Advisor biztonságos hivatkozás Override felügyeleti csomag tartalmazza, amely ezeket a Microsoft System Center Advisor figyelési kiszolgáló csoport.
    >   

8. Válassza a felügyeleti csomag a **célhelyként szolgáló felügyeleti csomag** listában, vagy hozzon létre egy új lezáratlan felügyeleti csomag kiválasztásával **új**. 

9. Amikor befejezte a módosításokat, válassza ki a **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Automatizálási hibrid forgatókönyv-feldolgozók az OMS-átjáró konfigurálása
Ha a környezetben Automation hibrid forgatókönyv-feldolgozóival, az alábbi lépések nyújtanak ideiglenes, manuális lehetséges megoldások konfigurálása az OMS-átjáró támogatja azokat.

A következő lépésekben hasznos tudnivalók az Azure-régió, ahol az Automation-fiók található. A hely található, a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az Azure Automation szolgáltatás.
3. Válassza ki a megfelelő Azure Automation-fiók.
4. A **hely**, a fiók a régió megtekintése.

    ![Azure portál – Automation fiókjának helye](./media/log-analytics-oms-gateway/location.png)  

Az alábbi táblázatok segítségével azonosíthatók a URL-címe, mindegyik helyen.

**Feladat futásidejű adatok URL-címei**

| **Hely** | **URL-cím** |
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

| **Hely** | **URL-cím** |
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

Ha a számítógép automatikusan regisztrálva van, a hibrid forgatókönyv-feldolgozók javítását a frissítés felügyeleti megoldást használni, kövesse az alábbi lépéseket:

1. A feladat futási adataihoz szolgáltatás URL-címek hozzáadása az engedélyezett állomások listájához, az OMS-átjárón. Írja be például a következőt: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Indítsa újra az OMS-átjáró szolgáltatás a következő PowerShell-parancsmag segítségével: `Restart-Service OMSGatewayService`

Ha a helyi parancsmaggal a hibrid forgatókönyv-feldolgozó regisztrálása, az Azure Automation a számítógép követi ezeket a lépéseket:

1. Az ügynök regisztrációs URL-címe hozzá az OMS-átjárón gazdakiszolgálóhoz listájához. Írja be például a következőt: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. A feladat futási adataihoz szolgáltatás URL-címek hozzáadása az engedélyezett állomások listájához, az OMS-átjárón. Írja be például a következőt: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Indítsa újra az OMS-átjáró szolgáltatás:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok
Parancsmagok segítségével feladatokat, amelyek szükségesek az OMS-átjáró konfigurációs beállításainak frissítése. Mielőtt azokat használni, ügyeljen arra, hogy a következő lépéseket:

1. Az OMS-átjáró (MSI) telepítéséhez.
2. Nyisson meg egy PowerShell-konzol ablakot.
3. A modul importálása a következő parancs beírásával: `Import-Module OMSGateway`.
4. Ha nincs hiba történt az előző lépésben, a modul importálása sikeres volt, és a parancsmag is használható. Gépelje be: `Get-Module OMSGateway`.
5. Módosítása után a parancsmagok használatával, győződjön meg arról, hogy az OMS-átjáró szolgáltatás újraindítása.

Ha hibaüzenetet kap a 3. lépésben, a modul nem importálható. A hiba akkor fordulhat elő, ha nem található a modul PowerShell. Az OMS-átjáró telepítési elérési úton található: *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **A parancsmag** | **Paraméterek** | **Leírás** | **Példa** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Kulcs |A szolgáltatás konfigurációjának beolvasása |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Key (required) <br> Érték |A szolgáltatás konfigurációját módosítja |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |A továbbító (fölérendelt) proxy címét |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Cím<br> Felhasználónév<br> Jelszó |Beállítja a továbbítási (fölérendelt) proxy címe (és hitelesítő adatok) |1. Állítsa be a továbbító proxy és a hitelesítő adat:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. A továbbító proxy hitelesítést nem igénylő beállításait: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Törölje a továbbítási proxybeállítást:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Lekérdezi a jelenleg engedélyezett állomás (csak a helyileg konfigurált; gazdagép nem tartalmaz engedélyezett gazdagépek automatikusan letöltött) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (required) |Hozzáadja a gazdagépet, az engedélyezett bővítmények listájához |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (required) |A gazdagép eltávolítása az engedélyezett bővítmények listájához |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Hozzáadja az ügyféltanúsítvány vonatkoznak az engedélyezett bővítmények listájához |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az ügyfél-tanúsítvány tulajdonosának eltávolítja az engedélyezett bővítmények listájához |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Lekérdezi a jelenleg engedélyezett ügyfél tanúsítványtulajdonosok (csak a helyileg konfigurált témák; nem tartalmazza automatikusan letöltött engedélyezett témák) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Hibaelhárítás
Az átjáró által naplózott eseményeket gyűjtő, az OMS-ügynököt futtató is rendelkeznie kell.

![Eseménynapló – OMS átjáró napló](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS átjáró esemény azonosítók és leírások**

Az alábbi táblázat a eseményazonosítók és az OMS-átjáró naplóeseményeket leírását:

| **Azonosító** | **Leírás** |
| --- | --- |
| 400 |Bármely alkalmazás hiba, amely nem rendelkezik egy adott. |
| 401 |Helytelen konfiguráció. Például: listenPort = "text" helyett egy egész számot. |
| 402 |A TLS-kézfogás üzenet elemzése kivétel. |
| 403 |Hálózati hiba történt. Például: nem lehet kapcsolódni a cél kiszolgálón. |
| 100 |Általános információkat. |
| 101 |Szolgáltatás elindult. |
| 102 |Szolgáltatás leállt. |
| 103 |HTTP-Csatlakozás parancsot kapott az ügyféltől. |
| 104 |Nem egy HTTP-csatlakozás parancsra. |
| 105 |Célkiszolgáló nem engedélyezett listában vagy a célport nincs biztonságos portja (443). <br> <br> Győződjön meg arról, hogy az átjárókiszolgálón az MMA ügynök és az ügynökök, amelyek az átjáró csatlakozik a ugyanazt a Naplóelemzési munkaterület. |
| 105 |Hiba TcpConnection – érvénytelen ügyféltanúsítvány: CN = átjáró <br><br> Győződjön meg arról, hogy: <br>    <br> -Használ egy átjáró verziószámú 1.0.395.0 vagy nagyobb. <br> – Az átjáró kiszolgálón a MMA ügynök és az ügynökök, amelyek az átjáró ugyanazon a Naplóelemzési munkaterület csatlakoznak. |
| 106 |Az OMS-átjáró csak akkor támogatja, a TLS 1.0, TLS 1.1-es és 1.2-es.  Nem támogatja az SSL. Nem támogatott a TLS/SSL protokoll bármilyen, az OMS-átjáró event ID 106 állít elő.|
| 107 |A TLS-munkamenet ellenőrzése befejeződött. |

**Teljesítményszámlálók gyűjtése**

Az alábbi táblázat a teljesítményszámlálót mutat be, az OMS-átjáró érhetők el. A számlálókat a Teljesítményfigyelő használatával adhat hozzá.

| **Name (Név)** | **Leírás** |
| --- | --- |
| OMS-aktív átjáró Ügyfélkapcsolat |Aktív ügyfél (TCP) hálózati kapcsolatok száma |
| OMS átjáró/hibák száma |Hibák száma |
| OMS átjáró/csatlakoztatva ügyfél |Kapcsolódó ügyfelek száma |
| OMS átjáró/elutasítása száma |A TLS-érvényesítési hiba miatt elutasítások számát |

![OMS-átjáró teljesítményszámlálói](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Segítségkérés
Ha be van jelentkezve az Azure-portálon, a segítségért az OMS-átjáró vagy bármely más Azure-szolgáltatások vagy a szolgáltatás funkciója is létrehozhat.

Segítségkérés, jelölje ki a portál jobb felső sarkában a kérdőjel szimbólum. Válassza ki **új támogatja a kérelem**. Töltse ki az új támogatási kérelem űrlapot.

![Új támogatási kérelem](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>További lépések
[Adatforrások hozzáadása](log-analytics-data-sources.md) adatokat gyűjteni a csatlakoztatott adatforrások és a Naplóelemzési munkaterület tárolja.
