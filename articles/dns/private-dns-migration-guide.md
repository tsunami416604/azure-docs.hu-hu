---
title: Új erőforrás-modellje-ba való migrálás a régi Azure DNS Private Zones
description: Az útmutató lépésről lépésre utasítást tartalmaz a legújabb erőforrásmodell örökölt privát DNS-zónák áttelepítése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276096"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Régi Azure DNS privát zónái áttelepítés az új erőforrás-modellje

Azt le szállítani egy új API-erőforrás modellt az Azure DNS privát zónái a frissítés előzetes kiadás részeként. Előnézet frissítése új funkciókat biztosítja, és eltávolítja a több korlátozásokat és megkötéseket a kezdeti nyilvános előzetes verzióját. Azonban ezeket az előnyöket nem érhetők el a privát DNS-zónák örökölt API használatával lettek létrehozva. Az új kiadásban kihasználása érdekében az örökölt privát DNS-zóna erőforrások át kell új erőforrás-modellje. Az áttelepítési folyamat egyszerű, és megadtuk az automatizálni a folyamatot egy PowerShell-szkriptet. Ez az útmutató lépésről lépésre utasítást az Azure DNS saját zónáival kapcsolatos új erőforrás-modellje történő áttelepítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy telepítette az Azure PowerShell legújabb verzióját. További információ az Azure PowerShell (Az) és a telepítéshez keresse fel https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Győződjön meg arról, hogy Az.PrivateDns modul az Azure PowerShell telepítve van. Ez a modul telepítéséhez nyisson meg egy rendszergazda jogú PowerShell-ablakban (felügyeleti mód), és adja meg az alábbi parancs

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>Az áttelepítési folyamat teljesen automatizált, és várhatóan nem tapasztalnak állásidőt okozhatnak. Azonban használata az Azure DNS saját zónák (előzetes verzió) egy kritikus fontosságú éles környezetben, végre kell hajtani áttelepítési folyamat alatt egy tervezett karbantartási időszakban. Győződjön meg arról, hogy nem módosítja a konfiguráció vagy a privát DNS-zónák rekordhalmazok az áttelepítési parancsfájl futtatása közben.

## <a name="installing-the-script"></a>A parancsfájl telepítése

Nyisson meg egy emelt szintű (rendszergazdai mód) PowerShell-ablakot, és futtassa a következő parancs

```powershell
install-script PrivateDnsMigrationScript
```

Adja meg az "A", amikor a rendszer kéri, a parancsfájl telepítése

![A parancsfájl telepítése](./media/private-dns-migration-guide/install-migration-script.png)

Manuálisan is beszerezhet a legújabb PowerShell-parancsfájlt: https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>A szkript futtatása

Hajtsa végre a következő parancsot a szkript futtatása

```powershell
PrivateDnsMigrationScript.ps1
```

![A szkript futtatása](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Adja meg az előfizetés-azonosító, és jelentkezzen be az Azure-bA

Azt adja meg előfizetési Azonosítóját, amely tartalmazza a privát DNS-zónák áttelepíteni kívánt kéri. Jelentkezzen be az Azure-fiókját a rendszer. A bejelentkezés befejezéséhez úgy, hogy a parancsfájl hozzáfér a privát DNS-zóna erőforrás az előfizetést.

![Bejelentkezés az Azure-ba](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Válassza ki a migrálni kívánt DNS-zónák

A parancsprogram az előfizetésben található összes privát DNS-zónák listájának, és kérni fogja, hogy erősítse meg, melyeket szeretne áttelepíteni. Adja meg az "A" privát DNS-zónák áttelepíteni. Ez a lépés végrehajtása, ha a parancsfájl új privát DNS-zónák létrehozását új erőforrás-modell használatával, és az adatokat másolja be az új DSN-zóna. Ez a lépés nem módosítja a meglévő privát DNS-zónák az ennek ellenére.

![Válassza ki a DNS-zónák](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS-feloldás átvált az új DNS-zónák

A zónák és -rekordok erőforrástármegosztásba megtörtént-e az új erőforrás-modellre, miután a szkript kéri majd, hogy a DNS-feloldás váltson új DNS-zónák. Ez a lépés eltávolítja a társítást örökölt privát DNS-zónák és a virtuális hálózatok között. Az örökölt zóna nincs csatolva a virtuális hálózatot, amikor a lenne automatikusan átveszi a fenti lépésben létrehozott új DNS-zónák a DNS-feloldás ezen virtuális hálózatok.

Válassza ki a "A" az összes virtuális hálózat DNS-feloldás váltani.

![Váltás a névfeloldás](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>A DNS-feloldás ellenőrzése

Mielőtt továbblépne, győződjön meg arról, hogy a várt módon működik-e a DNS-zónákat a DNS-feloldás. Akkor is jelentkezzen be az azure virtuális gépek és működik-e probléma az nslookup-lekérdezést a migrált zónákat a DNS-feloldás ellenőrzése.

![Névfeloldás ellenőrzése](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Ha azt tapasztalja, hogy a DNS-lekérdezések nem feloldása, várjon néhány percet, és ismételje meg a lekérdezéseket. Ha a DNS-lekérdezések a várt módon működnek, adja meg a "Y", amikor a parancsfájl kérni fogja, hogy a virtuális hálózat eltávolítása a saját DNS-zóna.

![Erősítse meg a névfeloldás](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Ha bármilyen okból DNS miatt felbontás ellen a migrált zónák nem várt módon működik, adja meg az "n" a fenti és a parancsfájl a DNS-feloldás színűre vissza a régi zónák. Hozzon létre egy támogatási jegyet, és segítünk a DNS-zónák áttelepítését.

## <a name="cleanup"></a>Felesleges tartalmak törlése

Ez a lépés törli a régi típusú DNS-zónák, és csak az, hogy a várt módon működik-e DNS-feloldás ellenőrzése után hajtható végre. You’ll be prompted to delete each private DNS zone. Miután ellenőrizte, hogy megfelelően működik-e a zónák DNS-feloldás adja meg a "Y" minden parancssorba.

![A fölöslegessé vált elemek eltávolítása](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Az automation frissítése

Automation többek között sablonok, a PowerShell-szkriptek vagy az SDK-val fejlesztett egyéni kód használata, frissítenie kell az automation az új erőforrás-modell használatához a privát DNS-zónák. Az alábbiakban a hivatkozások új privát DNS parancssori felülete/PS/SDK dokumentációját.
* [Az Azure DNS saját zónák REST API-val](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Az Azure DNS privát zónái CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Az Azure DNS privát zónái PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Az Azure DNS privát zónái SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>További segítségre van szüksége?

Hozzon létre egy támogatási jegyet, ha további segítségre az áttelepítési folyamat vagy bármilyen okból miatt a fenti listán szereplő lépések nem működnek az Ön számára. A szöveges fájlt a támogatási jegyet a PowerShell-parancsfájl által előállított tartalmazza.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre egy privát zónák az Azure DNS- [Azure PowerShell-lel](./private-dns-getstarted-powershell.md) vagy [Azure CLI-vel](./private-dns-getstarted-cli.md).

* Olvassa el néhány gyakori [privát zónák – forgatókönyvek](./private-dns-scenarios.md) , amelyek megvalósíthatók az Azure DNS saját zónáival kapcsolatos.

* A gyakori kérdések és válaszok az Azure DNS privát zónái kapcsolatban, beleértve a viselkedést is várható bizonyos típusú műveletek, lásd: [Private DNS – gyakori kérdések](./dns-faq-private.md).

* További információ a DNS-zónák és rekordok funkcionáló [DNS-zónák és rekordok áttekintése](dns-zones-records.md).

* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.
