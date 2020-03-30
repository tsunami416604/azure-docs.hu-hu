---
title: fájl belefoglalása
description: fájl belefoglalása
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321745"
---
A **Tesztmeghajtó** lapon beállíthat egy bemutatót (vagy "tesztmeghajtót"), amely lehetővé teszi az ügyfelek számára, hogy kipróbálják az ajánlatot, mielőtt elkötelezné magukat a megvásárlás mellett. További információ: [Mi az a Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Ha már nem szeretne tesztvezetést biztosítani az ajánlatához, térjen vissza az **Ajánlat beállítási** oldalára, és törölje a jelet **a Tesztvezetés engedélyezése**jelölőnégyzetből.

### <a name="technical-configuration"></a>Műszaki konfiguráció
A következő típusú tesztmeghajtók állnak rendelkezésre, mindegyik saját műszaki konfigurációs követelményekkel.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (műszaki konfiguráció nem szükséges)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Műszaki konfiguráció az Azure Resource Manager tesztmeghajtójához

Egy központi telepítési sablon, amely tartalmazza a megoldásösszes Azure-erőforrást. A forgatókönyvnek megfelelő termékek csak Azure-erőforrásokat használnak. További információ az [Azure Resource Manager tesztmeghajtójának](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező): Jelenleg 26 Azure által támogatott régió van, ahol a tesztmeghajtó elérhetővé tehető. A tesztmeghajtót általában azokban a régiókban szeretné elérhetővé tenni, ahol a legtöbb ügyfél várható, hogy a legjobb teljesítmény hez legközelebb eső régiót választhassák ki. Győződjön meg arról, hogy az előfizetés e kiválasztott régiókban az összes szükséges erőforrás üzembe helyezéséhez engedélyezett.

- **Példányok:** Válassza ki a típus (meleg vagy hideg) és a rendelkezésre álló példányok számát, amely megszorozza a régiók száma, ahol az ajánlat elérhető.

**Gyors**: Az ilyen típusú példány telepítve van, és a hozzáférésre vár a kiválasztott régiónként. Az ügyfelek azonnal hozzáférhetnek a tesztmeghajtó *kokszpéldányaihoz,* ahelyett, hogy meg kellene várniuk a központi telepítést. A kompromisszum az, hogy ezek a példányok mindig fut nak az Azure-előfizetés, így nagyobb uptime költség merülnek fel. Erősen ajánlott, hogy legalább egy *hot* példány, mivel a legtöbb ügyfél nem akar várni a teljes központi telepítések, ami egy lemorzsolódás az ügyfél használat, ha nincs *hot* példány nem érhető el.

**Hideg**: Ez a példánytípus a régiónként esetlegesen üzembe helyezhető példányok teljes számát jelöli. A hideg példányok megkövetelik, hogy a teljes Test Drive Resource Manager-sablon üzembe helyezze, amikor egy ügyfél kéri a tesztmeghajtót, így *a hideg* példányok betöltése sokkal lassabb, mint a hot-példányok. *Hot* A kompromisszum az, hogy csak akkor kell fizetnie a tesztvezetés időtartama alatt, *nem* mindig fut az Azure-előfizetés, mint egy *hot* példány.

- **Tesztmeghajtó Azure Resource Manager sablon:** Töltse fel az Azure Resource Manager sablont tartalmazó .zip.  Az Azure Resource Manager-sablon létrehozásáról az [Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)rövid útmutató című cikkben olvashat bővebben.

- **A tesztvezetés időtartama** (kötelező): Adja meg, hogy a tesztvezetés mennyi ideig maradjon aktív, az óra számalatt. A tesztvezetés automatikusan leáll ezen időszak lejárta után. Ezt az időtartamot csak egész számú órával lehet beállítani (pl. "2" óra, "1,5" érvénytelen).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>A Dynamics 365 tesztvezetés műszaki konfigurációja

A Microsoft eltávolíthatja a tesztmeghajtó konklúdi beállításának összetettségét a szolgáltatás kiépítésének és üzembe helyezésének üzemeltetésével és karbantartásával az ilyen típusú tesztmeghajtó használatával. Az ilyen típusú üzemeltetett tesztmeghajtókonfiguráció ugyanaz, függetlenül attól, hogy a tesztvezetés Business Central, Customer Engagement vagy Operations közönséget céloz meg.

- **Maximális egyidejű tesztmeghajtók** (kötelező): Állítsa be a tesztvezetés egyszerre használható ügyfeleinek maximális számát. Minden egyidejű felhasználó egy Dynamics 365-licencet használ fel, amíg a tesztmeghajtó aktív, ezért gondoskodnia kell arról, hogy elegendő licenc áll rendelkezésre a maximális korlát beállításának támogatásához. Ajánlott értéke 3-5.

- **A tesztvezetés időtartama** (kötelező): Adja meg azt az időtartamot, amaddig, amíg a tesztvezetés aktív marad az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ér, és már nem használja fel az egyik licencet. Javasoljuk, hogy az ajánlat összetettségétől függően 2-24 órás értéket nyújtson be. Ezt az időtartamot csak egész számú órával lehet beállítani (pl. "2" óra, "1,5" érvénytelen).  A felhasználó új munkamenetet kérhet, ha kifut az időből, és újra hozzá szeretne férni a tesztvezetéshez.

- **Példány URL-címe** (kötelező): Az URL-cím, ahol az ügyfél megkezdi a tesztvezetést. Általában az alkalmazást futtató Dynamics 365-példány URL-címe telepített mintaadatokkal (pl. ). `https://testdrive.crm.dynamics.com`

- **Példány webes API-URL-címe** (kötelező): A Dynamics 365-példány webes API-URL-címének lekérése a Microsoft 365-fiókba való bejelentkezéssel és a **Beállítások (Beállítások)** \&lapra való navigálással; **Testreszabás;** \& **Fejlesztői erőforrások** \&gt; **Példánywebes API -,** másolja az itt található URL-t `https://testdrive.crm.dynamics.com/api/data/v9.0`(pl. ).

- **Szerepkör neve** (kötelező): Adja meg az egyéni Dynamics 365 tesztmeghajtóban megadott biztonsági szerepkörnevét. Ezt a felhasználó a tesztvezetés során (pl. tesztvezetés-szerepkör) rendeli hozzá.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>A Logic alkalmazás tesztmeghajtójának műszaki konfigurációja

Minden egyéni terméknek az ilyen típusú tesztmeghajtó-telepítési sablont kell használnia, amely számos összetett megoldásarchitektúrát foglal magában. A Logic App tesztmeghajtóinak beállításával kapcsolatos további információkért látogasson el [az Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) and [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) oldalra a GitHubon.

- **Régió** (kötelező, egyválasztós legördülő lista): Jelenleg 26 Azure által támogatott régió van, ahol a tesztmeghajtó elérhetővé tehető. A logikai alkalmazás erőforrásai a kiválasztott régióban lesznek telepítve. Ha a logikai alkalmazás rendelkezik egy adott régióban tárolt egyéni erőforrásokat, győződjön meg arról, hogy a régió itt van kiválasztva. Ennek legjobb módja, ha teljes mértékben telepíti a logic app helyileg az Azure-előfizetésa a portálon, és ellenőrizze, hogy megfelelően működik-e, mielőtt ezt a kiválasztást.

- **Maximális egyidejű tesztmeghajtók** (kötelező): Állítsa be a tesztvezetés egyszerre használható ügyfeleinek maximális számát. Ezek a tesztmeghajtók már telepítve vannak, így az ügyfelek azonnal hozzáférhetnek a központi telepítésre való várakozás nélkül.

- **A tesztvezetés időtartama** (kötelező): Adja meg, hogy a tesztvezetés mennyi ideig maradjon aktív, az óra számalatt. A tesztvezetés ezen időszak lejárta után automatikusan leáll.

- **Azure-erőforráscsoport neve** (kötelező): Adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, ahol a logic app tesztmeghajtó menti.

- **Azure-logikai alkalmazás neve** (kötelező): Adja meg a tesztmeghajtót a felhasználóhoz hozzárendelő logikai alkalmazás nevét. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **A logic app neve (kötelező):** Adja meg annak a logikai alkalmazásnak a nevét, amely az ügyfél befejezése után dekátlanítja a tesztmeghajtót. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>A Power BI tesztmeghajtóihoz nem szükséges műszaki konfiguráció

Azok a termékek, amelyek interaktív Power BI-vizualizációt szeretnének bemutatni, egy beágyazott hivatkozás segítségével megoszthatják az egyéni irányítópultot tesztmeghajtóként, további műszaki konfiguráció nélkül. További információ a[Power BI-sablonalkalmazások](https://docs.microsoft.com/power-bi/service-template-apps-overview) beállításáról.

### <a name="deployment-subscription-details"></a>Üzembe helyezési előfizetés részletei

A Test Drive az Ön nevében történő üzembe helyezéséhez hozzon létre és adjon meg egy külön, egyedi Azure-előfizetést. (Nem szükséges a Power BI tesztmeghajtóihoz).

- **Azure-előfizetés-azonosító** (az Azure Resource Manager és a Logic alkalmazások esetén szükséges): Adja meg az előfizetés-azonosítót, amely hozzáférést biztosít az Azure-fiókszolgáltatásaihoz az erőforrás-használati jelentések hez és a számlázáshoz. Azt javasoljuk, hogy fontolja meg [egy külön Azure-előfizetés tesztelésére,](https://docs.microsoft.com/azure/billing/billing-create-subscription) ha még nem rendelkezik. Az Azure-előfizetés-azonosítóját az Azure [Portalra](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **Előfizetések** lapjára való navigálással találhatja meg. A lapon megjelenik az előfizetésazonosítója (pl. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-bérlőazonosító** (kötelező): Adja meg az Azure Active Directory (AD) [bérlői azonosítóját.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Az azonosító megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)válassza a bal oldali menü Active Directory lapját, válassza a **Tulajdonságok** lehetőséget, majd keresse meg a felsorolt **címtárazonosító** számot (pl. 50c464d3-4930-494c-963c-1e951d15360e). A tartománynév URL-címével is megkeresheti a szervezet [https://www.whatismytenantid.com](https://www.whatismytenantid.com)bérlői azonosítóját a következő címen: .

- **Azure AD-bérlői név** (dinamikus 365 esetén szükséges): Adja meg az Azure Active Directory (AD) nevét. Ennek a névnek a megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure AD alkalmazásazonosító** (kötelező): Adja meg az Azure Active Directory (AD) [alkalmazásazonosítóját.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Az azonosító megkereséséhez jelentkezzen be az [Azure Portalra,](https://portal.azure.com/)válassza a bal oldali menü Active Directory lapját, válassza az **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a felsorolt **alkalmazásazonosítószámot** (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD alkalmazásügyfél-titkos** (kötelező): Adja meg az Azure [AD-alkalmazásügyfél-titkos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) Ennek az értéknek a megkereséséhez jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) Válassza az **Azure Active Directory** fülét a bal oldali menüben, válassza az **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a tesztmeghajtó alkalmazást. Ezután válassza **a Tanúsítványok és titkos kulcsok**lehetőséget, válassza az Új **ügyféltitok**lehetőséget, adjon meg egy leírást, válassza a **Soha** **a Lejáratok**csoportban a Soha lehetőséget, majd válassza **a Hozzáadás lehetőséget.** Győződjön meg róla, hogy másolja le az értéket. (Ne navigáljon el az oldalról, mert ezt nem teszi meg, különben nem lesz hozzáférése az értékhez.)

Ne feledje, hogy **mentse,** mielőtt a következő szakasz!

### <a name="test-drive-listings-optional"></a>Tesztvezetés listái (nem kötelező)

A **Test Drive listázási lista** a **Tesztmeghajtó** lapon található beállítás on-át jeleníti meg azokat a nyelveket (és piacokat), ahol a tesztvezetés elérhető, jelenleg az angol (Egyesült Államok) az egyetlen elérhető hely. Ezenkívül ezen az oldalon megjelenik a nyelvspecifikus listaállapot, valamint a hozzáadott dátum/idő. Meg kell határoznia a tesztvezetés részleteit (leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): Írja le a tesztvezetést, a bemutatást, a felhasználó által kísérletezendő célokat, a feltárandó funkciókat és minden olyan releváns információt, amely segít a felhasználónak eldönteni, hogy beszerezze-e az ajánlatot. Ebben a mezőben legfeljebb 3000 karakter nyi szöveg adható meg. 

- **Hozzáférés-információk** (az Azure Resource Manager és logic tesztmeghajtók esetén szükséges): Magyarázza el, hogy mit kell tudnia az ügyfélnek a tesztmeghajtó eléréséhez és használatához. Végigvezethet egy forgatókönyvet az ajánlat használatáról, és pontosan azt, hogy az ügyfélnek mit kell tudnia a funkciók eléréséhez a tesztvezetés során. Ebben a mezőben legfeljebb 10 000 karakternyi szöveg adható meg.

- **Használati útmutató** (kötelező): A tesztvezetés élményének részletes áttekintése. A felhasználói kézikönyvnek pontosan azt kell kiterjednie, hogy mit szeretne az ügyfélnek a tesztvezetés megtapasztalásából, és referenciaként kell szolgálnia az esetleges kérdéseikhez. A fájlnak PDF formátumúnak kell lennie, és feltöltés után el kell nevezni (legfeljebb 255 karakter).

- **Videók hozzáadása** (nem kötelező): A videókat fel lehet tölteni a YouTube-ra vagy a Vimeo-ra, és itt hivatkozással lehet hivatkozni egy linkre és egy miniatűr képre (533 x 324 képpont), hogy az ügyfél megtekinthesse az információk átjárását, hogy jobban megérthesse a tesztvezetést, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyveket.
  - **Név** (kötelező)
  - **URL (csak YouTube vagy Vimeo)** (kötelező)
  - **Thumbnail (533 x 324px)**: A képfájlnak PNG formátumúnak kell lennie.

A mezők **kitöltése** után válassza a Mentés lehetőséget.
