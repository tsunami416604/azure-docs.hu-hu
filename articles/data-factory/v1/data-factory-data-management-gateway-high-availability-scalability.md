---
title: "Magas rendelkezésre állás az adatkezelési átjáró az Azure Data Factory |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan lehet horizontálisan adatkezelési átjárót további csomópontokat és skálázási hozzáadásával legfeljebb egy csomópont futtatható egyidejűleg futó feladatainak számát növeli."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 195a1a4810de478b77538716fa8d1362428864d8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Az adatkezelési átjáró - magas rendelkezésre állás és méretezhetőség (előzetes verzió)
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [önállóan üzemel a 2-es integrációs futásidejű](../create-self-hosted-integration-runtime.md). 


Ez a cikk segít a magas rendelkezésre állás és méretezhetőség megoldás konfigurálása az adatkezelési átjárót / integráció.    

> [!NOTE]
> Ez a cikk feltételezi, hogy Ön már ismeri a integrációs futásidejű (korábbi az adatkezelési átjáró) alapjait. Ha nem, lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md).

>**Az előzetes funkcióval hivatalosan az adatkezelési átjáró verziója 2.12.xxxx.x vagy újabb támogatott**. Ellenőrizze, hogy verzió 2.12.xxxx.x használ vagy újabb. Töltse le az adatkezelési átjáró legújabb verzióját [Itt](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Áttekintés
Felügyeleti adatátjáró a portálról egyetlen logikai átjáróval több helyszíni gépen telepített lehet társítani. Ezek a gépek nevezzük **csomópontok**. Akár lehet **négy csomópont** társított logikai átjáró. Több csomópont (a telepített átjárót a helyszíni gépeket) rendelkező logikai átjáró előnyei a következők:  

- A helyszíni és a felhő közötti adatátvitel teljesítményének növelése a adattárolókhoz.  
- Ha valamilyen okból leáll egyik csomópontján, más csomópontok továbbra is elérhetők az adatok mozgatására. 
- Ha a csomópontok egyikét kell karbantartás céljából elérhetetlenné válhat, más csomópontok továbbra is elérhetők az adatok mozgatására.

Beállíthatja úgy is száma **egyidejű adatátviteli feladatok** egy csomópont növelheti az adatok a helyszínen és a felhő közötti áthelyezése képességének futó adattárolókhoz. 

Az Azure portál használatával, ezeket a csomópontokat, amely segít eldönteni, hogy vegye fel vagy a csomópont eltávolítása a logikai átjáró állapotának figyelése. 

## <a name="architecture"></a>Architektúra 
A következő ábra az architektúra áttekintése, méretezhetőség és a rendelkezésre állás szolgáltatással az adatkezelési átjáró nyújt: 

![Az adatkezelési átjáró - magas rendelkezésre állás és méretezhetőség](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logikai átjáró** az átjáró ad hozzá egy adat-előállítót az Azure portálon. Korábban a helyszíni csak egy Windows-számítógép sikerült társítani az adatkezelési átjáró egy logikai átjáró telepítve. Ez a helyszíni átjáró számítógépén nevezzük. Mostantól társíthat legfeljebb **négy fizikai csomópontok** logikai átjáróként. Egy logikai átjárójának több csomópont neve egy **többcsomópontos átjáró**.  

Ezek a csomópontok vannak **aktív**. Az adatátviteli feladatok áthelyezni az adatokat a helyszíni és a felhő közötti összes tud feldolgozni adattárolókhoz. A csomópontok egyikét kézbesítő és munkavégző is jár el. A csoportok többi csomópontjának munkavégző csomópontokhoz. A **kézbesítő** csomópont lekéri az adatátviteli feladatok/feladatok felhőalapú szolgáltatásából, és kiszállítja őket (beleértve magát) munkavégző csomópontokhoz. A **munkavégző** csomópont végrehajtja az adatátviteli feladatok áthelyezni az adatokat a helyszíni és a felhő között adattárolókhoz. Minden csomópont munkavállalók. Csak egy csomópont lehet a küldő és a munkavégző.    

Általában egy csomópont lehet, hogy kezdjen és **horizontális felskálázás** további csomópontokat, a meglévő csomópontokon vannak túlterhelik az adatok mozgása terheléssel. Emellett **vertikális felskálázás** adatok adatátviteli képességet egy átjárócsomópontnak, amely a csomóponton futhatnak egyidejűleg futó feladatainak számát. Ez a funkció érhető el egy egy csomópontos átjáró (még akkor is, ha a méretezhetőség és a rendelkezésre állási funkció nincs engedélyezve). 

Egy átjárót a több csomópont tartja az adatok adattárolóhoz használandó hitelesítő adatok szinkronban összes csomópont. Ha egy csomópont-csomópont hálózati probléma, a hitelesítő adatok szinkronban lehet. Ha úgy állítja be, amely egy átjárót használja a helyszíni adatokat tároló hitelesítő adatait, a kézbesítő/munkavégző csomóponton menti hitelesítő adatokat. A kézbesítő csomópont szinkronizál más munkavégző csomópontokhoz. Ezt a folyamatot nevezik **hitelesítő adatok szinkronizálása**. A csomópontok közötti kommunikációs csatorna lehet **titkosított** egy nyilvános SSL/TLS-tanúsítvány. 

## <a name="set-up-a-multi-node-gateway"></a>Több csomópontos átjárót a beállítása
Jelen szakaszban feltételezzük, hogy elvégezték-módon, a következő két cikkek vagy ismerős, ezek a cikkek jellemzői: 

- [Az adatkezelési átjáró](data-factory-data-management-gateway.md) -átjáró részletes áttekintése.
- [Adatok áthelyezése között a helyszíni és felhőalapú adattároló](data-factory-move-data-between-onprem-and-cloud.md) -egy bemutató részletes utasításokat egy átjáró egyetlen csomóponttal rendelkező tartalmazza.  

> [!NOTE]
> Mielőtt adatkezelési átjárót egy a helyi Windows-számítógépen telepíti, olvassa el a szakaszában ismertetett előfeltételeknek [a fő cikk](data-factory-data-management-gateway.md#prerequisites).

1. Az a [forgatókönyv](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), logikai átjáró létrehozásakor engedélyezése a **magas rendelkezésre állás és méretezhetőség** szolgáltatás. 

    ![Az adatkezelési átjáró - enable magas rendelkezésre állás és méretezhetőség](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Az a **konfigurálása** lapján választhatja **Express telepítő** vagy **manuális telepítés** telepít egy átjárót az első csomóponton (egy a helyszíni Windows-számítógép) hivatkozásra.

    ![Az adatkezelési átjáró - kifejezett vagy manuális telepítés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > A gyorstelepítés beállítás használatakor a csomópontok kommunikáció titkosítás nélkül történik. A csomópont neve legyen, mint a gép nevét. Használja a manuális telepítési módra, ha a csomópontok kommunikációs titkosítani kell, vagy adja meg az Ön által választott csomópont nevét szeretné. Csomópont nevének később nem szerkeszthető.
3. Ha úgy dönt, **Gyorstelepítés**
    1. A következő üzenet jelenik meg az átjáró sikeres telepítése után:

        ![Az adatkezelési átjáró - gyors telepítés sikeres](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Indítsa el az felügyeleti Konfigurációkezelőjét az átjárót a következő [ezeket az utasításokat](data-factory-data-management-gateway.md#configuration-manager). Láthatja, hogy az átjáró neve, a csomópont neve, állapota stb.

        ![Az adatkezelési átjáró - sikeresen telepítve](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Ha úgy dönt, **manuális telepítési módra**:
    1. A telepítési csomag letölthető a Microsoft Download Center, futtassa az átjáró a számítógépre telepítéséhez.
    2. Használja a **hitelesítési kulcs** a a **konfigurálása** lapon regisztrálja az átjárót.
    
        ![Az adatkezelési átjáró - sikeresen telepítve](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Az a **új átjárócsomópont** lapon megadhat egyéni **neve** ahhoz az átjárócsomóponthoz. Alapértelmezés szerint a csomópont neve legyen, mint a gép nevét.    

        ![Az adatkezelési átjáró - nevének megadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. A következő oldalon, dönthet úgy, hogy **engedélyezheti a titkosítást csomópontok kommunikációhoz**. Kattintson a **kihagyása** letiltásához (alapértelmezett) titkosítás.

        ![Az adatkezelési átjáró - titkosítás engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > A titkosítási mód módosítása csak támogatott, ha az átjáró egyetlen csomópont már a logikai átjáró. Ha az átjáró több csomópont a titkosítási mód módosításához tegye a következőket: egy csomópont kívül minden csomópont törlése, módosítása a titkosítási mód és majd újból vegye fel a csomópontok.
        > 
        > Lásd: [a TLS/SSL-tanúsítványokkal szemben támasztott követelmények](#tlsssl-certificate-requirements) szakasz listáját egy TLS/SSL-tanúsítvány használatára vonatkozó követelményeket. 
    5. Az átjáró sikeres telepítése után kattintson a Configuration Manager indítása:
    
        ![Manuális telepítés - indítsa el a configuration manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. a csomópont (a helyszíni Windows számítógép), mely csatlakozási állapotát jeleníti meg, megtekintheti az adatkezelési átjáró konfigurációkezelőjének **átjárónevet**, és **csomópontnév**.  

        ![Az adatkezelési átjáró - sikeresen telepítve](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Ha az átjáró egy Azure virtuális gépen kiépíteni, [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Ezt a parancsfájlt hoz létre egy logikai átjárót az adatkezelési átjáró szoftverrel rendelkező virtuális gépek beállítja és regisztrálja őket a logikai átjáró. 
6. Azure-portálon, indítsa el a **átjáró** lap: 
    1. A data factory kezdőlapján a portálon, kattintson a **összekapcsolt szolgáltatások**.
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Válassza ki a **átjáró** megtekintéséhez a **átjáró** lap:
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Megjelenik a **átjáró** lap:   

        ![Egyetlen csomópont megtekintése átjáró](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kattintson a **csomópont hozzáadása** egy csomópont hozzáadása a logikai átjáró az eszköztáron. Ha azt tervezi, hogy a gyorstelepítés, a helyi gépről átjáróhoz csomópontként hozzáadni ehhez a lépéshez. 

    ![Az adatkezelési átjáró - csomópont menü hozzáadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Lépések hasonlóak az első csomópontot beállítására. A Configuration Manager felhasználói felület lehetővé teszi a csomópont nevét állítja be, ha a manuális telepítés lehetőséget választja: 

    ![Configuration Manager - telepítés második átjáró](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Miután az átjáró sikeresen telepítve van a csomópont, a Configuration Manager eszközt a következő képernyő jelenik meg:  

    ![Configuration Manager - telepítés második átjáró sikeres](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Ha megnyitja a **átjáró** lap a portál látható két átjárócsomópontok most: 

    ![Átjáró, a portál két csomópontja](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Egy átjáró csomópont törléséhez kattintson **törlése csomópont** az eszköztáron válassza ki a csomópontot, törlése, és kattintson a kívánt **törlése** az eszköztáron. Ez a művelet törli a kijelölt csomópont a csoportból. Vegye figyelembe, hogy ez a művelet nem távolítja el az átjáró felügyeleti szoftverek a csomópont (a helyi Windows-számítógépen). Használjon **programok telepítése és törlése** Vezérlőpult a helyszíni az átjáró eltávolítása. Átjáró csomópontjából eltávolításakor a rendszer automatikusan törli a portálon.   

## <a name="upgrade-an-existing-gateway"></a>Egy meglévő átjáró frissítése
Frissíthet egy meglévő átjárót, a magas rendelkezésre állás és méretezhetőség szolgáltatás használatához. Ez a funkció csak az adatkezelési átjáró verzió csomópontok működik > = 2.12.xxxx. Láthatja, hogy a gépre telepítve az adatkezelési átjáró verziója a **súgó** lapon, a adatkezelési átjáró konfigurációkezelőjének. 

1. Frissítse az átjárót a helyszíni gépre a legújabb verzióra következő úgy, hogy letölti és fut egy MSI telepítő csomag és program a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Lásd: [telepítési](data-factory-data-management-gateway.md#installation) című szakaszban talál információt.  
2. Keresse meg az Azure-portálon. Indítsa el a **adat-előállító lap** az a data factory. Indítsa el a társított szolgáltatások csempén kattintson a **összekapcsolt szolgáltatások lap**. Válassza ki az átjáró elindítása a **átjáró lap**. Kattintson a gombra, majd engedélyezze **előzetes verziójú funkciók** a következő ábrán látható módon: 

    ![Az adatkezelési átjáró - enable előzetes verziójú funkciók](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Az előzetes verziójú funkciók a portálon engedélyezése után zárja be az összes lapot. Nyissa meg újra a **átjáró lap** az új előzetes felhasználói felület (UI) megjelenítéséhez.
 
    ![Az adatkezelési átjáró - enable előzetes funkció sikeres](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Az adatkezelési átjáró – tekintse meg a felhasználói felület](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > A frissítés során az első csomópont neve nem a gép nevét. 
3. Ezután adja hozzá egy csomópont. Az a **átjáró** kattintson **csomópont hozzáadása**.  

    ![Az adatkezelési átjáró - csomópont menü hozzáadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    A fenti szakaszban leírt, a csomópont beállításához kövesse az utasításokat. 

### <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez

- Energiaséma konfigurálja, a gazdagépen az átjáró, hogy a gép hibernálásra nem. A gazdaszámítógépen szeretnénk, ha az átjáró nem válaszol a kérelmek.
- Készítsen biztonsági másolatot az átjáróhoz társított tanúsítvány.
- Győződjön meg róla minden csomópont (ajánlott) ideális teljesítmény hasonló konfigurációt. 
- Adjon hozzá legalább két csomópont a magas rendelkezésre állásának biztosításához.  

### <a name="tlsssl-certificate-requirements"></a>A TLS/SSL-tanúsítványokkal szemben támasztott követelmények
Az alábbiakban a futásidejű csomópontok integrációs közötti kommunikáció tehető biztonságossá használt TLS/SSL-tanúsítványra vonatkozó követelményekről:

- A tanúsítványnak kell lennie egy nyilvánosan megbízható X509 v3 tanúsítvány. Azt javasoljuk, hogy a nyilvános (külső) hitelesítésszolgáltató (CA) által kiállított tanúsítványokat használ.
- Minden integrációs futásidejű csomópont megbízhatónak kell lennie, ez a tanúsítvány, valamint az ügyfélszámítógépen, amely a hitelesítőadat-kezelő alkalmazást futtat. 
> [!NOTE]
> Hitelesítő adatok manager alkalmazás használata során biztonságosan beállítani a hitelesítő adatok másolása varázsló a / Azure-portálon. És ez lehet a helyi hálózatán belül bármely számítógépről égetett / személyes adattár.
- Helyettesítő tanúsítványokat támogatottak. Ha a tartománynév **node1.domain.contoso.com**, használhat ***. domain.contoso.com** , a tanúsítvány tulajdonosának nevével.
- SAN-tanúsítványok használata nem ajánlott, mert a tulajdonos alternatív neve csak az utolsó elem használja, az összes többit figyelmen kívül aktuális korlátai miatt. Például a SAN tanúsítványa, amelynek SAN vannak **node1.domain.contoso.com** és **node2.domain.contoso.com**, amelynek FQDN-je gépen csak használható ezzel a tanúsítvánnyal **node2.domain.contoso.com**.
- Támogatja az SSL-tanúsítványok Windows Server 2012 R2 által támogatott bármely kulcs mérete.
- Tanúsítvány használatával CNG kulcsok használata nem támogatott. Doesrted DoesDoes nem támogatja a CNG-kulccsal használó tanúsítványokat.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Gyakran ismételt kérdések: Amikor volna nem engedélyezhető a titkosítási?
Titkosítás engedélyezése adhat hozzá az infrastruktúra (tulajdonos nyilvános tanúsítvány) költségeket, ezért bizonyos engedélyezése titkosításra kihagyhatja az alábbi esetekben:
- Ha az integrációs futásidejű megbízható hálózathoz, vagy a hálózaton, például IP másodpercenként átlátszó titkosítással fut. Mivel ez a csatorna kommunikáció csak korlátozott megbízható hálózaton belül, előfordulhat, hogy nem kell további titkosítási.
- Ha az integrációs futásidejű nem fut az éles környezetben. Ez segít csökkenteni a TLS/SSL-tanúsítvány költség.


## <a name="monitor-a-multi-node-gateway"></a>Több csomópontos átjáró figyelője
### <a name="multi-node-gateway-monitoring"></a>Több csomópontos átjáró figyelése
Az Azure portálon minden csomóponton együtt átjáró csomópontok állapotok tekintheti meg közel valós idejű pillanatképe erőforrás-használat (Processzor, memória, network(in/out), stb.). 

![Az adatkezelési átjáró - több csomópont figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Engedélyezheti a **speciális beállítások** a a **átjáró** lapot, melyen megtekintheti például speciális metrikák **hálózati**(in/out), **szerepkör & hitelesítő adatok állapot**, ez az átjáró hibáinak feltárására lehet hasznos és **egyidejűleg futó feladatainak** (futtató / Limit) amely is lehet módosított / megváltozott ennek megfelelően során teljesítményhangolás. A következő táblázat ismerteti az oszlopok a **Átjárócsomópontok** listája:  

Figyelési tulajdonság | Leírás
:------------------ | :---------- 
Name (Név) | A logikai átjáró és az átjáróhoz társított csomópont neve.  
status | A logikai átjáró és az átjáró csomópontok állapota. Példa: Online/Offline/korlátozott/stb. A fenti állapotok megjelenése kapcsolatos információkért lásd: [az átjáró állapotának](#gateway-status) szakasz. 
Verzió | A logikai átjáró és az egyes átjárócsomópont verzióját jeleníti meg. A logikai átjáró verziója határozza meg a csoportban lévő csomópontok többsége verzióján alapul. Ha nincs a logikai átjáró beállítás, csak a csomópontok a azonos verziószámú a logikai átjáró függvényében eltérő verziójú csomópontok megfelelően. Mások korlátozott módban van, és manuálisan kell frissíteni, (csak abban az esetben az automatikus frissítés sikertelen lesz). 
Rendelkezésre álló memória | Rendelkezésre álló memória egy átjáró-csomóponton. Ez az érték közel valós idejű pillanatképet. 
Processzorkihasználtság | Egy átjáró csomópont CPU-felhasználását. Ez az érték közel valós idejű pillanatképet. 
Hálózatkezelés (In/Out) | Hálózathasználat egy átjáró csomópont. Ez az érték közel valós idejű pillanatképet. 
Egyidejűleg futó feladatainak (futtató / Limit) | Feladatok vagy minden egyes csomóponton futó feladatok száma. Ez az érték közel valós idejű pillanatképet. Korlát azt jelzi, hogy az egyes csomópontok maximális egyidejűleg futó feladatainak. Ez az érték van megadva a mérete alapján. Egyidejű feladatok végrehajtásának speciális forgatókönyvekhez, növelheti a korlát növelhető ahol Processzor / memória / hálózati alatt szükség, de a tevékenységek vannak időtúllépés miatt. Ez a funkció érhető el egy egy csomópontos átjáró (még akkor is, ha a méretezhetőség és a rendelkezésre állási funkció nincs engedélyezve). További információkért lásd: [szempontok méretezése](#scale-considerations) szakasz. 
Szerepkör | A szerepkörök – kézbesítő és munkavégző két típusa van. Az összes csomópontja a dolgozók, ami azt jelenti, hogy az összes felhasználásuk feladatok végrehajtásához. A kézbesítő csak egy csomópont, feladatok és feladatok a felhőalapú szolgáltatások lekéréses és mennyi őket (beleértve magát) különböző munkavégző csomópontokhoz használt van. 

![Az adatkezelési átjáró - speciális több csomópont figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Az átjáró állapotának

A következő táblázat a lehetséges állapotok egy **átjárócsomópont**: 

status  | Megjegyzések/forgatókönyvek
:------- | :------------------
Online | Csomópont csatlakozik a Data Factory szolgáltatásnak.
Offline | Csomópontja offline állapotban.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | Kapcsolat nem látható probléma miatt. HTTP-port 8050 problémát, a service bus kapcsolati probléma vagy a hitelesítő adatok szinkronizálási problémája miatt lehet. 
Inaktív | Csomópont van konfigurálva a konfigurációból egyéb többsége csomópontok különböző.<br/><br/> A csomópont inaktív lehet, ha más csomópontok nem tud kapcsolódni. 


A következő táblázat a lehetséges állapotok egy **logikai átjáró**. Az átjáró állapotának átjáró csomópontnak állapotok függ. 

status | Megjegyzések
:----- | :-------
Needs Registration | Nincs csomópont még regisztrálva van a logikai átjáró
Online | Átjáró csomópontja online állapotban.
Offline | Nincs csomópontja online állapotát.
Korlátozott | Ez az átjáró nem minden csomópontja kifogástalan állapotban vannak. Ez az állapot nem figyelmezteti rá, hogy néhány csomópont esetleg nem működik! <br/><br/>Hitelesítő adatok szinkronizálási problémája kézbesítő/munkavégző csomóponton okozhatja. 

### <a name="pipeline-activities-monitoring"></a>Feldolgozási sor / tevékenységek figyelése
Az Azure-portálon biztosít egy folyamat figyelésének lehetőségével részletes csomópont szintű adatokkal. Például azt jeleníti meg, mely tevékenységek futtatott melyik csomópontján. Ez az információ is hasznos információkat teljesítményproblémák egy adott csomóponton, mondja ki a hálózati sávszélesség-szabályozás miatt. 

![Az adatkezelési átjáró - folyamatok figyelését több csomópont](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Az adatkezelési átjáró - feldolgozási folyamat részletei](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Skála kapcsolatos szempontok

### <a name="scale-out"></a>Horizontális felskálázás
Ha a **elérhető memória pedig kevés** és a **CPU-használata túl magas**, új csomópont hozzáadása segíti a terhelés kibővítési gépek között. Ha tevékenységek hiba miatt hiúsulnak meg időtúllépési vagy az átjáró csomópont nem kapcsolódik, segít, ha egy csomópont hozzáadása az átjáró.
 
### <a name="scale-up"></a>Vertikális felskálázás
Amikor a rendelkezésre álló memória és CPU nem használhatók is, de a tétlen kapacitás 0, akkor kell vertikális felskálázás csomópont futtatható egyidejűleg futó feladatainak számát. Érdemes azt is, méretezést kívánó, amikor a tevékenység időtúllépés miatt, mivel az átjáró túl van terhelve. A következő ábrán látható módon is növelheti a maximális csomópont. Javasoljuk, hogy így dupla kezdődnie.  

![Az adatkezelési átjáró - skálázási kapcsolatos szempontok](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Ismert problémák/legfrissebb módosítása

- Jelenleg egyetlen logikai átjáró legfeljebb négy fizikai átjárócsomópontok lehet. Ha négynél több teljesítményének javítására szolgál, küldjön egy e-mailek [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- A hitelesítési kulcs az aktuális logikai átjáró váltson egy másik logikai átjáróról újra egy átjárócsomópont nem regisztrált. Ismételten a regisztrációt, távolítsa el az átjárót a csomópontról, telepítse újra az átjárót, és regisztrálhatja azt az más logikai átjáró a hitelesítési kulcs. 
- Ha HTTP-proxy szükség az átjáró összes csomóponthoz, állítsa be a proxy diahost.exe.config és diawp.exe.config, és ellenőrizze, hogy minden csomópontja ugyanazon diahost.exe.config és diawip.exe.config rendelkezik, a Kiszolgálókezelő használatával. Lásd: [konfigurálja a proxybeállításokat](data-factory-data-management-gateway.md#configure-proxy-server-settings) című szakaszban talál információt. 
- Csomópontok kommunikáció az átjáró Konfigurációkezelőjében titkosítási mód módosításához törölje a portál egy kivételével az összes csomópont. Vissza a titkosítási mód megváltoztatása után adja hozzá a csomópontok.
- Hivatalos SSL-tanúsítványt használja, ha úgy dönt, hogy titkosíthatja a csomópontok kommunikációs csatornát. A tanúsítvány nem lesz megbízható a más számítógépeken tanúsító listáján önaláírt tanúsítvány kapcsolódási problémák idézhet elő. 
- Egy logikai átjáró átjáró csomópont nem lehet regisztrálni, ha a csomópont verziószáma kisebb, mint a logikai átjáró verziója. Törölje a logikai átjáró összes csomópontja portal, hogy egy alacsonyabb verziójú node(downgrade) regisztrálhatja azt. Ha töröl egy logikai átjáró összes csomópontja, manuálisan telepíti, és regisztrálja az adott logikai átjáró új csomópontok. A gyorstelepítés nem támogatott ebben az esetben.
- Gyors telepítés használatával nem csomópontok telepítése egy meglévő logikai átjárón, amely felhő hitelesítő adatai továbbra is használja. Ellenőrizze a hitelesítő adatok tárolására az átjáró Configuration Managerből a beállítások lapon.
- Gyors telepítés használatával nem csomópontok telepítése egy meglévő logikai átjárón, amely csomópontok titkosítás engedélyezve van. Mivel a titkosítási mód beállítása magában foglalja a manuális hozzáadásával a tanúsítványok, Expressz telepítés lehetőség nincs további. 
- A fájl másolatát a helyszíni környezetből, ne használjon \\localhost vagy C:\files többé óta localhost vagy a helyi meghajtó előfordulhat, hogy nem kell minden csomópont keresztül érhető el. Ehelyett használjon \\ServerName\files adhatja meg a fájlok helyét.


## <a name="rolling-back-from-the-preview"></a>A Preview visszaállítása 
A Preview visszavonása törlése azonban fürtcsomópontok csomópontjaihoz. Nem számít, törlése, de ellenőrizze, hogy legalább egy csomópont az a logikai átjáró csomópontok. Egy csomópont törölhetők a számítógép-átjáró eltávolítása vagy az Azure portál használatával. Az Azure portálon a a **adat-előállító** társított szolgáltatások elindításához kattintson a **összekapcsolt szolgáltatások** lap. Válassza ki az átjáró elindítása a **átjáró** lap. Az átjáró lapján tekintheti meg az átjáróhoz társított csomópontok. A lap lehetővé teszi az átjáró csomópont törlése.
 
Ha töröl, kattintson a **az előzetes funkciók** az azonos Azure portálon, és tiltsa le az előzetes verziójú funkciók. Az átjárót egy csomópont GA (általánosan rendelkezésre álló) átjáró visszaállítása.


## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:
- [Az adatkezelési átjáró](data-factory-data-management-gateway.md) -átjáró részletes áttekintése.
- [Adatok áthelyezése között a helyszíni és felhőalapú adattároló](data-factory-move-data-between-onprem-and-cloud.md) -egy bemutató részletes utasításokat egy átjáró egyetlen csomóponttal rendelkező tartalmazza. 
