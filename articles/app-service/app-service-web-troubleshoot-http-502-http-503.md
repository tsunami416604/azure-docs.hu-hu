---
title: Javítsa ki a 502 Hibás átjáró, 503 a szolgáltatás nem érhető el |} A Microsoft Docs
description: Végezzen hibaelhárítást 502 Hibás átjáró és 503-as szolgáltatás nem érhető el hibák az Azure App Service-ben üzemeltetett webalkalmazásban.
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
ms.openlocfilehash: b1ffc80994244fc44e3cd23cbb05b14e6f878ddb
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125802"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>"502 Hibás átjáró" és "503 a szolgáltatás nem érhető el" az Azure web apps a HTTP-hibáinak elhárítása
"502 Hibás átjáró" és "503 a szolgáltatás nem érhető el" gyakori hibák a lévő üzemeltetett webalkalmazásban [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Ez a cikk segít a hibák elhárítása.

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás kérése**.

## <a name="symptom"></a>Jelenség
Ha a felhasználó a webalkalmazáshoz, egy HTTP adja vissza "502 Hibás átjáró" hiba vagy a http-"503-as szolgáltatás nem érhető el" hiba történt.

## <a name="cause"></a>Ok
Ezt gyakran okozza alkalmazás szintű problémákat, például:

* kérelmek hosszú ideig tart
* magas memória és CPU-t használó alkalmazás
* az alkalmazás összeomlik kivétel miatt.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Hibaelhárítási lépések, "502 Hibás átjáró" és "503 a szolgáltatás nem érhető el" hibák megoldása
Hibaelhárítási három különböző feladatokat, egymást követő sorrendben osztható:

1. [Figyelje meg, és figyelheti az alkalmazások viselkedése](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma megoldásához](#mitigate)

[App Service Web Apps](app-service-web-overview.md) minden lépésnél különféle lehetőségeket kínál.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Figyelje meg, és figyelheti az alkalmazások viselkedése
#### <a name="track-service-health"></a>Szolgáltatások állapotának nyomon követése
A Microsoft Azure publicizes minden alkalommal, amikor a szolgáltatás megszakítás és a teljesítmény teljesítménycsökkenés van. A szolgáltatás állapotának követheti a a [az Azure Portal](https://portal.azure.com/). További információkért lásd: [szolgáltatások állapotának nyomon követése](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>A webes alkalmazás figyelése
Ez a beállítás lehetővé teszi, hogy ismerje meg, ha az alkalmazás van-e bármilyen probléma fel. A web app (webalkalmazás) panelen kattintson a **kérelmek és hibák** csempére. A **metrika** panelen jelennek meg, a mérőszámok is hozzáadhat.

A metrikák, amelyeket érdemes a webalkalmazás figyelése néhány

* Átlagos memória-munkakészlet
* Átlagos válaszidő
* CPU-idő
* Memória-munkakészlet
* Kérelmek

![502 Hibás átjáró és a 503 a szolgáltatás nem érhető el a HTTP-hibák megoldása felé-WebApp figyelése](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

További információkért lásd:

* [Webes alkalmazások monitorozása az Azure App Service-ben](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatok gyűjtése
#### <a name="use-the-azure-app-service-support-portal"></a>Az Azure App Service támogatja a portálon
A Web Apps lehetővé teszi a webes alkalmazás HTTP naplók, eseménynaplókat, folyamat memóriaképek és további megtekintésével kapcsolatos problémák elhárítása biztosít. Mindez az információ támogatási portálunkon címen érheti **http://&lt;az alkalmazás neve >.scm.azurewebsites.net/Support**

Az Azure App Service támogatja a portal nyújt három külön lap az három lépést, a gyakori hibaelhárítási forgatókönyv támogatásához:

1. Figyelje meg a jelenlegi működése
2. Diagnosztikai adatok gyűjtése és a beépített elemzők futó elemzése
3. Problémamegoldás

Ha a probléma most történik, kattintson a **elemzés** > **diagnosztikai** > **diagnosztizálása most** diagnosztikai munkamenet létrehozásához, amely összegyűjti a HTTP-naplókat, az Eseménynapló, a memória kiíratása, a PHP-hibanaplók és a PHP feldolgozni a jelentést.

Az adatok gyűjtése történik, ha azt fogja is elemzést futtat az adatok és biztosít egy HTML-jelentést.

Abban az esetben, ha szeretné letölteni az adatokat, alapértelmezés szerint, azt kellene lennie D:\home\data\DaaS mappában lesz tárolva.

Az Azure App Service-támogatás Portal további információkért lásd: [támogatási Webhelybővítmény Azure websites új frissítések](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>A Kudu hibakereső konzol használata
A Web Apps tartalmaz egy hibakeresési konzolt, amely a hibakeresés, felfedezését, fájlok, valamint a környezet kapcsolatos információk lekérése JSON végpontjainak feltöltése is használhat. Ezt nevezzük a *Kudu konzol* vagy a *SCM irányítópultján* webalkalmazása számára.

Ezt az irányítópultot elérheti a hivatkozás a **https://&lt;az alkalmazás neve >.scm.azurewebsites.net/**.

Néhány dolog, ami a Kudu biztosít a következők:

* az alkalmazás környezeti beállítások
* naplóstream
* diagnosztikai memóriakép
* a hibakeresési konzolt, amelyben futtathatja a Powershell-parancsmagok és alapvető DOS-parancsok.

Egy másik hasznos funkció, a Kudu, abban az esetben, ha az alkalmazás az első-alkalommal kivételeket dob, használhatja a Kudu, és kiírja a SysInternals eszköz Procdump memória létrehozásához. Ezek a memóriaképek pillanatfelvételei a folyamat, és gyakran segíthet a webalkalmazás bonyolultabb hibáinak elhárítása.

A Kudu elérhető funkciókról további információkért lásd: [Azure-webhelyek online eszközeit ismertető](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma megoldásához
#### <a name="scale-the-web-app"></a>A webalkalmazás skálázása
Az Azure App Service a jobb teljesítmény és az átviteli sebesség, módosíthatja a méretezési csoport, amikor az alkalmazás futtatásakor. Webes alkalmazás vertikális felskálázása magában foglalja a két kapcsolódó műveletek: az App Service-csomag módosítása egy magasabb díjszabási csomagot, és bizonyos beállítások konfigurálása után a magasabb tarifacsomagra vált.

Méretezéssel kapcsolatos további információkért lásd: [webalkalmazások méretezése az Azure App Service](web-sites-scale.md).

Emellett kiválaszthatja az alkalmazás futtatásához egynél több példányon. Ez nem csupán nyújt további feldolgozási képesség, de emellett bizonyos mennyiségű hibatűrést biztosít. Ha a folyamat több példányon leáll, a másik példány továbbra is kérelmek.

Beállíthatja, hogy manuális vagy automatikus skálázása.

#### <a name="use-autoheal"></a>Használja az autoheal funkciót
Automatikus javítás funkció újrahasznosítja a munkavégző folyamat az alkalmazáshoz (például a konfigurációs módosítások, kérelmek, memória-alapú korlátok vagy a kérés végrehajtásához szükséges idő) kiválasztott beállítások alapján. Az esetek többségében a folyamat újraindítása a leggyorsabban úgy tudja elhárítani a problémát. Bár mindig újraindíthatja a webalkalmazás közvetlenül az Azure Portalon, automatikus javítás funkció el automatikusan az Ön számára. Mást nem kell tennie az egyes eseményindítók hozzáadása a webalkalmazása számára a legfelső szintű web.config fájlban. Vegye figyelembe, hogy működne-e ezek a beállítások azonos módon, még ha az alkalmazás nem az egyik .net.

További információkért lásd: [Azure-webhelyek automatikus javításáról](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Indítsa újra a webalkalmazást
Ez gyakran az helyreállíthatja az egyszeri hibák legegyszerűbb módja. Az a [az Azure Portal](https://portal.azure.com/), a webalkalmazás panelén, a lehetőségek közül választhat az alkalmazás újraindítása vagy leállítása.

 ![Indítsa újra az alkalmazást 502 Hibás átjáró és a 503 a szolgáltatás nem érhető el a HTTP-hibák megoldása](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

A webalkalmazás az Azure Powershell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

