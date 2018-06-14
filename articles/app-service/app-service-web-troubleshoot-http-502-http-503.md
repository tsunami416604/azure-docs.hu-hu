---
title: Javítsa ki a hibás átjáró 502, 503-as szolgáltatás nem érhető el |} Microsoft Docs
description: Végezzen hibaelhárítást a 502 Hibás átjáró hibák és 503-as szolgáltatás nem érhető el az Azure App Service-ben futtatott webalkalmazás.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 Hibás átjáró 503-as szolgáltatás nem érhető el, 503-as hiba, 502-es hiba
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836517"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>A "502 Hibás átjáró" és "503-as szolgáltatás nem érhető el" az Azure web apps a HTTP-hibák elhárítása
"502 Hibás átjáró" és "503-as szolgáltatás nem érhető el" tárolva a webalkalmazás gyakori hibáinak [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ez a cikk segítséget nyújt a hibák elhárítása.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás**.

## <a name="symptom"></a>Jelenség
Ha a felhasználó azt a webalkalmazást, egy HTTP adja vissza "502 Hibás átjáró" hiba vagy a HTTP "503-as szolgáltatás nem érhető el" hiba.

## <a name="cause"></a>Ok
Ez a probléma gyakran alkalmazás szintű problémákat, például:

* hosszú ideig kérelmek
* nagy memória/CPU-t használó alkalmazások
* az alkalmazás összeomló kivétel miatt.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Hibaelhárítási lépések "502 Hibás átjáró" és "503-as szolgáltatás nem érhető el" hiba elhárítása érdekében
Hibaelhárítás három különböző feladatokat, egymás utáni sorrendben osztható:

1. [Figyelje meg, és figyelheti az alkalmazások viselkedését](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma elhárítása érdekében](#mitigate)

[App Service Web Apps](/services/app-service/web/) minden lépésnél különböző lehetőséget biztosít.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Figyelje meg, és figyelheti az alkalmazások viselkedését
#### <a name="track-service-health"></a>Nyomon követése szolgáltatásának állapota
A Microsoft Azure publicizes minden alkalommal, amikor a szolgáltatás megszakadásának vagy a teljesítmény romlását van. A a szolgáltatás állapotának nyomon követheti a [Azure Portal](https://portal.azure.com/). További információkért lásd: [szolgáltatás állapotát nyomon](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>A webes alkalmazás figyelése
Ez a beállítás lehetővé teszi annak megállapítása, ha az alkalmazás van bármilyen probléma merül. A webalkalmazás panelen kattintson a **kérések és hibák követése** csempére. A **metrika** panelen láthatja, adhat hozzá minden metrikákat.

A metrikák, amelyeket a webalkalmazás figyelésére érdemes vannak

* Munkakészlet átlagos memória
* Átlagos válaszidő
* CPU-idő
* Memória munkakészlete
* Kérelmek

![a figyelő a webalkalmazás 502 Hibás átjáró és 503-as szolgáltatás nem érhető el a HTTP-hibák megoldása felé](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

További információkért lásd:

* [Az Azure App Service Web-alkalmazások figyelése](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatok gyűjtése
#### <a name="use-the-azure-app-service-support-portal"></a>Az Azure App Service támogatási portálon
Web Apps tesz lehetővé teszi a webalkalmazás HTTP naplókat, eseménynaplók, folyamat memóriaképek és több megtekintésével kapcsolatos problémák elhárítása. Végezheti el az összes ezt az információt a támogatási portálon, a **http://&lt;az alkalmazás neve >.scm.azurewebsites.net/Support**

Az Azure App Service támogatja a portál tesz lehetővé a három lépést az általános hibaelhárítási forgatókönyv támogatásához három különálló lappal:

1. Figyelje meg a jelenlegi viselkedése
2. Diagnosztikai adatok gyűjtése és a beépített elemzőkkel futtató elemzése
3. Csökkentése

Ha a probléma most történik, kattintson a **elemzés** > **diagnosztika** > **diagnosztizálása most** diagnosztikai munkamenet létrehozására meg, amely összegyűjti a HTTP jelentkezik, az Eseménynapló, memóriát, kiírt fájlok, a PHP hibanaplókat és a PHP feldolgozni a jelentést.

Miután az adatgyűjtés a következők, emellett futtassa az adatok elemzése és is biztosítanak egy HTML-jelentést.

Abban az esetben, ha szeretné letölteni az adatokat, alapértelmezés szerint, akkor legyen tárolva a D:\home\data\DaaS mappában.

Az Azure App Service-támogatás Portal további információkért lásd: [támogatási webhely bővítménynek az Azure-webhelyek új frissítések](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>A Kudu hibakereső konzol használata
A hibakereső konzol hibakeresés, felfedezése, fájlok, valamint JSON végpontokat a környezettel kapcsolatos információk beolvasásakor feltöltése használható webes alkalmazásokat tartalmaz. Ez a lehetőség a *Kudu konzol* vagy a *SCM irányítópult* a webalkalmazás.

Nyissa meg a hivatkozás férhet hozzá az irányítópulthoz **https://&lt;az alkalmazás neve >.scm.azurewebsites.net/**.

Az, amit a Kudu biztosítja a következők:

* az alkalmazás környezeti beállítások
* naplófolyamot
* diagnosztikai memóriakép
* a hibakeresési konzol, ahol futtathatja a Powershell-parancsmagok és alapvető DOS parancsok.

Egy másik fontos része a Kudu, abban az esetben, ha az alkalmazás első-alkalommal kivételek szűrész, használhatja a Kudu, és kiírja a SysInternals eszköz Procdump memória létrehozásához. Ezek memóriaképek pillanatképek a folyamat, és gyakran segítségével bonyolultabb webalkalmazásokba elhárítása.

A Kudu szolgáltatásainak további információkért lásd: [Azure Websitesra online eszközök kell tudni](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma elhárítása érdekében
#### <a name="scale-the-web-app"></a>A webalkalmazás skálázása
Az Azure App Service a teljesítmény növelése és a teljesítményt, módosíthatja a skála, amelyen az alkalmazást futtat. A webes alkalmazás vertikális felskálázásával magában foglalja a két kapcsolódó műveletek: az App Service-csomag módosítása egy magasabb szintű tarifacsomagban használható, és bizonyos beállítások konfigurálása után a magasabb szintű tarifacsomagban használható váltott.

A méretezés további információkért lásd: [egy webalkalmazás skálázása az Azure App Service](web-sites-scale.md).

Továbbá ha szeretné, futtassa az alkalmazást a egynél több példány. Ez nem csak nyújt további feldolgozási képesség, de is lehetővé teszi bizonyos hibatűrést. Ha a folyamat leáll egy példányán, a többi példány továbbra is küldött kérelmek.

Manuális vagy automatikus skálázás állíthatja be.

#### <a name="use-autoheal"></a>Elindulásáról használata
Elindulásáról (például a konfigurációs módosításokat, kérelmek, memória-alapú korlátok vagy egy kérelem végrehajtásához szükséges idő) kiválasztott beállítások alapján az alkalmazás munkavégző folyamata újraindul. Az esetek többségében a folyamat újrahasznosítását leggyorsabban úgy tudja elhárítani a problémát az. Abban az esetben, ha a webes alkalmazás közvetlenül az Azure portálon belül mindig újraindíthatja, elindulásáról lesz automatikusan elvégezze ezt meg. Végre kell hajtani mindössze néhány eseményindítók hozzáadása a webalkalmazás a legfelső szintű web.config fájlban. Vegye figyelembe, hogy ezek a beállítások csatlakoztatás működik ugyanúgy akkor is, ha az alkalmazás nem a .net, egyet.

További információkért lásd: [automatikus javítás Azure webhelyek](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>A webalkalmazás újraindítása
Ez gyakran az az egyszeri hibák legegyszerűbb módja. Az a [Azure Portal](https://portal.azure.com/), a webalkalmazás panelen, lehetősége van a leállítása, vagy indítsa újra az alkalmazást.

 ![Indítsa újra a hibás átjáró 502 és 503-as szolgáltatás nem érhető el a HTTP-hibák megoldására alkalmazást](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

A webalkalmazás Azure Powershell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

