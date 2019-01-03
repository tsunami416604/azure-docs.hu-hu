---
title: Az operációs rendszer és a futtatókörnyezet javítása kiadása ütemben történik – az Azure App Service-ben |} A Microsoft Docs
description: Ismerteti, hogyan frissítse az Azure App Service-frissítéseket az operációs rendszer és a modulok, és hogyan kezdheti közleményeket.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 576627c96b19dd3563ab21a5d478b779e4a3ed64
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731337"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Az operációs rendszer és a futtatókörnyezet javítása az Azure App Service-ben

Ez a cikk bemutatja, hogyan tehet szert az operációs rendszer vagy a szoftver bizonyos fájlverzió-információkat [App Service-ben](overview.md). 

Az App Service-Platform--szolgáltatásként, ami azt jelenti, hogy az operációs rendszer és az alkalmazást halmozódik az Ön számára; az Azure által felügyelt csak az alkalmazás és az adatok kezeléséhez. Nagyobb mértékű végezheti el az operációs rendszer és alkalmazás stack érhető el, a [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/). Az adott szem előtt hasznos lehet ennek ellenére meg tudni, hogy további információkat, például az App Service-felhasználóként:

-   Hogyan és mikor operációs rendszer frissítései érvényesek?
-   App Service-ben hogyan van javítani (például a nulladik napi) jelentős biztonsági rések ellen?
-   Mely operációs rendszer és a futtatókörnyezet verziója fut az alkalmazások?

Biztonsági okokból a biztonsági adatok meghatározott tulajdonságairól nincs közzétéve. Azonban a cikk célja, hogy által a folyamat a lehető legnagyobb átláthatóság vonatkozik, és hogyan akkor is naprakész maradhat a biztonsági közlemények és a futtatókörnyezet frissítéseket igény kielégítése érdekében.

## <a name="how-and-when-are-os-updates-applied"></a>Hogyan és mikor operációs rendszer frissítései érvényesek?

Az Azure kezeli a fiók alatt két szinttel, a fizikai kiszolgálóknak és a Vendég virtuális gépek (VM), az App Service-erőforrásokat futtató operációs rendszer javításait. Mindkét frissülnek havonta, amelyek igazodnak-e a havi [frissítő kedd](https://technet.microsoft.com/security/bulletins.aspx) ütemezés. Ezeket a frissítéseket a rendszer automatikusan alkalmazza, úgy, hogy garantálja a magas rendelkezésre állású SLA-t az Azure-szolgáltatásokat. 

Hogyan telepítse a frissítéseket a részletes információkért lásd: [az App Service-OS frissítés mögött Magic Quadrant Nyelvszakértőinkből](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hogyan foglalkozik az Azure jelentős biztonsági rések?

Ha súlyos biztonsági réseket írja elő az azonnali javításokat, mint például [nulladik napi biztonsági rések](https://wikipedia.org/wiki/Zero-day_(computing)), a fontos frissítések kezelése – eseti alapon.

Legyen naprakész a kritikus fontosságú biztonsági értesítéseket az Azure-ban funkcionáló [Azure Security Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Amikor támogatott nyelvi futtatókörnyezeteket frissítve, hozzáadva, vagy elavult?

Új stabil verzióiban támogatott nyelvi futtatókörnyezetet (nagyobb, kisebb vagy javítások) rendszeresen kerülnek az App Service-példányt. Egyes frissítések írja felül a meglévő telepítés, míg mások párhuzamosan lesz a meglévő verzió van telepítve. Felülírás telepítés azt jelenti, hogy az alkalmazás automatikusan futtatja a frissített runtime. Egy egymás melletti telepítés azt jelenti, hogy manuálisan át kell telepítenie az alkalmazást, hogy egy új modul verziót. További információkért tekintse meg a struktúrát.

Futásidejű frissítések és kivonások bejelentett itt:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Itt információkat nyelvi futtatókörnyezeteket App Service-alkalmazások beépített vonatkozik. Egy egyéni modult feltölti az App Service-ben például változatlan marad, ha manuálisan frissíti.
>
>

### <a name="new-patch-updates"></a>Új patch-frissítések

Javítás frissítések .NET, PHP, Java SDK-t vagy Tomcat vagy Jetty-verziót, a rendszer automatikusan alkalmazza a meglévő telepítés az új verzió felülírásával. NODE.js-javítás frissítések telepítése párhuzamosan lesz a meglévő verzió (hasonlóan a fő- és alverzió verzió a következő szakaszban). Új Python-javítás verziók keresztül manuálisan is telepíthető [webhelybővítményekkel](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), és a beépített Python telepítését.

### <a name="new-major-and-minor-versions"></a>Új fő- és alverzió verziók

Új nagyobb vagy kisebb verzió hozzáadásakor a létező verziókat párhuzamosan lesz telepítve van. Az alkalmazás manuálisan frissítheti az új verzióra. Ha a futtatókörnyezet verziójának egy konfigurációs fájlban konfigurált (például `web.config` és `package.json`), ugyanazzal a módszerrel frissíteni szeretne. Ha egy App Service-ben használt beállítást konfigurálja a futtatókörnyezet verziója, módosíthatja a a [az Azure portal](https://portal.azure.com) vagy futtatja egy [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) parancsot a [Cloud Shell](../cloud-shell/overview.md), mint a következő példákban látható:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Elavult verzió  

Ha egy régebbi verziója elavult, így, megtervezheti ennek megfelelően a futtatókörnyezet frissítése az Eltávolítás dátuma jelentettük be. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hogyan lehet az operációs rendszer és a futtatókörnyezet frissítési állapot lekérdezése saját példányokon?  

Zárolt operációs rendszer kritikus fontosságú információkhoz le a hozzáférést (lásd: [Azure App Service-ben az operációs rendszer funkcionalitása](operating-system-functionality.md)), a [Kudu konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) lehetővé teszi az App Service-példányhoz az operációs rendszer kapcsolatos lekérdezése verzió és a futtatókörnyezet-verzió. 

A következő táblázatban látható a verziók Windows és a nyelvi futtatókörnyezetet, hogy futnak az alkalmazások hogyan:

| Információ | Hol található | 
|-|-|
| Windows-verzió | Lásd: `https://<appname>.scm.azurewebsites.net/Env.cshtml` (a rendszer-információ) |
| .NET-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, futtassa a következő parancsot a parancssorban: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET core-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, futtassa a következő parancsot a parancssorban: <br> `dotnet --version` |
| PHP-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, futtassa a következő parancsot a parancssorban: <br> `php --version` |
| Alapértelmezett Node.js verzió | Az a [Cloud Shell](../cloud-shell/overview.md), futtassa a következő parancsot: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, futtassa a következő parancsot a parancssorban: <br> `python --version` |  

> [!NOTE]  
> Beállításjegyzék-helyhez való hozzáférés `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, ahol információkat ["KB" javítások](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) tárolódik, zárolva van.
>
>

## <a name="more-resources"></a>További erőforrások

[Adatvédelmi központ: Biztonsági](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bites ASP.NET Core az Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
