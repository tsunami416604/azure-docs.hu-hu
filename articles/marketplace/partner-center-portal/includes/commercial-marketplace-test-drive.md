---
title: fájl belefoglalása
description: fájl belefoglalása
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/13/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 32bc5f76e0be19ae5adb24f10094494d94eeb4d6
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019583"
---
A **tesztvezetés** lapon egy bemutatót (vagy "tesztelési meghajtót") állíthat be, amely lehetővé teszi az ügyfeleknek, hogy a megvásárlása előtt kipróbálják az ajánlatot. További információt a [Mi a test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)című cikkben talál. Ha már nem szeretne tesztelési meghajtót biztosítani az ajánlatához, térjen vissza az **ajánlat beállítása** lapra, és törölje a tesztelési **meghajtó engedélyezése**lehetőséget.

### <a name="technical-configuration"></a>Technikai konfiguráció
A következő típusú tesztelési meghajtók érhetők el, amelyek mindegyike saját technikai konfigurációs követelményekkel rendelkezik.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (Technikai konfiguráció nem szükséges)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó technikai konfigurációja

Egy központi telepítési sablon, amely tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. További információ [Azure Resource Manager tesztelési meghajtó](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)beállításáról.

- **Régiók** (kötelező): Jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.

- **Példányok**: Válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

**Gyors**: Ez a típusú példány üzembe van helyezve, és a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A hosszabb, hogy ezek a példányok mindig futtat az Azure-előfizetést, akkor számítunk fel a költségek nagyobb rendelkezésre állását. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

**Hideg**: Az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon**: Töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt.  További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)című cikkben található.

- **Teszt meghajtójának időtartama** (kötelező): Adja meg azt az időtartamot, ameddig a test meghajtója aktív marad, az órák száma szerint. A Test Drive Ez az időtartam lejárta után automatikusan leáll. Ez az időtartam csak egész számú órával állítható be (például a "2" óra ("1,5") nem érvényes.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció a Dynamics 365 Test Drive-hoz

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét azáltal, hogy a szolgáltatás üzembe helyezését és üzembe helyezését az ilyen típusú tesztelési meghajtó használatával végzi el és tartja karban. Az ilyen típusú üzemeltetett tesztvezetés konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés üzleti központi, vevői szerepvállalási vagy műveleti célközönségre irányul.

- **Egyidejű tesztelési meghajtók maximális** száma (kötelező): Állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért biztosítania kell, hogy elegendő licenc álljon rendelkezésre a maximálisan engedélyezett készlet támogatásához. A 3-5 javasolt értéke.

- **Teszt meghajtójának időtartama** (kötelező): Adja meg azt az időtartamot, ameddig a tesztelési meghajtó aktív marad az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ez az időtartam csak egész számú órával állítható be (például a "2" óra ("1,5") nem érvényes.  Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező): Az URL-cím, amelyen az ügyfél elkezdi a tesztelési meghajtót. A Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja https://testdrive.crm.dynamics.com) , és a mintaadatok telepítve vannak (például:.

- **Példány webes API URL-címe** (kötelező): A Dynamics 365-példány webes API URL-címének lekéréséhez jelentkezzen be a Microsoft 365-fiókjába, és navigáljon a **Beállítások** \&gt; **Testreszabás** \&gt; **Fejlesztői erőforrások** \&gt; A **példány webes API-ját (szolgáltatás gyökerének URL-címe)** másolja az itt https://testdrive.crm.dynamics.com/api/data/v9.0) található URL-címet (például:.

- **Szerepkör neve** (kötelező): Adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét. Ezt a rendszer a tesztelési meghajtó (például a test-Drive-role) során rendeli hozzá a felhasználóhoz.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Technikai konfiguráció a Logic app Test Drive-hoz

Minden egyéni terméknek ezt a típusú tesztelési meghajtó-telepítési sablont kell használnia, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyetlen kijelölésű legördülő lista): Jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. Ennek a legjobb módja a logikai alkalmazás helyi üzembe helyezése az Azure-előfizetésben a portálon, és annak ellenőrzése, hogy az megfelelően működik-e a kijelölés előtt.

- **Egyidejű tesztelési meghajtók maximális** száma (kötelező): Állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Teszt meghajtójának időtartama** (kötelező): Adja meg azt az időtartamot, ameddig a test meghajtója aktív marad, az órák száma szerint. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező): Adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, ahol a logikai alkalmazás tesztelési meghajtóját menti.

- **Azure Logic App-alkalmazás neve** (kötelező): Adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót rendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének kiépítése** (kötelező): Adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót felépíti az ügyfél befejeződése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI tesztelési meghajtókhoz nem szükséges technikai konfiguráció

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. További információ a[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) template-alkalmazások beállításáról.

### <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Ha a tesztelési meghajtót az Ön nevében szeretné üzembe helyezni, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést. (Power BI tesztelési meghajtók esetében nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges): Adja meg az előfizetés AZONOSÍTÓját, hogy hozzáférést biztosítson az Azure-fiók szolgáltatásaihoz az erőforrás-használati jelentéskészítéshez és a számlázáshoz. Javasoljuk, hogy hozzon [létre egy külön Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) -előfizetést, amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a Azure Portalba való [](https://portal.azure.com/) bejelentkezéssel és a bal oldali menü előfizetések lapján érheti el. A lap kiválasztása esetén megjelenik az előfizetési azonosító (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező): Adja meg a Azure Active Directory (AD [](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)) BÉRLŐi azonosítóját. Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok** elemet, majd keresse meg a felsorolt 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címével is megkeresheti a [https://www.whatismytenantid.com](https://www.whatismytenantid.com)következő helyen:.

- **Azure ad-bérlő neve** (a dinamikus 365 szükséges): Adja meg a Azure Active Directory (AD) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad-alkalmazás azonosítója** (kötelező): Adja meg a Azure Active Directory-(AD-) [alkalmazás azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure ad-alkalmazás-ügyfél titka** (kötelező): Adja meg az Azure AD-alkalmazás [ügyfelének titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. (Ne navigáljon el az oldalról, mielőtt ezt megtenné, különben nem lesz hozzáférése az értékhez.)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

### <a name="test-drive-listings-optional"></a>Tesztelési meghajtók listázása (nem kötelező)

A test Drive (tesztelési meghajtó) lapon található tesztelési **meghajtók listázása** lehetőség megjeleníti azokat a nyelveket (és piacokat), ahol a tesztelési meghajtó elérhető, jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a tesztvezetés részleteit (Leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): Írja le a tesztelési meghajtót, a bemutatott funkciókat, a felhasználó által a kísérletezéshez szükséges célokat, a felderített szolgáltatásokat és a megfelelő információkat, amelyekkel a felhasználó eldöntheti, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni. 

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges): Magyarázza el, hogy mit kell tudnia az ügyfélnek a tesztelési meghajtó eléréséhez és használatához. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező): Részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók Videók** hozzáadása (nem kötelező): A videók feltölthetők a YouTube-ra vagy a Vimeo-ra, és itt egy hivatkozással és egy miniatűr képpel (533 x 324 képpont) jeleníthetők meg, így az ügyfelek megtekinthetik az információk megtekintését, így könnyebben megismerhetik a tesztelési meghajtót, beleértve a funkcióinak sikeres használatát ajánljuk és értse az előnyeit kiemelő forgatókönyveket.
  - **Név** szükséges
  - **URL-cím (csak YouTube vagy Vimeo)** szükséges
  - **Miniatűr (533 x 324px)** : A képfájlnak PNG formátumúnak kell lennie.

A mezők befejezése után válassza a **Mentés** lehetőséget.
