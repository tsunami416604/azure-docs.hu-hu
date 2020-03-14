---
title: Azure IoT Hub-támogatás virtuális hálózatokhoz
description: Virtuális hálózatok kapcsolódási mintájának használata IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 1b250bee302cb305ee613010238283ceac4014ed
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375082"
---
# <a name="iot-hub-support-for-virtual-networks"></a>IoT Hub virtuális hálózatok támogatása

Ez a cikk bemutatja az VNET-kapcsolati mintát, és azt ismerteti, hogyan állítható be egy magánhálózati kapcsolat az IoT-hubhoz az ügyfél által birtokolt Azure-VNET keresztül.

> [!NOTE]
> A jelen cikkben ismertetett IoT hub-funkciók jelenleg a következő régiókban létrehozott IoT Hub számára érhetők el: az USA keleti régiója, az USA déli középső régiója és az USA 2. nyugati régiója.


## <a name="introduction"></a>Introduction (Bevezetés)

Alapértelmezés szerint az IoT Hub állomásnév egy nyilvános végpontra mutat, amely nyilvánosan irányítható IP-címmel rendelkezik az interneten keresztül. Ahogy az alábbi ábrán is látható, ez a IoT Hub nyilvános végpont a különböző ügyfelek tulajdonában lévő hubok között van megosztva, és a IoT-eszközök széles körű hálózatokon, valamint a helyszíni hálózatokon keresztül is elérhetők.

Számos IoT Hub funkció, többek között az [üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md), a [fájlfeltöltés](./iot-hub-devguide-file-upload.md)és a [tömeges eszközök importálása/exportálása](./iot-hub-bulk-identity-mgmt.md) hasonlóan szükséges a IoT hub és az ügyfél által birtokolt Azure-erőforráshoz való kapcsolódás a nyilvános végponton keresztül. Ahogy az alábbi ábrán is látható, ezek a csatlakozási útvonalak együttesen alkotják a kimenő forgalmat a IoT Hubról az ügyfelek erőforrásaira.
![IoT Hub nyilvános végpont](./media/virtual-network-support/public-endpoint.png)


Az ügyfelek több okból is korlátozhatják az Azure-erőforrásokhoz (beleértve a IoT Hub) való kapcsolódást a saját és a VNET keresztül. Ezek az okok a következők:

* A IoT hub hálózati szintű elkülönítése révén további biztonsági rétegeket is bevezetve a központhoz való kapcsolódás megakadályozása a nyilvános interneten keresztül.

* A helyszíni hálózati eszközök privát kapcsolódási élményének lehetővé tétele, amely biztosítja, hogy az adatok és a forgalom közvetlenül az Azure gerinc-hálózatra legyen továbbítva.

* A bizalmas helyszíni hálózatok kiszűrése elleni támadásának megakadályozása. 

* Az Azure-szintű csatlakozási mintákat a [privát végpontok](../private-link/private-endpoint-overview.md)használatával követte.


Ez a cikk azt ismerteti, hogyan valósíthatók meg ezek a célok a IoT Hub való csatlakozáshoz használt [privát végpontok](../private-link/private-endpoint-overview.md) használatával, az Azure-beli megbízható első féltől származó szolgáltatások kivételével, a IoT hubról más Azure-erőforrásokhoz való kapcsolódási kivételként.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Csatlakozás IoT Hub magánhálózati végpontok használatával

A privát végpontok egy olyan magánhálózati IP-cím, amely az Azure-erőforrások elérhetőségét biztosító, az ügyfél tulajdonában lévő VNET van lefoglalva. Ha saját végpontot használ az IoT hub számára, lehetővé teszi a VNET-ben működő szolgáltatások elérését IoT Hub anélkül, hogy forgalmat kellene elküldeni IoT Hub nyilvános végpontjának. Hasonlóképpen, a helyszíni üzemeltetésű eszközök a [virtuális magánhálózati (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) vagy a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privát kapcsolatok használatával is VNET az Azure-ban, és később a saját IoT hub (saját végpontján keresztül). Ennek eredményeképpen azok az ügyfelek, akik szeretnék korlátozni az IoT hub nyilvános végpontokhoz való kapcsolódást (vagy esetleg teljesen letiltják a szolgáltatást), az [IoT hub tűzfalszabályok](./iot-hub-ip-filtering.md) használatával érhetik el ezt a célt, miközben a magánhálózati végponttal megőrzik a kapcsolatot a hubhoz.

> [!NOTE]
> Ennek a beállításnak a fő témája a helyszíni hálózaton belüli eszközök. Ez a beállítás nem ajánlott a nagy kiterjedésű hálózatban üzembe helyezett eszközök esetében.

![IoT Hub nyilvános végpont](./media/virtual-network-support/virtual-network-ingress.png)

A folytatás előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Az IoT hub-t a [támogatott régiók](#regional-availability-private-endpoints)egyikében kell kiépíteni.

* Kiépített egy Azure-VNET egy olyan alhálózattal, amelyben a magánhálózati végpont létre lesz hozva. További részletekért lásd: [virtuális hálózat létrehozása az Azure CLI használatával](../virtual-network/quick-create-cli.md) .

* A helyszíni hálózatokon belül üzemelő eszközökön [virtuális magánhálózati (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) vagy [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -alapú privát kapcsolatok állíthatók be az Azure VNET.


### <a name="regional-availability-private-endpoints"></a>Regionális elérhetőség (privát végpontok)

A IoT Hub által támogatott privát végpontok a következő régiókban hozhatók létre:

* USA keleti régiója

* USA déli középső régiója

* USA nyugati régiója, 2.


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Magánhálózati végpont beállítása IoT Hub bejövő forgalomhoz

Privát végpont beállításához kövesse az alábbi lépéseket:

1. A következő Azure CLI-parancs futtatásával regisztrálja újra az Azure IoT Hub providert az előfizetésével:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Navigáljon a IoT Hub portál **privát Endpoint Connections** lapjára (ez a lap csak a [támogatott régiókban](#regional-availability-private-endpoints)található IoT-hubok esetében érhető el), majd kattintson a **+** jelre egy új privát végpont hozzáadásához.

3. Adja meg az előfizetést, az erőforráscsoportot, a nevet és a régiót, amelyből létre szeretné hozni az új privát végpontot (ideális esetben a magánhálózati végpontot ugyanabban a régióban kell létrehoznia, ahol a hub található). további részletekért lásd a [regionális elérhetőséget ismertető szakaszt](#regional-availability-private-endpoints) .

4. Kattintson a **Tovább gombra: erőforrás**lehetőségre, és adja meg az IoT hub erőforrás előfizetését, és válassza a **"Microsoft. Devices/IotHubs"** lehetőséget az erőforrás típusaként, a IoT hub nevét **erőforrásként**, a **iotHub** pedig a cél alerőforrásként.

5. Kattintson a **Tovább gombra: konfigurálás** lehetőségre, és adja meg a virtuális hálózatot és az alhálózatot a privát végpont létrehozásához a alkalmazásban. Ha szükséges, válassza az Azure Private DNS-zónával való integráció lehetőségét.

6. Kattintson a **Tovább gombra: címkék**, és opcionálisan adja meg az erőforráshoz tartozó címkéket.

7. A privát végpont erőforrás létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.


### <a name="pricing-private-endpoints"></a>Díjszabás (privát végpontok)

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Kimenő kapcsolatok IoT Hubról más Azure-erőforrásokra

IoT Hubnak hozzá kell férnie az Azure Blob Storage-hoz, az Event hubokhoz, a Service Bus erőforrásaihoz az [üzenetek útválasztásához](./iot-hub-devguide-messages-d2c.md), a [fájlfeltöltés](./iot-hub-devguide-file-upload.md)és a [tömeges eszközök importálásához és exportálásához](./iot-hub-bulk-identity-mgmt.md), ami általában az erőforrások nyilvános végpontján történik. Abban az esetben, ha a Storage-fiókot, az Event hubokat vagy a Service Bus-erőforrást egy VNET köti, az ajánlott konfiguráció alapértelmezés szerint letiltja az erőforráshoz való kapcsolódást. Ennek következtében a IoT Hub azon funkcióit akadályozza meg, amelyek hozzáférést igényelnek az adott erőforrásokhoz.

A helyzet enyhítéséhez engedélyeznie kell a IoT Hub-erőforrás kapcsolatát a Storage-fiókkal, az Event hubokkal vagy a Service Bus-erőforrásokkal az **Azure első féltől származó megbízható szolgáltatások** lehetőség használatával.

Az előfeltételek a következők:

* Az IoT hub-t a [támogatott régiók](#regional-availability-trusted-microsoft-first-party-services)egyikében kell kiépíteni.

* A IoT Hub felügyelt szolgáltatás identitását kell hozzárendelni a hub üzembe helyezési idején. Kövesse az utasításokat a [felügyelt szolgáltatás identitásával rendelkező központ létrehozásához](#create-a-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regionális elérhetőség (megbízható Microsoft első féltől származó szolgáltatások)

Az Azure megbízható első féltől származó szolgáltatások kivételt jelentenek az Azure Storage-ba irányuló tűzfal-korlátozások megkerülése érdekében, az Event hubok és a Service Bus-erőforrások csak a következő régiókban IoT hubok esetében:

* USA keleti régiója

* USA déli középső régiója

* USA nyugati régiója, 2.


### <a name="pricing-trusted-microsoft-first-party-services"></a>Díjszabás (megbízható Microsoft első féltől származó szolgáltatások)

A megbízható Microsoft első féltől származó szolgáltatások IoT a [támogatott régiókban](#regional-availability-trusted-microsoft-first-party-services)díjmentes. A kiépített Storage-fiókok, az Event hubok vagy a Service Bus-erőforrások díjai külön vonatkoznak.


### <a name="create-a-hub-with-managed-service-identity"></a>Központ létrehozása felügyelt szolgáltatás identitásával

A felügyelt szolgáltatás identitása erőforrás-kiépítési időszakban rendelhető hozzá a hubhoz (ez a funkció jelenleg nem támogatott a meglévő hubok esetében). Erre a célra az alábbi ARM-erőforrás-sablont kell használnia:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-03-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions>",
            "identity": { "type": "SystemAssigned" },
            "properties": { "minTlsVersion": "1.2" },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-02-01",
            "name": "updateIotHubWithKeyEncryptionKey",
            "dependsOn": [ "<provide-a-valid-resource-name>" ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "0.9.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Devices/IotHubs",
                            "apiVersion": "2020-03-01",
                            "name": "<provide-a-valid-resource-name>",
                            "location": "<any-of-supported-regions>",
                            "identity": { "type": "SystemAssigned" },
                            "properties": { "minTlsVersion": "1.2" },
                            "sku": {
                                "name": "<your-hubs-SKU-name>",
                                "tier": "<your-hubs-SKU-tier>",
                                "capacity": 1
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

Miután kicserélte az erőforrás értékeit `name`, `location`, `SKU.name` és `SKU.tier`, az Azure CLI használatával telepítheti az erőforrást egy meglévő erőforráscsoporthoz a következő használatával:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Az erőforrás létrehozása után lekérheti a hubhoz rendelt felügyelt szolgáltatás identitását az Azure CLI használatával:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Ha a IoT Hub felügyelt szolgáltatás identitásával lett kiépítve, kövesse a megfelelő szakaszt az útválasztási végpontok beállításához [a Storage-fiókok](#egress-connectivity-to-storage-account-endpoints-for-routing), az esemény- [hubok](#egress-connectivity-to-event-hubs-endpoints-for-routing)és a [Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) -erőforrások számára, illetve a [fájlfeltöltés](#egress-connectivity-to-storage-accounts-for-file-upload) és a [csoportos eszközök importálásának és exportálásának](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)konfigurálásához.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Kimenő kapcsolatok a Storage-fiókok végpontjai számára az útválasztáshoz

A IoT Hub konfigurálható úgy, hogy üzeneteket továbbítson az ügyfél által birtokolt Storage-fiókba. Annak engedélyezéséhez, hogy az útválasztási funkció hozzáférjen egy Storage-fiókhoz, amíg a tűzfal korlátozásai teljesülnek, a IoT Hub felügyelt szolgáltatás identitásával kell rendelkeznie (lásd: [hub létrehozása felügyelt szolgáltatás identitásával](#create-a-hub-with-managed-service-identity)). A felügyelt szolgáltatás identitásának kiosztása után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásához a Storage-fiók eléréséhez.

1. A Azure Portal nyissa meg a Storage-fiók **hozzáférés-vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza a **Storage blob-adatközreműködő** **SZEREPKÖR**, **Azure ad-felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget **, és válassza ki a IoT hub** erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiók**számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon az üzenet- **Útválasztás** lapra.

5. Navigáljon az **Egyéni végpontok** szakaszhoz, és kattintson a **Hozzáadás**gombra. Válassza a **tároló** lehetőséget a végpont típusaként.

6. A megjelenített oldalon adja meg a végpont nevét, válassza ki a blob Storage-ban használni kívánt tárolót, adja meg a kódolást és a fájlnév formátumát. Válassza ki a **hitelesítési típusként** **hozzárendelt rendszer** elemet a tárolási végponthoz. Kattintson a **Létrehozás** gombra.

Most, hogy az egyéni tárolási végpont a hub rendszerhez rendelt identitásának használatára van beállítva, és a tűzfal korlátozásai ellenére jogosult a tárolási erőforrás elérésére. Ezt a végpontot mostantól útválasztási szabály beállításához is használhatja.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Kimenő kapcsolat az Event hub-végpontokkal az útválasztáshoz

A IoT Hub konfigurálható úgy, hogy üzeneteket továbbítson az ügyfél által birtokolt Event hub-névtérnek. Ha engedélyezni szeretné, hogy az útválasztási funkció hozzáférjen egy Event hubok-erőforráshoz, amíg a tűzfal korlátozásai teljesülnek, a IoT Hub felügyelt szolgáltatás identitásával kell rendelkeznie (lásd: [hub létrehozása felügyelt szolgáltatás identitásával](#create-a-hub-with-managed-service-identity)). A felügyelt szolgáltatás identitásának kiosztása után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásának az esemény-hubok eléréséhez.

1. A Azure Portal navigáljon az Event hub- **hozzáférés-vezérlés (iam)** lapra, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki a **szerepkör**, az **Azure ad-felhasználó, a csoport vagy az egyszerű szolgáltatásnév** **Event Hubs adatfeladó** lehetőséget **, és válassza** ki a IoT hub erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a **tűzfalak és a virtuális hálózatok** lapra az Event hubokban, és engedélyezze a **hozzáférést a kiválasztott hálózatokból** lehetőséggel. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése az Event hubok**számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon az üzenet- **Útválasztás** lapra.

5. Navigáljon az **Egyéni végpontok** szakaszhoz, és kattintson a **Hozzáadás**gombra. Válassza az **Event hubok** lehetőséget a végpont típusaként.

6. A megjelenített oldalon adja meg a végpont nevét, válassza ki az Event hub-névteret és-példányt, és kattintson a **Létrehozás** gombra.

Most az egyéni Event hub-végpont úgy van beállítva, hogy a hub rendszerhez rendelt identitását használja, és a tűzfal korlátozásai ellenére jogosult az Event hub-erőforrás elérésére. Ezt a végpontot mostantól útválasztási szabály beállításához is használhatja.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Kimenő kapcsolatok a Service Bus-végpontokhoz útválasztáshoz

A IoT Hub konfigurálható úgy, hogy üzeneteket továbbítson az ügyfél által birtokolt Service Bus-névtérhez. Ha engedélyezni szeretné, hogy az útválasztási funkció hozzáférjen egy Service Bus-erőforráshoz, amíg a tűzfal korlátozásai teljesülnek, a IoT Hub felügyelt szolgáltatás identitásával kell rendelkeznie (lásd: [hub létrehozása felügyelt szolgáltatás identitásával](#create-a-hub-with-managed-service-identity)). A felügyelt szolgáltatás identitásának kiosztása után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásához a Service Bus eléréséhez.

1. A Azure Portal nyissa meg a Service Bus hozzáférés- **vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki a **Service Bus-Adatfeladót** **szerepkörként**, **Azure ad-felhasználóként, csoportként vagy egyszerű szolgáltatásnévként** **, és válassza** ki a IoT hub erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Service Bus **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Service Bus**számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon az üzenet- **Útválasztás** lapra.

5. Navigáljon az **Egyéni végpontok** szakaszhoz, és kattintson a **Hozzáadás**gombra. A végpont típusaként válassza a **Service Bus-várólista** vagy a **Service Bus-témakör** (az alkalmazható) lehetőséget.

6. A megjelenített oldalon adja meg a végpont nevét, válassza ki a Service Bus-névteret és-várólistát vagy-témakört (a megfelelő módon). Kattintson a **Létrehozás** gombra.

Most az egyéni Service Bus-végpont úgy van beállítva, hogy a hub rendszerhez rendelt identitását használja, és a tűzfal korlátozásai ellenére jogosult a Service Bus-erőforrás elérésére. Ezt a végpontot mostantól útválasztási szabály beállításához is használhatja.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>A fájlok feltöltésekor használt Storage-fiókokhoz való kimenő kapcsolatok

IoT Hub fájlfeltöltés funkciója lehetővé teszi, hogy az eszközök fájlokat töltsenek fel egy ügyfél tulajdonú Storage-fiókjába. Ha engedélyezni szeretné a fájl feltöltését, mindkét eszköznek és a IoT Hubnak kapcsolódnia kell a Storage-fiókhoz. Ha a Storage-fiókban tűzfal-korlátozások vannak érvényben, az eszköznek a támogatott Storage-fiók mechanizmusát (beleértve a [magánhálózati végpontokat](../private-link/create-private-endpoint-storage-portal.md), a [szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) vagy a [közvetlen tűzfal-konfigurációt](../storage/common/storage-network-security.md)) kell használnia a kapcsolat eléréséhez. Hasonlóképpen, ha a Storage-fiókban tűzfal-korlátozások vannak érvényben, IoT Hub konfigurálnia kell a tárolási erőforrás elérését a megbízható Microsoft Services-kivétel használatával. Erre a célra a IoT Hub felügyelt szolgáltatás identitásával kell rendelkeznie (lásd: [hub létrehozása felügyelt szolgáltatás identitásával](#create-a-hub-with-managed-service-identity)). A felügyelt szolgáltatás identitásának kiosztása után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásához a Storage-fiók eléréséhez.

1. A Azure Portal nyissa meg a Storage-fiók **hozzáférés-vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza a **Storage blob-adatközreműködő** **SZEREPKÖR**, **Azure ad-felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget **, és válassza ki a IoT hub** erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiók**számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

4. A IoT Hub erőforrás lapján navigáljon a **fájlfeltöltés** lapra.

5. A megjelenő oldalon válassza ki a blob Storage-ban használni kívánt tárolót, konfigurálja a **fájl értesítési beállításait**, az **sas TTL**, az **alapértelmezett TTL** és a **maximális kézbesítési** értéket a kívánt módon. Válassza ki a **hitelesítési típusként** **hozzárendelt rendszer** elemet a tárolási végponthoz. Kattintson a **Létrehozás** gombra.

A file upload tárolási végpontja most úgy van beállítva, hogy a hub rendszerhez rendelt identitását használja, és a tűzfal korlátozásai ellenére jogosult a tárolási erőforrás elérésére.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Kilépési kapcsolat a Storage-fiókokkal a tömeges eszközök importálásához/exportálásához

A IoT Hub támogatja az eszközök tömeges [importálására/exportálására](./iot-hub-bulk-identity-mgmt.md) vonatkozó adatokat az ügyfél által megadott Storage-blobba/-ból. Ha engedélyezni szeretné a tömeges importálási/exportálási funkciót, mindkét eszköznek és a IoT Hubnak kapcsolódnia kell a Storage-fiókhoz.

Ehhez a funkcióhoz IoT Hub kapcsolat szükséges a Storage-fiókhoz. Ha tűzfal-korlátozásokkal szeretné elérni a Service Bus-erőforrást, a IoT Hub felügyelt szolgáltatás identitásával kell rendelkeznie (lásd: [hub létrehozása felügyelt szolgáltatás identitásával](#create-a-hub-with-managed-service-identity)). A felügyelt szolgáltatás identitásának kiosztása után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásához a Service Bus eléréséhez.

1. A Azure Portal nyissa meg a Storage-fiók **hozzáférés-vezérlés (iam)** lapját, és kattintson a **Hozzáadás** lehetőségre a **szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza a **Storage blob-adatközreműködő** **SZEREPKÖR**, **Azure ad-felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget **, és válassza ki a IoT hub** erőforrás nevét a legördülő listából. Kattintson a **Mentés** gombra.

3. Navigáljon a Storage-fiók **tűzfalak és virtuális hálózatok** lapjára, és engedélyezze a **hozzáférés engedélyezése a kiválasztott hálózatokból** lehetőséget. A **kivételek** listájában jelölje be a **megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiók**számára jelölőnégyzetet. Kattintson a **Mentés** gombra.

Mostantól használhatja az Azure IoT-REST API az [importálási exportálási feladatok létrehozásához](https://docs.microsoft.com/rest/api/iothub/jobclient/getimportexportjobs) a tömeges importálási/exportálási funkciók használatával kapcsolatos információkért. Vegye figyelembe, hogy a kérés törzsében meg kell adnia a `storageAuthenticationType="identityBased"`t, és a `inputBlobContainerUri="https://..."` és a `outputBlobContainerUri="https://..."` kell használnia a Storage-fiók bemeneti és kimeneti URL-címéhez.


Az Azure IoT Hub SDK a szolgáltatás-ügyfél beállításjegyzék-kezelőjében is támogatja ezt a funkciót. Az alábbi kódrészletből megtudhatja, hogyan kezdeményezzen importálási feladatot vagy exportálási feladatot az C# SDK használatával.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Ha az Azure IoT SDK-k ezen régióra korlátozott verzióját szeretné használni a VNET C#-támogatással a, a Java és a Node. js használatával:

1. Hozzon létre egy `EnableStorageIdentity` nevű környezeti változót, és állítsa az értékét `1`re.

2. Az SDK letöltése:
    - > [Java](https://aka.ms/vnetjavasdk)
    - > [C#](https://aka.ms/vnetcsharsdk)
    - > [Node.js](https://aka.ms/vnetnodesdk)
 
A Python esetében töltse le a korlátozott verziót a Githubról.

1. Navigáljon a [GitHub kiadási oldalára](https://aka.ms/vnetpythonsdk).

2. Töltse le a következő fájlt, amelyet a kiadási oldal alján talál az **eszközök**nevű fejléc alatt.
    > *azure_iot_hub-2.2.0. Limited-py2. py3-none-any. WHL*

3. Nyisson meg egy terminált, és navigáljon a letöltött fájllal rendelkező mappához.

4. Futtassa a következő parancsot a Python Service SDK telepítéséhez a virtuális hálózatok támogatásával:
    > pip install./azure_iot_hub-2.2.0. Limited-py2. py3-none-any. WHL


## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokra kattintva további információt találhat IoT Hub szolgáltatásairól:

* [Üzenet-útválasztás](./iot-hub-devguide-messages-d2c.md)
* [Fájlfeltöltés](./iot-hub-devguide-file-upload.md)
* [Tömeges eszköz importálása/exportálása](./iot-hub-bulk-identity-mgmt.md) 