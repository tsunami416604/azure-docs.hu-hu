---
title: Logic App Test Drive | Azure Piactér
description: Bemutatja, hogyan hozhat létre a Tesztmeghajtó, amely egy Dynamics AX/CRM-példányhoz vagy bármely más, az Azure-on kívüli erőforráshoz kapcsolódik.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278382"
---
<a name="logic-app-test-drive"></a>Logikai alkalmazás tesztmeghajtója
====================

Ez a cikk a kiadók, akik ajánlatukat appsource és szeretné konsizni a Test Drive, amely csatlakozik a Dynamics AX/CRM-példány vagy bármely más erőforrás túl csak az Azure-ban.

<a name="how-to-build-a-logic-app-test-drive"></a>Hogyan építsünk egy Logic App Test Drive
-----------------------------------

A Logic App Test Test Drives tesztmeghajtóinak tesztkapcsolati dokumentációja jelenleg a GitHub on [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)szolgáltatáson található, további információért.

<a name="how-to-publish-a-test-drive"></a>Tesztvezetés közzététele
---------------------------

Most, hogy már megvan a Test Drive beépített, ez a szakasz végigvezeti az egyes mezők szükségesek ahhoz, hogy sikeresen tegye közzé a Test Drive.

![A Tesztmeghajtó szolgáltatás engedélyezése](./media/azure-resource-manager-test-drive/howtopub1.png)

Az első és legfontosabb mező az, hogy bekapcsolja, hogy szeretné-e az űrlap tesztelése az összes szükséges mezőt kitölteni. Ha a Nem lehetőséget választja, az űrlap letiltásra **kerül,** és ha újra közzéteszi a Tesztmeghajtót, a tesztmeghajtó törlődik az éles környezetből.

*Megjegyzés:* Ha a felhasználók aktívan használnak tesztmeghajtókat, akkor ezek a tesztmeghajtók a munkamenet lejártáig továbbra is futni fognak.

### <a name="details"></a>Részletek

A következő kitöltandó szakasz a Test Drive ajánlat részletei.

![A tesztillesztő adatai](./media/azure-resource-manager-test-drive/howtopub2.png)

**Leírás -** *[Kötelező mező]* Itt írja meg a fő leírást arról, hogy mi van a tesztmeghajtón. Az ügyfél jön ide, hogy olvassa el, milyen forgatókönyveket a Test Drive lesz, amely a termékről. 

**Felhasználói kézikönyv -** *[Kötelező mező]* Ez a Test Drive-élmény részletes forgatókönyve. Az ügyfél megnyitja ezt, és végigsétálhat pontosan azon, amit szeretne, hogy tegyenek a Tesztvezetés során. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen! (.pdf fájlnak kell lennie)

**Test Drive Demo Video -** \[Ajánlott\] Hasonló a felhasználói kézikönyv, a legjobb, ha egy video tutorial a Test Drive tapasztalat. Az ügyfél fogja nézni ezt a korábbi vagy során test drive, és végigjárni, hogy pontosan mit szeretne, hogy nem az egész Test Drive. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen!

- **Név** - A videó címe
- **Link** - A YouTube vagy a Vimeo beágyazott URL-címe kell, hogy legyen. Példa arra, hogyan lehet a beágyazott URL alatt van:
- **Thumbnail** - Jó minőségű képnek (533x324) kell lennie. Javasoljuk, hogy itt képernyőképet készít a Test Drive-élmény egy részéről.

Az alábbiakban azt olvashatja, hogy ezek a mezők hogyan jelennek meg az ügyfél számára a Test Drive-élmény során.

![A Test Drive mezők megjelenésése](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Műszaki konfiguráció

A következő kitöltandó szakasz ban konfigurálhatja a Test Drive Logic App alkalmazást, és megadhatja, hogy a Tesztmeghajtó-példányok hogyan működnek.

![A Test Drive műszaki konfigurációja](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Régió** - *[Kötelező mező]* A kiválasztott régióban válassza ki, ahol a Test Drive Logic App erőforrásait üzembe helyezése.

    *Megjegyzés:* Ha a logikai alkalmazás rendelkezik olyan egyéni erőforrásokkal, amelyek egy régióban vannak tárolva, győződjön meg arról, hogy a régió itt van kiválasztva. Ennek legjobb módja, ha **teljes mértékben telepíti a logic app helyileg az Azure-előfizetésben a portálon, és ellenőrizze, hogy működik-e,** mielőtt ide írnák.

- **Maximális egyidejű tesztmeghajtók** - *[Kötelező mező]* A már telepített és hozzáférésre váró tesztmeghajtó-példányok száma kiválasztott régiónként. Az ügyfelek azonnal elérhetik ezt a tesztmeghajtót, és nem kell megvárniuk a központi telepítést.

    *Megjegyzés:* Ha olyan webináriumot/osztályt futtat, ahol azt szeretné, hogy az összes N-számú tanuló tesztmeghajtót hajtson végre, ajánlott n számú hot példányt közzétenni, majd ha az osztály vége, tegye közzé újra a szokásos számú forró példányt.

- **Tesztvezetés időtartama (óra) -** *[Kötelező mező]* Időtartam, hogy mennyi \# ideig a tesztvezetés aktív marad, az óra. A tesztvezetés automatikusan leáll ezen időszak lejárta után.

- **Azure Resource Group name –** *[Required Field]* Írás az erőforráscsoport nevében, ahol a logikai alkalmazás tesztmeghajtók menti.

- **Logic App Name -** *[Required Field]* Írás a logikai alkalmazásban, amely a tesztmeghajtón lévő felhasználó hozzárendelésére szolgál, mielőtt az ügyfél megkapja, írja be a logikai alkalmazás nevét itt. Győződjön meg arról, hogy a fájl a fenti erőforráscsoportba van mentve.

- **Deprovision Logic App Name -** *[Required Field]* Írás a logikai alkalmazás nevét a test drive-ban létrehozott összes erőforrás deprovisioning. Győződjön meg arról, hogy a fájl a fenti erőforráscsoportba van mentve.

- **Hozzáférési információk -** *[Kötelező mező]* Miután az ügyfél megkapja a Tesztmeghajtót, a hozzáférési információk megjelennek számukra. Ezek az utasítások a Test Drive Resource Manager sablon hasznos kimeneti paramétereinek megosztására szolgálnak. A kimeneti paraméterek felvételéhez használjon dupla göndör szögletes zárójeleket (például **{{outputname}}**), és a rendszer helyesen szúrja be őket a helyre. (Html karakterlánc formázás ajánlott itt jelenik meg az előlapon).

### <a name="test-drive-deployment-subscription-details"></a>A Test Drive-alapú üzembe helyezési előfizetés részletei

Az utolsó kitöltendő szakasz az Azure-előfizetés és az Azure Active Directory (AD) automatikus üzembe helyezésének lehetővé teszi.

![A Test Drive-alapú üzembe helyezési előfizetés részletei](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-előfizetés-azonosító** *[Kötelező mező]* Ez hozzáférést biztosít az Azure-szolgáltatásokhoz és az Azure Portalhoz. Az előfizetés, ahol az erőforrás-használat jelentik, és a szolgáltatások számlázása. Ha még nem rendelkezik **külön** Azure-előfizetéssel csak tesztmeghajtókhoz, kérjük, folytassa, és készítsen egyet. Az Azure-előfizetés-azonosítókat az Azure Portalra való bejelentkezéssel és a bal oldali menü előfizetések parancsára való navigálással találhatja meg.
(Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-előfizetések](./media/azure-resource-manager-test-drive/subdetails2.png)

**Az Azure AD-bérlőazonosító** *[Kötelező mező]* Ha rendelkezik egy bérlői azonosító\> már elérhető megtalálja az alábbiakban a Tulajdonságok - Címtár-azonosító.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Ellenkező esetben hozzon létre egy új bérlőt az Azure Active Directoryban.

![Az Azure Active Directory tulajdonságainak képernyője](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Az Azure Active Directory bérlői](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App ID** *[Kötelező mező]* Következő lépés egy új alkalmazás létrehozása és regisztrálása. Ezt az alkalmazást arra fogjuk használni, hogy műveleteket hajtsunk végre a Test Drive-példányon.

1. Keresse meg az újonnan létrehozott könyvtárat vagy a már meglévő könyvtárat, és válassza az Azure Active directory a szűrőablaktáblán.
2. Keressen rá az "Alkalmazásregisztrációk" kifejezésre, és kattintson a "Hozzáadás" gombra
3. Adja meg az alkalmazás nevét.
4. Válassza ki a típusa a "Web app / API"
5. Adjon meg bármilyen értéket a bejelentkezési URL-címben,\'akkor nem fogjuk használni ezt a mezőt.
6. Kattintson a Létrehozás gombra.
7. Az alkalmazás létrehozása után nyissa meg\> a Tulajdonságok – Állítsa be az alkalmazást több-bérlősként, és nyomja le a Mentés parancsot.

Kattintson a Mentés gombra. Az utolsó lépés az, hogy megragad az alkalmazás azonosítója ehhez a regisztrált alkalmazáshoz, és illessze be a Test Drive mezőbe itt.

![Az Azure Active Directory alkalmazásazonosítója](./media/azure-resource-manager-test-drive/subdetails7.png)

Tekintettel arra, hogy az alkalmazást az előfizetés üzembe helyezéséhez használjuk, hozzá kell adnunk az alkalmazást az előfizetés közreműködőjeként. Ezekre vonatkozó utasítások a következők:

1. Keresse meg az Előfizetések panelt, és válassza ki a tesztvezetéshez használt megfelelő előfizetést.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **Szerepkör-hozzárendelések** fülre.  ![Azure Active Directory, új hozzáférés-vezérlési tag hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.**
1. Állítsa be a szerepkört **közreműködőként.**
1. Írja be az Azure AD-alkalmazás nevét, és válassza ki a szerepkör hozzárendeléséhez az alkalmazást.
    ![Az Azure Active Directory engedélyei](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kattintson a **Mentés** gombra.

**Azure AD alkalmazáskulcs –** *[Kötelező mező]* A végső mező egy hitelesítési kulcs létrehozása. A billentyűk alatt adjon hozzá egy kulcsleírást, állítsa úgy az időtartamot, hogy soha ne járjon le, majd válassza a mentés lehetőséget. **Fontos,** hogy ne legyen lejárt kulcs, amely megszakítja a tesztmeghajtót éles környezetben. Másolja másra ezt az értéket, és illessze be a szükséges Tesztmeghajtó mezőbe.

![Az Azure Active Directory kulcsai szakasz](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Az Azure App Regisztrációs előzetes verziója nem használható, mert jelenleg nem hoz létre base64 kódolású kulcsot.


<a name="next-steps"></a>További lépések
----------

Most, hogy az összes Tesztmeghajtó mező ki lett töltve, menjen át, és **tegye közzé újra** az ajánlatot. Miután a Tesztmeghajtó megfelelt a minősítési folyamaton, alaposan tesztelje az ügyfélélményt az ajánlat **előzetes verziójában.** Indítsa el a tesztmeghajtót a felhasználói felületen, és ellenőrizze, hogy a tesztmeghajtók teljes körűtelepítése megfelelő-e.

Fontos megjegyezni, hogy nem törli a Tesztmeghajtó egyetlen részét sem, mivel azok ki vannak építve az ügyfelek számára, így a Test Drive szolgáltatás automatikusan megtisztítja ezeket az erőforráscsoportokat, miután az ügyfél befejezte azt.

Ha már jól érzi magát az Előzetes ajánlattal, most itt az ideje, hogy **élőben induljon!** Van egy végső felülvizsgálati folyamat a Microsoft, miután az ajánlat már közzétették, hogy ellenőrizze a teljes végponttól végpontig tapasztalat. Ha valamilyen okból az ajánlatot elutasítják, értesítést küldünk az ajánlat műszaki kapcsolattartójának, amelyben elmagyarázza, hogy mit kell javítani.

Ha további kérdései vannak, hibaelhárítási tanácsokat keres, vagy szeretné sikeresebbé tenni a Tesztmeghajtót, látogasson el a [GYIK,Hibaelhárítás, & gyakorlati tanácsok](./marketing-and-best-practices.md)ra.
