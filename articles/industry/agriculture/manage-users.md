---
title: Felhasználók kezelése
description: Ez a cikk a felhasználók felügyeletét ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: f58db00e598fd08972adb60cbd8aae0d3bf3391d
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942311"
---
# <a name="manage-users"></a>Felhasználók kezelése

Az Azure FarmBeats felhasználói felügyeletet tartalmaz a Azure Active Directory (Azure AD) részét képező felhasználók számára. Hozzáadhat felhasználókat az Azure FarmBeats-példányhoz az API-k eléréséhez, megtekintheti a generált térképeket és az érzékelő telemetria a farmból.

## <a name="prerequisites"></a>Előfeltételek

- Szükség van egy Azure FarmBeats-telepítésre. Az Azure FarmBeats beállításával kapcsolatos további információkért lásd: a [FarmBeats telepítése](prepare-for-deployment.md) .
- Az Azure FarmBeats-példányban hozzáadni vagy eltávolítani kívánt felhasználók e-mail-azonosítója.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats-felhasználók kezelése

Az Azure FarmBeats az Azure AD-t használja a hitelesítéshez, a hozzáférés-vezérléshez és a szerepkörökhöz. Az Azure AD-bérlőben lévő felhasználók hozzáadhatók az Azure FarmBeats felhasználóihoz.

> [!NOTE]
> Ha a hozzáadni kívánt felhasználó nem található az Azure AD-bérlőben, kövesse az **Azure ad-felhasználók hozzáadása** szakasz utasításait a telepítés befejezéséhez, mielőtt továbblép az Azure FarmBeats-felhasználók beállítására.

**Szerepkörök**

Az Azure FarmBeats kétféle felhasználói szerepkört támogat:

 - **Rendszergazda** – az Azure FarmBeats adatközpont API-khoz való összes hozzáférés. Az ebben a szerepkörben lévő felhasználók lekérhetik az összes Azure FarmBeats-adatközpont-objektumot, végrehajtják a FarmBeats-gyorsító összes műveletét.
 - **Írásvédett** – csak olvasási hozzáférés a FarmBeats adatközpont API-khoz. A felhasználók megtekinthetik az adatközponti API-kat, a gyorsító irányítópultokat és térképeket. A "csak olvasási" szerepkörrel rendelkező felhasználók nem hajthatnak végre olyan műveleteket, mint például a Maps létrehozása, az eszközök társítása vagy a farmok létrehozása.


## <a name="add-user-to-azure-farmbeats"></a>Felhasználó hozzáadása az Azure FarmBeats

A következő lépésekkel adhat hozzá felhasználót az Azure FarmBeats:

1.  Jelentkezzen be a Gyorssegédbe, majd válassza a **Beállítások** ikont.
2.  Válassza a **Access Control**lehetőséget.

    ![A Project Farm veri](./media/create-farms/settings-users-1.png)

3.  Adja meg annak a felhasználónak az e-mail-AZONOSÍTÓját, akinek hozzáférést szeretne biztosítani.
4.  Válassza ki a kívánt szerepkört – rendszergazda vagy csak olvasható.
5.  Válassza a **szerepkör hozzáadása**elemet.

A hozzáadott felhasználó (k) mostantól elérhetővé válik az Azure FarmBeats (az adatközpont és a gyorsító) számára.

## <a name="delete-user-from-azure-farmbeats"></a>Felhasználó törlése az Azure FarmBeats

A következő lépésekkel távolíthat el egy felhasználót az Azure FarmBeats rendszerből:

1.  Jelentkezzen be a Gyorssegédbe, majd válassza a **Beállítások** ikont.
2.  Válassza a **Access Control**lehetőséget.
3.  A **Törlés** gombra kattintva törölheti a felhasználókat.

A felhasználó törlődik a rendszerből. A sikeres művelet megerősítéséhez a következő üzenet jelenik meg.

![A Project Farm veri](./media/create-farms/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD-felhasználók hozzáadása

> [!NOTE]
> Kövesse az alábbi lépéseket az Azure FarmBeats való felhasználói hozzáférés biztosításához, ha a felhasználó nem létezik az Azure AD-bérlőben. Az alábbi lépéseket kihagyhatja, ha a felhasználó létezik az Azure AD-bérlőben.
>

Az FarmBeats felhasználóknak az Azure AD-bérlőben kell rendelkezniük ahhoz, hogy hozzá lehessen rendelni azokat az alkalmazásokhoz és szerepkörökhöz. Ha felhasználókat szeretne hozzáadni az Azure AD-hez, kövesse az alábbi lépéseket:

1.  Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2.  Válassza ki a fiókját a jobb felső sarokban, és váltson a FarmBeats társított Azure AD-bérlőre.
3.  Válassza **Azure Active Directory > felhasználók**lehetőséget.
    Megjelenik a címtárban található felhasználók listája.
4.  Ha felhasználókat szeretne hozzáadni a címtárhoz, válassza az **új felhasználó**lehetőséget. Külső felhasználók esetében válassza az **új vendég felhasználó**lehetőséget.

    ![A Project Farm veri](./media/create-farms/manage-users-3.png)

5.  Töltse ki az új felhasználóhoz szükséges mezőket. Kattintson a **Létrehozás** gombra.

Az Azure ad-ben található felhasználók kezelésével kapcsolatos további információkért látogasson el az [Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) dokumentációjában.

## <a name="next-steps"></a>Következő lépések

Sikeresen hozzáadta a felhasználókat az Azure FarmBeats-példányhoz. Most Ismerkedjen meg a [farmok létrehozásával](manage-farms.md#create-farms).
