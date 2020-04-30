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
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 616910eda33b3ddc49fa6233ccb3989c5e4214e2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582845"
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

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisite"></a>Előfeltétel

 A csoport lejáratának beállításához szükséges legkevésbé Kiemelt szerepkör a szervezet felhasználói rendszergazdája.

## <a name="turn-on-user-creation-for-groups"></a>Felhasználói létrehozás bekapcsolása a csoportokhoz

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal.

2. Válassza a **Csoportok**, majd az **Általános** elemet.
  
   ![Önkiszolgáló csoport beállításai lap](./media/groups-quickstart-expiration/self-service-settings.png)

3. Állítsa **A felhasználók létrehozhatnak Office 365-csoportokat** elemet **Igen** értékűre.

4. Ha elkészült, a csoportbeállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza a **Azure Active Directory** > **csoportok** > **lejárata** lehetőséget a lejárat beállításainak megnyitásához.
  
   ![A csoport lejárati beállításainak lapja](./media/groups-quickstart-expiration/expiration-settings.png)

2. Állítsa be az elévülési intervallumot. Válasszon ki egy előre beállított értéket, vagy adjon meg egy 31 napon túli egyéni értéket. 

3. Adjon meg egy e-mail-címet, ahova a rendszer az elévülési értesítéseket küldheti, ha egy csoportnak nincs tulajdonosa.

4. Ebben az útmutatóban állítsa be az **Elévülési idő engedélyezése ezekhez az Office 365-csoportokhoz** elemet **Mind** értékűre.

5. Ha elkészült, az elévülési beállítások mentéséhez kattintson a **Mentés** gombra.

Ennyi az egész! Ebben a rövid útmutatóban sikeresen beállította az elévülési szabályzatot a kijelölt Office 365-csoportokhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="to-remove-the-expiration-policy"></a>Az elévülési szabályzat eltávolítása

1. Győződjön meg arról, hogy bejelentkezett a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely az Azure ad-szervezet globális rendszergazdája.
2. Válassza ki **Azure Active Directory** > **csoportok** > **lejáratát**.
3. Állítsa az **Elévülési idő engedélyezése ezekhez az Office 365-csoportokhoz** elemet **Egyik sem** értékűre.

### <a name="to-turn-off-user-creation-for-groups"></a>Felhasználói létrehozás kikapcsolása a csoportokhoz

1. Válassza ki a **Azure Active Directory** > **csoportok** > **általános**elemet. 
2. Állítsa be **A felhasználók létrehozhatnak Office 365-csoportokat az Azure-portálokon** elemet **Nem**értékűre.

## <a name="next-steps"></a>További lépések

További információ a Lejáratról, beleértve a PowerShell-utasításokat és a technikai korlátozásokat, lásd a következő cikket:

> [!div class="nextstepaction"]
> [Elévülési szabályzat PowerShell](groups-lifecycle.md)
