---
title: Számítógépek csatlakoztatása a Log Analytics-átjáró használatával |} A Microsoft Docs
description: Csatlakozzon az eszközök és az Operations Manager által figyelt számítógépek a Log Analytics-átjáró segítségével adatokat lehet küldeni az Azure Automation és a Log Analytics szolgáltatást Ha nem rendelkeznek internet-hozzáféréssel.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: 81005c2c95c9cccb32796d1afca4208f5ff8b919
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437339"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Internetelérés nélküli számítógépek csatlakoztatása a Log Analytics-átjáró használatával az Azure monitorban

>[!NOTE]
>A Microsoft Azure Monitor átkerül a Microsoft Operations Management Suite (OMS), mert terminológia módosul. Ez a cikk az Azure Log Analytics-átjáró, OMS-átjáró hivatkozik. 
>

Ez a cikk a Log Analytics-átjáró használatával, amikor a számítógépek közvetlenül kapcsolódó, vagy az Operations Manager által figyelt, amely nincs internet-hozzáféréssel rendelkezik az Azure Automation és az Azure Monitor-kommunikáció konfigurálását ismerteti. 

A Log Analytics-átjáró, amely támogatja a HTTP-bújtatás a HTTP-csatlakozási paranccsal továbbítsa HTTP-proxy. Ezt az átjárót is adatokat gyűjteni, és küldje el az Azure Automation és a egy Log Analytics-munkaterületet az Azure monitorban az internethez nem csatlakozó számítógépek nevében.  

A Log Analytics-átjáró támogatja:

* Jelentéskészítési legfeljebb négy azonos Naplóelemzési munkaterület ügynökök, amelyek mögött, és az Azure Automation hibrid Runbook-feldolgozók konfigurált.  
* Windows-számítógép, amelyen a Microsoft Monitoring Agent közvetlenül csatlakozik az Azure monitorban Log Analytics-munkaterület.
* Linux rendszerű számítógépek, amelyeken a Linuxhoz készült Log Analytics-ügynököket közvetlenül csatlakozik az Azure monitorban Log Analytics-munkaterület.  
* A System Center Operations Manager 2012 SP1 UR7, Operations Manager 2012 R2 UR3 vagy a felügyeleti csoport Operations Manager 2016 vagy újabb verzió, amely integrálva van a Log Analytics.  

Egyes informatikai biztonsági szabályzatok nem engedélyezi a hálózati számítógépeket internet-kapcsolattal. Ezeket a számítógépeket nem lehet pont terminálok (POS) eszköz vagy az IT-szolgáltatások, például támogató kiszolgálók. Ezek az eszközök csatlakozni az Azure Automation vagy a Log Analytics-munkaterület kezelését és a figyelő őket, konfigurálja azokat a Log Analytics-átjáró közvetlenül kommunikáljon. A Log Analytics-átjáró konfigurációs adatokat és a továbbítási adatok saját nevükben való kapnak. Ha a számítógépek közvetlenül csatlakozhat a Log Analytics-munkaterületet a Log Analytics-ügynökkel rendelkező vannak konfigurálva, a számítógépek inkább kommunikálni a Log Analytics-átjáró.  

A Log Analytics-átjáró az ügynököktől származó történő adatátvitelhez a szolgáltatás közvetlenül. Az átvitt adatok nem elemzése.

Ha az Operations Manager felügyeleti csoport integrálva van a Log Analytics, a felügyeleti kiszolgálók beállítható úgy, hogy a konfigurációs adatokat fogad és küld összegyűjtött adatokat, attól függően, a megoldás engedélyezését a Log Analytics-átjárón .  Az Operations Manager-ügynököket a felügyeleti kiszolgáló néhány adatot küld. Ha például ügynökök Operations Manager riasztásokat, Konfigurációelemzési adatokat, példánytér és kapacitásadatait küldhet. Egyéb nagy mennyiségű adatok, például az Internet Information Services (IIS) naplók, teljesítményadatok és a biztonsági eseményekről, közvetlenül a Log Analytics-átjáró továbbítja. 

Ha egy vagy több Operations Manager átjáró kiszolgálókat a szegélyhálózaton vagy elszigetelt hálózat nem megbízható rendszerek figyeléséhez van telepítve, ezeken a kiszolgálókon a Log Analytics-átjáró nem tud kommunikálni.  Az Operations Manager átjárókiszolgáló csak egy felügyeleti kiszolgáló is jelentéseket.  Ha az Operations Manager felügyeleti csoport van konfigurálva a Log Analytics-átjáróval folytatott kommunikációban, a proxy konfigurációs adatait a rendszer automatikusan továbbítja minden ügynök által felügyelt számítógépre, amely az Azure monitorral Teljesítménynapló-adatok gyűjtésére van konfigurálva akkor is, ha az érték üres.    

A magas rendelkezésre állás a közvetlenül csatlakoztatott vagy a Log Analytics-munkaterület az átjárón keresztül kommunikáló Operations Management-csoportok használata a hálózati terheléselosztási (NLB) a forgalom elosztását és irányíthatja át több átjáró kiszolgálón. Ily módon egy átjárókiszolgáló leáll, ha a forgalmat a rendszer átirányítja egy másik csomópont érhető el.  

A számítógépen, amelyen a Log Analytics-átjáró azonosíthatja a Szolgáltatásvégpontok, amelyek az átjárót kell kommunikálnia a Log Analytics Windows-ügynök szükséges. Az azonos munkaterületeknek való jelentés érdekében az átjáró közvetlen is kell az ügynököt, hogy az ügynök vagy Operations Manager felügyeleti csoportban az átjáró mögött van konfigurálva. Ez a konfiguráció lehetővé teszi, hogy az átjáró és az ügynök kommunikálni a hozzárendelt munkaterületet.

Az átjáró lehet többhelyű legfeljebb négy munkaterületeket. Ez a munkaterület egy Windows-ügynökök által támogatott teljes száma.  

Minden ügynök hálózati kapcsolat és az átjáró rendelkeznie kell, hogy az ügynökök automatikusan áthelyezhet adat és az átjáró. Kerülje el az átjárót egy tartományvezérlőre történő telepítése.

Az alábbi ábrán látható, a közvetlen ügynökök, az Azure Automation és a Log Analytics-átjárón keresztül áramló adatok. A proxykonfigurációjának meg kell egyeznie a port, amelyet a Log Analytics-átjáró van konfigurálva.  

![Közvetlen ügynök kommunikációs szolgáltatásokkal ábrája](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Az alábbi ábrán az adatfolyam egy Operations Manager felügyeleti csoportból a Log Analytics szolgáltatásba.   

![A Log Analytics az Operations Manager kommunikációs ábrája](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>A rendszer beállítása

A Log Analytics-átjáró futtatására kijelölt számítógépek az alábbi konfigurációval kell rendelkeznie:

* A Windows 10, Windows 8.1 vagy Windows 7
* A Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 vagy Windows Server 2008
* A Microsoft .NET-keretrendszer 4.5
* Legalább egy 4 magos processzor- és 8 GB memóriát 
* A [Log Analytics-ügynököket for Windows](agent-windows.md) jelentés ugyanazon a munkaterületen található, mint az ügynökök az átjárón keresztül kommunikáló megfelelően van konfigurálva

### <a name="language-availability"></a>Nyelvi rendelkezésre állása

A Log Analytics-átjáró az alábbi nyelveken érhető el:

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
A Log Analytics-átjáró támogatja a csak Transport Layer Security (TLS) 1.0, 1.1 és 1.2-es.  Nem támogatja a Secure Sockets Layer (SSL).  A Log Analytics az átvitt adatok biztonságának biztosítása érdekében az átjáró használatához legalább konfigurálása a TLS 1.2. Régebbi verziói a TLS vagy SSL ki téve. Bár a jelenleg engedélyezett előző verziókkal való kompatibilitás, ne használja őket.  

További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Támogatott ügynök-kapcsolatok száma
Az alábbi táblázat körülbelül hány ügynökök kommunikálni tudjanak egy átjárókiszolgáló. Támogatási töltse fel az adatok körülbelül 200 KB-os 6 másodpercenként ügynökök alapul. Minden ügynök tesztelt adatmennyiség naponta körülbelül 2.7-es GB.

|Átjáró |Ügynökök támogatott (becsült)|  
|--------|----------------------------------|  
|CPU: Intel Xeon E5-2660 v3 processzor \@ 2,6 GHz-es 2 mag<br> Memória: 4 GB<br> Hálózati sávszélesség: 1 Gbps| 600|  
|CPU: Intel Xeon E5-2660 v3 processzor \@ 2,6 GHz-es 4 mag<br> Memória: 8 GB<br> Hálózati sávszélesség: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>A Log Analytics-átjáró letöltése

A Log Analytics-átjáró telepítési fájl legújabb verziójának beszerzéséhez vagy [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) vagy az Azure Portalon.

Az Azure Portalról a Log Analytics-átjáró lekéréséhez kövesse az alábbi lépéseket:

1. Keresse meg a szolgáltatások listájában, és válassza ki **Log Analytics**. 
1. Jelöljön ki egy munkaterületet.
1. A munkaterület panel alatt **általános**válassza **gyors üzembe helyezés**. 
1. Alatt **válasszon egy a munkaterülethez csatlakoztatandó adatforrást**válassza **számítógépek**.
1. Az a **közvetlen ügynök** panelen válassza ki **töltse le a Log Analytics-átjáró**.
 
   ![Képernyőkép – a lépéseket a Log Analytics-átjáró letöltése](./media/gateway/download-gateway.png)

vagy 

1. A munkaterület panel alatt **beállítások**válassza **speciális beállítások**.
1. Lépjen a **csatlakoztatott források** > **Windows kiszolgálók** válassza **töltse le a Log Analytics-átjáró**.

## <a name="install-the-log-analytics-gateway"></a>A Log Analytics-átjáró telepítése

Az átjáró telepítéséhez kövesse az alábbi lépéseket.  (Ha nevű Log Analytics-továbbító egy korábbi verzióját telepítette, akkor frissül erre a kiadásra.)

1. Kattintson duplán a célmappa **Log Analytics gateway.msi**.
1. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.

   ![Képernyőkép a kezdőlapja az átjáró varázsló](./media/gateway/gateway-wizard01.png)

1. Az a **licencszerződés** lapon jelölje be **elfogadom a licencszerződés feltételeit** vállalja, hogy a Microsoft szoftverlicenc-feltételeket, és válassza ki a **tovább**.
1. Az a **Port és a proxykiszolgáló címét** oldalon:

   a. Adja meg az átjáró használni a TCP-port száma. A telepítő a Windows tűzfal bejövő szabály konfigurálása ezt a portszámot használja.  Az alapértelmezett érték: 8080-as.
      A portszám érvényes tartománya 1 és 65535. Ha a bemeneti nem esik a tartományba, hibaüzenet jelenik meg.

   b. Ha a kiszolgáló, amelyen az átjáró telepítve van egy proxyn keresztül történő kommunikációhoz van szüksége, adja meg a proxykiszolgáló címét, amelyen az átjáró kell csatlakoztatnia. Adja meg például a következőt: `http://myorgname.corp.contoso.com:80`.  Ha ezt a mezőt üresen hagyja, az átjáró megpróbálja közvetlenül csatlakozik az internethez.  Ha a proxykiszolgáló hitelesítést igényel, adjon meg egy felhasználónevet és jelszót.

   c. Kattintson a **Tovább** gombra.

   ![Az átjáró-proxy konfigurációs képernyőképe](./media/gateway/gateway-wizard02.png)

1. Nincs engedélyezve a Microsoft Update, a Microsoft Update lapon jelenik meg és lehet váltani, az engedélyezéshez. Jelölje ki, majd **tovább**. Ellenkező esetben folytassa a következő lépéssel.
1. Az a **célmappa** lapon hagyja bejelölve az alapértelmezett mappa: C:\Program Files\OMS átjáró, vagy adja meg a helyet, amelyre telepíteni az átjárót. Ezután kattintson a **Tovább** gombra.
1. Az a **készen áll a telepítésre** lapon jelölje be **telepítése**. Ha a felhasználói fiókok felügyeletének telepítése engedélyt kér, válassza ki a **Igen**.
1. A telepítés befejezése után jelölje ki a **Befejezés**. Győződjön meg arról, hogy a szolgáltatás fut-e, nyissa meg a services.msc beépülő modult, és ellenőrizze, hogy **OMS-átjáró** szolgáltatások listájában jelenik meg, és hogy annak állapotát **futó**.

   ![Képernyőfelvétel a helyi szolgáltatások, hogy az OMS-átjáró fut.](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Hálózati terheléselosztás beállítása 
Az átjáró magas rendelkezésre álláshoz használatával a hálózati terheléselosztás (NLB) vagy a Microsoft segítségével konfigurálhatja [hálózati terheléselosztás (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md), vagy a hardveres terheléselosztók. A terheléselosztó forgalmat kezeli a csomópontok között a Log Analytics-ügynökök kért kapcsolatot, vagy az Operations Manager felügyeleti kiszolgálók átirányításával. Egy átjáró kiszolgáló leáll, ha más csomópontokhoz átirányítja a forgalmat.

### <a name="microsoft-network-load-balancing"></a>A Microsoft hálózati terheléselosztás
Ismerje meg, hogyan tervezhet és a egy Windows Server 2016 hálózati terheléselosztási fürt üzembe helyezése, lásd: [a hálózati terheléselosztás](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Az alábbi lépéseket a Microsoft hálózati terheléselosztási fürt konfigurálását ismertetik.  

1. Jelentkezzen be a Windows server, amely tagja a rendszergazdák rendszergazdai fiókkal az NLB-fürt.  
2. Hálózati terheléselosztás kezelőjének megnyitása a Kiszolgálókezelőben, kattintson a **eszközök**, és kattintson a **a hálózati terheléselosztás kezelője**.
3. A Microsoft Monitoring Agent telepítve van egy Log Analytics-átjáró kiszolgáló kapcsolódni, kattintson a jobb gombbal a fürt IP-címét, és kattintson **gazdagép hozzáadása fürthöz**. 

    ![Hálózati terheléselosztás kezelője – betöltéséhez adja hozzá a gazdagépet a fürthöz](./media/gateway/nlb02.png)
 
4. Adja meg az IP-cím, az átjárókiszolgáló, amely kapcsolódni szeretne. 

    ![Hálózati terheléselosztás kezelője – betöltéséhez adja hozzá a gazdagépet a fürthöz: Kapcsolódás](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Ismerje meg, hogyan tervezhet és üzembe helyezése az Azure Load Balancerhez, lásd: [Mi az Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Alapszintű load balancer telepítéséhez kövesse a témakörben ismertetett lépéseket [rövid](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) kivételével a szakaszban ismertetett lépéseket **háttérkiszolgálók létrehozása**.   

> [!NOTE]
> Az Azure Load Balancer használatával konfigurálása a **alapszintű Termékváltozat**, megköveteli, hogy Azure-beli virtuális gépek rendelkezésre állási csoportba tartozik. A rendelkezésre állási csoportok kapcsolatos további információkért lásd: [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése](../../virtual-machines/windows/manage-availability.md). Meglévő virtuális gépek hozzáadása egy rendelkezésre állási csoporthoz, tekintse meg [állítsa be az Azure Resource Manager virtuális gép rendelkezésre állási csoport](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

A terheléselosztó létrehozása után egy háttérkészlet kell létrehozni, amely elosztja a forgalmat egy vagy több átjáró kiszolgáló között. A rövid útmutató a cikk a szakaszban ismertetett lépéseket követve [a terheléselosztó-erőforrások létrehozása](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Az állapotminta konfigurálásakor azt az átjárókiszolgáló TCP-port használatára kell konfigurálni. Az állapotminta dinamikusan hozzáadja vagy eltávolítja az átjárókiszolgálók az adott válaszuk alapján load balancer elforgatási. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>A Log Analytics-ügynököket és az Operations Manager felügyeleti csoport konfigurálása
Ebben a szakaszban láthatja a Log Analytics-átjáróval kommunikálni az Azure Automation és a Log Analytics közvetlenül csatlakoztatott Log Analytics-ügynökök, az Operations Manager felügyeleti csoport vagy az Azure Automation hibrid Runbook-feldolgozók konfigurálásáról.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Egy önálló Log Analytics-ügynök konfigurálása
A Log Analytics-ügynök telepítése a proxy server értékét cserélje az IP-címét a Log Analytics-átjáró kiszolgáló vagy a port számát. Ha több átjárókiszolgáló egy terheléselosztó mögé telepített, akkor a Log Analytics proxykonfigurációjának a terheléselosztó virtuális IP-címét.  

>[!NOTE]
>A Log Analytics-ügynök telepítése az átjáró és a Windows-számítógép, amely közvetlenül csatlakozik a Log Analytics: [a Log Analytics szolgáltatás az Azure-ban való csatlakozáshoz Windows számítógépek](agent-windows.md). Linux rendszerű számítógépek csatlakoztatását, lásd: [egy Linux rendszerű számítógépek a Log Analytics-ügynököket konfigurálhatja a hibrid környezetben](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Miután telepítette az ügynököt az átjárókiszolgáló, konfigurálja azt, hogy a munkaterületet, vagy az átjáróval kommunikáló ügynökök munkaterületen. Ha a Log Analytics Windows-ügynök nincs telepítve az átjáróra, 300 esemény bekerül az OMS-átjáró Eseménynapló, amely azt jelzi, hogy az ügynök telepítve kell lennie. Ha az ügynök telepítve van, de nincs beállítva a jelentés ugyanazon a munkaterületen található, mint az ügynökök rajta keresztül kommunikáló, események 105 írása ugyanabba a naplófájlba, amely azt jelzi, hogy kell-e az ügynököt az átjárón ugyanazon a munkaterületen található, mint az ügynökök jelentést kell konfigurálni, hogy co az átjáróval mmunicate.

Konfiguráció befejezése után indítsa újra az OMS-átjáró szolgáltatást, a módosítások életbe léptetéséhez. Ellenkező esetben az átjáró elutasítja az ügynökök, amelyek próbál meg kommunikálni a Log Analytics és az OMS-átjáró eseménynaplóban 105 esemény jelentést. Ez is történik, ha hozzáad vagy eltávolít egy munkaterületet az átjáró kiszolgálón az ügynök konfigurációjából.   

Az Automation hibrid Runbook-feldolgozó kapcsolatos információkért lásd: [automatizálhatja az erőforrások az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Beállítása az Operations Managerben, ahol az összes ügynöknek azonos proxykiszolgáló használata
Az Operations Manager proxykonfigurációt a rendszer automatikusan alkalmazza az ügynökök, amelyek az Operations Managerrel, jelentést, még akkor is, ha a beállítás értéke üres.  

OMS-átjáró használatával támogatja az Operations Manager, kell rendelkeznie:

* A Microsoft Monitoring Agent (8.0.10900.0 verzió vagy újabb verzió) az OMS-átjáró kiszolgálón telepítse és konfigurálja az ugyanazon a Log Analytics-munkaterületek, hogy a felügyeleti csoportban való jelentésre van konfigurálva.
* Internetkapcsolat. Azt is megteheti OMS-átjáró kapcsolódnia kell egy proxykiszolgálóra, amely csatlakozik az internethez.

> [!NOTE]
> Ha az átjáró nincs érték megadva, az ügynökök kerüljenek üres értékeket.
>

Az Operations Manager felügyeleti csoport első alkalommal regisztrál a Log Analytics-munkaterület, ha nem jelenik meg arra, hogy az operatív konzolon adja meg a felügyeleti csoport proxy beállításait. Ez a beállítás csak akkor, ha a felügyeleti csoport regisztrálva a szolgáltatásban érhető el.  

-Integráció konfigurálása, frissítse a rendszer proxykonfigurációt a Netsh használatával, a rendszer, ahol futtatja az operatív konzol és a felügyeleti csoport összes felügyeleti kiszolgálón. Kövesse az alábbi lépéseket:

1. Nyisson meg egy rendszergazda jogú parancssorból:

   a. Válassza ki **Start** , és adja meg **cmd**.  

   b. Kattintson a jobb gombbal **parancssor** válassza **Futtatás rendszergazdaként**.  

1. Írja be a következő parancsot:

   `netsh winhttp set proxy <proxy>:<port>`

Miután befejezte a Log Analytics-integráció, távolítsa el a módosítást futtatásával `netsh winhttp reset proxy`. Ezután az operatív konzolon, használja a **Configure proxy server** beállítással azt adhatja meg a Log Analytics-átjáró kiszolgáló. 

1. Az Operations Manager konzolon alatt **Operations Management Suite**válassza **kapcsolat**, majd válassza ki **proxykiszolgáló konfigurálása**.

   ![Képernyőkép az Operations Manager megjelenítése a kijelölt Proxy kiszolgáló konfigurálása](./media/gateway/scom01.png)

1. Válassza ki **proxykiszolgáló használata az Operations Management Suite eléréséhez** majd adja meg a Log Analytics-átjáró kiszolgálójának IP-cím vagy a terheléselosztó virtuális IP-címét. Ügyeljen arra, hogy előtaggal kezdődik `http://`.

   ![Képernyőkép az Operations Manager, a proxy-kiszolgáló címe megjelenítése](./media/gateway/scom02.png)

1. Válassza a **Finish** (Befejezés) elemet. Az Operations Manager felügyeleti csoportban most már a Log Analytics szolgáltatás az átjárókiszolgálón keresztül kommunikációra van konfigurálva.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Beállítása az Operations Managerben, ahol az egyedi ügynökök proxykiszolgáló használata
Nagy vagy összetett környezetek esetében érdemes vagy csak adott kiszolgálók (vagy) a Log Analytics-átjáró kiszolgáló használatára.  Ezek a kiszolgálók esetében nem frissíthető, az Operations Manager-ügynök közvetlenül, mert ez az érték felülírják a globális érték a felügyeleti csoport.  Ehelyett ezeket az értékeket leküldéses használt szabály felülbírálása.  

> [!NOTE] 
> Ezt a konfigurációs módszert használja, ha azt szeretné, hogy több Log Analytics-átjáró kiszolgáló a környezetben.  Megkövetelheti például adott Log Analytics-átjáró kiszolgálókon meg kell adni a regionális szinten.
>

Kiszolgálók vagy csoportok használata a Log Analytics-átjáró kiszolgáló beállítása: 

1. Nyissa meg az Operations Manager konzolt, és válassza ki a **szerzői műveletek** munkaterületen.  
1. Válassza a szerzői műveletek munkaterületének **szabályok**. 
1. Az Operations Manager eszköztárán válassza a **hatókör** gombra. Ha ez a gomb nem érhető el, ellenőrizze, hogy a kiválasztott egy objektumot, nem pedig mappát a **figyelés** ablaktáblán. A **felügyeleti csomag objektumai** párbeszédpanel közös célzott osztályok, csoportok és objektumok listáját jeleníti meg. 
1. Az a **keressen** írja be a következőt **Állapotfigyelő szolgáltatás** , és jelölje ki a listából. Kattintson az **OK** gombra.  
1. Keresse meg **Advisor beállítás szabály**. 
1. Az Operations Manager eszköztárán válassza **felülbírálások** , majd mutasson a **bírálja felül a Rule\For osztály egy adott objektumához: Állapotfigyelő szolgáltatás** , és válasszon ki egy objektumot a listából.  Vagy hozzon létre egy egyéni csoportot, amely tartalmazza a health service objektum a kiszolgálók, ez a felülbírálás a alkalmazni szeretné. Ezután alkalmazza az egyéni csoportot a felülbírálás.
1. Az a **felülbírálás tulajdonságai** párbeszédpanelen adjon hozzá egy négyzet be van jelölve a **felülbírálása** oszlop melletti a **WebProxyAddress** paraméter.  Az a **felülbírálás értéke** mezőben adja meg a Log Analytics-átjáró kiszolgáló URL-CÍMÉT. Ügyeljen arra, hogy előtaggal kezdődik `http://`.  

    >[!NOTE]
    > Nem kívánja engedélyezni a szabályt. Azt már felügyelt automatikusan egy felülbírálással, a Microsoft System Center Advisor biztonságos hivatkozás felülbírálhatja felügyeleti csomagban, amelynek a célja a Microsoft System Center Advisor figyelési kiszolgáló csoport.
    > 

1. Válassza ki a felügyeleti csomagot a **célhelyként szolgáló felügyeleti csomag** listában, vagy hozzon létre egy új, lezáratlan felügyeleti csomag kiválasztásával **új**. 
1. Ha elkészült, kattintson az **OK** gombra. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Az Automation hibrid Runbook-feldolgozók konfigurálása
Ha Automation hibrid Runbook-feldolgozók a környezetében, kövesse az alábbi lépéseket a manuális, ideiglenes lehetséges megoldások a dolgozók támogatása az OMS-átjáró konfigurálása.

A jelen szakaszban ismertetett lépések követéséhez, ismernie kell az Automation-fiókot tartalmazó Azure-régióban. Adott helyen található:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki az Azure Automation szolgáltatást.
1. Válassza ki a megfelelő Azure Automation-fiókot.
1. Megtekintheti a régió alatt **hely**.

   ![Az Automation-fiók helye az Azure Portal képernyőképe](./media/gateway/location.png)

Az alábbi táblázat segítségével azonosíthatja az egyes helyeken az URL-cím.

**Feladat Data modulu Runtime URL-címei**

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

**Az ügynök szolgáltatás URL-címek**

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

Ha a számítógép automatikusan hibrid Runbook-feldolgozók regisztrált, az Update Management megoldás segítségével kezelheti a javítás. Kövesse az alábbi lépéseket:

1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeinek a gazdagépen engedélyezett a Log Analytics-átjáró-listája. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics-átjáró szolgáltatás a következő PowerShell-parancsmag segítségével: `Restart-Service OMSGatewayService`

Ha a számítógép csatlakozik az Azure Automation hibrid Runbook-feldolgozó regisztrációs parancsmag használatával, kövesse az alábbi lépéseket:

1. A Log Analytics-átjáró engedélyezett gazdagép listájában adja hozzá az ügynök regisztrációs URL-címe. Például:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adja hozzá a Feladatadatok futásidejű szolgáltatás URL-címeinek a gazdagépen engedélyezett a Log Analytics-átjáró-listája. Például:`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Indítsa újra a Log Analytics-átjáró szolgáltatást.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok
Parancsmagok segítségével hajtsa végre a feladatokat a Log Analytics-átjáró konfigurációs beállításainak frissítése. Mielőtt parancsmagokat használni, ügyeljen arra, hogy:

1. Telepítse a Log Analytics-átjáró (Microsoft Windows Installer).
1. Nyisson meg egy PowerShell-konzolablakot.
1. A modul importálása a következő parancs beírásával: `Import-Module OMSGateway`
1. Ha nem történt hiba az előző lépésben, a modul importálása sikeresen megtörtént, és a parancsmag is használható. Adja meg `Get-Module OMSGateway`
1. Miután a parancsmagok használatával módosítani, indítsa újra az OMS-átjáró szolgáltatás.

Hiba a 3. lépésben, az azt jelenti, hogy a modul nem lett importálva. A hiba akkor fordulhat elő, ha a PowerShell a modul nem található. A modul az OMS-átjáró telepítési útvonal találhatja meg: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **A parancsmag** | **Paraméterek** | **Leírás** | **Példa** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Kulcs |A szolgáltatás konfigurációjának beolvasása |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Kulcs (kötelező) <br> Érték |A szolgáltatás konfigurációjának módosítása |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Relay-(upstream) proxy címét |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Cím<br> Felhasználónév<br> Jelszó |Beállítja a relay (upstream) proxy címét (vagy hitelesítő adatok) |1. Állítsa be a relay-proxy és a hitelesítő adatokat:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. A relay proxy hitelesítést nem igénylő beállításait: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. A relay-proxybeállítást törléséhez:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |A jelenleg engedélyezett gazdagép beolvasása (csak a helyileg konfigurált engedélyezett gazdagép nem, automatikusan letölti a gazdagépek engedélyezett) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |A gazdagép (kötelező) |Hozzáadja a gazdagépet, az engedélyezett listára |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |A gazdagép (kötelező) |A gazdagép eltávolítása az engedélyezett listára |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az engedélyezett listára vonatkoznak az ügyféltanúsítvány hozzáadása |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Tulajdonos (kötelező) |Az ügyfél-tanúsítvány tulajdonosának távolít el az engedélyezett listára |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Lekérdezi a tanúsítványtulajdonosok a jelenleg engedélyezett ügyfél (csak a helyileg konfigurált engedélyezett témák, nem, automatikusan letölti a tulajdonosok engedélyezett) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Hibaelhárítás
Az átjáró által naplózott eseményeket gyűjtő, rendelkeznie kell a Log Analytics-ügynök telepítve van.

![Képernyőkép a Log Analytics gateway-naplók az Eseménynapló listájáról](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics gateway esemény azonosítók és leírások

Az alábbi táblázat a eseményazonosítók és a Log Analytics-átjáró alkalmazásnapló-események leírását.

| **Azonosító** | **Leírás** |
| --- | --- |
| 400 |Bármilyen alkalmazáshibát, amelynek nincs egyedi azonosítója. |
| 401 |Helytelen konfiguráció. Ha például listenPort = "text" helyett egy egész számot. |
| 402 |Kivétel a TLS-kézfogás üzenet elemzésekor. |
| 403 |Hálózati hiba történt. Ha például nem lehet csatlakozni célkiszolgáló. |
| 100 |Általános információk. |
| 101 |Szolgáltatás elindult. |
| 102 |Szolgáltatás leállt. |
| 103 |Egy HTTP-csatlakozási parancsot az ügyféltől érkezett. |
| 104 |Nem egy HTTP-csatlakozási parancsot. |
| 105 |Célkiszolgáló nem szerepel az engedélyezési listán, vagy célport nem biztonságos (443). <br> <br> Győződjön meg arról, hogy az MMA-ügynök az OMS-átjáró kiszolgálón, és az OMS-átjáróval kommunikáló ügynökök vannak csatlakoztatva a Log Analytics munkaterületén. |
| 105 |Hiba TcpConnection – érvénytelen ügyféltanúsítvány: CN=Gateway. <br><br> Győződjön meg arról, hogy használ-e az OMS-átjáró 1.0.395.0 verzió vagy újabb. Emellett győződjön meg arról, hogy az MMA-ügynök az OMS-átjáró kiszolgálón, és az OMS-átjáróval kommunikáló ügynökök csatlakoztatva a Log Analytics munkaterületén. |
| 106 |A TLS/SSL protokoll verziója nem támogatott.<br><br> A Log Analytics-átjáró támogatja a csak a TLS 1.0, TLS 1.1 és 1.2-es. Nem támogatja az SSL.|
| 107 |A TLS-munkamenet ellenőriztük. |

### <a name="performance-counters-to-collect"></a>Teljesítményszámlálók gyűjtése

Az alábbi táblázat a Log Analytics-átjáró esetében rendelkezésre álló teljesítményszámlálók. Vegye fel a számlálókat a Teljesítményfigyelő segítségével.

| **Name (Név)** | **Leírás** |
| --- | --- |
| Log Analytics-aktív átjáró ügyfél-kapcsolat |Aktív ügyfél (TCP) hálózati kapcsolatok száma |
| Log Analytics-átjáró/hibák száma |Hibák száma |
| Log Analytics-átjáró/csatlakozott ügyfél |Csatlakoztatott ügyfelek száma |
| Log Analytics Gateway/elutasító száma |Bármely TLS-érvényesítési hiba miatt elutasítások száma |

![Képernyőkép a Log Analytics átjáróinterfész teljesítményszámlálók megjelenítése](./media/gateway/counters.png)

## <a name="assistance"></a>Segítségnyújtás
Ha be van jelentkezve az Azure Portalon, a Log Analytics-átjáró vagy bármely más Azure-szolgáltatás vagy funkció segítséget kaphat.
Segítséget szeretne kérni, kattintson a kérdőjel ikonra a portálon, válassza a jobb felső sarkában **új támogatási kérelem**. Végezze el az új támogatási űrlap.

![Képernyőkép egy új támogatási kérelem](./media/gateway/support.png)

## <a name="next-steps"></a>További lépések
[Adatforrások hozzáadása](../../azure-monitor/platform/agent-data-sources.md) csatlakoztatott forrásból gyűjthet adatokat, és tárolhatja az adatokat a Log Analytics-munkaterületre.