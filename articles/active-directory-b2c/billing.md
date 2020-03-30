---
title: Az Azure Active Directory B2C számlázási modellje
description: Ismerje meg az Azure AD B2C havi aktív felhasználóinak (MAU) számlázási modelljét, és hogy miként engedélyezheti a számlázást egy adott Azure-előfizetéshez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190007"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Az Azure Active Directory B2C számlázási modellje

Az Azure Active Directory B2C (Azure AD B2C) használat számlázása egy csatolt Azure-előfizetés, és egy havi aktív felhasználók (MAU) számlázási modellt használ. Megtudhatja, hogyan kapcsolhat össze egy Azure AD B2C-erőforrást egy előfizetéssel, és hogyan működik a MAU számlázási modell a következő szakaszokban.

> [!IMPORTANT]
> Ez a cikk nem tartalmaz díjszabási információkat. A használati számlázással és díjszabással kapcsolatos legfrissebb információkért lásd az [Azure Active Directory B2C díjszabását.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="monthly-active-users-mau-billing"></a>Havi aktív felhasználók (MAU) számlázása

Az Azure AD B2C-számlázás egy naptári hónapon belüli hitelesítési tevékenységgel rendelkező egyedi felhasználók száma alapján történik, amelyet havi aktív felhasználók (MAU) számlázásának neveznek.

**2019. november 01-től**az összes újonnan létrehozott Azure AD B2C-bérlő havi aktív felhasználó (MAU) számlázása. 2019. november 1-jén vagy azt követően [egy előfizetéshez kapcsolódó](#link-an-azure-ad-b2c-tenant-to-a-subscription) meglévő bérlők nek havi aktív felhasználókat (MAU) kell kiszámlázni.

Ha olyan meglévő Azure AD B2C-bérlővel rendelkezik, amely 2019.

* Frissítés a havi aktív felhasználók (MAU) számlázási modellre, vagy
* Maradjon a hitelesítésenkénti számlázási modellen

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Frissítés havi aktív felhasználók számlázási modelljére

Az Azure-előfizetés tulajdonosai, amelyek rendszergazdai hozzáféréssel rendelkeznek az Azure AD B2C erőforrását, átválthatnak a MAU számlázási modellre. A számlázási beállítások az Azure AD B2C-erőforrásban vannak konfigurálva.

A havi aktív felhasználók (MAU) számlázásra való átállás **visszafordíthatatlan.** Miután konvertáltegy Azure AD B2C-erőforrást a MAU-alapú számlázási modellre, nem állíthatja vissza az erőforrást a hitelesítési számlázási modellre.

A következőképpen válthat mau-számlázásra egy meglévő Azure AD B2C-erőforráshoz:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az előfizetés tulajdonosaként.
1. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, majd válassza ki az Azure AD B2C könyvtárat, amelyet mau-számlázásra szeretne frissíteni.<br/>
    ![Címtár- és előfizetésszűrő az Azure Portalon](./media/billing/portal-mau-01-select-b2c-directory.png)
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Az Azure AD B2C-bérlő **Áttekintés lapján** válassza ki a hivatkozást az **Erőforrás neve területen.** Az Azure AD B2C-erőforrás az Azure AD-bérlőben.<br/>
    ![Az Azure AD B2C erőforráshivatkozás kiemelve az Azure Portalon](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Az Azure AD B2C erőforrás **Áttekintés lapján** a **Számlázható egységek**csoportban válassza ki a **Hitelesítésenkénti (Módosítás MAU-ra)** hivatkozást.<br/>
    ![Váltás az Azure Portalon kiemelt MAU hivatkozásra](./media/billing/portal-mau-03-change-to-mau-link.png)
1. A MAU-számlázásra való frissítés befejezéséhez válassza a **Megerősítés** lehetőséget.<br/>
    ![MAU-alapú számlázás-megerősítési párbeszédpanel az Azure Portalon](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Mire számíthat, amikor áttér a MAU-számlázásra a hitelesítésenkénti számlázásból?

Mau-alapú mérés engedélyezve van, amint Ön, az előfizetés/erőforrás tulajdonosa, erősítse meg a módosítást. A havi számla a módosításig számlázott hitelesítési egységeket, valamint a módosítással kezdődő új MAU-egységeket fogja tükrözni.

A felhasználók nem számítanak kétszer az átmeneti hónapban. Azok az egyedi aktív felhasználók, akik a módosítás előtt hitelesítik magukat, egy naptári hónapban hitelesítésenkénti díjat számítanak fel. Ugyanezek a felhasználók nem szerepelnek a MAU számítás a fennmaradó előfizetés számlázási ciklus. Példa:

* A Contoso B2C bérlő 1000 felhasználóval rendelkezik. 250 felhasználó aktív egy adott hónapban. Az előfizetés rendszergazdája a hónap 10-én hitelesítésenkénti állapotról havi aktív felhasználókra (MAU) vált.
* Az 1.-10.
  * Ha 100 felhasználó jelentkezik be ebben az időszakban (1. -10.), akkor ezek a felhasználók *a hónapra vonatkozó fizetettként*vannak megjelölve.
* A 10-es (az áttérés tényleges időpontjától) számlázása mau díj.
  * Ha ebben az időszakban további 150 felhasználó jelentkezik be (10-30.), csak a további 150-et kell kiszámlázni.
  * Az első 100 felhasználó folyamatos tevékenysége nincs hatással a naptári hónap hátralévő részére vonatkozó számlázásra.

Az átállás számlázási időszakában az előfizetés tulajdonosa valószínűleg mindkét módszer (hitelesítésenként és MAU-nként) bejegyzéseit fogja látni az Azure-előfizetés számlázási kimutatásában:

* A használat bejegyzése a módosítás dátumáig/időpontjáig, amely a hitelesítést tükrözi.
* A havi aktív felhasználókat (MAU) tükröző, a módosítás utáni használatra vonatkozó bejegyzés.

Az Azure AD B2C használati számlázásával és díjszabásával kapcsolatos legfrissebb információkért lásd: [Azure Active Directory B2C díjszabás.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Azure AD B2C-bérlő csatolása előfizetéshez

Az Azure Active Directory B2C (Azure AD B2C) használati díjait egy Azure-előfizetésnek számlázjuk. Amikor egy Azure AD B2C-bérlő jön létre, a bérlői rendszergazdának explicit módon kell kapcsolnia az Azure AD B2C-bérlőt egy Azure-előfizetéshez.

Az előfizetési kapcsolat érhető el egy Azure AD B2C *erőforrás* a cél Azure-előfizetésen belül. Számos Azure AD B2C-erőforrás hozható létre egyetlen Azure-előfizetésben, valamint más Azure-erőforrásokkal, például virtuális gépekkel, Storage-fiókokkal és logikai alkalmazásokkal együtt. Az előfizetésen belüli összes erőforrást megtekintheti az Azure Active Directory (Azure AD) bérlőhöz, amelyhez az előfizetés társítva van.

Az Azure AD B2C-bérlőhöz kapcsolódó előfizetés használható az Azure AD B2C-használat vagy más Azure-erőforrások számlázásához, beleértve a további Azure AD B2C-erőforrásokat is. Nem használható más Azure-licencalapú szolgáltatások vagy Office 365-licencek hozzáadására az Azure AD B2C bérlőn belül.

### <a name="prerequisites"></a>Előfeltételek

* [Azure-előfizetés](https://azure.microsoft.com/free/)
* [Az Azure AD B2C-bérlő,](tutorial-create-tenant.md) amelyet egy előfizetéshez szeretne kapcsolni
  * Bérlői rendszergazdának kell lennie
  * A bérlőt már nem szabad előfizetéshez kapcsolni.

### <a name="create-the-link"></a>A hivatkozás létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki a könyvtárat, amely tartalmazza az Azure-előfizetést szeretne használni *(nem* az Azure AD B2C bérlőt tartalmazó könyvtár).
1. Válassza **az Erőforrás** `Active Directory B2C` létrehozása lehetőséget, írja be a **Keresés a piactéren** mezőt, majd válassza az **Azure Active Directory B2C**lehetőséget.
1. Válassza a **Létrehozás lehetőséget**
1. Válassza **a Hivatkozás egy meglévő Azure AD B2C-bérlő az én Azure-előfizetés.**
1. Válasszon ki egy **Azure AD B2C-bérlőt** a legördülő menüből. Csak azok a bérlők jelennek meg, amelyek globális rendszergazdája, és amelyek még nem kapcsolódnak előfizetéshez. Az **Azure AD B2C erőforrás neve** mező a kiválasztott Azure AD B2C-bérlő tartománynevével van feltöltve.
1. Válasszon ki egy aktív **Azure-előfizetést,** amelynek rendszergazdája.
1. Az **Erőforráscsoport**csoportban válassza **az Új létrehozása**lehetőséget, majd adja meg az **Erőforráscsoport helyét.** Az erőforráscsoport-beállítások itt nincsenek hatással az Azure AD B2C-bérlői helyre, teljesítményre vagy számlázási állapotra.
1. Kattintson a **Létrehozás** gombra.
    ![Az Azure AD B2C-erőforrások létrehozása lap az Azure Portalon](./media/billing/portal-01-create-b2c-resource-page.png)

Miután elvégezte ezeket a lépéseket egy Azure AD B2C-bérlőnél, az Azure-előfizetésszámlázása az Azure Direct vagy a Nagyvállalati szerződés részleteinek megfelelően történik, ha van ilyen.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Az Azure AD B2C bérlői erőforrások kezelése

Miután létrehozta az Azure AD B2C erőforrást egy Azure-előfizetésben, meg kell jelennie egy új, "B2C tenant" típusú erőforrásnak a többi Azure-erőforrással együtt.

Az erőforrás a következőkre használható:

* Keresse meg az előfizetést a számlázási adatok áttekintéséhez
* Az Azure AD B2C bérlő bérlői azonosítójának beszereznie GUID formátumban
* Nyissa meg az Azure AD B2C-bérlőt
* Támogatási kérelem benyújtása
* Az Azure AD B2C bérlői erőforrás áthelyezése egy másik Azure-előfizetésbe vagy erőforráscsoportba

![B2C Erőforrás-beállítások lap az Azure Portalon](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Regionális korlátozások

Ha az előfizetésben regionális korlátozásokat hozott létre az Azure-erőforrások létrehozásához, ez a korlátozás megakadályozhatja az Azure AD B2C erőforrás létrehozását.

A probléma enyhítése érdekében lazítson a regionális korlátozásokon.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure felhőszolgáltatói (CSP) előfizetések

Az Azure Cloud Solution Providers (CSP) előfizetések az Azure AD B2C támogatottak. A funkció API-k vagy az Azure AD B2C és az összes Azure-erőforrás Azure-portálhasználatával érhető el. A CSP-előfizetés-rendszergazdák más Azure-erőforrásokkal együtt összekapcsolhatják, áthelyezhetik és törölhetik az Azure AD B2C-vel létesített kapcsolatokat.

Az Azure AD B2C szerepköralapú hozzáférés-vezérlés használatával az Azure AD B2C-bérlő és egy Azure CSP-előfizetés közötti társítás nem befolyásolja a felügyelt Azure AD B2C-előfizetés kezelését. A szerepköralapú hozzáférés-vezérlés bérlőalapú szerepkörök, nem előfizetés-alapú szerepkörök használatával érhető el.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Az Azure AD B2C-bérlői számlázási előfizetés módosítása

Az Azure AD B2C-bérlők áthelyezhetők egy másik előfizetésbe, ha a forrás- és cél-előfizetések ugyanabban az Azure Active Directory-bérlőben találhatók.

Az Azure-erőforrások, például az Azure AD B2C-bérlő áthelyezése egy másik előfizetésbe [az Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Move resources to new resource group or subscription) (Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe) témakörből](../azure-resource-manager/management/move-resource-group-and-subscription.md)megtudhatja.

Mielőtt kezdeményezné a lépés, győződjön meg róla, hogy olvassa el a teljes cikket, hogy teljes mértékben megértsék a korlátozásokat és követelményeket egy ilyen lépés. Az erőforrások áthelyezésére vonatkozó utasításokon kívül olyan fontos információkat is tartalmaz, mint például az áthelyezés előtti ellenőrzőlista és az áthelyezési művelet ellenőrzése.

## <a name="next-steps"></a>További lépések

A legújabb díjszabási információkért lásd: [Azure Active Directory B2C díjszabás.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)
