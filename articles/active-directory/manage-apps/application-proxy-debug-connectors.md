---
title: Az alkalmazásproxy-összekötők – Azure Active Directory hibakeresése |} A Microsoft Docs
description: Hibakeresés az Azure Active Directory (Azure AD) alkalmazásproxy-összekötők.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172235"
---
# <a name="debug-application-proxy-connector-issues"></a>Application Proxy connector kapcsolatos problémák megoldásában 

Ez a cikk segít az Azure Active Directory (Azure AD) alkalmazásproxy-összekötők hibáinak elhárítása. Ha egy helyszíni webes alkalmazásba való távoli hozzáférés az Application Proxy szolgáltatást használja, de problémába ütközik az alkalmazás csatlakozik, ez a folyamatábra használatával összekötő kapcsolatos problémák megoldásában. 

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az alkalmazásproxy-összekötő telepítését és a egy problémát, amelynek. Az alkalmazásproxy-problémák hibaelhárításához, javasoljuk, hogy először határozza meg, ha az alkalmazásproxy-összekötők megfelelően vannak-e konfigurálva, a hibaelhárítási folyamat a. Ha még nem lehet csatlakozni az alkalmazást, kövesse a hibaelhárítási folyamat [hibakeresése az alkalmazásproxy-alkalmazások hibáinak](application-proxy-debug-apps.md).  


Proxy és az összekötők használatával kapcsolatos további információkért lásd:

- [Alkalmazásproxyn keresztül a helyszíni alkalmazások távoli elérését](application-proxy.md)
- [Alkalmazásproxy-összekötők](application-proxy-connectors.md)
- [Telepítés és a egy összekötő regisztrálása](application-proxy-add-on-premises-application.md)
- [Az alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Összekötő problémák folyamatábrája

Ez a folyamatábra végigvezeti az összekötő gyakori problémák a hibakereséshez. Tekintse meg az a folyamatábra szemlélteti a következő táblázat az egyes lépések kapcsolatos további információk.

![Egy összekötő hibakeresési lépéseket bemutató folyamatábra](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Műveletek | Leírás | 
|---------|---------|---------|
|1 | Keresse meg az alkalmazáshoz hozzárendel az összekötőcsoport | Valószínűleg több kiszolgálón telepített egy összekötőt, ebben az esetben az összekötők kell [összekötőcsoportok rendelt](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Összekötőcsoportok kapcsolatos további információkért lásd: [külön hálózatok és helyek összekötőcsoportok használatával az alkalmazások közzététele](application-proxy-connector-groups.md). |
|2 | Az összekötő telepítése és hozzárendelése egy csoporthoz | Ha nincs telepítve egy összekötőt, [telepítése és regisztrálása egy összekötő](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Ha az összekötő a csoporthoz nincs hozzárendelve, [az összekötő hozzárendelése egy csoporthoz](application-proxy-connector-groups.md#create-connector-groups).<br></br>Ha az alkalmazás nincs hozzárendelve egy összekötőcsoporthoz, [rendelje hozzá az alkalmazás egy összekötőcsoporthoz](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Az összekötő kiszolgáló-port teszt futtatása | Az összekötő-kiszolgálón futtassa a port tesztet használatával [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) vagy más port tesztelés eszközzel ellenőrizheti, ha a 443-as és a 80-as portok nyitva-e.|
|4 | Portok és tartományok konfigurálása | [Győződjön meg arról, hogy a tartományok és a portok megfelelően vannak konfigurálva,](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) megfelelően működjön, az összekötő vannak egyes portok nyitva kell lennie, és az URL-címeket, a kiszolgáló eléréséhez képesnek kell lennie. |
|5 | Ellenőrizze, hogy egy háttér-proxy van használatban | Ellenőrizze, hogy ha az összekötők használatával háttér-proxy kiszolgálók vagy kihagyásával őket. További információkért lásd: [összekötő proxy kapcsolatos problémák elhárítása és a szolgáltatás kapcsolódási problémák](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Az összekötő és a háttér-proxy használatára updater frissítése | Ha egy háttér-proxy van használatban, érdemes, hogy az összekötő ugyanarra a proxyra használ. További hibaelhárítási, és dolgozhat a proxykiszolgálók összekötők konfigurálásával kapcsolatos további információkért lásd: [együttműködnek a meglévő helyszíni proxykiszolgálók](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Az alkalmazás belső URL-címet az összekötő kiszolgáló betöltése | Az összekötő-kiszolgálón betölteni az alkalmazás belső URL-CÍMÉT. |
|8 | Belső hálózati kapcsolat ellenőrzése | Kapcsolódási probléma van a hibakeresési folyamat nem tudja diagnosztizálhatja a belső hálózaton. Az alkalmazás működéséhez az összekötők a belső használatú elérhetőnek kell lennie. Engedélyezheti és összekötő eseménynaplók megtekintésére, leírtak szerint [alkalmazásproxy-összekötők](application-proxy-connectors.md#under-the-hood). |
|9 | Növelje meg az időtúllépés értékének a háttér | A a **további beállítás** az alkalmazás módosítása a **háttéralkalmazás túllépte az időkorlátot** beállítást **hosszú**. Lásd: [helyszíni alkalmazás hozzáadása az Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Ha problémák továbbra is fennállnak, célként adott folyamathoz problémák, a felülvizsgálat-alkalmazás és a hibakeresés folyamatok egyszeri bejelentkezés | Használja a [hibakeresése az alkalmazásproxy-alkalmazások hibáinak](application-proxy-debug-apps.md) folyamat hibaelhárítása. |

## <a name="next-steps"></a>További lépések


* [Külön hálózatok és helyek összekötőcsoportok használatával az alkalmazások közzététele](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Proxy és az összekötő kapcsolatos hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
