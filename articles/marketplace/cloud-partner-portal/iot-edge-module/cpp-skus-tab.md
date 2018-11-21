---
title: Az Azure IoT Edge-modul termékváltozatok |} A Microsoft Docs
description: Hozzon létre egy IoT Edge-modul SKU-k.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 370d8160661c1f73124151a3a49d0bb3170dfb77
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276040"
---
# <a name="iot-edge-module-skus-tab"></a>IoT Edge-modul termékváltozatok lapon

A **termékváltozatok** lapján a **új ajánlat** lap lehetővé teszi, hogy hozzon létre egy vagy több termékváltozatok és rendelheti őket hozzá az új ajánlat.  Különböző SKU-k segítségével különbséget tenni egy megoldás szolgáltatáskészleteket, számlázási modellek vagy valamilyen más jellemzők alapján.

## <a name="sku-settings"></a>Termékváltozat-beállítások

Amikor egy új ajánlat létrehozása, nincsenek bármely a tartozó termékváltozatokat. Hozzon létre egy új Termékváltozatban, kövesse az alábbi lépéseket:

- Az a **IoT Edge-modulok > Új ajánlat** lapon válassza ki a **termékváltozatok** fülre.
- SKU-k, válassza a **+ új Termékváltozat** egy párbeszédpanel megnyitásához.

  ![Az IoT Edge-modulok új ajánlat lapján új Termékváltozat gomb](./media/iot-edge-module-skus-tab-new-sku.png)

- Az a **új Termékváltozat** párbeszédpanel mezőben, adja meg a Termékváltozat azonosítóját, majd **OK**.
(Az alábbi táblázat tartalmazza az azonosító elnevezési konvencióinak.)

A **termékváltozatok** lap frissülnek, és szerkesztheti a Termékváltozat konfigurálása mezőket jeleníti meg. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-AZONOSÍTÓJA**       | Ez a Termékváltozat azonosítója. Ez a név legfeljebb 50 karakter hosszú lehet, amely rendelkezik kisbetűs alfanumerikus karaktereket és kötőjeleket (-), de nem végződhet kötőjellel. **Megjegyzés:** az ajánlat közzétételének után nem módosíthatja ezt a nevet. A név termék URL-címek az országom. |

## <a name="sku-details"></a>Termékváltozat részletei

Konfigurálja a **Termékváltozatának adatait** hogyan a Termékváltozat jelenik meg az Azure Marketplace-en és az Azure Portal webhely meghatározásához.

![IoT Edge-modul termékváltozat metaadatok](media/iot-edge-module-skus-tab-metadata.png)

A következő táblázat ismerteti a célja, tartalom, és a mezők területen formázás **Termékváltozatának adatait**.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Cím**        | Ez a Termékváltozat címét. Legfeljebb 50 karakter hosszúságú. <br/> Megjelenik az Azure Portalon, és a egy alapértelmezett Modulnév (nélkül szóközöket és speciális karakterek) lesz mikor telepítették. A képek, lásd: pontosan hol ebben a mezőben jelenik meg az alábbi témakörben talál.|
| **összegzés**      | Ez a Termékváltozat rövid összefoglalása. A hosszabb 100 karakternél. Tegye **nem** összefoglalója az ajánlat csak a Termékváltozat.  Az összegzés megjelenik az Azure piactéren. A képek, lásd: pontosan hol ebben a mezőben jelenik meg az alábbi témakörben talál.|
| **Leírás**  | Ez a Termékváltozat rövid leírása. 3000 karakternél hosszabb. Írja le az ajánlatra, de csak a Termékváltozat. Az azure Marketplace-en és az Azure Portalon megjelennek. Az Azure Portalon, hozzáfűzi az ajánlatot a piactéren lapon meghatározott leíró Marketplace leírása.  Ugyanaz, mint a Termékváltozat összefoglaló lehet. A képek, lásd: pontosan hol ebben a mezőben jelenik meg az alábbi témakörben talál.|
| **Ez a Termékváltozat elrejtése** | Az alapértelmezett beállítás, amely folyamatosan **nem**. |

### <a name="sku-example"></a>Termékváltozat-példa

 Az alábbi példák mutatják hogyan Termékváltozat **cím**, **összefoglalás**, és **leírás** mező jelenik meg a különböző nézeteket.
 
#### <a name="on-the-azure-marketplace-website"></a>Az Azure Marketplace webhelyén:

- Ha megnézzük a Termékváltozatának adatait:

    ![Hogyan jelennek meg az Azure piactér webhelyén termékváltozatok](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>Az Azure Portal webhelyen:

- SKU-k böngészésekor:

    ![Hogyan IoT Edge-modul megjelenik-e az Azure Portalon #1 böngészésekor](media/iot-edge-module-portal-browse.png)

    ![Hogyan IoT Edge-modul megjelenik-e az Azure Portalon #2 böngészésekor](media/iot-edge-module-portal-product-picker.png)

- SKU-k keresése:

    ![Hogyan IoT Edge-modul megjelenik-e az Azure Portalon keresésekor](media/iot-edge-module-portal-search.png)

- Ha megnézzük a Termékváltozatának adatait:

    ![Hogyan IoT Edge-modul megjelenik-e ha a termék adatait a portálon](./media/iot-edge-module-portal-pdp.png)

- A modul telepítésekor:
    
    ![Hogyan IoT Edge-modul megjelenik-e folyamatban telepítésekor](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>Termékváltozat-tartalma

A **Edge-modul lemezképek**, adja meg az adatokat, hogy fel kell töltenie az IoT Edge-modul.

Ossza meg velünk a hozzáférést a [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR), amely tartalmazza az IoT Edge modul képen, hogy azt töltse fel, és azt igazolja. Miután közzé van téve, az IoT Edge-modul lesznek másolva, és elosztott egy az Azure piactér által üzemeltetett nyilvános container registry használatával.

Célozhat meg több platformot, és adja meg a címkék segítségével különböző verzióiban. Tudjon meg többet [címkéket és a verziókezelés "előkészítése az IoT Edge modul technikai eszközök"](./cpp-create-technical-assets.md).

![Az IoT Edge-modul képek](./media/iot-edge-module-skus-tab-acr.png)

A következő táblázat ismerteti a célja, a tartalmát, és a mezők formázása:

- **Adattár részletei**
- **Lemezkép verziója**

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
|  ***Adattár részletei***   |  |
| **előfizetés-azonosító**        | Az ACR Azure-előfizetés azonosítója.|
| **Erőforráscsoport neve**      | Az erőforráscsoport neve, az ACR.|
| **Beállításjegyzék neve**  | Az ACR-beállításjegyzék neve. Csak másolja a beállításjegyzék neve, nem a bejelentkezési kiszolgáló nevét (például nélkül a `azurecr.io`.) |
| **Adattár neve**  | Az ACR, amely tartalmazza az IoT Edge-modul adattár neve. **Megjegyzés:** után a név beállítása, azt később már nem módosítható. Az egyedi név, hogy ne legyen semmilyen egyéb ajánlattal-fiókjában található ugyanazzal a névvel. |
| **Felhasználónév** | Az ACR (rendszergazdai felhasználónév) tartozó felhasználónév. |
| **Jelszó** | Az ACR-tartozó jelszót. |
|  ***Lemezkép verziója***   |  |
| **Kép címke- vagy** | Legalább tartalmaznia kell egy `latest` címke és a egy verzió címkéje (például kezdve `xx.xx.xx-` ahol xx számos). Lehetnek [címkék manifest](https://github.com/estesp/manifest-tool) , amelyekre több platformon. Jegyzékfájl címke által hivatkozott összes címkék is hozzá kell adni úgy is fel őket. A címkék használatával az IoT Edge-modul több verzióját is hozzáadhat. Az összes manifest címkék (kivéve `latest`) kell kezdődnie vagy `X.Y-` vagy `X.Y.Z-` Z X, Y, amelyeknél egész számok. Tudjon meg többet [címkéket és a verziókezelés "előkészítése az IoT Edge modul technikai eszközök"](./cpp-create-technical-assets.md). <br/> Például ha egy `latest` mutat, amelyre mutat címkézése `1.0.1-linux-x64`, `1.0.1-linux-arm32`,, és `1.0.1-windows-arm32`, ezek 6 címkék itt hozzá kell adnia. |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>Indítsa el az IoT Edge-modul alapértelmezett beállításokat használó ügyfeleknek

Az IoT Edge-modul telepítéséhez a leggyakrabban használt beállítások megadása. Ügyfél központi telepítések optimalizálja őket az IoT Edge modul-a-beépített ezeket az alapértelmezett értékeket a indítása azáltal.

![IoT Edge modul alapértelmezett központi telepítési beállítások](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

A következő táblázat ismerteti a célja, a tartalmát, és a mezők formázása **alapértelmezett útvonalak**, **alapértelmezett ikereszköz kívánt tulajdonságait**, **alapértelmezett környezeti változók**, és **CreateOptions alapértelmezett**.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Az alapértelmezett útvonalak**        | Egyes alapértelmezett útvonal neve és értéke kevesebb, mint 512 karakterből kell lennie. Megadhatja, hogy legfeljebb 5 alapértelmezett útvonalakat. Ellenőrizze, hogy a helyes használandó [irányíthatja a szintaxis](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) az útvonal érték. Tekintse meg a modult, használja az alapértelmezett modul neve, amely lesz a **Termékváltozat cím** szóközöket és speciális karakterek nélkül. Tekintse meg más modulok még nem ismert, használja a `<FROM_MODULE_NAME>` konvenciókhoz, és ügyfeleinek, hogy ezek az adatok frissíteni kell. Tudjon meg többet [továbbítja az IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Például ha a modul `ContosoModule` a bemenet figyeli `ContosoInput` és kimeneti adatok `ContosoOutput`, logikus a következő 2 alapértelmezett útvonalak megadása:<br/>-#1 neve: `ToContosoModule`<br/>-#1. érték:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-#2 neve: `FromContosoModuleToCloud`<br/>-#2. érték: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Alapértelmezett ikereszköz kívánt tulajdonságait**      | Egyes alapértelmezett ikereszköz kívánt tulajdonságait név és érték kevesebb, mint 512 karakterből kell lennie. Legfeljebb 5 név-érték párok ikereszköz kívánt tulajdonságait határozhatja meg. Ikereszköz kívánt tulajdonságait értékének érvényes JSON-t, nem – escape-karakterrel, tömbök nélkül és beágyazott hierarchia maximuma pedig 4 kell lennie. Tudjon meg többet [ikereszköz kívánt tulajdonságait](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Például ha egy modul támogatja az ikereszköz kívánt tulajdonságait keresztül egy dinamikusan konfigurálhatók frissítési gyakoriság, logikus határozza meg a következő alapértelmezett ikereszköz kívánt tulajdonságot:<br/> -#1 neve: `RefreshRate`<br/>-#1. érték: `60`|
| **Alapértelmezett környezeti változók**  | Egyes alapértelmezett környezeti változók neve és értéke kevesebb, mint 512 karakterből állhat. Megadhatja, hogy legfeljebb 5 név-érték párok környezeti változókat. <br/>Például ha egy modul indítását megelőzően használati feltételek elfogadására van szükség, meghatározhatja a következő környezeti változó:<br/> -#1 neve: `ACCEPT_EULA`<br/>-#1. érték: `Y`|
| **Alapértelmezett createOptions**  | A createOptions kevesebb, mint 512 karakterből kell állnia. Érvényes JSON feloldójelekkel kell lennie. Tudjon meg többet [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Például ha egy modulnak szüksége van kötni a portot megadhatja a következő createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> Válassza ki **mentése** a Termékváltozat beállítások mentéséhez. 

## <a name="next-steps"></a>További lépések

Használja a [piactér lapján](./cpp-marketplace-tab.md) hozhat létre a piactéren az ajánlat leírását.
