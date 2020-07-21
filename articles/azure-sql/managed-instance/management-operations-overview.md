---
title: Felügyeleti műveletek
titleSuffix: Azure SQL Managed Instance
description: Ismerje meg az Azure SQL felügyelt példányok kezelési műveletének időtartamát és ajánlott eljárásait.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531490"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Az Azure SQL felügyelt példányok kezelési műveleteinek áttekintése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>Mik azok a felügyeleti műveletek?
Az Azure SQL felügyelt példánya olyan felügyeleti műveleteket biztosít, amelyek segítségével automatikusan telepítheti az új felügyelt példányokat, frissítheti a példány tulajdonságait, és törölheti a példányokat, ha már nincs rá szükség.

Az Azure-beli [virtuális hálózatokon belüli központi telepítések](../../virtual-network/virtual-network-for-azure-services.md) támogatásához és az ügyfelek elkülönítésének és biztonságának biztosításához az SQL felügyelt példánya [virtuális fürtökre](connectivity-architecture-overview.md#high-level-connectivity-architecture)támaszkodik. A virtuális fürt az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét jelöli. Az üres alhálózat minden felügyelt példányának telepítése lényegében egy új virtuális fürt buildout eredményez.

A központilag telepített felügyelt példányok további műveletei hatással lehetnek a mögöttes virtuális fürtre is. Ezek a műveletek befolyásolják a felügyeleti műveletek időtartamát, mivel a további virtuális gépek üzembe helyezése olyan terheléssel jár, amelyet figyelembe kell venni, amikor új központi telepítéseket vagy frissítéseket tervez a meglévő felügyelt példányokra.

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása).
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló).
- Példány törlése.

## <a name="management-operations-duration"></a>Felügyeleti műveletek időtartama
Általában a virtuális fürtökön végrehajtott műveletek a leghosszabb időt jelentik. A virtuális fürtök műveleteinek időtartama változik – az alábbi értékek általában a meglévő telemetria-adatokon alapulnak:

- **Virtuális fürt létrehozása**: a létrehozás egy szinkron lépés a példányok kezelési műveleteihez. **a műveletek 90%-a 4 órán belül befejeződik**.
- **Virtuális fürtök átméretezése (bővítés vagy zsugorodás)**: a bővítés egy szinkron lépés, míg a zsugorodás aszinkron módon történik (a példányok kezelési műveleteinek időtartamára való hatás nélkül). a **fürt bővítésének 90%-a kevesebb, mint 2,5 óra**.
- **Virtuális fürt törlése**: a törlés egy aszinkron lépés, de [manuálisan is elindítható](virtual-cluster-delete.md) egy üres virtuális fürtön, ebben az esetben a rendszer szinkron módon hajtja végre. a **virtuális fürtök törlésének 90%-a 1,5 órában fejeződik be**.

Emellett a példányok kezelése az üzemeltetett adatbázisokra vonatkozó egyik műveletet is magában foglalhat, ami hosszabb időtartamot eredményez:

- **Adatbázisfájlok csatolása az Azure Storage-ból**: egy szinkron lépés, mint például a számítás (virtuális mag) vagy a általános célú szolgáltatási szinten felfelé vagy lefelé skálázás. a **műveletek 90%-a 5 percen belül befejeződik**.
- **Always On rendelkezésre állási csoport**beültetése: egy szinkron lépés, például a számítási (virtuális mag) vagy a tárolási skálázás a üzletileg kritikus szolgáltatási rétegben, valamint a szolgáltatási réteg általános célúról üzletileg kritikusra (vagy fordítva) való megváltoztatására. A művelet időtartama a teljes adatbázis méretével, valamint az aktuális adatbázis-tevékenységgel (aktív tranzakciók száma) arányos. Az adatbázis-tevékenység egy példány frissítésekor jelentős variancia bevezetését jelentheti a teljes időtartamra. a **műveletek 90%-a 220 GB/óra vagy újabb**.

A következő táblázat összefoglalja a műveleteket és a jellemző általános időtartamokat:

|Kategória  |Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|---------|
|**Üzembe helyezés** |Első példány egy üres alhálózaton|Virtuális fürt létrehozása|a műveletek 90%-a 4 órán belül befejeződik.|
|Üzembe helyezés |Egy másik hardver generációjának első példánya egy nem üres alhálózatban (például az első gen 5 példány egy 4. generációs példánnyal rendelkező alhálózatban)|Virtuális fürt létrehozása *|a műveletek 90%-a 4 órán belül befejeződik.|
|Üzembe helyezés |A 4 virtuális mag első példányának létrehozása üres vagy nem üres alhálózatban|Virtuális fürt létrehozása * *|a műveletek 90%-a 4 órán belül befejeződik.|
|Üzembe helyezés |A következő példány létrehozása a nem üres alhálózaton belül (2., 3. stb. példány)|Virtuális fürtök átméretezése|a műveletek 90%-a 2,5 órában fejeződik be.|
|**Frissítés** |Példány tulajdonságainak módosítása (rendszergazdai jelszó, Azure AD-bejelentkezés, Azure Hybrid Benefit jelző)|n.a.|Legfeljebb 1 perc.|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú szolgáltatási szintet)|Adatbázisfájlok csatolása|a műveletek 90%-a 5 percen belül befejeződik.|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus szolgáltatási szintet)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú)|– A virtuális fürtök átméretezése<br>– Adatbázisfájlok csatolása|a műveletek 90%-a 2,5 órában fejeződik be.|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|**Törlés**|Példány törlése|Az összes adatbázis biztonsági másolatának naplózása|90%-os művelet akár 1 percenként is befejeződik.<br>Megjegyzés: Ha az alhálózat utolsó példánya törölve lett, a művelet 12 óra elteltével a virtuális fürt törlését fogja ütemezni. * *|
|Törlés|Virtuális fürt törlése (felhasználó által kezdeményezett művelet)|Virtuális fürt törlése|a műveletek 90%-a akár 1,5 óráig is befejeződik.|

\*A virtuális fürt hardveres generálásra épül.

\*\*12 óra az aktuális konfiguráció, de a jövőben változhat, így nem kell nehéz függőséget okozni. Ha korábban törölni kell egy virtuális fürtöt (például az alhálózat felszabadításához), tekintse meg az [alhálózat törlése a felügyelt példány törlése után](virtual-cluster-delete.md)című témakört.

## <a name="instance-availability-during-management-operations"></a>Példány rendelkezésre állása a felügyeleti műveletek során

Az SQL felügyelt példánya a frissítési **műveletek során elérhető**, kivéve a frissítés végén előforduló feladatátvétel által okozott rövid állásidőt. A [gyorsított adatbázis-helyreállításnak](../accelerated-database-recovery.md)köszönhetően általában akár 10 másodpercig is eltarthat.

> [!IMPORTANT]
> A felügyelt Azure SQL-példányok számításának vagy tárolásának méretezése nem ajánlott, vagy a szolgáltatási réteg nem módosítható egyszerre a hosszan futó tranzakciók (adatimportálás, adatfeldolgozási feladatok, indexek újraépítése stb.) alapján. A művelet végén elvégezhető adatbázis-feladatátvétel megszakítja az összes folyamatban lévő tranzakciót.

Az SQL felügyelt példánya nem érhető el az ügyfélalkalmazások számára az üzembe helyezési és törlési műveletek során.

## <a name="management-operations-cross-impact"></a>A kezelési műveletek hatásainak következményei

A felügyelt példányokon a felügyeleti műveletek befolyásolhatják az azonos virtuális fürtön belül elhelyezett példányok más felügyeleti műveleteit:

- A virtuális fürtben a **hosszan futó visszaállítási műveletek** más példány-létrehozási vagy skálázási műveleteket fognak tartani ugyanabban az alhálózatban.<br/>**Példa:** Ha van egy hosszú ideig futó visszaállítási művelet, és létezik egy létrehozási vagy méretezési kérelem ugyanabban az alhálózatban, akkor ez a kérés hosszabb ideig tart, mert a folytatás előtt megvárja, amíg a visszaállítási művelet befejeződik.
    
- A rendszer **egy későbbi példány-létrehozási vagy skálázási** műveletet tart fenn egy korábban kezdeményezett példány-létrehozási vagy-példány-méretezéssel, amely a virtuális fürt átméretezését kezdeményezte.<br/>**Példa:** Ha ugyanazon az alhálózaton több létrehozási és/vagy méretezési kérelem is található ugyanazon a virtuális fürtön belül, és az egyikük egy virtuális fürt átméretezését kezdeményezi, akkor a virtuális fürt átméretezését kérő összes kérelem 5 + perccel azután lesz elküldve, hogy a rendszer a folytatás előtt megvárja az átméretezés befejezését.

- Az **5 perces ablakban elküldött műveletek létrehozása/méretezése** párhuzamosan történik.<br/>**Példa:** A rendszer csak egy virtuális fürt átméretezését hajtja végre az 5 perces ablakban elküldött összes művelethez (az első művelet kérésének végrehajtásának pillanatától számítva). Ha egy másik kérelmet több mint 5 perccel az első elküldése után küld el, a rendszer megvárja, amíg a virtuális fürt átméretezése megkezdődik a végrehajtás megkezdése előtt.

> [!IMPORTANT]
> A folyamatban lévő másik művelet miatt megtartott felügyeleti műveletek automatikusan folytatódnak, amikor a feltételek teljesülnek. Nincs szükség felhasználói beavatkozásra az átmenetileg szüneteltetett felügyeleti műveletek folytatásához.

## <a name="canceling-management-operations"></a>Felügyeleti műveletek megszakítása

Az alábbi táblázat összefoglalja, hogyan lehet visszavonni bizonyos felügyeleti műveleteket és a jellemző általános időtartamokat:

Kategória  |Művelet  |Kampány  |Becsült megszakítási időtartam  |
|---------|---------|---------|---------|
|Üzembe helyezés |Példány létrehozása |Nem |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú) |Nem |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Törlés |Példány törlése |Nem |  |
|Törlés |Virtuális fürt törlése (felhasználó által kezdeményezett művelet) |Nem |  |

# <a name="portal"></a>[Portál](#tab/azure-portal)

A kezelési művelet megszakításához lépjen az Áttekintés panelre, és kattintson a folyamatban lévő művelet értesítési mezőjére. A jobb oldalon megjelenik egy képernyő, amelyen a folyamatban lévő művelet megjelenik, és a művelet megszakítására szolgáló gomb jelenik meg. Az első kattintás után a rendszer kérni fogja, hogy kattintson újra, és erősítse meg, hogy meg kívánja szüntetni a műveletet.

[![Művelet megszakítása](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

A megszakítási kérelem elküldését és feldolgozását követően értesítést kap, ha a megszakítás beküldése sikeres volt.

Ha a küldési megszakítási kérelem sikeres, a kezelési művelet végrehajtása néhány perc múlva megszakad, és hiba történik.

![Művelet eredményének megszakítása](./media/management-operations-overview/canceling-operation-result.png)

Ha a megszakítási kérelem meghiúsul, vagy a Mégse gomb nem aktív, az azt jelenti, hogy a felügyeleti művelet nem megszakítható állapotba került, és néhány percen belül befejeződik. A felügyeleti művelet addig folytatja a végrehajtást, amíg be nem fejeződik.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs Azure PowerShell telepítve, tekintse meg [a Azure PowerShell modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps)ismertető témakört.

A felügyeleti művelet megszakításához meg kell adnia a felügyeleti művelet nevét. Ezért először használja a Get parancsot a műveletek listájának beolvasásához, majd az adott művelet megszakításához.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

A részletes parancsok magyarázatát lásd: [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) és [stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest)ismertető témakört.

A felügyeleti művelet megszakításához meg kell adnia a felügyeleti művelet nevét. Ezért először használja a Get parancsot a műveletek listájának beolvasásához, majd az adott művelet megszakításához.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Részletes parancsok magyarázata: [az SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Következő lépések
- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
