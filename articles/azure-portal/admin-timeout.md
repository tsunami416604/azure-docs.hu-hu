---
title: A Azure Portal felhasználói számára a címtár szintű inaktivitás időkorlátjának beállítása | Microsoft Docs
description: A rendszergazdák a munkamenet kijelentkezése előtt a maximális üresjárati időt is kikényszerítik. Az inaktivitás időtúllépési házirendje a címtár szintjén van beállítva.
services: azure-portal
keywords: beállítások, időtúllépés
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79096608"
---
# <a name="set-directory-level-inactivity-timeout"></a>A címtár szintű inaktivitás időkorlátjának beállítása

Az inaktivitás időtúllépési beállítása segít megvédeni az erőforrásokat a jogosulatlan hozzáféréstől, ha a felhasználók elfelejtik a munkaállomás védelmét. Ha egy felhasználó egy ideig inaktív, akkor a Azure Portal-munkamenet automatikusan kijelentkezik. A [globális rendszergazdai szerepkör rendszergazdái](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) a munkamenet kijelentkezése előtt a maximális üresjárati időt is kikényszerítik. Az inaktivitás időtúllépési beállítása a könyvtár szintjén érvényes. További információ a címtárakról: [Active Directory tartományi szolgáltatások Overview (áttekintés](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

## <a name="configure-the-inactive-timeout-setting"></a>Az inaktív időtúllépési beállítás konfigurálása

Ha Ön globális rendszergazda, és a Azure Portal összes felhasználójának üresjárati időtúllépési beállítását szeretné kényszeríteni, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A globális oldal fejlécében válassza a **Beállítások** lehetőséget.
3. Válassza ki a hivatkozás szövegét a **könyvtár szintjének időtúllépése beállításnál**.

    ![A portál beállításainak megjelenítése a hivatkozás szövegének kiemelésével](./media/admin-timeout/settings.png)

4. Megnyílik egy új lap. A **könyvtár szintjének inaktivitásának beállítása** lapon jelölje be **a címtár szintjének üresjárati időkorlátjának engedélyezése a Azure Portal** számára jelölőnégyzetet a beállítás bekapcsolásához.
5. Ezután adja meg az **órákat** és a **perceket** azon maximális időtartamig, ameddig a felhasználó tétlen lehet, mielőtt a munkamenet automatikusan kijelentkezett.
6. Kattintson az **Alkalmaz** gombra.

    ![A címtár-szintű inaktivitás időtúllépésének beállítására szolgáló lap képernyőképe](./media/admin-timeout/configure.png)

Annak ellenőrzéséhez, hogy az inaktivitás időtúllépési szabályzata helyesen van-e beállítva, válassza az **értesítések** elemet a globális oldal fejlécében. Ellenőrizze, hogy megjelenik-e a sikeres értesítések listája.

  ![A címtár szintű inaktivitás időkorlátjának sikeres értesítési üzenetét bemutató képernyőkép](./media/admin-timeout/confirmation.png)

A beállítás érvénybe lép az új munkamenetek esetében. Azonnal nem lesz érvényes a már bejelentkezett felhasználókra.

> [!NOTE]
> Ha egy globális rendszergazda konfigurált egy címtár-szintű időtúllépési beállítást, a felhasználók felülbírálják a szabályzatot, és saját inaktív kijelentkezési időtartamot állíthatnak be. A felhasználónak azonban olyan időintervallumot kell választania, amely kisebb, mint a globális rendszergazda által a címtár szintjén beállított érték.
>

## <a name="next-steps"></a>További lépések

* [Az Azure Portal-beállítások megadása](set-preferences.md)
* [Felhasználói beállítások exportálása vagy törlése](azure-portal-export-delete-settings.md)
* [Kontrasztos megjelenítés bekapcsolása és téma módosítása](azure-portal-change-theme-high-contrast.md)
