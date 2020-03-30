---
title: Címtárszintű inaktivitás-időmeghosszabbítás beállítása az Azure Portal felhasználói számára | Microsoft dokumentumok
description: A rendszergazdák a munkamenet kijelentkezése előtt kikényszeríthetik a maximális tétlen időt. Az inaktivitás idő-megtagandátházirend a könyvtár szintjén van beállítva.
services: azure-portal
keywords: beállítások, időmeghosszabbítás
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096608"
---
# <a name="set-directory-level-inactivity-timeout"></a>Címtárszintű inaktivitás-időmeghosszabbítás beállítása

Az inaktivitás időelintézési beállítása segít megvédeni az erőforrásokat a jogosulatlan hozzáféréstől, ha a felhasználók elfelejtik biztosítani a munkaállomásukat. Ha egy felhasználó már egy ideje tétlen, az Azure Portal-munkamenet automatikusan kijelentkezik. A globális [rendszergazdai szerepkör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) rendszergazdái a munkamenet kijelentkezése előtt kényszeríthetik a maximális tétlen időt. Az inaktivitás időkitöltési beállítása a címtár szintjén érvényes. A könyvtárakról az Active Directory tartományi szolgáltatások – áttekintés című témakörben olvashat [bővebben.](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

## <a name="configure-the-inactive-timeout-setting"></a>Az inaktív időtúltöltés ibeállítása

Ha Ön globális rendszergazda, és az Azure Portal összes felhasználójára vonatkozóan egy tétlen időtúllépést szeretne érvényesíteni, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a **beállítások lehetőséget** a globális oldalfejlécből.
3. Jelölje be a hivatkozás szövegét **Könyvtárszint-időeltetés konfigurálása**.

    ![Képernyőkép a kijelölt hivatkozásszöveggel rendelkező portálbeállításokról](./media/admin-timeout/settings.png)

4. Megnyílik egy új lap. A **Címtárszint inaktivitás idő-nyitva** állásának konfigurálása lapon válassza **a Címtárszintű tétlen időmegt/ a beállítás bekapcsolásához lehetőség könyvtárszintű tétlen időmeg,** válassza.
5. Ezután adja meg az **Órák** és **percek értéket** arra a maximális időre, amaddig, amíg a felhasználó a munkamenet automatikus kijelentkezése előtt tétlen lehet.
6. Kattintson az **Alkalmaz** gombra.

    ![Képernyőkép a címtárszintű inaktivitás időidejének beállításához](./media/admin-timeout/configure.png)

Annak ellenőrzéséhez, hogy az inaktivitás időszüntetési házirendje megfelelően van-e beállítva, válassza az **Értesítések** lehetőséget a globális oldalfejlécből. Ellenőrizze, hogy szerepel-e sikerességi értesítés a listában.

  ![A címtárszintű inaktivitás időtúllépésének sikeres értesítési üzenetéről készült képernyőkép](./media/admin-timeout/confirmation.png)

A beállítás új munkamenetek esetén lép érvénybe. Nem vonatkozik azonnal azokra a felhasználókra, akik már be vannak jelentkezve.

> [!NOTE]
> Ha egy globális rendszergazda konfigurált egy címtárszintű időtúllépési beállítást, a felhasználók felülbírálhatják a házirendet, és beállíthatják saját inaktív kijelentkezési időtartamukat. A felhasználónak azonban olyan időintervallumot kell választania, amely kisebb, mint amit a globális rendszergazda a címtár szintjén beállított.
>

## <a name="next-steps"></a>További lépések

* [Az Azure Portal-beállítások megadása](set-preferences.md)
* [Felhasználói beállítások exportálása vagy törlése](azure-portal-export-delete-settings.md)
* [Kontrasztos megjelenítés bekapcsolása és téma módosítása](azure-portal-change-theme-high-contrast.md)
