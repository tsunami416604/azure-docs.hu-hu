---
title: Felhőalapú megoldások szolgáltatói | Azure piactér
description: A kiadók az ajánlatokat Microsoft Cloud megoldás-szolgáltató (CSP) partneri csatornán keresztül is eladhatják.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: b962610c585df288a9cb3297ed8e09c8abc5ac0a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160647"
---
# <a name="cloud-solution-providers"></a>Felhőszolgáltatók

A szoftveres ajánlatok több millió minősített Microsoft-ügyfelet is elérhet a felhőalapú megoldás-szolgáltatói (CSP) programban, az ajánlatok nyilvános rendelkezésre állása mellett a [Microsoft Web kirakatokon](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)keresztül.

A kiadók a rendelkezésre állásra vonatkozó ajánlatokat a CSP programban, egy új ajánlathoz vagy egy meglévőhöz konfigurálják, amely lehetővé teszi, hogy a partnerek eladják termékeiket, és kötegelt megoldásokat hozzanak létre az ügyfeleknek.

A kiadók feladata, hogy a végfelhasználók szüntesse meg a hibajavítást, és hogy a CSP-programban és/vagy az ügyfeleknél olyan mechanizmust biztosítson, amely segítséget nyújt a partnereknek. Az ajánlott eljárás az, hogy partnereket biztosítson a CSP programban a felhasználói dokumentációval, a képzéssel és a szolgáltatás állapotával/KIMARADÁSÁVAL kapcsolatos értesítésekkel (ha vannak ilyenek), hogy a CSP programban részt vevő partnerek az 1. szintű támogatási kérések kezelésére alkalmasak legyenek.  

A következő ajánlatok jogosultak arra, hogy a partnerek által a CSP programban való értékesítésre legyenek feljogosítva:

- Szolgáltatott szoftveres (SaaS) Transact-ajánlatok
- Virtual Machines (VM)
- Megoldássablonok
- Felügyelt alkalmazások

> [!NOTE]
> A tárolók és a saját licencek (BYOL) virtuálisgép-SKU-ket úgy kell megválasztani, hogy alapértelmezés szerint a kriptográfiai szolgáltatói programban lévő partnerek is eladják azokat.

## <a name="how-to-configure-an-offering"></a>Ajánlat konfigurálása

A CSP program bekapcsolási beállítása a partner központban van konfigurálva, vagy Cloud Partner Portal ajánlat-létrehozási élmény. [További információ a kiadói élmény megváltoztatásáról](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Partneri központ – opt

A partner Centerben a CSP viszonteladói célközönség modulban találhatja meg a bekapcsolási funkciót.

![CSP viszonteladói közönség](media/marketplace-publishers-guide/csp-reseller-audience.png)

A CSP-viszonteladói célközönség modulban három lehetőség közül választhat:

- Egy lehetőség: bármely partner a CSP programban
- Második lehetőség: a kiválasztott CSP programban megadott partnerek
- Harmadik lehetőség: nincsenek partnerek a CSP programban

#### <a name="option-one-any-partner-in-the-csp-program"></a>Egy lehetőség: bármely partner a CSP programban

![Bármely partner a CSP programban](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ha ezt a lehetőséget választja, a CSP program összes partnere jogosult az ajánlatának viszonteladására az ügyfelek számára.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Második lehetőség: a kiválasztott CSP programban megadott partnerek

![A CSP programban kiválasztott partnerek](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ennek a lehetőségnek a kiválasztásával engedélyezheti, hogy a CSP program mely partnerei jogosultak az ajánlat viszonteladására.

A partnerek engedélyezéséhez kattintson a **CSP-partnerek kijelölése** lehetőségre, és megjelenik egy menü, amely lehetővé teszi a partner neve vagy a CSP Azure Active Directory (HRE) BÉRLŐi azonosítójának keresését.

![CSP menü kiválasztása](media/marketplace-publishers-guide/csp-pop-up-module.png)

Keresési szűrőket alkalmazhat, például **országokat**, **kompetenciákat**vagy **képességeket**.

![A partnerek keresésének országa, kompetenciája és képzettségi szűrői](media/marketplace-publishers-guide/csp-add-resellers.png)

Miután kiválasztotta a partnerek listáját, válassza a **Hozzáadás**lehetőséget.

![A KRIPTOGRÁFIAi programban a jóváhagyott partnerek listája](media/marketplace-publishers-guide/csp-add-resellers-details.png)

A kiválasztott partnerek listáját megjelenítő tábla megjelenik a CSP-viszonteladó célközönsége lapon.

![A CSP viszonteladói célközönségének oldalán található partnerek listáját tartalmazó tábla](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

A módosítások regisztrálásához kattintson a **Piszkozat mentése** gombra.

Ha ez az ajánlat közzé van téve, közzé kell tennie az ajánlatot, hogy elérhetővé tegye a kiválasztott partnerei számára.

>[!NOTE]
>Ha egy adott régióban engedélyez egy partnert a CSP programban, az ajánlatot az adott régióhoz tartozó bármely ügyfélnek is értékesítheti. A CSP-ajánlatok régiókban való besorolásával kapcsolatos további információkért tekintse meg a [Cloud Solution Provider program regionális piacait és pénznemét](https://docs.microsoft.com/partner-center/regional-authorization-overview) ismertető témakört.

Ha egy már közzétett ajánlat CSP-listáját frissíti, vegye fel a további partnereket, és válassza a **szinkronizálás CSP-célközönség**lehetőséget.

Ha van olyan ajánlata, amely már rendelkezik a jogosult partnerek listájával, és egy másik ajánlathoz ugyanazt a listát szeretné használni, használja az **Importálás/exportálás**lehetőséget. Navigáljon a CSP-listát tartalmazó ajánlathoz, és válassza a **kriptográfiai szolgáltatók exportálása**lehetőséget. A függvény fejleszt egy. csv-fájlt, amely importálható egy másik ajánlatba.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Harmadik lehetőség: nincsenek partnerek a CSP programban

![Nincsenek partnerek a CSP programban](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ha ezt a lehetőséget választja, az ajánlatát a CSP programból választhatja ki. Ezt a lehetőséget bármikor módosíthatja.

### <a name="cloud-partner-portal-opt-in"></a>Cloud Partner Portal

Cloud Partner Portal a bekapcsolási lehetőség a piactér vagy a kirakat lapon van beállítva. A CSP programban meghatározott partnerek kiválasztásának lehetősége csak a partner Centerben érhető el.

![CSP – felhasználói élmény a CPP-ben](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Partnerek dehatalmazása a CSP programban

Ha engedélyezte a partnert a CSP programban, és a partner már eladta a terméket az ügyfeleknek, akkor nem engedélyezheti a partnert.

Ha a CSP program egyik partnere nem adta meg a terméket az ügyfeleknek, és az ajánlat közzétételét követően el szeretné távolítani a CSP-t, kövesse az alábbi utasításokat:

1. Lépjen a [support Request lapra](https://partner.microsoft.com/support/v2/?stage=1). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése**lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése**lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.




## <a name="navigate-between-options"></a>Navigáljon a beállítások között

Ebben a szakaszban a három CSP-viszonteladói lehetőség közül lehet navigálni.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigáljon a (z) lehetőség közül: bármelyik partner a CSP programban

Ha az ajánlat jelenleg **1. lehetőség: bármely partner a CSP programban** , és a másik két lehetőség közül bármelyiket el szeretné érni, a következő utasításokat követve hozhat létre egy kérést:

1. Lépjen a [support Request lapra](https://partner.microsoft.com/support/v2/?stage=1). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése**lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése**lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

> [!NOTE]
> Ha a két lehetőségre próbál navigálni, és a CSP-program egyik partnere már eladta az ajánlatot az ügyfelek számára, akkor a partner alapértelmezés szerint már szerepel a jóváhagyott partnerek listáján.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigáljon a második lehetőség közül: válassza ki az adott partnereket a CSP programban.

Ha az ajánlat jelenleg **2. lehetőség: a CSP programban kiválasztott partnerek közül választhatok** , és az egyiket szeretné megkeresni **: bármely partner a CSP programban**, az alábbi utasításokat követve hozzon létre egy kérést:

1. Lépjen a [support Request lapra](https://partner.microsoft.com/support/v2/?stage=1). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése**lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése**lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

 Ha az ajánlat jelenleg **2. lehetőség: a CSP programban kiválasztott partnerek közül választhatok** , és a **3. lehetőségre szeretne navigálni: nem található partner a CSP programban**, csak akkor fog tudni erre a lehetőségre navigálni, ha a CSP-program korábban már engedélyezte a partnereket, hogy a végfelhasználóknak ne eladják az ajánlatot. Kérelem létrehozásához kövesse az alábbi utasításokat:

1. Lépjen a [support Request lapra](https://partner.microsoft.com/support/v2/?stage=1). Az első néhány legördülő menüt automatikusan kitölti a rendszer.

   > [!NOTE]
   > Ne módosítsa a legördülő menü előre megadott beállításait.

2. **A termék verziójának kiválasztásához válassza az** **élő ajánlat kezelése**lehetőséget.
3. Válassza ki azt a kategóriát, **amely a legjobban leírja a problémát**, és válassza ki az ajánlatra hivatkozó kategóriát.
4. Ha **olyan problémát választ ki, amely a legjobban leírja a problémát**, válassza a **meglévő ajánlat frissítése**lehetőséget.
5. Kattintson a **tovább** gombra a **probléma részletei lapra** való átirányításhoz, és adja meg a probléma további részleteit.
6. A probléma címéhez használja a **kriptográfiai szolgáltatót** , és adja meg a többi szükséges szakaszt.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigáljon a 3. lehetőséghez: nincs partner a CSP programban

Ha az ajánlat jelenleg **3. lehetőség: nincs partner a CSP programban**, bármikor megnyithatja a másik két lehetőség valamelyikét.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Értékesítési és támogatási anyagok megosztása partnerekkel a CSP programban

Ahhoz, hogy a partnerek a felhőalapú megoldás-szolgáltatói programban leghatékonyabban képviseljék az ajánlatát, és a szervezete is részt vesz, olyan értékesítési és támogatási anyagokat kell benyújtania, amelyek elérhetők lesznek a viszonteladók számára. Ezek az erőforrások nem lesznek elérhetők a piactéren kirakatokban lévő ügyfelek számára.

### <a name="partner-center-csp-channel"></a>Partneri központ CSP-csatornája

Ha kiválasztotta a CSP-csatornát a partner Centerben, a kiadónak meg kell adnia egy URL-címet, amely a megfelelő marketinganyagok és csatorna elérhetőségi adatait tárolja a CSP-csatornán az ajánlati lista modulban:

![A partner Center CSP-biztosítékokkal kapcsolatos információi](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Cloud Partner Portal CSP-csatorna

Ha a Cloud Partner Portal a CSP-csatornát választotta, a kiadónak olyan URL-címet kell megadnia, amely a megfelelő marketinganyagok és csatorna elérhetőségi adatait tárolja a CSP-csatornán:

![Cloud Partner Portal CSP-biztosíték információi](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>További lépések

Látogasson el az [Azure Marketplace és a AppSource kiadói útmutatóba](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

A piactér GTM szolgáltatásaival kapcsolatos további információkért tekintse meg a piacra jutási [szolgáltatásokat](https://partner.microsoft.com/reach-customers/gtm)ismertető témakört.

Az ajánlat létrehozásához és konfigurálásához jelentkezzen be a [partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
