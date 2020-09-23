---
title: A Virtual Machines szolgáltatás Azure-beli autokezelése – gyakori kérdések
description: Válaszok a virtuális gépekre vonatkozó Azure-beli automanaged szolgáltatással kapcsolatos gyakori kérdésekre.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: ed97f7861f5dd959fd41ac22b4e497f492dbc3a3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940173"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Gyakori kérdések az Azure-beli automanage virtuális gépekről

Ez a cikk a [virtuális gépek Azure-beli automanage szolgáltatásával](automanage-virtual-machines.md)kapcsolatos leggyakoribb kérdésekre ad választ.

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az [MSDN webhelyen](https://azure.microsoft.com/support/forums/)található Azure-fórumokra, és stack overflow. Felteheti a problémát ezekben a fórumokon, vagy közzéteheti a [ @AzureSupport Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérést is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás lapon](https://azure.microsoft.com/support/options/)válassza a **támogatás**lekérése lehetőséget.


## <a name="azure-automanage-for-virtual-machines"></a>Azure-beli automata felügyelet virtuális gépeken

**Milyen előfeltételekre van szükség az Azure automanage szolgáltatás engedélyezéséhez?**

Az alábbi előfeltételek az Azure automanage szolgáltatás engedélyezésének előfeltételei:
- Csak Windows Server-alapú virtuális gépek
- A virtuális gépeknek futniuk kell
- A virtuális gépeknek támogatott régióban kell lenniük
- A felhasználónak megfelelő engedélyekkel kell rendelkeznie
- Csak a nem méretezési csoportba tartozó virtuális gépek
- A virtuális gépek nem csatolhatnak egy másik előfizetésben lévő log Analytics-munkaterülethez

**Milyen RBAC engedélyre van szükség az automanage engedélyezéséhez?**

A felhasználóknak tulajdonosi szerepkörrel kell rendelkezniük. Azt is megteheti, hogy a felhasználók a felhasználói hozzáférés rendszergazdai szerepkörrel együtt vehetik igénybe a közreműködő szerepkört az autofelügyelet alkalmazásához.


**Mely régiók támogatottak?**

A következő régiókban működő virtuális gépek támogatottak: Nyugat-Európa, USA keleti régiója, USA 2. nyugati régiója, Közép-Kanada, USA nyugati középső régiója.


**Mely funkciók automatizálják az Azure automatikus felügyeletét?**

Az [itt](virtual-machines-best-practices.md)felsorolt szolgáltatások a virtuális gép életciklusa alatt beléptetések, konfigurálások és figyelők automatikus kezelése.


**Testreszabhatók a konfigurációk az Azure automanage szolgáltatásban?**

Az ügyfelek a konfigurációs beállítások segítségével testre szabhatják az adott szolgáltatások beállításait, például a Azure Backup megőrzést. A módosítható beállítások teljes listájáért tekintse meg a dokumentációt [itt](virtual-machines-best-practices.md).


**Működik az Azure automanage a Linux és a Windows rendszerű virtuális gépekkel?**

Az automanage jelenleg a Windows Server Azure virtuális gépeket támogatja.


**Szelektíven is alkalmazhatom az automanage-t csak a virtuális gépek készletén?**

A kiválasztott új és meglévő virtuális gépeken a click és a Point egyszerűség lehetőséggel engedélyezhető az automanage szolgáltatás. Az automanage is bármikor letiltható.


**Támogatja az Azure automanage a virtuálisgép-méretezési csoportokban a virtuális gépeket?**

Nem, az Azure automanage jelenleg nem támogatja a virtuálisgép-méretezési csoportokban lévő virtuális gépeket.


**Mennyibe kerül az Azure automanaged Cost?**

Az Azure automanage szolgáltatás díjmentesen elérhető nyilvános előzetes verzióban. A csatlakoztatott Azure-erőforrások (például Azure Backup) költséggel járnak.


**Alkalmazhatom az Azure Policy használatával történő autofelügyeletet?**

Igen, van egy beépített szabályzatunk, amely automatikusan alkalmazza az automatikus felügyeletet a megadott hatókörön belüli összes virtuális gépre. A konfigurációs profilt (DevTest vagy éles környezet) is meg kell adnia az automatikus felügyelet fiókkal együtt. További információ az Azure Policy szolgáltatással való autofelügyelet engedélyezéséről [itt](virtual-machines-policy-enable.md).


**Mi az az automanage Account?**

Az automatikus felügyelet fiók egy MSI (Managed Service Identity), amely biztosítja a biztonsági környezetet, illetve azt az identitást, amely alatt az automatizált műveletek történnek.


**Módosíthatom a virtuális gép konfigurációs profilját?**

Ekkor le kell tiltania a virtuális gép automatikus felügyeletét, majd újra engedélyeznie kell az automatikus felügyeletet a kívánt konfigurációs profillal és beállításokkal.


**Ha a virtuális gép már konfigurálva van egy szolgáltatáshoz (például Update Management), az automatikusan kezeli az újrakonfigurálást?**
Nem, az automanage nem konfigurálja újra. Elkezdjük figyelni a szolgáltatáshoz kapcsolódó erőforrásokat a drifthez.


**Miért van a virtuális gép hibás állapota az automanage portálon?**

Ha az állapotot *nem sikerült*megtekinteni, a virtuális gép által használt erőforráscsoport használatával elháríthatja a telepítést. Lépjen az **erőforráscsoportok**elemre, válassza ki az erőforráscsoportot, kattintson a **központi telepítések** elemre, és tekintse meg a *hibás* állapotot a hiba részleteivel együtt.

**Hogyan szerezhetem be az automanage hibaelhárítási támogatását?**

A [technikai támogatási esetekről szóló jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)is megadhat. A **szolgáltatás** beállításnál keresse meg és válassza ki az *automanage* ( *felügyelet és felügyelet) szakaszt a figyelés és kezelés* szakaszban.


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)