---
title: Fenyegetések észlelése a felhőbeli natív számítást az Azure Security Centerben |} A Microsoft Docs
description: Ez a témakör bemutatja a felhőben az Azure Security Centerben elérhető natív számítási riasztások. az Azure Security Centerben.
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
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571685"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>A fenyegetésészlelés felhőbeli natív számítást az Azure Security Centerben

A felhőszolgáltatók, mint a Security Center kihasználja a belső-naplók elemzése, és azonosítani a támadási módszert a több cél rendelkezik egyedi látható-e. Ez a témakör bemutatja a riasztások a következő Azure-szolgáltatásokhoz érhető el:

* [Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Azure App Service <a name="app-services"></a>

A Security Center a méretezési csoport a felhő keresztül az Azure App Service-ben futó ügyfelek alkalmazásokat célzó támadások azonosítására használ. Webes alkalmazások gyakorlatilag bármely modern hálózati a folyamatban, a támadók mintavételi és kiaknázhatóak ezeket. Bizonyos környezetekben való továbbítása, mielőtt az Azure-ban futó alkalmazások kérelmek próbálja ki több átjáró, ellenőrzött és naplózott. Ezeket az adatokat használjuk majd azonosításához az azokat kihasználó támadások ellen, a támadók, és a további új minták létrehozását, amelyek később fogja használni.

Használata révén, amely az Azure rendelkezik a felhőszolgáltatók látható-e, a Security Center elemzi az App Service belső naplók segítségével határozza meg a több cél támadási módszert. Ha például széles körű keresés és elosztott támadások. Ilyen típusú támadások általában egy kis részét jelentik az IP-címek származik, és kiváló utaló bejárás hasonló végpontokra keresése a sebezhető oldalon vagy a beépülő modul több gazdagépen. Ez a felhő használatával észlelhető, de nem lehet azonosítani egy gazdagép szempontjából.

A Security Center is hozzáfér az alapul szolgáló próbakörnyezetbe lefordítja és virtuális gépek számára. Memória típusú kérdéseket, és az infrastruktúra képes a történet, egy új támadástól, az ügyfél gépek feltörések kártevőcsaládok mozognak. Ezért Security Center észlelni tudja a webalkalmazások hosszú elleni támadások kihasznált után.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Észlelt gyanús WordPress téma meghívása**|Az Azure App Service tevékenységnapló azt jelzi, hogy az App Service erőforrás egy lehetséges kód injektálási tevékenységet.<br/> Ez a gyanús tevékenység hasonló tevékenységnek, amely kezeli a WordPress téma kiszolgálóoldali kód, kiegészítve a úgy témafájl meghívandó közvetlen webes kérelem végrehajtása támogatja. A tevékenységet is látható volt a múltban támadássorozatot részeként WordPress keresztül.|
|**Weblap kapcsolatot észlelt rendellenes IP-címről**|Az Azure App Service tevékenységnapló azt jelzi, hogy kapcsolatot egy bizalmas weblapra, amely soha nem csatlakozott, mielőtt a forrás-címről. Ez azt jelentheti, hogy valaki megpróbál a webalkalmazás felügyeleti oldalakra találgatásos támadás. Az eredmény egy új hiteles felhasználó által használt IP-cím is lehet.|
|**IP-címet, amely csatlakozik az Azure App Service FTP felület található fenyegetések felderítése**|Az Azure App Service FTP-naplók analysis észlelte a forrás-címmel, amely nem található a Fenyegetésfelderítési hírcsatorna kapcsolatot. Során ezt a kapcsolatot a felhasználó az alábbi oldalakat érhető el.|
|**Webalkalmazás-ujjlenyomat észlelt**|Az Azure App Service tevékenységnapló azt jelzi, hogy egy lehetséges webes tevékenység az App Service erőforrás-ujjlenyomat. <br/>A rendszer gyanús tevékenységet észlelt a vakok Elefántviselkedési nevű eszközt társítva. Az eszköz ujjlenyomatok webkiszolgálók, és megpróbálja észlelni a telepített alkalmazások és azok verzióinak. A támadók gyakran használnak az eszköz számára a webalkalmazások tesztelés biztonsági rések.|
|**Esetlegesen sebezhető weblap észlelt gyanús hozzáférés**|Az Azure App Service tevékenységnapló azt jelzi, hogy fértek hozzá, hogy egy weblap, amelyen úgy tűnik, hogy bizalmas. <br/>A gyanús tevékenységek, egy forrás címe, amelynek hozzáférési mintájában hasonlít a webes képolvasó származik. Az ilyen típusú tevékenység gyakran egy támadó a hálózaton, próbálja ki, és hozzáférhetnek a bizalmas vagy sebezhető weblapok kísérlet társítva.|
|**PHP-fájl feltöltése mappában**|Az Azure App Service tevékenységnapló azt jelzi, hogy valami fért hozzá a feltöltési mappában található gyanús PHP lapra. <br/>Az ilyen típusú mappa nem tartalmaz általában PHP-fájlokat. Az ilyen típusú fájl létezik-e egy tetszőleges fájl feltöltése biztonsági rések kihasználásával kiaknázása utalhat.|
|**Linux-parancsok futtatása a Windows App Service-ben**|App Service-ben folyamatok elemzési azt észlelte, hogy a Windows App Service Linux parancs futtatására tett kísérlet. Ez a művelet a webalkalmazás futtatása. Ez a jelenség gyakran látható egy közös webalkalmazás biztonsági réseket kihasználó kampányok során.|
|**Gyanús PHP-végrehajtást észlelt**|Gép naplóinak jelzi, amely egy gyanús PHP-folyamat fut-e. A művelet tartalmazza az operációs rendszer parancsok vagy a PHP-kód futtatása a parancssorból, a PHP-folyamat használatával. Ez a viselkedés jogos lehetnek, a webes alkalmazások ezt a viselkedést is meg a kártékony tevékenységek, például a kísérletek webes parancskörnyezet webhelyek fertőznek.|
|**A folyamat végrehajtását ideiglenes mappából**|App Service-ben folyamatok elemzési az alkalmazás ideiglenes mappából egy folyamat végrehajtásának észlelte. Bár lehet, hogy ez a viselkedés jogos, a webalkalmazásokat a kártékony tevékenységek is meg ezt a viselkedést.|
|**Észlelt magas jogosultsági parancs futtatása**|Az App Service-folyamatok elemzése észlelte a magas szintű jogosultságot igénylő parancs futtatására tett kísérlet. A parancsot futtatta, a webes alkalmazás környezetében. Bár lehet, hogy ez a viselkedés jogos, a webalkalmazásokat a kártékony tevékenységek is meg ezt a viselkedést.|

> [!NOTE]
> Security Center Fenyegetésészlelése App Service-ben jelenleg nem érhető el az Azure government és szuverén felhő-régiók.

További információ az App Service fenyegetésészlelési riasztások látogasson el az Azure Security Center védelme az App Service, és tekintse át a figyelési és az App Service-munkaterhelések védelmének engedélyezése.

## Tárolók <a name="azure-containers"></a>

A Security Center a tárolókhoz a valós idejű fenyegetésészlelés Linuxos gépeken a auditd keretrendszer alapján biztosít. A riasztások Docker gyanús tevékenységek például a létrehozása egy gazdagépen arra utalhat, hogy a Secure Shell (SSH) kiszolgálót egy Docker-tárolót, vagy a titkosítási használja használatát belül futó emelt szintű tároló azonosítása. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét. Linux észlelések, mellett a Security Center is kínál, amelyek adott tárolók központi telepítések analytics.
