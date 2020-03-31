---
title: Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure Portalon (előzetes verzió) – Azure RBAC
description: Ismerje meg, hogyan hozhat létre egyéni Azure-szerepköröket az Azure szerepköralapú hozzáférés-vezérléshez (Azure RBAC) az Azure Portalhasználatával. Ez magában foglalja az egyéni szerepkörök felsorolását, létrehozását, frissítését és törlését.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674869"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Egyéni Azure-szerepkörök létrehozása vagy frissítése az Azure Portal használatával (előzetes verzió)

> [!IMPORTANT]
> Az Azure Portal használatával az Azure-portált használó egyéni szerepkörök jelenleg nyilvános előzetes verzióban vannak.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet egyedi igényeinek, létrehozhat jaatot saját Egyéni Azure-szerepkörökhöz. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatáscsoportokhoz az előfizetési és erőforráscsoport-hatókörökben. Az egyéni szerepkörök egy Azure Active Directory (Azure AD) címtárban tárolódnak, és megoszthatók az előfizetések között. Minden könyvtár legfeljebb 5000 egyéni szerepkörrel rendelkezhet. Egyéni szerepkörök az Azure Portalon, az Azure PowerShellen, az Azure CLI-n vagy a REST API-n keresztül hozhatók létre. Ez a cikk ismerteti, hogyan hozhat létre egyéni szerepkörök et az Azure Portalon (jelenleg előzetes verzióban).

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>1. lépés: A szükséges engedélyek meghatározása

Az Azure több ezer olyan engedéllyel rendelkezik, amelyeket az egyéni szerepkörbe is beilleszthet. Az alábbiakban négyféleképpen határozhatja meg, hogy milyen engedélyeket szeretne hozzáadni az egyéni szerepkörhöz:

| Módszer | Leírás |
| --- | --- |
| Tekintse meg a meglévő szerepeket | Megtekintheti a meglévő szerepköröket, hogy milyen engedélyeket használ. További információ: [Azure beépített szerepkörök.](built-in-roles.md) |
| Engedélyek keresése kulcsszó szerint | Amikor egyéni szerepkört hoz létre az Azure Portalhasználatával, kulcsszavak szerint kereshet engedélyeket. Kereshet például virtuális *gép* vagy *számlázási* engedélyek között. Ezt a keresési funkciót a [4.](#step-4-permissions) |
| Az összes engedély letöltése | Amikor egyéni szerepkört hoz létre az Azure Portalon, letöltheti az összes engedélyt CSV-fájlként, majd kereshet ebben a fájlban. Az **Engedélyek hozzáadása** ablaktáblán kattintson az **Összes engedély letöltése** gombra az összes engedély letöltéséhez. Az Engedélyek hozzáadása ablaktábláról további információt a [4.](#step-4-permissions) |
| Az engedélyek megtekintése a dokumentumokban | Az [Azure Resource Manager erőforrás-szolgáltatóműveleteiben](resource-provider-operations.md)megtekintheti a rendelkezésre álló engedélyeket. |

## <a name="step-2-choose-how-to-start"></a>2. lépés: Válassza ki a kezdés módját

Háromféleképpen kezdheti el létrehozni az egyéni szerepkört. Klónozhat egy meglévő szerepkört, teljesen elölről kezdheti, vagy jsonfájllal kezdhet. A legegyszerűbb módja egy meglévő szerepkör keresése, amely rendelkezik a szükséges engedélyek nagy részével, majd klónozza és módosítja azt a forgatókönyvhöz. 

### <a name="clone-a-role"></a>Szerepkör klónozása

Ha egy meglévő szerepkör nem rendelkezik a szükséges engedélyekkel, klónozhatja, majd módosíthatja az engedélyeket. A szerepkör klónozásának megkezdéséhez kövesse az alábbi lépéseket.

1. Az Azure Portalon nyisson meg egy előfizetést vagy erőforráscsoportot, ahol az egyéni szerepkört hozzárendelheti, majd nyissa meg a **hozzáférés-vezérlést (IAM)**.

    A következő képernyőképen a hozzáférés-vezérlés (IAM) lap nyílik meg egy előfizetéshez.

    ![Hozzáférés-vezérlési (IAM) lap egy előfizetéshez](./media/custom-roles-portal/access-control-subscription.png)

1. A **Szerepkörök** fülre kattintva megtekintheti az összes beépített és egyéni szerepkör listáját.

1. Keressen meg egy klónozni kívánt szerepkört, például a Számlázási olvasó szerepkört.

1. A sor végén kattintson a három pontra (**...**), majd a **Klónozás gombra.**

    ![Klónozott helyi menü](./media/custom-roles-portal/clone-menu.png)

    Ezzel megnyitja az egyéni szerepkörök szerkesztőjét a **Szerepkör klónozása** beállítással.

1. Folytassa [a 3.](#step-3-basics)

### <a name="start-from-scratch"></a>Kezdés a nulláról

Ha szeretné, kövesse ezeket a lépéseket, hogy új szerepkört indítson a nulláról.

1. Az Azure Portalon nyisson meg egy előfizetést vagy erőforráscsoportot, ahol az egyéni szerepkört hozzárendelheti, majd nyissa meg a **hozzáférés-vezérlést (IAM)**.

1. Kattintson **a Hozzáadás** gombra, majd az **Egyéni szerepkör hozzáadása (előnézet) parancsra.**

    ![Egyéni szerepkör menü hozzáadása](./media/custom-roles-portal/add-custom-role-menu.png)

    Ezzel megnyitja az egyéni szerepkörök szerkesztőjét a **Mindentőle kezdés** beállítással.

1. Folytassa [a 3.](#step-3-basics)

### <a name="start-from-json"></a>Indulás a JSON-ról

Ha szeretné, megadhatja a legtöbb egyéni szerepkörértékét egy JSON-fájlban. A fájlt megnyithatja az egyéni szerepkörök szerkesztőjében, további módosításokat hajthat végre, majd létrehozhatja az egyéni szerepkört. A JSON-fájl indításához kövesse az alábbi lépéseket.

1. Hozzon létre egy JSON-fájlt, amely a következő formátumú:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. A JSON-fájlban adja meg a különböző tulajdonságok értékeit. Íme egy példa néhány hozzáadott értékkel. A különböző tulajdonságokról a [Szerepkör-definíciók ismertetése](role-definitions.md)című témakörben talál további információt.

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Az Azure Portalon nyissa meg a **hozzáférés-vezérlési (IAM)** lapot.

1. Kattintson **a Hozzáadás** gombra, majd az **Egyéni szerepkör hozzáadása (előnézet) parancsra.**

    ![Egyéni szerepkör menü hozzáadása](./media/custom-roles-portal/add-custom-role-menu.png)

    Ezzel megnyitja az egyéni szerepkörök szerkesztőjét.

1. Az Alapok lap **Alapvonal-engedélyek csoportjában**válassza **a Start jSON-ból**lehetőséget.

1. A Fájl kiválasztása párbeszédpanel mellett kattintson a mappa gombra a Megnyitás párbeszédpanel megnyitásához.

1. Jelölje ki a JSON-fájlt, majd kattintson **a Megnyitás gombra.**

1. Folytassa [a 3.](#step-3-basics)

## <a name="step-3-basics"></a>3. lépés: Alapok

Az Alapok lapon **megadhatja** az egyéni szerepkör nevét, leírását és alapkonfigurációs engedélyeit.

1. Az **Egyéni szerepkörnév** mezőben adja meg az egyéni szerepkör nevét. A névnek egyedinek kell lennie az Azure AD-címtárban. A név betűket, számokat, szóközöket és speciális karaktereket tartalmazhat.

1. A **Leírás** mezőben adja meg az egyéni szerepkör nem kötelező leírását. Ez lesz az egyéni szerepkör elemleírása.

    Az **Alapterv engedélyek** beállítást már be kell állítani az előző lépés alapján, de módosítható.

    ![Az Alapok lap a megadott értékekkel](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>4. lépés: Engedélyek

Az **Engedélyek** lapon adhatja meg az egyéni szerepkör engedélyeit. Attól függően, hogy klónozott-e egy szerepkört, vagy a JSON-nal kezdte, előfordulhat, hogy az Engedélyek lap már felsorol bizonyos engedélyeket.

![Az Engedélyek lap egyéni szerepkör létrehozása](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Engedélyek hozzáadása és eltávolítása

Az alábbi lépésekkel adhat hozzá vagy távolíthat el engedélyeket az egyéni szerepkörhöz.

1. Engedélyek hozzáadásához kattintson az **Engedélyek hozzáadása** gombra az Engedélyek hozzáadása ablaktábla megnyitásához.

    Ez az ablaktábla az összes elérhető engedélyt különböző kategóriákba csoportosítva, kártyaformátumban sorolja fel. Minden kategória egy *erőforrás-szolgáltatót*jelöl, amely egy Azure-erőforrásokat biztosító szolgáltatás.

1. Az **Engedély keresése** mezőbe írjon be egy karakterláncot az engedélyek kereséséhez. Például keressen *számlát* a számlához kapcsolódó engedélyek megkereséséhez.

    Az erőforrás-szolgáltató kártyák listája a keresési karakterlánc alapján jelenik meg. Arról, hogy az erőforrás-szolgáltatók hogyan felelnek meg az Azure-szolgáltatásokhoz, olvassa el [az Azure-szolgáltatások erőforrás-szolgáltatói című témakört.](../azure-resource-manager/management/azure-services-resource-providers.md)

    ![Engedélyek hozzáadása ablaktábla erőforrás-szolgáltatóval](./media/custom-roles-portal/add-permissions-provider.png)

1. Kattintson egy olyan erőforrás-szolgáltatói kártyára, amely rendelkezhet az egyéni szerepkörhöz hozzáadni kívánt engedélyekkel, például a **Microsoft Billing szolgáltatással.**

    Az adott erőforrás-szolgáltató felügyeleti engedélyeinek listája a keresési karakterlánc alapján jelenik meg.

    ![Engedélyek listájának hozzáadása](./media/custom-roles-portal/add-permissions-list.png)

1. Ha az adatsíkra vonatkozó engedélyeket keres, kattintson az **Adatműveletek gombra.** Ellenkező esetben hagyja a műveletek kapcsolót **Műveletek** beállításra a felügyeleti síkra vonatkozó engedélyek listázásához. A felügyeleti sík és az adatsík közötti különbségekről további információt a [Kezelési és adatműveletek című témakörben talál.](role-definitions.md#management-and-data-operations)

1. Szükség esetén frissítse a keresési karakterláncot a keresés további finomításához.

1. Ha megtalálta az egyéni szerepkörhöz hozzáadni kívánt egy vagy több engedélyt, jelölje be az engedélyeket. Jelölje be például az **Egyéb : Számla letöltése** jelölőnégyzetet a számlák letöltésére vonatkozó engedély hozzáadásához.

1. A **Hozzáadás** gombra kattintva adja hozzá az engedélyt az engedélylistához.

    Az engedély hozzáadódik `Actions` egy `DataActions`vagy egy.

    ![Engedély hozzáadva](./media/custom-roles-portal/permissions-list-add.png)

1. Az engedélyek eltávolításához kattintson a sor végén lévő törlés ikonra. Ebben a példában, mivel a felhasználónak nem lesz `Microsoft.Support/*` szüksége támogatási jegyek létrehozására, az engedély törölhető.

### <a name="add-wildcard-permissions"></a>Helyettesítő karakteres engedélyek hozzáadása

Attól függően, hogy hogyan választotta a kezdést, előfordulhat, hogy az engedélylistában helyettesítő karakterekkel ( (\*) rendelkezik engedélyekkel kapcsolatos engedélyekkel. A helyettesítő\*karakter ( ) kiterjeszt egy engedélyt minden, ami megfelel a megadott karakterláncnak. Tegyük fel például, hogy az Azure Cost Management hez és az exportáláshoz kapcsolódó összes engedélyt hozzá akarta adni. Az alábbi engedélyek et is hozzáadhatja:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Ahelyett, hogy hozzáadná ezeket az engedélyeket, egyszerűen hozzáadhat egy helyettesítő engedélyt. A következő helyettesítő engedély például egyenértékű az előző öt engedéllyel. Ez magában foglalná a jövőbeli exportengedélyeket is, amelyek hozzáadhatók.

```
Microsoft.CostManagement/exports/*
```

Ha új helyettesítő karaktert szeretne hozzáadni, nem veheti fel az **Engedélyek hozzáadása** ablaktáblán. Helyettesítő engedély hozzáadásához manuálisan kell hozzáadnia a **JSON** lapon. További információ: [6. lépés: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Engedélyek kizárása

Ha a szerepkör rendelkezik\*helyettesítő ( ) engedéllyel, és ki szeretne zárni vagy ki szeretne vonni bizonyos engedélyeket az adott helyettesítő engedélyből, kizárhatja őket. Tegyük fel például, hogy a következő helyettesítő jogosultságokkal rendelkezik:

```
Microsoft.CostManagement/exports/*
```

Ha nem szeretné engedélyezni az exportálás törlését, kizárhatja a következő törlési engedélyt:

```
Microsoft.CostManagement/exports/delete
```

Ha kizár egy engedélyt, az `NotActions` `NotDataActions`a vagy a . A hatékony felügyeleti engedélyek számítása az `Actions` összes, majd az `NotActions`összes kivonása. A tényleges adatengedélyek számítása az `DataActions` összes, majd az összes `NotDataActions`kivonása.

> [!NOTE]
> Az engedély kizárása nem ugyanaz, mint a tagadás. Az engedélyek kizárása egyszerűen kényelmes módja az engedélyek kivonásának a helyettesítő karakteres engedélyekből.

1. Ha ki szeretne zárni vagy ki szeretne vonni egy engedélyt egy engedélyezett helyettesítő karakterengedélyből, kattintson az **Engedélyek kizárása** gombra az Engedélyek kizárása ablaktábla megnyitásához.

    Ezen az ablaktáblán megadhatja a kizárt vagy kivont kezelési vagy adatengedélyeket.

1. Ha megtalálta a kizárni kívánt egy vagy több engedélyt, jelölje be az engedélyeket, majd kattintson a **Hozzáadás** gombra.

    ![Engedélyek kizárása ablaktábla – kijelölt engedély](./media/custom-roles-portal/exclude-permissions-select.png)

    Az engedély a `NotActions` vagy `NotDataActions`a .

    ![Engedély kizárva](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>5. lépés: Hozzárendelhető hatókörök

A **Hozzárendelhető hatókörök** lapon megadhatja, hogy az egyéni szerepkör hol érhető el a hozzárendeléshez, például előfizetésvagy erőforráscsoport. Attól függően, hogy hogyan választotta a kezdőképernyőt, ezen a lapon felsorolhatja azt a hatókört, amelyen megnyitotta a Hozzáférés-vezérlési (IAM) lapot. A hozzárendelhető hatókör gyökérhatókörre ("/") való beállítása nem támogatott. Ehhez az előzetes verzióhoz nem adhat hozzá felügyeleti csoportot hozzárendelhető hatókörként.

1. A **Hozzárendelhető hatókörök hozzáadása** ablaktábla megnyitásához kattintson a Hozzárendelhető hatókörök hozzáadása elemre.

    ![Hozzárendelhető hatókörök lap](./media/custom-roles-portal/assignable-scopes.png)

1. Kattintson egy vagy több használni kívánt hatókörre, általában az előfizetésére.

    ![Hozzárendelhető hatókörök hozzáadása](./media/custom-roles-portal/add-assignable-scopes.png)

1. A **hozzárendelhető** hatókör hozzáadásához kattintson a Hozzáadás gombra.

## <a name="step-6-json"></a>6. lépés: JSON

A **JSON** lapon a JSON-ban formázott egyéni szerepkör jelenik meg. Ha szeretné, közvetlenül szerkesztheti a JSON-t. Ha helyettesítő (\*) engedélyt szeretne hozzáadni, ezt a lapot kell használnia.

1. A JSON szerkesztéséhez kattintson a **Szerkesztés gombra.**

    ![A JSON lap egyéni szerepkört jelenít meg](./media/custom-roles-portal/json.png)

1. Módosítsa a JSON-t.

    Ha a JSON nincs megfelelően formázva, egy piros csipkézett vonal és egy jelző jelenik meg a függőleges ereszcsatornában.

1. A szerkesztés befejezése után kattintson a **Mentés gombra.**

## <a name="step-7-review--create"></a>7. lépés: Felülvizsgálat + létrehozása

A **Véleményezés + létrehozás** lapon megtekintheti az egyéni szerepkör-beállításokat.

1. Tekintse át az egyéni szerepkör-beállításokat.

    ![Véleményezés + létrehozás lap](./media/custom-roles-portal/review-create.png)

1. Az egyéni szerepkör létrehozásához kattintson a **Létrehozás** gombra.

    Néhány pillanat múlva megjelenik egy üzenetpanel, amely jelzi, hogy az egyéni szerepkör sikeresen létrejött.

    ![Egyéni szerepkörüzenet létrehozása](./media/custom-roles-portal/custom-role-success.png)

    Ha bármilyen hibát észlel, egy üzenet jelenik meg.

    ![Véleményezés + létrehozási hiba](./media/custom-roles-portal/review-create-error.png)

1. Tekintse meg az új egyéni szerepkört a **Szerepkörök** listában. Ha nem látja az egyéni szerepkört, kattintson a **Frissítés gombra.**

     Eltarthat néhány percig, amíg az egyéni szerepkör mindenhol megjelenik.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

Az egyéni szerepkörök megtekintéséhez kövesse az alábbi lépéseket.

1. Nyisson meg egy előfizetést vagy erőforráscsoportot, majd nyissa meg **a hozzáférés-vezérlést (IAM)**.

1. A **Szerepkörök** fülre kattintva megtekintheti az összes beépített és egyéni szerepkör listáját.

1. A **Típus** listában válassza a **CustomRole** lehetőséget az egyéni szerepkörök megtekintéséhez.

    Ha nemrég hozta létre az egyéni szerepkört, és nem látja azt a listában, kattintson a **Frissítés**gombra.

    ![Egyéni szerepkörlista](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

1. Acikk korábbi részében leírtak szerint nyissa meg az egyéni szerepkörök listáját.

1. Kattintson a frissíteni kívánt egyéni szerepkör három pontra (**...**) , majd kattintson a **Szerkesztés**gombra. Ne feledje, hogy a beépített szerepkörök nem frissíthetők.

    Az egyéni szerepkör megnyílik a szerkesztőben.

    ![Egyéni szerepkör menü](./media/custom-roles-portal/edit-menu.png)

1. Az egyéni szerepkör frissítéséhez használja a különböző lapokat.

1. Miután végzett a módosításokkal, kattintson a **Véleményezés + létrehozás** fülre a módosítások áttekintéséhez.

1. Az egyéni szerepkör frissítéséhez kattintson a **Frissítés** gombra.

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

1. Acikk korábbi részében leírtak szerint nyissa meg az egyéni szerepkörök listáját.

1. Az egyéni szerepkört használó szerepkör-hozzárendelések eltávolítása.

1. Kattintson a törölni kívánt egyéni szerepkör három pontra (**...**) , majd kattintson a **Törlés gombra.**

    ![Egyéni szerepkör menü](./media/custom-roles-portal/delete-menu.png)

    Eltarthat néhány percig, amíg az egyéni szerepkör teljesen törlődik.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Egyéni szerepkör létrehozása az Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Egyéni szerepkörök az Azure-ban](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatóműveletei](resource-provider-operations.md)
