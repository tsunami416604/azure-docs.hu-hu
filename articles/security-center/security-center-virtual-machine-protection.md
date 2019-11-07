---
title: Azure Security Center gépek és alkalmazások védetté való ellátása
description: Ez a dokumentum olyan Security Center javaslatokat tartalmaz, amelyek segítenek a virtuális gépek és számítógépek, valamint a webalkalmazások és a App Service környezetek védelmében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 803e64c9df1b52a33a1b50714f77b005032bf200
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686355"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>A gépek és alkalmazások védelme Azure Security Center
Azure Security Center elemzi az Azure-erőforrások, a nem Azure-kiszolgálók és a virtuális gépek biztonsági állapotát. Ha a Security Center felismeri a lehetséges biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán. A javaslatok az Azure-erőforrásokra vonatkoznak: Virtual Machines (VM) és Computers, Applications, Networking, SQL, és Identity and Access.

Ez a cikk a gépekre és alkalmazásokra vonatkozó ajánlásokat tárgyalja.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
A **Security Center – áttekintés** irányítópulton nyomon követheti az erőforrások biztonsági állapotát. Az **erőforrások** szakasz tartalmazza az azonosított problémák számát és az egyes erőforrástípusok biztonsági állapotát.

Az összes probléma listáját megtekintheti a **javaslatok**lehetőség kiválasztásával. A javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).

A számítási és az App Services-javaslatok teljes listáját a [javaslatok](security-center-virtual-machine-protection.md#compute-and-app-recs)című részben tekintheti meg.

A folytatáshoz válassza a **számítási & alkalmazások** az **erőforrások** vagy a Security Center főmenü lehetőséget.
![Security Center irányítópult](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>A számítási és az App Services figyelése
A **számítási & alkalmazások**területen a következő lapok találhatók:

- **Áttekintés**: Monitorozás és a Security Center által azonosított javaslatok.
- **Virtuális gépek és számítógépek**: a virtuális gépek és számítógépek listája, valamint mindegyikre az aktuális biztonsági állapota.
- **Felhőszolgáltatások**: a Security Center által figyelt összes webes és feldolgozói szerepkör listája.
- **App Services**: az App Service-környezetek és az egyes alkalmazások aktuális biztonsági állapotának listája.
- **Tárolók**: a tárolók listája és a konfigurációjuk biztonsági értékelése.
- **Számítási erőforrások (előzetes verzió)** : a számítási erőforrások, például a Service Fabric-fürtök és az Event hubok javaslatainak listája.

A folytatáshoz válassza a **számítási & alkalmazások** az **erőforrás biztonsági higiéniája**alatt lehetőséget.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Minden egyes lap esetében több szakaszt hozhat létre, és az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg az adott probléma megoldásához szükséges lépésekről.

### Nem figyelt virtuális gépek és számítógépek<a name="unmonitored-vms-and-computers"></a>
Security Center ha a gép nem a Microsoft monitoring Agent bővítményt futtatja, a rendszer nem figyeli a virtuális gépet vagy számítógépet. Előfordulhat, hogy egy gépen már telepítve van egy helyi ügynök, például a OMS Direct ügynök vagy a System Center Operations Manager ügynök. Az ezekkel az ügynökökkel rendelkező gépeket a rendszer nem figyeli, mivel ezek az ügynökök nem teljes mértékben támogatottak Security Centerban. A Security Center összes funkciójának legteljesebb kihasználása érdekében szükség van a Microsoft Monitoring Agent bővítményre.

A bővítményt a már telepített helyi ügynökön kívül is telepítheti a nem figyelt virtuális gépre vagy a számítógépre. A két ügynök konfigurációja legyen megegyező, és ugyanahhoz a munkaterülethez csatlakoztassa őket. Ez lehetővé teszi, hogy a Security Center interakcióba léphessen a Microsoft Monitoring Agent bővítménnyel, és adatokat gyűjtsön. [A virtuálisgép-bővítmény engedélyezésével](../azure-monitor/learn/quick-collect-azurevm.md) foglalkozó témakörben találja a Microsoft Monitoring Agent bővítmény telepítésével kapcsolatos utasításokat.

[A Monitoring Agent állapotproblémái](security-center-troubleshooting-guide.md#mon-agent) szakaszban többet is megtudhat arról, hogy a Security Center miért nem tudja sikeresen monitorozni az automatikus üzembe helyezésre inicializált virtuális gépeket és számítógépeket.

### <a name="recommendations"></a>Javaslatok
Ez a szakasz ajánlásokat tartalmaz az egyes virtuális gépekre és számítógépekre, a webes és feldolgozói szerepkörökre, a Azure App Service Web Appsra és a Security Center figyelők Azure App Service Environment. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett erőforrások teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja.

Mindegyik javaslat olyan műveleteket tartalmaz, amelyeket a kiválasztása után elvégezhet. Ha például a **hiányzó rendszerfrissítések**lehetőséget választja, akkor a virtuális gépek és a hiányzó javításokat tartalmazó számítógépek száma, valamint a hiányzó frissítés súlyossága jelenik meg.

A **rendszerfrissítések alkalmazásával** összefoglalja a kritikus frissítéseket egy gráf formátumban, egy a Windowshoz, egy pedig a Linux rendszerhez. A második részben az alábbi információkat tartalmazó táblázat található:

- **NÉV** – A hiányzó frissítés neve.
- **Nem. Virtuális gépek & számítógépek**: azon virtuális gépek és számítógépek teljes száma, amelyek esetében ez a frissítés hiányzik.
- **Frissítés súlyossága**: az adott javaslat súlyosságát írja le:

    - **Kritikus**: a biztonsági rés egy értelmes erőforrással (alkalmazás, virtuális gép vagy hálózati biztonsági csoport) van, és figyelmet igényel.
    - **Fontos**: nem kritikus vagy további lépések szükségesek egy folyamat végrehajtásához vagy egy biztonsági rés megszüntetéséhez.
    - **Mérsékelt**: A biztonsági rést orvosolni kell, de nincs szükség azonnali beavatkozásra. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)


- **STATE** (Állapot): a javaslat aktuális állapota:

    - **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
    - **In Progress** (Folyamatban): folyamatban van a javaslat alkalmazása az érintett erőforrásokra, további lépésekre nincs szükség.
    - **Resolved** (Megoldott): a javaslat alkalmazása megtörtént. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

A javaslat részleteinek megtekintéséhez kattintson a hiányzó frissítés nevére a listában.


> [!NOTE]
> A biztonsági javaslatok itt ugyanazok, mint a **javaslatok** csempén. A javaslatok megoldásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Virtuális gépek és számítógépek
A virtuális gépek és számítógépek szakasz áttekintést nyújt az összes virtuálisgép-és számítógép-javaslatról. Minden oszlop egy javaslatcsoportot képvisel.

![Javaslatok virtuális gépekhez és számítógépekhez](./media/security-center-virtual-machine-recommendations/vm-computers.png)

A listában négyféle ikon látható:

![Nem Azure-beli számítógép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![Azure Resource Manager virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager virtuális gép.

![Klasszikus Azure-beli virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasszikus Azure-beli virtuális gép.


![A munkaterületről azonosított virtuális gépek](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ebbe beletartoznak az előfizetésben szereplő munkaterületre vonatkozó más előfizetések virtuális gépei, valamint Operations Manager közvetlen ügynökkel telepített virtuális gépek és nem rendelkeznek erőforrás-AZONOSÍTÓval.

Az egyes javaslatok alatt megjelenő ikon segíti a virtuális gép és a beavatkozást igénylő számítógép gyors azonosítását, valamint a javaslat típusát. A szűrőket használhatja **Erőforrás típusa** és **Súlyosság**szerint is a listában való kereséshez.

Az egyes virtuális gépek biztonsági javaslatainak részletezéséhez kattintson a virtuális gépre.
Itt láthatja a virtuális gép vagy a számítógép biztonsági részleteit. Alul a javasolt művelet és az egyes problémák súlyossága látható.
![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Felhőszolgáltatások
A Cloud Services esetében egy javaslat jön létre, ha az operációs rendszer verziója elavult.

![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Olyan forgatókönyv esetén, ahol javaslata van (amely nem az előző példában látható), az ajánlásban szereplő lépéseket kell követnie az operációs rendszer verziójának frissítéséhez. Ha egy frissítés elérhetővé válik, a rendszer riasztást jelenít meg (a probléma súlyosságától függően vörös vagy narancssárga riasztást). Ha ezt a riasztást kijelöli a Webrole1 webes (a Windows Servert az IIS-ben automatikusan üzembe helyezett webalkalmazással futtatja) vagy a WorkerRole1 (a Windows Servert az IIS-ben automatikusan telepített webalkalmazással futtatja), akkor a javaslat további részleteket tartalmaz.

Ha előírásszerűbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre.



![Operációs rendszer verziójának frissítése](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
A App Service információk megtekintéséhez engedélyeznie kell a App Service az előfizetésében. A szolgáltatás engedélyezésével kapcsolatos útmutatásért lásd: [App Service Azure Security Centerekkel való védelemmel](security-center-app-services.md).
[!NOTE]
> A figyelési App Service előzetes verzióban érhető el, és csak a Security Center Standard szintjére használható.


Az **app Services**alatt megtalálhatja az App Service-környezetek listáját, valamint az állapot összegzését az értékelés Security Center elvégzése alapján.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

A listában háromféle ikon látható:

![App Services-környezet](./media/security-center-virtual-machine-recommendations/ase.png) App Services-környezet.

![Webalkalmazás](./media/security-center-virtual-machine-recommendations/web-app.png) Webalkalmazás.

![Function alkalmazás](./media/security-center-virtual-machine-recommendations/function-app.png) Function alkalmazás.

1. Válasszon ki egy webalkalmazást. Megnyílik egy összefoglaló nézet három lapból:

   - **Javaslatok**: a sikertelen Security Center által végrehajtott értékelések alapján.
   - **Átadott értékelések**: az átadott Security Center által elvégzett értékelések listája.
   - Nem **elérhető értékelések**: azon értékelések listája, amelyeket egy hiba miatt nem sikerült futtatni, vagy ha a javaslat nem vonatkozik az adott app Service-re

   A **javaslatok** területen a kiválasztott webalkalmazásra és az egyes javaslatok súlyosságára vonatkozó javaslatok szerepelnek.

   ![Javaslatok App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Válasszon egy javaslatot a javaslat leírásának megtekintéséhez, valamint a nem megfelelő állapotú erőforrások, az egészséges erőforrások és a nem ellenőrzött erőforrások listájához.

   - Az **átadott értékelések** oszlopban az átadott értékelések listája szerepel. Az értékelések súlyossága mindig zöld.

   - Válasszon ki egy átadott értékelést a listából az értékelés leírásához, a nem megfelelő állapotú és kifogástalan erőforrások listáját, valamint a nem ellenőrzött erőforrások listáját. A nem megfelelő állapotú erőforrásokhoz tartozó lap szerepel, de ez a lista mindig üres, mert az értékelés sikeres volt.

     ![Szervizelés App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok
Security Center automatikusan felfedi, hogy vannak-e méretezési készletek, és javasolja, hogy telepítse a Microsoft monitoring agentet ezekre a méretezési csoportokra. 

A Microsoft monitoring Agent telepítése: 

1. Válassza a **figyelési ügynök telepítése virtuálisgép-méretezési csoportra**lehetőséget. A nem figyelt méretezési csoportok listáját kapja meg.
2. Válasszon egy nem kifogástalan állapotú méretezési készletet. Kövesse az utasításokat, és telepítse a figyelési ügynököt egy meglévő feltöltött munkaterületen, vagy hozzon létre egy újat. Ha nincs beállítva, ügyeljen arra, hogy a munkaterület [árképzési szintjét](security-center-pricing.md) állítsa be.

   ![Az MMS telepítése](./media/security-center-virtual-machine-recommendations/install-mms.png)

Új méretezési csoportok beállítása a Microsoft monitoring Agent Automatikus telepítéséhez:
1. Nyissa meg Azure Policy és kattintson a **definíciók**elemre.
2. Keresse meg a házirendet a **Windows rendszerű virtuálisgép-méretezési csoportok log Analytics ügynökének üzembe helyezéséhez** , és kattintson rá.
3. Kattintson a **Hozzárendelés** gombra.
4. Állítsa be a **hatókör** és **log Analytics munkaterületet** , és kattintson a **hozzárendelés**elemre.

Ha az összes meglévő méretezési csoportot be szeretné állítani a Microsoft monitoring Agent telepítésére, akkor a Azure Policyban lépjen a **szervizelés** elemre, és alkalmazza a meglévő házirendet a meglévő méretezési csoportokra.


## Számítási és alkalmazási javaslatok<a name="compute-and-app-recs"></a>
|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|App Service|20|A webalkalmazás csak HTTPS protokollon keresztül érhető el|A webalkalmazások hozzáférésének korlátozása csak HTTPS protokollon keresztül.|
|App Service|20|függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|Csak HTTPS-kapcsolaton keresztül korlátozza a függvények elérését.|
|App Service|5|A App Services diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|App Service|10|A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Ha már nincs szüksége rá, kapcsolja ki a webes alkalmazások hibakeresését. A távoli hibakereséshez a bejövő portokat függvényalkalmazás kell megnyitni.|
|App Service|10|A távoli hibakeresést ki kell kapcsolni a Function alkalmazáshoz|Ha már nincs szüksége a használatra, kapcsolja ki a hibakeresést függvényalkalmazás. A távoli hibakereséshez a bejövő portokat függvényalkalmazás kell megnyitni.|
|App Service|10|Ne engedélyezze az összes (*) erőforrást az alkalmazás eléréséhez| A WEBSITE_LOAD_CERTIFICATES paraméter nem engedélyezhető "" értékre. A paraméter "" értékre állítása azt jelenti, hogy az összes tanúsítvány betöltődik a webalkalmazások személyes tanúsítványtárolóba. Ez a legalacsonyabb jogosultsági szinttel való visszaéléshez vezethet, mivel nem valószínű, hogy a helynek a futtatókörnyezet összes tanúsítványához hozzá kell férnie.|
|App Service|20|A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazások elérését|Csak a szükséges tartományokat engedélyezze a webalkalmazással való kommunikációhoz. A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a webalkalmazás elérését.|
|App Service|20|A CORS nem teszi lehetővé minden erőforrás számára a függvényalkalmazás elérését| Csak a szükséges tartományokat engedélyezze a Function alkalmazással való kommunikációhoz. A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a Function alkalmazás elérését.|
|Számítási erőforrások (batch)|1|A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni|Metrika riasztási szabályainak konfigurálása a Batch-fiókon, valamint a metrikák készletének törlése a teljes események és a készlet törlése indítási események|
|Számítási erőforrások (Service Fabric)|10|Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez|Az ügyfél-hitelesítés csak a Service Fabric Azure Active Directoryon keresztül hajtható végre.|
|Számítási erőforrások (Automation-fiók)|5|Az Automation-fiók változóit titkosítani kell|Az Automation-fiókok változó eszközei titkosításának engedélyezése bizalmas adatok tárolására.|
|Számítási erőforrások (keresés)|5|Diagnosztikai naplók engedélyezésének naplózása a keresési szolgáltatásokhoz|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (Service Bus)|5|A Service Bus diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (stream Analytics)|5|A Azure Stream Analytics diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (batch)|5|Diagnosztikai naplók engedélyezése a Batch-fiókokban|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (Event hub)|5|Az Event hub diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (logikai alkalmazások)|5|Diagnosztikai naplók engedélyezése Logic Apps|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (Service Fabric)|15|Állítsa a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre Service Fabric|A Service Fabric három védelmi szintet biztosít (nincs, aláírás és EncryptAndSign) a csomópontok közötti kommunikációhoz az elsődleges fürt tanúsítványának használatával. Állítsa be a védelmi szintet annak biztosítására, hogy minden csomópont-csomópont típusú üzenet titkosítva legyen, és digitálisan alá legyen írva. |
|Számítási erőforrások (Service Bus)|1|Az összes engedélyezési szabály eltávolítása Service Bus névtérből származó RootManageSharedAccessKey kivételével |Service Bus-ügyfelek nem használhatnak névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtérben lévő összes várólistához és témakörhöz. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Számítási erőforrások (Event hub)|1|A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből|Az Event hub-ügyfelek nem használhatnak olyan névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtérben lévő összes várólistához és témakörhöz. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Számítási erőforrások (Event hub)|5|Az Event hub-entitás engedélyezési szabályait definiálni kell|Az Event hub-entitás engedélyezési szabályainak naplózása a legkevésbé privilegizált hozzáférés biztosításához.|
|Gép|50|Figyelési ügynök telepítése a gépeken|Telepítse a figyelési ügynököt az adatgyűjtés engedélyezéséhez, a frissítések vizsgálatához, az alapkonfiguráció vizsgálatához és az Endpoint Protection szolgáltatáshoz az egyes gépeken.|
|Gép|50|Automatikus kiépítés és adatgyűjtés engedélyezése az előfizetésekhez |Az előfizetésekben lévő gépek automatikus kiépítési és adatgyűjtési szolgáltatásának engedélyezése az adatgyűjtésre, a frissítések vizsgálatára, az alapkonfiguráció vizsgálatára és az Endpoint Protection szolgáltatásra az előfizetésekhez hozzáadott összes gépen.|
|Gép|40|A figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken|A teljes Security Center védelem érdekében a hibaelhárítási útmutató utasításait követve oldja meg a figyelési ügynökkel kapcsolatos problémákat a számítógépeken.| 
|Gép|40|Az Endpoint Protection állapotával kapcsolatos problémák megoldása a gépeken|A teljes Security Center védelem érdekében a hibaelhárítási útmutató utasításait követve oldja meg a figyelési ügynökkel kapcsolatos problémákat a számítógépeken.|
|Gép|40|A számítógépek hiányzó vizsgálati adataival kapcsolatos hibák megoldása|A virtuális gépeken és számítógépeken a hiányzó vizsgálati adatvizsgálatok hibáinak megoldása. A számítógépeken a vizsgálati eredmények hiányzó biztonsági vizsgálatokat eredményeznek, például a frissítések vizsgálatát, az alapkonfiguráció vizsgálatát és az Endpoint Protection-megoldások hiányzó vizsgálatát.|
|Gép|40|A számítógépekre telepíteni kell a rendszerfrissítéseket|A Windows és a Linux rendszerű virtuális gépek és számítógépek biztonságossá tételéhez telepítse a hiányzó rendszerbiztonsági és kritikus frissítéseket
|Gép|15|Webalkalmazási tűzfal hozzáadása| Helyezzen üzembe egy webalkalmazási tűzfal (WAF) megoldást a webalkalmazások védelmére. |
|Gép|40|A Cloud Service-szerepkörök operációsrendszer-verziójának frissítése|Frissítse a Cloud Service szerepköreihez tartozó operációs rendszer (OS) verzióját az operációsrendszer-család számára elérhető legújabb verzióra.|
|Gép|35|A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell|Javítsa ki a számítógépeken a biztonsági beállítások sebezhetőségeit a támadások elleni védelem érdekében.|
|Gép|35|Biztonsági rések szervizelése a tárolók biztonsági beállításaiban|Javítsa ki a biztonsági beállításokat a Docker által telepített számítógépeken a támadások elleni védelem érdekében.|
|Gép|25|Adaptív alkalmazások vezérlőinek engedélyezése|Az alkalmazások vezérlésének engedélyezésével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Ez segít megerősíteni a virtuális gépeket a kártevők ellen. Security Center a gépi tanulás segítségével elemzi az egyes virtuális gépeken futó alkalmazásokat, és segít az engedélyezési szabályok alkalmazásában az intelligenciával. Ez a funkció leegyszerűsíti az alkalmazások engedélyezési szabályainak konfigurálását és karbantartását.|
|Gép|20|Endpoint Protection-megoldás telepítése a számítógépekre|Telepítsen egy Endpoint Protection-megoldást a virtuális gépekre a fenyegetések és a biztonsági rések elleni védelem érdekében.|
|Gép|20|A rendszerfrissítések alkalmazásához indítsa újra a gépeket|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához, és a számítógép védelmét a biztonsági rések alapján.|
|Gép|15|A lemezes titkosítást a virtuális gépeken kell alkalmazni|A Windows-és Linux-alapú virtuális gépekhez Azure Disk Encryption egyaránt titkosíthatja a virtuális gépek lemezeit. Azure Disk Encryption (ADE) kihasználja a Windows iparági szabvány BitLocker szolgáltatását és a Linux DM-Crypt szolgáltatását, hogy az operációs rendszer és az adatlemez-titkosítás biztosítson az adatai védelméhez és védelméhez, valamint a szervezeti biztonság és megfelelőség biztosításához az ügyfél Azure Key vaultban vállalt kötelezettségek. Ha a megfelelőségi és biztonsági követelmény megköveteli, hogy a titkosítási kulcsok használatával a végpontok között titkosítsa az adatait, beleértve az időszakos (helyileg csatolt ideiglenes) lemez titkosítását, használja az Azure Disk encryptiont. Másik lehetőségként alapértelmezés szerint a Managed Disks titkosítva maradnak az Azure Storage Service Encryption használatával, ahol a titkosítási kulcsok a Microsoft által felügyelt kulcsok az Azure-ban. Ha ez megfelel a megfelelőségi és biztonsági követelményeknek, kihasználhatja az alapértelmezett felügyelt lemezes titkosítást, hogy megfeleljen az igényeinek.|
|Gép|30|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|
|Gép|15|Webalkalmazási tűzfal hozzáadása| Helyezzen üzembe egy webalkalmazási tűzfal (WAF) megoldást a webalkalmazások védelmére. |
|Gép|30|A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Azok a virtuális gépek, amelyeken a sebezhetőségi felmérés harmadik féltől származó megoldást helyeznek üzembe, folyamatosan értékelik az alkalmazások és az operációs rendszer biztonsági réseit. Ha ilyen biztonsági réseket talál, ezek a javaslat részeként további információkhoz is elérhetők.|
|Gép|30|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|
|Gép|1|A virtuális gépeket át kell telepíteni az új AzureRM-erőforrásokra|A virtuális gépek Azure Resource Manager használatával olyan biztonsági fejlesztéseket biztosíthat, mint például a következők: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, erőforrás-kezelő alapú üzembe helyezés és irányítás, felügyelt identitásokhoz való hozzáférés, a Key Vault for Secrets elérése Az Azure AD-alapú hitelesítés és a címkék és erőforráscsoportok támogatása a biztonsági felügyelet megkönnyítésére. |
|Gép|30|A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Azok a virtuális gépek, amelyeken a sebezhetőségi felmérés harmadik féltől származó megoldást helyeznek üzembe, folyamatosan értékelik az alkalmazások és az operációs rendszer biztonsági réseit. Ha ilyen biztonsági réseket talál, ezek a javaslat részeként további információkhoz is elérhetők.|
|Virtuálisgép-méretezési csoport |4|A Virtual Machine Scale Sets diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig is megtarthatja őket. Ez lehetővé teszi, hogy vizsgálati célokra újra létrehozza a tevékenységek nyomvonalait. Ez akkor hasznos, ha biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Virtuálisgép-méretezési csoport|35|A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell|Javítsa a biztonsági beállításokat a virtuálisgép-méretezési csoportokban a támadások elleni védelem érdekében. |
|Virtuálisgép-méretezési csoport|5|Az Endpoint Protection állapotával kapcsolatos hibák elhárítása virtuálisgép-méretezési csoportokban|Javítsa ki az Endpoint Protection-állapottal kapcsolatos hibákat a virtuálisgép-méretezési csoportokban a fenyegetések és a biztonsági rések elleni védelem érdekében. |
|Virtuálisgép-méretezési csoport|10|Az Endpoint Protection szolgáltatást virtuális gépekre kell telepíteni|Telepítsen egy Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra a fenyegetések és a biztonsági rések elleni védelem érdekében. |
|Virtuálisgép-méretezési csoport|40|A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell|Telepítse a hiányzó rendszerbiztonsági és kritikus frissítéseket a Windows és a Linux rendszerű virtuálisgép-méretezési csoportok biztonságossá tételéhez. |
 





## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következőket:


* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Identitás és hozzáférés figyelése az Azure Security Centerben](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás védelme Azure Security Center](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

