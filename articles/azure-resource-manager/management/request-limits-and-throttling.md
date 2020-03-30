---
title: Kérelemkorlátozások és -szabályozás
description: Bemutatja, hogyan kell használni a szabályozás ta- és előfizetés-kezelői kérelmek, ha elérte az előfizetési korlátokat.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239363"
---
# <a name="throttling-resource-manager-requests"></a>Erőforrás-kezelő kéréseinek szabályozása

Ez a cikk azt ismerteti, hogy az Azure Resource Manager hogyan szabályozza a kérelmeket. Bemutatja, hogyan követheti nyomon a korlát elérése előtt megmaradó kérelmek számát, és hogyan válaszolhat, ha elérte a korlátot.

A fojtás két szinten történik. Az Azure Resource Manager szabályozza az előfizetésés a bérlő kérelmeket. Ha a kérelem az előfizetés és a bérlő szabályozási korlátja alatt van, az Erőforrás-kezelő továbbítja a kérelmet az erőforrás-szolgáltatónak. Az erőforrás-szolgáltató a műveletekhez igazított szabályozási korlátokat alkalmaz. Az alábbi képen bemutatja, hogyan szabályozás a kérelem a felhasználó tól az Azure Resource Manager és az erőforrás-szolgáltató.

![Sávszélesség-szabályozás kérése](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Előfizetési és bérlői korlátok

Minden előfizetési szintű és bérlői szintű művelet szabályozási korlátok hatálya alá tartozik. Az előfizetési kérelmek olyan kérelmek, amelyek az előfizetés-azonosító átadását, például az előfizetéserőforrás-csoportok lekérését foglalják magukban. A bérlői kérelmek nem tartalmazzák az előfizetés-azonosítót, például az érvényes Azure-helyek lekérését.

Az óránkénti alapértelmezett szabályozási korlátok az alábbi táblázatban láthatók.

| Hatókör | Műveletek | Korlát |
| ----- | ---------- | ------- |
| Előfizetés | Olvas | 12000 |
| Előfizetés | Törli | 15 000 |
| Előfizetés | Írja | 1200 |
| Bérlő | Olvas | 12000 |
| Bérlő | Írja | 1200 |

Ezeknek a korlátoknak a hatóköre a kéréseket intéző rendszerbiztonsági tag (felhasználó vagy alkalmazás) és az előfizetés azonosítója vagy a bérlő azonosítója. Ha a kérések több rendszerbiztonsági tagból érkeznek, akkor az előfizetésben vagy a bérlőben a korlát nagyobb, mint óránként 12 000 olvasás és 1200 írás.

Ezek a korlátozások minden Egyes Azure Resource Manager-példányra vonatkoznak. Minden Azure-régióban több példány található, és az Azure Resource Manager minden Azure-régióban telepítve van.  Tehát a gyakorlatban a határértékek magasabbak, mint ezek a korlátok. A kérelmeket egy felhasználó általában kezeli az Azure Resource Manager különböző példányai.

## <a name="resource-provider-limits"></a>Erőforrás-szolgáltatói korlátok

Az erőforrás-szolgáltatók saját sávszélesség-szabályozási korlátjukat alkalmazzák. Mivel az Erőforrás-kezelő szabályszabályozza az elsődleges azonosító tésszel, és az Erőforrás-kezelő példánya szerint, az erőforrás-szolgáltató több kérést kaphat, mint az előző szakaszalapértelmezett korlátai.

Ez a szakasz néhány széles körben használt erőforrás-szolgáltató szabályozási korlátait ismerteti.

### <a name="storage-throttling"></a>Tárolási szabályozás

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Hálózati sávszélesség-szabályozás

A Microsoft.Network erőforrás-szolgáltató a következő szabályozási korlátokat alkalmazza:

| Művelet | Korlát |
| --------- | ----- |
| írás / törlés (PUT) | 1000 percenként |
| olvasás (GET) | 10000 5 percenként |

### <a name="compute-throttling"></a>Számítási szabályozás

A számítási műveletek szabályozási korlátairól az [API-szabályozási hibák elhárítása – Számítási hiba című](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)témakörben talál további információt.

A virtuálisgép-méretezési csoporton belüli virtuálisgép-példányok ellenőrzéséhez használja a [Virtuálisgép-méretezési készletek műveletet.](/rest/api/compute/virtualmachinescalesetvms) Használja például a [virtuális gép méretezési készletének virtuális gépekkel –](/rest/api/compute/virtualmachinescalesetvms/list) paraméterekkel rendelkező listája a virtuálisgép-példányok energiagazdálkodási állapotának ellenőrzéséhez. Ez az API csökkenti a kérelmek számát.

### <a name="azure-resource-graph-throttling"></a>Az Azure Resource Graph szabályozása

[Az Azure Resource Graph](../../governance/resource-graph/overview.md) korlátozza a kérelmek számát a műveletekhez. A cikkben a fennmaradó kérelmek meghatározásához szükséges lépések, valamint a korlát elérésekor a válaszadás irása ira is vonatkoznak az Erőforrás-diagramra. A Resource Graph azonban saját korlátot és alaphelyzetbe állítást állít be. További információ: [Resource Graph szabályozásfejlécek](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="error-code"></a>Hibakód

Amikor eléri a korlátot, a **429-es**HTTP-állapotkód túl sok kérés jelenik meg. A válasz tartalmaz egy **Újrapróbálkozás utáni** értéket, amely megadja, hogy az alkalmazásnak hány másodpercet kell várnia (vagy alvó állapotba kell várnia) a következő kérelem elküldése előtt. Ha az újrapróbálkozási érték letelte előtt küld egy kérelmet, a rendszer nem dolgozza fel a kérést, és új újrapróbálkozási értéket ad vissza.

A megadott időre való várakozás után bezárhatja és újra megnyithatja a kapcsolatot az Azure-ral. A kapcsolat alaphelyzetbe állításával csatlakozhat az Azure Resource Manager egy másik példányához.

Ha egy Azure SDK-t használ, az SDK automatikus újrapróbálkozási konfigurációval rendelkezhet. További információ: [Az Azure-szolgáltatások újrapróbálkozási útmutatója.](/azure/architecture/best-practices/retry-service-specific)

Egyes erőforrás-szolgáltatók 429-et adnak vissza, hogy bejelentsenek egy átmeneti problémát. A probléma lehet egy túlterhelési állapot, amelyet közvetlenül nem okoz a kérés. Vagy ideiglenes hibát jelenthet a célerőforrás vagy a függő erőforrás állapotával kapcsolatban. A hálózati erőforrás-szolgáltató például 429-et ad vissza az **ÚjrapróbálhatóHibaDueToAnotherOperation** hibakóddal, ha a célerőforrást egy másik művelet zárolta. Annak megállapításához, hogy a hiba a szabályozásból vagy egy ideiglenes feltételből származik-e, tekintse meg a hiba részleteit a válaszban.

## <a name="remaining-requests"></a>Fennmaradó kérelmek

A fennmaradó kérelmek számát a válaszfejlécek vizsgálatával határozhatja meg. Az olvasási kérelmek a fejlécben a fennmaradó olvasási kérelmek számának értékét adják vissza. Az írási kérelmek tartalmazzák a fennmaradó írási kérelmek számát. Az alábbi táblázat az okat az értékeket megvizsgálható válaszfejléceket ismerteti:

| Válasz fejléce | Leírás |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-olvasás |Az előfizetés hatóköre beolvassa a fennmaradó olvasást. Ez az érték olvasási műveleteknél ad vissza. |
| x-ms-ratelimit-remaining-subscription-writes |Előfizetés hatókörrel írt írások maradt. Ezt az értéket az írási műveletek során adja vissza a rendszer. |
| x-ms-ratelimit-remaining-tenant-olvasás |A bérlő hatóköre még olvas |
| x-ms-ratelimit-remaining-tenant-writes |A bérlő hatókörrel írt írásai |
| x-ms-ratelimit-remaining-subscription-resource-requests |Előfizetés hatókörrel kapcsolatos erőforrástípus-kérelmek hátralévő.<br /><br />Ez a fejlécérték csak akkor kerül visszaadásra, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. Az Erőforrás-kezelő ezt az értéket adja hozzá az előfizetés olvasása vagy írása helyett. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Előfizetés hatókörrel rendelkező erőforrástípus-beszedési kérelmek hátra.<br /><br />Ez a fejlécérték csak akkor kerül visszaadásra, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. Ez az érték a fennmaradó beszedési kérelmek (listaerőforrások) számát adja meg. |
| x-ms-ratelimit-remaining-tenant-resource-requests |A bérlő hatókörrel rendelkező erőforrástípus-kérelmek hátravannak.<br /><br />Ez a fejléc csak bérlői szintű kérelmekhez kerül hozzáadásra, és csak akkor, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. Az Erőforrás-kezelő ezt az értéket adja hozzá a bérlő olvasása vagy írása helyett. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Bérlőhatókör-erőforrás típusú gyűjtemény kérelmek hátra.<br /><br />Ez a fejléc csak bérlői szintű kérelmekhez kerül hozzáadásra, és csak akkor, ha egy szolgáltatás felülbírálta az alapértelmezett korlátot. |

Az erőforrás-szolgáltató a fennmaradó kérelmekre vonatkozó információkat is visszaadhat válaszfejléceket. A számítási erőforrás-szolgáltató által visszaadott válaszfejlécekről a [Hívásdíj információs válaszfejlécei](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)című témakörben talál további információt.

## <a name="retrieving-the-header-values"></a>A fejlécértékek beolvasása

Ezeknek a fejlécértékeknek a beolvasása a kódban vagy a parancsfájlban nem különbözik a fejlécértékek beolvasását. 

A **C#** mezőben például a fejlécértékét egy **válasz** nak nevezett **HttpWebResponse** objektumból a következő kóddal lehet beolvasni:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

A **PowerShellben**a fejlécértékét egy Invoke-WebRequest műveletből olvassa be.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

A teljes PowerShell-példát az Erőforrás-kezelő előfizetési korlátainak ellenőrzése című témakörben [taszítja.](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)

Ha meg szeretné tekinteni a fennmaradó hibakeresési kérelmeket, megadhatja a **-Debug** paramétert a PowerShell-parancsmagon. **PowerShell**

```powershell
Get-AzResourceGroup -Debug
```

Amely számos értéket ad vissza, beleértve a következő válaszértéket:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Az írási korlátok beírásához használjon írási műveletet: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Amely számos értéket ad vissza, beleértve a következő értékeket:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Az **Azure CLI**rendszerben a fejléc értékét a részletesebb beállítás használatával szerezheti be.

```azurecli
az group list --verbose --debug
```

Amely számos értéket ad vissza, beleértve a következő értékeket:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Az írási korlátok beírásához használjon írási műveletet: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Amely számos értéket ad vissza, beleértve a következő értékeket:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>További lépések

* A teljes PowerShell-példát az Erőforrás-kezelő előfizetési korlátainak ellenőrzése című témakörben [taszítja.](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)
* A korlátokról és kvótákról az [Azure előfizetési és szolgáltatáskorlátai, kvótái és korlátai](../../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál további információt.
* Az aszinkron REST-kérelmek kezeléséről az [Aszinkron Azure-műveletek nyomon követése](async-operations.md)című témakörben olvashat.
