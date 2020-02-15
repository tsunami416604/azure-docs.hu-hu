---
title: Tesztkörnyezet-fiókok konfigurálása a Azure Lab Servicesban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes Lab-fiókot, vagy törölhet egy labor-fiókot Azure Lab Servicesban.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 37a657093fd55ce752095417fe744f83946962db
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210575"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Tesztkörnyezet-fiókok konfigurálása Azure Lab Services 
Azure Lab Services a labor-fiók a felügyelt labor típusok, például a tanterem Labs tárolója. A rendszergazda létrehoz egy Azure Lab Services, és hozzáférést biztosít a labor tulajdonosai számára, akik a fiókban létrehozhatnak laborokat. Ez a cikk bemutatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes tesztkörnyezet fiókját, vagy törölhet egy labor-fiókot.

## <a name="connect-with-a-peer-virtual-network"></a>Kapcsolat egyenrangú virtuális hálózattal
Ha egy virtuális hálózatot egyenrangú hálózatként szeretne csatlakozni a labor virtuális hálózatához, kövesse az alábbi lépéseket:

1. A **labor-fiók** lapon válassza a bal oldali menü **Labs-konfiguráció** elemét.

    ![Labs konfigurációs lapja](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. A **társ virtuális hálózat**esetében válassza az **engedélyezve** vagy a **Letiltva**lehetőséget. Az alapértelmezett érték **le van tiltva**. A társ virtuális hálózat engedélyezéséhez hajtsa végre a következő lépéseket: 
    1. Válassza az **Engedélyezve** lehetőséget.
    2. Válassza ki a **VNet** a legördülő listából. 
3. Válassza az eszköztár **Save** (Mentés) elemét. 

Az ebben a fiókban létrehozott laborok a kiválasztott virtuális hálózathoz csatlakoznak. Hozzáférhetnek a kiválasztott virtuális hálózatban lévő erőforrásokhoz. További információ: [a tesztkörnyezet hálózatának összekötése egy társ virtuális hálózattal Azure Lab Servicesban](how-to-connect-peer-virtual-network.md).

Ha kijelöl egy virtuális hálózatot a **társ virtuális hálózat** mezőhöz, akkor a **tesztkörnyezet-létrehozó engedélyezése a laborban hely** beállítás le van tiltva. Ez azért van, mert a labor-fiókban lévő laboroknak ugyanabban a régióban kell lenniük, mint a társ virtuális hálózatban lévő erőforrásokhoz való kapcsolódáshoz. 

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Címtartomány megadása a virtuális gépek számára a tesztkörnyezetben
A következő eljárás a virtuális gépekhez tartozó címtartomány megadásának lépéseit írja le a laborban. Ha frissíti a korábban megadott tartományt, a módosított címtartomány csak a módosítás után létrehozott virtuális gépekre vonatkozik. 

Íme néhány korlátozás a címtartomány megadásához, amelyet érdemes szem előtt tartani. 

- Az előtagnak kisebbnek vagy egyenlőnek kell lennie, mint 23. 
- Ha egy virtuális hálózat a labor-fiókhoz van társítva, a megadott címtartomány nem lehet átfedésben a társ virtuális hálózatból származó címtartományból.

1. A **labor-fiók** lapon válassza a bal oldali menü **Labs-konfiguráció** elemét.
2. A **címtartomány** mezőben határozza meg a laborban létrehozandó virtuális gépek címtartomány-tartományát. A címtartomány legyen az osztály nélküli tartományok közötti útválasztás (CIDR) jelölése (például: 10.20.0.0/23). A laborban található virtuális gépek ebben a címtartományból lesznek létrehozva.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Címtartomány konfigurálása](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

1. A **labor-fiók** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd kattintson a **+ szerepkör-hozzárendelés hozzáadása** elemre az eszköztáron. 

    ![Access Control – > szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tesztkörnyezet létrehozója** **szerepkört**, válassza ki azt a felhasználót, amelyet hozzá szeretne adni a labor létrehozói szerepkörhöz, majd válassza a **Mentés**lehetőséget. 

    ![Tesztkörnyezet létrehozójának hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Ha nem Microsoft-fiók felhasználót ad hozzá labor creatorként, tekintse meg a [nem Microsoft-fiók felhasználó hozzáadása labor creatorként](#add-a-non-microsoft-account-user-as-a-lab-creator) című szakaszt. 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>A tesztkörnyezet-készítők számára elérhető Piactéri lemezképek meghatározása
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

1. Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A fenti legördülő listában a **Csak az engedélyezettek**/**Csak a letiltottak** lehetőségre kattintva szűrheti a listát, hogy csak az engedélyezett/letiltott rendszerképek jelenjenek meg. 
    
    ![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
        
    - Egyetlen virtuális gépet hoznak létre.
    - A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
    - Nincs szükség külön licenccsomag vásárlására.
2. Egy engedélyezett Marketplace-beli rendszerkép **letiltásának** a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)** , majd a **Rendszerkép letiltása** lehetőségre. 

        ![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek letiltása** lehetőségre. 

        ![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. A letiltott Marketplace-beli rendszerképek **engedélyezésének** a fentiekhez hasonlóan a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)** , majd a **Rendszerkép engedélyezése** lehetőségre. 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek engedélyezése** lehetőségre. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Nem Microsoft-fiók felhasználó hozzáadása labor creatorként
Ha egy felhasználót labor creatorként szeretne felvenni, használja az e-mail-fiókját. A következő típusú e-mail-fiókokat lehet használni:

- Az egyetemi Office 365 Azure Active Directory (HRE) által biztosított e-mail-fiók. 
- Egy Microsoft e-mail-fiók, például `@outlook.com`, `@hotmail.com`, `@msn.com`vagy `@live.com`.
- Egy nem a Microsofttól származó e-mail-fiók, például a Yahoo vagy a Google által biztosított. Az ilyen típusú fiókoknak azonban Microsoft-fiókhoz kell kapcsolódniuk.
- Egy GitHub-fiók. Ennek a fióknak egy Microsoft-fiók-vel kell összekapcsolnia.

### <a name="using-a-non-microsoft-email-account"></a>Nem a Microsofttól származó e-mail-fiók használata
A labor-készítők/oktatók a nem a Microsofttól származó e-mail-fiókokkal regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba.  A labor Services portálra való bejelentkezéshez azonban az oktatóknak először létre kell hozniuk egy Microsoft-fiók, amely nem a Microsoft e-mail-címéhez van csatolva.

Lehet, hogy számos oktató már rendelkezik egy Microsoft-fiók a nem Microsoft-e-mail-címeihez. Például az oktatóknak már van Microsoft-fiókuk, ha az e-mail-címüket a Microsoft más termékeivel vagy szolgáltatásaival (például Office, Skype, OneDrive vagy Windows) használták.  

Amikor az oktatók bejelentkeznek a labor Services portálra, a rendszer az e-mail-címük és jelszavuk megadását kéri. Ha az oktató olyan nem Microsoft-fiókba próbál bejelentkezni, amelyhez nincs Microsoft-fiók társítva, akkor az oktató a következő hibaüzenetet kapja: 

![Hibaüzenet](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft-fiókra való feliratkozáshoz az oktatóknak [http://signup.live.com](http://signup.live.com)kell lépniük.  


### <a name="using-a-github-account"></a>GitHub-fiók használata
Az oktatók egy meglévő GitHub-fiókkal is regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba. Ha az oktató már rendelkezik egy Microsoft-fiók a GitHub-fiókjához, akkor bejelentkezhet, és megadhatja a jelszavát az előző szakaszban látható módon. Ha még nem csatolták a GitHub-fiókját egy Microsoft-fiókhoz, akkor a **bejelentkezési lehetőségeket**kell választania:

![Bejelentkezési beállítások hivatkozása](../media/how-to-configure-student-usage/signin-options.png)

A **bejelentkezési beállítások** lapon válassza a **Bejelentkezés a githubkal**lehetőséget.

![Bejelentkezés GitHub-hivatkozással](../media/how-to-configure-student-usage/signin-github.png)

Végül a rendszer kéri, hogy hozzon létre egy Microsoft-fiók, amely a GitHub-fiókjához van csatolva. Automatikusan megtörténik, amikor az oktató kiválasztja a **Next (tovább**) gombot.  Ezután az oktató azonnal bejelentkezik, és csatlakozik az osztályterem laborhoz.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Virtuális gépek automatikus leállítása a kapcsolat bontásakor
A távoli asztali kapcsolat leválasztása után engedélyezheti vagy letilthatja a Windows Lab virtuális gépek (sablon vagy tanuló) automatikus leállítását. Azt is megadhatja, hogy a virtuális gépek mennyi ideig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt.

![Automatikus leállítás beállítása Lab-fiókban](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Ez a beállítás a labor-fiókban létrehozott összes laborra vonatkozik. A labor tulajdonosa felülbírálhatja ezt a beállítást a tesztkörnyezet szintjén. A labor-fiókban a beállítás módosítása csak a módosítás után létrehozott laborokra lesz hatással.

Ha szeretné megtudni, hogy a labor tulajdonosa hogyan állíthatja be ezt a beállítást a labor szintjén, tekintse meg [ezt a cikket.](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
