---
title: Azure IoT Edge modul sk-ek | Azure Piactér
description: Hozzon létre egy IoT Edge-modul létrehozása.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a989113c80cd8189cce9da959fe27509297f677a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745688"
---
# <a name="iot-edge-module-skus-tab"></a>Az IoT Edge modul skus lapja

>[!Important]
>2020. március 30-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

Az **Új ajánlat** lap **SK-ja** lehetővé teszi egy vagy több skus létrehozását, és társíthatja őket az új ajánlathoz.  Különböző sk-ek segítségével különbséget tehet a megoldás szolgáltatáskészletek, számlázási modellek vagy más jellemzők szerint.


## <a name="sku-settings"></a>Termékváltozat beállításai

Amikor új ajánlatot hoz létre, nincs az ajánlathoz társított skus. Új termékváltozat létrehozásához kövesse az alábbi lépéseket:

- Az **IoT Edge-modulok > új ajánlat** lapon válassza a **SDO-k** lapot.
- A termékváltozatban válassza **a + Új termékváltozat** lehetőséget a párbeszédpanel megnyitásához.

  ![Új Termékváltozat gomb az Új ajánlat lapon az IoT Edge modulokhoz](./media/iot-edge-module-skus-tab-new-sku.png)

- Az **Új termékváltozat** párbeszédpanelen adja meg a termékváltozat azonosítóját, majd kattintson az **OK gombra.**
(Az alábbi táblázat az azonosító elnevezési konvencióit tartalmazza.)

A **termékváltozatok** lap frissül, és megjeleníti a termékváltozat konfigurálásához szerkesztett mezőket. A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Termékváltozat-azonosító\***       | A termékváltozat azonosítója. Ez a név legfeljebb 50 karakterből áll, amely kisméretű alfanumerikus karakterekből vagy kötőjelekből (-) áll, de nem végződhet kötőjellel. **Megjegyzés:** Ezt a nevet az ajánlat közzététele után nem módosíthatja. A név nyilvánosan látható a termék URL-címében. |
|  |  |


## <a name="sku-details"></a>SKU részletek

Konfigurálja a **termékváltozat adatait** annak meghatározásához, hogy a termékváltozat hogyan jelenjen meg az Azure Marketplace-en és az Azure Portal-webhelyeken.

![IoT Edge modul sku metaadatai](media/iot-edge-module-skus-tab-metadata.png)

Az alábbi táblázat a Termékváltozat részletei csoport mezőinek célját, tartalmát és **formázását ismerteti.** A kötelező mezőket csillag (*) vádolja.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Cím\***        | A termékváltozat címe. Legfeljebb 50 karakter hosszú. <br/> Megjelenik az Azure Portalon, és alapértelmezett modulnévként lesz használva (szóközök és speciális karakterek nélkül), amikor telepítve van. Az alábbi képeken pontosan láthatja, hogy hol jelenik meg ez a mező.|
| **Összefoglaló\***      | A termékváltozat rövid összefoglalása. Legfeljebb 100 karakter hosszú. **Ne** foglalja össze az ajánlatot, csak a termékváltozat.  Ez az összefoglaló az Azure Piactéren jelenik meg. Az alábbi képeken pontosan láthatja, hogy hol jelenik meg ez a mező.|
| **Leírás\***  | A termékváltozat rövid leírása. Legfeljebb 3000 karakter hosszú. Ne írja le az ajánlatot, de csak ez a termékváltozat. Megjelenik az azure-piacon és az Azure Portalon. Az Azure Portalon hozzáfűzi a Marketplace-leírás a Piactér lapon meghatározott ajánlat leírása.  Ez lehet ugyanaz, mint a Termékváltozat összegzése. Az alábbi képeken pontosan láthatja, hogy hol jelenik meg ez a mező.|
| **A termékváltozat elrejtése\*** | Tartsa meg az alapértelmezett beállítást, amely **nem.** |
|  |  |


### <a name="sku-example"></a>Példa termékváltozatra

 Az alábbi példák bemutatják, hogyan jelennek meg a Termékváltozat **címe,** **az Összegzés**és a **Leírás** mezők különböző nézetekben.
 

#### <a name="on-the-azure-marketplace-website"></a>Az Azure Marketplace webhelyén:

- Ha ránézünk SKU részletek:

    ![Hogyan jelennek meg a nagykonókészletek az Azure Marketplace webhelyén?](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Az Azure Portal webhelyén:

- A ska-k böngészése során:

    ![Hogyan jelenik meg az IoT Edge-modul az Azure Portal on-#1](media/iot-edge-module-portal-browse.png)

    ![Hogyan jelenik meg az IoT Edge-modul az Azure Portal on-#2](media/iot-edge-module-portal-product-picker.png)

- A sku-k keresésekor:

    ![Hogyan jelenik meg az IoT Edge-modul az Azure Portalon való kereséskor?](media/iot-edge-module-portal-search.png)

- Ha ránézünk SKU részletek:

    ![Hogyan jelenik meg az IoT Edge modul, amikor a termék részleteit a portálon keresi](./media/iot-edge-module-portal-pdp.png)

- A modul telepítésekor:
    
    ![Hogyan jelenik meg az IoT Edge-modul az üzembe helyezéskor?](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Termékváltozat tartalma

Az **Edge module images csoportban**adja meg az IoT Edge-modul feltöltéséhez szükséges információkat.

Adjon hozzáférést az [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) rendszerhez, amely az IoT Edge-modul lemezképét tartalmazza, hogy feltölthessük és hitelesíthessük azt. Közzététel után az IoT Edge-modul lesz másolva és terjeszthető egy nyilvános tároló beállításjegyzék az Azure Marketplace-en üzemeltetett.

Több platformot is megcélozhat, és címkéken keresztül több verziót is megadhat. További információ a [címkékről és a verziószámozásról az "IoT Edge modul technikai eszközeinek előkészítése" című részben.](./cpp-create-technical-assets.md)

![IoT-peremhálózati modul képei](./media/iot-edge-module-skus-tab-acr.png)

Az alábbi táblázat a **Képtár részletei** és a Képverzió című szakasz mezőinek célját, tartalmát és **formázását**ismerteti.  A kötelező mezőket csillag (*) vádolja.


|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Képtár részletei***    |
| **Előfizetés azonosítója\***        | Az ACR Azure-előfizetésazonosítója.|
| **Erőforráscsoport neve\***      | Az ACR erőforráscsoport neve.|
| **Beállításjegyzék neve\***  | Az ACR rendszerleíró adatbázis neve. Csak a rendszerleíró adatbázis nevét másolja, NEM a bejelentkezési kiszolgáló nevét (például a `azurecr.io`.) nélkül. |
| **Tárház neve\***  | Az IoT Edge-modult tartalmazó ACR tárházneve. **Megjegyzés:** A név beállítása után később nem módosítható. Használjon egyedi nevet annak biztosítására, hogy a fiókjában egyetlen más ajánlatnak sem legyen ugyanaz a neve. |
| **Felhasználónév\*** | Az ACR-hez társított felhasználónév (rendszergazdai felhasználónév). |
| **Jelszó\*** | Az ACR-hez társított jelszó. |
|    |  ***Rendszerképverzió***   |
| **Képcímke vagy kivonatoló\*** | Legalább egy címkét `latest` és egy verziócímkét kell `xx.xx.xx-` tartalmaznia (például azzal kezdve, hogy hol xx egy szám). Több platformot megcélozhatnak. [manifest tags](https://github.com/estesp/manifest-tool) A jegyzékcímkét hivatkozó összes címkét hozzá kell adni, hogy feltölthessük őket. Az IoT Edge-modul több verzióját is hozzáadhatja címkék használatával. Minden jegyzéknév-címkének (kivéve `latest`) az `X.Y-` `X.Y.Z-` X, Y, Z egész számokkal kell kezdődnie. További információ a [címkékről és a verziószámozásról az "IoT Edge modul technikai eszközeinek előkészítése" című részben.](./cpp-create-technical-assets.md) <br/> Ha például `latest` egy címke a `1.0.1-linux-x64`pontjára `1.0.1-linux-arm32`mutat, a , és `1.0.1-windows-arm32`ezt a 6 címkét itt kell hozzáadni. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Segítse ügyfeleit az indításban az alapértelmezett beállítások kal

Adja meg a leggyakoribb beállításokat az IoT Edge-modul üzembe helyezéséhez. Optimalizálja az ügyfelek központi telepítését azáltal, hogy lehetővé teszi számukra, hogy az IoT Edge-modult az alapértelmezett beállításokkal elindítsák.

![Az IoT Edge modul alapértelmezett beállításai a telepítéskor](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

Az alábbi táblázat az **Alapértelmezett útvonalak,** az **Alapértelmezett ikerkívánt tulajdonságok**, **az Alapértelmezett környezeti változók**és az **Alapértelmezett létrehozási beállítások**mezőinek célját, tartalmát és formázását ismerteti.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Alapértelmezett útvonalak**        | Minden alapértelmezett útvonalnévnek és értéknek 512 karakternél rövidebbnek kell lennie. Legfeljebb 5 alapértelmezett útvonalat határozhat meg. Ügyeljen arra, hogy az útvonalértékben megfelelő [útvonalszintaxist használjon.](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) A modulra való hivatkozáshoz használja az alapértelmezett modulnevét, amely szóközök és speciális karakterek nélküli **termékváltozat címe** lesz. Ha más, még nem ismert modulokra szeretne hivatkozni, az `<FROM_MODULE_NAME>` egyezmény segítségével tudassa ügyfeleivel, hogy frissíteniük kell ezeket az adatokat. További információ az [IoT Edge-útvonalakról.](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) <br/> Ha például `ContosoModule` a modul a `ContosoInput` bemeneteket és `ContosoOutput`a kimeneti adatokat figyeli a alkalmazásban, célszerű a következő két alapértelmezett útvonalat meghatározni:<br/>- Név #1:`ToContosoModule`<br/>- Érték #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Név #2:`FromContosoModuleToCloud`<br/>- Érték #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Alapértelmezett iker kívánt tulajdonságok**      | Minden alapértelmezett iker kívánt tulajdonságok neve és értéke kevesebb, mint 512 karakter. Legfeljebb 5 név/iker kívánt tulajdonságdefiniálása. Az iker kívánt tulajdonságok értékeinek érvényes JSON-nak kell lenniük, nem escaped, tömbök nélkül, és a maximális beágyazott hierarchia 4. További információ az [ikerkívánt tulajdonságokról.](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties) <br/> Ha például egy modul két kívánt tulajdonságon keresztül támogatja a dinamikusan konfigurálható frissítési gyakoriságot, célszerű meghatározni a következő alapértelmezett ikerkívánt tulajdonságot:<br/> - Név #1:`RefreshRate`<br/>- Érték #1:`60`|
| **Alapértelmezett környezeti változók**  | Minden alapértelmezett környezeti változó nevének és értékének 512 karakternél rövidebbnek kell lennie. Legfeljebb 5 név/érték környezeti változót határozhat meg. <br/>Ha például egy modulnak el kell fogadnia a használati feltételeket az indítás előtt, a következő környezeti változót határozhatja meg:<br/> - Név #1:`ACCEPT_EULA`<br/>- Érték #1:`Y`|
| **Alapértelmezett createOptions**  | A createOptions karakternek 512 karakternél rövidebbnek kell lennie. Érvényes JSON-nak kell lennie, nem szökésben. További információ a [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules)beállításról. <br/> Ha például egy modulhoz portkötésre van szükség, a következő createOptions értékeket határozhatja meg:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

A **Termékváltozat** beállításainak mentéséhez válassza a Mentés lehetőséget. 


## <a name="next-steps"></a>További lépések

A [Piactér lapon](./cpp-marketplace-tab.md) hozzon létre egy piactér leírását az ajánlathoz.
