---
title: Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok |} A Microsoft Docs
description: További tudnivalók a felügyeleti csoportok és azok használatát.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2018
ms.author: rithorn
ms.openlocfilehash: b95dba65a7ab89306844e48641aba584e3d6b175
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371516"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. A fenti előfizetések hatókörének biztosítanak az Azure felügyeleti csoportok. "Felügyeleti csoportok" nevű tárolókba előfizetések rendszerezése és a felügyeleti csoportok a szabályozási feltételek vonatkoznak. A felügyeleti csoporton belül az összes előfizetés automatikusan örökli a feltételek a felügyeleti csoportra alkalmazza. Felügyeleti csoportok lehetővé teszik, nagyvállalati szintű felügyeleti, függetlenül attól, hogy milyen típusú előfizetések lehetséges, hogy a nagy mennyiségű.

Például egy felügyeleti csoporthoz, amely korlátozza a virtuális gép (VM) létrehozásához rendelkezésre álló régiók szabályzatokat is alkalmazhat. Ez a szabályzat akkor alkalmazható, előfizetések és erőforrásokat a felügyeleti csoport összes felügyeleti csoportok azáltal, hogy csak virtuális gépek hozhatók létre az adott régióban.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Felügyeleti csoportok és az előfizetések hierarchia

Felügyeleti csoportok és az előfizetések a egyesített házirend- és hozzáférés-kezelés hierarchiává az erőforrások rendszerezéséhez rugalmas szerkezete hozhat létre.
Az alábbi ábrán látható egy példa a felügyeleti csoportok használatával cégirányítási-hierarchiák létrehozásáról.

![fa](media/management-groups/MG_overview.png)

Hozzon létre egy hierarchia, mint ebben a példában egy házirendet, például az "infrastruktúra csoport felügyeleti csoportja" az USA nyugati régiójában korlátozódik VM helyek vonatkozik engedélyezése belső megfelelőségi és biztonsági házirendjeivel. Ez a szabályzat mindkét EA-előfizetések, a felügyeleti csoportban az alakzatot öröklik, és ezen előfizetések mellett minden virtuális gépre érvényes lesz. Ezt a szabályzatot örökli a felügyeleti csoport az előfizetésekhez, mivel a biztonsági házirend továbbfejlesztett cégirányítási lehetővé tevő erőforrás vagy az előfizetés tulajdonosa nem módosítható.

Egy másik forgatókönyv, ahol a felügyeleti csoportok használna, hogy több előfizetést felhasználói hozzáférést biztosítanak.  Több felügyeleti csoport-előfizetések áthelyezésével, hogy a számítógép egy RBAC-hozzárendelés létrehozása a felügyeleti csoport, amely örökli az összes olyan előfizetést, hogy a hozzáférést.  Parancsfájl RBAC-hozzárendeléseket több előfizetés nélkül a felügyeleti csoport egy hozzárendelési engedélyezheti a felhasználóknak, amire csak szükségük hozzáférésre.

### <a name="important-facts-about-management-groups"></a>Fontos alapvető tudnivalók a felügyeleti csoportok

- 10 000 felügyeleti csoport egyetlen címtárban (az Azure Active Directory-bérlő) nem használható.
- Egy felügyeleti csoport fa mélysége legfeljebb hat szintje is támogatja.
  - Ez a korlátozás nem tartalmazza a gyökérszintű vagy az előfizetés szintjén.
- Minden egyes felügyeleti csoport és az előfizetés csak egy szülő támogatja.
- Egyes felügyeleti csoportjai rendelkezhet több gyermeke is lehet.
- Összes előfizetés és a felügyeleti csoport összes könyvtárban egy hierarchián belül található. Lásd: [a legfelső szintű felügyeleti csoportra vonatkozó fontos tények](#important-facts-about-the-root-management-group) kivételekhez az előzetes verzió ideje alatt.

## <a name="root-management-group-for-each-directory"></a>Gyökérszintű felügyeleti csoport minden könyvtár

Minden könyvtár neve a "Root" felügyeleti csoport egyetlen legfelső szintű felügyeleti csoport van megadva. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. A gyökérszintű felügyeleti csoport lehetővé teszi a globális szabályzatok és az RBAC-hozzárendelések a könyvtár szintjén alkalmazható. A [Directory rendszergazdának kell megszereznie magukat](../role-based-access-control/elevate-access-global-admin.md) , ha kezdetben a legfelső szintű csoport tulajdonosa. Miután a rendszergazda, a csoport tulajdonosa, azok rendelhet bármely RBAC szerepkör más directory – felhasználók és csoportok kezelése a hierarchiában.  

### <a name="important-facts-about-the-root-management-group"></a>A legfelső szintű felügyeleti csoportra vonatkozó fontos tények

- Alapértelmezés szerint kapnak a gyökérszintű felügyeleti csoport nevét és Azonosítóját. A megjelenítendő név megjelenítése az Azure Portalon különböző bármikor frissíthető.
  - A "Bérlő legfelső szintű csoport" lesz.
  - Az azonosító lesz az Azure Active Directory-azonosítója.
- A gyökérszintű felügyeleti csoport nem lehet törölték vagy áthelyezték, szemben a többi felügyeleti csoportba.  
- Összes előfizetés és a felügyeleti csoportok modellrészek egy legfelső szintű felügyeleti csoporthoz a címtáron belül.
  - A címtárban található összes erőforrást a legfelső szintű felügyeleti csoporthoz tartozó globális felügyeleti modellrészek.
  - Az új előfizetési lehetőségekről, a legfelső szintű felügyeleti csoport létrehozásakor automatikusan jéhez.
- Az összes Azure-ügyfelek tekintheti a legfelső szintű felügyeleti csoporthoz, de nem minden ügyfele a gyökérszintű felügyeleti csoport kezeléséhez.
  - Mindenki, aki hozzáfér egy előfizetést a környezetben, ahol ezt az előfizetést a hierarchia látható.  
  - Senki nem alapértelmezett való hozzáféréssel kap hozzáférést a legfelső szintű felügyeleti csoportban. Directory globális rendszergazdái is csak a felhasználók szintre emelhet, magukat, hogy hozzáférést kapjanak.  Miután hozzáféréssel rendelkeznek, a címtár rendszergazdái rendelheti bármely RBAC szerepkör más felhasználók kezeléséhez.  

>[!NOTE]
>Ha a címtár első lépéseiben a felügyeleti csoportok szolgáltatás 6/25/2018 előtt, a előfordulhat, hogy nem állította be az összes előfizetést a hierarchiában a címtárban. A felügyeleti csoport csapat visszamenőlegesen frissíti a felügyeleti csoportok használata a nyilvános előzetes verziója 2018 július/augusztus belül céldátum előtt elindított mindegyik könyvtár. A könyvtárakban található összes előfizetés lesz a gyökérszintű felügyeleti tartozó gyermekek előzetes.  
>
>Ha kérdése van a visszamenőleges folyamattal, kapcsolatba: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>A felügyeleti csoport első telepítése

Ha bármely felhasználó elindítja a felügyeleti csoportok, nincs egy kezdeti telepítési folyamat, amely akkor fordul elő. Az első lépéseként a gyökérszintű felügyeleti csoport jön létre a címtárban. Ez a csoport létrehozása után a címtárban lévő összes meglévő előfizetés jönnek létre a gyökérszintű felügyeleti csoport gyermekei.  Ez a folyamat az az oka, hogy ellenőrizze, hogy csak egy felügyeleti csoport hierarchiájának a címtáron belül van.  A hierarchián belül a könyvtár lehetővé teszi, hogy a közigazgatási ügyfelek globális hozzáférés és a szabályzatokat, amelyeket más ügyfelek számára a címtáron belül nem tudja alkalmazni. Semmit a legfelső szintű hozzárendelt érvényesek lesznek az összes felügyeleti csoportok, előfizetések, erőforráscsoportok és a címtárban található erőforrásokat létesíteni egy hierarchia a címtáron belül.  

> [!IMPORTANT]
> Felhasználói hozzáférés és szabályzat-hozzárendelés a legfelső szintű felügyeleti csoport bármely hozzárendelését **a könyvtárban lévő valamennyi erőforrására vonatkozik**. Emiatt minden ügyfélnek ki kell értékelni a hatókörön meghatározott elemek rendelkeznie kell.  Felhasználói hozzáférés és a szabályzat-hozzárendelések "Kell, hogy" csak ebben a hatókörben kell lennie.  
  
## <a name="management-group-access"></a>Felügyeleti csoport hozzáférése

Az Azure felügyeleti csoportokból támogatási [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az összes erőforrás hozzáfér, és a szerepkör-definíciók. Ezek az engedélyek öröklődnek, gyermekszintű erőforrása, amely a hierarchiában. Minden olyan beépített RBAC-szerepkört rendelhet egy felügyeleti csoportot, amely a hierarchiában lejjebb lévő erőforrások öröklik.  Az RBAC szerepkör Virtuálisgép-közreműködői például egy felügyeleti csoporthoz is hozzárendelhető. Ehhez a szerepkörhöz nincs művelet van a felügyeleti csoport, de öröklik az alatt a felügyeleti csoport összes virtuális gépre.  

Az alábbi ábra a felügyeleti csoportok szerepkörök és a támogatott műveletek listáját jeleníti meg.

| RBAC szerepkör neve             | Létrehozás | Átnevezés | Áthelyezés | Törlés | Hozzáférés hozzárendelése | Szabályzat hozzárendelése | Olvasás  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Tulajdonos                       | X      | X      | X    | X      | X             | X             | X     |
|Közreműködő                 | X      | X      | X    | X      |               |               | X     |
|Felügyeleti csoport közreműködői *             | X      | X      | X    | X      |               |               | X     |
|Olvasó                      |        |        |      |        |               |               | X     |
|Felügyeleti csoport olvasó *                  |        |        |      |        |               |               | X     |
|Erőforrás-szabályzati közreműködő |        |        |      |        |               | X             |       |
|Felhasználói hozzáférés rendszergazdája   |        |        |      |        | X             |               |       |

*: Felügyeleti csoport közreműködői és a felügyeleti csoport olvasó csak engedélyezése a felhasználók számára, hogy ezeket a műveleteket hajthat végre a felügyeleti csoport hatóköre.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Egyéni RBAC szerepkör-definíció- és hozzárendelés

Egyéni RBAC-szerepkörök jelenleg nem támogatottak a felügyeleti csoportok.  Tekintse meg a [felügyeleti csoport Visszajelzési fórum](https://aka.ms/mgfeedback) ennek az elemnek a állapotának megtekintéséhez.

## <a name="next-steps"></a>További lépések

Felügyeleti csoportok kapcsolatos további információkért lásd:

- [Az Azure-erőforrások rendszerezése felügyeleti csoportok létrehozása](management-groups-create.md)
- [Módosítása, törlése és a felügyeleti csoportok kezelése](management-groups-manage.md)
- [Az Azure PowerShell-modul telepítése](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Tekintse át a REST API-specifikációja](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Az Azure CLI-bővítmény telepítése](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
