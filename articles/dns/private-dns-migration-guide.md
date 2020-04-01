---
title: Örökölt Azure DNS-személyes zónák áttelepítése új erőforrásmodellbe
titleSuffix: Azure DNS
description: Ez az útmutató lépésről lépésre bemutatja, hogyan lehet áttelepíteni az örökölt magán DNS-zónákat a legújabb erőforrásmodellre
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939350"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Örökölt Azure DNS-személyes zónák áttelepítése új erőforrásmodellbe

A nyilvános előzetes verzió során a privát DNS-zónákat "dnszones" erőforrással hozták létre, "zoneType" tulajdonsággal" "Privát" tulajdonsággal. 2019. december 31-e után az ilyen zónák nem támogatottak, és a "dnszones" helyett a "privateDnsZones" erőforrástípust használó GA erőforrásmodellre kell áttelepíteni őket. Az áttelepítési folyamat egyszerű, és egy PowerShell-parancsfájlt biztosítottunk a folyamat automatizálásához. Ez az útmutató lépésenkénti utasítást tartalmaz az Azure DNS-beli privát zónák áttelepítéséhez az új erőforrásmodellbe.

Hogy megtudja, a dnszones források, amelyek áttelepítést igényelnek; az alábbi parancsot az Azure CLI-ben.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy telepítette az Azure PowerShell legújabb verzióját. Az Azure PowerShellről (Az) és a telepítésmódjáról további információt ahttps://docs.microsoft.com/powershell/azure/new-azureps-module-az

Győződjön meg arról, hogy az Az.PrivateDns-modul az Azure PowerShell telepítve van. A modul telepítéséhez nyisson meg egy emelt szintű PowerShell-ablakot (felügyeleti mód), és írja be a következő parancsot

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Az áttelepítési folyamat teljesen automatizált, és nem várható, hogy nem okoz leállást. Ha azonban az Azure DNS-privát zónákat (előzetes verziót) használja egy kritikus éles környezetben, a következő áttelepítési folyamatot kell végrehajtania egy tervezett karbantartási időablakban. Győződjön meg arról, hogy az áttelepítési parancsfájl futtatása közben nem módosítja a privát DNS-zónák konfigurációját vagy rekordkészleteit.

## <a name="installing-the-script"></a>A parancsfájl telepítése

Emelt szintű PowerShell-ablak megnyitása (felügyeleti mód) és a következő parancs futtatása

```powershell
install-script PrivateDnsMigrationScript
```

Írja be az "A" szót, amikor a parancsfájl telepítésére kérik

![A parancsfájl telepítése](./media/private-dns-migration-guide/install-migration-script.png)

Manuálisan is beszerezheti a PowerShell-parancsfájl legújabb verzióját ahttps://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Az áttelepítési parancsfájl nem futtatható az Azure felhőbeli rendszerhéjban, és egy virtuális gépen vagy az internethez csatlakoztatott helyi gépen kell végrehajtani.

## <a name="running-the-script"></a>A parancsfájl futtatása

A következő parancs végrehajtása a parancsfájl futtatásához

```powershell
PrivateDnsMigrationScript.ps1
```

![A parancsfájl futtatása](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Adja meg az előfizetés azonosítóját és jelentkezzen be az Azure-ba

A rendszer kérni fogja az áttelepíteni kívánt privát DNS-zónákat tartalmazó előfizetés-azonosító megadását. A rendszer megkéri, hogy jelentkezzen be Azure-fiókjába. Töltse ki a bejelentkezést, hogy a parancsfájl hozzáférhessen a privát DNS-zóna erőforrásaihoz az előfizetésben.

![Bejelentkezés az Azure-ba](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Jelölje ki az áttelepíteni kívánt DNS-zónákat

A parancsfájl az előfizetésben lévő összes privát DNS-zóna listáját tartalmazza, és kéri, hogy erősítse meg, hogy melyeket szeretné áttelepíteni. Adja meg az "A" szót az összes privát DNS-zóna áttelepítéséhez. A lépés végrehajtása után a parancsfájl új magánDNS-zónákat hoz létre az új erőforrásmodell használatával, és másolja az adatokat az új DSN-zónába. Ez a lépés egyébként sem módosítja a meglévő privát DNS-zónákat.

![DNS-zónák kiválasztása](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>A DNS-feloldás váltása az új DNS-zónákra

Miután a zónákat és rekordokat átmásolta az új erőforrásmodellbe, a parancsfájl kérni fogja, hogy váltsa át a DNS-feloldást új DNS-zónákra. Ez a lépés eltávolítja az örökölt magánDNS-zónák és a virtuális hálózatok közötti társítást. Ha az örökölt zóna nincs csatolva a virtuális hálózatoktól, a fenti lépésben létrehozott új DNS-zónák automatikusan átveszik a virtuális hálózatok DNS-feloldását.

Válassza az "A" lehetőséget az összes virtuális hálózat DNS-feloldásának váltásához.

![Névfeloldás váltása](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>A DNS-feloldás ellenőrzése

Mielőtt továbblépne, ellenőrizze, hogy a DNS-zónák DNS-feloldása a várt módon működik-e. Bejelentkezhet az azure-beli virtuális gépekre, és nslookup lekérdezést adhat ki az áttelepített zónákkal kapcsolatban, hogy ellenőrizze, hogy a DNS-feloldás működik-e.

![Névfeloldás ellenőrzése](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Ha úgy találja, hogy a DNS-lekérdezések nem oldódnak fel, várjon néhány percet, majd próbálkozzon újra a lekérdezésekkel. Ha a DNS-lekérdezések a várt módon működnek, írja be az "Y" értéket, amikor a parancsfájl a virtuális hálózat magánDNS-zónából való eltávolítását kéri.

![Névfeloldás megerősítése](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Ha az áttelepített zónák dns-feloldási oka miatt nem a várt módon működik, írja be az "N" értéket a fenti lépésbe, és a parancsfájl visszakapcsolja a DNS-feloldást az örökölt zónákra. Hozzon létre egy támogatási jegyet, és mi segítünk a DNS-zónák áttelepítésében.

## <a name="cleanup"></a>Felesleges tartalmak törlése

Ez a lépés törli az örökölt DNS-zónákat, és csak akkor hajtható végre, ha meggyőződött arról, hogy a DNS-feloldás a várt módon működik. A rendszer kéri, hogy törölje az egyes privát DNS-zónákat. Minden kérdésnél írja be az "Y" értéket, miután ellenőrizte, hogy az adott zónák DNS-feloldása megfelelően működik-e.

![A fölöslegessé vált elemek eltávolítása](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Az automatizálás frissítése

Ha automatizálást használ, beleértve a sablonokat, a PowerShell-parancsfájlokat vagy az SDK használatával kifejlesztett egyéni kódot, frissítenie kell az automatizálást a magánDNS-zónák új erőforrásmodelljének használatához. Az alábbiakban az új privát DNS CLI/PS/SDK dokumentációra mutató hivatkozásoktalálhatók.
* [Azure DNS-személyes zónák REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Azure DNS-privát zónák CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Azure DNS-hálózati PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Az Azure DNS privát zónái SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>További segítségre van szüksége

Hozzon létre egy támogatási jegyet, ha további segítségre van szüksége az áttelepítési folyamattal kapcsolatban, vagy bármilyen okból a fent felsorolt lépések nem működnek az Ön számára. A PowerShell-parancsfájl által létrehozott átiratfájl tartalmazza a támogatási jegyet.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre privát zónát az Azure DNS-ben az [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy [az Azure CLI](./private-dns-getstarted-cli.md)használatával.

* Az Azure DNS-ben a privát zónákkal megvalósítható gyakori [privát zónaforgatókönyvekről](./private-dns-scenarios.md) olvashat.

* Az Azure DNS-ben a privát zónákkal kapcsolatos gyakori kérdésekről és válaszokról, beleértve az adott típusú műveleteknél elvárható viselkedést, olvassa el a [Privát DNS gyakori kérdések című témakört.](./dns-faq-private.md)

* A DNS-zónákról és -rekordokról a [DNS-zónák és rekordok áttekintése című témakörben olvashat.](dns-zones-records.md)

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
