---
title: Hozzon létre egyéni szerepköralapú hozzáférés-vezérlés szerepköreinek, és rendelje hozzá a belső és külső felhasználók számára az Azure-ban |} Microsoft Docs
description: PowerShell és a parancssori felület használatával a belső és külső felhasználók számára létrehozott egyéni RBAC-szerepkörök hozzárendelése
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: d2eb39aa0a3fda7b543b6989cda937559f4d09ea
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="intro-on-role-based-access-control"></a>A szerepköralapú hozzáférés-vezérlés – bevezetés

Szerepköralapú hozzáférés-vezérlés az Azure portál csak szolgáltatása: így tulajdonosai előfizetés részletes szerepkörök hozzárendelése más felhasználók akik kezelhetik a környezetükben meghatározott erőforrás-hatókörök.

Szerepalapú lehetővé teszi, hogy jobb biztonságkezelés nagy méretű szervezeteknek, és az SMB-khez külső közreműködő, szállítók vagy adott erőforrásoknak a környezetben, de nem feltétlenül a teljes infrastruktúra vagy bármelyik részére hozzáférést igénylő freelancers használata a számlázással kapcsolatos hatókörök. Az RBAC lehetővé teszi, hogy egy Azure-előfizetéssel rendelkező rugalmasan kezeli a rendszergazdai fiókot (szolgáltatás-rendszergazda szerepkörrel egy előfizetés szintjén), és több felhasználók meghívást az azonos előfizetésben, de bármilyen rendszergazdai jogosultságok nélkül működik az . Felügyeleti és számlázási szempontból az RBAC funkció bizonyul egy idő és a felügyeleti hatékony beállítása a különböző forgatókönyvekben Azure használatával.

## <a name="prerequisites"></a>Előfeltételek
Az RBAC használata az Azure környezetben van szükség:

* Önálló rendelkezik Azure-előfizetés a felhasználóhoz rendelt tulajdonosa (előfizetés szerepkör)
* Rendelkezik a tulajdonosi szerepkört, az Azure-előfizetés
* Rendelkezik hozzáféréssel a [Azure-portálon](https://portal.azure.com)
* Győződjön meg arról, hogy a következő erőforrás-szolgáltató regisztrálva a felhasználó az előfizetéshez: **Microsoft.Authorization**. Az erőforrás-szolgáltatók regisztrálásával kapcsolatos további információkért lásd: [Resource Manager szolgáltatók, régiók, API verziók és sémák](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Office 365-előfizetéssel, vagy az Azure Active Directory-licencek (például: Azure Active Directory eléréséhez) kiosztott az Office 365 felügyeleti központ nem felel meg az RBAC használata.

## <a name="how-can-rbac-be-used"></a>Hogyan lehet használni az RBAC
Az RBAC alkalmazni lehet az Azure-ban három különböző hatóköröket. A legalacsonyabb egy legmagasabb hatálya alá akkor a következők:

* Előfizetés (legmagasabb)
* Erőforráscsoport
* Erőforrás hatókör (a hozzáférési legalacsonyabb kínál az egyes Azure-erőforrás hatókör célként megadott engedélyeket)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Az előfizetés hatókörből RBAC-szerepkörök hozzárendelése
Nincsenek két gyakori példán RBAC használja (de nem kizárólagosan):

* Hogy a szervezetek a külső felhasználók (nem a rendszergazda felhasználó Azure Active Directory-bérlő része) meghívót, hogy bizonyos erőforrások vagy a teljes előfizetés kezelése
* A felhasználók a szervezet (részét képezik a felhasználó Azure Active Directory-bérlő), de a különböző csapatok vagy csoportokat, amelyek a teljes előfizetés vagy egy bizonyos erőforráscsoportok vagy az erőforrás-hatókörök a környezetben a részletes hozzáférésre van szükségük belső használata

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Hozzáférés egy felhasználó Azure Active Directory kívül egy előfizetés szintjén
Az RBAC-szerepkörök csak akkor adhatók **tulajdonosok** az előfizetés. Ezért a rendszergazdának kell bejelentkeznie a felhasználót, hogy ehhez a szerepkörhöz előzetesen hozzárendelt vagy hozott létre az Azure-előfizetés.

Az Azure-portálon után jelentkezzen be rendszergazdaként, válassza ki "Előfizetések", és válassza a kívánt egy.
![előfizetés panel az Azure-portálon](./media/role-assignments-external-users/0.png) alapértelmezés szerint a rendszergazdai jogú felhasználó rendelkezik vásárolt Azure-előfizetést, ha a felhasználó fog megjelenni **Fiókadminisztrátor**, ez az előfizetés szerepkör alatt. Azure-előfizetés szerepkörökkel kapcsolatos további információkért lásd: [hozzáadása vagy módosítása, hogy az előfizetés vagy a szolgáltatások kezelése az Azure rendszergazdai szerepkörök](../billing/billing-add-change-azure-subscription-administrator.md).

Ebben a példában a felhasználó a "alflanigan@outlook.com" van a **tulajdonos** az "Ingyenes" az aad-ben az előfizetéshez bérlői "Alapértelmezett bérlőt Azure". Mivel ez a felhasználó hozta létre a kezdeti Microsoft Account "Outlook" az Azure-előfizetés (Microsoft Account = Outlook, a működés közbeni stb.) az alapértelmezett tartomány nevét ennél a bérlőnél a hozzáadott összes többi felhasználó számára lesz **"@alflaniganuoutlook.onmicrosoft.com"**. Úgy lett kialakítva, a szintaxist, az új tartomány formátuma kiépítésekor a bérlő létrehozó felhasználó felhasználónevét és tartományát nevét, és vegye fel a bővítmény **". onmicrosoft.com"**.
Ezenkívül felhasználók bejelentkezhetnek a bérlő az egyéni tartománynév hozzáadása, és azt az új bérlő ellenőrzése után. Az Azure Active Directory-bérlő egyéni tartománynév ellenőrzése További információkért lásd: [egyéni tartománynév hozzáadása a címtárhoz](/active-directory/active-directory-add-domain).

Ebben a példában a "Alapértelmezett bérlőt Azure" könyvtárban található csak azokat a felhasználókat, a tartomány nevét "@alflanigan.onmicrosoft.com".

Az előfizetés kiválasztása után a rendszergazda felhasználó kattintson kell **hozzáférés-vezérlés (IAM)** , majd **új szerepkör hozzáadása**.





![hozzáférés-vezérlési IAM funkciója Azure-portálon](./media/role-assignments-external-users/1.png)





![Új felhasználó hozzáadása a hozzáférés-vezérlési IAM funkciója Azure-portálon](./media/role-assignments-external-users/2.png)

A következő lépés, hogy válassza ki hozzá kell rendelni a szerepkört és a felhasználó, akinek a Szerepalapú szerepkör rendeli hozzá. Az a **szerepkör** legördülő menüre, a rendszergazda felhasználó számára megjelenített csak a beépített RBAC szerepkörök az Azure-ban rendelkezésre álló. Részletesebb ismereteket szeretnének elsajátítani a minden egyes szerepkör és a hozzárendelhető hatókörök, lásd: [átruházásához hozzáférés-vezérlés beépített szerepkörök](built-in-roles.md).

A rendszergazdai jogú felhasználó majd hozzá kell a külső felhasználó e-mail címe. A várt működése a külső felhasználó számára nem jelenik meg a meglévő bérlő. Után a külső felhasználó kérték, ő lesz látható a **előfizetések > hozzáférés-vezérlés (IAM)** már hozzá vannak rendelve egy Szerepalapú szerepkört az előfizetés hatókörből aktuális felhasználókkal.





![engedélyek hozzáadása új RBAC-szerepkör](./media/role-assignments-external-users/3.png)





![előfizetés szintjén RBAC-szerepkörök listája](./media/role-assignments-external-users/4.png)

A felhasználó "chessercarlton@gmail.com" kell meghívott egy **tulajdonos** az "Ingyenes" előfizetés. Meghívást, követően a külső felhasználó kap egy e-mailes megerősítés egy aktiválási hivatkozást tartalmazó.
![e-mailek meghívó RBAC-szerepkör](./media/role-assignments-external-users/5.png)

Folyamatban a szervezeten kívül, az új felhasználó nem rendelkezik meglévő attribútuma "Alapértelmezett bérlőt Azure" könyvtárban. Azok a rendszer létrehozza, miután a külső felhasználó hozzájárult rögzítsen a rendszer a könyvtárban, az előfizetéshez társított rendelt szerepkör.





![meghívót e-mailt az RBAC-szerepkör](./media/role-assignments-external-users/6.png)

A külső felhasználó azt mutatja be az Azure Active Directory-bérlő mostantól külső felhasználóként, és ez az Azure-portálon tekintheti meg.





![felhasználók panel az azure active Directoryval Azure-portálon](./media/role-assignments-external-users/7.png)



Az a **felhasználók** nézet, a külső felhasználók felismeri a különböző ikon írja be az Azure-portálon.

Azonban biztosítása **tulajdonos** vagy **közreműködő** a külső felhasználók elérését a **előfizetés** hatókörét, nem engedélyezi a hozzáférést a rendszergazda felhasználó könyvtárába, kivéve, ha a **Globális rendszergazda** lehetővé teszi. A felhasználói tulajdonságokat a **felhasználói típus**, amelynek van két általános paramétert, **tag** és **vendég** azonosítható. Egy tag egy felhasználót, hogy a címtárban regisztrálva van, míg a Vendég egy meghívót, hogy a könyvtár külső forrásból származó felhasználói. További információkért lásd: [hogyan Azure Active Directory rendszergazdák hozzá B2B együttműködés felhasználók](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Győződjön meg arról, hogy a portál a hitelesítő adatok megadása után, a külső felhasználó kiválasztja a megfelelő könyvtárban való bejelentkezéshez. Azonos is van a több könyvtárak eléréséhez és az Azure-portálon jobb felső felhasználónév kattintva válassza ki vagy az egyik és a felhasználónak a legördülő listából válassza ki a megfelelő könyvtár.

A Vendég a címtárban, miközben a külső felhasználó kezelheti az Azure-előfizetéshez tartozó összes erőforrást, de a könyvtár nem hozzáférhető.





![az azure active Directoryval az Azure-portálhoz korlátozott hozzáférés](./media/role-assignments-external-users/9.png)

Az Azure Active Directory és az Azure-előfizetés nem rendelkezik egy szülő-gyermek kapcsolat, például a más Azure-erőforrások (például: virtuális gépek, virtuális hálózatok, a webalkalmazások, tárolási stb.) az Azure-előfizetéssel rendelkezik. Összes ez utóbbi van létrehozott, kezelt és fizetni az Azure-előfizetéssel, míg az Azure-előfizetések az Azure-címtár való hozzáférés kezelése. További információkért lásd: [hogyan egy Azure-előfizetéshez az Azure AD kapcsolódó](/active-directory/active-directory-how-subscriptions-associated-directory).

Az összes a beépített RBAC szerepkörből **tulajdonos** és **közreműködő** minden erőforrásoknak a környezetben, a különbség a teljes felügyeleti hozzáférést nyújtanak, előfordulhat, hogy egy közreműködői nem hozható létre, majd új RBAC-szerepkörök törlése . A beépített szerepkörök, például **virtuális gép közreműködő** csak az erőforrásokat, függetlenül attól, hogy a név által jelzett összes felügyeleti hozzáférést nyújtanak a **erőforráscsoport** történő létrehozásuk alatt.

A beépített RBAC szerepe hozzárendelése **virtuális gép közreműködő** egy előfizetés szintjén azt jelenti, hogy a felhasználó a szerepét:

* Megtekintheti az összes virtuális gép függetlenül azok telepítési dátumát és az erőforráscsoportok részét képezik
* A virtuális gépek összes felügyeleti hozzáféréssel rendelkezik az előfizetéshez
* Az előfizetést nem lehet megtekinteni, bármilyen más típusú erőforrások
* A módosításokat a számlázási szempontjából nem tud működni.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Beépített RBAC szerepkör hozzárendelése egy külső felhasználó
A különböző esetén ez a vizsgálat, a külső felhasználó a "alflanigan@gmail.com" meg van adva egy **virtuális gép közreműködő**.




![virtuális gép közreműködő beépített szerepkör](./media/role-assignments-external-users/11.png)

A normál beépített szerephez a külső felhasználó működése áttekinthetők és felügyelhetők a csak virtuális gépek és a szomszédos erőforrás-kezelő csak szükséges erőforrások telepítése közben. Úgy lett kialakítva a korlátozott szerepkörök ajánlatot csak a saját Azure-portálon létrehozott levelező erőforrásokhoz való hozzáférés.



![virtuális gép közreműködői szerepkör áttekintése Azure-portálon](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Hozzáférés egy felhasználó ugyanabban a könyvtárban egy előfizetés szintjén
A folyamat megegyezik a külső felhasználók hozzáadása, mind a felügyelet szempontjából megadását a Szerepalapú szerepkör, valamint a felhasználói hozzáférést megkapják a szerepkörhöz. Itt különbség, hogy a meghívott felhasználók nem kapják meg minden e-mailek meghívókat, a bejelentkezés után az előfizetésen belüli összes erőforrás hatókör az irányítópult elérhető lesz.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Az erőforrás csoport hatókörű RBAC-szerepkörök hozzárendelése
Hozzárendelése a Szerepalapú szerepet egy **erőforráscsoport** hatókörben van egy azonos folyamata hozzárendelése a szerepkört az előfizetés szintjén mindkét típusú felhasználók – külső vagy belső (könyvtárába része). A felhasználók, az RBAC-szerepkör, hogy tekintse meg a környezetben csak az erőforráscsoport van hozzájuk rendelve való hozzáférést a **erőforráscsoportok** ikon az Azure portálon.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Az erőforrás-hatókörben RBAC-szerepkörök hozzárendelése
Hozzárendelése az Azure-erőforrás hatókörre RBAC szerepet van egy azonos folyamata hozzárendelése a szerepkört az előfizetés szintjén vagy az erőforráscsoport szintjén, a következő mindkét forgatókönyvet ugyanabban a munkafolyamatban. Ebben az esetben a felhasználókat, akik az RBAC szerepkör csak akkor hozzárendelt hozzáférés, vagy elemek látható a **összes erőforrás** lapon vagy közvetlenül az irányítópulton.

Az RBAC egyaránt erőforrás csoporthatókör vagy erőforrás hatókör fontos eleme a felhasználók számára, ügyeljen arra, hogy jelentkezzen be a megfelelő könyvtárban van.





![Directory bejelentkezés az Azure-portálon](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Egy Azure Active Directory csoport RBAC-szerepkörök hozzárendelése
Az RBAC használata az Azure-ban három különböző hatóköröket, minden helyzet kínálnak a kezelése, a központi telepítését, és a különböző erőforrások felügyelete személyes előfizetés kezelése igénye nélkül hozzárendelt felhasználóként jogosultságot. Függetlenül a Szerepalapú szerepkör hozzá van rendelve egy előfizetés, erőforráscsoporthoz vagy erőforrás hatókör, a hozzárendelt felhasználók által létrehozott további, az összes erőforrást számlázása alatt, ahol a felhasználók rendelkeznek-e a hozzáférést egy Azure-előfizetéssel. Ezzel a módszerrel számlázási a teljes Azure-előfizetéshez rendszergazdai jogosultságokkal rendelkező felhasználók rendelkezik teljes áttekintése a felhasználás, függetlenül az erőforrások kezel.

Nagyobb szervezeteknek figyelembe véve, hogy a felhasználót a rendszergazda hozzáférést szeretne biztosítani a részletes csoportok vagy részlegek teljes, nem különállóan minden felhasználónak, így annak eldöntéséhez, hogy a terv az Active Directory-csoportok esetében ugyanúgy alkalmazható RBAC-szerepkörök Ez különösen idő és a felügyeleti hatékony lehetőség. Ebben a példában mutatja be a **közreműködő** szerepkört az előfizetés szintjén a bérlő csoportok egyikéhez bővült.





![az AAD-csoportokat RBAC-szerepkör hozzáadása](./media/role-assignments-external-users/14.png)

Ezek a csoportok olyan biztonsági csoportok, ezek kiépített és felügyelt csak az Azure Active Directoryban.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Nyissa meg a támogatási kérelmek PowerShell használatával történő egyéni RBAC szerepkör létrehozása
A beépített szerepkörök az Azure-ban rendelkezésre álló gondoskodjon arról, hogy bizonyos jogosultsági szintek a környezetben elérhető erőforrások alapján. Ha a beépített szerepkörök nem felelnek meg az igényeinek, létrehozhat egyéni szerepkörök.

Szeretne létrehozni egy egyéni biztonsági szerepkört, egy beépített szerepkör kezdődnie, szerkesztheti, és ezután hozzon létre egy új szerepkört. Az ebben a példában a beépített **olvasó** szerepkör engedélyezése a felhasználó a lehetőség a támogatási kérelmek megnyitása van szabva.

A PowerShellben használja a [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) parancs használatával exportálja a **olvasó** szerepkör JSON formátumban.

```powershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Az alábbiakban látható az olvasó szerepkört JSON-kimenetét.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

A következő szerkeszti a JSON kimeneti az egyéni szerepkör létrehozásához.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Egy tipikus szerepkör áll három fő szakasz **műveletek**, **NotActions**, és **AssignableScopes**.

A **művelet** a szakasz a megengedett műveletek a szerepkörhöz. Ebben az esetben létrehozása támogatási jegyek, a **Microsoft.Support/&ast;**  műveletet hozzá kell adni. Fontos tudni, hogy az egyes műveletek szeretné elérhetővé tenni egy erőforrás-szolgáltató. Ahhoz, hogy az erőforrás-szolgáltató műveletek listáját, használhatja a [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) parancsot, vagy tekintse meg [Azure Resource Manager erőforrás-szolgáltató műveletek](resource-provider-operations.md).

Az adott szerepkörhöz tartozó összes műveletek korlátozásához erőforrás-szolgáltatók találhatók a **NotActions** szakasz.
Fontos, hogy a szerepkör tartalmazza a explicit előfizetési azonosítók felhasználási kötelező. Az előfizetési azonosítók a **AssignableScopes**, ellenkező esetben Ön nem jogosult lesz importálnia kell a szerepkört az előfizetés.

Az egyéni szerepkör létrehozásához használja a [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) parancsot, és adja meg a JSON szerepkör frissített definíciós fájl.

```powershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Ebben a példában a nevet az egyéni szerepkör "olvasó támogatási jegyek hozzáférési szint". Ez lehetővé teszi a felhasználónak az előfizetés, valamint a Megnyitás támogatási kérelmek mindent megtekinthetnek.

> [!NOTE]
> A csak két beépített szerepkörök, amelyek lehetővé teszik a felhasználót, hogy nyissa meg a támogatási kérelmek **tulajdonos** és **közreműködő**. A felhasználó megnyithatja a támogatási kérelmek ő szerepkört kell hozzárendelni egy az előfizetési hatókört, mert összes támogatási kérelmek létrehozása az Azure-előfizetés alapján.

Az új egyéni szerepkör érhető el az Azure-portálon, és a felhasználók számára is hozzárendelhető.

![Képernyőkép a egyéni biztonsági szerepkört importálni az Azure-portálon](./media/role-assignments-external-users/18.png)

![Képernyőkép a egyéni importált szerepkör hozzárendelése felhasználóhoz ugyanabban a könyvtárban](./media/role-assignments-external-users/19.png)

![egyéni importált szerepkör engedélyeinek képernyőképe](./media/role-assignments-external-users/20.png)

Egyéni szerepkörrel rendelkező felhasználók mostantól létrehozhat új támogatási kérelmek.

![Képernyőkép a támogatási kérelmek létrehozása egyéni szerepkör](./media/role-assignments-external-users/21.png)

Egyéni szerepkörrel rendelkező felhasználók nem egyéb műveleteket hajthat végre, például a virtuális gépek létrehozása vagy erőforráscsoportokat létrehozni.

![egyéni szerepkör nem sikerült létrehozni a virtuális gépek képernyőképe](./media/role-assignments-external-users/22.png)

![egyéni szerepkör nem hozhat létre új RGs képernyőképe](./media/role-assignments-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Nyissa meg a támogatási kérelmek Azure parancssori felület használatával történő egyéni RBAC szerepkör létrehozása

Azure parancssori felület használatával egyéni szerepkör létrehozásának lépései hasonlóak powershellel, azzal a különbséggel, hogy a JSON-kimenetét nem egyezik.

Például elindíthatja a beépített **olvasó** szerepkör. Az olvasó szerepkört műveleteit kilistázhatja a [az szerepkör-definíció lista](/cli/azure/role/definition#az_role_definition_list) parancsot.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Hozzon létre egy JSON-fájl a következő formátumban. A **Microsoft.Support/&ast;**  művelet hozzá lett adva a **műveletek** részek, hogy a felhasználói támogatási kérelmek is megnyithatja, miközben továbbra is egy olvasó lehet. Hozzá kell adni az előfizetés-azonosító, amelyeken ezt a szerepkört a kell használni a **AssignableScopes** szakasz.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Az egyéni szerepkör létrehozásához használja a [az szerepkör-definíció létrehozása](/cli/azure/role/definition#az_role_definition_create) parancsot.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Az új egyéni szerepkör már elérhető az Azure portálon, és ez a szerepkör folyamata megegyezik az előző PowerShell szakaszban.

![Parancssori felület 1.0 használatával létrehozott egyéni biztonsági szerepkört az Azure portál képernyőképe](./media/role-assignments-external-users/26.png)
