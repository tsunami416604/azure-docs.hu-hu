---
title: Gyakori riasztások és megoldások az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogy miként oldhatja meg az Azure Active Directory tartományi szolgáltatások állapotának részeként létrehozott gyakori riasztásokat
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: ed791ea10c072308c16baac9fa469a46b19ec648
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654500"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Ismert problémák: Gyakori riasztások és -megoldások az Azure Active Directory tartományi szolgáltatásokban

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) az alkalmazások identitásának és hitelesítésének központi részeként néha problémákat okoznak. Ha problémákba ütközik, néhány gyakori riasztás és a kapcsolódó hibaelhárítási lépések segítenek a dolgok újrafuttatásában. Bármikor [megnyithat egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségre.

Ez a cikk hibaelhárítási információkat tartalmaz az Azure AD DS gyakori riasztások.

## <a name="aadds100-missing-directory"></a>AADDS100: Hiányzó könyvtár

### <a name="alert-message"></a>Figyelmeztető üzenet

*Lehet, hogy a felügyelt tartományhoz társított Azure AD-könyvtárat törölték. A felügyelt tartomány már nem támogatott konfigurációban van. A Microsoft nem tudja figyelni, kezelni, javítani és szinkronizálni a felügyelt tartományt.*

### <a name="resolution"></a>Megoldás:

Ez a hiba általában akkor jelentkezik, ha egy Azure-előfizetés takar egy új Azure AD-címtárba, és az Azure AD DS-hez társított régi Azure AD-könyvtár törlődik.

Ez a hiba helyreállíthatatlan. A riasztás feloldásához [törölje a meglévő Azure AD DS felügyelt tartományt,](delete-aadds.md) és hozza létre újra az új címtárban. Ha problémái vannak az Azure AD DS felügyelt tartományának törlésével, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Az Azure AD B2C fut ebben a könyvtárban

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások nem engedélyezhetők az Azure AD B2C címtárban.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS automatikusan szinkronizálja az Azure AD-címtár. Ha az Azure AD-címtár b2C-hez van konfigurálva, az Azure AD DS nem telepíthető és szinkronizálható.

Az Azure AD DS használatához újra létre kell hoznia a felügyelt tartományt egy nem Azure AD B2C címtárban a következő lépésekkel:

1. [Törölje az Azure AD DS felügyelt tartományt](delete-aadds.md) a meglévő Azure AD-címtárból.
1. Hozzon létre egy új Azure AD-címtárat, amely nem egy Azure AD B2C könyvtár.
1. [Hozzon létre egy helyettesítő Azure AD DS felügyelt tartományt.](tutorial-create-instance.md)

Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: A cím nyilvános IP-tartományban van

### <a name="alert-message"></a>Figyelmeztető üzenet

*Annak a virtuális hálózatnak az IP-címtartománya, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat, nyilvános IP-tartományban van. Az Azure AD tartományi szolgáltatások engedélyezni kell a virtuális hálózat egy privát IP-címtartományban. Ez a konfiguráció hatással van a Microsoft felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.*

### <a name="resolution"></a>Megoldás:

Mielőtt elkezdené, győződjön meg róla, hogy megértette [a privát IP v4 címtereket.](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)

A virtuális hálózaton belül a virtuális gépek az alhálózathoz konfigurált IP-címtartományban is kaphatnak kéréseket az Azure-erőforrásokhoz. Ha egy alhálózat nyilvános IP-címtartományát konfigurálja, előfordulhat, hogy a virtuális hálózaton keresztül irányított kérelmek nem érik el a kívánt webes erőforrásokat. Ez a konfiguráció az Azure AD DS előre nem látható hibákhoz vezethet.

> [!NOTE]
> Ha a virtuális hálózatban konfigurált internet IP-címtartománya a tulajdonában van, ez a riasztás figyelmen kívül hagyható. Az Azure AD tartományi szolgáltatások azonban nem véglegesítheti az [SLA]](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)ezzel a konfigurációval, mivel ez előre nem látható hibákhoz vezethet.

A riasztás feloldásához törölje a meglévő Azure AD DS felügyelt tartományt, és hozza létre újra egy privát IP-címtartománnyal rendelkező virtuális hálózatban. Ez a folyamat zavaró, mivel az Azure AD DS felügyelt tartomány nem érhető el, és minden egyéni erőforrásokat létrehozott, például a saját források vagy a szolgáltatásfiókok elvesznek.

1. [Törölje az Azure AD DS felügyelt tartományt](delete-aadds.md) a címtárból.
1. A virtuális hálózati IP-címtartomány frissítéséhez keresse meg és válassza a *virtuális hálózat* lehetőséget az Azure Portalon. Válassza ki az Azure AD DS virtuális hálózatát, amely helytelenül rendelkezik nyilvános IP-címtartomány-készlet-készletével.
1. A **Beállítások csoportban**válassza a *Címterület lehetőséget.*
1. Frissítse a címtartományt a meglévő címtartomány kiválasztásával és szerkesztésével, vagy adjon hozzá egy további címtartományt. Győződjön meg arról, hogy az új IP-címtartomány privát IP-tartományban van. Ha készen áll, **mentse** a módosításokat.
1. A bal oldali navigációs sávon válassza az **Alhálózatok** lehetőséget.
1. Válassza ki a szerkesztni kívánt alhálózatot, vagy hozzon létre egy további alhálózatot.
1. Frissítse vagy adja meg a privát IP-címtartományt, majd **mentse** a módosításokat.
1. [Hozzon létre egy helyettesítő Azure AD DS felügyelt tartományt.](tutorial-create-instance.md) Győződjön meg arról, hogy a frissített virtuális hálózati alhálózatot választotta magánIP-címtartománysal.

Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Az Azure-előfizetés nem található

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartományhoz társított Azure-előfizetéstörölve lett.  Az Azure AD tartományi szolgáltatások hoz egy aktív előfizetés tanusítatot a megfelelő működés folytatásához.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS aktív előfizetést igényel, és nem helyezhető át egy másik előfizetésre. Ha az Azure AD DS felügyelt tartománya az Azure-ds felügyelt tartományhoz társított Azure-előfizetés törlődik, újra létre kell hoznia egy Azure-előfizetést és az Azure AD DS felügyelt tartományát.

1. [Hozzon létre egy Azure-előfizetést.](../cost-management-billing/manage/create-subscription.md)
1. [Törölje az Azure AD DS felügyelt tartományt](delete-aadds.md) a meglévő Azure AD-címtárból.
1. [Hozzon létre egy helyettesítő Azure AD DS felügyelt tartományt.](tutorial-create-instance.md)

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Az Azure-előfizetés le van tiltva

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartományhoz társított Azure-előfizetés nem aktív.  Az Azure AD tartományi szolgáltatások hoz egy aktív előfizetés tanusítatot a megfelelő működés folytatásához.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS aktív előfizetést igényel. Ha az Azure AD DS felügyelt tartománya társított Azure-előfizetés nem aktív, meg kell újítania az előfizetés újraaktiválásához.

1. [Az Azure-előfizetés megújítása.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)
2. Az előfizetés megújítása után az Azure AD DS-értesítés lehetővé teszi a felügyelt tartomány újbóli engedélyezését.

Ha a felügyelt tartomány ismét engedélyezve van, az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Az előfizetés áthelyezve könyvtárak

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások által használt előfizetés átkerült egy másik címtárba. Az Azure AD tartományi szolgáltatások nak rendelkeznie kell egy aktív előfizetés ugyanabban a címtárban a megfelelő működéshez.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS aktív előfizetést igényel, és nem helyezhető át egy másik előfizetésre. Ha az Azure AD DS felügyelt tartományához társított Azure-előfizetés átkerül, helyezze vissza az előfizetést az előző könyvtárba, vagy [törölje a felügyelt tartományt](delete-aadds.md) a meglévő címtárból, és [hozzon létre egy helyettesítő Azure AD DS felügyelt tartományt a kiválasztott előfizetésben.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: A felügyelt tartomány erőforrásai nem találhatók

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartományhoz használt erőforrástörölve lett. Ez az erőforrás szükséges az Azure AD tartományi szolgáltatások megfelelő működéséhez.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS további erőforrásokat hoz létre a megfelelő működéshez, például a nyilvános IP-címeket, a virtuális hálózati adaptereket és a terheléselosztót. Ha ezen erőforrások bármelyikét törli, a felügyelt tartomány nem támogatott állapotban van, és megakadályozza a tartomány kezelését. Ezekről az erőforrásokról az [Azure AD DS által használt hálózati erőforrások](network-considerations.md#network-resources-used-by-azure-ad-ds)című témakörben talál további információt.

Ez a riasztás akkor jön létre, ha a szükséges erőforrások egyikét törli. Ha az erőforrást kevesebb, mint 4 órával ezelőtt törölték, van esély arra, hogy az Azure platform automatikusan újra létrehozza a törölt erőforrást. A következő lépések bemutatják, hogyan ellenőrizheti az állapotot és az időbélyeget az erőforrások törléséhez:

1. Az Azure Portalon keresse meg és válassza a **Domain Services lehetőséget.** Válassza ki az Azure AD DS felügyelt tartományát, például *a aaddscontoso.com.*
1. A bal oldali navigációs ban válassza az **Egészség**lehetőséget.
1. Az állapotlapon válassza ki az *AADDS109*azonosítóval ellátott riasztást.
1. A riasztás rendelkezik egy időbélyeg, amikor először található. Ha ez az időbélyeg kevesebb, mint 4 órával ezelőtt, az Azure platform képes lehet automatikusan újra az erőforrást, és a riasztás feloldása önmagában.

    Ha a riasztás több mint 4 órás, az Azure AD DS felügyelt tartomány a helyreállíthatatlan állapotban van. [Törölje az Azure AD DS felügyelt tartományát,](delete-aadds.md) majd [hozzon létre egy helyettesítő felügyelt tartományt.](tutorial-create-instance.md)

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A felügyelt tartományhoz társított alhálózat megtelt

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások üzembe helyezéséhez kiválasztott alhálózat megtelt, és nincs hely a további tartományvezérlő, amelyet létre kell hozni.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS virtuális hálózati alhálózatának elegendő IP-címre van szüksége az automatikusan létrehozott erőforrásokhoz. Ez az IP-címterület magában foglalja a csereerőforrások létrehozásának szükségességét, ha karbantartási esemény van. A rendelkezésre álló IP-címek kifutásának kockázatának minimalizálása érdekében ne telepítsen további erőforrásokat, például a saját virtuális gépeit ugyanabba a virtuális hálózati alhálózatba, mint az Azure AD DS.

Ez a hiba helyreállíthatatlan. A riasztás feloldásához [törölje a meglévő Azure AD DS felügyelt tartományt,](delete-aadds.md) és hozza létre újra. Ha problémái vannak az Azure AD DS felügyelt tartományának törlésével, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Egyszerű szolgáltatás nem engedélyezett

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások által a tartomány kiszolgálására használt szolgáltatás nem jogosult az Azure-előfizetés erőforrásainak kezelésére. Az egyszerű szolgáltatásnak engedélyeket kell szereznie a felügyelt tartomány kiszolgálásához.*

### <a name="resolution"></a>Megoldás:

Néhány automatikusan létrehozott egyszerű szolgáltatás használatával kezeli, és erőforrások at egy Azure AD DS felügyelt tartományban erőforrások at. Ha ezen egyszerű szolgáltatásbiztonsági tagok valamelyikének hozzáférési engedélyei módosulnak, a tartomány nem tudja megfelelően kezelni az erőforrásokat. A következő lépések bemutatják, hogyan értheti meg, majd adhat hozzáférési engedélyeket egy egyszerű szolgáltatásnak:

1. A [szerepköralapú hozzáférés-vezérlésről és az alkalmazásokhoz való hozzáférés engedélyezéséről az Azure Portalon](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)olvashat.
2. Tekintse át az *abba844e-bc0e-44b0-947a-dc74e5d09022* azonosítóval rendelkező szolgáltatásnév hozzáférését, és adja meg a korábban megtagadott hozzáférést.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Nincs elég IP-cím a felügyelt tartományban

### <a name="alert-message"></a>Figyelmeztető üzenet

*Azonosítottuk, hogy a tartományban lévő virtuális hálózat alhálózata nem rendelkezik elegendő IP-címmel. Az Azure AD tartományi szolgáltatásoknak legalább két elérhető IP-címre van szüksége azon az alhálózaton belül, amelyben engedélyezve van. Azt javasoljuk, hogy legalább 3-5 tartalék IP-cím legyen az alhálózaton belül. Ez akkor fordulhatelő elő, ha más virtuális gépek vannak telepítve az alhálózaton belül, így kimerítve a rendelkezésre álló IP-címek számát, vagy ha az alhálózatban elérhető IP-címek száma korlátozva van.*

### <a name="resolution"></a>Megoldás:

Az Azure AD DS virtuális hálózati alhálózatának elegendő IP-címre van szüksége az automatikusan létrehozott erőforrásokhoz. Ez az IP-címterület magában foglalja a csereerőforrások létrehozásának szükségességét, ha karbantartási esemény van. A rendelkezésre álló IP-címek kifutásának kockázatának minimalizálása érdekében ne telepítsen további erőforrásokat, például a saját virtuális gépeit ugyanabba a virtuális hálózati alhálózatba, mint az Azure AD DS.

A riasztás megoldásához törölje a meglévő Azure AD DS felügyelt tartományt, és hozza létre újra egy elég nagy IP-címtartománnyal rendelkező virtuális hálózatban. Ez a folyamat zavaró, mivel az Azure AD DS felügyelt tartomány nem érhető el, és minden egyéni erőforrásokat létrehozott, például a saját források vagy a szolgáltatásfiókok elvesznek.

1. [Törölje az Azure AD DS felügyelt tartományt](delete-aadds.md) a címtárból.
1. A virtuális hálózati IP-címtartomány frissítéséhez keresse meg és válassza a *virtuális hálózat* lehetőséget az Azure Portalon. Válassza ki a virtuális hálózat az Azure AD DS, amely rendelkezik a kis IP-címtartomány.
1. A **Beállítások csoportban**válassza a *Címterület lehetőséget.*
1. Frissítse a címtartományt a meglévő címtartomány kiválasztásával és szerkesztésével, vagy adjon hozzá egy további címtartományt. Győződjön meg arról, hogy az új IP-címtartomány elég nagy az Azure AD DS alhálózati tartományhoz. Ha készen áll, **mentse** a módosításokat.
1. A bal oldali navigációs sávon válassza az **Alhálózatok** lehetőséget.
1. Válassza ki a szerkesztni kívánt alhálózatot, vagy hozzon létre egy további alhálózatot.
1. Frissítse vagy adja meg az elég nagy IP-címtartományt, majd **mentse** a módosításokat.
1. [Hozzon létre egy helyettesítő Azure AD DS felügyelt tartományt.](tutorial-create-instance.md) Győződjön meg arról, hogy a frissített virtuális hálózati alhálózatot választja ki elég nagy IP-címtartománysal.

Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Az erőforrások helyreállíthatatlanok

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások által használt erőforrások váratlan állapotban észlelhetők, és nem állíthatók helyre.*

### <a name="resolution"></a>Megoldás:

Ez a hiba helyreállíthatatlan. A riasztás feloldásához [törölje a meglévő Azure AD DS felügyelt tartományt,](delete-aadds.md) és hozza létre újra. Ha problémái vannak az Azure AD DS felügyelt tartományának törlésével, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Érvénytelen alhálózat

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások üzembe helyezéséhez kiválasztott alhálózat érvénytelen, és nem használható.*

### <a name="resolution"></a>Megoldás:

Ez a hiba helyreállíthatatlan. A riasztás feloldásához [törölje a meglévő Azure AD DS felügyelt tartományt,](delete-aadds.md) és hozza létre újra. Ha problémái vannak az Azure AD DS felügyelt tartományának törlésével, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Az erőforrások zárolva vannak

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartomány által használt egy vagy több hálózati erőforrás nem üzemeltethető, mert a célhatókör zárolva van.*

### <a name="resolution"></a>Megoldás:

Erőforrás-zárolások lehet alkalmazni az Azure-erőforrások a módosítás vagy törlés megelőzése érdekében. Mivel az Azure AD DS felügyelt szolgáltatás, az Azure platformnak képesnek kell lennie a konfiguráció módosítására. Ha egy erőforrás-zárolás t alkalmaznéhány Azure AD DS-összetevőn, az Azure platform nem tudja végrehajtani a felügyeleti feladatokat.

Ha ellenőrizni szeretné az Erőforrások zárolását az Azure AD DS-összetevőkön, és el szeretné távolítani őket, hajtsa végre az alábbi lépéseket:

1. Az erőforráscsoport ban található minden egyes Azure AD DS-hálózati összetevő, például a virtuális hálózat, a hálózati adapter vagy a nyilvános IP-cím esetében tekintse meg a műveleti naplókat az Azure Portalon. Ezeknek a műveletnaplóknak jelezniük kell, hogy miért hibásodik meg egy művelet, és hol van alkalmazva erőforrás-zárolás.
1. Jelölje ki azt az erőforrást, amelyre a zárolást alkalmazza, majd a **Zárolások**csoportban jelölje ki és távolítsa el a zárolás(oka)t.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Az erőforrások használhatatlanok

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartomány által használt egy vagy több hálózati erőforrás házirend-korlátozás(ok) miatt nem működtethető.*

### <a name="resolution"></a>Megoldás:

Szabályzatok az Azure-erőforrások és erőforráscsoportok, amelyek szabályozzák, hogy milyen konfigurációs műveletek engedélyezettek. Mivel az Azure AD DS felügyelt szolgáltatás, az Azure platformnak képesnek kell lennie a konfiguráció módosítására. Ha egy szabályzat az Azure AD DS egyes összetevőin van alkalmazva, előfordulhat, hogy az Azure platform nem tudja végrehajtani a felügyeleti feladatokat.

Az Azure AD DS-összetevők alkalmazott szabályzatainak ellenőrzéséhez és frissítéséhez hajtsa végre az alábbi lépéseket:

1. Az erőforráscsoport ban lévő azure-beli AD DS-hálózati összetevők mindegyikéhez, például a virtuális hálózathoz, a hálózati adapterhez vagy a nyilvános IP-címhez tekintse meg a műveleti naplókat az Azure Portalon. Ezeknek a műveletnaplóknak jelezniük kell, hogy miért hibás a művelet, és hol alkalmaznak korlátozó házirendet.
1. Jelölje ki azt az erőforrást, amelyre a házirend et alkalmazza, majd a **Házirendek**csoportban válassza ki és szerkesztse a házirendet, hogy kevésbé korlátozó legyen.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A szinkronizálás egy ideje nem fejeződött be

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartomány utoljára [dátum] volt szinkronizálva az Azure AD-vel. Előfordulhat, hogy a felhasználók nem tudnak bejelentkezni a felügyelt tartományba, vagy előfordulhat, hogy a csoporttagságok nincsenek szinkronban az Azure AD-vel.*

### <a name="resolution"></a>Megoldás:

[Ellenőrizze az Azure AD DS állapotát](check-health.md) a felügyelt tartomány konfigurációjában problémákat jelző riasztásokat. A hálózati konfigurációval kapcsolatos problémák blokkolhatják az Azure AD szinkronizálását. Ha meg tudja oldani a konfigurációs problémát jelző riasztásokat, várjon két órát, és ellenőrizze, hogy a szinkronizálás sikeresen befejeződött-e.

A következő gyakori okok miatt a szinkronizálás leáll egy Azure AD DS felügyelt tartományban:

* A szükséges hálózati kapcsolat le van tiltva. Ha többet szeretne tudni arról, hogyan ellenőrizheti az Azure virtuális hálózatproblémáit, és mi szükséges, olvassa el a hálózati biztonsági csoportok és az [Azure AD tartományi szolgáltatások hálózati követelményeinek](network-considerations.md) [hibaelhárítása](alert-nsg.md) című témakört.
*  A jelszó-szinkronizálás nem lett beállítva vagy sikeresen befejeződött az Azure AD DS felügyelt tartományának telepítésekor. Beállíthatja a jelszó-szinkronizálást a [csak felhőalapú felhasználók](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) vagy [hibrid felhasználók on-prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Egy ideje nem készült biztonsági mentés

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartományról legutóbb a [date] készült biztonsági másolato.*

### <a name="resolution"></a>Megoldás:

[Ellenőrizze az Azure AD DS állapotát](check-health.md) a felügyelt tartomány konfigurációjában jelentkező problémákat jelző riasztásokat. A hálózati konfigurációval kapcsolatos problémák letilthatják, hogy az Azure platform sikeresen készítsen biztonsági mentéseket. Ha meg tudja oldani a konfigurációs problémát jelző riasztásokat, várjon két órát, és ellenőrizze, hogy a szinkronizálás sikeresen befejeződött-e.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Felfüggesztés a letiltott előfizetés miatt

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartomány felfüggesztésre kerül, mert a tartományhoz társított Azure-előfizetés nem aktív.*

### <a name="resolution"></a>Megoldás:

> [!WARNING]
> Ha egy Azure AD DS felügyelt tartomány hosszabb ideig van felfüggesztve, fennáll annak a veszélye, hogy törli. A felfüggesztés okát a lehető leggyorsabban oldja meg. További [információ: Az Azure AD DS felfüggesztett állapotának ismertetése.](suspension.md)

Az Azure AD DS aktív előfizetést igényel. Ha az Azure AD DS felügyelt tartománya társított Azure-előfizetés nem aktív, meg kell újítania az előfizetés újraaktiválásához.

1. [Az Azure-előfizetés megújítása.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)
2. Az előfizetés megújítása után az Azure AD DS-értesítés lehetővé teszi a felügyelt tartomány újbóli engedélyezését.

Ha a felügyelt tartomány ismét engedélyezve van, az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Felfüggesztés érvénytelen konfiguráció miatt

### <a name="alert-message"></a>Figyelmeztető üzenet

*A felügyelt tartomány érvénytelen konfiguráció miatt felfüggesztésre kerül. A szolgáltatás már régóta nem tudja kezelni, kijavítani vagy frissíteni a felügyelt tartomány tartományvezérlőit.*

### <a name="resolution"></a>Megoldás:

> [!WARNING]
> Ha egy Azure AD DS felügyelt tartomány hosszabb ideig van felfüggesztve, fennáll annak a veszélye, hogy törli. A felfüggesztés okát a lehető leggyorsabban oldja meg. További [információ: Az Azure AD DS felfüggesztett állapotának ismertetése.](suspension.md)

[Ellenőrizze az Azure AD DS állapotát](check-health.md) a felügyelt tartomány konfigurációjában jelentkező problémákat jelző riasztásokat. Ha meg tudja oldani a konfigurációs problémát jelző riasztásokat, várjon két órát, és ellenőrizze, hogy befejeződött-e a szinkronizálás. Ha készen áll, [nyisson meg egy Azure-támogatási kérelmet][azure-support] az Azure AD DS felügyelt tartományának újbóli engedélyezéséhez.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémák merülnek fel, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
