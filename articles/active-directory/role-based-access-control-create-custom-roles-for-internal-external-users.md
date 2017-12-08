---
title: "Egyéni szerepköralapú hozzáférés-vezérlés szerepköröket hozhat létre, és rendelje hozzá a belső és külső felhasználók számára az Azure-ban |} Microsoft Docs"
description: "PowerShell és a parancssori felület használatával a belső és külső felhasználók számára létrehozott egyéni RBAC-szerepkörök hozzárendelése"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.openlocfilehash: 213b02205bbe7f767b6aff6a0693bb34b97cb9ec
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="intro-on-role-based-access-control"></a>A szerepköralapú hozzáférés-vezérlés – bevezetés

Szerepköralapú hozzáférés-vezérlés az Azure portál csak szolgáltatása: így tulajdonosai előfizetés részletes szerepkörök hozzárendelése más felhasználók akik kezelhetik a környezetükben meghatározott erőforrás-hatókörök.

Az RBAC lehetővé teszi, hogy jobb biztonságkezelés nagy méretű szervezeteknek, és az SMB-khez működik-e külső közreműködő, szállítókkal és freelancers, amelyhez hozzá kell férniük az adott környezetben meghatározott erőforrás azonban nem feltétlenül a teljes infrastruktúra vagy bármely a számlázással kapcsolatos hatókörök. Az RBAC lehetővé teszi, hogy egy Azure-előfizetéssel rendelkező rugalmasan kezeli a rendszergazdai fiókot (szolgáltatás-rendszergazda szerepkörrel egy előfizetés szintjén), és több felhasználók meghívást az azonos előfizetésben, de bármilyen rendszergazdai jogosultságok nélkül működik az . Felügyeleti és számlázási szempontból az RBAC funkció bizonyul egy idő és a felügyeleti hatékony beállítása a különböző forgatókönyvekben Azure használatával.

## <a name="prerequisites"></a>Előfeltételek
Az RBAC használata az Azure környezetben van szükség:

* Önálló rendelkezik Azure-előfizetés a felhasználóhoz rendelt tulajdonosa (előfizetés szerepkör)
* Rendelkezik a tulajdonosi szerepkört, az Azure-előfizetés
* Rendelkezik hozzáféréssel a [Azure-portálon](https://portal.azure.com)
* Győződjön meg arról, hogy a következő erőforrás-szolgáltató regisztrálva a felhasználó az előfizetéshez: **Microsoft.Authorization**. Az erőforrás-szolgáltatók regisztrálásával kapcsolatos további információkért lásd: [Resource Manager szolgáltatók, régiók, API verziók és sémák](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Office 365-előfizetéssel, vagy az Azure Active Directory-licencek (például: Azure Active Directory eléréséhez) kiosztott az Office 365 portál nem érvényes az RBAC használata.

## <a name="how-can-rbac-be-used"></a>Hogyan lehet használni az RBAC
Az RBAC alkalmazni lehet az Azure-ban három különböző hatóköröket. A legalacsonyabb egy legmagasabb hatálya alá akkor a következők:

* Előfizetés (legmagasabb)
* Erőforráscsoport
* Erőforrás hatókör (a hozzáférési legalacsonyabb kínál az egyes Azure-erőforrás hatókör célként megadott engedélyeket)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Az előfizetés hatókörből RBAC-szerepkörök hozzárendelése
Nincsenek két gyakori példán RBAC használja (de nem kizárólagosan):

* Hogy a szervezetek a külső felhasználók (nem a rendszergazda felhasználó Azure Active Directory-bérlő része) meghívót, hogy bizonyos erőforrások vagy a teljes előfizetés kezelése
* A felhasználók a szervezet (részét képezik a felhasználó Azure Active Directory-bérlő), de a különböző csapatok vagy csoportokat, amelyek a teljes előfizetés vagy egy bizonyos erőforráscsoportok vagy az erőforrás-hatókörök részletes hozzáférésre van szükségük belső használata a környezet

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Hozzáférés egy felhasználó Azure Active Directory kívül egy előfizetés szintjén
Az RBAC-szerepkörök csak akkor adhatók **tulajdonosok** az előfizetés ezért a rendszergazdai jogú felhasználó kell bejelentkeznie, amely rendelkezik-e előre szerepkörrel, vagy az Azure-előfizetés hozott létre egy felhasználónévvel.

Az Azure-portálon után bejelentkezés rendszergazdaként, válassza ki "Előfizetések", és válassza a kívánt egy.
![előfizetés panel az Azure-portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) alapértelmezés szerint a rendszergazdai jogú felhasználó rendelkezik vásárolt Azure-előfizetést, ha a felhasználó fog megjelenni **Fiókadminisztrátor**, ez az előfizetés szerepkör alatt. Az Azure-előfizetés szerepkörök további részletekért lásd: [hozzáadása vagy módosítása, hogy az előfizetés vagy a szolgáltatások kezelése az Azure rendszergazdai szerepkörök](/billing/billing-add-change-azure-subscription-administrator.md).

Ebben a példában a felhasználó a "alflanigan@outlook.com" van a **tulajdonos** az "Ingyenes" az aad-ben az előfizetéshez bérlői "Alapértelmezett bérlőt Azure". Mivel ez a felhasználó hozta létre a kezdeti Microsoft Account "Outlook" az Azure-előfizetés (Microsoft Account = Outlook, a működés közbeni stb.) az alapértelmezett tartomány nevét ennél a bérlőnél a hozzáadott összes többi felhasználó számára lesz **"@alflaniganuoutlook.onmicrosoft.com"**. Úgy lett kialakítva, a szintaxist, az új tartomány formátuma kiépítésekor a bérlő létrehozó felhasználó felhasználónevét és tartományát nevét, és vegye fel a bővítmény **". onmicrosoft.com"**.
Ezenkívül felhasználók is jelentkezzen be a bérlő egy egyéni tartománynév hozzáadása, és azt az új bérlő ellenőrzése után. Az Azure Active Directory-bérlő egyéni tartománynév ellenőrzése a további részletekért lásd: [egyéni tartománynév hozzáadása a címtárhoz](/active-directory/active-directory-add-domain).

Ebben a példában a "Alapértelmezett bérlőt Azure" könyvtárban található csak azokat a felhasználókat, a tartomány nevét "@alflanigan.onmicrosoft.com".

Az előfizetés kiválasztása után a rendszergazda felhasználó kattintson kell **hozzáférés-vezérlés (IAM)** , majd **új szerepkör hozzáadása**.





![hozzáférés-vezérlési IAM funkciója Azure-portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![Új felhasználó hozzáadása a hozzáférés-vezérlési IAM funkciója Azure-portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

A következő lépés, hogy válassza ki hozzá kell rendelni a szerepkört és a felhasználó, akinek a Szerepalapú szerepkör rendeli hozzá. Az a **szerepkör** legördülő menüjében a rendszergazdai jogú felhasználó számára megjelenített csak a beépített RBAC szerepkörök, amelyek elérhetők az Azure-ban. Részletesebb ismereteket szeretnének elsajátítani a minden egyes szerepkör és a hozzárendelhető hatókörök, lásd: [átruházásához hozzáférés-vezérlés beépített szerepkörök](role-based-access-built-in-roles.md).

A rendszergazdai jogú felhasználó majd hozzá kell a külső felhasználó e-mail címe. A várt működése a külső felhasználó számára nem jelenik meg a meglévő bérlő. Után a külső felhasználó kérték, ő lesz látható a **előfizetések > hozzáférés-vezérlés (IAM)** az aktuális felhasználókkal, amely már hozzá vannak rendelve az RBAC szerepkört az előfizetés hatókörben.





![engedélyek hozzáadása új RBAC-szerepkör](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![előfizetés szintjén RBAC-szerepkörök listája](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

A felhasználó "chessercarlton@gmail.com" kell meghívott egy **tulajdonos** az "Ingyenes" előfizetés. Meghívást, követően a külső felhasználó kap egy e-mailes megerősítés egy aktiválási hivatkozást tartalmazó.
![e-mailek meghívó RBAC-szerepkör](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Folyamatban a szervezeten kívül, az új felhasználó nem rendelkezik meglévő attribútuma "Alapértelmezett bérlőt Azure" könyvtárban. Azok a rendszer létrehozza, miután a külső felhasználó hozzájárult rögzítsen a rendszer a könyvtárban, amely az előfizetéshez, amely rendelt szerepkör.





![meghívót e-mailt az RBAC-szerepkör](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

A külső felhasználó azt mutatja be az Azure Active Directory-bérlő mostantól külső felhasználóként, és ez az Azure portálon, és a klasszikus portálon tekintheti meg.





![felhasználók panel az azure active Directoryval Azure-portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![felhasználók panel az azure active Directoryval a klasszikus Azure portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

Az a **felhasználók** mindkét portálok a külső felhasználók felismeri a nézet:

* A különböző ikon írja be az Azure-portálon
* A különböző sourcing pont a klasszikus portál

Azonban biztosítása **tulajdonos** vagy **közreműködő** a külső felhasználók elérését a **előfizetés** hatókörét, nem engedélyezi a hozzáférést a rendszergazda felhasználó könyvtárába, kivéve, ha a **Globális rendszergazda** lehetővé teszi. A felhasználói tulajdonságokat a **felhasználótípust** két közös paramétert tartalmaz **tag** és **vendég** azonosítható legyen. Egy tag a felhasználó, amely a címtárban regisztrálva van, míg a Vendég egy meghívót, hogy a könyvtár külső forrásból származó felhasználói. További információkért lásd: [hogyan Azure Active Directory rendszergazdák hozzá B2B együttműködés felhasználók](active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Győződjön meg arról, hogy a portál a hitelesítő adatok megadása után, a külső felhasználó kiválasztja a megfelelő könyvtárban való bejelentkezéshez. Azonos is van a több könyvtárak eléréséhez és az Azure-portálon jobb felső felhasználónév kattintva válassza ki vagy az egyik és a felhasználónak a legördülő listából válassza ki a megfelelő könyvtár.

A Vendég a címtárban, miközben a külső felhasználó kezelheti az Azure-előfizetéshez tartozó összes erőforrást, de a könyvtár nem hozzáférhető.





![az azure active Directoryval az Azure-portálhoz korlátozott hozzáférés](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Az Azure Active Directory és az Azure-előfizetés nem rendelkezik egy szülő-gyermek kapcsolat, például a más Azure-erőforrások (például: virtuális gépek, virtuális hálózatok, a webalkalmazások, tárolási stb.) az Azure-előfizetéssel rendelkezik. Minden az utóbbi létrehozott, kezelt és fizetni az Azure-előfizetéssel, míg az Azure-előfizetések az Azure-címtár való hozzáférés kezelése. További részletekért lásd: [hogyan egy Azure-előfizetéshez az Azure AD kapcsolódó](/active-directory/active-directory-how-subscriptions-associated-directory).

Az összes a beépített RBAC szerepkörből **tulajdonos** és **közreműködő** minden erőforrásoknak a környezetben, a különbség a teljes felügyeleti hozzáférést nyújtanak, előfordulhat, hogy egy közreműködői nem hozható létre, majd új RBAC-szerepkörök törlése . A beépített szerepkörök, például **virtuális gép közreműködő** csak az erőforrásokat, függetlenül attól, hogy a név által jelzett összes felügyeleti hozzáférést nyújtanak a **erőforráscsoport** történő létrehozásuk alatt.

A beépített RBAC szerepe hozzárendelése **virtuális gép közreműködő** egy előfizetés szintjén azt jelenti, hogy a felhasználó a szerepét:

* Megtekintheti az összes virtuális gép függetlenül azok telepítési dátumát és az erőforráscsoportok részét képezik
* A virtuális gépek összes felügyeleti hozzáféréssel rendelkezik az előfizetéshez
* Az előfizetést nem lehet megtekinteni, bármilyen más típusú erőforrások
* A módosításokat a számlázási szempontjából nem tud működni.

> [!NOTE]
> Az RBAC alatt az Azure portál csak szolgáltatásai, azt nem adja meg a klasszikus portál eléréséhez.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Beépített RBAC szerepkör hozzárendelése egy külső felhasználó
A különböző esetén ez a vizsgálat, a külső felhasználó a "alflanigan@gmail.com" meg van adva egy **virtuális gép közreműködő**.




![virtuális gép közreműködő beépített szerepkör](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

A normál beépített szerephez a külső felhasználó működése áttekinthetők és felügyelhetők a csak virtuális gépek és a szomszédos erőforrás-kezelő csak szükséges erőforrások telepítése közben. Úgy lett kialakítva, a korlátozott szerepkörök ajánlat csak a saját Azure-portálon létrehozott levelező erőforrásokhoz való hozzáférés, attól függetlenül történik néhány továbbra is telepíthető a klasszikus portálon (például: virtuális gépek).





![virtuális gép közreműködői szerepkör áttekintése Azure-portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Hozzáférés egy felhasználó ugyanabban a könyvtárban egy előfizetés szintjén
A folyamat megegyezik a külső felhasználók hozzáadása, mind a felügyelet szempontjából megadását a Szerepalapú szerepkör, valamint a felhasználói hozzáférést megkapják a szerepkörhöz. Itt különbség, hogy a meghívott felhasználók nem kapják meg minden e-mailek meghívókat, a bejelentkezés után az előfizetésen belüli összes erőforrás hatókör az irányítópult elérhető lesz.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Az erőforrás csoport hatókörű RBAC-szerepkörök hozzárendelése
Hozzárendelése a Szerepalapú szerepet egy **erőforráscsoport** hatókörben van egy azonos folyamata hozzárendelése a szerepkört az előfizetés szintjén mindkét típusú felhasználók – külső vagy belső (könyvtárába része). A felhasználók, amelyek az RBAC-szerepkör, hogy tekintse meg a környezetben csak az erőforráscsoport van hozzájuk rendelve való hozzáférést a **erőforráscsoportok** ikon az Azure portálon.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Az erőforrás-hatókörben RBAC-szerepkörök hozzárendelése
Hozzárendelése az Azure-erőforrás hatókörre RBAC szerepet van egy azonos folyamata hozzárendelése a szerepkört az előfizetés szintjén vagy az erőforráscsoport szintjén, a következő mindkét forgatókönyvet ugyanabban a munkafolyamatban. Ebben az esetben a felhasználók, amelyek az RBAC-szerepkör csak akkor hozzárendelt hozzáférés, vagy elemek látható a **összes erőforrás** lapon vagy közvetlenül az irányítópulton.

Az RBAC egyaránt erőforrás csoporthatókör vagy erőforrás hatókör fontos eleme a felhasználók számára, ügyeljen arra, hogy jelentkezzen be a megfelelő könyvtárban van.





![Directory bejelentkezés az Azure-portálon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Egy Azure Active Directory csoport RBAC-szerepkörök hozzárendelése
Az RBAC használata az Azure-ban három különböző hatóköröket, minden helyzet kezeléséhez, a központi telepítését és a különböző erőforrások felügyelete személyes előfizetés kezelése igénye nélkül hozzárendelt felhasználóként jogosultság kínálnak. Attól függetlenül történik az RBAC szerepkör tartozik előfizetés, erőforráscsoporthoz vagy erőforrás hatókör, az összes az erőforrásokat a hozzárendelt felhasználók által létrehozott további a számlázása alatt, ahol a felhasználók rendelkeznek-e a hozzáférést egy Azure-előfizetéssel. Ezzel a módszerrel számlázási a teljes Azure-előfizetéshez rendszergazdai jogosultságokkal rendelkező felhasználók rendelkezik teljes áttekintése a felhasználás, függetlenül az erőforrások kezel.

Nagyobb szervezeteknek figyelembe véve, hogy a felhasználót a rendszergazda hozzáférést szeretne biztosítani a részletes csoportok vagy részlegek teljes, nem különállóan minden felhasználónak, így annak eldöntéséhez, hogy a terv az Active Directory-csoportok esetében ugyanúgy alkalmazható RBAC-szerepkörök Ez különösen idő és a felügyeleti hatékony lehetőség. Ebben a példában mutatja be a **közreműködő** szerepkört az előfizetés szintjén a bérlő csoportok egyikéhez bővült.





![az AAD-csoportokat RBAC-szerepkör hozzáadása](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Ezek a csoportok üzembe helyezve, és csak az Azure Active Directoryban felügyelni biztonsági csoportokat.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Nyissa meg a támogatási kérelmek PowerShell használatával történő egyéni RBAC szerepkör létrehozása
A beépített RBAC-szerepkörök az Azure-ban rendelkezésre álló gondoskodjon arról, hogy bizonyos jogosultsági szintek a környezetben elérhető erőforrások alapján. Azonban ezek a szerepkörök egyike a rendszergazdai jogú felhasználó saját igényeinek megfelelően, ha nincs a beállítás a egyéni RBAC-szerepkörök létrehozásával még jobban hozzáférés korlátozásához.

Egyéni RBAC-szerepkörök létrehozásához egy beépített szerep, szerkesztheti, majd importálja vissza a környezetben. A letöltés és a szerepkör feltöltése felügyelt PowerShell vagy a parancssori felület használatával.

Fontos megismerni az előfeltételeket részletes hozzáférést biztosíthat az előfizetés szintjén és is lehetővé teszi a meghívott felhasználó rugalmasan megnyitása támogatási kérelmek létrehozása egy egyéni biztonsági szerepkört.

Ehhez a példához a beépített szerepkör **olvasó** engedélyezése a felhasználó a lehetőség a támogatási kérelmek megnyitása testreszabása lehetővé teszi a felhasználók elérését a erőforrás hatókörök megtekintése, de nem szerkesztheti azokat, vagy hozzon létre újakat.

Az első műveletet exportáló a **olvasó** emelt jogosultsági szintű rendszergazdaként futtatott szerepkör kell PowerShell leforgása alatt elvégezhetők.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![Az olvasó RBAC szerepkör PowerShell képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

Majd, vissza kell fejteni a JSON-sablon a szerepkör.





![Egyéni olvasó RBAC szerepkör JSON-sablon](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

Egy tipikus RBAC szerepkör áll kívül három fő szakasz **műveletek**, **NotActions** és **AssignableScopes**.

Az a **művelet** című szakaszában felsorolt összes engedélyezett műveletek ehhez a szerepkörhöz. Fontos megérteni, hogy minden műveletet olyan erőforrás-szolgáltató van hozzárendelve. Ebben az esetben a támogatási jegyek létrehozásának képességét a **Microsoft.Support** szerepelnie kell az erőforrás-szolgáltató.

Ahhoz, hogy összes az erőforrás-szolgáltató elérhető és az előfizetéshez regisztrált, használhatja a PowerShell.
```
Get-AzureRMResourceProvider

```
Ezenkívül ellenőrizheti az összes elérhető PowerShell parancsmagjainak az erőforrás-szolgáltatók kezelése.
    ![Az erőforrás-szolgáltató management PowerShell képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Az adott RBAC-szerepkörök a műveletek korlátozása, erőforrás-szolgáltatók szakaszban felsorolt **NotActions**.
Utolsó akkor kötelező, hogy a Szerepalapú szerepkör tartalmazza a explicit előfizetési azonosítók felhasználási. Az előfizetési azonosítók alatt a **AssignableScopes**, ellenkező esetben azt nem használhatók az előfizetésében szerepkört importálni.

Után létrehozása és testreszabása az RBAC-szerepkör, importálandók kell biztonsági másolatot a környezet.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

Ebben a példában a Szerepalapú szerepkörhöz tartozó egyéni neve "Olvasó támogatási jegyek hozzáférési szint" a felhasználó az előfizetéshez mindent megtekinthetnek és nyissa meg a támogatási kérelmek.

> [!NOTE]
> A támogatási kérelmek nyitó művelet engedélyezése csak két beépített RBAC szerep **tulajdonos** és **közreműködő**. A felhasználó megnyithatja a támogatási kérelmek ő szerepkört kell hozzárendelni egy Szerepalapú csak az előfizetési hatókört, mert összes támogatási kérelmek létrehozása az Azure-előfizetés alapján.

Az új egyéni szerepkör van rendelve egy felhasználó, az ugyanabban a könyvtárban.





![az Azure portálon importálni egyéni RBAC szerepkör képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![Képernyőkép a egyéni importált RBAC szerepkör hozzárendelése felhasználói ugyanabban a könyvtárban](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![egyéni importált RBAC szerepkör engedélyeinek képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

A példa további részletes hogy az egyéni RBAC szerepkör határain hangsúlyozzák az alábbiak szerint:
* Új támogatási kérelmek hozhat létre.
* Nem hozható létre új erőforrás hatókörök (például: virtuális gép)
* Nem hozható létre új erőforrás-csoportok





![Képernyőkép a támogatási kérelmek létrehozása egyéni RBAC szerepkör](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![nem sikerült létrehozni a virtuális gépek egyéni RBAC szerepkör képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![nem sikerült létrehozni az új RGs egyéni RBAC szerepkör képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Nyissa meg a támogatási kérelmek Azure parancssori felület használatával történő egyéni RBAC szerepkör létrehozása
A Mac és a PowerShell való hozzáférés nélkül fut, az Azure parancssori felület módja a nyissa meg.

Egy egyéni biztonsági szerepkört létrehozásának a lépései megegyeznek, CLI-vel a szerepkör nem tölti le a JSON-sablon, de a a parancssori Felülettel megtekinthetők kivételével.

Ehhez a példához I választotta, a beépített szerepkör **biztonsági mentés olvasó**.

```

azure role show "backup reader" --json

```





![Parancssori felület Képernyőkép a biztonsági mentési olvasó szerepkört megjelenítése](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

A szerepkör a Visual Studio módosítása után a tulajdonságokat a JSON-sablon másolása a **Microsoft.Support** erőforrás-szolgáltató hozzá lett adva a **műveletek** részek, hogy a felhasználó megnyithatja támogatása kérelmek, miközben továbbra is a mentési tárolók olvasójának lehet. Újra kell hozzáadnia az előfizetés-azonosító, amelyeken ezt a szerepkört kell használni a rendszer a **AssignableScopes** szakasz.

```

azure role create --inputfile <path>

```





![Egyéni RBAC szerepkör importálása CLI képernyőképe](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

Az új szerepkör már elérhető az Azure portálon, és a hozzárendeléseket folyamat megegyezik az előző példához hasonlóan.





![Az Azure portál Képernyőkép a CLI 1.0 használatával létrehozott egyéni RBAC szerepkör](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

A legújabb Build 2017 frissítésétől az Azure-felhő rendszerhéj általánosan elérhető. Azure Cloud rendszerhéj egészíti ki IDE és az Azure-portálon. Ezzel a szolgáltatással hitelesítése és Azure-ban üzemeltetett egy webböngésző-alapú rendszerhéj kap, és használhatja a számítógépen telepített parancssori felület helyett.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
