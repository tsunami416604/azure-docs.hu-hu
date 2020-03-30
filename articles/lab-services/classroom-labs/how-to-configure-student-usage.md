---
title: Használati beállítások konfigurálása az Azure Lab Services tantermi laboratóriumaiban
description: Ismerje meg, hogyan konfigurálhatja a diákok számát egy labor, kap őket regisztrált a laborban, szabályozhatja, hogy hány órát használhatják a virtuális gép, és így tovább.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159455"
---
# <a name="add-and-manage-lab-users"></a>Tesztkörnyezeti felhasználók létrehozása és felügyelete

Ez a cikk ismerteti, hogyan adhat hozzá tanuló felhasználókat egy laborba, regisztrálja őket a laborban, szabályozhatja, hogy hány további órákat használhatják a virtuális gép (VM), és így tovább. 

## <a name="add-users-to-a-lab"></a>Felhasználók hozzáadása laborhoz

Ebben a szakaszban manuálisan vagy CSV-fájl feltöltésével adhat hozzá diákokat egy laborhoz. Tegye a következőket:

1. A bal oldali ablaktáblában válassza a **Felhasználók**lehetőséget. 

    Alapértelmezés szerint a **Hozzáférés korlátozása** beállítás be van kapcsolva, és ha csak a felhasználók listájában szerepelnek, a diákok nem regisztrálhatnak a tesztkörnyezetben, még akkor sem, ha rendelkeznek regisztrációs hivatkozással. Csak a felsorolt felhasználók regisztrálhatnak a tesztkörnyezetben az elküldött regisztrációs hivatkozás használatával. Ebben az eljárásban felhasználókat vesz fel a listára. Másik lehetőségként kikapcsolhatja **a Hozzáférés korlátozása**lehetőséget, amely lehetővé teszi a diákok számára, hogy regisztráljanak a laborban, feltéve, hogy rendelkeznek a regisztrációs hivatkozással. 

1. A **Felhasználók** ablaktábla tetején válassza a **Felhasználók hozzáadása**lehetőséget, majd a **Hozzáadás e-mail cím szerint**lehetőséget. 

    ![A "Felhasználók hozzáadása" gomb](../media/how-to-configure-student-usage/add-users-button.png)

1. A **Felhasználók hozzáadása** ablaktáblán adja meg a diákok e-mail címét külön sorokban vagy pontosvesszővel elválasztott egysorban. 

    ![Felhasználók e-mail címeinek hozzáadása](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Kattintson a **Mentés** gombra. 

    A lista megjeleníti az aktuális felhasználók e-mail címét és állapotát, függetlenül attól, hogy regisztrálva vannak-e a tesztkörnyezetben vagy sem. 

    ![Felhasználók listája](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Miután a diákok regisztráltak a laborban, a lista megjeleníti a nevüket. A listában látható név az Azure Active Directoryban a diákok vezeték- és keresztnevei alapján épül fel. 

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása CSV-fájl feltöltésével

A felhasználókat az e-mail címüket tartalmazó CSV-fájl feltöltésével is hozzáadhatja.

1. A Microsoft Excelben hozzon létre egy CSV-fájlt, amely egy oszlopban sorolja fel a diákok e-mail címeit.

    ![CsV-fájlban lévő felhasználók listája](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. A **Felhasználók** ablaktábla tetején válassza a **Felhasználók hozzáadása**lehetőséget, majd a **CSV feltöltése**lehetőséget.

    ![A "CSV feltöltése" gomb](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Jelölje ki a diákok e-mail címeit tartalmazó CSV-fájlt, majd válassza a **Megnyitás**gombot.

    A **Felhasználók hozzáadása** ablak ban a CSV-fájlból származó e-mail címlista jelenik meg. 

    ![A "Felhasználók hozzáadása" ablak a CSV-fájlból származó e-mail címekkel](../media/how-to-configure-student-usage/add-users-window.png)

1. Kattintson a **Mentés** gombra. 

1. A **Felhasználók** ablaktáblán tekintse meg a hozzáadott diákok listáját. 

    ![A hozzáadott felhasználók listája a "Felhasználók" ablaktáblán](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Meghívók küldése a felhasználóknak

Ha regisztrációs hivatkozást szeretne küldeni az új felhasználóknak, használja az alábbi módszerek egyikét. 

Ha a **hozzáférés korlátozása** beállítás engedélyezve van a tesztkörnyezetben, csak a felsorolt felhasználók használhatják a regisztrációs hivatkozást a tesztkörnyezetbe való regisztrációhoz. A beállítás alapértelmezés szerint engedélyezett. 

### <a name="invite-all-users"></a>Az összes felhasználó meghívása

Ez a módszer bemutatja, hogyan küldhet e-mailt egy regisztrációs linkkel és egy választható üzenetet az összes felsorolt diáknak.

1. A **Felhasználók** ablaktáblán válassza az **Összes meghívása**lehetőséget. 

    ![A "Mindenki meghívása" gomb](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. A **Meghívó küldése e-mailben** ablakban írjon be egy nem kötelező üzenetet, majd válassza **a Küldés lehetőséget.** 

    Az e-mail automatikusan tartalmazza a regisztrációs linket. A regisztrációs hivatkozás külön-külön történő beírásához és mentéséhez jelölje ki a három pontot (**...**) a **Felhasználók** ablaktábla tetején, majd a Regisztrációs **hivatkozás t**. 

    ![A "Regisztrációs hivatkozás küldése e-mailben" ablak](../media/tutorial-setup-classroom-lab/send-email.png)

    A **Felhasználók** lista **Meghívó** oszlopa minden hozzáadott felhasználó meghívási állapotát megjeleníti. Az állapotnak **Küldés,** majd **Elküldés \<dátuma>** lesz. 

### <a name="invite-selected-users"></a>Kijelölt felhasználók meghívása

Ez a módszer megmutatja, hogyan hívhat szanaát csak bizonyos diákokra, és hogyan kaphat egy regisztrációs linket, amelyet megoszthat másokkal.

1. A **Felhasználók** ablaktáblán jelöljön ki egy vagy több tanulót a listában. 

1. A kijelölt tanuló sorában jelölje ki a **boríték** ikont, vagy az eszköztáron válassza a **Meghívás lehetőséget.** 

    ![Kijelölt felhasználók meghívása](../media/how-to-configure-student-usage/invite-selected-users.png)

1. A **Meghívó küldése e-mailben** ablakban írjon be egy nem kötelező **üzenetet**, majd válassza **a Küldés lehetőséget.** 

    ![E-mail küldése a kijelölt felhasználóknak](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    A **Felhasználók** ablaktábla a művelet állapotát jeleníti meg a tábla **Meghívó** oszlopában. A meghívó e-mail tartalmazza a regisztrációs linket, amelyet a diákok a laborban való regisztrációhoz használhatnak.

## <a name="get-the-registration-link"></a>A regisztrációs hivatkozás beszereznie

Ebben a szakaszban a regisztrációs linket a portálról szerezheti be, és elküldheti a saját e-mail alkalmazásából. 

1. A **Felhasználók** ablaktáblán válassza a **Regisztrációs hivatkozás lehetőséget.**

    ![A diákok regisztrációs hivatkozása](../media/how-to-configure-student-usage/registration-link-button.png)

1. A **Felhasználó regisztrációja** ablakban válassza a **Másolás**lehetőséget, majd a **Kész**lehetőséget. 

    ![A "Felhasználói regisztráció" ablak](../media/how-to-configure-student-usage/registration-link.png)

    A rendszer a vágólapra másolja a hivatkozást. 
    
1. Az e-mail alkalmazásban illessze be a regisztrációs hivatkozást, majd küldje el az e-mailt egy tanulónak, hogy a tanuló regisztrálhasson az osztályra. 

## <a name="view-registered-users"></a>Regisztrált felhasználók megtekintése

1. Nyissa meg az [Azure Lab Services](https://labs.azure.com) webhelyét. 
1. Válassza **a Bejelentkezés**lehetőséget, majd adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is.
1. A **Saját laborok** lapon válassza ki azt a tesztkörnyezetet, amelynek használatát nyomon szeretné követni. 
1. A bal oldali ablaktáblában válassza a **Felhasználók**lehetőséget, vagy válassza a **Felhasználók csempét.** 

    A **Felhasználók** ablaktábla megjeleníti azoknak a diákoknak a listáját, akik regisztráltak a laborban.  

    ![Regisztrált felhasználók listája](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Kvóták beállítása a felhasználók számára

Minden tanulóhoz az alábbi módon állíthatsz be egy órakvótát: 

1. A **Felhasználók** ablaktáblán válassza a **Felhasználónkénti kvóta: \<az eszköztáron> óra(ok) számát.** 
1. A **Felhasználónkénti kvóta** ablakban adja meg, hogy hány órát szeretne adni minden tanulónak az ütemezett óraidőn kívül, majd válassza a **Mentés gombot.**

    ![A "Kvóta felhasználónként" ablak](../media/how-to-configure-student-usage/quota-per-user.png)    

    A módosított értékek mostantól megjelennek a **felhasználónkénti \<kvótán: az** eszköztáron és a felhasználók listájában>gomb száma, az itt látható módon:

    ![Kvótaórák felhasználónként](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > A [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a diákoknak kiosztott kvótába. A kvóta a diákok által a virtuális gépekre fordított ütemezett órákon kívül töltött időre vonatkozik. 

## <a name="set-additional-quotas-for-specific-users"></a>További kvóták beállítása adott felhasználók számára

Az előző szakaszban az összes felhasználószámára beállított közös kvótákon túl is megadhat kvótákat bizonyos diákok számára. Ha például oktatóként 10 órára állítja be az összes tanuló kvótáját, és további 5 órás kvótát állít be egy adott tanulóra vonatkozóan, akkor az a tanuló 15 (10 + 5) óra kvótát kap. Ha később módosítja a közös kvótát, például 15-re, a tanuló 20 (15 + 5) óra kvótát kap. Ne feledje, hogy ez a teljes kvóta kívül esik az ütemezett időponton. Az az idő, amelyet egy diák egy tesztkörnyezetvirtuális gépen tölt az ütemezett időpontban, nem számít bele ebbe a kvótába. 

További kvóták beállításához tegye a következőket:

1. A **Felhasználók** ablaktáblán jelöljön ki egy tanulót a listából, majd válassza a **Kvóta beállítása lehetőséget** az eszköztáron. 

    ![A "Kvóta beállítása" gomb](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. A **kijelölt felhasználó \<vagy felhasználók e-mail-címének módosítása>** adja meg a kijelölt tanulónak vagy diákoknak megadni kívánt további laborórák számát, majd válassza az **Alkalmaz**lehetőséget. 

    ![A "Kvóta módosítása ..." Ablak](../media/how-to-configure-student-usage/additional-quota.png)

    A **Használat** oszlop a kijelölt diákok frissített kvótáját jeleníti meg. 

    ![Új használat a felhasználó számára](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Diákfiókok

Ha tanulókat szeretne felvenni egy tanterem-laborba, használja az e-mail fiókjukat. A diákok a következő típusú e-mail fiókkal rendelkezhetnek:

- A hallgatói e-mail-fiók, amelyet a universitys Azure Active Directory-példánya biztosít az Office 365-be. 
- Microsoft-tartományos e-mail fiók, például *outlook.com,* *hotmail.com,* *msn.com*vagy *live.com.*
- Nem microsoftos e-mail fiók, például a Yahoo! vagy a Google. Az ilyen típusú fiókokat azonban microsoftos fiókkal kell összekapcsolni.
- Egy GitHub-fiók. Ezt a fiókot Microsoft-fiókkal kell összekapcsolni.

### <a name="use-a-non-microsoft-email-account"></a>Nem Microsoft os e-mail fiók használata
A diákok nem microsoftos e-mail fiókok használatával regisztrálhatnak és bejelentkezhetnek egy tantermi laborba.  A regisztráció hoz azonban először létre egy Microsoft-fiókot, amely a nem microsoftos e-mail címükhöz kapcsolódik.

Előfordulhat, hogy sok diák rendelkezik már olyan Microsoft-fiókkal, amely a nem microsoftos e-mail címéhez kapcsolódik. A diákok például már rendelkeznek Microsoft-fiókkal, ha más Microsoft-termékekkel vagy -szolgáltatásokkal , például az Office-szal, a Skype-pal, a OneDrive-val vagy a Windows-szal használták az e-mail címüket.  

Amikor a diákok a regisztrációs linksegítségével jelentkeznek be egy osztályterembe, a rendszer kéri az e-mail címük és a jelnevük megírására. Azok a diákok, akik nem Microsoft-fiókkal próbálnak bejelentkezni, nem microsoftos fiókkal, a következő hibaüzenetet kapják: 

![Hibaüzenet a bejelentkezéskor](../media/how-to-configure-student-usage/cant-find-account.png)

Az alábbiakban egy linket olvashat, amelyen a diákok [regisztrálhatnak egy Microsoft-fiókra.](http://signup.live.com)  

> [!IMPORTANT]
> Amikor a diákok bejelentkeznek egy tantermi laborba, nem kapnak lehetőséget Microsoft-fiók létrehozására. Ezért azt javasoljuk, hogy ezt a regisztrációs http://signup.live.comhivatkozást, az osztályteremben a labor regisztrációs e-mailben, amelyet nem Microsoft-fiókokat használó diákoknak küld.

### <a name="use-a-github-account"></a>GitHub-fiók használata
A diákok egy meglévő GitHub-fiók használatával is regisztrálhatnak, és bejelentkezhetnek egy tantermi laborba. Ha már rendelkeznek a GitHub-fiókjukhoz kapcsolt Microsoft-fiókkal, a diákok bejelentkezhetnek, és megadhatják a jelszavukat az előző szakaszban látható módon. 

Ha még nem kapcsolták össze GitHub-fiókjukat egy Microsoft-fiókkal, a következőket tehetik:

1. Válassza a **Bejelentkezési beállítások** hivatkozást, ahogy az itt látható:

    ![A "Bejelentkezési beállítások" hivatkozás](../media/how-to-configure-student-usage/signin-options.png)

1. A **Bejelentkezési beállítások ablakban** válassza a **Bejelentkezés a GitHubbal**lehetőséget.

    ![A "Bejelentkezés a GitHubbal" hivatkozás](../media/how-to-configure-student-usage/signin-github.png)

    A kérdésben a diákok létrehoznak egy Microsoft-fiókot, amely a GitHub-fiókjukhoz kapcsolódik. A csatolás automatikusan megtörténik, amikor a **Tovább**lehetőséget választják. Ezután azonnal bejelentkeznek, és csatlakoznak az osztálytermi laborhoz.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Felhasználók listájának exportálása CSV-fájlba

1. Nyissa meg a **Felhasználók** ablaktáblát.
1. Az eszköztáron jelölje ki a három pontot (**...**), majd kattintson **a CSV exportálása gombra.** 

    ![A "CSV exportálása" gomb](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- Rendszergazdáknak: [Laborfiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- Labortulajdonosok nak: [Laborok létrehozása és kezelése,](how-to-manage-classroom-labs.md) sablonok [beállítása és közzététele](how-to-create-manage-template.md)
- Laborfelhasználók számára: [Tantermi laborok elérése](how-to-use-classroom-lab.md)
