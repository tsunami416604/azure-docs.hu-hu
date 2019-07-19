---
title: Fenyegetések észlelése a Felhőbeli natív számítási feladatokhoz a Azure Security Centerban | Microsoft Docs
description: Ez a témakör a Felhőbeli natív számítási riasztásokat mutatja be Azure Security Centerban. Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295845"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Fenyegetések észlelése a Felhőbeli natív számítási feladatokhoz Azure Security Center

A felhőalapú szolgáltatóként a Security Center kihasználja az egyedi láthatóságot, amely a belső naplók elemzésére és a támadási módszerek azonosítására szolgál több cél esetében. Ez a témakör a következő Azure-szolgáltatásokhoz elérhető riasztásokat mutatja be:

* [Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center kihasználja a felhő méretezését, hogy azonosítsa a Azure App Serviceon futó ügyfelekre irányuló támadásokat. Ha a webalkalmazások gyakorlatilag bármilyen modern hálózaton vannak, a támadók megkeresik ezeket a gyengeségeket, és kihasználják azokat. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón haladnak át, ahol megvizsgálják és naplózzák azokat. Ezt követően a rendszer felhasználja ezeket az információkat a biztonsági rések, a támadók és a később felhasználható új minták azonosítására.

Azáltal, hogy az Azure-t felhőalapú szolgáltatóként látja el, Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. Ilyenek például a széleskörű vizsgálat és az elosztott támadások. Ez a típusú támadás általában az IP-címek kis részéből származik, és a hasonló végpontokra való bejárási mintákat mutatja több gazdagépen, egy sebezhető oldal vagy beépülő modul keresésekor. Ez a felhő használatával észlelhető, de egyetlen gazdagép szempontjából nem azonosítható.

A Security Center az alapul szolgáló munkapéldányokat és virtuális gépeket is elérheti. A memória-kriminalisztikai szolgáltatással együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért a Security Center a webalkalmazások elleni támadásokat a kihasználás után hosszú ideig képes felderíteni.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Gyanús WordPress Theme meghívást észlelt**|A Azure App Service tevékenység naplója az App Service-erőforrás lehetséges kód-injektálási tevékenységét jelzi.<br/> Ez a gyanús tevékenység olyan tevékenységhez hasonlít, amely egy WordPress-témát kezel a kód kiszolgálóoldali végrehajtásának támogatásához, majd egy közvetlen webes kérést, amely a manipulált téma meghívására szolgál. Ez a típusú tevékenység a múltban a WordPress elleni támadási kampány részeként látható.|
|**Kapcsolat a weboldallal az észlelt rendellenes IP-címről**|A Azure App Service tevékenység naplója egy bizalmas weboldalhoz való kapcsolódást jelez egy olyan forrásoldali címről, amely még soha nem kapcsolódott hozzá. Ez arra utalhat, hogy valaki egy találgatásos támadásra tesz kísérletet a webalkalmazás-adminisztrációs oldalain. Az is előfordulhat, hogy egy legitim felhasználó új IP-címet használ.|
|**Az Azure App Service FTP-interfészhez kapcsolódó IP-cím található a veszélyforrások felderítésében**|Azure App Service az FTP-naplók elemzése a veszélyforrások felderítése hírcsatornában található forrás címről érkező kapcsolatokat észlelt. Ebben a kapcsolatban a felhasználók az alább felsorolt oldalakat érik el.|
|**Webes ujjlenyomat észlelhető**|A Azure App Service tevékenység naplója egy lehetséges webes ujjlenyomat-hozzárendelési tevékenységet jelez a App Service erőforráson. <br/>Ez a gyanús tevékenység a Blind Elephant nevű eszközhöz van társítva. Az eszköz ujjlenyomatok webkiszolgálók, és megkísérli felderíteni a telepített alkalmazásokat és azok verzióit. A támadók gyakran ezt az eszközt használják a webalkalmazások felderítésére a biztonsági rések kereséséhez.|
|**A rendszer gyanús hozzáférést észlelt a valószínűleg sebezhető weblaphoz**|A Azure App Service tevékenység naplója azt jelzi, hogy egy bizalmasnak tűnő weblap elérhető. <br/>Ez a gyanús tevékenység olyan forrásoldali címről származik, amelynek a hozzáférési mintája egy webes képolvasó esetében hasonlít. Ez a fajta tevékenység gyakran a támadók által a hálózatra irányuló kísérlettel van társítva, hogy megpróbáljon hozzáférni a bizalmas vagy sebezhető weblapokhoz.|
|**PHP-fájl a feltöltési mappában**|A Azure App Service tevékenység naplója azt jelzi, hogy valami hozzáfért egy gyanús PHP-oldalhoz, amely a feltöltés mappában található. <br/>Ez a típusú mappa általában nem tartalmaz PHP-fájlokat. Az ilyen típusú fájlok létezése arra utalhat, hogy kihasználja a fájlok feltöltésének tetszőleges sebezhetőségét.|
|**Linux-parancsok futtatására tett kísérlet Windows App Service**|App Service folyamatok elemzése azt észlelte, hogy egy Linux-parancs futtatására tett kísérletet a Windows App Service. Ezt a műveletet a webalkalmazás futtatta. Ez gyakran előfordul a gyakori webalkalmazások biztonsági réseit kihasználó kampányok során.|
|**Gyanús PHP-végrehajtás észlelhető**|A gépi naplók azt jelzik, hogy egy gyanús PHP-folyamat fut. A művelet az operációsrendszer-parancsok vagy PHP-kód parancssorból való futtatására tett kísérletet a PHP-folyamat használatával. Habár ez a viselkedés legitim lehet, a webalkalmazásokban ez a viselkedés olyan rosszindulatú tevékenységekben is megfigyelhető, mint például a webhelyek webes rendszerhéjokkal történő megfertőzésének kísérlete.|
|**Folyamat-végrehajtás ideiglenes mappából**|App Service folyamatok elemzése egy folyamat végrehajtását észlelte az alkalmazás ideiglenes mappájából. Habár ez a viselkedés legitim lehet, a webalkalmazásokban ez a viselkedés a kártékony tevékenységekben is megfigyelhető.|
|**Kísérlet történt a magas jogosultságú parancs futtatására**|A App Service folyamatok elemzése olyan parancs futtatására tett kísérletet észlelt, amely magas szintű jogosultságokat igényel. A parancs a webalkalmazási környezetben futott. Habár ez a viselkedés legitim lehet, a webalkalmazásokban ez a viselkedés a kártékony tevékenységekben is megfigyelhető.|

> [!NOTE]
> Security Center veszélyforrások észlelése App Service jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

App Service veszélyforrások észlelésével kapcsolatos riasztásokról a App Service védelme a Azure Security Center használatával című témakörben olvashat bővebben, és áttekintheti, hogyan engedélyezheti a App Service számítási feladatok figyelését és védelmét.

## Konténerek<a name="azure-containers"></a>

Security Center valós idejű veszélyforrásokat észlel a tárolók számára a Linux rendszerű gépeken az auditált keretrendszer alapján. A riasztások számos gyanús Docker-tevékenységet azonosítanak, például egy gazdagépre emelt jogosultságú tároló létrehozását, a Docker-tárolón belül futó Secure Shell-(SSH-) kiszolgáló, illetve a kriptográfiai bányászok használatát. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét. A Linux-észlelések mellett a Security Center a tárolók üzembe helyezésére vonatkozó elemzéseket is kínál.
