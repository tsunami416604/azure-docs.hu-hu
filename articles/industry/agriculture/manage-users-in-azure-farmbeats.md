---
title: Felhasználók kezelése az Azure FarmBeats-ben
description: Ez a cikk ismerteti, hogyan kezelheti a felhasználók at Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502065"
---
# <a name="manage-users"></a>Felhasználók kezelése

Az Azure FarmBeats felhasználói felügyeletet tartalmaz az Azure Active Directory (Azure AD) példány részét vevő személyek számára. Felhasználók hozzáadása az Azure FarmBeats-példány hoz az API-k eléréséhez, a létrehozott térképek megtekintéséhez, és a hozzáférés érzékelő telemetriai a farmból.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure FarmBeats telepítése szükséges. További információ: [Install Azure FarmBeats](install-azure-farmbeats.md).
- Az Azure FarmBeats-példányból hozzáadni vagy eltávolítani kívánt felhasználók e-mail-azonosítói.

## <a name="manage-azure-farmbeats-users"></a>Az Azure FarmBeats felhasználóinak kezelése

Az Azure FarmBeats az Azure AD-t használja a hitelesítéshez, a hozzáférés-vezérléshez és a szerepkörökhöz. Az Azure AD-bérlőben felhasználókat adhat hozzá az Azure FarmBeats-ben.

> [!NOTE]
> Ha egy felhasználó nem Egy Azure AD-bérlői felhasználó, kövesse az Utasításokat az **Azure AD-felhasználók hozzáadása** szakaszban a beállítás befejezéséhez.

Az Azure FarmBeats kétféle felhasználói szerepkört támogat:

 - **Rendszergazda:** Teljes hozzáférés az Azure FarmBeats Datahub API-khoz. Ebben a szerepkörben lévő felhasználók lekérdezhetik az összes Azure FarmBeats Datahub-objektumot, és végrehajthatják a FarmBeats gyorsítóösszes műveletét.
 - **Írásvédett:** A FarmBeats Datahub API-k írásvédett elérése. A felhasználók megtekinthetik a Datahub API-kat, a gyorssegéd irányítópultjait és a térképeket. Az írásvédett hozzáféréssel rendelkező felhasználók nem hajthatnak végre olyan műveleteket, mint a térképek létrehozása, az eszközök társítása vagy farmok létrehozása.

## <a name="add-users-to-azure-farmbeats"></a>Felhasználók hozzáadása az Azure FarmBeats szolgáltatáshoz

Felhasználók hozzáadása az Azure FarmBeats szolgáltatáshoz:

1. Jelentkezzen be a gyorssegédbe, és kattintson a **Beállítások** ikonra.
2. Válassza **a Hozzáférés-vezérlés lehetőséget**.

    ![A Farmok beállításai ablaktábla](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Adja meg annak a felhasználónak az e-mail azonosítóját, akihez hozzáférést kíván adni.
4. Válassza ki a kívánt szerepkört, **rendszergazda** vagy **írásvédett.**
5. Válassza **a Szerepkör hozzáadása lehetőséget.**

A hozzáadott felhasználó most már elérheti az Azure FarmBeats (mind datahub és gyorsító).

## <a name="delete-users-from-azure-farmbeats"></a>Felhasználók törlése az Azure FarmBeats szolgáltatásból

Felhasználók eltávolítása az Azure FarmBeats rendszerből:

1. Jelentkezzen be a gyorssegédbe, és kattintson a **Beállítások** ikonra.
2. Válassza **a Hozzáférés-vezérlés lehetőséget**.
3. Válassza a **Törlés** elemet.

   A felhasználó törlődik a rendszerből. A következő megerősítő üzenet jelenik meg:

   ![Az Azure FarmBeats megerősítő üzenete](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD-felhasználók hozzáadása

> [!NOTE]
> Az Azure FarmBeats-felhasználóknak létezniük kell az Azure AD-bérlőben, mielőtt hozzárendeli őket az alkalmazásokhoz és szerepkörökhöz. Ha egy felhasználó nem létezik az Azure AD-bérlőben, kövesse az ebben a szakaszban található utasításokat. Hagyja ki az utasításokat, ha egy felhasználó már létezik az Azure AD-bérlőben.

Kövesse a felhasználók Azure AD-hez való hozzáadásának lépéseit:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. A jobb felső sarokban válassza ki a fiókját, majd váltson a FarmBeats-hez társított Azure AD-bérlőre.
3. Válassza az Azure Active > **Directory-felhasználók lehetőséget.** **Azure Active Directory**

    Megjelenik az Azure AD-felhasználók listája.

4. Ha felhasználót szeretne hozzáadni a címtárhoz, válassza az **Új felhasználó**lehetőséget. Külső felhasználó hozzáadásához válassza az **Új vendégfelhasználó lehetőséget.**

    ![A "Minden felhasználó" ablaktábla](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Jelölje ki az új felhasználó nevét, majd töltse ki a szükséges mezőket az adott felhasználóhoz.
6. Kattintson a **Létrehozás** gombra.

Az Azure AD-felhasználók kezeléséről a [Felhasználók hozzáadása vagy törlése az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)című témakörben talál.

## <a name="next-steps"></a>További lépések

Sikeresen hozzáadott felhasználókat az Azure FarmBeats-példányhoz. Most, megtanulják, hogyan kell [létrehozni és kezelni gazdaságok](manage-farms-in-azure-farmbeats.md#create-farms).
