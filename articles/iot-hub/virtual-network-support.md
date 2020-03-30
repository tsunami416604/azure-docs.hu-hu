---
title: Az Azure IoT Hub támogatása virtuális hálózatokhoz
description: A virtuális hálózatok kapcsolódási mintázatának használata az IoT Hubbal
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501436"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Az IoT Hub támogatása a virtuális hálózatokhoz

Ez a cikk bemutatja a virtuális hálózat kapcsolódási mintáját, és részletesen bemutatja, hogyan állíthat be egy privát kapcsolati élményt egy IoT-központhoz egy ügyfél tulajdonában lévő Azure virtuális hálózaton keresztül.

> [!NOTE]
> Az IoT Hub ebben a cikkben ismertetett funkciók jelenleg elérhetők a [felügyelt szolgáltatásidentitással létrehozott](#create-an-iot-hub-with-managed-service-identity) IoT-központok számára a következő régiókban: USA keleti régiói, USA déli középső régiói és USA nyugati régiói 2.


## <a name="introduction"></a>Bevezetés

Alapértelmezés szerint az IoT Hub állomásnevek egy nyilvános végponthoz, amely nyilvánosan irányítható IP-címet az interneten keresztül. Amint az alábbi ábrán látható, ez az IoT Hub nyilvános végpont javallottaa a különböző ügyfelek tulajdonában lévő csomópontok között, és az IoT-eszközök által széles hálózatokon keresztül, valamint a helyszíni hálózatokon egyaránt elérhető.

Számos IoT Hub-szolgáltatás, beleértve [az üzenet-útválasztást,](./iot-hub-devguide-messages-d2c.md) [a fájlfeltöltést](./iot-hub-devguide-file-upload.md)és a [tömeges eszközimportálást/-exportálást,](./iot-hub-bulk-identity-mgmt.md) hasonlóképpen kapcsolatot igényel az IoT Hub és az ügyfél tulajdonában lévő Azure-erőforrás között a nyilvános végponton keresztül. Az alábbiakban bemutatott módon ezek a kapcsolódási útvonalak együttesen alkotják az IoT Hub és az ügyfél-erőforrások közötti kimenő forgalmat.
![IoT Hub nyilvános végpont](./media/virtual-network-support/public-endpoint.png)


Több okból is előfordulhat, hogy az ügyfelek szeretnék korlátozni a kapcsolatot az Azure-erőforrások (beleértve az IoT Hub) egy virtuális hálózat, amely a saját és működő. Ezek az okok a következők:

* További biztonsági rétegeket vezet be az IoT-központ hálózati szintű elkülönítésén keresztül azáltal, hogy megakadályozza a nyilvános interneten keresztül a hubhoz való kapcsolódási kitettséget.

* Privát kapcsolati élmény engedélyezése a helyszíni hálózati eszközökből, biztosítva, hogy az adatok és a forgalom közvetlenül az Azure gerinchálózatára kerüljön.

* A kiszivárgási támadások megakadályozása a bizalmas helyszíni hálózatokból. 

* A létrehozott Azure-szintű kapcsolódási mintákat [magánvégpontok](../private-link/private-endpoint-overview.md)használatával követi.


Ez a cikk ismerteti, hogyan érheti el ezeket a célokat [a privát végpontok](../private-link/private-endpoint-overview.md) az IoT Hub hoz való kimenő kapcsolatok hoz, az Azure megbízható első fél szolgáltatások kivétel az IoT Hubról más Azure-erőforrásokkal való kimenő kapcsolatok használatával.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Be- és bekapcsolási kapcsolat az IoT Hubhoz privát végpontok használatával

A privát végpont egy ügyfél tulajdonában lévő virtuális hálózaton belül lefoglalt privát IP-cím, amelyen keresztül egy Azure-erőforrás elérhető. Az IoT hub privát végpontjának létrehozásával engedélyezheti, hogy a virtuális hálózaton belül működő szolgáltatások elérhetők az IoT Hub anélkül, hogy az IoT Hub nyilvános végpontra való elküldését meg kellene küldeni. Hasonlóképpen a helyszíni eszközök is használhatják a [virtuális magánhálózat (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) vagy [az ExpressRoute](https://azure.microsoft.com/services/expressroute/) privát társviszony-létesítési kapcsolatot a virtuális hálózathoz az Azure-ban, majd az IoT Hubhoz (a privát végponton keresztül). Ennek eredményeképpen azok az ügyfelek, akik korlátozni kívánják a kapcsolatot az IoT hub nyilvános végpontjaihoz (vagy esetleg teljesen le szeretnék tiltani) ezt a célt az [IoT Hub tűzfalszabályainak](./iot-hub-ip-filtering.md) használatával érhetik el, miközben megőrzik a hubhoz való kapcsolódást a privát végpont használatával.

> [!NOTE]
> A telepítés fő célja a helyszíni hálózaton belüli eszközök. Ez a beállítás nem ajánlott a nagy kiterjedésű hálózatokban telepített eszközök esetében.

![IoT Hub nyilvános végpont](./media/virtual-network-support/virtual-network-ingress.png)

A folytatás előtt győződjön meg arról, hogy a következő előfeltételek teljesülnek:

* Az IoT hub felügyelt [szolgáltatásidentitással](#create-an-iot-hub-with-managed-service-identity)kell kiépíteni.

* Az IoT-központot a [támogatott régiók](#regional-availability-private-endpoints)egyikében kell kiépíteni.

* Kiépített egy Azure-virtuális hálózatot egy alhálózattal, amelyben a privát végpont jön létre. További részletekért [tekintse meg virtuális hálózat létrehozása az Azure CLI használatával.](../virtual-network/quick-create-cli.md)

* A helyszíni hálózatokon belül működő eszközök esetén állítsa be [a virtuális magánhálózatot (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) vagy [az ExpressRoute](https://azure.microsoft.com/services/expressroute/) privát társviszony-létesítést az Azure virtuális hálózatba.


### <a name="regional-availability-private-endpoints"></a>Regionális elérhetőség (magánvégpontok)

Az IoT Hub által támogatott privát végpontok a következő régiókban létrehozott:

* USA keleti régiója

* USA déli középső régiója

* USA nyugati régiója, 2.


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Privát végpont beállítása az IoT Hub be- és visszakesse számára

Privát végpont beállításához kövesse az alábbi lépéseket:

1. Futtassa a következő Azure CLI parancsot az Azure IoT Hub-szolgáltató előfizetéssel történő újbóli regisztrálásához:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Keresse meg a **privát végpont kapcsolatok** fülre az IoT Hub-portálon (ez a lap csak a támogatott [régiókban](#regional-availability-private-endpoints)lévő IoT-központokban érhető el), és kattintson a **+** jelre egy új privát végpont hozzáadásához.

3. Adja meg az előfizetést, az erőforráscsoportot, a nevet és a régiót az új privát végpont létrehozásához (ideális esetben a privát végpontot ugyanabban a régióban kell létrehozni, mint a hubot; további részletekért lásd a [regionális rendelkezésre állási szakaszt).](#regional-availability-private-endpoints)

4. Kattintson a **Tovább: Erőforrás**, és adja meg az előfizetést az IoT Hub-erőforrás, és válassza a **"Microsoft.Devices/IotHubs"** erőforrástípusként, az IoT Hub neve **erőforrásként,** és **iotHub** cél al-erőforrásként.

5. Kattintson a **Tovább gombra: Konfigurálja** és adja meg a virtuális hálózatot és az alhálózatot a privát végpont létrehozásához. Válassza ki a lehetőséget, hogy integrálja az Azure privát DNS-zóna, ha szükséges.

6. Kattintson a **Tovább gombra: Címkék**, és tetszés szerint adja meg az erőforrás címkéket.

7. Kattintson **a Véleményezés + létrehozás** gombra a privát végponterőforrás létrehozásához.


### <a name="pricing-private-endpoints"></a>Árképzés (magánvégpontok)

A díjszabással kapcsolatos részletekről az [Azure Private Link díjszabása](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Kimenő kapcsolatok az IoT Hubról más Azure-erőforrásokhoz

Az IoT Hubnak hozzá kell férnie az Azure blob storage-hoz, az eseményközpontokhoz, az [üzenet-útválasztáshoz,](./iot-hub-devguide-messages-d2c.md) [a fájlfeltöltéshez](./iot-hub-devguide-file-upload.md)és a [tömeges eszközimportáláshoz/-exportáláshoz,](./iot-hub-bulk-identity-mgmt.md)amely általában az erőforrások nyilvános végpontján keresztül történik. Abban az esetben, ha a tárfiókot, eseményközpontokat vagy szolgáltatásbusz-erőforrást egy virtuális hálózathoz köti, a javasolt konfiguráció alapértelmezés szerint blokkolja az erőforráshoz való kapcsolódást. Következésképpen ez akadályozza az IoT Hub funkcióit, amely hozzáférést igényel ezekhez az erőforrásokhoz.

A helyzet enyhítése érdekében engedélyeznie kell a kapcsolatot az IoT Hub-erőforrásból a tárfiókhoz, az eseményközpontokhoz vagy a szolgáltatásbusz-erőforrásokhoz az **Azure külső megbízható szolgáltatások** beállításán keresztül.

Az előfeltételek a következők:

* Az IoT-központot a [támogatott régiók](#regional-availability-trusted-microsoft-first-party-services)egyikében kell kiépíteni.

* Az IoT Hub kell hozzárendelni egy felügyelt szolgáltatás identitása a hub kiépítése kor. Kövesse a [felügyelt szolgáltatásidentitással rendelkező hub létrehozásáról](#create-an-iot-hub-with-managed-service-identity)szóló utasításokat.


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regionális elérhetőség (megbízható Microsoft-szolgáltatások)

Az Azure megbízható, első féltől származó szolgáltatások kivétele az Azure storage, eseményközpontok és szolgáltatásbusz-erőforrások tűzfal-korlátozásainak megkerülése érdekében csak a következő régiókban támogatott az IoT Hubok számára:

* USA keleti régiója

* USA déli középső régiója

* USA nyugati régiója, 2.


### <a name="pricing-trusted-microsoft-first-party-services"></a>Díjszabás (megbízható Microsoft-szolgáltatások)

A Microsoft külső szolgáltatásainak megbízható kivételfunkciója ingyenes a [támogatott régiók](#regional-availability-trusted-microsoft-first-party-services)IoT-központjaiban. A kiépített tárfiókok, eseményközpontok vagy a szolgáltatásbusz-erőforrások díjai külön-külön érvényesek.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>IoT-központ létrehozása felügyelt szolgáltatásidentitással

A felügyelt szolgáltatás identitása erőforrás-kiépítéskor rendelhető a hubhoz (ez a szolgáltatás jelenleg nem támogatott a meglévő csomópontok esetében), amely megköveteli, hogy az IoT hub a TLS 1.2-t használja minimális verzióként. Ehhez az alábbi ARM erőforrássablont kell használnia:

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
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
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
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
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
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
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

Miután az `name`erőforrásokkal helyettesítette `location` `SKU.name` az `SKU.tier`értékeket , és az Azure CLI segítségével üzembe helyezheti az erőforrást egy meglévő erőforráscsoportban a következők használatával:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Az erőforrás létrehozása után lekérheti a hubhoz rendelt felügyelt szolgáltatásidentitást az Azure CLI használatával:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Miután az IoT Hub felügyelt szolgáltatásidentitással rendelkezik, kövesse a megfelelő szakaszt a [storage-fiókokhoz,](#egress-connectivity-to-storage-account-endpoints-for-routing) [eseményközpontokhoz](#egress-connectivity-to-event-hubs-endpoints-for-routing)és [a szolgáltatásbusz-erőforrásokhoz](#egress-connectivity-to-service-bus-endpoints-for-routing) tartozó útválasztási végpontok beállításához, vagy [a fájlfeltöltés](#egress-connectivity-to-storage-accounts-for-file-upload) és [a tömeges eszközimportálás/-exportálás](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)konfigurálásához.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Kimenő kapcsolatok a tárfiók végpontjaihoz az útválasztáshoz

Az IoT Hub beállítható úgy, hogy üzeneteket irányítson egy ügyfél tulajdonában lévő tárfiókba. Ahhoz, hogy az útválasztási funkció hozzáférhessen egy tárfiókhoz, miközben tűzfalkorlátozások vannak érvényben, az IoT Hubnak felügyelt szolgáltatásidentitással kell rendelkeznie (olvassa el, hogyan [hozhat létre egy hubot felügyelt szolgáltatásidentitással).](#create-an-iot-hub-with-managed-service-identity) A felügyelt szolgáltatásidentitás kiépítése után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásának a tárfiók eléréséhez.

1. Az Azure Portalon keresse meg a tárfiók **hozzáférés-vezérlési (IAM)** fülét, és kattintson a **Hozzáadás** gombra a **Szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki **a Storage Blob Data Contributor** **szerepkörként**, **Az Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás,** mint **hozzáférés hozzárendelése,** és válassza ki az IoT Hub erőforrás nevét a legördülő listában. Kattintson a **Mentés** gombra.

3. Nyissa meg a **tárfiók Tűzfalak és virtuális hálózatok** lapját, és engedélyezze **a hozzáférés engedélyezése a kijelölt hálózatokról** beállítást. A **Kivételek** listában jelölje be a **Megbízható Microsoft-szolgáltatások hozzáférése a tárfiókhoz jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. Az IoT Hub erőforráslapján keresse meg az **Üzenetútválasztás** lapot.

5. Nyissa meg az **Egyéni végpontok szakaszt,** és kattintson a **Hozzáadás gombra.** Válassza a **Tároló végponttípust.**

6. A megjelenő lapon adja meg a végpont nevét, válassza ki a blobstorage-ban használni kívánt tárolót, adja meg a kódolást és a fájlnév formátumát. Válassza ki **a rendszer kijelölt** **hitelesítési típusa** a tárolási végponthoz. Kattintson a **Létrehozás** gombra.

Most az egyéni tárolási végpont van beállítva, hogy a hub rendszer által hozzárendelt identitás, és a tűzfal korlátozások ellenére rendelkezik engedéllyel a tárolási erőforrás eléréséhez. Most már használhatja ezt a végpontot egy útválasztási szabály beállításához.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Kimenő kapcsolatok az eseményközpontok végpontjaihoz az útválasztáshoz

Az IoT Hub beállítható úgy, hogy üzeneteket irányítson az ügyfél tulajdonában lévő eseményközpontok névterébe. Ahhoz, hogy az útválasztási funkció hozzáférhessen egy eseményközpontok erőforrásához, miközben tűzfalkorlátozások vannak érvényben, az IoT Hubnak felügyelt szolgáltatásidentitással kell rendelkeznie (olvassa el, hogyan [hozhat létre egy hubot felügyelt szolgáltatásidentitással).](#create-an-iot-hub-with-managed-service-identity) A felügyelt szolgáltatásidentitás kiépítése után kövesse az alábbi lépéseket, hogy az RBAC engedélyt adjon a hub erőforrás-identitásának az eseményközpontok eléréséhez.

1. Az Azure Portalon keresse meg az eseményközpontok **hozzáférés-vezérlési (IAM)** fülét, és kattintson a **Hozzáadás** gombra a **Szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza **ki az Event Hubs Data Sender** **szerepkörként**, **Az Azure AD-felhasználó, -csoport vagy egyszerű szolgáltatás** hozzáférést **rendel,** és válassza ki az IoT Hub erőforrás nevét a legördülő listában. Kattintson a **Mentés** gombra.

3. Nyissa meg a **Tűzfalak és a virtuális hálózatok** lapot az eseményközpontokban, és engedélyezze a hozzáférés engedélyezése a kijelölt **hálózatokról** beállítást. A **Kivételek** listában jelölje be a **Megbízható Microsoft-szolgáltatások hozzáférése az eseményközpontokhoz jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. Az IoT Hub erőforráslapján keresse meg az **Üzenetútválasztás** lapot.

5. Nyissa meg az **Egyéni végpontok szakaszt,** és kattintson a **Hozzáadás gombra.** Végponttípusként válassza **az Eseményközpontok** lehetőséget.

6. A megjelenő lapon adja meg a végpont nevét, jelölje ki az eseményközpontok névterét és példányát, majd kattintson a **Létrehozás gombra.**

Most az egyéni eseményközpontok végpontja úgy van beállítva, hogy a hub rendszer hez rendelt identitását használja, és a tűzfalkorlátozások ellenére rendelkezik engedéllyel az eseményközpontok erőforrásának eléréséhez. Most már használhatja ezt a végpontot egy útválasztási szabály beállításához.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Kimenő kapcsolat a szolgáltatásbusz végpontjaihoz az útválasztáshoz

Az IoT Hub beállítható úgy, hogy üzeneteket irányítson az ügyfél tulajdonában lévő szolgáltatásbusz-névtérbe. Ahhoz, hogy az útválasztási funkció hozzáférhessen egy szolgáltatásbusz-erőforráshoz, miközben tűzfalkorlátozások vannak érvényben, az IoT Hubnak felügyelt szolgáltatásidentitással kell rendelkeznie (olvassa el, hogyan [hozhat létre egy hubot felügyelt szolgáltatásidentitással).](#create-an-iot-hub-with-managed-service-identity) A felügyelt szolgáltatásidentitás kiépítése után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásának a szolgáltatásbusz eléréséhez.

1. Az Azure Portalon keresse meg a szolgáltatásbusz **hozzáférés-vezérlési (IAM)** fülre, és kattintson a **Hozzáadás** a **Szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki **a Service bus Data Sender** **szerepkörként**, **Az Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás** hozzáférést **rendel,** és válassza ki az IoT Hub erőforrás nevét a legördülő listában. Kattintson a **Mentés** gombra.

3. Nyissa meg a **szervizbusz Tűzfalak és virtuális hálózatok** lapját, és engedélyezze **a hozzáférés engedélyezése a kijelölt hálózatokról** beállítást. A **Kivételek** listában jelölje be a **Megbízható Microsoft-szolgáltatások számára a szolgáltatásbusz elérésének engedélyezése jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. Az IoT Hub erőforráslapján keresse meg az **Üzenetútválasztás** lapot.

5. Nyissa meg az **Egyéni végpontok szakaszt,** és kattintson a **Hozzáadás gombra.** Válassza ki **a Service bus-várólista** vagy **a Service bus témakör** (adott esetben) a végpont típusa.

6. A megjelenő lapon adja meg a végpont nevét, válassza ki a szolgáltatásbusz névterét és várólistáját vagy témakörét (adott esetben). Kattintson a **Létrehozás** gombra.

Most az egyéni szolgáltatásbusz-végpont van beállítva, hogy a hub rendszer hozzárendelt identitás, és a tűzfal korlátozások ellenére rendelkezik engedéllyel a szolgáltatásbusz-erőforrás eléréséhez. Most már használhatja ezt a végpontot egy útválasztási szabály beállításához.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Kimenő kapcsolat a tárfiókokhoz a fájlfeltöltéshez

Az IoT Hub fájlfeltöltési funkciója lehetővé teszi az eszközök számára, hogy fájlokat töltsenek fel egy ügyfél tulajdonában lévő tárfiókba. Ahhoz, hogy a fájl feltöltése működjön, mind az eszközök, mind az IoT Hub kapcsolattal kell rendelkeznie a tárfiókhoz. Ha tűzfalkorlátozások vannak érvényben a tárfiókban, az eszközöknek a támogatott tárfiók bármely mechanizmusát kell használniuk (beleértve a [privát végpontokat,](../private-link/create-private-endpoint-storage-portal.md) [a szolgáltatásvégpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) vagy a [közvetlen tűzfal-konfigurációt)](../storage/common/storage-network-security.md)a kapcsolat megszerzéséhez. Hasonlóképpen, ha tűzfalkorlátozások vannak érvényben a tárfiókban, az IoT Hub-ot úgy kell konfigurálni, hogy a megbízható Microsoft-szolgáltatások kivételen keresztül hozzáférjen a tárolóerőforráshoz. Ebből a célból az IoT Hubnak felügyelt szolgáltatásidentitással kell rendelkeznie (tekintse meg, hogyan [hozhat létre egy hubot felügyelt szolgáltatásidentitással).](#create-an-iot-hub-with-managed-service-identity) A felügyelt szolgáltatásidentitás kiépítése után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásának a tárfiók eléréséhez.

1. Az Azure Portalon keresse meg a tárfiók **hozzáférés-vezérlési (IAM)** fülét, és kattintson a **Hozzáadás** gombra a **Szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki **a Storage Blob Data Contributor** **szerepkörként**, **Az Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás,** mint **hozzáférés hozzárendelése,** és válassza ki az IoT Hub erőforrás nevét a legördülő listában. Kattintson a **Mentés** gombra.

3. Nyissa meg a **tárfiók Tűzfalak és virtuális hálózatok** lapját, és engedélyezze **a hozzáférés engedélyezése a kijelölt hálózatokról** beállítást. A **Kivételek** listában jelölje be a **Megbízható Microsoft-szolgáltatások hozzáférése a tárfiókhoz jelölőnégyzetet.** Kattintson a **Mentés** gombra.

4. Az IoT Hub erőforráslapján keresse meg a **Fájlfeltöltés** lapot.

5. A megjelenő lapon válassza ki a blobstorageban használni kívánt tárolót, konfigurálja a **Fájlértesítési beállításokat**, a **SAS TTL**, **az alapértelmezett TTL** és **a Maximális kézbesítési szám** kívánt módon. Válassza ki **a rendszer kijelölt** **hitelesítési típusa** a tárolási végponthoz. Kattintson a **Létrehozás** gombra.

Most a fájlfeltöltési végpont a hub rendszerhez rendelt identitásának használatára van beállítva, és a tűzfalkorlátozások ellenére is rendelkezik engedéllyel a tárolási erőforrás eléréséhez.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Kimenő kapcsolat a tömeges eszközimportálás/-exportálás tárfiókjaihoz

Az IoT Hub támogatja az eszközök adatainak [importálása/exportálása](./iot-hub-bulk-identity-mgmt.md) funkciót ömlesztve az ügyfél által biztosított tárolási blobból/-ba. Ahhoz, hogy a tömeges importálási/exportálási funkció működjön, mind az eszközök, mind az IoT Hub nak rendelkeznie kell a tárfiókhoz való kapcsolódással.

Ehhez a funkcióhoz az IoT Hub és a tárfiók közötti kapcsolatra van szükség. A szolgáltatásbusz-erőforrások eléréséhez, miközben tűzfalkorlátozások vannak érvényben, az IoT Hub nak rendelkeznie kell egy felügyelt szolgáltatásidentitással [(lásd: hub létrehozása felügyelt szolgáltatásidentitással).](#create-an-iot-hub-with-managed-service-identity) A felügyelt szolgáltatásidentitás kiépítése után kövesse az alábbi lépéseket, hogy RBAC engedélyt adjon a hub erőforrás-identitásának a szolgáltatásbusz eléréséhez.

1. Az Azure Portalon keresse meg a tárfiók **hozzáférés-vezérlési (IAM)** fülét, és kattintson a **Hozzáadás** gombra a **Szerepkör-hozzárendelés hozzáadása** szakaszban.

2. Válassza ki **a Storage Blob Data Contributor** **szerepkörként**, **Az Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás,** mint **hozzáférés hozzárendelése,** és válassza ki az IoT Hub erőforrás nevét a legördülő listában. Kattintson a **Mentés** gombra.

3. Nyissa meg a **tárfiók Tűzfalak és virtuális hálózatok** lapját, és engedélyezze **a hozzáférés engedélyezése a kijelölt hálózatokról** beállítást. A **Kivételek** listában jelölje be a **Megbízható Microsoft-szolgáltatások hozzáférése a tárfiókhoz jelölőnégyzetet.** Kattintson a **Mentés** gombra.

Most már használhatja az Azure IoT REST [API-k at importálási exportálási feladatok létrehozásához](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) a tömeges importálási/exportálási funkció használatával kapcsolatos információkért. Vegye figyelembe, hogy meg `storageAuthenticationType="identityBased"` kell adnia `inputBlobContainerUri="https://..."` a `outputBlobContainerUri="https://..."` kérelem törzsében, és használja, illetve a bemeneti és kimeneti URL-címaként.


Az Azure IoT Hub SDK is támogatja ezt a funkciót a szolgáltatásügyfél beállításjegyzék-kezelőjében. A következő kódrészlet bemutatja, hogyan kezdeményezhet importálási feladatot vagy exportálhat feladatot a C# SDK használatával.

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


Az Azure IoT SDK-k régiókorlátozott verziójának használata a C#, Java és Node.js virtuális hálózati támogatásával:

1. Hozzon létre `EnableStorageIdentity` egy nevű környezeti `1`változót, és állítsa az értékét .

2. Töltse le az SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Python esetén töltse le a korlátozott verziót a GitHubról.

1. Keresse meg a [GitHub kiadási lapját.](https://aka.ms/vnetpythonsdk)

2. Töltse le a következő fájlt, amely a kiadási oldal alján, az **eszközök**nevű fejléc alatt található.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Nyisson meg egy terminált, és keresse meg a letöltött fájlt tartalmazó mappát.

4. Futtassa a következő parancsot a Python Service SDK virtuális hálózatok támogatásával való telepítéséhez:
    > pip telepítés ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokra kattintva többet is megtudhat az IoT Hub funkcióiról:

* [Üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md)
* [Fájl feltöltése](./iot-hub-devguide-file-upload.md)
* [Tömeges eszköz importálása/exportálása](./iot-hub-bulk-identity-mgmt.md) 
