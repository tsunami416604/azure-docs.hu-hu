---
title: Fenyegetések észlelése a Felhőbeli natív számítási feladatokhoz a Azure Security Centerban | Microsoft Docs
description: Ez a témakör a Felhőbeli natív számítási riasztásokat mutatja be Azure Security Centerban.
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
ms.openlocfilehash: 78f7633af1631eab8fdfb21fb8ff94eafc0247a9
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013359"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Fenyegetések észlelése a Felhőbeli natív számítási feladatokhoz Azure Security Center

A Felhőbeli szolgáltatóként a Azure Security Center az egyedi láthatóságot használja a belső naplók elemzéséhez és a támadási módszerek azonosításához több célponton. Ez a témakör a következő Azure-szolgáltatásokhoz elérhető riasztásokat mutatja be:

* [Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center a felhő méretezésével azonosítja a App Serviceon futó alkalmazásokat célzó támadásokat. A webalkalmazások közösek a modern hálózatokban, és a támadók mintavételt használnak a gyengeségek kihasználásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Az Azure felhőalapú szolgáltatóként való láthatóságának használatával Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. A módszertan például kiterjedt keresési és elosztott támadásokat tartalmaz. Ez a típusú támadás általában az IP-címek kis részéből származik, és a hasonló végpontokra való bejárási mintákat mutatja több gazdagépen. A támadások egy sebezhető oldalra vagy beépülő modulra keresnek, és egyetlen gazdagép szempontjából nem azonosíthatók.

A Security Center az alapul szolgáló munkapéldányokat és virtuális gépeket is elérheti. A memória-kriminalisztikai szolgáltatással együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért a Security Center a webalkalmazások elleni támadásokat a kihasználás után hosszú ideig képes felderíteni.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Gyanús WordPress Theme meghívást észlelt**|A App Service tevékenység naplója az App Service-erőforrás lehetséges kód-injektálási tevékenységét jelzi.<br/> Ez a gyanús tevékenység olyan tevékenységre hasonlít, amely egy WordPress-témát kezel a kód kiszolgálóoldali végrehajtásának támogatásához, majd egy közvetlen webes kérést, amely a manipulált téma meghívására szolgál. Ez a típusú tevékenység a WordPress elleni támadási kampány része lehet.|
|**Kapcsolat a weboldallal az észlelt rendellenes IP-címről**|A App Service tevékenység naplója egy bizalmas weboldalhoz való kapcsolódást jelez egy olyan forrásoldali címről, amely még soha nem kapcsolódott hozzá. Ez arra utalhat, hogy valaki egy találgatásos támadásra tesz kísérletet a webalkalmazás-adminisztrációs oldalain. Az is előfordulhat, hogy egy új IP-címet használó legitim felhasználó eredménye.|
|**Az Azure App Service FTP-interfészhez kapcsolódó IP-cím található a veszélyforrások felderítésében**|App Service az FTP-naplók elemzése a veszélyforrások adatcsatornájában található forrás-címről származó kapcsolatokat észlelt. A kapcsolat során a felhasználók hozzáfértek a felsorolt lapokhoz.|
|**Webes ujjlenyomat észlelhető**|A App Service tevékenység naplója egy lehetséges webes ujjlenyomat-hozzárendelési tevékenységet jelez a App Service erőforráson. <br/>Ez a gyanús tevékenység egy vak elefánt nevű eszközhöz van társítva. Az eszköz ujjlenyomatok webkiszolgálók, és megkísérli felderíteni a telepített alkalmazásokat és azok verzióit. A támadók gyakran ezt az eszközt használják a webalkalmazások felderítésére a biztonsági rések kereséséhez.|
|**A rendszer gyanús hozzáférést észlelt a valószínűleg sebezhető weblaphoz**|A App Service tevékenység naplója azt jelzi, hogy egy bizalmasnak tűnő weblap elérhető. <br/>Ez a gyanús tevékenység olyan forrásoldali címről származik, amelynek a hozzáférési mintája egy webes képolvasó esetében hasonlít. Ez a fajta tevékenység gyakran a támadók által a hálózatra irányuló kísérlettel van társítva, hogy megpróbáljon hozzáférni a bizalmas vagy sebezhető weblapokhoz.|
|**PHP-fájl a feltöltési mappában**|A App Service tevékenység naplója azt jelzi, hogy valami hozzáfért egy gyanús PHP-oldalhoz a feltöltés mappában. <br/>Ez a típusú mappa általában nem tartalmaz PHP-fájlokat. Az ilyen típusú fájlok létezése arra utalhat, hogy kihasználja a fájlok feltöltésének tetszőleges sebezhetőségét.|
|**Linux-parancsok futtatására tett kísérlet Windows App Service**|App Service folyamatok elemzése azt észlelte, hogy egy Linux-parancs futtatására tett kísérletet a Windows App Service. Ezt a műveletet a webalkalmazás futtatta. Ezt a viselkedést gyakran a közös webalkalmazásban biztonsági rést kihasználó kampányoknál tekintjük meg.|
|**Gyanús PHP-végrehajtás észlelhető**|A gépi naplók azt jelzik, hogy egy gyanús PHP-folyamat fut. A művelet az operációs rendszer parancsainak vagy a PHP-kód parancssorból való futtatására tett kísérletet tartalmazott a PHP-folyamat használatával. Habár ez a viselkedés lehet legitim, a webalkalmazásokban ez a viselkedés rosszindulatú tevékenységeket jelezhet, például a webhelyek webes rendszerhéjokkal történő megfertőzésére tett kísérleteket.|
|**Folyamat-végrehajtás ideiglenes mappából**|App Service folyamatok elemzése egy folyamat végrehajtását észlelte az alkalmazás ideiglenes mappájából. Habár ez a viselkedés legitim lehet, a webes alkalmazásokban ez a viselkedés rosszindulatú tevékenységeket jelezhet.|
|**Kísérlet történt a magas jogosultságú parancs futtatására**|A App Service folyamatok elemzése olyan parancs futtatására tett kísérletet észlelt, amely magas szintű jogosultságokat igényel. A parancs a webalkalmazási környezetben futott. Habár ez a viselkedés legitim lehet, a webes alkalmazásokban ez a viselkedés rosszindulatú tevékenységeket jelezhet.|

> [!NOTE]
> Security Center veszélyforrások észlelése App Service jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

## Azure Container Service<a name="azure-containers"></a>

Security Center valós idejű veszélyforrásokat észlel a tárolók számára a Linux rendszerű gépeken az auditált keretrendszer alapján. A riasztások számos gyanús Docker-tevékenységet azonosítanak, például egy gazdagépen egy emelt szintű tároló létrehozását, egy Docker-tárolón belül futó Secure Shell-(SSH-) kiszolgáló, vagy egy kriptográfiai bányászok használatát. 

Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét. A Linux-észlelések mellett a Security Center a tárolók üzembe helyezésére vonatkozó elemzéseket is kínál.
