---
title: Az Azure SQL Database felügyelt példányok Vnetjének konfigurálásával |} A Microsoft Docs
description: Ez a témakör ismerteti a virtuális hálózat (VNet) egy Azure SQL Database felügyelt példánya a konfigurációs beállításokat.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: dfcd61abd9f995a9bb848c23143adb99b0620956
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042159"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Virtuális hálózat konfigurálása az Azure SQL Database felügyelt példány

Az Azure SQL Database felügyelt példányain telepíteni kell egy Azure-ban [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md). A központi telepítés lehetővé teszi, hogy a következő esetekben: 
- Felügyelt példány csatlakozik közvetlenül egy a helyszíni hálózatból 
- Felügyelt példány csatlakozik a csatolt kiszolgáló vagy egy másik helyszíni adattár 
- Felügyelt példány csatlakozik az Azure-erőforrások  

## <a name="plan"></a>Felkészülés

Tervezze meg, hogyan kívánja üzembe helyezni egy virtuális hálózat, az alábbi kérdésekre adott válaszai segítségével felügyelt példány: 
- Tervezi egy vagy több felügyelt példányok üzembe helyezéséhez? 

  A felügyelt példányok száma a felügyelt példányok lefoglalni az alhálózat minimális mérete határozza meg. További információkért lásd: [alhálózat méretét határozza meg a felügyelt példány](#determine-the-size-of-subnet-for-managed-instances). 
- Nem kell a felügyelt példány üzembe egy meglévő virtuális hálózaton, vagy hoz létre egy új hálózati? 

   Ha azt tervezi, használja a meglévő virtuális hálózattal, módosítania, hogy a felügyelt példány megfeleljen a hálózati konfigurációt. További információkért lásd: [meglévő virtuális hálózat módosítása a felügyelt példány](#modify-an-existing-virtual-network-for-managed-instances). 

   Ha azt tervezi, hozzon létre új virtuális hálózatot, [új virtuális hálózat létrehozása felügyelt példány](#create-a-new-virtual-network-for-a-managed-instance).

## <a name="requirements"></a>Követelmények

Felügyelt példány létrehozásához (a felügyelt példány alhálózatára) dedikált alhálózatán belül a virtuális hálózat, amely megfelel az alábbi követelményeknek:
- **Alhálózat dedikált**: A felügyelt példány alhálózatára nem tartalmazhat bármilyen hozzá társított más felhőalapú szolgáltatás, és nem lehet egy átjáró-alhálózatot. Nem lesz képes a felügyelt példány létrehozása egy felügyelt példányra naplóátvitelen kívüli egyéb erőforrásokra tartalmazó alhálózathoz, és nem adhatja később hozzá más erőforrásokhoz az alhálózat.
- **Kompatibilis hálózati biztonsági csoport (NSG)**: egy NSG-t a felügyelt példány alhálózatára társított tartalmaznia kell a szabályok az alábbi táblázatban (kötelező bejövő biztonsági szabályokat és kötelező kimenő biztonsági szabályok) található egyéb szabályok előtt látható. Hálózati biztonsági csoportok segítségével teljes mértékben ki férhet hozzá a felügyelt példány adatok végpont 1433-as porton a kimenő forgalmának szűrésével. 
- **Kompatibilis a felhasználó által definiált útvonaltábla (UDR)**: A felügyelt példány alhálózatára rendelkeznie kell egy felhasználó útválasztási táblázatot az **0.0.0.0/0 Internet következő ugrási típusú** , a hozzárendelt kötelező udr-t. Emellett egy udr-t, hogy irányítja a forgalmat, amely rendelkezik a cél virtuális hálózati átjáró vagy virtuális hálózati berendezésre (NVA) keresztül a helyszíni privát IP-címtartományok is hozzáadhat. 
- **Nem kötelező egyéni DNS**: Ha egy egyéni DNS a virtuális netword van megadva, az Azure rekurzív feloldó IP-címet (például a 168.63.129.16) hozzá kell adni a listához. További információkért lásd: [egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md). Az egyéni DNS-kiszolgáló a következő tartományok és az altartományokra feloldásához képesnek kell lennie: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, és *microsoftonline-p.com*. 
- **Nincsenek Szolgáltatásvégpontok**: A felügyelt példány alhálózatára nem rendelkeznie kell egy hozzá társított végpontot. Győződjön meg arról, hogy szolgáltatás végpontok lehetőség le van tiltva a virtuális hálózat létrehozásakor.
- **Elegendő IP-címek**: A felügyelt példány alhálózatára rendelkeznie kell a legalább 16 IP-címek (javasolt a minimális érték 32 IP-címek). További információkért lásd: [alhálózat méretét határozza meg a felügyelt példány](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Nem helyezhet üzembe egy új felügyelt példány, ha a cél alhálózat nem kompatibilis az összes követelménynek. Felügyelt példány létrejön, amikor egy *hálózati házirend-leképezés* hálózati konfiguráció nem megfelelő módosítható az alhálózaton van alkalmazva. Az alhálózatról, az utolsó példány eltávolítása után a *hálózati házirend-leképezés* is el lesz távolítva

### <a name="mandatory-inbound-security-rules"></a>Kötelező bejövő biztonsági szabályok 

| NÉV       |PORT                        |PROTOKOLL|FORRÁS           |CÉL|A MŰVELET|
|------------|----------------------------|--------|-----------------|-----------|------|
|felügyelet  |9000, 9003, 1438, 1440, 1452|TCP     |Bármelyik              |Bármelyik        |Engedélyezés |
|mi_subnet   |Bármelyik                         |Bármelyik     |MI ALHÁLÓZAT        |Bármelyik        |Engedélyezés |
|health_probe|Bármelyik                         |Bármelyik     |AzureLoadBalancer|Bármelyik        |Engedélyezés |

### <a name="mandatory-outbound-security-rules"></a>Kötelező kimenő biztonsági szabályok 

| NÉV       |PORT          |PROTOKOLL|FORRÁS           |CÉL|A MŰVELET|
|------------|--------------|--------|-----------------|-----------|------|
|felügyelet  |80-as, 443-as, 12000|TCP     |Bármelyik              |Bármelyik        |Engedélyezés |
|mi_subnet   |Bármelyik           |Bármelyik     |Bármelyik              |MI ALHÁLÓZAT  |Engedélyezés |

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Alhálózat méretét határozza meg a felügyelt példány

Felügyelt példány létrehozásakor az Azure virtuális gépek kiépítésekor kiválasztott csomagtól függően számos foglal le. Mivel ezek a virtuális gépek társítva az alhálózat, IP-címek igényelnek. Magas rendelkezésre állásának biztosításához a normál működést és a szolgáltatás karbantartás alatt, az Azure további virtuális gépeket is kioszthat. Ennek eredményeképpen az alhálózat szükséges IP-címek száma, az alhálózat által felügyelt példányok száma nagyobb. 

A kialakításból fakadóan felügyelt példány legalább 16, az alhálózat IP-címet kell, és előfordulhat, hogy legfeljebb 256 IP-cím használata. Ennek eredményeképpen alhálózati maszkok/28-as, /24 használja, az alhálózati IP-címtartományok meghatározásakor. 

> [!IMPORTANT]
> Alhálózat 16 IP-címekkel rendelkező mérete korlátozott lehetséges a további felügyelt példány horizontális felskálázás az operációs rendszer nélküli helyreállításra minimális. Erősen ajánlott lehetőséget választva alhálózati előtagot/27-eset vagy alá. 

Ha azt tervezi, az alhálózaton belül több felügyelt példány üzembe helyezése és az alhálózat méretét optimalizálására, a számítás használni ezeket a paramétereket: 

- Az Azure az alhálózat öt IP-címet használ a saját igényei szerint 
- Egyes általános célú példányok van szüksége a két cím 
- Egyes üzletileg kritikus példányok kell négy címét

**Példa**: tervezett három általános célú és két üzleti az kritikus fontosságú felügyelt példányok. Hogy azt jelenti, hogy 5 + 3 * 2 + 2 * 4 = 19 kell IP-címek. IP-címtartományok 2 hatványa határozzák meg, mint az IP-címtartományt 32 kell (2 ^ 5) IP-címeket. Ezért kell lefoglalni az alhálózat/27-eset a alhálózati maszkkal. 

> [!IMPORTANT]
> További fejlesztések fog elavulnak számítási fent látható. 

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>Hozzon létre egy új virtuális hálózatot a felügyelt példányhoz

A létrehozása és konfigurálása virtuális hálózat legegyszerűbben az Azure Resource Manager üzembe helyezési sablon használata.

1. Jelentkezzen be az Azure portálra.

2. Használat **üzembe helyezés az Azure** üzembe helyezése az Azure-felhőben lévő virtuális hálózati gombra:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Ez a gomb megnyílik egy űrlapot, amely segítségével konfigurálhatja a hálózati környezet ahol telepíthet a felügyelt példány.

  > [!Note]
  > Az Azure Resource Manager-sablont fog üzembe helyezni a virtuális hálózat két alhálózattal. Egy alhálózat nevű **ManagedInstances** felügyelt példányok számára fenntartva, és rendelkezik előre konfigurált útválasztási táblázatot, míg a másik alhálózat nevű **alapértelmezett** szolgál, amely hozzá kell férnie a felügyelt források A példány (például az Azure virtuális gépeken). Eltávolíthatja **alapértelmezett** Ha már nincs szüksége alhálózatra.

3. Konfigurálja a hálózati környezet. A következő képernyőn konfigurálhatja a hálózati környezet paraméterei:

![Azure-hálózat konfigurálása](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Előfordulhat, hogy módosítsa a virtuális hálózatok és alhálózatok nevét, és állítsa be a hálózati erőforrásokhoz tartozó IP-címtartományok. Után a "Vásárlás" gombra az űrlap létrehozása, és konfigurálja a környezetet. Ha már nincs szüksége a két alhálózat törölheti az alapértelmezettet. 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Felügyelt példányok egy meglévő virtuális hálózat módosítása 

Kérdések és válaszok ebben a szakaszban bemutatják, hogyan felügyelt példány hozzáadása meglévő virtuális hálózattal. 

**Használ-e a meglévő virtuális hálózat klasszikus vagy Resource Manager üzemi modell?** 

Felügyelt példány csak a Resource Manager virtuális hálózatot hozhat létre. 

**Hozzon létre új SQL felügyeltpéldány-alhálózatot, vagy a meglévőt szeretne használni?**

Ha szeretne, hozzon létre újat: 

- Az alábbi az irányelveket, az alhálózat méretének kiszámítása a [alhálózat méretét határozza meg a felügyelt példányok](#determine-the-size-of-subnet-for-managed-instances) szakaszban.
- Kövesse a [hozzáadása, módosítása vagy törlése egy virtuális hálózat alhálózatához](../virtual-network/virtual-network-manage-subnet.md). 
- Hozzon létre egy útválasztási táblázatot, amely tartalmaz egy bejegyzést, **0.0.0.0/0**, ahogy a következő Ugrás az interneten, és társíthatja azt az alhálózatot a felügyelt példány esetében.  

Ha szeretne létrehozni egy meglévő alhálózaton belül a felügyelt példány, a következő PowerShell-parancsfájlt az alhálózat előkészítése javasoljuk.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
Alhálózat előkészítési három egyszerű lépésben történik:

- Ellenőrzése – a kiválasztott virtuális: nincs és az alhálózat hálózati követelményeiben felügyelt példány érvényesítése
- Megerősítése – felhasználó látható, amely kell végzett készíti elő az alhálózat a felügyelt példány üzembe helyezési és beleegyezését kéri módosításainak egy halmazát
- Készítse elő – virtuális hálózat és alhálózat megfelelően van beállítva

**Egyéni DNS-kiszolgáló konfigurálva van?** 

Ha igen, tekintse meg [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md). 

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md)
- Hozzon létre egy Vnetet, létrehoz egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból történő bemutató oktatóanyagot, tekintse meg a [létrehozása egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)
