---
title: Csoport lejárati szabályzatának rövid útmutatója – Azure AD | Microsoft Docs
description: Az Office 365-csoportok elévülési ideje – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b6e4f521568c89e415e6e9fa621175052cf34d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026902"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Rövid útmutató: Az Office 365-csoportok elévülési idejének beállítása az Azure Active Directoryban

Ennek a rövid útmutatónak a segítségével beállíthatja az Office 365-csoportjainak elévülési szabályzatát. Ha a felhasználók beállíthatják a saját csoportjaikat, megsokszorozódhatnak a nem használt csoportok. A nem használt csoportok kezelésének egy módja, ha elévülési időt állít be hozzájuk, ezzel csökkentve a csoportok manuális törléséhez szükséges karbantartási időt.

Az elévülési szabályzat nem bonyolult:

- A felhasználói tevékenységgel rendelkező csoportok automatikusan megújulnak a közeljövőben
- A csoporttulajdonosok értesítést kapnak, hogy újítsák meg az elévülő csoportjukat
- A meg nem újított csoport törlődik
- A törölt Office 365-csoportot a csoporttulajdonosok vagy az Azure AD-rendszergazdák 30 napig még visszaállíthatják.

> [!NOTE]
> A csoportok mostantól az Azure AD Intelligence használatával automatikusan megújulnak, függetlenül attól, hogy a legutóbbi használatban vannak-e. Ez a megújítási döntés az Office 365-szolgáltatásokban, például az Outlook, a SharePoint, a Teams, a Yammer és egyebek területén lévő csoportok felhasználói tevékenységén alapul.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisite"></a>Előfeltétel

 A csoport lejáratának beállításához szükséges legkevésbé Kiemelt szerepkör a szervezet felhasználói rendszergazdája.

## <a name="turn-on-user-creation-for-groups"></a>Felhasználói létrehozás bekapcsolása a csoportokhoz

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal.

2. Válassza a **Csoportok**, majd az **Általános** elemet.
  
   ![Önkiszolgáló csoport beállításai lap](./media/groups-quickstart-expiration/self-service-settings.png)

3. Állítsa **A felhasználók létrehozhatnak Office 365-csoportokat** elemet **Igen** értékűre.

4. Ha elkészült, a csoportbeállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki **Azure Active Directory** > **csoportok** > **lejáratát** a lejárat beállításainak megnyitásához.
  
   ![A csoport lejárati beállításainak lapja](./media/groups-quickstart-expiration/expiration-settings.png)

2. Állítsa be az elévülési intervallumot. Válasszon ki egy előre beállított értéket, vagy adjon meg egy 31 napon túli egyéni értéket. 

3. Adjon meg egy e-mail-címet, ahova a rendszer az elévülési értesítéseket küldheti, ha egy csoportnak nincs tulajdonosa.

4. Ebben az útmutatóban állítsa be az **Elévülési idő engedélyezése ezekhez az Office 365-csoportokhoz** elemet **Mind** értékűre.

5. Ha elkészült, az elévülési beállítások mentéséhez kattintson a **Mentés** gombra.

Ennyi az egész! Ebben a rövid útmutatóban sikeresen beállította az elévülési szabályzatot a kijelölt Office 365-csoportokhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="to-remove-the-expiration-policy"></a>A lejárati szabályzat eltávolítása

1. Győződjön meg arról, hogy be van jelentkezve az [Azure Portalra](https://portal.azure.com) a bérlő globális rendszergazdai fiókjával.
2. Válassza az **Azure Active Directory** > **Csoportok** > **Elévülés** elemet.
3. Állítsa az **Elévülési idő engedélyezése ezekhez az Office 365-csoportokhoz** elemet **Egyik sem** értékűre.

### <a name="to-turn-off-user-creation-for-groups"></a>A csoportok felhasználó általi létrehozásának kikapcsolása

1. Válassza az **Azure Active Directory** > **Csoportok** > **Általános** elemet. 
2. Állítsa be **A felhasználók létrehozhatnak Office 365-csoportokat az Azure-portálokon** elemet **Nem**értékűre.

## <a name="next-steps"></a>Következő lépések

További információ a Lejáratról, beleértve a PowerShell-utasításokat és a technikai korlátozásokat, lásd a következő cikket:

> [!div class="nextstepaction"]
> [Elévülési szabályzat PowerShell](groups-lifecycle.md)
