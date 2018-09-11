---
title: Csak az idő a virtuális gép eléréséhez az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan igény szerint Virtuálisgép-hozzáférés az Azure Security Center segít az Azure virtuális gépekhez való hozzáférés vezérlése.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2018
ms.author: rkarlin
ms.openlocfilehash: 2a079456813a67eb40d5cf42bcdd2c91fbc631d3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297039"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Igény szerint virtuálisgép-hozzáférés kezelése

Csak az idő a virtuális gép (VM) hozzáférés segítségével zárolását, így az Azure virtuális gépekre, csökkentve a támadásokkal szembeni sérülékenységet ugyanakkor könnyű hozzáférést biztosít arra, hogy szükség esetén a virtuális gépekhez csatlakozhat a bejövő forgalmat.

> [!NOTE]
> Az igény szerinti funkció érhető el a Standard szintű, a Security Center.  A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Támadás

Találgatásos támadások, gyakran cél felügyeleti portokhoz való hozzáférést egy virtuális géphez. Sikeres, ha egy támadó irányítását a virtuális gép felett, és egy foothold létesíteni a saját környezetben.

Találgatásos támadás való kitettség csökkentése érdekében egyik módja, hogy korlátozza, ameddig egy port nyitva. A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak addig kell nyitva lenniük, amíg Ön csatlakozik a virtuális géphez, például azért, hogy felügyeleti vagy karbantartási feladatokat végezzen. JIT engedélyezése esetén a Security Center használ [hálózati biztonsági csoport](../virtual-network/security-overview.md#security-rules) (NSG) szabályok, amelyek a felügyeleti portokhoz való hozzáférés korlátozása, így a támadók tudják célba.

![Csak az idő a forgatókönyv][1]

## <a name="how-does-just-in-time-access-work"></a>Hogyan csak az idő access működik?

Ha az igény szerinti hozzáférés engedélyezve van, a Security Center minden, az Azure-beli virtuális gépekre érkező forgalmat zárol egy NSG-szabály létrehozásával. Kiválaszthatja a portokat a virtuális gépen, amelyre a bejövő forgalom lesz zárolva. Ezeket a portokat szabályozza az igény szerinti megoldást.

Amikor egy felhasználó egy virtuális Géphez való hozzáférés, a Security Center ellenőrzi, hogy a felhasználó rendelkezik-e [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md) , amely írási hozzáférést nyújt a virtuális gép engedélyeit. Írási engedéllyel rendelkeznek, a kérés jóváhagyást, és a Security Center automatikusan konfigurálja a hálózati biztonsági csoportok (NSG-k) a kijelölt porton ennyi ideig forgalom engedélyezéséhez. a megadott. Az időszak lejárta után a Security Center visszaállítja az NSG-k korábbi állapotába. Ezeket a kapcsolatokat korábban már létrehozott nem folyamatban szakadnak meg, azonban.

> [!NOTE]
> Time VM access csak a Security Center jelenleg csak virtuális gépek Azure Resource Managerrel üzembe helyezett támogatja. Ismerje meg, a klasszikus és Resource Manager üzembe helyezési modellel kapcsolatos információkért tekintse meg a [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Csak a szerinti hozzáférés használata

1. Nyissa meg a **Security Center** irányítópultját.

2. A bal oldali panelen válassza ki a **igény szerinti Virtuálisgép-hozzáférési**.

![Virtuálisgép-hozzáférési szerinti csempe][2]

A **igény szerinti Virtuálisgép-hozzáférési** ablak nyílik meg.

![Virtuálisgép-hozzáférési szerinti csempe][10]

A **Just in time VM access** (Igény szerinti hozzáférés a virtuális gépekhez) információt nyújt a virtuális gépek állapotáról:

- **Configured** (Konfigurált) – Olyan virtuális gépek, amelyeket úgy vannak konfigurálva, hogy támogassák a virtuális gépek igény szerinti elérését. Az adatok a múlt hétre vonatkoznak, és az egyes virtuális Gépekhez tartalmazza a jóváhagyott kérések, a legutóbbi hozzáférés dátuma és ideje és utolsó felhasználó számát.
- **Recommended** (Ajánlott) – Olyan virtuális gépek, amelyek támogatni tudják a virtuális gépek igény szerinti elérését, de nem lettek erre konfigurálva. Javasoljuk, hogy engedélyezze a idő VM hozzáférés-vezérlés csak az ezeknek a virtuális gépeknek. Lásd: [konfigurálása igény szerinti hozzáférés szabályzat](#configuring-a-just-in-time-access-policy).
- **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:
  - Missing NSG (Hiányzó NSG) – Az igény szerinti megoldáshoz szükség van egy NSG-re.
  - Classic VM (Klasszikus virtuális gép) – A Security Centerben a virtuális gépek igény szerinti elérése jelenleg csak az Azure Resource Manageren keresztül üzembe helyezett virtuális gépek esetén támogatott. Klasszikus üzemi modellben nem támogatja az igény szerinti megoldást.
  - Other (Egyéb) – A virtuális gép akkor kerül ebbe a kategóriába, ha az előfizetés biztonsági szabályzatában vagy az erőforráscsoportban ki van kapcsolva az igény szerinti megoldás, vagy ha a virtuális gépnek hiányzik a nyilvános IP-címe, és nem rendelkezik NSG-vel.

## <a name="configuring-a-just-in-time-access-policy"></a>Konfigurálás igény szerinti hozzáférési szabályzat

Válassza ki az engedélyezni kívánt virtuális gépek:

1. A **igény szerinti Virtuálisgép-hozzáférési**, jelölje be a **ajánlott** fülre.

  ![Csak az idő hozzáférés engedélyezése][3]

2. A **virtuális gép**, válassza ki az engedélyezni kívánt virtuális gépeket. Ez a virtuális gép melletti jelölőnégyzet bejelölésekor helyezi.
3. Válassza ki **engedélyezése a virtuális gépek igény szerinti**.
4. Kattintson a **Mentés** gombra.

### <a name="default-ports"></a>Alapértelmezett portok

Láthatja, hogy az alapértelmezett portokat, amelyek JIT engedélyezése a Security Center javasolja.

1. A **igény szerinti Virtuálisgép-hozzáférési**, jelölje be a **ajánlott** fülre.

  ![Megjeleníti az alapértelmezett portok][6]

2. A **virtuális gépek**, válasszon ki egy virtuális Gépet. Ez mellett a virtuális gép be van jelölve, és megnyitja **virtuális gépek igény szerinti hozzáférés konfigurációs**. Ezt a panelt az alapértelmezett portok jeleníti meg.

### <a name="add-ports"></a>Adja hozzá a portokat

A **virtuális gépek igény szerinti hozzáférés konfigurációs**, is hozzáadhat és konfigurálhat egy új portot, amelyen szeretné, engedélyezheti az igény szerinti megoldást.

1. Alatt **virtuális gépek igény szerinti hozzáférés konfigurációs**válassza **Hozzáadás**. Ez megnyitja **Add port configuration**.

  ![Port konfigurálása][7]

2. A **Add port configuration**, azonosíthatja a port, protokoll típusa, engedélyezett forrásoldali IP-címek és kérelem maximális időtartama.

  Engedélyezett forrásoldali IP-címek számára hozzáférést kaphatnak, amelyek jóváhagyott kérelem engedélyezett IP-címtartományokkal.

  Kérelem maximális időtartama a maximális időtartam, hogy egy adott port nyitva lehet.

3. Kattintson az **OK** gombra.

## <a name="requesting-access-to-a-vm"></a>Egy virtuális Géphez való hozzáférés kérése

Egy virtuális Géphez való hozzáférés kérése:

1. A **igény szerinti Virtuálisgép-hozzáférési**, jelölje be a **konfigurált** fülre.
2. A **virtuális gépek**, válassza ki a virtuális gépeket, amely engedélyezi a hozzáférést. Ez a virtuális gép melletti jelölőnégyzet bejelölésekor helyezi.
3. Válassza ki **hozzáférés kérése**. Ez megnyitja **hozzáférés kérése**.

  ![Egy virtuális Géphez való hozzáférés kérése][4]

4. A **hozzáférés kérése**, konfigurálja az egyes virtuális Gépekhez a portok megnyitásához és a forrás IP-címe, amely a port meg van nyitva, és az idő ablak, amelynek a portot nyitjuk. Csak az éppen konfigurált portok hozzáférést kérhet szerinti szabályzat. Minden port rendelkezik a megengedett maximális ideje származik az igény szerinti szabályzat.
5. Válassza ki **portok megnyitásához**.

> [!NOTE]
> Amikor egy felhasználó egy virtuális Géphez való hozzáférés, a Security Center ellenőrzi, hogy a felhasználó rendelkezik-e [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md) , amely írási hozzáférést nyújt a virtuális gép engedélyeit. Ha írási engedéllyel rendelkeznek, a kérés jóváhagyást.
>
>

> [!NOTE]
> Ha egy felhasználó hozzáférést kér egy proxykiszolgáló mögött található, a "Saját IP-címe" beállítás nem működnek. Előfordulhat, hogy a szervezet teljes tartományának megadása szükséges.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Szerkesztési igény szerinti hozzáférési szabályzat

Virtuális gép csak az idő a házirend meglévő, hozzáadásával és konfigurálásával, nyissa meg a virtuális gép új port, illetve egy már védett port kapcsolatos más paraméter módosításával módosíthatja.

Annak érdekében, hogy csak egy virtuális gép szerinti szabályzat a meglévő szerkesztésekor a **konfigurált** lapon:

1. A **virtuális gépek**, válasszon egy virtuális Gépet, a port hozzáadása a virtuális Gépeket a soron belül három pontra kattintva. Megjelenik egy menü.
2. Válassza ki **szerkesztése** menüjében. Ez megnyitja **virtuális gépek igény szerinti hozzáférés konfigurációs**.

  ![A szabályzat szerkesztése][8]

3. A **virtuális gépek igény szerinti hozzáférés konfigurációs**, vagy szerkesztheti a meglévő beállítások már védett port portjának kattintva, vagy választhat **Hozzáadás**. Ez megnyitja **Add port configuration**.

  ![Port hozzáadása][7]

4. A **Add port configuration**, azonosíthatja a port, protokoll típusa, engedélyezett forrásoldali IP-címek, valamint a maximális kérelem időtartama.
5. Kattintson az **OK** gombra.
6. Kattintson a **Mentés** gombra.

## <a name="auditing-just-in-time-access-activity"></a>Csak az idő hozzáférési tevékenység naplózása

Virtuális gép a tevékenységeket a naplóbeli keresés segítségével betekintést kaphatnak. A naplók megtekintéséhez:

1. A **igény szerinti Virtuálisgép-hozzáférési**, jelölje be a **konfigurált** fülre.
2. A **virtuális gépek**, válasszon ki egy virtuális Gépet kapcsolatos információk megtekintéséhez kattintson a három pontra a soron belül a virtuális gép. Megjelenik egy menü.
3. Válassza ki **tevékenységnapló** menüjében. Ez megnyitja **tevékenységnapló**.

  ![Válassza ki a tevékenységnaplóban][9]

  **Tevékenységnapló** együtt idő, dátum és az előfizetés virtuális gép korábbi műveletek szűrt nézetét jeleníti meg.

  ![Tevékenységnapló megtekintése][5]

Letöltheti a naplózási adatok kiválasztásával **Ide kattintva letöltheti az összes elem CSV-fájlként**.

Módosítsa a szűrőket, és válasszon **alkalmaz** hozzon létre egy keresési és a napló.

## <a name="using-just-in-time-vm-access-via-rest-apis"></a>Virtuálisgép-hozzáférés REST API-kon keresztül szerinti használatával

Az igény szerinti Virtuálisgép-hozzáférési szolgáltatás használható az Azure Security Center API-n keresztül. Konfigurált virtuális gépek adatainak lekérése, újakat vehet fel, kérhet hozzáférést egy virtuális géphez, és több, az API-val. Lásd: [Jit hálózati hozzáférési házirendeket](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), további információ az igény szerinti REST API-t.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Konfigurálás igény szerinti szabályzat egy virtuális géphez

Konfigurálhat igény szerinti szabályzat egy adott virtuális gépen, futtassa ezt a parancsot a PowerShell-munkamenetben kell: Set-ASCJITAccessPolicy.
A parancsmagok dokumentációira további információért kövesse.

### <a name="requesting-access-to-a-vm"></a>Egy virtuális Géphez való hozzáférés kérése

Egy adott virtuális Gépre, amely a egyszerűen elemezhetnek eléréséhez ideje a megoldásban, futtassa ezt a parancsot a PowerShell-munkamenetben kell: meghívása ASCJITAccess.
A parancsmagok dokumentációira további információért kövesse.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan igény szerint a Security Center segít a Virtuálisgép-hozzáférés az Azure virtuális gépekhez való hozzáférés.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági szabályzatok beállítása](security-center-policies.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
- [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
- [Biztonsági állapotfigyelés](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
- [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
- [Partneri megoldások monitorozása](security-center-partner-solutions.md) – útmutató a partnermegoldások állapotának monitorozásához.
- [A Security Center – gyakori kérdések](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](https://blogs.msdn.microsoft.com/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
