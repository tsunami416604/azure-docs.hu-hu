---
title: Számlázási modell Azure Active Directory B2C
description: Ismerje meg az Azure AD B2C's havi aktív felhasználók (MAU) számlázási modelljét, valamint egy adott Azure-előfizetés számlázásának engedélyezését.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190007"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Számlázási modell Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) használat számlázása egy társított Azure-előfizetéshez történik, és havi aktív felhasználói (MAU) számlázási modellt használ. Megtudhatja, hogyan kapcsolhat Azure AD B2C-erőforrást egy előfizetéshez, és hogyan működik a MAU számlázási modell a következő fejezetekben.

> [!IMPORTANT]
> Ez a cikk nem tartalmaz díjszabási információt. A használati számlázással és a díjszabással kapcsolatos legfrissebb információkért tekintse meg a [Azure Active Directory B2C díjszabását](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Havi aktív felhasználók (MAU) számlázása

Azure AD B2C számlázás díja a naptári hónapon belül a hitelesítési tevékenységgel rendelkező egyedi felhasználók számának, azaz a havi aktív felhasználók (MAU) számlázásnak.

**2019. november**1-jétől az újonnan létrehozott Azure ad B2C bérlők havi aktív felhasználók (Mau) szerint kerülnek számlázásra. A 2019. november 1-én vagy azt követően az [előfizetéshez kapcsolódó](#link-an-azure-ad-b2c-tenant-to-a-subscription) meglévő bérlők havi aktív felhasználók (Mau) számlán lesznek felszámítva.

Ha olyan meglévő Azure AD B2C Bérlővel rendelkezik, amely egy, a 2019. november 1. előtti előfizetéshez van csatolva, a következők közül választhat:

* Frissítsen a havi aktív felhasználók (MAU) számlázási modelljére, vagy
* Maradjon a hitelesítési számlázási modellen

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Frissítés a havi aktív felhasználói számlázási modellre

Azok az Azure-előfizetések tulajdonosai, akik rendszergazdai hozzáféréssel rendelkeznek a Azure AD B2C erőforráshoz, válthatnak a MAU számlázási modellre. A számlázási beállítások a Azure AD B2C erőforrásban konfigurálhatók.

A havi aktív felhasználók (MAU) számlázására való váltás **visszafordíthatatlan**. Ha egy Azure AD B2C erőforrást a MAU-alapú számlázási modellre konvertál, az erőforrást nem lehet visszaállítani a hitelesítési számlázási modellre.

A következőkben megtudhatja, hogyan válthat egy meglévő Azure AD B2C erőforrást a MAU-számlázásra:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az előfizetés tulajdonosaként.
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a Azure ad B2C könyvtárat, amelyet Mau-számlázásra szeretne frissíteni.<br/>
    ![Címtár-és előfizetés-szűrő a Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. A Azure AD B2C bérlő **Áttekintés** lapján kattintson az **Erőforrás neve**alatt található hivatkozásra. A rendszer átirányítja az Azure AD-bérlő Azure AD B2C erőforrására.<br/>
    ![Azure AD B2C az erőforrás-hivatkozás ki van emelve a Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Az Azure AD B2C erőforrás **áttekintő** lapján a **számlázható egységek**területen válassza ki a **hitelesítést (a Mau-re váltás)** hivatkozásra.<br/>
    ![A Azure Portal Kiemelt MAU-hivatkozásra vált](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Válassza a **megerősítés** lehetőséget a frissítés a Mau-számlázásra való befejezéséhez.<br/>
    ![MAU-alapú számlázási megerősítő párbeszédpanel Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Mit kell várni a MAU-számlázásra való áttéréskor a hitelesítési számlázástól

A MAU-alapú mérés az előfizetés/erőforrás tulajdonosaként azonnal engedélyezve van, erősítse meg a változást. A havi számla a változás és a MAU új egységeinek változása után számlázza a hitelesítési egységeket.

A felhasználók nem számítanak fel kétszer az átmeneti hónap során. A módosítást megelőzően hitelesítő egyedi aktív felhasználóknak egy naptári hónapban kell fizetniük a hitelesítési díjak alapján. Ugyanazok a felhasználók nem szerepelnek a MAU-számításban az előfizetés számlázási ciklusának hátralévő részében. Például:

* A contoso B2C-bérlő 1 000 felhasználóval rendelkezik. 250 a felhasználók egy adott hónapban aktívak. Az előfizetés rendszergazdája a hónap 10. napján megváltoztathatja a hitelesítést a havi aktív felhasználók (MAU) esetében.
* Az 1 – 10. számlázási díj számlázása az egyes hitelesítési modell alapján történik.
  * Ha 100 felhasználó jelentkezik be ebben az időszakban (1 – 10.), ezek *a felhasználók a hónapra kifizetettként*vannak megjelölve.
* A 10 (az áttérés tényleges időpontjában) számlázása a MAU-díj alapján történik.
  * Ha egy további 150 felhasználó jelentkezik be ebben az időszakban (10 – 30.), akkor csak a további 150 lesz kiszámlázva.
  * Az első 100 felhasználó folyamatos tevékenysége nem befolyásolja a naptári hónap hátralévő részének számlázását.

Az áttérés számlázási időszaka során az előfizetés tulajdonosa valószínűleg mindkét módszer (hitelesítés és/MAU) bejegyzéseinek megjelenítése az Azure-előfizetés számlázási utasításában jelenik meg:

* Egy bejegyzés a használathoz, amely a módosítás dátumát és időpontját tükrözi.
* A havi aktív felhasználókat (MAU) tükröző módosítás utáni használat bejegyzése.

A Azure AD B2C használati számlázásával és díjszabásával kapcsolatos legfrissebb információkért tekintse meg a [Azure Active Directory B2C díjszabását](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Azure AD B2C bérlő összekapcsolása egy előfizetéssel

Azure Active Directory B2C (Azure AD B2C) használati díjait egy Azure-előfizetésre számoljuk fel. Azure AD B2C bérlő létrehozásakor a bérlői rendszergazdának explicit módon kell összekapcsolnia a Azure AD B2C bérlőt egy Azure-előfizetéssel.

Az előfizetés hivatkozását úgy érheti el, hogy létrehoz egy Azure AD B2C *erőforrást* a cél Azure-előfizetésen belül. Több Azure AD B2C erőforrás is létrehozható egyetlen Azure-előfizetésben, valamint más Azure-erőforrásokkal, például a virtuális gépekkel, a Storage-fiókokkal és a Logic Appsokkal. Az előfizetésen belüli összes erőforrást megtekintheti az előfizetéshez társított Azure Active Directory (Azure AD) bérlőhöz.

Azure AD B2C bérlőhöz kapcsolódó előfizetés használható Azure AD B2C használat vagy más Azure-erőforrások, többek között a további Azure AD B2C erőforrások számlázására is. Nem használható további Azure-licenc-alapú szolgáltatások vagy Office 365-licencek hozzáadására a Azure AD B2C bérlőn belül.

### <a name="prerequisites"></a>Előfeltételek

* [Azure-előfizetés](https://azure.microsoft.com/free/)
* Az előfizetéshez csatolni kívánt [Azure ad B2C bérlő](tutorial-create-tenant.md)
  * Bérlői rendszergazdának kell lennie
  * A bérlőnek még nincs előfizetéshez csatolva

### <a name="create-the-link"></a>A hivatkozás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a használni kívánt Azure-előfizetést tartalmazza (*nem* pedig a Azure ad B2C bérlőt tartalmazó könyvtár).
1. Válassza az **erőforrás létrehozása**lehetőséget, `Active Directory B2C` írja be a **Keresés a piactéren** mezőbe, majd válassza a **Azure Active Directory B2C**lehetőséget.
1. **Létrehozás** kiválasztása
1. Válassza a **meglévő Azure ad B2C bérlő összekapcsolása az Azure-előfizetéssel**lehetőséget.
1. Válasszon ki egy **Azure ad B2C bérlőt** a legördülő listából. Csak azok a bérlők jelennek meg, amelyekhez Ön globális rendszergazda, és amelyek még nem kapcsolódnak előfizetéshez. A **Azure ad B2C erőforrás neve** mező a kiválasztott Azure ad B2C-bérlő tartománynevével van feltöltve.
1. Válasszon ki egy aktív Azure- **előfizetést** , amelynek Ön a rendszergazdája.
1. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget, majd adja meg az **erőforráscsoport helyét**. Az erőforráscsoport beállításai nincsenek hatással a Azure AD B2C bérlői helyére, teljesítményére vagy számlázási állapotára.
1. Kattintson a **Létrehozás** gombra.
    ![A Azure Portal Azure AD B2C erőforrás-létrehozási lapja](./media/billing/portal-01-create-b2c-resource-page.png)

Miután elvégezte ezeket a lépéseket egy Azure AD B2C bérlő számára, az Azure-előfizetését az Azure Direct-ban vagy Nagyvállalati Szerződés-on alapuló adatok alapján számítjuk fel, ha van ilyen.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C bérlői erőforrások kezelése

Miután létrehozta a Azure AD B2C erőforrást egy Azure-előfizetésben, látnia kell egy "B2C-bérlő" típusú új erőforrást a többi Azure-erőforrással.

Ezt az erőforrást a következő célra használhatja:

* Keresse meg az előfizetést a számlázási adatok áttekintéséhez
* Azure AD B2C bérlő bérlői AZONOSÍTÓjának beolvasása GUID formátumban
* Ugrás a Azure AD B2C-bérlőre
* Támogatási kérelem beküldése
* Azure AD B2C bérlői erőforrás áthelyezése másik Azure-előfizetésre vagy-erőforráscsoporthoz

![A B2C erőforrás-beállítások lapja a Azure Portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Regionális korlátozások

Ha az előfizetésében regionális korlátozásokat hozott létre az Azure-erőforrások létrehozásához, akkor ez a korlátozás megakadályozhatja a Azure AD B2C erőforrás létrehozását.

A probléma megoldásához lazítsa meg regionális korlátozásait.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure Cloud Solution Providers (CSP) előfizetések

A Azure AD B2C támogatja az Azure Cloud Solution Providers (CSP) előfizetéseket. A funkció API-kkal vagy a Azure AD B2Choz és az összes Azure-erőforráshoz Azure Portal érhető el. A CSP-előfizetések rendszergazdái kapcsolatokat kapcsolhatnak össze, helyezhetnek el és törölhetnek más Azure-erőforrásokkal végzett Azure AD B2Cokkal.

A szerepköralapú hozzáférés-vezérlést használó Azure AD B2C felügyeletét a Azure AD B2C bérlő és az Azure CSP-előfizetés közötti társítás nem érinti. A szerepköralapú hozzáférés-vezérlés a bérlői alapú szerepkörök, nem előfizetés-alapú szerepkörök használatával érhető el.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C bérlő számlázási előfizetésének módosítása

Azure AD B2C bérlők áthelyezhetők egy másik előfizetésbe, ha a forrás-és a cél-előfizetések ugyanabban a Azure Active Directory-bérlőn belül vannak.

Ha szeretné megismerni, hogyan helyezhetők át az Azure-erőforrások, például a Azure AD B2C bérlője egy másik előfizetésbe, tekintse meg az [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)

Mielőtt elkezdené az áthelyezést, olvassa el a teljes cikket, amely részletesen ismerteti az ilyen áthelyezésre vonatkozó korlátozásokat és követelményeket. Az erőforrások áthelyezésére vonatkozó utasítások mellett olyan kritikus információkat is tartalmaz, mint például az áthelyezés előtti ellenőrzőlista, valamint a mozgatási művelet ellenőrzése.

## <a name="next-steps"></a>További lépések

A legfrissebb díjszabási információkért lásd: [Azure Active Directory B2C díjszabása](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
