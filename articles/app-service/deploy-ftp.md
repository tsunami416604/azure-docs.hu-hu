---
title: Tartalom üzembe helyezése FTP/S használatával
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást Azure App Service FTP-vagy FTPS használatával. A titkosítatlan FTP letiltásával javíthatja a webhelyek biztonságát.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fcc7c5b8fa182cace6e3dae0b1cae4cd41c5dcb9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532582"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Az alkalmazás üzembe helyezése az Azure App Service FTP/S használatával

Ez a cikk bemutatja, hogyan használható az FTP vagy a FTPS a webalkalmazások, a Mobile apps-háttérrendszer vagy az API-alkalmazások [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714)való üzembe helyezéséhez.

Az alkalmazáshoz tartozó FTP/S végpont már aktív. Az FTP/S központi telepítésének engedélyezéséhez nincs szükség konfigurációra.

## <a name="open-ftp-dashboard"></a>FTP-irányítópult megnyitása

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **app Services**lehetőséget.

    ![Keressen rá az App Services kifejezésre.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Válassza ki a telepíteni kívánt webalkalmazást.

    ![Válassza ki az alkalmazást.](media/app-service-continuous-deployment/select-your-app.png)

3. Válassza ki az **üzembehelyezési központ** > **FTP** > -**irányítópultját**.

    ![FTP-irányítópult megnyitása](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP-kapcsolat adatainak beolvasása

Az FTP-irányítópulton válassza a **Másolás** lehetőséget a FTPS-végpont és az alkalmazás hitelesítő adatainak másolásához.

![FTP-adatok másolása](./media/app-service-deploy-ftp/ftp-dashboard.png)

Javasoljuk, hogy az alkalmazáshoz tartozó **hitelesítő adatok** használatával telepítse az alkalmazást, mert az minden alkalmazás esetében egyedi. Ha azonban a **felhasználói hitelesítő adatok**elemre kattint, megadhat felhasználói szintű hitelesítő adatokat, amelyeket az előfizetésében található összes app Service alkalmazáshoz HASZNÁLHAT az FTP/S bejelentkezéshez.

> [!NOTE]
> Hitelesítés egy FTP-/FTPS-végponthoz felhasználói szintű hitelesítő adatokkal, amelyek a felhasználónevet a következő formátumban használják: 
>
>`<app-name>\<user-name>`
>
> Mivel a felhasználói szintű hitelesítő adatok a felhasználóhoz kapcsolódnak, nem pedig egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie ahhoz, hogy a bejelentkezési műveletet a megfelelő alkalmazás-végpontra irányítsa.
>

## <a name="deploy-files-to-azure"></a>Fájlok üzembe helyezése az Azure-ban

1. Az FTP-ügyfélről (például [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)vagy [megnyerő](https://winscp.net/index.php)) használja az összegyűjtött kapcsolati adatokat az alkalmazáshoz való kapcsolódáshoz.
2. Másolja a fájlokat és a hozzá tartozó címtár-struktúrát a [ **/site/wwwroot** könyvtárba](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) az Azure-ban (vagy a **/site/wwwroot/App_Data/Jobs/** könyvtárat a webjobs szolgáltatáshoz).
3. Keresse meg az alkalmazás URL-címét, és ellenőrizze, hogy az alkalmazás megfelelően fut-e. 

> [!NOTE] 
> A [git-alapú telepítések](deploy-local-git.md)eltérően az FTP-telepítés nem támogatja a következő üzembe helyezési automatizálásokat: 
>
> - függőségek visszaállítása (például NuGet, NPM, PIP és zeneszerzői automatizálások)
> - a .NET bináris fájljainak összeállítása
> - a web. config generációja (itt a [Node. js példa](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Létrehozza ezeket a szükséges fájlokat manuálisan a helyi gépen, majd telepítse azokat az alkalmazással együtt.
>

## <a name="enforce-ftps"></a>FTPS betartatása

A fokozott biztonság érdekében csak a TLS/SSL protokollt használó FTP-t engedélyezze. Ha nem használja az FTP-telepítést, le is tilthatja az FTP-t és a FTPS.

Az alkalmazás erőforrás-lapja [Azure Portalban](https://portal.azure.com)válassza a **konfiguráció** > **általános beállítások** lehetőséget a bal oldali navigációs sávon.

A titkosítatlan FTP letiltásához válassza a **FTPS csak** **FTP-állapotban**lehetőséget. Az FTP és a FTPS teljes letiltásához válassza a **Letiltva**lehetőséget. Amikor végzett, kattintson a **Mentés** gombra. Ha **csak a FTPS**-t használja, a webalkalmazás **TLS/SSL-beállítások** paneljére kell kikényszeríteni a TLS 1,2-es vagy újabb verzióját. A TLS 1,0 és 1,1 **csak a FTPS**esetében támogatott.

![FTP/S letiltása](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Az [Azure CLI](/cli/azure)használatával történő FTP-telepítéssel kapcsolatban lásd: [Webalkalmazás létrehozása és fájlok üzembe helyezése FTP-vel (Azure CLI)](./scripts/cli-deploy-ftp.md).

Az [Azure PowerShell](/cli/azure)használatával történő FTP-telepítéssel kapcsolatban lásd: [fájlok feltöltése egy webalkalmazásba FTP használatával (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP-telepítés hibáinak megoldása

- [Hogyan lehet elhárítani az FTP-telepítést?](#how-can-i-troubleshoot-ftp-deployment)
- [Nem tudok FTP-t és közzétenni a kódot. Hogyan oldható fel a probléma?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hogyan csatlakozhatok az FTP-hez Azure App Service passzív módban?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hogyan lehet elhárítani az FTP-telepítést?

Az FTP-telepítés hibaelhárításának első lépéseként elszigeteli az üzembe helyezési problémát egy futásidejű alkalmazás hibája alapján.

A központi telepítési probléma általában nem tartalmaz az alkalmazásba telepített fájlokat vagy helytelen fájlokat. Az FTP központi telepítésének kivizsgálásával vagy egy másik telepítési útvonal (például a verziókövetés) kiválasztásával elháríthatja a hibakeresést.

A futásidejű alkalmazások problémái általában az alkalmazáshoz központilag telepített fájlok megfelelő készletét eredményezik, de helytelen az alkalmazás viselkedése. A hibakereséshez összpontosíthat a futásidejű kódok viselkedésére, és kivizsgálhatja a hibák adott elérési útját.

Az üzembe helyezési vagy futásidejű probléma okának meghatározásához lásd: [üzembe helyezési](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)és futásidejű problémák.

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nem tudok FTP-hez csatlakozni, és közzétenni a kódot. Hogyan oldható fel a probléma?
Győződjön meg arról, hogy a helyes állomásnevet és [hitelesítő adatokat](#open-ftp-dashboard)adta meg. Győződjön meg arról is, hogy a számítógépen a következő FTP-portok nincsenek letiltva a tűzfalon:

- FTP-vezérlőkapcsolati port: 21
- FTP-adatkapcsolati port: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hogyan csatlakozhatok az FTP-hez Azure App Service passzív módban?
Azure App Service támogatja az aktív és passzív módban történő csatlakozást. A passzív üzemmód használata ajánlott, mert az üzembe helyezési gépek általában tűzfal mögött vannak (az operációs rendszeren vagy otthoni vagy üzleti hálózat részeként). Tekintse meg [a megnyerő dokumentációban található példát](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>További lépések

A speciális üzembe helyezési forgatókönyvek esetében próbálja meg [üzembe helyezni az Azure-ban a git használatával](deploy-local-git.md). Az Azure-hoz készült git-alapú üzembe helyezés lehetővé teszi a verziókövetés, a csomagok visszaállítása, az MSBuild és egyebek használatát.

## <a name="more-resources"></a>További erőforrások

* [Központi telepítési hitelesítő adatok Azure App Service](deploy-configure-credentials.md)
