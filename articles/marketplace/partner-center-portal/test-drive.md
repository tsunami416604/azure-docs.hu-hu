---
title: Tesztelési meghajtók konfigurálása a Microsoft kereskedelmi piactéren
description: További információ a tesztelési meghajtókról. A tesztelési meghajtók lehetővé teszik az új ügyfelek számára az ajánlat tesztelését, mielőtt véglegesítik a vásárlást.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 8bd17858fa6866549088de8307f40d7b79daef6b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786546"
---
# <a name="learn-about-test-drive-for-your-offer"></a>Ismerje meg az ajánlat tesztelésére szolgáló meghajtót

A Microsoft kereskedelmi piactéren a **Test Drive** lehetőséggel beállíthatók a termék főbb szolgáltatásainak gyakorlati és saját irányítású bemutatói. A tesztelési meghajtóval az új ügyfelek az ajánlat megvásárlása előtt kipróbálhatják az ajánlatot. További információ: [Mi az a test Drive?](../cloud-partner-portal/test-drive/what-is-test-drive.md).

Ha már nem szeretne tesztelési meghajtót biztosítani az ajánlatához, térjen vissza az **ajánlat beállítása** lapra, és törölje a **Test Drive engedélyezése**jelölőnégyzet jelölését.

## <a name="technical-configuration"></a>Technikai konfiguráció

A következő típusú tesztelési meghajtók érhetők el, amelyek mindegyike saját technikai konfigurációs követelményekkel rendelkezik.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Logikai alkalmazás](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (technikai konfiguráció nem szükséges)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó technikai konfigurációja

Létezik egy központi telepítési sablon, amely tartalmazza az összes Azure-erőforrást, amely magában foglalja a megoldását. Ez csak az Azure-erőforrásokat használó termékek számára alkalmas. További információ [Azure Resource Manager tesztelési meghajtó](../cloud-partner-portal/test-drive/azure-resource-manager-test-drive.md)beállításáról.

- **Régiók** (kötelező): jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.

- **Példányok**: válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

  - **Gyors**: Ez a típusú példány üzembe van helyezve, és a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A kompromisszum azt eredményezi, hogy ezek a példányok mindig az Azure-előfizetésen futnak, így a költségek nagyobb üzemidőt jelentenek. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

  - **Hideg**: az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon**: töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt.  További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)című cikkben található.

- **Tesztelési meghajtó időtartama** (kötelező): adja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra, "1,5" nem érvényes).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technikai konfiguráció a Dynamics 365 Test Drive-hoz

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét azáltal, hogy a szolgáltatás üzembe helyezését és üzembe helyezését az ilyen típusú tesztelési meghajtó használatával végzi el és tartja karban. Az ilyen típusú üzemeltetett tesztvezetés konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés üzleti központi, vevői szerepvállalási vagy műveleti célközönségre irányul.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező): adja meg, hogy legfeljebb hány ügyfél használhatja egyszerre a tesztelési meghajtót. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért biztosítania kell, hogy elegendő licenc álljon rendelkezésre a maximálisan engedélyezett készlet támogatásához. A 3-5 javasolt értéke.

- **Tesztelési meghajtó időtartama** (kötelező): adja meg, hogy mennyi ideig maradjon aktív a tesztvezetés az órák számának meghatározásával. Ennyi óra elteltével a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ezt az időtartamot csak egész számú órával lehet beállítani (például "2" óra, "1,5" nem érvényes).  Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező): az az URL-cím, amelyben az ügyfél el fogja kezdeni a tesztelési meghajtót. Általában a Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja `https://testdrive.crm.dynamics.com`(például:).

- **Példány webes API URL-címe** (kötelező): a Dynamics 365-példány webes API URL-címének lekéréséhez jelentkezzen be a Microsoft 365-fiókjába, és navigáljon a **Beállítások** \&gt; **Testreszabás** \&gt; **Fejlesztői erőforrások** \&gt; A **példány webes API-ját (a szolgáltatás gyökerének URL-címe)** másolja az itt `https://testdrive.crm.dynamics.com/api/data/v9.0`található URL-címet (például:).

- **Szerepkör neve** (kötelező): adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét. Ezt a rendszer a tesztelési meghajtó (például a test-Drive-role) során rendeli hozzá a felhasználóhoz.

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technikai konfiguráció a Logic app Test Drive-hoz

Minden egyéni terméknek ezt a típusú tesztelési meghajtó-telepítési sablont kell használnia, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyszer használatos legördülő lista): jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. Ennek a legjobb módja a logikai alkalmazás helyi üzembe helyezése az Azure-előfizetésben a portálon, és annak ellenőrzése, hogy az megfelelően működik-e a kijelölés előtt.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező): adja meg, hogy legfeljebb hány ügyfél használhatja egyszerre a tesztelési meghajtót. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Tesztelési meghajtó időtartama** (kötelező): adja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező): adja meg az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nevét, amelybe a rendszer menti a logikai alkalmazás tesztelési meghajtóját.

- **Azure Logic App-alkalmazás neve** (kötelező): adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót hozzárendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének** megszüntetése (kötelező): adja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót felépíti az ügyfél befejezése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI tesztelési meghajtókhoz nem szükséges technikai konfiguráció

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. További információ a [Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) template-alkalmazások beállításáról.

## <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Ha a tesztelési meghajtót az Ön nevében szeretné üzembe helyezni, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést. (Power BI tesztelési meghajtók esetében nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges): adja meg az előfizetés azonosítóját, amely hozzáférést biztosít az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](https://docs.microsoft.com/azure/billing/billing-create-subscription) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztása esetén megjelenik az előfizetési azonosító (például `a83645ac-1234-5ab6-6789-1h234g764ghty`).

- **Azure ad-bérlő azonosítója** (kötelező): adja meg a Azure Active Directory (ad) [bérlői azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok** elemet, majd keresse meg a felsorolt **könyvtár azonosítószámát** (például `50c464d3-4930-494c-963c-1e951d15360e`:). A szervezet bérlői AZONOSÍTÓját a tartománynév URL-címével is megkeresheti a [https://www.whatismytenantid.com](https://www.whatismytenantid.com)következő helyen:.

- **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges): adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad** -alkalmazás azonosítója (kötelező): adja meg a Azure Active Directory (ad) [alkalmazás-azonosítóját](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd keresse meg a listában szereplő **alkalmazás** -azonosító `50c464d3-4930-494c-963c-1e951d15360e`számát (például:).

- **Azure ad-alkalmazás ügyfél-titka** (kötelező): adja meg az Azure ad-alkalmazás [ügyfél-titkos kulcsát](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok**lehetőséget, válassza az **új ügyfél titka**elemet, adja meg a leírást, válassza a **soha nem** **jár le**, majd a **Hozzáadás**lehetőséget. Ügyeljen rá, hogy az értéket másolja le. (Ne navigáljon el az oldalról, mielőtt ezt megtenné, különben nem lesz hozzáférése az értékhez.)

Mielőtt továbblép a következő szakaszra, ne felejtse el **menteni** .

## <a name="test-drive-listings-optional"></a>Tesztelési meghajtók listázása (nem kötelező)

A test Drive (tesztelési meghajtó) **lapon található** tesztelési **meghajtók listázása** lehetőség megjeleníti azokat a nyelveket (és piacokat), ahol a tesztelési meghajtó elérhető, jelenleg angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a tesztvezetés részleteit (Leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célokat, a felderített funkciókat, valamint minden olyan releváns információt, amellyel a felhasználó eldöntheti, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni.

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges): ismertesse, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező): részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók: videók hozzáadása** (nem kötelező): a videók a YouTube vagy a Vimeo webhelyre tölthetők fel, és a hivatkozás és a miniatűr képét (533 x 324 képpont) is feltölthetik, így az ügyfél megtekintheti az információk áttekintését, így könnyebben megismerheti a tesztelési meghajtót, beleértve az ajánlat funkcióinak sikeres használatát és az előnyeiket kiemelő forgatókönyveket.
  - **Név** (kötelező)
  - **URL-cím (csak YouTube vagy Vimeo)** (kötelező)
  - **Miniatűr (533 x 324 px)** – a képfájlnak png formátumúnak kell lennie.

A mezők befejezése után válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

[Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
