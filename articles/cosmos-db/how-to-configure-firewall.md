---
title: Az Azure Cosmos DB-fiók egy IP-tűzfal konfigurálása
description: Ismerje meg, IP hozzáférés-vezérlési házirendeket a tűzfaltámogatás beállítása az Azure Cosmos DB-adatbázisfiókhoz.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 26f2131fd62ddc83c2a6d93c4cff557402a88463
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281114"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>IP-tűzfal konfigurálása az Azure Cosmos DB-ben

Az IP-tűzfalakban használatával az Azure Cosmos DB-fiókban tárolt adatok biztonságát. Az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a tűzfaltámogatás bejövő. Adhatók meg az IP-tűzfal az Azure Cosmos DB-fiókot a következő módszerek egyikével:

* Az Azure Portalról
* Az Azure Resource Manager-sablonok deklaratív használatával
* Programozott módon az Azure CLI-vel vagy az Azure PowerShell frissítésével keresztül a **ipRangeFilter** tulajdonság

## <a id="configure-ip-policy"></a> Az IP-tűzfal konfigurálása az Azure portal használatával

IP hozzáférés-vezérlési házirend beállítása az Azure Portalon, nyissa meg az Azure Cosmos DB-fiók lapját, és válassza ki **tűzfal és virtuális hálózatok** a navigációs menüben. Módosítsa a **engedélyezze a hozzáférést** értéket a következőre **kiválasztott hálózatok**, majd válassza ki **mentése**. 

![Képernyőfelvétel: hogyan kell az Azure Portalon nyissa meg a tűzfal lap](./media/how-to-configure-firewall/azure-portal-firewall.png)

Amikor IP hozzáférés-vezérlés be van kapcsolva, az Azure Portalon teszi lehetővé IP-címeket, IP-címtartományok és kapcsolók adja meg. Kapcsolók más Azure-szolgáltatások és az Azure portal elérésének lehetővé tétele. A következő szakaszok adnak részletes ezek a kapcsolók.

> [!NOTE]
> Miután engedélyezte IP hozzáférés-vezérlési szabályzat az Azure Cosmos DB-fiókja, az Azure Cosmos DB-fiókba érkező kérelmek gépek kívül az engedélyezett IP-címtartományok listáját a rendszer elutasítja. Böngészés az Azure Cosmos DB-erőforrásokat a portálról is blokkolva van a hozzáférés-vezérlés integritását.

### <a name="allow-requests-from-the-azure-portal"></a>Az Azure Portalon kéréseinek engedélyezéséhez. 

Ha programozott módon engedélyezi egy IP-hozzáférés-vezérlési házirend, hozzá kell az Azure portal IP-címét a **ipRangeFilter** tulajdonság megtartásához. A portál IP-címek a következők:

|Régió|IP-cím|
|------|----------|
|Németország|51.4.229.218|
|Kína|139.217.8.252|
|USA-beli államigazgatás|52.244.48.71|
|Minden más régiókban|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Kiválasztásával engedélyezheti a hozzáférést az Azure Portalra a **engedélyezze a hozzáférést az Azure portal** beállítás, az alábbi képernyőképen látható módon: 

![Képernyőfelvétel az Azure portal hozzáférés engedélyezése:](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Globális Azure-adatközpontok és a más forrásokból, Azure-ban kéréseinek engedélyezéséhez.

Az Azure Cosmos DB-fiókja elérését, amelyek nem biztosítják a statikus IP-cím (például az Azure Stream Analytics és az Azure Functions) szolgáltatásokat, ha a hozzáférés korlátozása érdekében továbbra is használhatja az IP-tűzfalon. Engedélyezi a hozzáférést az Azure Cosmos DB-fiókhoz az ilyen szolgáltatásoktól, adja hozzá az IP-cím 0.0.0.0 engedélyezett IP-címek listájára. A 0.0.0.0 címet korlátozza a kéréseket az Azure Cosmos DB-fiók Azure-adatközpont IP-címtartományból. Ez a beállítás nem engedélyezi a hozzáférést a más IP-tartományokat az Azure Cosmos DB-fiókhoz.

> [!NOTE]
> Ez a beállítás konfigurálja a tűzfalat az Azure-ban, beleértve az Azure-ban üzembe helyezett más ügyfelek előfizetéseiből érkező kérések összes kérelem engedélyezéséhez. Ez a beállítás által engedélyezett IP-címek listájának széles, így azt egy tűzfalszabályzat hatékonyságát korlátozza. Használja ezt a beállítást csak akkor, ha a kérelmek nem statikus IP-címek és alhálózatok a virtuális hálózatok származik. Ezzel a művelettel automatikusan lehetővé teszi a hozzáférést az Azure Portalról, mert az Azure Portalon az Azure-ban üzemel.

Kiválasztásával engedélyezheti a hozzáférést az Azure Portalra a **belül nyilvános Azure-adatközpontok kapcsolatokat fogadjon** beállítás, az alábbi képernyőképen látható módon: 

![Képernyőfelvétel: hogyan kell az Azure Portalon nyissa meg a tűzfal lap](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Az aktuális IP-cím érkező kéréseket

Fejlesztés egyszerűsítése érdekében, az Azure portal segítségével azonosíthatja, és adja hozzá az IP-címét az ügyfélszámítógép az engedélyezett listára. A gépen futó alkalmazások ezután hozzáférhetnek az Azure Cosmos DB-fiókot. 

A portál automatikusan észleli az ügyfél IP-cím. Lehet, hogy a gép ügyfél IP-címét, vagy a hálózati átjáró IP-címét. Ellenőrizze, hogy távolítsa el az IP-címet, az éles számítási feladatok végrehajtása előtt. 

Az IP-címek listáját az aktuális IP-cím hozzáadásához válassza **saját aktuális IP-cím hozzáadása**. Ezután válassza a **Save** (Mentés) lehetőséget.

![A bemutató Képernyőkép az aktuális IP-cím tűzfal beállításainak konfigurálása](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>A cloud servicesből kérelmek

Az Azure-ban a cloud services módon egy közös logikai középső rétegű szolgáltatás üzemeltetéséhez Azure Cosmos DB használatával. Hozzáférés engedélyezése az Azure Cosmos DB-fiók egy felhőalapú szolgáltatásából, hozzá kell adnia a nyilvános IP-cím a felhőalapú szolgáltatás által az Azure Cosmos DB-fiókhoz társított IP-címeket az engedélyezett listára [IP hozzáférés-vezérlési házirendkonfigurálása](#configure-ip-policy). Ez biztosítja, hogy a cloud services szerepkör példányainak férhetnek hozzá az Azure Cosmos DB-fiókot. 

IP-címeket lehet lekérdezni a cloud Services az Azure Portalon, az alábbi képernyőképen látható módon:

![Képernyőfelvétel: a nyilvános IP-cím jelenik meg az Azure Portalon a felhőszolgáltatások](./media/how-to-configure-firewall/public-ip-addresses.png)

Horizontális felskálázás a felhőszolgáltatások szerepkörpéldányok hozzáadásával, ezekhez a példányokhoz automatikusan hozzáférést kap az Azure Cosmos DB-fiókot, mert ugyanazon a felhőszolgáltatáson részei.

### <a name="requests-from-virtual-machines"></a>Virtuális gépek érkező kéréseket

Is [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/) vagy [a virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) középső rétegű szolgáltatások működtetéséhez az Azure Cosmos DB használatával. Adja meg a Cosmos DB-fiókot, hogy engedélyezze a hozzáférést a virtuális gépekről, konfigurálnia kell a nyilvános IP-cím a virtuális gép és/vagy az Azure Cosmos DB-fiókja által engedélyezett IP-címek egyik virtuálisgép-méretezési [ az IP-hozzáférés-vezérlési házirend beállítása](#configure-ip-policy). 

IP-címeket virtuális gépekhez az Azure Portalon kérheti le az alábbi képernyőképen látható módon:

![Képernyőfelvétel az Azure Portalon megjelenő virtuális gép nyilvános IP-cím:](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Ha a virtuálisgép-példányokat ad hozzá a csoporthoz, azok automatikusan hozzáférést kapnak a az Azure Cosmos DB-fiók.

### <a name="requests-from-the-internet"></a>Az internetről érkező kéréseket

Az Azure Cosmos DB-fiók használatakor a számítógépről az interneten lévő ügyfél IP-címét vagy IP-címtartomány a gép hozzá kell adni az engedélyezett listára, az IP-címek a fiókjához.

## <a id="configure-ip-firewall-arm"></a>Az IP-tűzfalak konfigurálása Resource Manager-sablon használatával

Az Azure Cosmos DB-fiókhoz való hozzáférés-vezérlés konfigurálása, ellenőrizze, hogy megadja, hogy a Resource Manager-sablon a **ipRangeFilter** attribútum az engedélyezett IP-címtartományok listáját. Például hozzáadhat a sablonhoz a következő JSON-kódot:

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
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Egy IP-hozzáférés-vezérlési házirend beállítása az Azure CLI-vel

Az alábbi parancs bemutatja, hogyan hozhat létre Azure Cosmos DB-fiókot, IP hozzáférés-vezérlést: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

A tűzfalbeállítások, a meglévő fiók frissítéséhez, futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Az IP-hozzáférés-vezérlési szabályzat hibáinak elhárítása

Az alábbi beállítások segítségével elháríthatja, IP hozzáférés-vezérlési házirend kapcsolatos problémák: 

### <a name="azure-portal"></a>Azure Portal 
Egy IP hozzáférés-vezérlési szabályzat az Azure Cosmos DB-fiók engedélyezésével letiltása összes kérelem a fiókjához a gépek kívül az engedélyezett IP-címtartományok listája. Ahhoz, hogy a portál adatsík műveletek, mint a böngészés tárolók és a dokumentumok lekérdezésére, explicit módon hozzáférést az Azure portál használatával kell a **tűzfal** panel a portálon.

### <a name="sdks"></a>SDK-k 
Gépek, amelyek nem szerepelnek az engedélyezési listához, egy általános SDK-k használatával az Azure Cosmos DB-erőforrások elérésekor **403 Tiltott** válasz nincs további részletekkel. Ellenőrizze az engedélyezett IP-címlistáját a fiókjához, és győződjön meg arról, hogy a megfelelő házirend-konfigurációt az Azure Cosmos DB-fiók van hozzárendelve. 

### <a name="source-ips-in-blocked-requests"></a>Forrás IP-címek a blokkolt kérelmek
Az Azure Cosmos DB-fiók diagnosztikai naplózás engedélyezése. Ezek a naplók megjelenítése az egyes kérések és válaszok. A 403-as visszatérési kód a tűzfallal kapcsolatos üzenetek naplózása. Ezek az üzenetek szűrése, láthatja a forrás IP-címek a blokkolt kérelmek számára. Lásd: [diagnosztikai naplózás az Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Az Azure Cosmos DB-szolgáltatásvégponttal alhálózatból származó kérelmek engedélyezve
Egy alhálózatot a virtuális hálózatban, amely rendelkezik a szolgáltatásvégpont engedélyezve van az Azure Cosmos DB érkező kéréseket küld az Azure Cosmos DB-fiókok a virtuális hálózatot és alhálózatot identitás. Ezek a kérelmek nem rendelkezik a nyilvános IP-címét a forrás, így az IP-szűrők utasítsa el azokat. Engedélyezi a hozzáférést az adott alhálózatok a virtuális hálózatok, adjon hozzá egy hozzáférés-vezérlési lista leírt módon [konfigurálása virtuális hálózat és alhálózat-alapú hozzáférés az Azure Cosmos DB-fiók](how-to-configure-vnet-service-endpoint.md). A alkalmazni tűzfalszabályok esetében akár 15 percet is igénybe vehet.


## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB-fiók egy virtuális hálózati szolgáltatásvégpont beállítása: a következő cikkeket:

* [Virtuális hálózat és alhálózat hozzáférés-vezérlés az Azure Cosmos DB-fiók](vnet-service-endpoint.md)
* [Virtuális hálózat és alhálózat-alapú hozzáférés az Azure Cosmos DB-fiók konfigurálása](how-to-configure-vnet-service-endpoint.md)


