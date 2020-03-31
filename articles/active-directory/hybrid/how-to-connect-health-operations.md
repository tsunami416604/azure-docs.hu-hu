---
title: Az Azure Active Directory csatlakozásállapot-alapú műveletei
description: Ez a cikk az Azure AD Connect Health üzembe helyezése után elvégezhető további műveleteket ismerteti.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef908429d359020282920d73480a472bfde0aa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261514"
---
# <a name="azure-active-directory-connect-health-operations"></a>Az Azure Active Directory csatlakozásállapot-alapú műveletei
Ez a témakör ismerteti a különböző műveleteket az Azure Active Directory (Azure AD) Connect Health használatával hajthatja végre.

## <a name="enable-email-notifications"></a>E-mailértesítések engedélyezése
Konfigurálhatja az Azure AD Connect Health szolgáltatás e-mail értesítések küldésére, ha riasztások azt jelzik, hogy az identitás-infrastruktúra nem kifogástalan. Ez akkor fordul elő, ha egy riasztás jön létre, és ha feloldódik.

![Képernyőkép az Azure AD Connect Health e-mail értesítési beállításairól](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Az e-mail értesítések alapértelmezés szerint engedélyezve vannak.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Az Azure AD Connect Health e-mail értesítések engedélyezése
1. Nyissa **meg** annak a szolgáltatásnak a Riasztások paneljét, amelyről e-mailben értesítést szeretne kapni.
2. A műveletsávon kattintson az **Értesítési beállítások gombra.**
3. Az e-mail értesítési kapcsolón válassza **a BE**lehetőséget.
4. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy az összes globális rendszergazda e-mailben értesítéseket kapjon.
5. Ha bármely más e-mail-címen szeretne e-mailértesítéseket kapni, adja meg őket a **További e-mail címzettek** mezőben. Ha el szeretne távolítani egy e-mail címet a listából, kattintson a jobb gombbal a bejegyzésre, és válassza a **Törlés parancsot.**
6. A módosítások véglegesítéséhez kattintson a **Mentés gombra.** A módosítások csak a mentés után lépnek érvénybe.

>[!NOTE] 
> Ha a háttérszolgáltatásban problémák merülnek fel a szinkronizálási kérelmek feldolgozásával kapcsolatban, a szolgáltatás egy értesítő e-mailt küld a hiba részleteivel a bérlő felügyeleti kapcsolattartójának e-mail címére(i). Hallottuk az ügyfelek visszajelzéseit, hogy bizonyos esetekben az ilyen üzenetek mennyisége megfizethetetlenül nagy, ezért megváltoztatjuk az üzenetek küldésének módját. 
>
> Ahelyett, hogy minden szinkronizálási hibához küldenénk egy üzenetet, minden alkalommal, amikor bekövetkezik, napi kivonatot küldünk ki a háttérszolgáltatás által visszaadott összes hibáról. Ez lehetővé teszi az ügyfelek számára, hogy hatékonyabban dolgozzák fel ezeket a hibákat, és csökkenti az ismétlődő hibaüzenetek számát.
>
> Azt tervezzük, hogy ezt a változtatást 2020.

## <a name="delete-a-server-or-service-instance"></a>Kiszolgáló vagy szolgáltatáspéldány törlése

>[!NOTE] 
> A törlési lépésekhez Azure AD prémium licenc szükséges.

Bizonyos esetekben előfordulhat, hogy el szeretne távolítani egy kiszolgálót a figyelésből. Az alábbiakban kell tudnia, hogy eltávolítson egy kiszolgálót az Azure AD Connect állapotszolgáltatásból.

Kiszolgáló törlésekor vegye figyelembe az alábbiakat:

* Ez a művelet leállítja a további adatok gyűjtését a kiszolgálóról. Ez a kiszolgáló törlődik a figyelési szolgáltatásból. A művelet után nem tekintheti meg a kiszolgáló új riasztási, figyelési vagy használati elemzési adatait.
* Ez a művelet nem távolítja el az állapotügynököt a kiszolgálóról. Ha a lépés végrehajtása előtt nem távolítja el az állapotügynököt, a kiszolgálón az állapotügynökkel kapcsolatos hibák jelenhetnek meg.
* Ez a művelet nem törli a kiszolgálóról már összegyűjtött adatokat. Ezek az adatok törlődnek az Azure adatmegőrzési szabályzatának megfelelően.
* A művelet végrehajtása után, ha újra meg szeretné figyelni ugyanazt a kiszolgálót, el kell távolítania, majd újra kell telepítenie az állapotügynököt ezen a kiszolgálón.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Kiszolgáló törlése az Azure AD Connect health szolgáltatásból

>[!NOTE] 
> A törlési lépésekhez Azure AD prémium licenc szükséges.

Azure AD Connect Health az Active Directory összevonási szolgáltatásokhoz (AD FS) és az Azure AD Connect (Szinkronizálás):

1. Nyissa meg a **Kiszolgáló** panelt a **Kiszolgálólista** panelen az eltávolítandó kiszolgáló nevének kiválasztásával.
2. A **Kiszolgáló** panelen, a műveletsávon kattintson a **Törlés gombra.**
![Képernyőkép az Azure AD Connect állapotának törlési kiszolgálójáról](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Erősítse meg a kiszolgáló nevének beírásával a megerősítő mezőbe.
4. Kattintson a **Törlés** gombra.

Azure AD Connect Health for Azure Active Directory tartományi szolgáltatások:

1. Nyissa meg a **Tartományvezérlők irányítópultot.**
2. Válassza ki az eltávolítandó tartományvezérlőt.
3. A műveletsávon kattintson a **Kijelölt törlése gombra.**
4. Erősítse meg a kiszolgáló törlésére irányuló műveletet.
5. Kattintson a **Törlés** gombra.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Szolgáltatáspéldány törlése az Azure AD Connect health szolgáltatásból
Bizonyos esetekben előfordulhat, hogy el szeretne távolítani egy szolgáltatáspéldányt. Az alábbiakban kell tudnia egy szolgáltatáspéldány eltávolításához az Azure AD Connect állapotszolgáltatásból.

Szolgáltatáspéldány törlésekor vegye figyelembe a következőket:

* Ez a művelet eltávolítja az aktuális szolgáltatáspéldányt a figyelési szolgáltatásból.
* Ez a művelet nem távolítja el és nem távolítja el az állapotügynököt a szolgáltatáspéldány részeként figyelt kiszolgálókról. Ha a lépés végrehajtása előtt nem távolítja el az állapotügynököt, a kiszolgálókon az állapotügynökkel kapcsolatos hibák jelenhetnek meg.
* A szolgáltatáspéldány összes adatát az Azure adatmegőrzési szabályzatának megfelelően törli.
* A művelet végrehajtása után, ha meg szeretné kezdeni a szolgáltatás figyelését, távolítsa el és telepítse újra az állapotügynököt az összes kiszolgálón. A művelet végrehajtása után, ha újra meg szeretné figyelni ugyanazt a kiszolgálót, távolítsa el, telepítse újra és regisztrálja az állapotügynököt a kiszolgálón.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Szolgáltatáspéldány törlése az Azure AD Connect health szolgáltatásból
1. Nyissa meg a **Szolgáltatás** panelt a **Szolgáltatáslista** panelen az eltávolítani kívánt szolgáltatásazonosító (farmnév) kiválasztásával. 
2. A **Szolgáltatás** panelen, a műveletsávon kattintson a **Törlés gombra.** 
![Képernyőkép az Azure AD Connect állapottörlési szolgáltatásáról](./media/how-to-connect-health-operations/DeleteServer.png)
3. Erősítse meg a szolgáltatás nevét a megerősítő mezőbe (például: sts.contoso.com).
4. Kattintson a **Törlés** gombra.
   <br><br>

[//]: # (Az RBAC szakasz kezdete)
## <a name="manage-access-with-role-based-access-control"></a>Hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel
[Szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) az Azure AD Connect Health hozzáférést biztosít a felhasználók és csoportok nem a globális rendszergazdák számára. Az RBAC szerepköröket rendel a tervezett felhasználókhoz és csoportokhoz, és lehetővé teszi a címtárglobális rendszergazdáinak korlátozását.

### <a name="roles"></a>Szerepkörök
Az Azure AD Connect Health a következő beépített szerepköröket támogatja:

| Szerepkör | Engedélyek |
| --- | --- |
| Tulajdonos |A tulajdonosok *kezelhetik a hozzáférést* (például szerepkört rendelhetnek egy felhasználóhoz vagy csoporthoz), megtekinthetik a *portálon* található összes információt (például a riasztásokmegtekintését), és módosíthatják a *beállításokat* (például e-mail értesítéseket) az Azure AD Connect Health szolgáltatáson belül. <br>Alapértelmezés szerint az Azure AD globális rendszergazdák vannak rendelve ezt a szerepkört, és ez nem módosítható. |
| Közreműködő |A közreműködők *megtekinthetik* az összes információt (például a riasztásokmegtekintését) a portálról, és módosíthatják a *beállításokat* (például e-mail értesítéseket) az Azure AD Connect Health-ben. |
| Olvasó |Az olvasók *megtekinthetik* az összes információt (például a riasztásokat) az Azure AD Connect Health portálon belül. |

Az összes többi szerepkör (például a felhasználói hozzáférés rendszergazdái vagy a DevTest Labs-felhasználók) nincs hatással az Azure AD Connect Health elérésére, még akkor sem, ha a szerepkörök elérhetők a portálkörnyezetben.

### <a name="access-scope"></a>Hozzáférési hatókör
Az Azure AD Connect Health két szinten támogatja a hozzáférés kezelését:

* **Minden szolgáltatáspéldány :** A legtöbb esetben ez az ajánlott elérési út. Szabályozza a hozzáférést az összes szolgáltatáspéldány (például egy AD FS-farm) az Összes szerepkör-típusok, amelyek az Azure AD Connect Health által figyelt.
* **Szolgáltatáspéldány**: Bizonyos esetekben előfordulhat, hogy szerepkörtípusok vagy egy szolgáltatáspéldány alapján kell elkülönítenia a hozzáférést. Ebben az esetben kezelheti a hozzáférést a szolgáltatáspéldány szintjén.  

Az engedély akkor adható meg, ha a végfelhasználó a címtár vagy a szolgáltatáspéldány szintjén rendelkezik hozzáféréssel.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Felhasználók vagy csoportok hozzáférésének engedélyezése az Azure AD Connect Health szolgáltatáshoz
A következő lépések bemutatják, hogyan engedélyezheti a hozzáférést.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1. lépés: Válassza ki a megfelelő hozzáférési hatókört
Ha engedélyezni szeretné a felhasználói hozzáférést az Azure AD Connect Health *összes szolgáltatáspéldányszintjén,* nyissa meg az Azure AD Connect Health fő paneljét.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2. lépés: Felhasználók és csoportok hozzáadása és szerepkörök hozzárendelése
1. A **Konfigurálás** csoportban kattintson a **Felhasználók gombra.**<br>
   ![Képernyőkép az Azure AD Connect Health erőforrás oldalsávjáról](./media/how-to-connect-health-operations/startRBAC.png)
2. Válassza a **Hozzáadás** lehetőséget.
3. A **Szerepkör kiválasztása** ablaktáblában jelöljön ki egy szerepkört (például **Tulajdonos).**<br>
   ![Képernyőkép az Azure AD Connect Health RBAC-felhasználók ablakáról](./media/how-to-connect-health-operations/RBAC_add.png)
4. Írja be a megcélzott felhasználó vagy csoport nevét vagy azonosítóját. Egyszerre egy vagy több felhasználót vagy csoportot is kijelölhet. Kattintson a **Kiválasztás** gombra.
   ![Képernyőkép az Azure AD Connect Health RBAC-felhasználók ablakáról](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Válassza **az OK gombot.**<br>
6. A szerepkör-hozzárendelés befejezése után a felhasználók és csoportok megjelennek a listában.<br>
   ![Képernyőkép az Azure AD Connect Health RBAC-felhasználók ablakáról, kiemelve az új felhasználókkal](./media/how-to-connect-health-operations/RBAC_user_list.png)

Most a felsorolt felhasználók és csoportok a hozzárendelt szerepkörök szerint férhetnek hozzá.

> [!NOTE]
> * A globális rendszergazdák mindig teljes hozzáféréssel rendelkeznek az összes művelethez, de a globális rendszergazdai fiókok nem szerepelnek az előző listában.
> * A Felhasználók meghívása funkció nem támogatott az Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3. lépés: A panel helyének megosztása felhasználókkal vagy csoportokkal
1. Az engedélyek hozzárendelése után a felhasználó hozzáférhet az Azure AD Connect Health szolgáltatáshoz, ha [itt](https://aka.ms/aadconnecthealth)jár.
2. A panelen a felhasználó rögzítheti a panelt vagy annak különböző részeit a műszerfalra. Egyszerűen kattintson a **Pin a műszerfalhoz** ikonra.<br>
   ![Képernyőkép az Azure AD Connect Health RBAC pin panelről, kiemelve a pinikonnal](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> A Reader szerepkörrel rendelkező felhasználó nem tudja beolvasni az Azure AD Connect Health bővítményt az Azure Piactérről. A felhasználó nem tudja végrehajtani a szükséges "create" műveletet ehhez. A felhasználó továbbra is eljut a panel megy az előző linkre. Későbbi használatra a felhasználó rögzítheti a panelt az irányítópulton.
>
>

### <a name="remove-users-or-groups"></a>Felhasználók vagy csoportok eltávolítása
Eltávolíthatja az Azure AD Connect Health RBAC-hoz hozzáadott felhasználót vagy csoportot. Egyszerűen kattintson a jobb gombbal a felhasználóra vagy csoportra, és válassza **az Eltávolítás parancsot.**<br>
![Képernyőkép az Azure AD Connect Health RBAC-felhasználók ablakáról, kiemelve az Eltávolítás lehetőséggel](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (RBAC szakasz vége)

## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Az Azure AD Connect állapotügynök telepítése](how-to-connect-health-agent-install.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Az Azure AD Connect Health használata szinkronizáláshoz](how-to-connect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
