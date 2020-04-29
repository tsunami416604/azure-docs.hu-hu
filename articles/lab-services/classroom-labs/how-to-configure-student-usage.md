---
title: Használati beállítások konfigurálása a tanterem Labs-ban Azure Lab Services
description: Megtudhatja, hogyan konfigurálhatja a tanulók számát a laborban, regisztrálhatja őket a laborban, meghatározhatja, hogy hány órát használhatnak a virtuális gép, és így tovább.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159455"
---
# <a name="add-and-manage-lab-users"></a>Tesztkörnyezeti felhasználók létrehozása és felügyelete

Ez a cikk azt ismerteti, hogyan lehet tanuló felhasználókat felvenni egy laborba, regisztrálni őket a laborban, szabályozni a virtuális gépet (VM) használó további órák számát, és így tovább. 

## <a name="add-users-to-a-lab"></a>Felhasználók hozzáadása laborhoz

Ebben a szakaszban a tanulókat manuálisan vagy egy CSV-fájl feltöltésével adja hozzá a laborhoz. Tegye a következőket:

1. A bal oldali ablaktáblán válassza a **felhasználók**lehetőséget. 

    Alapértelmezés szerint a **hozzáférés korlátozása** beállítás be van kapcsolva, és ha a felhasználók listáján szerepelnek, a tanulók nem regisztrálhatnak a laborba, még akkor sem, ha regisztrációs hivatkozással rendelkeznek. Csak a felsorolt felhasználók regisztrálhatnak a laborba az Ön által küldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Azt is megteheti, hogy kikapcsolja a **hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a tanulók számára, hogy regisztráljanak a laborban, amennyiben rendelkeznek a regisztrációs hivatkozással. 

1. A **felhasználók** ablaktábla tetején válassza a **felhasználók hozzáadása**lehetőséget, majd válassza a **Hozzáadás e-mail-cím alapján**lehetőséget. 

    ![A "felhasználók hozzáadása" gomb](../media/how-to-configure-student-usage/add-users-button.png)

1. A **felhasználók hozzáadása** panelen adja meg a tanulók e-mail-címeit külön vonalakon, vagy egyetlen sorban pontosvesszővel elválasztva. 

    ![Felhasználók e-mail címeinek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Kattintson a **Mentés** gombra. 

    A lista megjeleníti az aktuális felhasználók e-mail-címeit és állapotát, függetlenül attól, hogy regisztrálva vannak-e a laborban. 

    ![Felhasználók listája](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Miután a tanulók regisztrálva lettek a laborban, a lista megjeleníti a nevüket. A listában megjelenő név a Azure Active Directory tanulóinak vezetékneve és vezetékneve alapján jön létre. 

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása CSV-fájl feltöltésével

Hozzáadhat felhasználókat úgy is, hogy feltölt egy CSV-fájlt, amely tartalmazza az e-mail-címüket.

1. A Microsoft Excelben hozzon létre egy CSV-fájlt, amely egy oszlopban a tanulók e-mail-címeit listázza.

    ![CSV-fájlban lévő felhasználók listája](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. A **felhasználók** ablaktábla tetején válassza a **felhasználók hozzáadása**lehetőséget, majd válassza a CSV-fájl **feltöltése**lehetőséget.

    ![A "CSV feltöltése" gomb](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Válassza ki a diákok e-mail-címeit tartalmazó CSV-fájlt, majd kattintson a **Megnyitás**gombra.

    A **felhasználók hozzáadása** ablakban megjelenik az e-mail-címek listája a csv-fájlból. 

    ![A "felhasználók hozzáadása" ablak a CSV-fájlból származó e-mail-címekkel](../media/how-to-configure-student-usage/add-users-window.png)

1. Kattintson a **Mentés** gombra. 

1. A **felhasználók** ablaktáblán tekintse meg a felvett diákok listáját. 

    ![A "felhasználók" ablaktáblán felvett felhasználók listája](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Meghívók küldése a felhasználóknak

Az új felhasználóknak szóló regisztrációs hivatkozás elküldéséhez használja az alábbi módszerek egyikét. 

Ha a **hozzáférés korlátozása** beállítás engedélyezve van a laborban, csak a felsorolt felhasználók használhatják a regisztrációs hivatkozást a laborba való regisztrációhoz. A beállítás alapértelmezés szerint engedélyezett. 

### <a name="invite-all-users"></a>Minden felhasználó meghívása

Ez a módszer azt mutatja be, hogyan küldhet e-mailt egy regisztrációs hivatkozással, és egy opcionális üzenetet az összes felsorolt tanulónak.

1. A **felhasználók** ablaktáblán válassza az **összes meghívása**lehetőséget. 

    ![Az "összes meghívása" gomb](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. A **Meghívás küldése e-mailben** ablakban adjon meg egy opcionális üzenetet, majd válassza a **Küldés**lehetőséget. 

    Az e-mail automatikusan tartalmazza a regisztrációs hivatkozást. A regisztrációs hivatkozás külön beszerzéséhez és mentéséhez válassza a **felhasználók** ablaktábla tetején található három pontot (**..**.), majd válassza a **regisztrációs hivatkozás**lehetőséget. 

    ![A "regisztrációs hivatkozás küldése e-mailben" ablak](../media/tutorial-setup-classroom-lab/send-email.png)

    A **felhasználók** lista **Meghívási** oszlopa megjeleníti az egyes hozzáadott felhasználók Meghívási állapotát. Az állapotnak a **Küldés** gombra kell váltania, majd el kell **küldenie \<a dátumot>**. 

### <a name="invite-selected-users"></a>Kiválasztott felhasználók meghívása

Ez a módszer azt mutatja be, hogyan hívhat meg csak bizonyos tanulókat, és hogyan szerezhet be olyan regisztrációs hivatkozást, amelyet másokkal is megoszthat.

1. A **felhasználók** ablaktáblán válasszon ki egy tanulót vagy több tanulót a listában. 

1. A kiválasztott diák sorában válassza a **boríték** ikont, vagy az eszköztáron válassza a **meghívás**lehetőséget. 

    ![Kiválasztott felhasználók meghívása](../media/how-to-configure-student-usage/invite-selected-users.png)

1. A **Meghívás küldése e-mailben** ablakban adjon meg egy opcionális **üzenetet**, majd válassza a **Küldés**lehetőséget. 

    ![E-mail küldése a kiválasztott felhasználóknak](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    A **felhasználók** ablaktábla a tábla **meghívás** oszlopában jeleníti meg a művelet állapotát. A meghívó e-mail-címe tartalmazza azt a regisztrációs hivatkozást, amelyet a tanulók a laborban használhatnak a regisztráláshoz.

## <a name="get-the-registration-link"></a>Regisztrációs hivatkozás beszerzése

Ebben a szakaszban a portálról kérheti le a regisztrációs hivatkozást, és elküldheti azt a saját e-mail alkalmazásával. 

1. A **felhasználók** ablaktáblán válassza a **regisztrációs hivatkozás**lehetőséget.

    ![A diákok regisztrációs hivatkozása](../media/how-to-configure-student-usage/registration-link-button.png)

1. A **felhasználó regisztrálása** ablakban válassza a **Másolás**lehetőséget, majd válassza a **kész**lehetőséget. 

    ![A "felhasználói regisztráció" ablak](../media/how-to-configure-student-usage/registration-link.png)

    A rendszer a vágólapra másolja a hivatkozást. 
    
1. Az e-mail-alkalmazásban illessze be a regisztrációs hivatkozást, majd küldje el az e-mailt egy tanulónak, hogy a tanuló regisztráljon az osztályra. 

## <a name="view-registered-users"></a>Regisztrált felhasználók megtekintése

1. Lépjen a [Azure Lab Services](https://labs.azure.com) webhelyére. 
1. Válassza a **Bejelentkezés**lehetőséget, majd adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is.
1. A **saját laborok** oldalon válassza ki azt a labort, amelynek a felhasználását nyomon szeretné követni. 
1. A bal oldali ablaktáblán válassza a **felhasználók**lehetőséget, vagy válassza a **felhasználók** csempét. 

    A **felhasználók** ablaktáblán megjelenik azoknak a tanulóknak a listája, akik regisztrálva vannak a laborban.  

    ![Regisztrált felhasználók listája](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Kvóták beállítása a felhasználók számára

Az egyes tanulók számára a következő módon állíthat be egy órás kvótát: 

1. A **felhasználók** ablaktáblán válassza a **kvóta felhasználónként: \<szám> óra** lehetőséget az eszköztáron. 
1. A **kvóta felhasználónként** ablakban adja meg, hogy hány órát szeretne adni az egyes tanulóknak az ütemezett osztály időpontján kívül, majd válassza a **Mentés**lehetőséget.

    ![A "kvóta felhasználónként" ablak](../media/how-to-configure-student-usage/quota-per-user.png)    

    A módosított értékek mostantól a **kvóta felhasználónként: \<órák száma>** gomb az eszköztáron és a felhasználók listájában jelennek meg, ahogy az itt látható:

    ![Kvóta órája felhasználónként](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a tanulók számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="set-additional-quotas-for-specific-users"></a>További kvóták beállítása adott felhasználók számára

Bizonyos tanulók számára megadhat kvótákat az előző szakaszban szereplő összes felhasználóhoz beállított általános kvótán kívül. Ha például oktatóként állítja be a kvótát az összes tanuló számára 10 órára, és egy adott diák esetében 5 óra további kvótát állít be, a tanuló 15 (10 + 5) órányi kvótát kap. Ha később módosítja a közös kvótát, mondjuk 15, a tanuló 20 (15 + 5) kvótát kap. Ne feledje, hogy ez az általános kvóta kívül esik az ütemezett időponton. Az az idő, ameddig a tanuló egy laboratóriumi virtuális gépen az ütemezett időpontra költ, nem számít bele a kvótába. 

További kvóták beállításához tegye a következőket:

1. A **felhasználók** ablaktáblán válasszon ki egy tanulót a listából, majd válassza a **kvóta módosítása** lehetőséget az eszköztáron. 

    ![A "kvóta módosítása" gomb](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. A **kiválasztott felhasználó vagy felhasználók \<e-mail-címére vonatkozó kvóta módosítása>** adja meg a kiválasztott tanuló vagy tanulók számára biztosítani kívánt további labor-órák számát, majd válassza az **alkalmaz**lehetőséget. 

    ![A "kvóta módosítása..." ablak](../media/how-to-configure-student-usage/additional-quota.png)

    A **használat** oszlopban látható a kiválasztott tanulók frissített kvótája. 

    ![A felhasználó új használata](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Tanulói fiókok

Ha tanulókat szeretne felvenni egy osztályterem laborba, használja az e-mail-fiókjait. A diákok a következő típusú e-mail-fiókokkal rendelkezhetnek:

- A Student-féle e-mail-fiók, amelyet az egyetemi Azure Active Directory Office 365-példánya biztosít. 
- Egy Microsoft-tartományi e-mail-fiók, például a *Outlook.com*, a *hotmail.com*, a *MSN.com*vagy a *Live.com*.
- Nem a Microsofttól származó e-mail-fiók, például egy Yahoo! által biztosított vagy a Google. Az ilyen típusú fiókoknak azonban Microsoft-fiókhoz kell kapcsolódniuk.
- Egy GitHub-fiók. Ennek a fióknak egy Microsoft-fiók-vel kell összekapcsolnia.

### <a name="use-a-non-microsoft-email-account"></a>Nem a Microsofttól származó e-mail-fiók használata
A tanulók a nem a Microsofttól származó e-mail-fiókokkal regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba.  A regisztrációhoz azonban először létre kell hoznia egy Microsoft-fiók, amely a nem a Microsoft e-mail-címéhez van csatolva.

Előfordulhat, hogy számos tanuló már rendelkezik egy Microsoft-fiók, amely nem a Microsoft e-mail-címéhez van csatolva. Például a tanulóknak már van Microsoft-fiókuk, ha az e-mail-címüket más Microsoft-termékekkel vagy-szolgáltatásokkal (például Office, Skype, OneDrive vagy Windows) használták.  

Ha a tanulók a regisztrációs hivatkozásra kattintva bejelentkeznek egy osztályterembe, a rendszer kéri az e-mail-címük és jelszavuk megadását. Azok a tanulók, akik nem a Microsoft-fiókhoz kapcsolódó nem Microsoft-fiókkal próbálnak bejelentkezni, a következő hibaüzenetet kapják: 

![Bejelentkezéskor megjelenő hibaüzenet](../media/how-to-configure-student-usage/cant-find-account.png)

A diákoknak itt egy hivatkozása van a [Microsoft-fiókra való feliratkozáshoz](http://signup.live.com).  

> [!IMPORTANT]
> Ha a tanulók bejelentkeznek egy osztályterem laborba, nem kapnak lehetőséget a Microsoft-fiók létrehozására. Ezért javasoljuk, hogy ezt a regisztrációs hivatkozást, http://signup.live.coma nem Microsoft-fiókokat használó tanulók számára elküldött tantermi labor regisztrációs e-mailben adja meg.

### <a name="use-a-github-account"></a>GitHub-fiók használata
A tanulók egy meglévő GitHub-fiókkal is regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba. Ha már rendelkezik egy Microsoft-fiók a GitHub-fiókjával, a tanulók bejelentkezhetnek, és megadhatják a jelszavukat az előző szakaszban látható módon. 

Ha még nem csatolták GitHub-fiókját egy Microsoft-fiókhoz, a következőket teheti:

1. Válassza ki a **bejelentkezési beállítások** hivatkozást, ahogy az itt látható:

    ![A "bejelentkezési beállítások" hivatkozás](../media/how-to-configure-student-usage/signin-options.png)

1. A **bejelentkezési beállítások** ablakban válassza a **Bejelentkezés a githubkal**lehetőséget.

    ![A "Bejelentkezés a GitHubtal" hivatkozás](../media/how-to-configure-student-usage/signin-github.png)

    Ekkor a tanulók a GitHub-fiókhoz kapcsolódó Microsoft-fiók hozhatnak létre. A csatolás automatikusan megtörténik, amikor a **tovább**gombra kattintanak. Ezután azonnal bejelentkeznek és csatlakoznak az osztályterem laborhoz.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Felhasználók listájának exportálása CSV-fájlba

1. Nyissa meg a **felhasználók** ablaktáblát.
1. Az eszköztáron válassza a három pontot (**..**.), majd válassza a **CSV exportálása**lehetőséget. 

    ![A "CSV exportálása" gomb](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- Rendszergazdáknak: [labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- Labor tulajdonosainak: [Labs létrehozása és kezelése](how-to-manage-classroom-labs.md) , [sablonok beállítása és közzététele](how-to-create-manage-template.md)
- Labor-felhasználók számára: [Access tanterem Labs](how-to-use-classroom-lab.md)
