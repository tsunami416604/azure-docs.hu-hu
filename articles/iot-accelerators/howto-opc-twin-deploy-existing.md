---
title: OPC Twin modul telepítése meglévő Azure-projekthez | Microsoft dokumentumok
description: Ez a cikk az OPC Twin üzembe helyezését ismerteti egy meglévő projektben. Azt is megtudhatja, hogyan háríthatja el a központi telepítési hibák elhárítását.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824121"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Opc Twin telepítése meglévő projektre

Az OPC Twin modul az IoT Edge-en fut, és számos peremhálózati szolgáltatást biztosít az OPC Twin és registry szolgáltatásokszámára.

Az OPC Twin mikroszolgáltatás megkönnyíti a gyári üzemeltetők és az OPC UA szervereszközök közötti kommunikációt a gyári szinten egy OPC Twin IoT Edge modulon keresztül. A mikroszolgáltatás elérhetővé teszi az OPC UA-szolgáltatásokat (Tallózás, Olvasás, Írás és Végrehajtás) a REST API-n keresztül. 

Az OPC UA eszköz beállításjegyzék-mikroszolgáltatása hozzáférést biztosít a regisztrált OPC UA-alkalmazásokhoz és azok végpontjaihoz. Az operátorok és a rendszergazdák regisztrálhatják és leiratkozhatják az új OPC UA-alkalmazásokat, és böngészhetnek a meglévők között, beleértve a végpontjaikat is. Az alkalmazás- és végpontkezelés mellett a beállításjegyzék-szolgáltatás a regisztrált OPC Twin IoT Edge modulokat is katalogizálja. A szolgáltatás API-t biztosít a peremhálózati modul funkcióinak vezérlését, például a kiszolgálófelderítés indítása vagy leállítása (beolvasási szolgáltatások), vagy az OPC Twin mikroszolgáltatás sal elérhető új végponti ikerpáraktiválások aktiválása.

A modul lényege a felügyelő identitása. A felügyelő kezeli a végpont iker, amely megfelel az OPC UA szerver végpontok, amelyek a megfelelő OPC UA beállításjegyzék API-val aktivált. Ez a végpont twins lefordítani OPC UA JSON kapott a felhőben futó OPC Twin mikroszolgáltatás az OPC UA bináris üzenetek, amelyek egy állapotalapú biztonságos csatornán keresztül küldött a felügyelt végpontra. A felügyelő felderítési szolgáltatásokat is biztosít, amelyek eszközfelderítési eseményeket küldenek az OPC UA-eszköz bevezetési szolgáltatásába feldolgozásra, ahol ezek az események az OPC UA rendszerleíró adatbázisának frissítését eredményezik.  Ez a cikk bemutatja, hogyan telepítheti az OPC Twin modult egy meglévő projektre.

> [!NOTE]
> A központi telepítés részleteiről és utasításairól a [GitHub-tárházban](https://github.com/Azure/azure-iiot-opc-twin-module)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy telepítve van a PowerShell és [az AzureRM PowerShell-bővítmények.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) Ha még nem tette meg, klónozza ezt a GitHub-tárházat. Futtassa a következő parancsokat a PowerShellben:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Ipari IoT-szolgáltatások üzembe helyezése az Azure-ban

1. A PowerShell-munkamenetben futtassa a következőket:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. A gombra a következő utasításokat követve rendeljen nevet a központi telepítés erőforráscsoportjához, és egy nevet a webhelyhez.   A parancsfájl üzembe helyezi a mikroszolgáltatásokat és az Azure platformfüggőségeit az Azure-előfizetéserőforrás-csoportba.  A parancsfájl is regisztrál egy alkalmazást az Azure Active Directory (AAD) bérlőben az OAUTH-alapú hitelesítés támogatásához.  A telepítés több percet vesz igénybe.  Példa arra, hogy mit szeretne látni, ha a megoldás sikeresen telepítve van:

   ![Az Industrial IoT OPC Twin telepítése a meglévő projektbe](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A kimenet tartalmazza a nyilvános végpont URL-címét. 

3. A parancsfájl sikeres befejezése után válassza ki, `.env` hogy menti-e a fájlt.  A `.env` környezeti fájlra van szüksége, ha a felhővégponthoz szeretne csatlakozni olyan eszközökkel, mint például a konzol, vagy modulokat telepít fejlesztési és hibakeresési célokra.

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, hogy rövid és egyszerű erőforráscsoport nevet használ.  A név az erőforrások elnevezésére is használatos, mivel meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="website-name-already-in-use"></a>A webhely neve már használatban van

Lehetséges, hogy a weboldal neve már használatban van.  Ha ez a hiba jelentkezik, másik alkalmazásnevet kell használnia.

### <a name="azure-active-directory-aad-registration"></a>Az Azure Active Directory (AAD) regisztrációja

A központi telepítési parancsfájl két AAD-alkalmazást próbál regisztrálni az Azure Active Directoryban.  A kiválasztott AAD-bérlő höz fűződő jogosultságaitól függően előfordulhat, hogy a központi telepítés sikertelen lesz. Két lehetőség érhető el:

1. Ha egy AAD-bérlőt választott a bérlők listájából, indítsa újra a parancsfájlt, és válasszon egy másikat a listából.
2. Másik lehetőségként telepítsen egy privát AAD-bérlőt egy másik előfizetésben, indítsa újra a parancsfájlt, és válassza ki a használatát.

> [!WARNING]
> SOHA ne folytassa hitelesítés nélkül.  Ha úgy dönt, hogy ezt teszi, bárki hozzáférhet az OPC Twin végpontjaihoz az internetről nem hitelesítetten.   Mindig kiválaszthatja a ["helyi" telepítési lehetőséget,](howto-opc-twin-deploy-dependencies.md) hogy rúgja a gumiabroncsokat.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>All-in-one ipari IoT-szolgáltatások bemutatójának üzembe helyezése

Ahelyett, hogy csak a szolgáltatások és a függőségek is üzembe helyezheti az all-in-one demó.  Az all in one demó három OPC UA-kiszolgálót, az OPC Twin modult, az összes mikroszolgáltatást és egy mintawebalkalmazást tartalmaz.  Demonstrációs célokra készült.

1. Győződjön meg róla, hogy van egy klón a tárház (lásd fent). Nyisson meg egy PowerShell-kérdést a tárház gyökerében, és futtassa a következőket:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. A következő utasításokat követve rendeljen új nevet az erőforráscsoporthoz, és egy nevet a webhelyhez.  A sikeres üzembe helyezést követően a parancsfájl megjeleníti a webalkalmazás végpontjának URL-címét.

## <a name="deployment-script-options"></a>Központi telepítési parancsfájl beállításai

A parancsfájl a következő paramétereket veszi figyelembe:

```powershell
-type
```

A telepítés típusa (vm, helyi, bemutató)

```powershell
-resourceGroupName
```

Lehet egy meglévő vagy egy új erőforráscsoport neve.

```powershell
-subscriptionId
```

Nem kötelező, az előfizetés-azonosító, ahol az erőforrások at kell telepíteni.

```powershell
-subscriptionName
```

Vagy az előfizetés nevét.

```powershell
-resourceGroupLocation
```

Nem kötelező, erőforráscsoport helye. Ha meg van adva, megpróbál létrehozni egy új erőforráscsoportot ezen a helyen.

```powershell
-aadApplicationName
```

Az AAD-alkalmazás neve, amely alatt regisztrálható.

```powershell
-tenantId
```

AAD-bérlő.

```powershell
-credentials
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan telepítheti az OPC Twin-t egy meglévő projektre, itt van a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Az OPC UA kliens és az OPC UA PLC biztonságos kommunikációja](howto-opc-vault-secure.md)
