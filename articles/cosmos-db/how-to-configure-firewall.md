---
title: Útmutató az Azure Cosmos-fiók IP-tűzfal konfigurálása
description: Ismerje meg, IP hozzáférés-vezérlési házirendeket a tűzfaltámogatás beállítása az Azure Cosmos DB-adatbázisfiókhoz.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: e1253d4a43e45fc7df7efbb6d5f9dd4176957ca2
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629095"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>Útmutató az Azure Cosmos-fiók IP-tűzfal konfigurálása

Az IP-tűzfalakban használatával az Azure Cosmos-fiókban tárolt adatok biztonságát. Az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a tűzfaltámogatás bejövő. A következő módszerek egyikét használva az IP-tűzfal állíthatja be az Azure Cosmos-fiók:

1. Az Azure Portalról
2. Az Azure Resource Manager-sablonok deklaratív használatával
3. Programozott módon az Azure CLI-vel vagy az Azure Powershell frissítésével keresztül a **ipRangeFilter** tulajdonság.

## <a id="configure-ip-policy"></a> Az Azure portal használatával IP-tűzfal konfigurálása

Az IP-hozzáférés-vezérlési házirend beállítása az Azure Portalon, keresse meg az Azure Cosmos DB-fiók lapra, kattintson a **tűzfal és virtuális hálózatok** a navigációs menüben, majd módosítsa a **engedélyezze a hozzáférést** érték a **kiválasztott hálózatok**, és kattintson a **mentése**. 

![Képernyőfelvétel: hogyan kell az Azure Portalon nyissa meg a tűzfal lap](./media/how-to-configure-firewall/azure-portal-firewall.png)

Miután IP hozzáférés-vezérlés be van kapcsolva, az Azure portal teszi lehetővé IP-címeket, IP-címtartományok, valamint a kapcsolók más Azure-szolgáltatások és az Azure Portallal való hozzáférés engedélyezéséhez adjon meg. Ezek a kapcsolók részleteit a következő szakaszokban találhatók.

> [!NOTE]
> Miután engedélyezte IP hozzáférés-vezérlési szabályzat az Azure Cosmos-fiókja, a rendszer elutasítja a az Azure Cosmos-fiókba érkező kérések gépek kívül az engedélyezett IP-címtartományok listája. Böngészés az Azure Cosmos DB-erőforrásokat a portálon is lehet blokkolja a hozzáférés-vezérlés integritását.

### <a name="allow-requests-from-the-azure-portal"></a>Az Azure Portalon kéréseinek engedélyezéséhez. 

Ha programozott módon engedélyezi egy IP-hozzáférés-vezérlési házirend, hozzá kell az Azure portal IP-címét a **ipRangeFilter** tulajdonság megtartásához. A portál IP-címek a következők:

|Régió|IP-cím|
|------|----------|
|Németország|51.4.229.218|
|Kína|139.217.8.252|
|USA-beli államigazgatás|52.244.48.71|
|A fenti három kivételével minden régióban|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Kiválasztásával engedélyezheti a hozzáférést az Azure Portalra a **engedélyezze a hozzáférést az Azure portal** lehetőséget az alábbi képernyőképen látható módon: 

![Képernyőfelvétel az Azure portal hozzáférés engedélyezése:](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Globális Azure-adatközpontok és a más forrásokból, Azure-ban kéréseinek engedélyezéséhez.

Ha az Azure Cosmos-fiók elérését, amelyek nem biztosítják a statikus IP-cím, például az Azure Stream analytics szolgáltatásokat az Azure Functions stb. is az IP-tűzfal használatával korlátozhatja a hozzáférést. Az ilyen szolgáltatásoktól engedélyezi a hozzáférést az Azure Cosmos-fiók, a tűzfal beállítást engedélyezni kell. A tűzfal beállítás engedélyezése, az IP-cím-0.0.0.0 hozzáadása az engedélyezett IP-címek listáját. 0.0.0.0 kérelmek korlátozza a az Azure Cosmos-fiók Azure-adatközpont IP-címtartományból. Ez a beállítás nem engedélyezi a hozzáférést a más IP-tartományokat az Azure Cosmos-fiókjába.

> [!NOTE]
> Ez a beállítás konfigurálja a tűzfalat az Azure például kérelmek az Azure-ban üzembe helyezett más ügyfelek előfizetéseiből érkező összes kéréseinek engedélyezéséhez. Ez a beállítás által engedélyezett IP-címek listájának széles, úgy korlátozza a tűzfalházirend hatékonyságát. Ez a beállítás csak akkor, ha az ügyfélkérések statikus IP-címeket vagy alhálózatokat, virtuális hálózatok nem használható. Ezzel a művelettel automatikusan lehetővé teszi a hozzáférést az Azure Portalról, mivel az Azure Portalon az Azure-ban üzemel.

Kiválasztásával engedélyezheti a hozzáférést az Azure Portalra a **fogadja a csatlakozásokat, a nyilvános Azure-adatközpontok** lehetőséget az alábbi képernyőképen látható módon: 

![Képernyőfelvétel: hogyan kell az Azure Portalon nyissa meg a tűzfal lap](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Az aktuális IP-cím érkező kéréseket

Egyszerűbben fejlesztési az Azure portal segítségével azonosíthatja, és adja hozzá az IP-címét az ügyfélszámítógép az engedélyezett listára úgy, hogy a gépen futó alkalmazások hozzáférhessenek-e az Azure Cosmos-fiók. A portál által automatikusan észlelt az ügyfél IP-cím. Elképzelhető, hogy a gép ügyfél IP-címét, vagy a hálózati átjáró IP-címét. Ellenőrizze, hogy távolítsa el az IP-címet, mielőtt az éles környezetben a számítási feladatokat. 

Ahhoz, hogy az aktuális IP-cím, válassza ki a **saját aktuális IP-cím hozzáadása**, amely az aktuális IP-cím ad hozzá az IP-címek listáját, és kattintson a **mentése**.

![A bemutató Képernyőkép az aktuális IP-tűzfal beállításainak konfigurálása](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>A cloud servicesből kérelmek

Az Azure-ban a cloud services módon egy közös üzemeltetéséhez a középső réteg szolgáltatás logikai Azure Cosmos DB használatával. Hozzáférés engedélyezése az Azure Cosmos-fiókjába egy felhőalapú szolgáltatásából, a felhőszolgáltatás a nyilvános IP-cím hozzá kell adni az engedélyezett listára által az Azure Cosmos-fiókjához társított IP-címek [IP hozzáférés-vezérlési házirendkonfigurálása](#configure-ip-policy). Ez biztosítja, hogy a cloud services szerepkör példányainak férhetnek hozzá az Azure Cosmos-fiók. IP-címeket lehet lekérdezni a cloud Services az Azure Portalon, az alábbi képernyőképen látható módon:

![Képernyőfelvétel: a nyilvános IP-cím jelenik meg az Azure Portalon a felhőszolgáltatások](./media/how-to-configure-firewall/public-ip-addresses.png)

Horizontális felskálázás a felhőszolgáltatás további szerepkör-példány hozzáadásával, ezekhez a példányokhoz automatikusan hozzáférést kap az Azure Cosmos-fiók, mivel ugyanazon a felhőszolgáltatáson részét képezik.

### <a name="requests-from-virtual-machines"></a>Virtuális gépek érkező kéréseket

[Virtuális gépek](https://azure.microsoft.com/services/virtual-machines/) vagy [a virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) is használható az Azure Cosmos DB használatával, a középső rétegbeli szolgáltatásokat. A Cosmos fiók konfigurálásával lehetővé teheti hozzáférés virtuális gépekről, nyilvános IP-címek a virtuális gép és/vagy virtuálisgép-méretezési csoport úgy kell konfigurálni az Azure Cosmos-fiók által engedélyezett IP-címek egyik [a IP-cím konfigurálása hozzáférés-vezérlési házirend](#configure-ip-policy). IP-címeket virtuális gépekhez az Azure Portalon kérheti le az alábbi képernyőképen látható módon:

![Képernyőfelvétel az Azure Portalon megjelenő virtuális gép nyilvános IP-cím:](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

További virtuálisgép-példányokat ad hozzá a csoporthoz, azok automatikusan rendelkezésre az Azure Cosmos-fiókhoz való hozzáférést.

### <a name="requests-from-the-internet"></a>Az internetről érkező kéréseket

A Azure Cosmos fiókjába az interneten egy számítógépről, amikor az ügyfél IP-cím vagy IP-címtartomány a gép hozzá kell adni a fiókhoz tartozó IP-cím az engedélyezett listára.

## <a id="configure-ip-firewall-arm"></a>Resource Manager-sablon használatával IP-tűzfal konfigurálása

Konfigurálhatja a hozzáférés-vezérlés az Azure Cosmos-fiókjába, a Resource Manager-sablont kell megadnia a **ipRangeFilter** attribútum az IP-címtartományokat, amelyek kell lennie az engedélyezési listához hozzáadni kívánt listáját. Például hozzáadhat a sablonhoz a következő JSON-kódot:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>Azure CLI-vel IP hozzáférés-vezérlési házirend beállítása

Az alábbi parancs bemutatja, hogyan hozzon létre egy Azure Cosmos-fiókot IP hozzáférés-vezérlés: 

```azurecli-interactive
az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

A tűzfalbeállítások, a meglévő fiók frissítéséhez, futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Problémák elhárítása IP hozzáférés-vezérlési házirend

Az alábbi beállítások segítségével elháríthatja, IP hozzáférés ellenőrzési szabályzat problémák: 

### <a name="azure-portal"></a>Azure Portal 
Egy IP hozzáférés-vezérlési szabályzat az Azure Cosmos-fiók engedélyezése esetén a fiókjához érkező kérések az engedélyezett IP-címtartományok listája kívüli gépek le lesznek tiltva. Ezért, ha portál adatok síkjával végzett műveletek, például böngészési tárolók és a dokumentumok lekérdezésének engedélyezni szeretné, kell explicit módon lehetővé teszi az Azure portál elérésére a **tűzfal** panel a portálon.

### <a name="sdks"></a>SDK-k 
Azure Cosmos-erőforrásokkal, amelyek nem az engedélyezett listában szereplő gépekről SDK-k használatával használatakor **általános 404-es nem talált választ ad vissza semmilyen további részletekkel**. Ellenőrizze az engedélyezett IP-címlistáját a fiókjához, és győződjön meg arról, hogy a megfelelő házirend-konfigurációt a Cosmos-fiók van hozzárendelve. 

### <a name="check-source-ips-in-blocked-requests"></a>Ellenőrizze a forrás IP-címek a blokkolt kérelmek
Engedélyezze a diagnosztikai naplózás az Azure Cosmos-fiókja, ezek a naplók a következő jelenik meg minden egyes kérés- és. **A tűzfallal kapcsolatos üzeneteket belső jelentkezett a 403-as visszatérési kód**. Ezek az üzenetek szűrése, láthatja a forrás IP-címek a blokkolt kérelmek számára. Lásd: [diagnosztikai naplózás az Azure Cosmos DB](logging.md).

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Alhálózat szolgáltatásvégpont engedélyezve van az Azure Cosmos Database érkező kéréseket
Egy alhálózatot a virtuális Hálózattal, az Azure Cosmos DB szolgáltatás végponttal rendelkezik érkező kéréseket küld virtuális Hálózatot és alhálózatot identitását az Azure Cosmos-fiókok engedélyezve van. Ezeket a kérelmeket azok visszautasítja, IP-szűrőket, így nem kell a nyilvános IP-címét a forrás. Virtuális hálózatok adott alhálózatokhoz való hozzáférés engedélyezéséhez adja hozzá a virtuális hálózati hozzáférés-vezérlési lista leírt [konfigurálása virtuális hálózat és alhálózat-alapú hozzáférés az Azure Cosmos-fiókja](how-to-configure-vnet-service-endpoint.md). A alkalmazni tűzfalszabályok esetében akár 15 percet is igénybe vehet.


## <a name="next-steps"></a>További lépések

Konfigurálja a virtuális hálózati szolgáltatásvégpont Azure Cosmos DB-fiókja, tekintse meg a következő cikkeket:

* [Virtuális Hálózatot és alhálózatot az Azure Cosmos-fiókhoz tartozó hozzáférés-vezérlés](vnet-service-endpoint.md)
* [A szerepköralapú hozzáférés az Azure Cosmos-fiók konfigurálása a virtuális hálózatot és alhálózatot](how-to-configure-vnet-service-endpoint.md)


