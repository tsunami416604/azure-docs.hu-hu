---
title: Használati beállítások konfigurálása a tanterem Labs-ban Azure Lab Services
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 80e8bc47f6e6293d70bbc9fae888abdf5527fe93
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169213"
---
# <a name="add-and-manage-lab-users"></a>Tesztkörnyezeti felhasználók létrehozása és felügyelete
Ez a cikk azt ismerteti, hogyan adhat hozzá felhasználókat a laborhoz, hogyan regisztrálhatja őket a laborban, hogy hány órát használhat a virtuális gép, és így tovább. 


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a laborhoz

1. Válassza a bal oldali menü **felhasználók** elemét. Alapértelmezés szerint a **hozzáférés korlátozása** beállítás engedélyezve van. Ha ez a beállítás be van kapcsolva, a felhasználók nem regisztrálhatnak a laborba még akkor sem, ha a felhasználó a felhasználók listáján szerepel. Csak a listán szereplő felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Azt is megteheti, hogy kikapcsolja a **hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a felhasználók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 
2. Válassza a **felhasználók hozzáadása** lehetőséget az eszköztáron, majd válassza a **Hozzáadás e-mail-címek alapján**lehetőséget. 

    ![Felhasználók hozzáadása gomb](../media/how-to-configure-student-usage/add-users-button.png)
1. A **felhasználók hozzáadása** lapon adja meg a felhasználók e-mail-címeit külön sorokban, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználói e-mail-címek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. A listában megjelenik a felhasználók e-mail-címe és állapota (regisztrált vagy nem). 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

    > [!NOTE]
    > A listában szereplő felhasználók nevét a laborba való regisztráció után fogja látni. A listában megjelenő név a Azure Active Directory felhasználójának első és utolsó neveivel jön létre. 

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

Ha a **hozzáférés korlátozása** engedélyezve van a laborban, csak a felhasználók listájában lévő felhasználók használhatják a regisztrációs hivatkozást a laborba való regisztrációhoz. A beállítás alapértelmezés szerint engedélyezett. 

### <a name="invite-all-users"></a>Minden felhasználó meghívása
1. Váltson a **felhasználók** nézetre, ha már nincs a lapon, és válassza az **összes meghívása** lehetőséget az eszköztáron. 

    ![Tanulók kiválasztása](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. A **Meghívás küldése e-mailben** lapon adjon meg egy opcionális üzenetet, majd válassza a **Küldés**lehetőséget. Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. Ezt a regisztrációs hivatkozást a következő parancs kiválasztásával érheti el: **... (három pont)** az eszköztáron és a **regisztrációs hivatkozáson**. 

    ![Regisztrációs hivatkozás küldése e-mailben](../media/tutorial-setup-classroom-lab/send-email.png)
4. A **meghívás** állapota megjelenik a **felhasználók** listájában. Az állapotnak a **Küldés** gombra kell váltania, majd **\<dátummal kell elküldenie >** . 

    A tanulók osztályhoz való hozzáadásával és a labor használatának felügyeletével kapcsolatos további információkért lásd: [a tanulói használat konfigurálása](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Kiválasztott felhasználók meghívása

1. Válasszon ki egy felhasználót vagy több felhasználót a listában. 
2. Ezután válassza ki a kijelölt sorban látható **boríték** ikont (vagy) az eszköztáron válassza a **meghívás** lehetőséget. 

    ![Kiválasztott felhasználók meghívása](../media/how-to-configure-student-usage/invite-selected-users.png)
3. A **Meghívás küldése e-mailben** ablakban adjon meg egy opcionális **üzenetet**, majd válassza a **Küldés**lehetőséget. 

    ![E-mail küldése a kiválasztott felhasználóknak](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    A művelet állapota a **felhasználók** lista **meghívás** oszlopában látható. A meghívó e-mail-címe tartalmazza azt a regisztrációs hivatkozást, amelyet a felhasználók a laborba való regisztráláshoz használhatnak.

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
2. Válassza ki a **kvóta felhasználónként: \<számot > órát** az eszköztáron. 
3. A **kvóta felhasználónként** lapon adja meg, hogy hány órát szeretne megadni az egyes felhasználóknak (tanulóknak) az ütemezett osztály időpontján kívül, majd válassza a **Mentés**lehetőséget.

    ![Kvóta felhasználónként](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Ekkor megjelennek a módosított értékek az eszköztáron: **kvóta felhasználónként: \<óra >** . 

    ![Kvóta felhasználónként – utána](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="set-additional-quota-for-a-specific-user"></a>További kvóta beállítása egy adott felhasználó számára
Megadhat további kvótákat a felhasználók számára. Ez a kvóta az előző szakaszban szereplő összes felhasználóhoz tartozó közös kvóta mellett is megadható. Ha például (oktatóként) az összes felhasználóra vonatkozóan 10 órára állítja be a kvótát, és egy adott felhasználó számára 5 óra további kvótát állít be, a felhasználók 15 (10 + 5) órányi kvótát kap. Ha később módosítja a közös kvótát, azaz 15, akkor a felhasználó 20 (15 + 5) órányi kvótát kap. Ne feledje, hogy ez az általános kvóta az ütemezett időpontban kívül esik. Az az idő, ameddig a tanuló a labor virtuális gépen a menetrendi idő alatt elkölt, nem számít bele a kvótába. 

Ehhez kövesse az alábbi lépéseket:

1. **A felhasználók lapon válasszon** ki egy felhasználót (tanulót) a felhasználók listájából.
2. Ezután válassza a **kvóta módosítása** lehetőséget az eszköztáron. 

    ![Kvóta módosítása gomb](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Adja meg a kiválasztott felhasználó vagy felhasználók számára a **további órák** számát, majd válassza az **alkalmaz**lehetőséget. 

    ![További kvóta a felhasználó számára](../media/how-to-configure-student-usage/additional-quota.png)
4. A felhasználó frissített használati adatait a **használati** oszlopban láthatja. 

    ![A felhasználó új használata](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Tanulói fiókok
Ha tanulókat szeretne felvenni egy osztályterem laborba, használja az e-mail-fiókjait. A következő típusú e-mail-fiókokat lehet használni:

- Egy tanulói e-mail-fiók, amelyet az egyetemi Office 365 Azure Active Directory (HRE) biztosít. 
- Egy Microsoft e-mail-fiók, például `@outlook.com`, `@hotmail.com`, `@msn.com`vagy `@live.com`.
- Egy nem a Microsofttól származó e-mail-fiók, például a Yahoo vagy a Google által biztosított. Az ilyen típusú fiókoknak azonban Microsoft-fiókhoz kell kapcsolódniuk.
- Egy GitHub-fiók. Ennek a fióknak egy Microsoft-fiók-vel kell összekapcsolnia.

### <a name="using-a-non-microsoft-email-account"></a>Nem a Microsofttól származó e-mail-fiók használata
A tanulók a nem a Microsofttól származó e-mail-fiókokkal regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba.  A regisztrációhoz azonban a tanulóknak először létre kell hozniuk egy Microsoft-fiók, amely nem a Microsoft e-mail-címéhez van csatolva.

Előfordulhat, hogy számos tanuló már rendelkezik egy Microsoft-fiók a nem a Microsoft e-mail-címeihez társítva. Például a tanulóknak már van Microsoft-fiókuk, ha a Microsoft más termékeivel vagy szolgáltatásaival, például az Office-, Skype-, OneDrive-vagy Windows-alkalmazásokkal használták az e-mail-címüket.  

Amikor egy tanuló rákattint a regisztrációs URL-címre, hogy bejelentkezzen egy osztályterembe, a rendszer az e-mail-címük és jelszavuk megadását kéri. Ha a tanuló olyan nem Microsoft-fiókba próbál bejelentkezni, amelyhez nincs Microsoft-fiók társítva, akkor a tanuló a következő hibaüzenetet kapja: 

![Hibaüzenet](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft-fiókra való feliratkozáshoz a tanulóknak [http://signup.live.com](http://signup.live.com)kell megjelenniük.  

> [!IMPORTANT]
> Ha a tanulók bejelentkeznek a tantermi laborba, nem kapnak lehetőséget a Microsoft-fiók létrehozására. Ezért javasoljuk, hogy ezt a regisztrációs hivatkozást a nem Microsoft-fiókokat használó tanulók számára elküldött tantermi labor regisztrációs e-mailbe foglalja bele.

### <a name="using-a-github-account"></a>GitHub-fiók használata
A tanulók egy meglévő GitHub-fiókkal is regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba. Ha a tanuló már rendelkezik egy Microsoft-fiók a GitHub-fiókjához, akkor bejelentkezhet, és megadhatja a jelszavát az előző szakaszban látható módon. Ha még nem csatolták a GitHub-fiókját egy Microsoft-fiókhoz, akkor a **bejelentkezési lehetőségeket**kell választania:

![Bejelentkezési beállítások hivatkozása](../media/how-to-configure-student-usage/signin-options.png)

A **bejelentkezési beállítások** lapon válassza a **Bejelentkezés a githubkal**lehetőséget.

![Bejelentkezés GitHub-hivatkozással](../media/how-to-configure-student-usage/signin-github.png)

Végül a rendszer kéri, hogy hozzon létre egy Microsoft-fiók, amely a GitHub-fiókjához van csatolva. Automatikusan megtörténik, amikor a tanuló kiválasztja a **Next (tovább**) lehetőséget.  Ezután a tanuló azonnal bejelentkezik, és csatlakozik az osztályterem laborhoz.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
