---
title: "Az Azure Active Directory Connect Health üzemeltetése"
description: "Ez a cikk ismerteti a további műveleteket, miután telepítette az Azure AD Connect Health végrehajtható."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 5e6910ea52f880e28378c9bf7fde02d080bc2e58
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-connect-health-operations"></a>Az Azure Active Directory Connect Health üzemeltetése
Ez a témakör ismerteti a különböző műveletek Azure Active Directory (Azure AD) Connect Health használatával végezheti el.

## <a name="enable-email-notifications"></a>E-mail értesítések engedélyezése
Konfigurálhatja az Azure AD Connect Health szolgáltatás e-mail értesítést, ha a riasztás azt jelzi, hogy a identitás-infrastruktúra nem működik megfelelően. Ez akkor fordul elő, amikor a rendszer riasztást állít elő, ha meg oldva.

![Képernyőfelvétel az Azure AD Connect Health e-mail értesítési beállítások](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> Értesítő e-mailek alapértelmezés szerint engedélyezve vannak.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Az Azure AD Connect Health e-mail értesítések engedélyezése
1. Nyissa meg a **riasztások** legyen az e-mail értesítéseket kapjanak, a szolgáltatás paneljét.
2. Az műveletsávon kattintson **értesítési beállításainak**.
3. Válassza az e-mail értesítési kapcsoló **ON**.
4. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy az összes globális rendszergazdák e-mail értesítéseket.
5. Ha szeretne kapni az értesítő e-mailek, más e-mail címet, adja meg azokat a **további e-mailek címzettjeinek** mezőbe. Az e-mail címének eltávolításához ebből a listából, kattintson a jobb gombbal az bejegyzést, és válassza ki **törlése**.
6. A módosítások véglegesítéséhez, kattintson a **mentése**. Módosítások mentése után csak lépnek érvénybe.

## <a name="delete-a-server-or-service-instance"></a>Egy kiszolgáló vagy a szolgáltatás példány törlése

Bizonyos esetekben érdemes a figyelt kiszolgáló eltávolítása. Itt van szüksége lehet az Azure AD Connect Health szolgáltatás eltávolít egy kiszolgálót.

Amikor egy kiszolgáló törölni, vegye figyelembe a következőket:

* Ez a művelet leáll, a kiszolgáló további adatok begyűjtését. Ez a kiszolgáló törlődik a figyelési szolgáltatásból. Ez a művelet után áll nem tudja megtekinteni az új riasztások, a figyelés, vagy a használati analitikai adatok ehhez a kiszolgálóhoz.
* Ez a művelet nem távolítja el a Health Agent ügynököt a kiszolgálóról. Ha nem távolította el a Health Agent ügynököt a lépés végrehajtása előtt, láthatja a rendszerállapot-ügynöke, a kiszolgálón kapcsolatos hibákat.
* Ez a művelet nem törli az erről a kiszolgálóról már begyűjtött adatokat. El az adatok törlése az Azure adatmegőrzési házirend szerint.
* Ez a művelet elvégzése után Ha el szeretné indítani a figyelést ugyanazon a kiszolgálón újra, akkor távolítsa el és telepítse újra a Health Agent ügynököt a kiszolgálón.

### <a name="to-delete-a-server-from-the-azure-ad-connect-health-service"></a>Kiszolgáló törlése az Azure AD Connect Health szolgáltatás
Az Azure AD Connect Health Active Directory összevonási szolgáltatások (AD FS) és az Azure AD Connect (Sync):

1. Nyissa meg a **Server** a panel a **kiszolgálólista** panel eltávolítani kívánt kiszolgáló nevét kiválasztva.
2. Az a **Server** paneljén műveletsávon kattintson **törlése**.
3. Erősítse meg a megerősítő mezőbe, írja be a kiszolgáló nevét.
4. Kattintson a **Törlés** gombra.

Az Azure AD Connect Health az Azure Active Directory tartományi szolgáltatások:

1. Nyissa meg a **tartományvezérlők** irányítópult.
2. Válassza ki az eltávolítani kívánt tartományvezérlő.
3. Az műveletsávon kattintson **törli a kijelölt**.
4. Erősítse meg a műveletet, a kiszolgáló törlése.
5. Kattintson a **Törlés** gombra.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>A szolgáltatáspéldány törlése az Azure AD Connect Health szolgáltatással
Bizonyos esetekben előfordulhat, hogy eltávolítani kívánt egy szolgáltatáspéldány. Ez egy szolgáltatáspéldány eltávolítása az Azure AD Connect Health szolgáltatásnak tudnia kell.

Amikor a szolgáltatáspéldány törölni, vegye figyelembe a következőket:

* Ez a művelet eltávolítja az aktuális szolgáltatáspéldányt a figyelési szolgáltatásból.
* Ez a művelet nem távolítja el a Health Agent eltávolítása a szolgáltatáspéldány részeként volt figyelt kiszolgálók. Ha nem távolította el a Health Agent ügynököt a lépés végrehajtása előtt, láthatja a Health Agent ügynököt a kiszolgálón kapcsolatos hibákat.
* A szolgáltatáspéldány összes adat törlődik az Azure adatmegőrzési házirend szerint.
* A művelet végrehajtása után Ha el szeretné indítani a figyelést a szolgáltatáshoz, távolítsa el, és telepítse újra a Health Agent ügynököt a kiszolgálón. A művelet végrehajtása után ha szeretné figyelni újra ugyanarra a kiszolgálóra, távolítsa el, telepítse újra, és regisztrálja a rendszerállapot-ügynöke ezen a kiszolgálón.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>A szolgáltatáspéldány törlése az Azure AD Connect Health szolgáltatás
1. Nyissa meg a **szolgáltatás** a panel a **lista** ; ehhez válassza a szolgáltatás azonosítója (farm neve), amely az eltávolítani kívánt panel.
2. Az a **Server** paneljén műveletsávon kattintson **törlése**.
3. Erősítse meg a megerősítést kérő párbeszédpanelen írja be a szolgáltatás nevét (például: sts.contoso.com).
4. Kattintson a **Törlés** gombra.
   <br><br>

[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Hozzáférés kezelése a szerepköralapú hozzáférés-vezérlés
[Szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control-configure.md) az Azure AD Connect Health hozzáférést biztosít a felhasználók és csoportok globális rendszergazdák eltérő. Az RBAC szerepkört rendel hozzá a kívánt felhasználók számára csoportok és a globális rendszergazdák a címtáron belül korlátozni mechanizmust biztosít.

### <a name="roles"></a>Szerepkörök
Az Azure AD Connect Health támogatja a következő beépített szerepkörök:

| Szerepkör | Engedélyek |
| --- | --- |
| Tulajdonos |Tulajdonosai *hozzáférésének kezelése olyan* (például szerepkör hozzárendelése egy felhasználóhoz vagy csoporthoz), *összes információját megjeleníthetik* (például a riasztás megtekintése) a portálról, és *beállításainak módosítása* (például e-mail értesítések) az Azure AD Connect Health belül. <br>Alapértelmezés szerint az Azure AD globális rendszergazdák vannak hozzárendelve ehhez a szerepkörhöz, és ez nem módosítható. |
| Közreműködő |A közreműködők is *összes információját megjeleníthetik* (például a riasztás megtekintése) a portálról, és *beállításainak módosítása* (például e-mail értesítések) az Azure AD Connect Health belül. |
| Olvasó |Olvasók is *összes információját megjeleníthetik* (például a riasztás megtekintése) az Azure AD Connect Health belül a portálról. |

Minden más szerepkörök (például felhasználói rendszergazdák vagy a DevTest Labs felhasználók) befolyásolni eléréséhez belül az Azure AD Connect Health, akkor is, ha a szerepkörök elérhető a portál élményt nyújt.

### <a name="access-scope"></a>Hozzáférési hatókör
Az Azure AD Connect Health által támogatott két szintű hozzáférés-kezelés:

* **Minden szolgáltatáspéldány**: a legtöbb esetben ajánlott elérési útja. Azt szabályozza a hozzáférést minden szolgáltatáspéldány (például AD FS-farm) az Azure AD Connect Health által figyelt összes szerepkör-típusa.
* **Szolgáltatáspéldány**: bizonyos esetekben szükség lehet elkülönítse alapú felhasználóiszerepkör-típusokhoz vagy egy szolgáltatáspéldány való hozzáférést. Ebben az esetben a szolgáltatás példányszintű hozzáférését kezelheti.  

Engedélyt kap, ha a felhasználó hozzáfér a könyvtár vagy a szolgáltatás szintű példányt.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Engedélyezi a hozzáférést az Azure AD Connect Health
A következő lépések bemutatják, hogyan teszi lehetővé a hozzáférést.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1. lépés: Válassza ki a megfelelő hozzáférési hatókör
A felhasználói hozzáférést, a *minden szolgáltatáspéldány* belül az Azure AD Connect Health szinten, nyissa meg az Azure AD Connect Health fő panelje.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2. lépés: Felhasználók és csoportok hozzáadása, és szerepkörök hozzárendelése
1. Az a **konfigurálása** kattintson **felhasználók**.<br>
   ![Képernyőfelvétel az Azure AD Connect Health RBAC fő panelen a kijelölt felhasználókkal](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Válassza a **Hozzáadás** lehetőséget.
3. Az a **Szerepkörválasztás** ablaktáblán válassza ki a megfelelő szerepkör (például **tulajdonos**).<br>
   ![Képernyőfelvétel az Azure AD Connect Health RBAC felhasználók ablak](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Írja be a nevet vagy a célzott felhasználó vagy csoport azonosítója. Választhat egy vagy több felhasználók vagy csoportok egyszerre. Kattintson a **Kiválasztás** gombra.
   ![Képernyőfelvétel az Azure AD Connect Health RBAC felhasználók ablak](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Kattintson az **OK** gombra.<br>
6. A szerepkör-hozzárendelés befejezése után a felhasználók és csoportok szerepelnek a listán.<br>
   ![Képernyőfelvétel az Azure AD Connect Health RBAC felhasználók-ablakot, kiemelve az új felhasználók](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Most már a listán szereplő felhasználók és csoportok hozzáféréssel rendelkezik, a hozzárendelt szerepköröknek megfelelően.

> [!NOTE]
> * Globális rendszergazdák mindig rendelkezik teljes körű hozzáférés az összes műveletet, de a globális rendszergazdai fiókok nincsenek jelen az előző listában.
> * A felhasználók meghívása nem támogatja az Azure AD Connect Health belül.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3. lépés: A panel helye megoszthatja a felhasználók vagy csoportok
1. Engedélyek hozzárendelése után a felhasználó hozzáférhet-e az Azure AD Connect Health címen [Itt](http://aka.ms/aadconnecthealth).
2. A panelen a felhasználó rögzíthető a panelen, illetve a különböző részeit, az irányítópult megnyitásához. Egyszerűen kattintson a **rögzítés az irányítópulton** ikonra.<br>
   ![Képernyőfelvétel az Azure AD Connect Health RBAC PIN-kód panelen, a rögzítés ikonja kiemelve](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> Az olvasó szerepkörrel rendelkező felhasználó nincs az Azure AD Connect Health-bővítmény beolvasása az Azure piactérről. A felhasználó nem hajtható végre a "create" ehhez a művelethez szükséges. A felhasználó elérheti a panel nyissa meg az előző kapcsolat. Későbbi használatra a felhasználó rögzíthet az irányítópultra panel.
>
>

### <a name="remove-users-or-groups"></a>Felhasználók vagy csoportok eltávolítása
Eltávolíthatja a felhasználó vagy egy csoportot az Azure AD Connect Health RBAC hozzáadni. Egyszerűen kattintson a jobb gombbal a felhasználót vagy csoportot, és válassza **eltávolítása**.<br>
![Képernyőfelvétel az Azure AD Connect Health RBAC felhasználók-ablakot, távolítsa el a kijelölt](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – gyakori kérdések](active-directory-aadconnect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](active-directory-aadconnect-health-version-history.md)
