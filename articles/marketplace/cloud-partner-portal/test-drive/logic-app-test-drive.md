---
title: Logic app Test Drive | Azure piactér
description: A cikk azt ismerteti, hogyan hozhat létre olyan tesztelési meghajtót, amely egy Dynamics AX-vagy CRM-példánnyal, vagy bármely más, az Azure-on túli erőforrással csatlakozik.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 87170344f7467770829cbd8ba7d8936a9e39c0cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824598"
---
<a name="logic-app-test-drive"></a>Logic app Test Drive
====================

Ez a cikk azoknak a kiadóknak szól, akik rendelkeznek a AppSource, és szeretnék felépíteni a tesztelési meghajtót, amely egy Dynamics AX/CRM-példánnyal vagy más erőforrással csatlakozik az Azure-on kívül.

<a name="how-to-build-a-logic-app-test-drive"></a>Logikai alkalmazás tesztelési meghajtójának létrehozása
-----------------------------------

A test Drive dokumentációja a Logic app test-meghajtókhoz jelenleg továbbra is a GitHubon zajlik a [műveletekhez](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)bevonásához. További információért látogasson el ide.

<a name="how-to-publish-a-test-drive"></a>Tesztelési meghajtó közzététele
---------------------------

Most, hogy elkészítette a tesztelési meghajtót, ez a szakasz végigvezeti a tesztelési meghajtó sikeres közzétételéhez szükséges összes mezőn.

![A test Drive funkció engedélyezése](./media/azure-resource-manager-test-drive/howtopub1.png)

Az első és legfontosabb mező az, hogy a kitöltéshez meg kell-e jeleníteni az űrlap tesztelését az összes kötelező mezővel. Ha a nem lehetőséget választja **,** az űrlap le lesz tiltva, és ha újból közzéteszi a tesztelési meghajtót, a rendszer eltávolítja a tesztelési meghajtót az éles környezetből.

*Megjegyzés*: Ha a felhasználók aktívan használják a tesztelési meghajtókat, akkor ezek a tesztelési meghajtók továbbra is futni fognak, amíg a munkamenet le nem jár.

### <a name="details"></a>Részletek

A kitöltendő következő szakasz a tesztelési meghajtó ajánlatának részleteit tartalmazza.

![Illesztőprogram-ellenőrzés részletei](./media/azure-resource-manager-test-drive/howtopub2.png)

**Leírás –** *[kötelező mező]* itt írhatja be a tesztelési meghajtón található fő leírást. Az ügyfél itt olvashatja el, hogy milyen forgatókönyveket fog a tesztvezetés a termékre vonatkozóan. 

**Felhasználói kézikönyv –** *[kötelező mező]* ez a tesztelési meghajtó felületének részletes áttekintése. Az ügyfél ekkor megnyithatja ezt, és pontosan megtekintheti, hogy mit szeretne tenni a tesztelési meghajtón. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen! (. PDF-fájlnak kell lennie)

A **Test Drive bemutató videója –** \[ajánlott\] a felhasználói kézikönyvhez hasonló módon, a legjobb megoldás egy videós oktatóanyag készítése a test Drive-élményhez. Az ügyfél ezt megelőzően vagy a tesztelési meghajtón tekinti meg, és pontosan megmutatja, mit szeretne tenni a tesztelési meghajtón. Fontos, hogy ez a tartalom könnyen érthető és követhető legyen!

- A videó **neve** – cím
- **Hivatkozás** – a YouTube vagy a Vimeo beágyazott URL-címének kell lennie. Példa a beágyazott URL-cím beszerzésére:
- **Miniatűr** – magas minőségi képpontnak (533x324) kell lennie. A test Drive-élmény néhány részének képernyőképét javasoljuk.

Alább látható, hogyan jelennek meg ezek a mezők az ügyfél számára a test Drive-élményben.

![A test Drive-mezők megjelenése és hangulata](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technikai konfiguráció

A kitöltés következő szakasza a tesztelési meghajtó logikai alkalmazásának konfigurálása és a tesztvezetés-példányok működésének meghatározása.

![Tesztelési meghajtó technikai konfigurációja](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Régió** -  *[kötelező mező]* a kiválasztott régió az a hely, ahol kiválaszthatja, hol vannak telepítve a test Drive Logic app-erőforrások.

    *Megjegyzés:* Ha a logikai alkalmazás rendelkezik olyan egyéni erőforrásokkal, amelyek egy régióban vannak tárolva, győződjön meg arról, hogy a régió itt van kiválasztva. Ennek a legjobb módja a **logikai alkalmazás helyi üzembe helyezése az Azure-előfizetésben a portálon, és annak ellenőrzése, hogy az működik** -e az itt való írás előtt.

- Az **egyidejű tesztelési meghajtók maximális** száma -  *[kötelező mező]* a már üzembe helyezett tesztvezetés-példányok száma, és a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztelési meghajtókhoz, és nem kell megvárniuk az üzembe helyezést.

    *Megjegyzés:* Ha olyan webes szemináriumot vagy osztályt futtat, amelyben az összes N-számú tanulót szeretné kipróbálni, akkor azt javasoljuk, hogy tegyen közzé nagy számú gyors példányt, majd miután az osztály átvette a normál számú gyors példány újbóli közzétételét.

- **Tesztelési meghajtó időtartama (óra) –** *[kötelező mező]* időtartam, ameddig a tesztvezetés aktív marad, \# óra múlva. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve-** *[kötelező mező]* írja be az erőforráscsoport nevét, ahol a logikai alkalmazás tesztelési meghajtóit menti a rendszer.

- A **logikai alkalmazás nevének kiosztása –** *[kötelező mező]* írás a logikai alkalmazásban, amely a felhasználó a tesztelési meghajtón való hozzárendelésére szolgál, mielőtt az ügyfél beolvassa azt, írja be a logikai alkalmazás nevét. Győződjön meg arról, hogy a fájl a fenti erőforráscsoporthoz lett mentve.

- A **logikai alkalmazás nevének megszüntetése –** *[kötelező mező]* írja a logikai alkalmazás nevét a tesztelési meghajtón létrehozott összes erőforrás megszüntetéséhez. Győződjön meg arról, hogy a fájl a fenti erőforráscsoporthoz lett mentve.

- **Hozzáférési információk –** *[kötelező mező]* miután egy ügyfél lekéri a tesztelési meghajtót, a hozzáférési információk megjelennek a számukra. Ezek az utasítások célja, hogy megosszák a hasznos kimeneti paramétereket a test Drive Resource Manager-sablonból. A kimeneti paraméterek belefoglalásához használjon dupla kapcsos zárójeleket (például **{{outputname}}** ), és a helyükön helyesen lesznek beszúrva. (A HTML-karakterlánc formázását érdemes az előtérben megjeleníteni).

### <a name="test-drive-deployment-subscription-details"></a>Tesztelési meghajtó üzembe helyezésének előfizetése – részletek

A kitöltendő utolsó szakasz az Azure-előfizetés és a Azure Active Directory (AD) összekapcsolásával automatikusan képes lesz a tesztelési meghajtók üzembe helyezésére.

![Tesztelési meghajtó üzembe helyezésének előfizetése – részletek](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-előfizetés azonosítója** *[kötelező mező]* ez hozzáférést biztosít az Azure-szolgáltatásokhoz és a Azure Portalhoz. Az előfizetés az erőforrás-használat jelentését és a szolgáltatások számlázását jelenti. Ha még nem rendelkezik **külön** Azure-előfizetéssel kizárólag tesztelési meghajtókhoz, folytassa a következő lépéssel. Az Azure-előfizetési azonosítók megkereséséhez jelentkezzen be Azure Portal és navigáljon a bal oldali menüben lévő előfizetésekhez.
(Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-előfizetések](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure ad-bérlő azonosítója** *[kötelező mező]* ha már elérhető bérlői azonosító, a Properties-\> Directory-azonosítóban találhatja meg alább.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Ellenkező esetben hozzon létre egy új bérlőt Azure Active Directory.

![Azure Active Directory tulajdonságok képernyő](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-Test-Drive/subdetails5.png)

![Bérlők Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure ad alkalmazás azonosító** *[kötelező mező]* következő lépés egy új alkalmazás létrehozása és regisztrálása. Ezt az alkalmazást fogjuk használni a test Drive-példányon végrehajtott műveletek végrehajtásához.

1. Navigáljon az újonnan létrehozott címtárhoz vagy már meglévő címtárhoz, és válassza az Azure Active Directory elemet a szűrő ablaktáblán.
2. Keressen rá a "Alkalmazásregisztrációk" kifejezésre, és kattintson a "Hozzáadás" gombra.
3. Adja meg az alkalmazás nevét.
4. Válassza ki a "Web App/API" típust
5. Adjon meg bármilyen értéket a bejelentkezési URL-címben, mi nyerte el\'t a mező használatával.
6. Kattintson a Létrehozás gombra.
7. Az alkalmazás létrehozása után lépjen a Properties-\> az alkalmazás beállítása több-bérlős elemre, és kattintson a Save (Mentés) gombra.

Kattintson a Mentés gombra. Az utolsó lépés az alkalmazás AZONOSÍTÓjának beolvasása a regisztrált alkalmazáshoz, és beillesztés a test Drive (tesztelési meghajtó) mezőbe.

![Azure Active Directory alkalmazás azonosítója](./media/azure-resource-manager-test-drive/subdetails7.png)

Mivel az alkalmazást az előfizetésre való központi telepítésre használjuk, az alkalmazást közreműködőként kell hozzáadni az előfizetéshez. Az alábbi utasítások a következők:

1. Navigáljon az előfizetések panelre, és válassza ki a megfelelő előfizetést, amelyet csak a tesztelési meghajtóhoz használ.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **szerepkör-hozzárendelések** fülre.  ![Azure Active Directory, új Access Control rendszerbiztonsági tag hozzáadása](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kattintson a **szerepkör-hozzárendelés hozzáadása**lehetőségre.
1. Adja meg a szerepkört **közreműködőként**.
1. Írja be az Azure AD-alkalmazás nevét, és válassza ki azt az alkalmazást, amelyhez hozzá szeretné rendelni a szerepkört.
    ![Azure Active Directory engedélyek](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kattintson a **Save** (Mentés) gombra.

**Azure ad alkalmazás Key-** *[kötelező mező]* a végső mező egy hitelesítési kulcs létrehozása. A kulcsok területen adja meg a kulcs leírását, állítsa be az időtartamot, hogy soha ne járjon le, majd válassza a mentés lehetőséget. **Fontos** elkerülni, hogy a rendszer ne jelentsen egy lejárt kulcsot, így a tesztelési meghajtót éles környezetben fogja megszüntetni. Másolja ezt az értéket, és illessze be a szükséges tesztvezetés mezőjébe.

![Azure Active Directory kulcsok szakasz](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Az Azure-alkalmazás regisztrációjának előzetes verziója nem használható, mert jelenleg nem állít elő Base64 kódolású kulcsot.


<a name="next-steps"></a>További lépések
----------

Most, hogy kitöltötte az összes tesztelési meghajtó mezőjét, ugorjon át, és **tegye közzé újból** az ajánlatot. Miután a tesztvezetés átadta a minősítési folyamatot, az ajánlat **előzetes** verziójában alaposan tesztelni kell a felhasználói élményt. Indítsa el a tesztelési meghajtót a felhasználói felületen, és ellenőrizze, hogy a tesztelési meghajtók teljes mértékben telepítve vannak-e.

Fontos megjegyezni, hogy nem törli a tesztvezetés azon részét, amelyet az ügyfeleknek kiépítenek, ezért a test Drive szolgáltatás automatikusan törli ezeket az erőforráscsoportokat, miután az ügyfél befejezte azt.

Ha már elégedett az előzetes verzió ajánlatával, most itt az ideje, hogy **élj**! A Microsoft végső felülvizsgálati folyamata az ajánlat közzétételét követően, a teljes végpontok közötti élmény ellenőrzéséhez. Ha valamilyen okból elutasítják az ajánlatot, elküldünk egy értesítést az ajánlat mérnöki kapcsolattartójának, amely elmagyarázza, mit kell megjavítania.

Ha további kérdései vannak, hibaelhárítási tanácsokat talál, vagy ha szeretné, hogy a tesztvezetés sikeres legyen, tekintse meg a [Gyakori kérdések, hibaelhárítás és & ajánlott eljárásokat](./marketing-and-best-practices.md).
