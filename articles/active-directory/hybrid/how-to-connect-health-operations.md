---
title: Az Azure Active Directory Connect Health műveletei
description: Ez a cikk ismerteti, amely az Azure AD Connect Health üzembe helyezését követően végezhető további műveletek.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: fdcbd0ac718efac98121f4d77ac62e618a1cc6a7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496245"
---
# <a name="azure-active-directory-connect-health-operations"></a>Az Azure Active Directory Connect Health műveletei
Ez a témakör ismerteti a különféle műveleteket hajthat végre az Azure Active Directory (Azure AD) Connect Health használatával.

## <a name="enable-email-notifications"></a>E-mail-értesítések engedélyezése
Konfigurálhatja az Azure AD Connect Health szolgáltatás e-mail-értesítések küldése, ha a riasztások jelzik, hogy az identitás-infrastruktúra állapota nem kifogástalan. Ez akkor fordul elő, amikor a rendszer riasztást állít elő, ha megoldották.

![Képernyőkép az Azure AD Connect Health e-mail értesítési beállítások](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mail-értesítések alapértelmezés szerint engedélyezve vannak.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Az Azure AD Connect Health e-mail értesítések engedélyezése
1. Nyissa meg a **riasztások** panelen a szolgáltatás, amelynek az e-mailben értesítést kapni szeretne.
2. A művelet sávon kattintson **értesítési beállítások**.
3. Válassza az e-mail értesítési kapcsoló **ON**.
4. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy minden globális rendszergazda, ha e-mail értesítéseket szeretne kapni.
5. Ha más e-mail-címeket, email értesítéseket kapni szeretné, adja meg azokat a **E-mail további címzettjei** mezőbe. Az e-mail címének eltávolításához ebből a listából, kattintson a jobb gombbal az bejegyzést, és válassza ki **törlése**.
6. A módosítások véglegesítéséhez, kattintson a **mentése**. Módosítások mentése után csak lépnek érvénybe.

## <a name="delete-a-server-or-service-instance"></a>Egy kiszolgáló vagy szolgáltatáspéldány törlése

>[!NOTE] 
> Az Azure AD prémium licencre szükség a törlés lépéseihez.

Bizonyos esetekben érdemes a figyelt kiszolgáló eltávolítása. Íme, amit tudni, hogy egy kiszolgáló eltávolítása az Azure AD Connect Health szolgáltatásba.

Ha már törölni a kiszolgálót, vegye figyelembe a következőket:

* Ez a művelet leállítja a további adatok gyűjtése arról a kiszolgálóról. Ez a kiszolgáló törlődik a figyelési szolgáltatásból. Ez a művelet után Ön nem tudja megtekinteni az új riasztások, figyelési vagy használati elemzési adatok ehhez a kiszolgálóhoz.
* Ez a művelet nem távolítja el a Health Agent a kiszolgálóról. Ez a lépés végrehajtása előtt nem távolította el a Health Agent, láthatja a kiszolgálón a Health Agent kapcsolódó hibák.
* Ez a művelet nem törli a erről a kiszolgálóról már begyűjtött adatokat. Az adatokat az Azure adatmegőrzési szabályzatának megfelelően törlődik.
* A művelet végrehajtása után, ha el szeretné indítani a figyelést ugyanazon a kiszolgálón, újra kell távolítania, és telepítse újra a Health Agent ezen a kiszolgálón.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Kiszolgáló törlése az Azure AD Connect Health szolgáltatás

>[!NOTE] 
> Az Azure AD prémium licencre szükség a törlés lépéseihez.

Az Azure AD Connect Health for Active Directory összevonási szolgáltatások (AD FS) és az Azure AD Connect (Sync):

1. Nyissa meg a **kiszolgáló** panelt a a **kiszolgálólista** panelen válassza a kiszolgáló nevét, el kell távolítani.
2. Az a **kiszolgáló** paneljén, az műveletsávon kattintson **törlése**.
![Képernyőkép az Azure AD Connect Health-kiszolgáló törlése](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Erősítse meg a megerősítés mezőben írja be a kiszolgáló nevét.
4. Kattintson a **Törlés** gombra.

Az Azure AD Connect Health az Azure Active Directory tartományi szolgáltatások:

1. Nyissa meg a **tartományvezérlők** irányítópultot.
2. Válassza ki a tartományvezérlő, el kell távolítani.
3. A művelet sávon kattintson **törli a kiválasztott**.
4. Erősítse meg a műveletet úgy, hogy törli a kiszolgálót.
5. Kattintson a **Törlés** gombra.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Az Azure AD Connect Health szolgáltatás egy szolgáltatáspéldány törlése
Bizonyos esetekben érdemes eltávolítani egy szolgáltatáspéldány. Íme egy szolgáltatáspéldány eltávolítása az Azure AD Connect Health szolgáltatásnak tudnia kell.

Ha törölni egy szolgáltatáspéldány, vegye figyelembe a következőket:

* Ez a művelet eltávolítja az aktuális szolgáltatáspéldányt a figyelési szolgáltatásból.
* Ez a művelet nem távolítja el a Health Agent egyetlen olyan kiszolgálót, a szolgáltatáspéldány részeként is figyeli. Ez a lépés végrehajtása előtt nem távolította el a Health Agent, láthatja a kiszolgálókon a Health Agent kapcsolódó hibák.
* A szolgáltatáspéldány összes adata törlődik az Azure adatmegőrzési szabályzatának megfelelően.
* A művelet végrehajtása után ha szeretné figyelni a szolgáltatást, távolítsa el, és telepítse újra a Health-ügynököt az összes kiszolgálón. A művelet végrehajtása után ha szeretné figyelni újra ugyanazon a kiszolgálón, távolítsa el, telepítse újra a, és regisztrálja a Health Agent, az adott kiszolgálón.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Szolgáltatáspéldány törlése az Azure AD Connect Health szolgáltatás
1. Nyissa meg a **szolgáltatás** panelt a a **lista** panelen válassza a szolgáltatás azonosítója (a farm nevét), amely a el kívánja távolítani. 
2. Az a **szolgáltatás** paneljén, az műveletsávon kattintson **törlése**. 
![Képernyőkép az Azure AD Connect Health szolgáltatás törlése](./media/how-to-connect-health-operations/DeleteServer.png)
3. Erősítse meg a megerősítés mezőben írja be a szolgáltatás nevét (például: sts.contoso.com).
4. Kattintson a **Törlés** gombra.
   <br><br>

[//]: # (Az RBAC szakasz kezdete)
## <a name="manage-access-with-role-based-access-control"></a>A szerepköralapú hozzáférés-vezérléssel-hozzáférés kezelése
[Szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) az Azure AD Connect Health hozzáférést biztosít a felhasználók és csoportok a globális rendszergazdák kivételével. Az RBAC szerepkörök rendel az importálni kívánt felhasználók és csoportok, és korlátozhatja a globális rendszergazdák a címtáron belül mechanizmust biztosít.

### <a name="roles"></a>Szerepkörök
Az Azure AD Connect Health támogatja a következő beépített szerepkörök:

| Szerepkör | Engedélyek |
| --- | --- |
| Tulajdonos |Tulajdonosai *hozzáférés kezelése* (például szerepkör hozzárendelése egy felhasználóhoz vagy csoporthoz), *összes információját megjeleníthetik* (például a riasztások megtekintése) a portálról, és *beállításainak módosítása* (a például, e-mail-értesítések) az Azure AD Connect Health belül. <br>Alapértelmezés szerint az Azure AD globális rendszergazdái vannak hozzárendelve ehhez a szerepkörhöz, és ez nem módosítható. |
| Közreműködő |A közreműködők is *összes információját megjeleníthetik* (például a riasztások megtekintése) a portálról, és *beállításainak módosítása* (például e-mail-értesítések) belül az Azure AD Connect Health. |
| Olvasó |Olvasói is *összes információját megjeleníthetik* (például a riasztások megtekintése) belül az Azure AD Connect Health portálról. |

Minden más szerepkörök (például felhasználói rendszergazdák vagy a DevTest Labs-felhasználó) semmilyen hatást nem el az Azure AD Connect Health belül, akkor is, ha a szerepkörök érhetők el a portál felülete.

### <a name="access-scope"></a>Hozzáférési hatókör
Az Azure AD Connect Health két szinten hozzáférés kezelését támogatja:

* **Minden szolgáltatáspéldány**: Ez a legtöbb esetben ajánlott elérési. Azt szabályozza a hozzáférést az összes service-példány (például AD FS-farm) által az Azure AD Connect Health által figyelt összes szerepkör-típusa.
* **Szolgáltatáspéldány**: Bizonyos esetekben szüksége lehet, hogy elkülönítse a szerepkör-típusok, vagy egy szolgáltatáspéldány-alapú hozzáférés. Ebben az esetben a szolgáltatás példányszintű hozzáférést is kezelheti.  

Engedélyt kapnak, ha egy végfelhasználó hozzáfér a directory vagy a szolgáltatás szintű példány.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Lehetővé teszi a felhasználók vagy csoportok hozzáférés az Azure AD Connect Health
A következő lépések bemutatják, hogyan engedélyezi a hozzáférést.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1. lépés: Válassza ki a megfelelő hozzáférési hatókör
A felhasználói hozzáférés engedélyezéséhez a *minden szolgáltatáspéldány* szintű Azure AD Connect Health belül, nyissa meg az Azure AD Connect Health fő panelje.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2. lépés: Felhasználók és csoportok hozzáadása, és a szerepkörök hozzárendelésére
1. Az a **konfigurálása** területén kattintson **felhasználók**.<br>
   ![Képernyőkép az Azure AD Connect Health erőforrás oldalsáv](./media/how-to-connect-health-operations/startRBAC.png)
2. Válassza a **Hozzáadás** lehetőséget.
3. Az a **Szerepkörválasztás** ablaktáblán válassza ki a megfelelő szerepkör (például **tulajdonosa**).<br>
   ![Képernyőkép az Azure AD Connect Health RBAC felhasználók ablak](./media/how-to-connect-health-operations/RBAC_add.png)
4. Írja be a nevet vagy a megcélzott felhasználó vagy csoport azonosítója. Választhat egy vagy több felhasználót vagy csoportot egy időben. Kattintson a **Kiválasztás** gombra.
   ![Képernyőkép az Azure AD Connect Health RBAC felhasználók ablak](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Kattintson az **OK** gombra.<br>
6. A szerepkör-hozzárendelés befejezése után a felhasználók és csoportok szerepelnek a listán.<br>
   ![Képernyőkép az Azure AD Connect Health RBAC felhasználók ablakról, amelyen kiemelve az új felhasználók](./media/how-to-connect-health-operations/RBAC_user_list.png)

Most már a listán szereplő felhasználók és csoportok rendelkezik hozzáféréssel, a hozzárendelt szerepkörök alapján.

> [!NOTE]
> * A globális rendszergazdák mindig összes művelet teljes hozzáféréssel rendelkezik, de a globális rendszergazdai fiókok nem szerepelnek az előző listában.
> * A felhasználó meghívása funkció nem támogatott az Azure AD Connect Health belül.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3. lépés: A panel helye megoszthatja a felhasználók vagy csoportok
1. Engedélyek hozzárendelése után a felhasználó hozzáférhet-e az Azure AD Connect Health címen [Itt](https://aka.ms/aadconnecthealth).
2. A panelen a felhasználó rögzítheti a panelt, vagy különböző részeit, az irányítópulton. Egyszerűen kattintson a **rögzítés az irányítópulton** ikonra.<br>
   ![Képernyőkép az Azure AD Connect Health RBAC PIN-kód panelen, a rögzítés ikonja kiemelve](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Az olvasói szerepkörrel rendelkező felhasználók nem sikerül az Azure Marketplace-ről az Azure AD Connect Health bővítmény beszerzése. A felhasználó nem tudja végrehajtani a "create" ehhez a művelethez szükséges. A felhasználó elérheti a panelen nyissa meg a fenti hivatkozással. Későbbi használatra a felhasználó rögzítheti az irányítópulton a panelt.
>
>

### <a name="remove-users-or-groups"></a>Felhasználók vagy csoportok eltávolítása
A felhasználó vagy egy csoport hozzáadva a az Azure AD Connect Health RBAC eltávolíthatja. Egyszerűen kattintson a jobb gombbal a felhasználót vagy csoportot, és válassza ki **eltávolítása**.<br>
![Képernyőkép az Azure AD Connect Health RBAC felhasználók ablakról, amelyen kiemelve eltávolítása](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Az RBAC szakasz)

## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](how-to-connect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
