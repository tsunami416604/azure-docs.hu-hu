---
title: Telepítse az alkalmazást az Azure App Service FTP/S használatával |} Microsoft Docs
description: Megtudhatja, hogyan telepítse az alkalmazást az Azure App Service FTP vagy ftps-t használ.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariac
ms.openlocfilehash: 2ec08b45fab9987e9271c1ff3101eaf321dc84be
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234223"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Telepítse az alkalmazást az Azure App Service segítségével FTP/S

Ez a cikk bemutatja, hogyan FTP vagy ftps-t használja a webalkalmazást, mobil-háttéralkalmazás és API-alkalmazás telepítési [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Az FTP/S-végpont az alkalmazás már aktív. FTP/S telepítési ahhoz szükséges, hogy nem igényel konfigurálást.

## <a name="open-ftp-dashboard"></a>Nyissa meg FTP irányítópult

Az a [Azure-portálon](https://portal.azure.com), nyissa meg az alkalmazás [erőforrás oldala](../azure-resource-manager/resource-group-portal.md#manage-resources).

Az FTP-irányítópult megnyitásához kattintson **folyamatos kézbesítési (előzetes verzió)** > **FTP** > **irányítópult**.

![Nyissa meg FTP irányítópult](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP-kiszolgáló kapcsolati adatainak lekérése

Az FTP-irányítópulton kattintson **másolási** másolása a FTPS végpont és az alkalmazás hitelesítő adatait.

![FTP-adatok másolása](./media/app-service-deploy-ftp/ftp-dashboard.png)

Javasoljuk, hogy használjon **App hitelesítő adatok** való telepítése az alkalmazásban, mert minden alkalmazáshoz egyedi. Azonban ha **felhasználói hitelesítő adatok**, beállíthatja a felhasználói szintű hitelesítő adatokat, amelyek az előfizetésében szereplő összes App Service apps FTP/S-bejelentkezési használható.

## <a name="deploy-files-to-azure"></a>Fájlok telepítése az Azure-bA

1. Az FTP-ügyfél (például [Visual Studio](https://www.visualstudio.com/vs/community/) vagy [FileZilla](https://filezilla-project.org/download.php?type=client)), a kapcsolat az alkalmazás kapcsolódni összegyűjtött információkat felhasználva.
3. Másolja a fájlokat és a megfelelő könyvtárstruktúrát a [ **/hely/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) az Azure-ban (vagy a **/hely/wwwroot/App_Data/feladatok/** könyvtár a webjobs-feladatok).
4. Tallózással keresse meg az alkalmazás URL-CÍMÉT, ellenőrizze az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> Eltérően [Git-alapú telepítések](app-service-deploy-local-git.md), FTP-telepítés nem támogatja a következő központi telepítés automatizálása: 
>
> - a függőségi visszaállítja (például a NuGet, NPM, PIP és szerkesztő automatizálása)
> - a .NET bináris fájl fordítás
> - a Web.config fájl. generációs (itt egy [Node.js példa](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Létre manuálisan a helyi számítógépen a szükséges fájlokat, és telepítheti azokat az alkalmazás együtt.
>
>

## <a name="enforce-ftps"></a>FTPS kényszerítése

A fokozott biztonság érdekében engedélyezze FTP SSL-en keresztül csak. Ha nem adja meg FTP telepítési FTP és a ftps-t is letilthatók.

Az alkalmazás erőforrás lapján [Azure-portálon](https://portal.azure.com), jelölje be **Alkalmazásbeállítások** a bal oldali navigációs.

Titkosítatlan FTP letiltásához válassza **FTPS csak**. FTP és a ftps-t teljesen letiltásához válassza **letiltása**. Ha befejezte, kattintson a **mentése**.

![Tiltsa le az FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

FTP-telepítés a [Azure CLI](/cli/azure), lásd: [webalkalmazás létrehozása és központi telepítése FTP (Azure CLI) fájlok](./scripts/app-service-cli-deploy-ftp.md).

FTP-telepítés a [Azure PowerShell](/cli/azure), lásd: [fájlok feltöltése FTP (PowerShell) segítségével webes alkalmazás](./scripts/app-service-powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP telepítési hibáinak elhárítása

- [Hogyan háríthatom FTP telepítési?](#how-can-i-troubleshoot-ftp-deployment)
- [I nem lehet FTP, és tegye közzé a saját kód. Hogyan lehet megoldani a problémát?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan lehet kapcsolódni az Azure App Service FTP passzív módra keresztül?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan háríthatom FTP telepítési?

Az első lépés FTP üzembe helyezés hibaelhárítása van elkülönítése a egy futásidejű alkalmazásproblémák egy központi telepítési probléma.

Egy központi telepítési probléma általában a fájlok vagy hibás fájlok, az alkalmazás központi telepítése nem eredményez. Elháríthatja a FTP telepítési vizsgál, vagy jelöljön ki egy alternatív telepítési elérési utat (például a verziókövetési rendszerrel).

A futásidejű alkalmazásproblémák általában eredményez a az alkalmazás, de nem megfelelő alkalmazások viselkedése telepített fájlok megfelelő halmazát. Kód viselkedés futásidőben összpontosít, és vizsgálja a konkrét hiba elérési utak elhárításához.

A központi telepítés, illetve runtime jelöléssel probléma megállapításához lásd: [futásidejű problémák és telepítési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I nem lehet FTP, és tegye közzé a saját kód. Hogyan lehet megoldani a problémát?
Ellenőrizze, hogy helyesen adta a megfelelő állomásnév és [hitelesítő adatok](#step-1--set-deployment-credentials). Ellenőrizze azt is, hogy a számítógépen a következő FTP-portok nem blokkolja tűzfal:

- FTP-vezérlőkapcsolati port: 21
- FTP-adatok csatlakozási portja: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan lehet kapcsolódni az Azure App Service FTP passzív módra keresztül?
Az Azure App Service támogatja az aktív és passzív módú keresztül kapcsolódik. Passzív módú használata ajánlott, mivel a központi telepítés gépek általában (az operációs rendszer vagy üzleti hálózati vagy otthoni részeként) tűzfal mögött található. Lásd: egy [példa a WinSCP dokumentációs](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>További lépések

Összetettebb központi telepítési forgatókönyve esetén próbálja [üzembe helyezése az Azure git](app-service-deploy-local-git.md). Az Azure Git-alapú telepítést lehetővé teszi a verziókövetés, a csomag visszaállítás, az MSBuild és több.

## <a name="more-resources"></a>További források

* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deploy-ftp.md)
