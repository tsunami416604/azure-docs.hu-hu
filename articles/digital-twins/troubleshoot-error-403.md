---
title: 'Az Azure Digital Twins-kérelem meghiúsult a következő állapottal: 403 (tiltott)'
description: 'Sikertelen volt a szolgáltatási kérelem oka és megoldása. Állapot: 403 (tiltott) "az Azure Digital Twins-on.'
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: a971291dd423894e4d04158abe873a7222f9802c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405588"
---
# <a name="service-request-failed-status-403-forbidden"></a>A szolgáltatási kérelem sikertelen. Állapot: 403 (tiltott)

Ez a cikk az okokat és a megoldási lépéseket ismerteti, amelyekkel 403-es hibát fogadhat az Azure Digital Twins szolgáltatástól érkező kérelmekből. 

## <a name="symptoms"></a>Probléma

Ez a hiba számos, hitelesítést igénylő szolgáltatási kérelem esetében fordulhat elő. A hatás az, hogy az API-kérelem sikertelen, a hiba állapotát adja vissza `403 (Forbidden)` .

## <a name="causes"></a>Okok

### <a name="cause-1"></a>Ok #1

Ez a hiba általában azt jelzi, hogy a szolgáltatáshoz tartozó szerepköralapú hozzáférés-vezérlési (RBAC) engedélyek nincsenek megfelelően beállítva. Az Azure Digital Twins-példányok számos művelete megköveteli, hogy a **felügyelni kívánt példányon**rendelkezzen az *Azure Digital Twins tulajdonos (előzetes verzió)* szerepkörrel. 

### <a name="cause-2"></a>Ok #2

Ha ügyfélalkalmazás használatával kommunikál az Azure Digital Twins szolgáltatással, ez a hiba azért fordulhat elő, mert a [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) alkalmazás regisztrációja nem rendelkezik az Azure Digital Twins szolgáltatáshoz beállított engedélyekkel.

Az alkalmazás regisztrációjának az Azure Digital Twins API-khoz konfigurált hozzáférési engedélyekkel kell rendelkeznie. Ezután, amikor az ügyfélalkalmazás az alkalmazás regisztrálásakor hitelesíti magát, megkapja az alkalmazás-regisztrációhoz megadott engedélyeket.

## <a name="solutions"></a>Megoldások

### <a name="solution-1"></a>Megoldás #1

Az első megoldás annak ellenőrzése, hogy az Azure-felhasználó rendelkezik-e az _**Azure Digital Twins tulajdonos (előzetes verzió)**_ szerepkörrel a felügyelni kívánt példányon. Ha nem rendelkezik ezzel a szerepkörrel, állítsa be.

Vegye figyelembe, hogy ez a szerepkör eltér a következőtől:...
* a *tulajdonosi* szerepkör a teljes Azure-előfizetésben. Az *Azure Digital Twins tulajdonosa (előzetes verzió)* az Azure digitális ikrek egyik szerepköre, és az egyes Azure digitális Twins-példányokra is kiterjed.
* a *tulajdonosi* szerepkör az Azure digitális ikrekben. Ez két különböző Azure digitális Twins-felügyeleti szerepkör, és az *Azure Digital Twins tulajdonosa (előzetes verzió)* az előzetes verzióban való felügyelethez használt szerepkör.

#### <a name="check-current-setup"></a>Aktuális beállítás keresése

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Hibák elhárítása 

Ha nem rendelkezik ezzel a szerepkör-hozzárendeléssel, az **Azure-előfizetésben** lévő tulajdonosi szerepkörrel rendelkező személynek az alábbi paranccsal kell futtatnia az Azure Digital *Twins tulajdonos (előzetes verzió)* szerepkört az Azure **Digital Twins-példányon**. 

Ha Ön az előfizetés tulajdonosa, saját maga is futtathatja ezt a parancsot. Ha nem, forduljon a tulajdonoshoz, és futtassa ezt a parancsot az Ön nevében.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

További információ erről a szerepkör-követelményről és a hozzárendelési folyamatról: a [ *felhasználó hozzáférési engedélyeinek beállítása* szakasz](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) , *útmutató: példány és hitelesítés beállítása (CLI vagy portál)*.

Ha már rendelkezik ezzel a szerepkör-hozzárendeléssel, és továbbra is a 403-es problémát tapasztalja, folytassa a következő megoldással.

### <a name="solution-2"></a>Megoldás #2

A második megoldás annak ellenőrzése, hogy az Azure AD-alkalmazás regisztrációja rendelkezik-e az Azure Digital Twins szolgáltatáshoz konfigurált engedélyekkel. Ha nincs konfigurálva, állítsa be őket.

#### <a name="check-current-setup"></a>Aktuális beállítás keresése

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Először ellenőrizze, hogy az Azure Digital Twins engedélyeinek beállításai megfelelően vannak-e beállítva a regisztrációhoz. Ehhez a menüsávban válassza a *jegyzékfájl* lehetőséget az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a következő mezőket `requiredResourceAccess` . Az értékeknek meg kell egyezniük az alábbi képernyőképen láthatókkal:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Hibák elhárítása

Ha ezek bármelyike másképp jelenik meg, mint a leírtak között, kövesse az alkalmazások regisztrálásának beállítása című témakör útmutatását az [ *ügyfélalkalmazások hozzáférési engedélyeinek beállítása* című szakaszban](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) , *útmutató: példány és hitelesítés beállítása (parancssori felület vagy portál)*.

## <a name="next-steps"></a>Következő lépések

Olvassa el a telepítési lépéseket az új Azure Digital Twins-példány létrehozásához és hitelesítéséhez:
* [*Útmutató: példány és hitelesítés beállítása (CLI)*](how-to-set-up-instance-cli.md)

További információ az Azure Digital Twins biztonságáról és engedélyeiről:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)