---
title: Üzemeltetett teszt meghajtó | Azure piactér
description: A piactéren üzemeltetett tesztkörnyezet karbantartásának beállítása
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2ce43402fb283cce09687bc7226770731f1bae0d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827518"
---
# <a name="hosted-test-drive"></a>Szolgáltatott tesztverzió

Az üzemeltetett tesztvezetés eltávolítja a Microsoft üzemeltetési szolgáltatásának összetettségét, és karbantartja azt a szolgáltatást, amely végrehajtja a tesztelési meghajtó felhasználó általi üzembe helyezését és megszüntetését. Ez a cikk azoknak a kiadóknak szól, akik rendelkeznek a AppSource, vagy újat készítenek, és egy üzemeltetett tesztelési meghajtót szeretnének kínálni, amely a Dynamics 365 for Customer engagement, a Dynamics 365 for Finance and Operations vagy a Dynamics 365 Business Central szolgáltatáshoz kapcsolódik. például.

## <a name="how-to-publish-a-test-drive"></a>Tesztelési meghajtó közzététele

Navigáljon a meglévő ajánlathoz, vagy hozzon létre egy új ajánlatot.

Válassza ki a test Drive (tesztelés) lehetőséget az oldalsó menüből.

Válassza a \'igen\' lehetőséget, hogy \'engedélyezzen egy tesztelési meghajtó\' beállítást.

Adja meg a következő mezőket a \'details\' szakaszban.

- **Leírás**: adja meg a tesztelési meghajtó áttekintését. Ez a szöveg jelenik meg a felhasználó számára a tesztelési meghajtó üzembe helyezésekor. Ez a mező támogatja a HTML-t, ha formázott tartalmat szeretne megadni.
- **Felhasználói kézikönyv**: töltsön fel egy részletes felhasználói kézikönyvet (Type. PDF fájl), amely segít az alkalmazás használatának megértésében.
- A **Test Drive bemutató videója**: opcionálisan feltöltheti az alkalmazást bemutató videót.

Adja meg a AppSource engedélyt a test Drive-felhasználók kiépítéséhez és megszüntetéséhez a bérlőn az [itt](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)található utasítások alapján.

Ebben a lépésben létrehozza a \'Azure AD alkalmazás ID\' és \'Azure AD alkalmazás kulcs\' az alább említett értékeket.

Adja meg a következő mezőket a \'technikai konfiguráció\' szakaszban:

- **A tesztvezetés típusa**: válassza \'a Microsoft által üzemeltetett (például a Dynamics 365 for Customer engagement) lehetőséget. Ez azt jelzi, hogy a Microsoft fogja üzemeltetni és karbantartani a szolgáltatást, amely végrehajtja a tesztelési meghajtó felhasználó általi üzembe helyezését és megszüntetését.
- **Párhuzamos tesztelési meghajtók maximális**száma: állítsa be ezt a mezőt azon egyidejű felhasználók számára, akik egy adott időpontban aktív tesztelési meghajtóval rendelkezhetnek. Minden felhasználó felhasznál egy Dynamics-licencet, amíg a tesztvezetés aktív, ezért meg kell győződnie arról, hogy legalább ennyi Dynamics-licenc áll rendelkezésre a tesztelési meghajtó felhasználói számára. A 3-5 javasolt értéke.
- **Teszt meghajtójának időtartama (óra)** : állítsa be ezt a mezőt azon órák számára, ameddig a felhasználók tesztelési meghajtója aktív lesz. Ennyi óra elteltével a rendszer kiépíti a felhasználót a bérlőtől. Az alkalmazás összetettségétől függően a 2-24 óra ajánlott értéknek kell lennie. A felhasználó mindig kérhet egy másik tesztvezetés, ha elfogynak az idő, és újra el szeretné érni a teszt meghajtót.
- **Példány URL-címe**: adjon meg egy URL-címet, amelyet a test Drive-felhasználó kezdetben fog navigálni a tesztvezetés indításakor. Ez általában annak a Dynamics 365-példánynak az URL-címe, amelyen az alkalmazás és a mintaadatok telepítve vannak. Példa értéke: https: \//testdrive. CRM. Dynamics. com
- **Azure ad-bérlő azonosítója**: adja meg a Dynamics 365-példányhoz tartozó Azure-bérlő azonosítóját. Az érték lekéréséhez jelentkezzen be Azure Portalre, és navigáljon a \'Azure Active Directory\' -\> a tulajdonságok menüből válassza ki a tulajdonságokat,\> másolja a címtár-azonosítót. Példa értéke: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure ad alkalmazás azonosítója**: a 7. lépésben létrehozott Azure ad alkalmazás azonosítója. \ példa értéke: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure ad alkalmazás kulcs**: a 7. lépésben létrehozott Azure ad alkalmazás titka. \ példa értéke: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Azure ad-bérlő neve**: adja meg az Azure-bérlő nevét a Dynamics 365-példányhoz. Használja a \<tenantname. \>onmicrosoft.com formátumot. Példa értéke: testdrive.onmicrosoft.com
- **Példány webes API URL-címe**: adja meg a Dynamics 365-példány webes API URL-címét. Ezt az értéket lekérheti, ha bejelentkezik a Microsoft Dynamics 365-példányba, és megkeresi a Setting-\> customization-\> fejlesztői erőforrás-\> példány webes API-ját (másolja ezt az URL-t). Példa értéke: https: \//testdrive. CRM. Dynamics. com/API/Value/v 9.0 
- **Szerepkör neve**: adja meg a tesztelési meghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör nevét. Ez a szerepkör lesz hozzárendelve a felhasználókhoz a tesztelési meghajtón. Példa értéke: testdriverole

## <a name="next-steps"></a>További lépések

Ha készen áll az ajánlat **közzétételére** , az alkalmazás minősítésének ellenőrzése után **megtekintheti** az ajánlatát. Indítsa el a tesztelési meghajtót a felhasználói felületen, és ellenőrizze, hogy a tesztelési meghajtók megfelelően futnak-e. Ha már elégedett az előzetes verzió ajánlatával, most itt az ideje, hogy **élj!**
