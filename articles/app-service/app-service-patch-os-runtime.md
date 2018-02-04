---
title: "Az operációs rendszer és a futásidejű javítás az Azure App Service szolgáltatásban |} Microsoft Docs"
description: "Ismerteti, hogyan frissítse az Azure App Service-frissítéseket az operációs rendszer és a futtatókörnyezetek és a válaszok közleményeket."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.openlocfilehash: 869bd0e3f684ff4a2291e189cf247daedfb74922
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Az operációs rendszer és a futásidejű javítás az Azure App Service-ben

A cikkből megtudhatja, hogyan kérhet az operációs rendszer vagy a szoftver bizonyos fájlverzió-információkat [App Service](app-service-web-overview.md). 

App Service egy Platform,--szolgáltatás, amely azt jelenti, hogy az operációs rendszer és a verem alkalmazás kezeli az Ön Azure; csak kezelheti az alkalmazás és a hozzá kapcsolódó adatokat. Nagyobb mértékben vezérelheti az operációs rendszer és az alkalmazás verem érhető el a [Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/). Vele szem előtt akkor célszerű azonban az Ön App Service-felhasználóként tudni, hogy további információkat, például:

-   Hogyan és mikor operációs rendszer frissítése érdekében vonatkoznak?
-   App Service hogyan van telepítve (például nulla napos) jelentős biztonsági rések elleni?
-   Mely operációs rendszer és a futtatókörnyezet verziója fut az alkalmazások?

Biztonsági okokból a biztonsági adatokat az egyes tulajdonságait nincs közzétéve. Azonban a cikk célja, hogy enyhítse által a folyamat átláthatóságának maximalizálva vonatkozik, és hogyan maradhat, ha a biztonsági közlemények, illetve runtime frissítési naprakész.

## <a name="how-and-when-are-os-updates-applied"></a>Hogyan és mikor operációs rendszer frissítése érdekében vonatkoznak?

Azure kezeli, az operációs rendszer két, a fizikai kiszolgálók és a Vendég virtuális gépek (VM), az App Service-erőforrások futtató javítását. Mindkét frissülnek, havonta, amely illeszkedik a havi [frissítő kedd](https://technet.microsoft.com/security/bulletins.aspx) ütemezést. A frissítések automatikusan, alkalmazását úgy, hogy a magas rendelkezésre állású SLA-t az Azure-szolgáltatásokat biztosítja. 

Részletes információ a frissítések alkalmazásának módját: [mögött App Service operációs rendszer frissítése érdekében magic Demystifying](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hogyan foglalkozik Azure jelentős biztonsági rések?

Ha súlyos biztonsági réseket írja elő az azonnali javítását, például a [nulla napos biztonsági rések](https://wikipedia.org/wiki/Zero-day_(computing)), a fontos frissítések-eseti alapon történik.

Látogasson el a kritikus fontosságú biztonsági közlemények az Azure-ban naprakész [Azure biztonsági Blog](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Ha támogatott nyelvi futtatókörnyezetek frissítve, hozzáadásakor, vagy elavult?

Új stabil verzióiban támogatott nyelvi futtatókörnyezetek (nagyobb, kisebb vagy javítás) rendszeres időközönként kerülnek be az App Service-példány. Egyes frissítések felülírja a meglévő telepítés, míg mások és meglévő verziók telepítve vannak. Egy felülírása telepítés azt jelenti, hogy a frissített Runtime automatikusan futtatja az alkalmazást. Egy egymás melletti telepítés azt jelenti, hogy manuálisan kell áttelepíteni az alkalmazás egy új verziójú futásidejű kihasználásához. További információkért tekintse meg a alszakaszokat egyikét.

Futásidejű frissítések és deprecations bejelentette itt:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Itt információkat az App Service alkalmazásba beépített nyelvi futtatókörnyezetek vonatkozik. Az App Service-be feltölteni egy egyéni futásidejű például változatlan marad Ha manuálisan frissíti.
>
>

### <a name="new-patch-updates"></a>Új javítás frissítések

Javítás frissítéseket .NET, PHP, Java SDK vagy Tomcat vagy Jetty-verziót alkalmazza automatikusan felülírja a meglévő telepítés az új verzióval. NODE.js javítás frissítések telepítése és a meglévő verzió (a következő szakaszban fő- és alverzió verzióihoz hasonlóan). Új Python javítás verziók a manuálisan telepíthető [bővítmények hely](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)), a beépített Python telepített párhuzamos.

### <a name="new-major-and-minor-versions"></a>Új fő- és alverzió verziók

Amikor a fő vagy új verzió ad hozzá, és a létező verziókat telepítették. Manuálisan frissítheti az alkalmazást az új verzióra. Ha a konfigurációs fájlban konfigurálni a futásidejű verzióját (például `web.config` és `package.json`), frissítenie kell ugyanazzal a módszerrel. Ha egy App Service beállítás konfigurálása a futásidejű verzióját, módosíthatja a a [Azure-portálon](https://portal.azure.com) vagy futtatásával egy [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) parancsot a [felhő rendszerhéj](../cloud-shell/overview.md), mint a következő példákban látható:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Elavult verzió

Ha egy régebbi verziója elavult, a profileltávolítási dátum elavulásának bejelentéséig, hogy a futtatókörnyezet verziófrissítés megtervezheti ennek megfelelően. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hogyan tudja lekérdezni a példányokon az operációs rendszer és futásidejű frissítés állapota?

Zárolt kritikus operációs rendszer adatainak le a hozzáférési (lásd: [operációs rendszer működőképességét Azure App Service](web-sites-available-operating-system-functionality.md)), a [Kudu konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) lehetővé teszi a App Service-példány kapcsolatban az operációs rendszer lekérdezése futásidejű verziójához és. 

Az alábbi táblázat az alkalmazásokat futtatja verziók a Windows és a nyelvi futtatókörnyezet módjáról:

| Információ | Hol található az |
|-|-|
| Windows-verzió | Lásd: `https://<appname>.scm.azurewebsites.net/Env.cshtml` (a rendszeradatok) |
| .NET-verziója | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, a következő parancsot a parancssorba: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| A .NET core verziója | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, a következő parancsot a parancssorba: <br> `dotnet --version` |
| PHP-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, a következő parancsot a parancssorba: <br> `php --version` |
| Alapértelmezett Node.js verziót | Az a [felhő rendszerhéj](../cloud-shell/overview.md), a következő parancsot: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`, a következő parancsot a parancssorba: <br> `python --version` |

> [!NOTE]
> Beállításjegyzék-helyhez való hozzáférés `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`, ahol információkat ["KB" javítások]((https://technet.microsoft.com/security/bulletins.aspx)) található, zárolva van.
>
>

## <a name="more-resources"></a>További erőforrások

[Biztonsági és adatkezelési központ: biztonsági](https://www.microsoft.com/TrustCenter/Security/default.aspx)  
[64 bites ASP.NET Core Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
