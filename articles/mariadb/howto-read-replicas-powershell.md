---
title: Olvasási replikák kezelése – Azure PowerShell-Azure Database for MariaDB
description: Ismerje meg, hogyan állíthat be és kezelhet olvasási replikákat Azure Database for MariaDB a PowerShell használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 0280d69dc552b776457ff28d19968f6494a846ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707946"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB a PowerShell használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MariaDB szolgáltatásban a PowerShell használatával. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Az olvasási replikákat a PowerShell használatával hozhatja létre és kezelheti.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a főkiszolgáló a fenti díjszabási szintek egyikében van.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

A következő paranccsal hozhat létre olvasási replika-kiszolgálót:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

A `New-AzMariaDbServerReplica` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Az az erőforráscsoport, amelyben a replika-kiszolgáló létrejön.  |
| Name | mydemoreplicaserver | A létrehozott új replika-kiszolgáló neve. |

Egy több régióból származó olvasási replika létrehozásához használja a **Location** paramétert. Az alábbi példa egy replikát hoz létre az **USA nyugati** régiójában.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md).

Alapértelmezés szerint az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a főkiszolgáló, kivéve ha meg van adva a **SKU** paraméter.

> [!NOTE]
> Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a főkiszolgálónál egyenlő vagy nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

### <a name="list-replicas-for-a-master-server"></a>Főkiszolgáló replikáinak listázása

Egy adott főkiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

A `Get-AzMariaDReplica` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Az az erőforráscsoport, amelybe a replika-kiszolgáló létre lesz hozva.  |
| ServerName | mydemoserver | A főkiszolgáló neve vagy azonosítója. |

### <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Az olvasási replika kiszolgáló törlését a parancsmag futtatásával teheti meg `Remove-AzMariaDbServer` .

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A főkiszolgálók törléséhez futtathatja a `Remove-AzMariaDbServer` parancsmagot.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Database for MariaDB kiszolgáló újraindítása a PowerShell-lel](howto-restart-server-powershell.md)
