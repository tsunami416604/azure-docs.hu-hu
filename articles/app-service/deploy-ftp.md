---
title: Tartalom telepítése FTP/S használatával
description: Ismerje meg, hogyan telepítheti alkalmazását az Azure App Service-ben FTP vagy FTPS használatával. A webhely biztonságának javítása a titkosítatlan FTP letiltásával.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 7bc637b5719da3c5f5e5607436aa7da0721f5a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266012"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Az alkalmazás telepítése az Azure App Service szolgáltatásba FTP/S használatával

Ebből a cikkből megtudhatja, hogy miként telepítheti az FTP- vagy FTPS-t a webalkalmazás, a mobilalkalmazás-háttér- vagy az API-alkalmazás Azure App Service szolgáltatásba való üzembe [helyezéséhez.](https://go.microsoft.com/fwlink/?LinkId=529714)

Az alkalmazás FTP/S végpontja már aktív. Az FTP/S telepítésének engedélyezéséhez nincs szükség konfigurációra.

## <a name="open-ftp-dashboard"></a>FTP-irányítópult megnyitása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki **az App Services**lehetőséget.

    ![Alkalmazásszolgáltatások keresése.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Válassza ki a telepíteni kívánt webalkalmazást.

    ![Válassza ki az alkalmazást.](media/app-service-continuous-deployment/select-your-app.png)

3. Válassza a **Deployment Center** > **FTP-irányítópultját.****FTP** > 

    ![FTP-irányítópult megnyitása](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP-kapcsolat adatainak beszerezni

Az FTP-irányítópulton válassza a **Másolás** lehetőséget az FTPS-végpont és az alkalmazás hitelesítő adatainak másolásához.

![FTP-adatok másolása](./media/app-service-deploy-ftp/ftp-dashboard.png)

Javasoljuk, hogy az **alkalmazás hitelesítő adatait** használja az alkalmazásra való üzembe helyezéshez, mert az minden alkalmazásra egyedi. Ha azonban a Felhasználói hitelesítő adatok elemre kattint, **beállíthatja**az FTP/S bejelentkezéshez használható felhasználói hitelesítő adatokat az előfizetésösszes App Service-alkalmazásához.

> [!NOTE]
> FTP/FTPS-végpontra hitelesítés felhasználói szintű hitelesítő adatokkal a következő formátumban igénylő felhasználónevet igényel: 
>
>`<app-name>\<user-name>`
>
> Mivel a felhasználói szintű hitelesítő adatok a felhasználóhoz vannak kapcsolva, és nem egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie, hogy a bejelentkezési műveletet a megfelelő alkalmazásvégpontra irányítsa.
>

## <a name="deploy-files-to-azure"></a>Fájlok üzembe helyezése az Azure-ban

1. Az FTP-ügyfélprogramból (például [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)vagy [WinSCP)](https://winscp.net/index.php)az összegyűjtött kapcsolati adatok alapján csatlakozhat az alkalmazáshoz.
2. Másolja a fájlokat és a megfelelő könyvtárstruktúrát az Azure [ **/site/wwwroot** könyvtárába](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (vagy a **/site/wwwroot/App_Data/Jobs/** könyvtárba a WebJobs-hoz).
3. Tallózással keresse meg az alkalmazás URL-címét, és ellenőrizze, hogy az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> A [Git-alapú telepítésekkel](deploy-local-git.md)ellentétben az FTP-telepítés nem támogatja a következő telepítési automatizálásokat: 
>
> - függőség-visszaállítások (például NuGet, NPM, PIP és Composer automatizálások)
> - .NET bináris fájlok összeállítása
> - generációs web.config (itt van egy [Node.js példa](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Ezeket a szükséges fájlokat manuálisan hozza létre a helyi számítógépen, majd telepítse őket az alkalmazással együtt.
>

## <a name="enforce-ftps"></a>FTPS kényszerítése

A fokozott biztonság érdekében csak az SSL-en keresztül imázsos FTP-t engedélyezze. Az FTP és az FTPS is letiltható, ha nem használja az FTP-telepítést.

Az alkalmazás erőforráslapján az [Azure Portalon](https://portal.azure.com)válassza a **Konfigurációs** > **általános beállítások a** bal oldali navigációs.

A titkosítatlan FTP letiltásához válassza **a Csak FTPS** **FTP állapotban**lehetőséget. Ha teljesen le szeretné tiltani az FTP és az FTPS szolgáltatást is, válassza **a Letiltva**lehetőséget. Amikor végzett, kattintson a **Mentés** gombra. Ha **csak FTPS-t**használ, a TLS 1.2-es vagy újabb verziót a webalkalmazás **TLS/SSL** beállításpaneljéhez navigálva kell érvényesítenie. A TLS 1.0 és 1.1 nem támogatott **csak FTPS .**

![FTP/S letiltása](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az Azure [CLI](/cli/azure)használatával történő FTP-telepítésről a [Webalkalmazás létrehozása és fájlok ftp-vel (Azure CLI) való telepítése](./scripts/cli-deploy-ftp.md)című témakörben talál.

Az Azure [PowerShell](/cli/azure)használatával történő FTP-telepítésről a [Fájlok feltöltése FTP (PowerShell) használatával című](./scripts/powershell-deploy-ftp.md)témakörben talál.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP-telepítés – problémamegoldás

- [Hogyan háríthatók el az FTP telepítésének hibaelhárítása?](#how-can-i-troubleshoot-ftp-deployment)
- [Nem tudom FTP és közzéteszi a kódot. Hogyan oldhatom meg a problémát?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan csatlakozhatok az FTP-hez az Azure App Service-ben passzív módban?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan háríthatók el az FTP telepítésének hibaelhárítása?

Az FTP-telepítés elhárításának első lépése a központi telepítési probléma elkülönítése egy futásidejű alkalmazásproblémából.

A központi telepítési probléma általában nem eredményez fájlokat vagy helytelen fájlokat telepítve az alkalmazásba. A hibaelhárításhoz vizsgálja az FTP központi telepítését, vagy válasszon egy alternatív telepítési útvonalat (például a forrásvezérlőt).

A futásidejű alkalmazásprobléma általában az alkalmazáshoz telepített megfelelő fájlkészletet eredményezi, de az alkalmazás helytelen működését. A hibaelhárítást úgy háríthatja el, hogy futásidőben a kód viselkedésére összpontosít, és bizonyos hibaútvonalakat vizsgál.

Központi telepítési vagy futásidejű problémák megállapításáról a [Központi telepítés és a futásidejű problémák témakörben](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)talál.

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nem tudok FTP-hez csatlakozni, és közzétenni a kódot. Hogyan oldhatom meg a problémát?
Ellenőrizze, hogy a megfelelő állomásnevet és [hitelesítő adatokat](#open-ftp-dashboard)adta-e meg. Ellenőrizze azt is, hogy a következő FTP-portokat a számítógépen nem blokkolja-e tűzfal:

- FTP-vezérlőkapcsolati port: 21
- FTP-adatkapcsolat iportja: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan csatlakozhatok az FTP-hez az Azure App Service-ben passzív módban?
Az Azure App Service támogatja az aktív és a passzív módban történő csatlakozást. A passzív mód használata azért előnyös, mert a központi telepítési gépek általában tűzfal mögött vannak (az operációs rendszerben, illetve otthoni vagy üzleti hálózat részeként). Lásd [a WinSCP dokumentációjának egy példáját.](https://winscp.net/docs/ui_login_connection) 

## <a name="next-steps"></a>További lépések

A speciálisabb üzembe helyezési forgatókönyvek érdekében próbálja meg [telepíteni az Azure-ba a Git segítségével.](deploy-local-git.md) Git-alapú üzembe helyezés az Azure-ban lehetővé teszi a verziókövetést, a csomag-visszaállítást, az MSBuild-et és egyebeket.

## <a name="more-resources"></a>További erőforrások

* [Az Azure App Service telepítési hitelesítő adatai](deploy-configure-credentials.md)
