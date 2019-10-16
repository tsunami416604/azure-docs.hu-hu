---
title: Használati beállítások konfigurálása a Azure Lab Services osztályterem Labs-ban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a laborhoz tartozó felhasználók számát, regisztrálhatja őket a laborban, meghatározhatja, hogy hány órát használhatnak a virtuális gép, és így tovább.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324401"
---
# <a name="add-and-manage-lab-users"></a>Labor-felhasználók hozzáadása és kezelése
Ez a cikk azt ismerteti, hogyan adhat hozzá felhasználókat a laborhoz, hogyan regisztrálhatja őket a laborban, hogy hány órát használhat a virtuális gép, és így tovább. 


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz

1. Válassza a bal oldali menü **felhasználók** elemét. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás be van kapcsolva, a felhasználók nem regisztrálhatnak a laborba még akkor sem, ha a felhasználó a felhasználók listáján szerepel. Csak a listán szereplő felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Azt is megteheti, hogy kikapcsolja a **hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 
2. Válassza a **felhasználók hozzáadása** lehetőséget az eszköztáron, majd válassza a **Hozzáadás e-mail-címek alapján**lehetőséget. 

    ![Felhasználók hozzáadása gomb](../media/how-to-configure-student-usage/add-users-button.png)
1. A **felhasználók hozzáadása** lapon adja meg a felhasználók e-mail-címeit külön sorokban, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználói e-mail-címek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában megjelenik a felhasználók e-mail-címe és állapota (regisztrált vagy nem). 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása CSV-fájl feltöltésével
Felhasználókat úgy is hozzáadhat, ha feltölt egy CSV-fájlt a felhasználók e-mail-címeivel.

1. Hozzon létre egy CSV-fájlt a felhasználók e-mail-címeivel egy oszlopban.

    ![CSV-fájl felhasználókkal](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. A labor **felhasználók** lapján kattintson a **felhasználók hozzáadása** elemre az eszköztáron, majd válassza a CSV-fájl **feltöltése**lehetőséget.

    ![CSV-fájl feltöltése gomb](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Válassza ki a CSV-fájlt a felhasználói e-mail-címekkel. Ha a CSV-fájl kiválasztása után a **Megnyitás** lehetőséget választja, a következő **felhasználók hozzáadása** ablak jelenik meg. Az e-mail cím lista a CSV-fájlból származó e-mail-címekkel van kitöltve. 

    ![Felhasználói ablak hozzáadása a CSV-fájlból származó e-mail-címekkel](../media/how-to-configure-student-usage/add-users-window.png)
4. A **felhasználók hozzáadása** ablakban válassza a **Mentés** lehetőséget. 
5. Erősítse meg, hogy a felhasználók listáját látja a felhasználók listájában. 

    ![Hozzáadott felhasználók listája](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Meghívók küldése a felhasználóknak
A következő módszerek egyikével elküldheti a regisztrációs hivatkozást a tanulóknak. Az első módszer azt mutatja be, hogyan küldhet e-maileket a tanulóknak a regisztrációs hivatkozással és egy opcionális üzenettel. A második módszer azt mutatja be, hogyan kérheti le a másokkal megosztható regisztrációs hivatkozást a kívánt módon. 

Ha a **hozzáférés korlátozása** engedélyezve van a laborban, csak a felhasználók listájában lévő felhasználók használhatják a regisztrációs hivatkozást a laborba való regisztrációhoz. Ez a beállítás alapértelmezés szerint engedélyezve van. 

### <a name="invite-all-users"></a>Minden felhasználó meghívása

1. Váltson a labor **Users (felhasználók** ) lapjára. 
2. Válassza az **összes meghívása** lehetőséget az eszköztáron. 
3. Adjon meg egy **üzenetet** a felhasználóknak. Ez egy választható lépés.
4. Ezután válassza a **Küldés**lehetőséget.

    ![Minden felhasználó meghívása](../media/how-to-configure-student-usage/invite-all.png)

    A művelet állapota a **felhasználók** lista **Meghívási** oszlopában jelenik meg. A meghívó e-mail-címe tartalmazza azt a regisztrációs hivatkozást, amelyet a felhasználók a laborba való regisztráláshoz használhatnak. 

### <a name="invite-selected-users"></a>Kiválasztott felhasználók meghívása

1. Válasszon ki egy felhasználót vagy több felhasználót a listában. 
2. Ezután válassza ki a kijelölt sorban látható **boríték** ikont (vagy) az eszköztáron válassza a **meghívás** lehetőséget. 

    ![Kiválasztott felhasználók meghívása](../media/how-to-configure-student-usage/invite-selected-users.png)
3. A **Meghívás küldése e-mailben** ablakban adjon meg egy opcionális **üzenetet**, majd válassza a **Küldés**lehetőséget. 

    ![E-mail küldése a kiválasztott felhasználóknak](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    A művelet állapota a **felhasználók** lista **Meghívási** oszlopában jelenik meg. A meghívó e-mail-címe tartalmazza azt a regisztrációs hivatkozást, amelyet a felhasználók a laborba való regisztráláshoz használhatnak.

1. Váltson a **felhasználók** nézetre, ha már nincs a lapon. 

## <a name="get-registration-link"></a>Regisztrációs hivatkozás beolvasása
A regisztrációs hivatkozást a portálról is lekérheti, és a saját e-mail-ügyfélalkalmazás használatával is elküldheti. 

1. Váltson a **felhasználók** nézetre a bal oldali menüben a **felhasználók** lehetőség kiválasztásával. 
2. Válassza a **... lehetőséget. (három pont)** az eszköztáron, majd válassza a **regisztrációs hivatkozás**lehetőséget.

    ![A diákok regisztrációs hivatkozása](../media/how-to-configure-student-usage/registration-link-button.png)
1. A **felhasználó regisztrálása** párbeszédpanelen válassza a **Másolás** gombot. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/how-to-configure-student-usage/registration-link.png)
2. A **felhasználó regisztrálása** párbeszédpanelen válassza a **kész**lehetőséget. 
4. Küldje el a **regisztrációs hivatkozást** egy tanulónak, hogy a tanuló regisztráljon az osztályra. 

## <a name="view-users-registered-with-the-lab"></a>A tesztkörnyezetben regisztrált felhasználók megtekintése

A bal oldali menüben válassza a **felhasználók** lehetőséget a laborban regisztrált felhasználók listájának megtekintéséhez. 

![A laborban regisztrált felhasználók listája](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Kvóták beállítása a felhasználók számára
A kvótákat felhasználónként is beállíthatja a következő lépésekkel: 

1. Ha a lap még nem aktív, válassza a bal oldali menüben a **felhasználók** lehetőséget. 
2. Válasszon **kvótát felhasználónként: &lt;number @ no__t-2 óra** az eszköztáron. 
3. A **kvóta felhasználónként** lapon adja meg, hogy hány órát szeretne megadni az egyes felhasználóknak (tanulóknak) az ütemezett osztály időpontján kívül, majd válassza a **Mentés**lehetőséget.

    ![Kvóta felhasználónként](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Ekkor megjelennek a módosult értékek az eszköztáron: **kvóta felhasználónként: &lt;number of hours @ no__t-2**. 

    ![Kvóta felhasználónként – utána](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="set-additional-quota-for-a-specific-user"></a>További kvóta beállítása egy adott felhasználó számára
Beállíthat egy külön kvótát a felhasználó számára. Ehhez kövesse az alábbi lépéseket:

1. **A felhasználók lapon válasszon** ki egy felhasználót (tanulót) a felhasználók listájából.
2. Ezután válassza a **kvóta módosítása** lehetőséget az eszköztáron. 

    ![Kvóta módosítása gomb](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Adja meg a kiválasztott felhasználó vagy felhasználók számára a **további órák** számát, majd válassza az **alkalmaz**lehetőséget. 

    ![További kvóta a felhasználó számára](../media/how-to-configure-student-usage/additional-quota.png)
4. A felhasználó frissített használati adatait a **használati** oszlopban láthatja. 

    ![A felhasználó új használata](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
