---
title: Hibakeresési alkalmazásproxy-összekötők – Azure Active Directory | Microsoft dokumentumok
description: Hibakeresési problémák az Azure Active Directory (Azure AD) alkalmazásproxy-összekötőkkel.
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311849"
---
# <a name="debug-application-proxy-connector-issues"></a>Alkalmazásproxy-összekötők problémáinak hibakeresése 

Ez a cikk segít az Azure Active Directory (Azure AD) alkalmazásproxy-összekötőivel kapcsolatos problémák elhárításában. Ha az alkalmazásproxy-szolgáltatást használja egy helyszíni webalkalmazás távoli eléréséhez, de nem sikerül csatlakoznia az alkalmazáshoz, használja ezt a folyamatábrát az összekötőproblémák hibakereséséhez. 

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy telepítette az alkalmazásproxy-összekötőt, és probléma van. Az alkalmazásproxyval kapcsolatos problémák elhárításakor azt javasoljuk, hogy kezdje el ezt a hibaelhárítási folyamatot annak megállapítására, hogy az alkalmazásproxy-összekötők megfelelően vannak-e konfigurálva. Ha továbbra is problémái vannak az alkalmazáshoz való csatlakozással, kövesse a [Hibakeresési alkalmazásproxy alkalmazásokkal kapcsolatos problémák](application-proxy-debug-apps.md)hibaelhárítási folyamatát.  


Az alkalmazásproxyról és az összekötők használatáról a következő témakörökben talál további információt:

- [Távoli hozzáférés a helyszíni alkalmazásokhoz alkalmazásproxyn keresztül](application-proxy.md)
- [Alkalmazásproxy-összekötők](application-proxy-connectors.md)
- [Összekötő telepítése és regisztrálása](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Folyamatábra összekötőproblémákhoz

Ez a folyamatábra végigvezeti a leggyakoribb összekötőproblémák hibakeresésének lépésein. Az egyes lépésekről a folyamatábrát követő táblázatban talál részleteket.

![Folyamatábra az összekötő hibakeresésének lépéseit mutatja be](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Műveletek | Leírás | 
|---------|---------|---------|
|1 | Az alkalmazáshoz rendelt összekötőcsoport megkeresése | Valószínűleg több kiszolgálóra telepített összekötővel rendelkezik, ebben az esetben az összekötőket [összekötőcsoportokhoz](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)kell rendelni. Az összekötőcsoportokról az [Alkalmazások közzététele külön hálózatokon és helyeken összekötőcsoportok használatával című](application-proxy-connector-groups.md)témakörben olvashat bővebben. |
|2 | Az összekötő telepítése és csoport hozzárendelése | Ha nincs telepítve összekötő, olvassa el [az Összekötő telepítése és regisztrálása című témakört.](application-proxy-add-on-premises-application.md#install-and-register-a-connector)<br></br> Ha problémái vannak az összekötő telepítésével, olvassa el a Probléma az összekötő telepítése korlátja című [témakört.](application-proxy-connector-installation-problem.md)<br></br> Ha az összekötő nincs hozzárendelve egy csoporthoz, olvassa el [az Összekötő hozzárendelése csoporthoz](application-proxy-connector-groups.md#create-connector-groups)című témakört.<br></br>Ha az alkalmazás nincs hozzárendelve egy összekötő csoporthoz, olvassa el [az Alkalmazás hozzárendelése egy összekötőcsoporthoz című témakört.](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)|
|3 | Portteszt futtatása az összekötő kiszolgálón | Az összekötő kiszolgálón futtasson porttesztet [a telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) vagy más portvizsgáló eszközzel annak ellenőrzésére, hogy a 443-as és a 80-as port nyitva van-e.|
|4 | Tartományok és portok konfigurálása | [Ellenőrizze, hogy a tartományok és a portok megfelelően vannak-e konfigurálva](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Az összekötő megfelelő működéséhez vannak bizonyos portok, amelyeknek nyitva kell lenniük, és a kiszolgáló által elérhető URL-címeknek is elérhetőnek kell lenniük. |
|5 | Annak ellenőrzése, hogy van-e használatban háttérproxy | Ellenőrizze, hogy az összekötők háttérproxy-kiszolgálókat használnak-e, vagy megkerülik-e azokat. További információt az [összekötőproxy-problémák és a szolgáltatáscsatlakozási problémák elhárítása című témakörben talál.](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues) |
|6 | Az összekötő és a frissítő frissítése a háttérproxy használatához | Ha háttérproxy van használatban, győződjön meg arról, hogy az összekötő ugyanazt a proxyt használja. Az összekötők proxykiszolgálókkal való működésének hibaelhárításáról és konfigurálásáról a [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)című témakörben talál. |
|7 | Az alkalmazás belső URL-címének betöltése az összekötő kiszolgálón | Az összekötő kiszolgálón töltse be az alkalmazás belső URL-címét. |
|8 | Belső hálózati kapcsolat ellenőrzése | Van egy kapcsolódási probléma a belső hálózaton, amelyet ez a hibakeresési folyamat nem tud diagnosztizálni. Az alkalmazásnak belsőleg hozzáférhetőnek kell lennie ahhoz, hogy az összekötők működjenek. Az összekötő eseménynaplóit az [Alkalmazásproxy-összekötők leírtak](application-proxy-connectors.md#under-the-hood)szerint engedélyezheti és megtekintheti. |
|9 | A háttéridő-túltöltési érték meghosszabbítása | Az alkalmazás **további beállításai** párbeszédpanelen módosítsa a **háttéralkalmazás időkimenő beállítását** **Hosszú**ra. Lásd: [Helyszíni alkalmazás hozzáadása az Azure AD-hez.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) |
|10 | Ha a problémák továbbra is fennállnak, célzott konkrét folyamatproblémák, tekintse át az alkalmazást és az SSO-hibakeresési folyamatokat | Használja a [Debug Application Proxy alkalmazás hibaelhárítási](application-proxy-debug-apps.md) folyamat. |

## <a name="next-steps"></a>További lépések


* [Alkalmazások közzététele különböző hálózatokon és helyeken összekötőcsoportok használatával](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Alkalmazásproxyval és csatlakozóval kapcsolatos hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
