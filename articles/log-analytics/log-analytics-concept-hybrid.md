---
title: Adatokat gyűjteni a környezet az Azure Naplóelemzés |} Microsoft Docs
description: Ez a témakör segítségével megtudhatja, hogyan adatok gyűjtéséhez és a helyszíni vagy más felhőalapú környezetben a Naplóelemzési található számítógépek figyeléséhez.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: magoedte
ms.openlocfilehash: 2597b434bc6db0d5639709a9ce869462c3e47f56
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Adatgyűjtés a Naplóelemzési a környezetében lévő számítógépek

Az Azure Naplóelemzés gyűjtenek, majd intézkedjen szereplő Windows vagy Linux rendszerű számítógépek adatait:

* [Az Azure virtuális gépek](log-analytics-quick-collect-azurevm.md) a napló Analytics Virtuálisgép-bővítmény használatával 
* Az adatközpontja fizikai kiszolgálóként vagy virtuális gépek
* Virtuális gép egy felhőben üzemeltetett szolgáltatás például az Amazon Web Services (AWS)

A környezetében üzemeltetett számítógépek közvetlenül lehet csatlakoztatni Naplóelemzési, vagy ha már figyel ezeken a számítógépeken, a System Center Operations Manager 2012 R2 2016 vagy 1801-es verzió, a műveletek kezelése felügyeleti csoportban található, integrálhatja Elemzés jelentkezzen, és továbbra is az informatikai szolgáltatás műveletek folyamatok karbantartása.  

## <a name="overview"></a>Áttekintés

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Előtt elemzése és a gyűjtött adatok alapján, először telepítse, és csatlakozzon az ügynököt az összes számítógépet, amelyet szeretne adatokat küldeni a Naplóelemzés szolgáltatás. Ügynökök telepítővel, parancssor, a helyszíni számítógépekre vagy a Szükségeskonfiguráció-konfiguráló (DSC) az Azure Automationben telepíthető. 

Az ügynök a Linux és a Windows 443-as TCP-porton keresztül kommunikál a Log Analytics szolgáltatással kimenő, és ha a számítógép csatlakozik egy tűzfal vagy a proxy-kiszolgálót kommunikálnak az interneten keresztül [Előfeltételek szakaszában](#prerequisites) számára Ismerje meg a hálózati konfiguráció szükséges.  Ha az IT-biztonsági házirendeknek nem engedélyezi a számítógépek az internethez való kapcsolódáshoz a hálózaton, beállíthatja az [OMS átjáró](log-analytics-oms-gateway.md) , majd konfigurálja az ügynök szolgáltatáshoz az átjárón keresztül csatlakozni. Az ügynök ezután fogadni a konfigurációs adatokat, és attól függően, hogy milyen adatok gyűjtési szabályok és a megoldások engedélyezte az összegyűjtött adatok küldése. 

Ha a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2-ben futtató számítógép többhelyű az adatok gyűjtéséhez és a szolgáltatás továbbítja, és továbbra is figyeli a Log Analytics szolgáltatással lehet [Operations Manager ](log-analytics-om-agents.md). Az Operations Manager felügyeleti csoport integrálva van a Naplóelemzési által figyelt Linux rendszerű számítógépek nem kapják konfigurálás adatforrások és a felügyeleti csoporton keresztül továbbítandó összegyűjtött adatokat. A Windows-ügynök is tud jelentéseket legfeljebb négy munkaterületek, míg a Linux-ügynök csak támogatja egyetlen munkaterület jelentéskészítés.  

Az ügynök a Linux és a Windows nem csak a Log Analyticshez való kapcsolódás, Azure Automation állomás a hibrid forgatókönyv-feldolgozói szerepkör és felügyeleti megoldások, például a változások követése és a frissítések kezelése is támogatja.  A hibrid forgatókönyv-feldolgozói szerepkör kapcsolatos további információkért lásd: [Azure Automation hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md).

## <a name="prerequisites"></a>Előfeltételek
Megkezdése előtt tekintse át a következő adatok ellenőrzése a minimális követelményeknek.

### <a name="windows-operating-system"></a>A Windows operációs rendszer
A Windows operációs rendszer következő verzióiban hivatalosan támogatja a Windows-ügynök:

* Windows Server 2008 Service Pack 1 (SP1) vagy újabb verzió
* Windows 7 SP1 és újabb verziók.

> [!NOTE]
> Az ügynök a Windows csak a Transport Layer Security (TLS) 1.0 és 1.1 támogatja.  

#### <a name="network-configuration"></a>Hálózati konfiguráció
Az alábbi lista a proxy és tűzfal konfigurációs adatokat, a Windows-ügynök Naplóelemzési folytatott kommunikációhoz szükséges információt. Akkor kimenő forgalomról beszélünk a hálózatról a Log Analytics szolgáltatásba. 

| Ügynök erőforrása | Portok | HTTPS-ellenőrzés kihagyása|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Igen |
|*.oms.opinsights.azure.com | 443 | Igen | 
|*.blob.core.windows.net | 443 | Igen | 
|*.azure-automation.net | 443 | Igen | 

### <a name="linux-operating-systems"></a>A Linux operációs rendszert
A következő Linux terjesztésekről hivatalosan támogatottak.  A Linux-ügynök azonban más listán nem szereplő terjesztéseket is működni.  Hacsak máshogy nincs jelezve, minden kisebb kiadásokban minden felsorolt főverzió támogatottak.  

* Amazon Linux 2012.09 való 2015.09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)  
* Oracle Linux 5, 6 és 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6, 7 (x86/x64)
* Debian GNU/Linux 8 (x86/x64), 6, 7
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 és 12 (x86/x64)

#### <a name="network-configuration"></a>Hálózati konfiguráció
Az alábbi lista a proxy és tűzfal konfigurációs adatokat, a Linux-ügynök Naplóelemzési folytatott kommunikációhoz szükséges információt.  

|Ügynök erőforrása| Portok | Irány |  
|------|---------|--------|  
|*.ods.opinsights.azure.com | 443-as port | Bejövő és kimenő|  
|*.oms.opinsights.azure.com | 443-as port | Bejövő és kimenő|  
|*.blob.core.windows.net | 443-as port | Bejövő és kimenő|  
|*.azure-automation.net | 443-as port | Bejövő és kimenő|  

A Linux-ügynök támogatja a proxykiszolgáló vagy a HTTPS protokollon keresztül Naplóelemzés szolgáltatás OMS-átjáró keresztül kommunikál.  A névtelen és alapszintű hitelesítés (felhasználónév/jelszó) támogatottak.  A proxy kiszolgáló telepítése során vagy a telepítés után a proxy.conf konfigurációs fájl módosításával adható meg.  

A proxy konfigurációs érték szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Ha a proxykiszolgáló nem igényli a hitelesítésre, a Linux-ügynök továbbra is szükséges pszeudo felhasználói/jelszó megadása. Ez lehet a felhasználónév vagy jelszó.

|Tulajdonság| Leírás |
|--------|-------------|
|Protokoll | https |
|Felhasználó | Nem kötelező felhasználónév a proxyhitelesítéshez |
|jelszó | Opcionális jelszót a proxyhitelesítéshez |
|proxyhost | Cím vagy FQDN-jét a proxy server/OMS átjáró |
|port | A proxy server/OMS átjáró választható portszáma |

Például:`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Ha különleges karaktereket használhatja például a "@" a jelszó, a hibaüzenet proxy kapcsolat mert értéke helytelenül van elemezni.  A probléma megkerüléséhez az URL-cím, egy eszközzel, mint a jelszó kódolása [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Telepítse és konfigurálja az ügynök 
A helyszíni számítógépek kapcsolódásához közvetlenül a Naplóelemzési valósítható meg a követelményeitől függően különböző módszerekkel. A következő táblázat ismerteti az egyes módszerek annak meghatározásához, amely a legjobban a szervezetében.

|Forrás | Módszer | Leírás|
|-------|-------------|-------------|
| Windows rendszerű számítógép|- [Manuális telepítés](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [A verem Azure Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |A Microsoft Monitoring agent telepítése a parancssorból vagy az Azure Automation DSC, például automatizált metódussal [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), vagy ha telepítette a Microsoft Azure Resource Manager-sablonnal Az Azure verem az adatközpontban található.| 
|Linux rendszerű számítógép| [Manuális telepítés](log-analytics-quick-collect-linux-computer.md)|A hívó burkoló-parancsfájl a Githubon található Linux-ügynök telepítése. | 
| System Center Operations Manager|[Az Operations Manager integrálása a Naplóelemzési](log-analytics-om-agents.md) | Operations Manager és a Naplóelemzési továbbítására közötti integráció konfigurálása adatokat gyűjtött Linux és Windows küldő számítógépek hardverhibái is egy felügyeleti csoportban.|  

## <a name="next-steps"></a>További lépések

* Felülvizsgálati [adatforrások](log-analytics-data-sources.md) az elérhető adatokat gyűjteni a Windows vagy Linux rendszer adatforrásokat. 

* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére. 

* További tudnivalók [megoldások](log-analytics-add-solutions.md) , amely Naplóelemzési funkciók hozzáadása, és is gyűjthet adatokat a OMS tárházba.