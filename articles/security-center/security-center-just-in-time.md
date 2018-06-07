---
title: Csak idő virtuális gép férnek hozzá az Azure Security Centerben |} Microsoft Docs
description: Ez a dokumentum azt mutatja be, hogyan igény szerint VM hozzáférés az Azure Security Center segítséget nyújt az Azure virtuális gépeken való hozzáférés szabályozása.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2018
ms.author: terrylan
ms.openlocfilehash: 60a5de16f4146e112a85d74634c662e228a0854f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640557"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>JIT-virtuális gép hozzáférés kezelése

Csak az idő a virtuális gép (VM) hozzáférés segítségével az Azure virtuális gépeken, támadásoknak való kitettség csökkentése során könnyen hozzá lehet férni a virtuális gépekhez, szükség esetén csatlakoztassa a bejövő forgalom zárolását.

> [!NOTE]
> A csak időbeli funkció érhető el a Security Center Standard csomagra.  A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Támadás

Találgatásos támadások gyakran felügyeleti célportjainak jelentkezhessenek be a virtuális gépek eléréséhez. Ha sikeres, a támadó ellenőrzése alatt tartja a virtuális gép igénybe, és egy foothold létesíteni a környezetbe.

Egy találgatásos támadással való kitettség csökkentése érdekében módja az, hogy mennyi ideig legyen egy nyitott port korlátozza. A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak addig kell nyitva lenniük, amíg Ön csatlakozik a virtuális géphez, például azért, hogy felügyeleti vagy karbantartási feladatokat végezzen. Amikor a JIT engedélyezve van, a Security Center az [hálózati biztonsági csoport](../virtual-network/security-overview.md#security-rules) (NSG) szabályok, amelyek felügyelet portjai való hozzáférés korlátozása, így nem tudja megcélozni a támadók.

![Csak az idő forgatókönyv][1]

## <a name="how-does-just-in-time-access-work"></a>Hogyan csak az idő access működik?

Ha az igény szerinti hozzáférés engedélyezve van, a Security Center minden, az Azure-beli virtuális gépekre érkező forgalmat zárol egy NSG-szabály létrehozásával. Kiválaszthatja a a virtuális Gépre, amelyre a bejövő forgalom lesz zárolva. Ezeket a portokat szabályozzák az imént idő megoldásban.

Amikor egy felhasználó egy virtuális Géphez való hozzáférést igényel, a Security Center ellenőrzi, hogy a felhasználó rendelkezik-e [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) írási hozzáférést biztosítson a virtuális gép az engedélyeket. Ha írási engedélyek lettek, a kérelem jóváhagyása és a Security Center automatikusan konfigurálja a hálózati biztonsági csoportok (NSG-k) a kijelölt porton ennyi ideig bejövő adatforgalom engedélyezésére adott meg. Az időszak lejárta után a Security Center az NSG-ket visszaállítja korábbi állapotába. Ezeket a kapcsolatokat, korábban már létrehozott nem alatt szakadnak meg, azonban.

> [!NOTE]
> Biztonsági központ csak a virtuális gép elérhető jelenleg csak virtuális gépek Azure Resource Manager használatával telepített. Ismerje meg, a klasszikus és Resource Manager üzembe helyezési modellel kapcsolatos információkért tekintse meg a [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Csak az idő access használatával

1. Nyissa meg a **Security Center** irányítópultját.

2. A bal oldali panelen válassza ki a **közvetlenül a virtuális gép elérhető**.

![Virtuális gép hozzáférés JIT csempéje][2]

A **közvetlenül a virtuális gép elérhető** ablak nyílik meg.

![Virtuális gép hozzáférés JIT csempéje][10]

A **Just in time VM access** (Igény szerinti hozzáférés a virtuális gépekhez) információt nyújt a virtuális gépek állapotáról:

- **Configured** (Konfigurált) – Olyan virtuális gépek, amelyeket úgy vannak konfigurálva, hogy támogassák a virtuális gépek igény szerinti elérését. Közölt adatok az elmúlt héten, és az egyes virtuális gépek magában foglalja a jóváhagyott kéréseket, legutóbbi hozzáférés dátuma és időpontja és utolsó felhasználó számát.
- **Recommended** (Ajánlott) – Olyan virtuális gépek, amelyek támogatni tudják a virtuális gépek igény szerinti elérését, de nem lettek erre konfigurálva. Javasoljuk, hogy engedélyezze a csak az idő VM hozzáférés-vezérlés a virtuális gépeken. Lásd: [konfigurálása csak hozzáférési házirendben idő](#configuring-a-just-in-time-access-policy).
- **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:
  - Missing NSG (Hiányzó NSG) – Az igény szerinti megoldáshoz szükség van egy NSG-re.
  - Classic VM (Klasszikus virtuális gép) – A Security Centerben a virtuális gépek igény szerinti elérése jelenleg csak az Azure Resource Manageren keresztül üzembe helyezett virtuális gépek esetén támogatott. A klasszikus üzembe helyezési nem támogatja a csak az idő megoldás.
  - Other (Egyéb) – A virtuális gép akkor kerül ebbe a kategóriába, ha az előfizetés biztonsági szabályzatában vagy az erőforráscsoportban ki van kapcsolva az igény szerinti megoldás, vagy ha a virtuális gépnek hiányzik a nyilvános IP-címe, és nem rendelkezik NSG-vel.

## <a name="configuring-a-just-in-time-access-policy"></a>Beállítása csak hozzáférési házirendben idő

Az engedélyezni kívánt virtuális gépek kiválasztása:

1. A **közvetlenül a virtuális gép elérhető**, jelölje be a **ajánlott** fülre.

  ![Csak az idő hozzáférés engedélyezése][3]

2. A **virtuális gép**, válassza ki az engedélyezni kívánt virtuális gépeket. Ez helyezi a virtuális gépek melletti négyzetet.
3. Válassza ki **engedélyezése virtuális gépeken JIT**.
4. Kattintson a **Mentés** gombra.

### <a name="default-ports"></a>Alapértelmezett port

Az alapértelmezett portok, amely a Security Center JIT engedélyezését javasolja tekintheti meg.

1. A **közvetlenül a virtuális gép elérhető**, jelölje be a **ajánlott** fülre.

  ![Megjeleníti az alapértelmezett portok][6]

2. A **virtuális gépek**, jelöljön ki egy virtuális Gépet. Ez a virtuális gép mellett be van jelölve, és megnyitja **JIT VM konfiguráció**. Ezt a panelt jeleníti meg az alapértelmezett portok.

### <a name="add-ports"></a>Portok hozzáadása

A **JIT VM konfiguráció**, is hozzáadhat és engedélyezése a csak akkor szeretne új port konfigurálása idő megoldásban.

1. A **JIT VM konfiguráció**, jelölje be **Hozzáadás**. Ekkor megnyílik **Hozzáadás portkonfigurációjának**.

  ![Port konfigurálása][7]

2. A **Hozzáadás portkonfigurációjának**, a port, a protokoll típusát, a forrás IP-címek és a kérelem maximális idő azonosításához.

  Forrás IP-címek az IP-címtartományok is elérheti az engedélyezett kérésre engedélyezett.

  Kérések maximális ideje a maximális időszak, hogy egy adott portot lehet megnyitni.

3. Kattintson az **OK** gombra.

## <a name="requesting-access-to-a-vm"></a>A virtuális gépek hozzáférést kérő

Kérjen hozzáférést egy virtuális géphez:

1. A **közvetlenül a virtuális gép elérhető**, jelölje be a **beállított** fülre.
2. A **virtuális gépek**, válassza ki a virtuális gépeket, amely engedélyezi a hozzáférést. Ez helyezi a virtuális gépek melletti négyzetet.
3. Válassza ki **hozzáférés kérése**. Ekkor megnyílik **hozzáférés kérése**.

  ![Kérjen hozzáférést egy virtuális géphez][4]

4. A **hozzáférés kérése**, konfigurálnia az egyes virtuális gépek a portok megnyitásához és a forrás IP-címe, amely a port meg van nyitva, és a időszak, amelynek az port meg van nyitva. Kérhet hozzáférést csak a portok az imént beállított idő házirendben. Minden porthoz tartozik egy engedélyezett maximális időtartamot a csak származó idő házirendben.
5. Válassza ki **portok megnyitása**.

> [!NOTE]
> Amikor egy felhasználó egy virtuális Géphez való hozzáférést igényel, a Security Center ellenőrzi, hogy a felhasználó rendelkezik-e [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) írási hozzáférést biztosítson a virtuális gép az engedélyeket. Ha írási engedélyekkel rendelkeznek, elfogadja a kérelmet.
>
>

> [!NOTE]
> Ha egy felhasználó hozzáférést kér a rendszer proxy mögött, a "Saját IP-címe" beállítás nem működnek. Előfordulhat, hogy a szervezet teljes tartományának megadása szükséges.
>
>

## <a name="editing-a-just-in-time-access-policy"></a>Szerkesztés csak hozzáférési házirendben idő

Módosíthatja a virtuális gép csak az idő a házirend meglévő, hozzáadásával és konfigurálásával, nyissa meg ezt a virtuális gépet az új port, illetve egy már védett port kapcsolódó más paraméter módosításával.

Ahhoz, hogy csak az idő házirendet a virtuális gépek meglévő szerkesztésekor a **beállított** lapon:

1. A **virtuális gépek**, jelöljön ki egy virtuális Gépet, a port hozzáadása a soron belüli három pontra kattintva ezt a virtuális gépet. Ekkor megnyílik egy menüben.
2. Válassza ki **szerkesztése** a menüben. Ekkor megnyílik **JIT VM konfiguráció**.

  ![A szabályzat szerkesztése][8]

3. A **JIT VM konfiguráció**, vagy szerkesztheti a meglévő beállítások már védett port a porton kattint, vagy választhat **Hozzáadás**. Ekkor megnyílik **Hozzáadás portkonfigurációjának**.

  ![Adjon hozzá egy portot][7]

4. A **Hozzáadás portkonfigurációjának**, azonosítsa a port, protokolltípus kérelem ideje forrás IP-címek és a maximális engedélyezett.
5. Kattintson az **OK** gombra.
6. Kattintson a **Mentés** gombra.

## <a name="auditing-just-in-time-access-activity"></a>Csak az idő-hozzáférési tevékenységet naplózás

Akkor is kaphat a naplófájl-keresési VM tevékenységeket. Naplók megtekintése:

1. A **közvetlenül a virtuális gép elérhető**, jelölje be a **beállított** fülre.
2. A **virtuális gépek**, válassza ki a virtuális gépek ezt a virtuális gépet a soron belüli három pontra kattintva adatainak megjelenítéséhez. Ekkor megnyílik egy menüben.
3. Válassza ki **tevékenységnapló** a menüben. Ekkor megnyílik **tevékenységnapló**.

  ![Válassza ki a tevékenység naplója][9]

  **Tevékenységnapló** szűrt ezt a virtuális gépet és idő, dátum és előfizetés korábbi műveletek jeleníti meg.

  ![Tevékenység napló megtekintése][5]

A naplózási adatok kiválasztásával letöltheti **Ide kattintva letöltheti elemeinek CSV-ként**.

Módosítsa a szűrőt, és válasszon **alkalmaz** a Keresés és a napló létrehozása.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Igény szerint VM hozzáférés PowerShell használatával

Ahhoz, hogy az csak a PowerShell idő megoldás győződjön meg arról, hogy a [legújabb](/powershell/azure/install-azurerm-ps) Azure PowerShell-verzió.
Ha így tesz, telepítenie kell a [legújabb](https://aka.ms/asc-psgallery) az Azure Security Center a PowerShell-galériából.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Konfigurálása csak a virtuális gépek ideje házirend

Csak konfigurálása egy adott VM-idő szabályzatok futtassa ezt a parancsot a PowerShell-munkamenetben kell: Set-ASCJITAccessPolicy.
A parancsmagok dokumentációira további kövesse.

### <a name="requesting-access-to-a-vm"></a>A virtuális gépek hozzáférést kérő

Egy adott virtuális Gépet, a csak a védett eléréséhez idő megoldásban ez a parancs futtatásához a PowerShell-munkamenetben szüksége: meghívása ASCJITAccess.
A parancsmagok dokumentációira további kövesse.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan igény szerint a Security Center segítséget nyújt a virtuális gép hozzáférés az Azure virtuális gépeken való hozzáférést.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági szabályzatok beállítása](security-center-policies.md) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
- [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
- [Biztonsági állapotfigyelés](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
- [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
- [Partnermegoldások figyelése](security-center-partner-solutions.md) – megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
- [Security Center: GYIK](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
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
