---
title: Elévülési szabályzat a rövid útmutató az Office 365-csoportok – Azure Active Directory |} A Microsoft Docs
description: Az Office 365-csoportok elévülési ideje – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01cedadc115496fcf00df986b4ad4b9c5aab5139
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606183"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Gyors útmutató: Állítsa be az Office 365-csoportok az Azure Active Directoryban lejár

Ennek a rövid útmutatónak a segítségével beállíthatja az Office 365-csoportjainak elévülési szabályzatát. Ha a felhasználók beállíthatják a saját csoportjaikat, megsokszorozódhatnak a nem használt csoportok. A nem használt csoportok kezelésének egy módja, ha elévülési időt állít be hozzájuk, ezzel csökkentve a csoportok manuális törléséhez szükséges karbantartási időt.

Az elévülési szabályzat nem bonyolult:

* A csoporttulajdonosok értesítést kapnak, hogy újítsák meg az elévülő csoportjukat
* A meg nem újított csoport törlődik
* A törölt Office 365-csoportot a csoporttulajdonosok vagy az Azure AD-rendszergazdák 30 napig még visszaállíthatják.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisite"></a>Előfeltétel

 Az alacsonyabb szintű szerepkör, csoport lejárati beállításához szükséges a szervezet felhasználói rendszergazdája lesz.

## <a name="turn-on-user-creation-for-groups"></a>Felhasználói létrehozás bekapcsolása a csoportokhoz

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) rendszergazdai felhasználói fiókkal.

2. Válassza a **Csoportok**, majd az **Általános** elemet.
  
   ![Önkiszolgáló csoport-beállítások lap](./media/groups-quickstart-expiration/self-service-settings.png)

3. Állítsa **A felhasználók létrehozhatnak Office 365-csoportokat** elemet **Igen** értékűre.

4. Ha elkészült, a csoportbeállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com)válassza **Azure Active Directory** > **csoportok** > **lejárati** , Nyissa meg a lejárati beállítások.
  
   ![Csoport lejárati beállítások lap](./media/groups-quickstart-expiration/expiration-settings.png)

2. Állítsa be az elévülési intervallumot. Válasszon ki egy előre beállított értéket, vagy adjon meg egy 31 napon túli egyéni értéket. 

3. Adjon meg egy e-mail-címet, ahova a rendszer az elévülési értesítéseket küldheti, ha egy csoportnak nincs tulajdonosa.

4. Ebben az útmutatóban állítsa be az **Elévülési idő engedélyezése ezekhez az Office 365-csoportokhoz** elemet **Mind** értékűre.

5. Ha elkészült, az elévülési beállítások mentéséhez kattintson a **Mentés** gombra.

Ennyi az egész! Ebben a rövid útmutatóban sikeresen beállította az elévülési szabályzatot a kijelölt Office 365-csoportokhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="to-remove-the-expiration-policy"></a>Az elévülési szabályzat eltávolítása

1. Győződjön meg arról, hogy be van jelentkezve az [Azure Portalra](https://portal.azure.com) a bérlő globális rendszergazdai fiókjával.
2. Válassza az **Azure Active Directory** > **Csoportok** > **Elévülés** elemet.
3. Állítsa az **Elévülési idő engedélyezése ezekhez az Office 365-csoportokhoz** elemet **Egyik sem** értékűre.

### <a name="to-turn-off-user-creation-for-groups"></a>Felhasználó létrehozása a csoportok kikapcsolása

1. Válassza az **Azure Active Directory** > **Csoportok** > **Általános** elemet. 
2. Állítsa be **A felhasználók létrehozhatnak Office 365-csoportokat az Azure-portálokon** elemet **Nem**értékűre.

## <a name="next-steps"></a>További lépések

Többek között a PowerShell-utasítások és technikai korlátok miatt lejárati kapcsolatos további információkért lásd a következő cikket:

> [!div class="nextstepaction"]
> [Elévülési szabályzatot PowerShell](groups-lifecycle.md)
