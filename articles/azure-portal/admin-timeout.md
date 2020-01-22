---
title: A Azure Portal felhasználói számára a címtár szintű inaktivitás időkorlátjának beállítása | Microsoft Docs
description: A rendszergazdák a munkamenet kijelentkezése előtt a maximális üresjárati időt is kikényszerítik. Az inaktivitás időtúllépési házirendje a címtár szintjén van beállítva.
services: azure-portal
keywords: beállítások, időtúllépés
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87de67eec9171825a1ebff9ab60782fea8836039
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310798"
---
# <a name="set-directory-level-inactivity-timeout"></a>A címtár szintű inaktivitás időkorlátjának beállítása

Az inaktivitás időtúllépési beállítása segít megvédeni az erőforrásokat a jogosulatlan hozzáféréstől, ha a felhasználók elfelejtik a munkaállomás védelmét. Ha egy felhasználó egy ideig inaktív, akkor a Azure Portal-munkamenet automatikusan kijelentkezik. A rendszergazdák a munkamenet kijelentkezése előtt a maximális üresjárati időt is kikényszerítik. Az inaktivitás időtúllépési beállítása a könyvtár szintjén érvényes. További információ a címtárakról: [Active Directory tartományi szolgáltatások Overview (áttekintés](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

## <a name="configure-the-inactive-timeout-setting"></a>Az inaktív időtúllépési beállítás konfigurálása

Ha Ön rendszergazda, és a Azure Portal összes felhasználójának üresjárati időtúllépési beállítását szeretné kényszeríteni, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
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
> Ha a rendszergazda címtár-szintű időtúllépési beállítást konfigurált, a felhasználók felülbírálják a szabályzatot, és saját inaktív kijelentkezési időtartamot állíthatnak be. A felhasználónak azonban olyan időintervallumot kell választania, amely kisebb, mint a könyvtár szintjén beállított érték.
>

## <a name="next-steps"></a>Következő lépések

* [A Azure Portal beállításainak megadása](set-preferences.md)
* [Felhasználói beállítások exportálása vagy törlése](azure-portal-export-delete-settings.md)
* [Kontrasztos megjelenítés bekapcsolása és téma módosítása](azure-portal-change-theme-high-contrast.md)
