---
title: Fenyegetések észlelése a felhőalapú natív számítástechnika Azure Security Centerban | Microsoft Docs
description: Ez a cikk a Felhőbeli natív számítási riasztásokat ismerteti Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: d705174f46ba38f37ae8cce9839c2d1f63ab6bf8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664273"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Fenyegetések észlelése a Felhőbeli natív számítástechnika Azure Security Center

Natív megoldásként Azure Security Center egyedi láthatóságot biztosít a belső naplók számára a támadási módszertan azonosítására több cél között. Ez a cikk a következő Azure-szolgáltatásokhoz elérhető riasztásokat ismerteti:

* [Azure App Service](#app-services)
* [Azure-tárolók](#azure-containers) 

> [!NOTE]
> Ezek a szolgáltatások jelenleg nem érhetők el az Azure governmentben és a szuverén Felhőbeli régiókban.

## Azure App Service<a name="app-services"></a>

Security Center a felhő méretezésével azonosítja a App Serviceon futó alkalmazásokat célzó támadásokat. A támadók webes alkalmazásokat kereshetnek a gyengeségek megtalálásához és kiaknázásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Az Azure felhőalapú szolgáltatóként való láthatóságának használatával Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. A módszertan például kiterjedt keresési és elosztott támadásokat tartalmaz. Ez a típusú támadás általában az IP-címek egy kis részhalmazát mutatja be, és a hasonló végpontokra való bejárási mintákat jeleníti meg több gazdagépen. A támadások egy sebezhető oldalra vagy beépülő modulra keresnek, és egyetlen gazdagép szempontjából nem azonosíthatók.

A Security Center az alapul szolgáló munkapéldányokat és virtuális gépeket is elérheti. A memória-kriminalisztikai szolgáltatással együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért még akkor is, ha a webalkalmazás kihasználása után Security Center van telepítve, lehetséges, hogy észlelni tudja a folyamatos támadásokat.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Gyanús WordPress Theme meghívást észlelt**|A App Service tevékenység naplója az App Service-erőforrás lehetséges kód-injektálási tevékenységét jelzi.<br/> Ez a gyanús tevékenység olyan tevékenységre hasonlít, amely egy WordPress-témát kezel a kód kiszolgálóoldali végrehajtásának támogatásához, majd egy közvetlen webes kérést, amely a manipulált téma meghívására szolgál. Ez a típusú tevékenység a WordPress elleni támadási kampány része lehet.|
|**Kapcsolat a weboldallal az észlelt rendellenes IP-címről**|A App Service tevékenység naplója egy bizalmas weboldalhoz való kapcsolódást jelez egy olyan forrásoldali címről, amely még soha nem kapcsolódott hozzá. Ez a kapcsolat arra utalhat, hogy valaki egy találgatásos támadási kísérletet tesz a webalkalmazás-felügyeleti oldalaira. Az is előfordulhat, hogy egy új IP-címet használó legitim felhasználó eredménye.|
|**Az Azure App Service FTP-interfészhez kapcsolódó IP-cím található a veszélyforrások felderítésében**|App Service az FTP-naplók elemzése a veszélyforrások adatcsatornájában található forrás-címről származó kapcsolatokat észlelt. A kapcsolat során a felhasználók hozzáfértek a felsorolt lapokhoz.|
|**Webes ujjlenyomat észlelhető**|A App Service tevékenység naplója egy lehetséges webes ujjlenyomat-hozzárendelési tevékenységet jelez a App Service erőforráson. <br/>Ez a gyanús tevékenység egy vak elefánt nevű eszközhöz van társítva. Az eszköz ujjlenyomatok webkiszolgálók, és megkísérli felderíteni a telepített alkalmazásokat és azok verzióit. A támadók gyakran ezt az eszközt használják a webalkalmazások felderítésére a biztonsági rések kereséséhez.|
|**A rendszer gyanús hozzáférést észlelt a valószínűleg sebezhető weblaphoz**|A App Service tevékenység naplója azt jelzi, hogy egy bizalmasnak tűnő weblap elérhető. <br/>Ez a gyanús tevékenység olyan forrásoldali címről származik, amelynek a hozzáférési mintája egy webes képolvasó esetében hasonlít. Ez a fajta tevékenység gyakran a támadók által a hálózatra irányuló kísérlettel van társítva, hogy megpróbáljon hozzáférni a bizalmas vagy sebezhető weblapokhoz.|
|**PHP-fájl a feltöltési mappában**|A App Service tevékenység naplója azt jelzi, hogy valami hozzáfért egy gyanús PHP-oldalhoz a feltöltés mappában. <br/>Ez a típusú mappa általában nem tartalmaz PHP-fájlokat. Az ilyen típusú fájlok létezése arra utalhat, hogy kihasználja a fájlok feltöltésének tetszőleges sebezhetőségét.|
|**Linux-parancsok futtatására tett kísérlet Windows App Service**|App Service folyamatok elemzése azt észlelte, hogy egy Linux-parancs futtatására tett kísérletet a Windows App Service. Ezt a műveletet a webalkalmazás futtatta. Ezt a viselkedést gyakran a közös webalkalmazásban biztonsági rést kihasználó kampányoknál tekintjük meg.|
|**Gyanús PHP-végrehajtás észlelhető**|A gépi naplók azt jelzik, hogy egy gyanús PHP-folyamat fut. A művelet az operációs rendszer parancsainak vagy a PHP-kód parancssorból való futtatására tett kísérletet tartalmazott a PHP-folyamat használatával. Habár ez a viselkedés lehet legitim, a webalkalmazásokban ez a viselkedés rosszindulatú tevékenységeket jelezhet, például a webhelyek webes rendszerhéjokkal történő megfertőzésére tett kísérleteket.|
|**Folyamat-végrehajtás ideiglenes mappából**|App Service folyamatok elemzése egy folyamat végrehajtását észlelte az alkalmazás ideiglenes mappájából. Habár ez a viselkedés legitim lehet, a webes alkalmazásokban ez a viselkedés rosszindulatú tevékenységeket jelezhet.|
|**Kísérlet történt a magas jogosultságú parancs futtatására**|A App Service folyamatok elemzése olyan parancs futtatására tett kísérletet észlelt, amely magas szintű jogosultságokat igényel. A parancs a webalkalmazási környezetben futott. Habár ez a viselkedés legitim lehet, a webes alkalmazásokban ez a viselkedés rosszindulatú tevékenységeket jelezhet.|

## Azure-tárolók<a name="azure-containers"></a>

Security Center valós idejű veszélyforrások észlelését teszi lehetővé a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A fenyegetéseket különböző szinteken észleljük: 

* A **gazdagép szintjének** Security Center ügynöke (a standard szinten érhető el, a részletek [díjszabása](security-center-pricing.md) ) figyeli a Linuxot a gyanús tevékenységekhez. Az ügynök riasztásokat küld a csomópontból vagy a rajta futó tárolóból származó gyanús tevékenységekről. Ilyen tevékenység például a webrendszerhéj-észlelés és a kapcsolat ismert gyanús IP-címekkel.

    Az ügynök a tároló-specifikus elemzéseket figyeli, így mélyebb betekintést nyújt a tároló-környezet biztonságára. Riasztásokat indít az eseményekhez, például a privilegizált tárolók létrehozásához, az API-kiszolgálókhoz való gyanús hozzáféréshez, valamint a Secure Shell-(SSH-) kiszolgálókhoz, amelyek egy Docker-tárolón belül futnak.

    >[!NOTE]
    > Ha úgy dönt, hogy nem telepíti az ügynököket a gazdagépekre, csak a veszélyforrások észlelésével járó előnyök és riasztások egy részhalmazát fogja kapni. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.

* **AK-fürt szintjén**a Kubernetes-naplózási elemzések alapján észlelt fenyegetések észlelése. Az **ügynök** nélküli figyelés engedélyezéséhez adja hozzá az előfizetéshez a Kubernetes lehetőséget a **díjszabási & beállítások** lapon (lásd a [díjszabást](security-center-pricing.md)). A riasztások ezen a szinten történő létrehozásához Security Center figyeli az AK által felügyelt szolgáltatásokat az AK által beolvasott naplók használatával. Az ezen a szinten található események közé tartoznak például az elérhető Kubernetes-irányítópultok, a magas jogosultsági szintű szerepkörök létrehozása és a bizalmas csatlakoztatások létrehozása. 

    >[!NOTE]
    > A Security Center észlelési riasztásokat hoz létre az Azure Kubernetes szolgáltatás műveleteihez és központi telepítések esetén, miután a Kubernetes beállítás engedélyezve van az előfizetési beállításokban. 

Emellett a biztonsági kutatók globális csapata folyamatosan figyeli a fenyegetés tájképét. A felderített tároló-specifikus riasztásokat és biztonsági réseket adják hozzá.


### <a name="aks-cluster-level-alerts"></a>AK-beli fürt szintű riasztások

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**ELŐNÉZET – szerepkör kötése a fürthöz – a rendszer a rendszergazda szerepkört észlelte**|A Kubernetes naplózási elemzési naplója egy új kötést észlelt a fürt – rendszergazda szerepkörhöz, amely rendszergazdai jogosultságokat eredményezett. A rendszergazdai jogosultságok szükségtelen biztosításához a fürt jogosultság-eszkalációs problémái merülhetnek fel.|
|**ELŐNÉZET – elérhető Kubernetes-irányítópult észlelve**|A Kubernetes-naplózási napló elemzése a Kubernetes-irányítópult terheléselosztó-szolgáltatás általi expozícióját észlelte. A közzétett irányítópultok lehetővé teszik a nem hitelesített hozzáférést a fürt felügyeletéhez, és biztonsági fenyegetést jelenthetnek.|
|**ELŐZETES verzió – új, magas jogosultsági szintű szerepkör észlelhető**|A Kubernetes napló elemzése egy új, magas jogosultságokkal rendelkező szerepkört észlelt. A magas jogosultságokkal rendelkező szerepkörhöz tartozó kötések a fürtben a felhasználó/csoport számára emelt szintű jogosultságokat biztosítanak. Az emelt szintű jogosultságok szükségtelen biztosításához a fürt jogosultság-eszkalációs problémái merülhetnek fel.|
|**ELŐZETES verzió – a rendszer a Kube új tárolóját észlelte.**|A Kubernetes napló elemzése egy új tárolót észlelt a Kube-System névtérben, amely nem tartozik a névtérben általában futó tárolók közé. A Kube-rendszer névterei nem tartalmazhatnak felhasználói erőforrásokat. A támadók a kártékony összetevők elrejtésére használhatják ezt a névteret.|
|**ELŐNÉZET – digitális valutaváltó tároló észlelve**|A Kubernetes napló elemzése olyan tárolót észlelt, amely egy digitális valutaváltó eszközhöz társított képpel rendelkezik.|
|**ELŐZETES verzió – a privilegizált tároló észlelve**|A Kubernetes napló elemzése új emelt szintű tárolót észlelt. Egy emelt szintű tároló hozzáfér a csomópont erőforrásaihoz, és megszakítja a tárolók elkülönítését. Ha sérült, a támadó a privilegizált tárolóval hozzáférhet a csomóponthoz.|
|**ELŐNÉZET – a rendszer érzékeny kötet csatlakoztatásával rendelkező tárolót észlelt**|A Kubernetes napló elemzése egy új, bizalmas kötet csatlakoztatásával rendelkező tárolót észlelt. A észlelt kötet egy olyan hostPath-típus, amely bizalmas fájlt vagy mappát csatlakoztat a csomópontból a tárolóhoz. Ha a tároló biztonságban van, a támadó ezt a csatlakoztatást használva hozzáférhet a csomóponthoz.|



### <a name="host-level-alerts"></a>Gazdagép szintű riasztások

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**A privilegizált tároló észlelve**|A gépi naplók azt jelzik, hogy egy emelt szintű Docker-tároló fut. Az emelt szintű tárolók teljes hozzáféréssel rendelkeznek a gazdagép erőforrásaihoz. Ha sérült, a támadó a privilegizált tárolóval hozzáférhet a gazdagéphez.|
|**Rendszerjogosultságú parancs futtatása a tárolóban**|A gépi naplók azt jelzik, hogy egy emelt szintű parancs fut egy Docker-tárolóban. A rendszerjogosultságú parancsok kiterjesztett jogosultságokkal rendelkeznek a gazdagépen.|
|**Feltett Docker-démon észlelve**|A gépi naplók azt jelzik, hogy a Docker-démon (dockerd) egy TCP-szoftvercsatornát tesz elérhetővé. Alapértelmezés szerint a Docker-konfiguráció nem használ titkosítást vagy hitelesítést, ha a TCP-szoftvercsatorna engedélyezve van. Ha bárki hozzáfér a megfelelő porthoz, akkor teljes hozzáférést kaphat a Docker-démonhoz.|
|**Az SSH-kiszolgáló egy tárolón belül fut**|A gépi naplók azt jelzik, hogy egy SSH-kiszolgáló egy Docker-tárolón belül fut. Habár ez a viselkedés szándékos lehet, gyakran azt jelzi, hogy egy tároló hibásan van konfigurálva vagy megszegve.|
|**A tárolót bányász képképpel észlelték**|A gépi naplók a digitális valuta kitermeléséhez kapcsolódó rendszerképet futtató Docker-tárolók végrehajtását jelzik. Ez a viselkedés valószínűleg azt jelzi, hogy az erőforrások visszaéltek.|
|**Gyanús kérelem Kubernetes API-hoz**|A gépi naplók azt jelzik, hogy gyanús kérés történt a Kubernetes API-nak. A kérelem egy Kubernetes-csomópontból lett elküldve, valószínűleg a csomóponton futó egyik tárolóból. Bár ez a viselkedés szándékos lehet, jelezheti, hogy a csomópont sérült tárolót futtat.|
|**Gyanús kérelem a Kubernetes-irányítópultra**|A gépi naplók azt jelzik, hogy gyanús kérés történt a Kubernetes irányítópultján. A kérelem egy Kubernetes-csomópontból lett elküldve, valószínűleg a csomóponton futó egyik tárolóból. Bár ez a viselkedés szándékos lehet, jelezheti, hogy a csomópont sérült tárolót futtat.|