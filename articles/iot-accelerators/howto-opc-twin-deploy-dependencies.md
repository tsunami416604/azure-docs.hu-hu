---
title: Az OPC Twin felhőfüggőségek telepítése az Azure-ban | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan telepítheti az OPC Twin Azure-függőségek helyi fejlesztés és hibakeresés szükséges.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824099"
---
# <a name="deploying-dependencies-for-local-development"></a>Függőségek telepítése helyi fejlesztéshez

Ez a cikk bemutatja, hogyan telepítheti csak az Azure Platform Services szükséges a helyi fejlesztés és hibakeresés.   A végén lesz egy erőforráscsoport telepítve, amely tartalmazza a helyi fejlesztéshez és hibakereséshez szükséges mindent.

## <a name="deploy-azure-platform-services"></a>Az Azure platformszolgáltatásainak üzembe helyezése

1. Győződjön meg arról, hogy telepítve van a PowerShell és [az AzureRM PowerShell-bővítmények.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)  Nyisson meg egy parancssort vagy terminált, és futtassa a következőket:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Kövesse a utasításokat, hogy nevet rendeljen az erőforráscsoporthoz a központi telepítéshez.  A parancsfájl csak a függőségeket telepíti erre az erőforráscsoportra az Azure-előfizetésben, de a mikroszolgáltatásokat nem.  A parancsfájl is regisztrál egy alkalmazást az Azure Active Directoryban.  Ez az OAUTH-alapú hitelesítés támogatásához szükséges.  A telepítés több percet is igénybe vehet.

3. A parancsfájl befejezése után kiválaszthatja az .env fájl mentését.  Az .env környezeti fájl a fejlesztőgépen futtatni kívánt összes szolgáltatás és eszköz konfigurációs fájlja.  

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, hogy rövid és egyszerű erőforráscsoport nevet használ.  A név az erőforrások elnevezésére is használatos, mivel meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="azure-active-directory-aad-registration"></a>Az Azure Active Directory (AAD) regisztrációja

A központi telepítési parancsfájl megpróbálja regisztrálni az AAD-alkalmazásokat az Azure Active Directoryban.  A ttól függően, hogy a jogokat a kiválasztott AAD-bérlő, ez sikertelen lehet.   Három beállítás érhető el:

1. Ha egy AAD-bérlőt választott a bérlők listájából, indítsa újra a parancsfájlt, és válasszon egy másikat a listából.
2. Másik lehetőségként telepítsen egy privát AAD-bérlőt, indítsa újra a parancsfájlt, és válassza ki a használatát.
3. Folytassa hitelesítés nélkül.  Mivel a mikroszolgáltatásokat helyileg futtatja, ez elfogadható, de nem utánozza az éles környezeteket.  

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen telepítette az OPC Twin szolgáltatásokat egy meglévő projektbe, itt a javasolt következő lépés:

> [!div class="nextstepaction"]
> [További információ az OPC Twin modulok telepítéséről](howto-opc-twin-deploy-modules.md)
