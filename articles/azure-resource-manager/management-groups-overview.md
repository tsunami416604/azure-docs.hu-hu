---
title: Az Azure felügyeleti csoportok az erőforrások rendszerezéséhez |} Microsoft Docs
description: További tudnivalók a felügyeleti csoportok és a használatukat.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/28/2018
ms.author: rithorn
ms.openlocfilehash: 611faef7e4b94b1734896fb64ca29540b12bc057
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102342"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok

Ha a szervezetben sok a lekérdezés, szükség lehet egy úgy, hogy hatékonyan kezelheti a hozzáférést, a házirendek és a megfelelőségi előfizetésekben. Az Azure felügyeleti csoportok biztosítanak az előfizetések fent hatókör. A "felügyeleti csoport" nevű tárolók előfizetések rendszerezése és a cégirányítási feltételek vonatkoznak a felügyeleti csoportok. A felügyeleti csoporton belül minden előfizetés automatikusan öröklik a feltételeket, a felügyeleti csoportra alkalmazza. Felügyeleti csoportjai vállalati szintű felügyeleti függetlenül attól, milyen típusú előfizetések lehetséges, hogy nagy léptékben.

A felügyeleti csoport funkciót egy nyilvános előzetes verziójában érhető el. Felügyeleti csoportok használatának megkezdéséhez jelentkezzen be a [Azure-portálon](https://portal.azure.com) keresse meg a **felügyeleti csoportok** a a **minden szolgáltatás** szakasz.

Például a felügyeleti csoporthoz, amely korlátozza a virtuális gép (VM) létrehozásához elérhető régiók házirendeket is alkalmazhat. Ezzel a házirend csak a virtuális gépek az adott régióban létrehozni lehetővé minden felügyeleti csoportok, előfizetések és erőforrások adott felügyeleti csoportba tartozó volna alkalmazható.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>A felügyeleti csoportok és előfizetések hierarchia

Felügyeleti csoport és az előfizetések a egyesített házirend- és hozzáférés-kezelés hierarchiába az erőforrások rendszerezéséhez rugalmas szerkezete hozhat létre.
Az alábbi ábrán látható egy példa hierarchiában, amely a felügyeleti csoport és szervezeti egységek szerint vannak rendszerezve előfizetések áll.

![Fa](media/management-groups/MG_overview.png)

Hozzon létre egy hierarchiát, amely a szervezeti egységek szerint vannak csoportosítva, rendelhet [átruházásához hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) szerepkörök, amelyek *öröklése* az adott felügyeleti csoportba tartozó szervezeti. Felügyeleti csoportok segítségével csökkentheti a terhelést, és csökkenti a hiba csak egyszer rendelhető hozzá a szerepkört ehhez.

### <a name="important-facts-about-management-groups"></a>Fontos alapvető tudnivalók a felügyeleti csoportok

- 10 000 felügyeleti csoportok támogatja a egyetlen könyvtárban található.
- A felügyeleti csoport fa mélysége legfeljebb hat szintet is támogat.
  - Ezt a határt nem tartalmazza a legfelső szintű vagy az előfizetés szintjén.
- Minden felügyeleti csoport és az előfizetés csak egy szülő támogathat.
- Minden felügyeleti csoportban több gyermeke lehet.
- Előfizetések és a felügyeleti csoport összes könyvtárban egy hierarchián belül található. Lásd: [a legfelső szintű felügyeleti csoportra vonatkozó fontos tények](#important-facts-about-the-root-management-group) az előzetes kivételekhez.

### <a name="preview-subscription-visibility-limitation"></a>Előzetes előfizetés látható korlátozása

Jelenleg az előzetes kiadásban, ha még nem tudja megtekinteni az előfizetések, amelyek elérésére örökölt vannak belül korlátozása. A hozzáférés öröklik az előfizetéshez, de az Azure erőforrás-kezelő nem tudta tiszteletben még az öröklési hozzáférést.  

Az előfizetés információkat beolvasni a REST API használatával adatait adja vissza, rendelkezik hozzáféréssel, de az Azure portál és az Azure Powershell belül az előfizetések többé ne jelenjen meg.

Ez az elem van végzett, és a felügyeleti csoportok is megjelenik az összefoglalójuk "Általánosan rendelkezésre álló.", mielőtt a rendszer fel fogja oldani  

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Cloud Solution Provider (CSP) korlátozás előzetes

Nincs a jelenlegi korlátozás Cloud Solution Provider (CSP) partnerek számára, ahol nem hozhat létre, és az ügyfél címtáron belül az ügyfél felügyeleti csoportok kezelése.  
Ez az elem van végzett, és a felügyeleti csoportok is megjelenik az összefoglalójuk "Általánosan rendelkezésre álló.", mielőtt a rendszer fel fogja oldani

## <a name="root-management-group-for-each-directory"></a>Gyökérszintű felügyeleti csoport minden könyvtár

Minden könyvtár kap a "Gyökér" felügyeleti csoport egyetlen legfelső szintű felügyeleti csoportjában. A gyökérszintű felügyeleti csoport összeállítása a hierarchiába, hogy az összes felügyeleti csoportot, és előfizetések részekre bontani hozzá. A gyökérszintű felügyeleti csoport lehetővé teszi a globális házirendek és az RBAC-hozzárendelések a könyvtár szintjén kell alkalmazni. A [Directory rendszergazdának kell jogosultságszint-emelés maguk](../role-based-access-control/elevate-access-global-admin.md) kell kezdetben a legfelső szintű csoport tulajdonosa. Ha a rendszergazda a csoport tulajdonosa, azok rendelhet RBAC szerepköröket egyéb directory – felhasználók és csoportok kezelése a hierarchiában.  

### <a name="important-facts-about-the-root-management-group"></a>A legfelső szintű felügyeleti csoportra vonatkozó fontos tények

- A gyökérszintű felügyeleti csoport nevét és Azonosítóját alapértelmezés szerint kapnak. A megjelenített név az Azure-portálon belül különböző megjelenítendő bármikor lehet frissíteni.
  - A "Bérlői legfelső szintű csoport" lesz.
  - Az azonosító lesz az Azure Active Directory-azonosítót.
- A gyökérszintű felügyeleti csoport helyezhető át vagy törölték, szemben a más felügyeleti csoportok.  
- Az előfizetések és a felügyeleti csoportok többszörös a egy legfelső szintű felügyeleti csoporthoz a címtáron belül.
  - A címtárban található összes erőforrások a gyökérszintű felügyeleti csoporthoz tartozó globális felügyeleti részekre bontani.
  - Új előfizetések vannak automatikusan alapértelmezett értéke a gyökérszintű felügyeleti csoport létrehozásakor.
- A gyökérszintű felügyeleti csoport összes Azure-ügyfél látható, de nem minden ügyfél kezeléséhez a legfelső szintű felügyeleti csoporthoz hozzáféréssel rendelkező.
  - Bárki, aki hozzáfér egy előfizetés tekintheti meg a környezetben, ahol adott előfizetéshez a hierarchiában.  
  - Nem alapértelmezett való hozzáférése a gyökérszintű felügyeleti csoport. Könyvtár globális rendszergazdák nem csak a felhasználók emelhet magukat hozzáférést.  Amennyiben rendelkeznek hozzáféréssel, a címtárban kezeléséhez más felhasználóit is felruházhatja RBAC-szerepköröket.  

>[!NOTE]
>A címtár indítja a felügyeleti csoportok szolgáltatás 6/25/2018 előtt, ha a címtár előfordulhat, hogy nem állítható be az összes előfizetést a hierarchiában. A felügyeleti csoport team visszamenőleges frissíti a minden felügyeleti csoportok használata a nyilvános előzetes verziójában belül július 2018 időpont előtt elindított könyvtár. Minden előfizetés könyvtárak fog történni, a legfelső szintű felügyeleti csoportba tartozó gyermekek előzetes.  
>
>Ha kérdése van a visszamenőleges folyamatban, forduljon a: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>A felügyeleti csoport első telepítése

Bármely felhasználó elindítja a felügyeleti csoportok, nincs egy kezdeti telepítési folyamat, amely akkor fordul elő. Az első lépés a gyökérszintű felügyeleti csoport jön létre a címtárban. Ez a csoport létrehozása után, hogy a címtárban található összes meglévő előfizetések válnak, a legfelső szintű felügyeleti csoport gyermekei.  Ez a folyamat oka győződjön meg arról, hogy csak egy felügyeleti csoport hierarchia a címtáron belül.  A hierarchián belül a címtár lehetővé teszi, hogy a felügyeleti ügyfelek számára a globális hozzáférési és, hogy a címtárban található más ügyfelek nem kerülhető házirendek vonatkoznak. Semmit a legfelső szintű hozzárendelt megadhasson felügyeleti csoportok, előfizetések, erőforráscsoport-sablonok és a címtárban található erőforrások azzal, hogy a címtárban található egy hierarchiában.  

> [!IMPORTANT]
> Felhasználói hozzáférés vagy házirend hozzárendelés a legfelső szintű felügyeleti csoport bármely hozzárendelés **vonatkozik, hogy a címtárban található összes erőforrást**. Ezáltal az összes ügyfél számára ki kell értékelnie, az ebben a hatókörben definiált elemekre tartoznia kell.  Felhasználói hozzáférés, a házirend-hozzárendelések "Kell" csak ebben a hatókörben kell lennie.  
  
## <a name="management-group-access"></a>Felügyeleti csoport hozzáférése

Az Azure felügyeleti csoportokból támogatási [átruházásához hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az összes erőforrás hozzáférések és szerepkör-definíciók. Ezeket az engedélyeket a hierarchiában található gyermek erőforrásokhoz örökölt. Beépített RBAC szerepköröket rendelhet egy felügyeleti csoportot, amely örökli az erőforrásokhoz a hierarchia valamennyi alsóbb szintjén.  Például a Szerepalapú szerepkör virtuális gép közreműködő rendelhet egy felügyeleti csoportban. Ez a szerepkör semmilyen művelet nem rendelkezik a felügyeleti csoportra vonatkozóan, de öröklik az adott felügyeleti csoportba tartozó összes virtuális gépen.  

Az alábbi táblázat a felügyeleti csoportok szerepkörök és a támogatott műveletek listáját jeleníti meg.

| Az RBAC szerepkör neve             | Létrehozás | Átnevezés | Áthelyezés | Törlés | Hozzáférés hozzárendelése | Szabályzat hozzárendelése | Olvasás  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Tulajdonos                       | X      | X      | X    | X      | X             |               | X     |
|Közreműködő                 | X      | X      | X    | X      |               |               | X     |
|Olvasó                      |        |        |      |        |               |               | X     |
|Erőforrás-szabályzati közreműködő |        |        |      |        |               | X             |       |
|Felhasználói hozzáférés rendszergazdája   |        |        |      |        | X             |               |       |

### <a name="custom-rbac-role-definition-and-assignment"></a>Egyéni RBAC szerepkör-definíció és a hozzárendelés

Egyéni RBAC-szerepkörök jelenleg a felügyeleti csoportok nem támogatottak.  Tekintse meg a [felügyeleti csoport visszajelzési fórumon](https://aka.ms/mgfeedback) elem állapotának megtekintése.

## <a name="next-steps"></a>További lépések

Felügyeleti csoportok kapcsolatos további információkért lásd:

- [Azure-erőforrások rendszerezéséhez felügyeleti csoportok létrehozása](management-groups-create.md)
- [Módosítása, törlése és a felügyeleti csoportok kezelése](management-groups-manage.md)
- [Az Azure PowerShell modul telepítése](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Tekintse át a REST API-specifikáció](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Az Azure CLI-bővítményének telepítése](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
