---
title: Operációs rendszer és futásidejű javítási ütem
description: Megtudhatja, hogy az Azure App Service hogyan frissíti az operációs rendszert és a futásidőket, milyen futásidőkkel és javítási szintekkel rendelkezik az alkalmazások, és hogyan kaphat frissítési bejelentéseket.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18
ms.openlocfilehash: 597964914f4022899ab027b735ec6932105497b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273640"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Operációs rendszer és futásidejű javítás az Azure App Service-ben

Ez a cikk bemutatja, hogyan szerezhet be bizonyos verzióinformációkat az operációs rendszerrel vagy szoftverrel kapcsolatban az [App Service szolgáltatásban.](overview.md) 

Az App Service egy platform-szolgáltatásként, ami azt jelenti, hogy az operációs rendszer és az alkalmazásverem az Azure által felügyelt; csak az alkalmazást és annak adatait kezelheti. Az operációs rendszer és az alkalmazásverem hatékonyabbá válik az [Azure virtuális gépekben.](https://docs.microsoft.com/azure/virtual-machines/) Ezt szem előtt tartva azonban hasznos, ha Az App Service-felhasználók több információt tudnak meg, például:

-   Hogyan és mikor alkalmazzák az operációs rendszer frissítéseit?
-   Hogyan javítják az App Service-t jelentős (például nulladik napi) biztonsági rések ellen?
-   Mely operációs rendszer- és futásidejű verziók futtatják az alkalmazásokat?

Biztonsági okokból a biztonsági információk bizonyos sajátosságait nem tesszük közzé. A cikk célja azonban az aggodalmak enyhítése a folyamat átláthatóságának maximalizálásával, valamint azzal, hogy miként őrizheti meg a biztonsággal kapcsolatos bejelentésekkel vagy futásidejű frissítésekkel kapcsolatos naprakészségeket.

## <a name="how-and-when-are-os-updates-applied"></a>Hogyan és mikor alkalmazzák az operációs rendszer frissítéseit?

Az Azure kezeli az operációs rendszer javítását két szinten, a fizikai kiszolgálók és a vendég virtuális gépek (VM-ek), amelyek az App Service-erőforrásokat futtatják. Mindkettő havonta frissül, amely igazodik a havi [Patch kedd](https://technet.microsoft.com/security/bulletins.aspx) menetrend. Ezek a frissítések automatikusan érvénybe lépnek, oly módon, hogy garantálják az Azure-szolgáltatások magas rendelkezésre állású SLA-ját. 

A frissítések alkalmazásával kapcsolatos részletes információkért [lásd: Az App Service operációs rendszer frissítéseinek varázsának demisztifikája.](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html)

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Hogyan kezeli az Azure a jelentős biztonsági réseket?

Ha a súlyos biztonsági rések azonnali javítást igényelnek, például [a nulladik napi biztonsági réseket,](https://wikipedia.org/wiki/Zero-day_(computing))a magas prioritású frissítéseket eseti alapon kezeljük.

Az Azure biztonsági [blogján](https://azure.microsoft.com/blog/topics/security/)a kritikus biztonsági bejelentésekkel naprakész maradhat. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Mikor frissülnek, vesznek fel vagy elavultak a támogatott nyelvi futásidők?

A támogatott nyelvi futásidők (fő, kisebb vagy javítás) új, stabil verziói rendszeres időközönként hozzáadódnak az App Service-példányokhoz. Egyes frissítések felülírják a meglévő telepítést, míg mások a meglévő verziókkal együtt vannak telepítve. A felülírási telepítés azt jelenti, hogy az alkalmazás automatikusan fut a frissített futásidőben. Az egymás melletti telepítés azt jelenti, hogy manuálisan kell áttelepítenie az alkalmazást az új futásidejű verzió előnyeinek kihasználásához. További információt az egyik alszakaszban talál.

A futásidejű frissítéseket és akadásokat itt jelentik meg:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Az itt megadott információk az App Service-alkalmazásba beépített nyelvi futásidőkre vonatkoznak. Az App Service-be feltöltött egyéni futásidejű például változatlan marad, hacsak manuálisan nem frissíti azt.
>
>

### <a name="new-patch-updates"></a>Új javításfrissítések

A .NET, PHP, Java SDK vagy Tomcat/Jetty verzió javítófrissítései automatikusan érvénybe lépnek a meglévő telepítés és az új verzió felülírásával. A Node.js javítások frissítései a meglévő verziókkal együtt vannak telepítve (hasonlóan a következő szakasz fő- és alverzióihoz). Az új Python-javítások manuálisan [telepíthetők a webhelybővítményeken](https://azure.microsoft.com/blog/azure-web-sites-extensions/)keresztül, a beépített Python-telepítésekkel együtt.

### <a name="new-major-and-minor-versions"></a>Új fő- és alverziók

Új fő- vagy alverzió hozzáadásakor a rendszer a meglévő verziókkal együtt telepíti. Manuálisan frissítheti az alkalmazást az új verzióra. Ha konfigurálta a futásidejű verziót `web.config` egy `package.json`konfigurációs fájlban (például és a) ugyanazzal a módszerrel kell frissítenie. Ha egy App Service-beállítást használt a futásidejű verzió konfigurálásához, módosíthatja azt az [Azure Portalon](https://portal.azure.com) vagy egy [Azure CLI-parancs](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) futtatásával a [Cloud Shellben,](../cloud-shell/overview.md)ahogy az a következő példákban látható:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Elavult verziók  

Ha egy régebbi verzió elavult, az eltávolítási dátum be van jelentve, így a futásidejű verziófrissítés ennek megfelelően tervezhető. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Hogyan kérdezhetem le az operációs rendszert és a futásidejű frissítés állapotát a példányaimon?  

Míg a kritikus operációsrendszer-információk le vannak zárva a hozzáférésből (lásd: [Az Operációs rendszer funkciói az Azure App Service-ben),](operating-system-functionality.md)a [Kudu konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console) lehetővé teszi az App Service-példány lekérdezését az operációs rendszer verzióiés futásidejű verziói tekintetében. 

Az alábbi táblázat bemutatja, hogyan használható a Windows verziói és az alkalmazásokat futtató nyelvi futtatókörnyezet:

| Információ | Hol található meg? | 
|-|-|
| Windows-verzió | Lásd `https://<appname>.scm.azurewebsites.net/Env.cshtml` (a Rendszerinformáció csoportban) |
| .NET verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`alkalmazásban futtassa a következő parancsot a parancssorban: <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET Core verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`alkalmazásban futtassa a következő parancsot a parancssorban: <br> `dotnet --version` |
| PHP verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`alkalmazásban futtassa a következő parancsot a parancssorban: <br> `php --version` |
| Alapértelmezett Node.js verzió | A [Felhőrendszerhéjban](../cloud-shell/overview.md)futtassa a következő parancsot: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python-verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`alkalmazásban futtassa a következő parancsot a parancssorban: <br> `python --version` |  
| Java verzió | A `https://<appname>.scm.azurewebsites.net/DebugConsole`alkalmazásban futtassa a következő parancsot a parancssorban: <br> `java -version` |  

> [!NOTE]  
> A rendszerleíró `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`adatbázis helyéhez való hozzáférés, ahol a ["KB" javításokon](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins) tárolt adatok tárolódnak, zárolva van.
>
>

## <a name="more-resources"></a>További erőforrások

[Adatvédelmi központ: Biztonság](https://www.microsoft.com/en-us/trustcenter/security)  
[64 bites ASP.NET Core az Azure App Service szolgáltatásban](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
