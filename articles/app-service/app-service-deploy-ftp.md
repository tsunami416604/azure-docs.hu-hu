---
title: Alkalmazás üzembe helyezése az Azure App Service-ben FTP/S használatával |} A Microsoft Docs
description: Megtudhatja, hogyan helyezze üzembe az alkalmazást az Azure App Service-ben FTP- vagy FTPS használatával.
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
ms.openlocfilehash: f82a01f3a2b88e66988717eb008a65c8f2aecf28
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900325"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Alkalmazás üzembe helyezése az Azure App Service-be FTP/S

Ez a cikk bemutatja, hogyan a web Apps, mobil-háttéralkalmazás vagy API-alkalmazás üzembe helyezése FTP- vagy FTPS használatával [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Az alkalmazás FTP/S végpontja már aktív. Semmilyen beállítást nem kell FTP/S üzembe helyezési engedélyezéséhez szükségesek.

## <a name="open-ftp-dashboard"></a>FTP-irányítópult megnyitása

Az a [az Azure portal](https://portal.azure.com), nyissa meg az alkalmazás [erőforráslapján](../azure-resource-manager/resource-group-portal.md#manage-resources).

Az FTP-irányítópult megnyitásához kattintson a **folyamatos Készregyártás (előzetes verzió)** > **FTP** > **irányítópult**.

![FTP-irányítópult megnyitása](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP-kapcsolati adatok lekéréséhez

Az FTP-irányítópulton kattintson **másolási** másolása a FTPS végpontok és hitelesítő adatokat.

![FTP-adatok másolása](./media/app-service-deploy-ftp/ftp-dashboard.png)

Javasoljuk, hogy használja **alkalmazás hitelesítő adatok** , mivel minden alkalmazáshoz egyedi helyezze üzembe az alkalmazásban. Azonban ha **felhasználói hitelesítő adatok**, beállíthatja a felhasználói szintű hitelesítő adatokat, az előfizetés összes App Service-alkalmazáshoz FTP/S bejelentkezéshez használható.

## <a name="deploy-files-to-azure"></a>Az Azure files üzembe helyezése

1. Az FTP-ügyfél (például [Visual Studio](https://www.visualstudio.com/vs/community/) vagy [Filezillát](https://filezilla-project.org/download.php?type=client)), az alkalmazás csatlakozni összegyűjtött kapcsolati információk.
3. Másolja a fájlokat és a megfelelő könyvtárstruktúrát a [ **/site/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) az Azure-ban (vagy a **/site/wwwroot/App_Data/feladatok/** könyvtárat a webjobs-feladatokat).
4. Keresse meg az alkalmazás URL-címe, ellenőrizze az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> Ellentétben [Git-alapú telepítések](app-service-deploy-local-git.md), FTP telepítési nem támogatja a következő központi telepítés automatizálását: 
>
> - függőség (például a NuGet, NPM, PIP és Composer automatizálását) visszaállítása
> - .NET bináris fájlok összeállítása
> - web.config generációja (Íme egy [Node.js példa](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Hozhatja létre ezeket a szükséges fájlokat manuálisan a helyi gépén, majd telepítheti őket az alkalmazás együtt.
>
>

## <a name="enforce-ftps"></a>FTPS kényszerítése

A fokozott biztonság érdekében engedélyezze az FTP SSL-en keresztül csak. Ha nem használja a FTP telepítési FTP és a FTPS is letilthatók.

Az alkalmazás erőforrások oldalának a [az Azure portal](https://portal.azure.com)válassza **Alkalmazásbeállítások** a bal oldali navigációs.

Titkosítatlan FTP letiltásához válassza **FTPS csak**. Az FTP és a FTPS teljes letiltásához válassza **letiltása**. Amikor végzett, kattintson a **Mentés** gombra. Ha használ **FTPS csak** ki kell kényszerítenie a TLS 1.1-es vagy magasabb, az a **SSL-beállítások** az web App. Nem támogatja a TLS 1.0 **FTPS csak**.

![Tiltsa le az FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

FTP-telepítés a [Azure CLI-vel](/cli/azure), lásd: [webes alkalmazás létrehozása és üzembe helyezése FTP használatával (Azure CLI) fájlok](./scripts/app-service-cli-deploy-ftp.md).

FTP-telepítés a [Azure PowerShell-lel](/cli/azure), lásd: [fájlok feltöltése webalkalmazásba FTP (PowerShell) használatával](./scripts/app-service-powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP telepítési hibáinak elhárítása

- [Hogyan háríthatom el az FTP telepítési?](#how-can-i-troubleshoot-ftp-deployment)
- [E nem lehet FTP, és tegye közzé a saját kódját. Hogyan lehet megoldani a problémát?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan csatlakoztathatom a az Azure App Service-ben FTP passzív mód keresztül?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan háríthatom el az FTP telepítési?

FTP-üzembehelyezési hibaelhárítási az első lépés egy alkalmazás futtatókörnyezeti probléma az üzembehelyezési probléma van elkülönítés.

Üzembehelyezési probléma általában a fájlok vagy az alkalmazásra alkalmazott megfelelő fájlok nem eredményez. Az FTP-üzembehelyezési kivizsgálása vagy egy alternatív telepítési elérési utat (például a verziókövetés) kiválasztásával elhárítását.

Egy alkalmazás futtatókörnyezeti probléma általában eredményez az alkalmazás, de nem megfelelő alkalmazások viselkedésének telepített megfelelő beállítását. A kód működését futásidőben koncepciójának és kivizsgálása konkrét hiba elérési utak elhárítását.

A központi telepítés vagy a futásidejű probléma megállapításához lásd: [memóriafelhasználás problémákat és üzembe helyezési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>E nem lehet FTP, és tegye közzé a saját kódját. Hogyan lehet megoldani a problémát?
Ellenőrizze, hogy megadta a helyes állomásnevet és [hitelesítő adatok](#step-1--set-deployment-credentials). Ellenőrizze továbbá, hogy a gépen a következő FTP-portokat nem blokkolja tűzfal:

- FTP-vezérlőkapcsolati port: 21
- FTP-adatkapcsolati port: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan csatlakoztathatom a az Azure App Service-ben FTP passzív mód keresztül?
Az Azure App Service támogatja az aktív és passzív mód használatával csatlakozik. Passzív mód használata ajánlott, mert a központi telepítési gépek rendszerint (az operációs rendszer vagy üzleti hálózati vagy otthoni részeként) tűzfal mögött található. Tekintse meg az [például a WinSCP dokumentációjának](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>További lépések

Összetettebb központi telepítési forgatókönyvek esetén próbálja meg [üzembe helyezése az Azure-ban a Git](app-service-deploy-local-git.md). Git-alapú üzembe helyezés az Azure lehetővé teszi a verziókezeléshez, a csomag-visszaállítás, az MSBuild és egyéb.

## <a name="more-resources"></a>További források

* [Az Azure App Service üzembe helyezési hitelesítő adatok](app-service-deployment-credentials.md)
