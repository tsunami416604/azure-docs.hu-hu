---
title: Felhasználó hozzáadása laborlétrehozóként az Azure Lab Servicesben
description: Ez a cikk bemutatja, hogyan adhat hozzá egy felhasználót a Lab Creator szerepkör egy tesztkörnyezet-fiók az Azure Lab Services-ben. A tesztkörnyezet alkotói laborokat hozhatnak létre ezen a tesztkörnyezet-fiókon belül.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444770"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Laborkészítők hozzáadása egy tesztkörnyezet-fiókhoz az Azure Lab Servicesben
Ez a cikk bemutatja, hogyan adhat hozzá felhasználókat tesztkörnyezet-alkotókként egy tesztkörnyezet-fiókhoz az Azure Lab Servicesben. Ezek a felhasználások, majd létrehozhat tantermi laborok a laborfiókban. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Microsoft-felhasználói fiók hozzáadása a Lab Creator szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt szeretne adni az oktatóknak arra, hogy laborokat hozzanak létre az osztályaikszámára, adja hozzá őket a **Lab Creator** szerepkörhöz:

1. A **Laborfiók** lapon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson a **+ Szerepkör-hozzárendelés hozzáadása** gombra az eszköztáron. 

    ![Hozzáférés-vezérlés -> Szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **Szerepkör-hozzárendelés hozzáadása** lapon válassza a Lab Creator for Role **(Laborkészítő** **szerepkörhöz**) lehetőséget, jelölje ki a Lab Alkotók szerepkörhöz hozzáadni kívánt felhasználót, és válassza a **Mentés gombot.** 

    ![Laborkészítő hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Ha nem Microsoft-fiókfelhasználót ad hozzá tesztkörnyezet-létrehozóként, tekintse meg a [Nem Microsoft-fiókfelhasználó hozzáadása laborlétrehozóként](#add-a-non-microsoft-account-user-as-a-lab-creator) című szakaszt. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Nem Microsoft-fiókfelhasználó hozzáadása tesztkörnyezet-létrehozóként
Ha tesztkörnyezet-létrehozóként szeretne felhasználót felvenni, használja az e-mail fiókjait. A következő típusú e-mail fiókok használhatók:

- Az egyetem Office 365 Azure Active Directoryja (AAD) által biztosított e-mail fiók. 
- Microsoft-e-mail fiók, `@hotmail.com` `@msn.com`például `@live.com` `@outlook.com`, , , vagy .
- Nem microsoftos e-mail fiók, például a Yahoo vagy a Google által biztosított e-mail fiók. Az ilyen típusú fiókokat azonban microsoftos fiókkal kell összekapcsolni.
- Egy GitHub-fiók. Ezt a fiókot Microsoft-fiókkal kell összekapcsolni.

### <a name="using-a-non-microsoft-email-account"></a>Nem microsoftos e-mail fiók használata
A laborkészítők/oktatók nem microsoftos e-mail fiókok at használhatnak a regisztrációhoz és az osztályterem-laborba való bejelentkezéshez.  A Lab Services portálra való bejelentkezéshez azonban először létre kell hoznia egy Microsoft-fiókot, amely a nem microsoftos e-mail címükhöz kapcsolódik.

Előfordulhat, hogy sok oktató már rendelkezik a nem microsoftos e-mail címéhez kapcsolódó Microsoft-fiókkal. Az oktatók például már rendelkeznek Microsoft-fiókkal, ha az e-mail címüket a Microsoft más termékeivel vagy szolgáltatásaival, például az Office-szal, a Skype-pal, a OneDrive-val vagy a Windows rendszerrel használták.  

Amikor az oktatók bejelentkeznek a Lab Services portálra, a rendszer kéri az e-mail címüket és a jelszavukat. Ha az oktató olyan nem Microsoft-fiókkal próbál bejelentkezni, amelyhez nincs microsoftos fiók csatolva, az oktató a következő hibaüzenetet kapja: 

![Hibaüzenet](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft-fiók regisztrálásához az oktatóknak [http://signup.live.com](http://signup.live.com)a t.  


### <a name="using-a-github-account"></a>GitHub-fiók használata
Az oktatók egy meglévő GitHub-fiók használatával is regisztrálhatnak, és bejelentkezhetnek egy tantermi laborba. Ha az oktató már rendelkezik a GitHub-fiókhoz kapcsolt Microsoft-fiókkal, akkor bejelentkezhet, és megadhatja a jelszavát az előző szakaszban látható módon. Ha még nem kapcsolták össze GitHub-fiókjukat egy Microsoft-fiókkal, válassza a **Bejelentkezési beállítások lehetőséget:**

![Bejelentkezési beállítások hivatkozása](../media/how-to-configure-student-usage/signin-options.png)

A **Bejelentkezési beállítások** lapon válassza a **Bejelentkezés a GitHubbal**lehetőséget.

![Bejelentkezés a GitHub-hivatkozással](../media/how-to-configure-student-usage/signin-github.png)

Végül a rendszer kéri, hogy hozzon létre egy Microsoft-fiókot, amely kapcsolódik a GitHub-fiókhoz. Ez automatikusan megtörténik, amikor az oktató kiválasztja a **Tovább**gombot.  Az oktató ezután azonnal bejelentkezett, és csatlakozik az osztályteremben labor.


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként hozzon létre és kezeljen laborokat](how-to-manage-classroom-labs.md)
- [Labortulajdonosként sablonok beállítása és közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálja és szabályozza a tesztkörnyezet használatát](how-to-configure-student-usage.md)
- [Laborfelhasználóként tantermi laborok elérése](how-to-use-classroom-lab.md)
