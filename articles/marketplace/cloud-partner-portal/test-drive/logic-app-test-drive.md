---
title: Logikai alkalmazás tesztelése |} A Microsoft Docs
description: Azt ismerteti, hogyan hozhat létre a Test Drive kapcsoló, a Dynamics AX/CRM-példányon, vagy bármely más túli csak Azure-erőforrás.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: d784941c43da13a2c1bd120599aa02fe14e5a5b4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456824"
---
<a name="logic-app-test-drive"></a>Logikai alkalmazás kipróbálása
====================

Ez a cikk a kiadók, akik az ajánlatot az appsource-ban, és létre szeretné hozni a Test Drive kapcsoló, a Dynamics AX/CRM-példányon, vagy bármely más túli csak Azure-erőforrás.

<a name="how-to-build-a-logic-app-test-drive"></a>Hogyan hozhat létre egy logikai alkalmazás kipróbálása
-----------------------------------

Továbbra is a Githubon, jelenleg Logic App-Tesztverziókkal meghajtó dokumentáció tesztje [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), keresse fel további ott.

<a name="how-to-publish-a-test-drive"></a>A Test Drive közzététele
---------------------------

Most, hogy a beépített Test Drive, ez a szakasz végigvezeti Önt a mezőkben ahhoz, hogy a Test Drive közzététele sikertelen.

![A Test Drive funkció engedélyezése](./media/azure-resource-manager-test-drive/howtopub1.png)

Az első és legfontosabb mezőt, hogy állíthatja be, hogy szeretne tesztelni kívánja az összes kötelező mezőt a képernyő jelenik meg, hogy adja meg. Ha bejelöli **nem,** az űrlap le lesz tiltva, és ha ismét közzéteszi az a Tesztverziós le van tiltva, a Test Drive éles törlődik.

*Megjegyzés*: Ha bármely Tesztverzió felhasználók által aktívan használt, ezeket a Tesztverzió továbbra is futni, amíg a munkamenet lejár.

### <a name="details"></a>Részletek

Töltse ki a következő szakaszban a Test Drive részleteit kínálnak.

![Teszt illesztőprogram adatai](./media/azure-resource-manager-test-drive/howtopub2.png)

**Leírás –** *[kötelező mező]* Ez az, ahol ír a fő leírást arról, mi van a Test Drive. Az ügyfél milyen helyzetekben a Test Drive fog lefedő, a termékkel kapcsolatos elolvasható itt fognak érkezni. 

**Felhasználó manuális –** *[kötelező mező]* Ez az a Test Drive felhasználói élmény részletes leírását. Az ügyfél megnyílik ez, és pontosan mit azt szeretné, hajtsa végre a Test Drive alatt is végig. Fontos, hogy ez a tartalom így könnyen megismerhető és hajtsa végre a! (.Pdf fájlnak kell lennie)

**Tesztelje a meghajtó – bemutató videó -** \[ajánlott\] hasonló felhasználói kézikönyvet, a legjobb, ha egy videó-oktatóanyag, a Test Drive felhasználói élmény. Az ügyfél figyelni ezen előzetes vagy a Test Drive során, és pontosan mit azt szeretné, hajtsa végre a Test Drive alatt is végig. Fontos, hogy ez a tartalom így könnyen megismerhető és hajtsa végre a!

- **Név** – a videó címe
- **Hivatkozás** -kell lennie egy beágyazott YouTube vagy Vimeo URL-CÍMÉT. A beágyazott URL-cím beszerzése a példában nem éri el:
- **Miniatűr** -képnek kell lennie egy kiváló minőségű (533 x 324) képpont. Javasoljuk, hogy egy része a Test Drive felhasználói élmény képernyőképe itt is.

Az alábbi, hogyan ezek a mezők meg az ügyfelek számára a Test Drive során.

![Test Drive mezők megjelenését és működését érintő](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technikai konfiguráció

Töltse ki a következő szakaszban a Test Drive logikai alkalmazás konfigurálása és hogyan kifejezetten meghatározása a Test Drive instances munka.

![Tesztkonfiguráció meghajtó technikai](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Régió** - *[kötelező mező]* választott régió, ahol válasszon a Ha a meghajtó a logikai alkalmazás tesztelése erőforrások vannak üzembe helyezve.

    *Megjegyzés:* Ha a logikai alkalmazás minden olyan egyéni erőforrás, amely egy régióban vannak tárolva, győződjön meg arról, adott régióban itt ki van jelölve. A legjobb módja az, hogy **teljesen helyileg, az Azure-előfizetést a portálon, a logikai alkalmazás üzembe helyezése, és ellenőrizze, hogy működik-e** itt írása előtt.

- **Maximális párhuzamos Tesztverzió** - *[kötelező mező]* Test Drive-példányok, amelyek már központilag telepítve és Várakozás a kiválasztott régiónként eléréséhez. Ügyfelek azonnal hozzáférhetnek a Tesztverzió várnia a központi telepítés helyett.

    *Megjegyzés:* Ha egy webináriumra/class adat. Ha szeretné a tanulóknak, hogy kipróbálás N számú futnak, javasoljuk, hogy közzé N számú a gyakori elérésű példányok és majd ezt követően az osztály több mint tennie, térjen vissza a normál gyakori elérésű-példányok számát.

- **Tesztelje a meghajtó időtartama (óra) –** *[kötelező mező]* időtartama mennyi ideig a Test Drive aktív marad, a \# óra. A Test Drive Ez az időtartam lejárta után automatikusan leáll.

- **Azure erőforráscsoport-neve -** *[kötelező mező]* írási az az erőforráscsoport neve, ahol a logikai alkalmazás Tesztverziókkal mentésére.

- **Rendelje hozzá a logikai alkalmazás neve -** *[kötelező mező]* a logikai alkalmazást, a Test drive-on tárolt felhasználó hozzárendelése előtt az ügyfél lekéri, amellyel írási írja be ide a logikai alkalmazás nevét. Ellenőrizze, hogy a fájl mentésekor a fenti erőforráscsoportban.

- **Logikai alkalmazás neve - megszüntetése** *[kötelező mező]* írja be a logikai alkalmazás nevét a megszüntetés a Test Drive létrehozott összes erőforrást. Ellenőrizze, hogy a fájl mentésekor a fenti erőforráscsoportban.

- **Információ - hozzáférés** *[kötelező mező]* után az ügyfél lekéri a Test Drive, a hozzáférési információk jelennek-e meg a számukra. Ezeket az utasításokat úgy van kialakítva, hogy a hasznos a kimeneti paraméterek a Test Drive Resource Manager-sablon megosztása. Adja meg a kimeneti paraméterek, használja a kapcsos zárójelek (például **{{outputname}}**), és azok kerül beillesztésre megfelelően a helyen. (HTML karakterlánc-formátum ajánlott Itt jelennek meg az előtér).

### <a name="test-drive-deployment-subscription-details"></a>Tesztelés üzembe helyezési előfizetés részletei

Az utolsó szakaszban adja meg, hogy tud érvénybe léptetni a Tesztverzió automatikusan csatlakozzon az Azure-előfizetés és az Azure Active Directory (AD).

![Tesztelje a meghajtó üzembe helyezési előfizetés részletei](./media/azure-resource-manager-test-drive/subdetails1.png)

**Az Azure előfizetés-azonosító** *[kötelező mező]* Ez hozzáférést biztosít az Azure-szolgáltatások és az Azure Portalon. Az előfizetés, ahol készüljön jelentés, erőforrás-használat és a szolgáltatás számlázása. Ha Ön még nem rendelkezik egy **külön** Tesztverzió az Azure-előfizetés csak, kérjük lépjen tovább, és hozzon létre egyet. Az Azure-előfizetés azonosítókat megkereséséhez az Azure Portalra jelentkezik be, és ellenőrizheti, hogy az előfizetések, a bal oldali menüben.
(Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Az Azure-előfizetések](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-bérlő azonosítója** *[kötelező mező]* Ha a bérlő Azonosítóját már rendelkezésre álló tulajdonságok, azt alább találhatja\> címtár-azonosító

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Máskülönben hozzon létre egy új bérlőt az Azure Active Directoryban.

![Az Azure Active Directory tulajdonságai képernyő](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Az Azure Active Directory-bérlők](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD-alkalmazás azonosítója** *[kötelező mező]* hozhat létre, és a egy új alkalmazás regisztrálása a következő lépéssel. Ezt az alkalmazást használjuk a Test Drive-példány műveletek végrehajtásához.

1. Lépjen abba a könyvtárba, újonnan létrehozott vagy már meglévő könyvtár, és válassza ki az Azure Active directory szűrő ablaktáblát.
2. Keressen az "Alkalmazásregisztrációk", és kattintson a "Hozzáadás" gombra
3. Adjon meg egy alkalmazásnevet.
4. Adja meg, mint "webalkalmazás / API"
5. Adja meg a bejelentkezési URL-értéket, megnyert\'t használja ezt a mezőt.
6. Kattintson a Létrehozás gombra.
7. Miután létrehozta az alkalmazást, válassza a Tulajdonságok –\> állítja be az alkalmazás több-bérlős, és kattintson a Mentés gombra.

Kattintson a Mentés gombra. Az utolsó lépés, hogy az Alkalmazásazonosítót, a regisztrált alkalmazás megnyitása, és illessze be ide a Test Drive mező.

![Az Azure Active Directory-alkalmazás azonosítója](./media/azure-resource-manager-test-drive/subdetails7.png)

Adott használjuk az alkalmazás üzembe helyezéséhez az előfizetéshez, hozzá kell adnunk a az alkalmazás az előfizetés közreműködője. A következő útmutatót: ezek a következők alatt:

1. Az előfizetések panelen keresse meg, és válassza ki a megfelelő előfizetést, amely csak a Test Drive használja.
1. Kattintson a **hozzáférés-vezérlés (IAM)**.
1. Kattintson a **szerepkör-hozzárendelések** fülre.  ![Az Azure Active Directory, új egyszerű hozzáférés-vezérlés hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kattintson a **szerepkör-hozzárendelés hozzáadása**.
1. Állítsa be a szerepkört, **közreműködői**.
1. Írja be az Azure AD-alkalmazás nevét, és válassza ki a szerepkör hozzárendelése az alkalmazást.
    ![Az Azure Active Directory-engedélyek](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kattintson a **Save** (Mentés) gombra.

**Az Azure AD-Alkalmazáskulcs -** *[kötelező mező]* az utolsó mezőt, hogy a hitelesítési kulcs létrehozásához. A kulcsok adjon meg egy kulcs leírást az időtartamot, soha nem jár le, majd válassza a mentés. Ez **fontos** ne kelljen egy lejárt kulcs, amely megszakítja a test drive éles környezetben. Másolja ezt az értéket, és illessze be a Test Drive kötelező mező.

![Az Azure Active Directory-kulcsok szakaszban](./media/azure-resource-manager-test-drive/subdetails8.png)

<a name="next-steps"></a>További lépések
----------

Most, hogy az összes a Test Drive mezőket kitölteni, mennek keresztül és **tegye közzé újra** az ajánlatot. A Test Drive a minősítési folyamat letelte után kell lépjen egy alaposan tesztelje az ügyfélélmény a **előzetes** az Ön ajánlatát. Indítsa el a Test Drive a felhasználói felületen, és győződjön meg arról, hogy a Tesztverzió rendszer teljes mértékben telepíti megfelelően.

Fontos megjegyezni, hogy nem törli a Test Drive bármelyik részét, kiépítésüket ügyfelei, így az ügyfél azt befejezése után a Test Drive szolgáltatás automatikusan tiszta ezeket az erőforráscsoportokat.

Ha úgy gondolja, hogy tisztában az előzetes verzió ajánlathoz, most már, ideje **élesben**! Van a Microsoft végső lektorálásra folyamat után az ajánlat már közzétett kettős ellenőrizze a teljes teljes körű felhasználói élményt. Ha valamilyen okból az ajánlat beolvasása visszautasítja, küldeni fogunk egy értesítést a engineering contact az ajánlatban elmagyarázza, mit lehet javítani kell.

Ha további kérdése van, hibaelhárítási tanácsokat keres, vagy kívánja-e a Test Drive sokkal sikeresebb lenne, nyissa meg [– gyakori kérdések, hibaelhárítási és ajánlott eljárások](./marketing-and-best-practices.md).
