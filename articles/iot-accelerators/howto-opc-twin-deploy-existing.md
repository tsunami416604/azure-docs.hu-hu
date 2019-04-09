---
title: Egy meglévő projekt üzembe helyezése az Azure IoT OPC UA-eszköz felügyeleti modul |} A Microsoft Docs
description: Hogyan helyezhet üzembe egy meglévő projekt OPC Ikereszköz.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dcf6acca344fe2a34fdc48fe89c5a1ee62b10b23
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255886"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Az OPC-Ikereszköz egy meglévő projekt üzembe helyezése

Az OPC-Ikereszköz modul az IoT Edge-ben fut, és számos biztonsági szolgáltatások biztosít az OPC-Ikereszköz és beállításjegyzék-szolgáltatásokhoz. 

Az OPC-Ikereszköz micro szolgáltatás lehetővé teszi a feldolgozó kezelők és a egy OPC Ikereszköz IoT Edge-modul segítségével a gyárban OPC UA eszközök közötti kommunikáció. A micro szolgáltatás OPC UA szolgáltatások (Tallózás, olvasási, írási és végrehajtási) a REST API-n keresztül tesz elérhetővé. 

Az OPC UA-Eszközjegyzék micro szolgáltatás regisztrált OPC UA-alkalmazások és azok végpontjait hozzáférést biztosít. Kezelők és a rendszergazdák regisztrálhat és új OPC UA-alkalmazások regisztrációját és keresse meg a meglévőket, beleértve azok végpontjait. Alkalmazás-és végpont-felügyeleten kívül a beállításjegyzék szolgáltatás regisztrált OPC Device Twin IoT Edge-modulok is összegyűjti. A szolgáltatás API-t biztosít edge modul funkcióinak, például vezérlő elindítása vagy leállítása folyamatban van a kiszolgáló felderítése (keresési szolgáltatások) vagy az OPC-Twin micro szolgáltatással elérhető új végpont a párok aktiválása.

A modul középpontjában a felügyelő identitását. A felügyelő kezeli a végpont ikereszköz, amely megfelel az OPC UA-kiszolgálói végpontot, amelyek akkor aktiválódnak, a megfelelő OPC UA-beállításjegyzék API használatával. Ez a végpont párok OPC UA JSON az OPC UA bináris üzenetekről, amelyeken a felügyelt endpoint állapot-nyilvántartó biztonságos csatornán keresztül érkeznek be a felhőben futó OPC Ikereszköz micro szolgáltatásból származó fordítja le. A felügyelő is felderítési szolgáltatást nyújt, amelyek eszköz felderítési eseményeket küld az OPC UA-eszköz regisztrációs szolgáltatás feldolgozásra, ahol ezek az események eredményez frissítések az OPC UA-beállításjegyzékbe.  Ez a cikk bemutatja, hogyan helyezhet üzembe egy meglévő projekt az OPC-Twin-modul. 

> [!NOTE]
> Üzembe helyezés részleteit és az utasítások további információkért lásd: a GitHub [tárház](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy a PowerShell és [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-az-ps) bővítmények telepítve.   Ha Ön még nem tette még, klónozza a GitHub-adattárban.  Nyisson meg egy parancssort vagy a terminált, és futtassa:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Helyezze üzembe az Azure ipari IoT-szolgáltatások

1. A parancssor megnyitása vagy terminálon futtassa:

   ```bash
   deploy
   ```

2. Kövesse az utasításokat, rendelje hozzá az erőforráscsoporthoz az üzemelő példány nevét és a egy nevet a webhelyhez.   A szkript üzembe helyezi a mikroszolgáltatásokhoz, és az erőforrás-csoportba az Azure-előfizetésben az Azure platform függősége.  A parancsfájl is regisztrál egy alkalmazást az Azure Active Directory (AAD) bérlő OAUTH-alapú hitelesítés támogatásához.  Üzembe helyezés több percet vesz igénybe.  Milyen jelennének meg a megoldás sikeresen üzembe helyezése után egy példát:

   ![Meglévő projekt üzembe helyezése ipari IoT OPC Ikereszköz](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A kimenet tartalmazza a nyilvános végpont URL-CÍMÉT. 

3. Miután a parancsprogram sikeresen lefutott, válassza ki, hogy a .env fájlt menteni szeretné.  Ha szeretne kapcsolódni, a felhőbeli végpont, eszközök, például a konzol használatával, illetve hogyan helyezhet üzembe modulokat a fejlesztés és hibakeresés .env környezet fájl szükséges.

## <a name="troubleshooting-deployment-failures"></a>Üzembe helyezés hibáinak elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, egy rövid és egyszerű erőforráscsoport-nevet használja.  A név hálózatnév-erőforrások, például meg kell felelnie az elnevezési követelményeknek erőforrás is szolgál.  

### <a name="website-name-already-in-use"></a>Webhely neve már használatban van

Akkor lehet, hogy a webhely neve már használatban van.  Ha ezt a hibát tapasztal, szeretné használni egy másik alkalmazásnevet.

### <a name="azure-active-directory-aad-registration"></a>Az Azure Active Directory (AAD) regisztráció

Az üzembe helyezési parancsfájl megpróbálja két AAD-alkalmazás regisztrálása az Azure Active Directoryban.  Attól függően, a kiválasztott AAD-bérlő jogosultságok a telepítés meghiúsulhat. Két lehetőség van:

1. Ha egy AAD-bérlő a bérlők listáját, indítsa újra a parancsfájlt, és válasszon egy másikat a listából.
2. Azt is megteheti üzembe helyezése saját AAD-bérlő egy másik előfizetésben, indítsa újra a parancsfájlt, és válassza a használatára.

> [!WARNING]
> Soha ne folytassa a hitelesítés nélkül.  Ha ezt választja, az internetről nem hitelesített bárki is hozzáférhet az OPC-Eszközfelügyelet végpontok.   Mindig választhatják a ["local" üzembe helyezési lehetőség](howto-opc-twin-deploy-dependencies.md) a próbához.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Egy teljes körű ipari IoT szolgáltatásokat bemutató üzembe helyezése

Csak a szolgáltatások és a függőségek helyett egy teljes körű bemutatót is telepítheti.  Az összes egy bemutatót a három OPC UA-kiszolgálók, az OPC-Twin-modul, az összes mikroszolgáltatásokhoz és egy mintául szolgáló webalkalmazást tartalmaz.  Az bemutatási célokra szolgál.

1. Győződjön meg arról, hogy egy klónozott tárház (lásd fent). Nyissa meg a tárházat és a Futtatás gyökérkönyvtárában található egy parancssorba vagy terminálba:

   ```bash
   deploy -type demo
   ```

2. Kövesse az utasításokat az erőforráscsoport és a egy nevet a webhelyhez hozzárendelése egy új nevet.  Sikeresen üzembe helyezését követően a szkript a webes alkalmazás végpontjának URL-CÍMÉT jeleníti meg.

## <a name="deployment-script-options"></a>Üzembe helyezési szkript beállításai

A szkript a következő paramétereket fogadja:

```bash
-type
```

A típusú telepítés (virtuális gép, helyi, bemutató)

```bash
-resourceGroupName
```

Lehet egy meglévő vagy új erőforráscsoport nevét.

```bash
-subscriptionId
```

Nem kötelező megadni, ahol erőforrásokat üzembe helyezve az előfizetés-azonosító.

```bash
-subscriptionName
```

Vagy az előfizetés nevét.

```bash
-resourceGroupLocation
```

Nem kötelező; az erőforráscsoport helyét. Ha meg van adva, megpróbálja ezen a helyen hozzon létre egy új erőforráscsoportot.

```bash
-aadApplicationName
```

Regisztrálja alatt az AAD-alkalmazás nevét. 

```bash
-tenantId
```

AAD-bérlő használatához.

```bash
-credentials
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan helyezhet üzembe egy meglévő projekt OPC Ikereszköz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az OPC-ügyfél és az OPC PLC kommunikációjának biztonságossá tétele](howto-opc-vault-deploy-existing-client-plc-communication.md)
