---
title: Üzemeltetett teszt meghajtó | Azure piactér
description: A piactéren üzemeltetett tesztkörnyezet karbantartásának beállítása
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 67d8421b2e545c951dcbc3280a306514e4b14897
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030048"
---
# <a name="hosted-test-drive"></a>Szolgáltatott tesztverzió

Az üzemeltetett tesztvezetés eltávolítja a Microsoft üzemeltetési szolgáltatásának összetettségét, és karbantartja azt a szolgáltatást, amely végrehajtja a tesztelési meghajtó felhasználó általi üzembe helyezését és megszüntetését. Ez a cikk azoknak a kiadóknak szól, akik rendelkeznek a AppSource, vagy újat készítenek, és egy üzemeltetett tesztelési meghajtót szeretnének kínálni, amely a Dynamics 365 for Customer engagement, a Dynamics 365 for Finance and Operations vagy a Dynamics 365 Business Central szolgáltatáshoz kapcsolódik. például.

## <a name="how-to-publish-a-test-drive"></a>A Test Drive közzététele

Navigáljon a meglévő ajánlathoz, vagy hozzon létre egy új ajánlatot.

Válassza ki a test Drive (tesztelés) lehetőséget az oldalsó menüből.

Válassza a \'Yes @ no__t-1 lehetőséget a \'Enable egy Test Drive @ no__t-3 kapcsolót.

Adja meg a következő mezőket a \'Details @ no__t-1 szakaszban.

- **Leírás**: Adja meg a tesztelési meghajtó áttekintését. Ez a szöveg jelenik meg a felhasználó számára a tesztelési meghajtó üzembe helyezésekor. Ez a mező támogatja a HTML-t, ha formázott tartalmat szeretne megadni.
- **Felhasználói kézikönyv**: Töltse fel a részletes felhasználói kézikönyvet (. pdf típusú fájl), amely segítséget nyújt a felhasználók számára az alkalmazás használatának megismerésében.
- A **Test Drive bemutató videója**: Igény szerint feltöltheti az alkalmazást bemutató videót.

Adja meg a AppSource engedélyt a test Drive-felhasználók kiépítéséhez és megszüntetéséhez a bérlőn az [itt](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)található utasítások alapján.

Ebben a lépésben létrehozza a \'Azure AD-alkalmazás azonosítójának @ no__t-1 és \'Azure AD App Key @ no__t-3 értékeket alább.

Adja meg a következő mezőket a \'Technical Configuration @ no__t-1 szakaszban:

- **A tesztelési meghajtó típusa**: Válassza a @no__t 0Microsoft (például a Dynamics 365 for Customer engagement) lehetőséget. Ez azt jelzi, hogy a Microsoft fogja üzemeltetni és karbantartani a szolgáltatást, amely végrehajtja a tesztelési meghajtó felhasználó általi üzembe helyezését és megszüntetését.
- **Párhuzamos tesztelési meghajtók maximális**száma: Állítsa be ezt a mezőt azon egyidejű felhasználók számára, akik egy adott időpontban aktív tesztelési meghajtóval rendelkezhetnek. Minden felhasználó felhasznál egy Dynamics-licencet, amíg a tesztvezetés aktív, ezért meg kell győződnie arról, hogy legalább ennyi Dynamics-licenc áll rendelkezésre a tesztelési meghajtó felhasználói számára. A 3-5 javasolt értéke.
- **Teszt meghajtójának időtartama (óra)** : Állítsa be ezt a mezőt arra az időtartamra, ameddig a felhasználók tesztelési meghajtója aktív lesz. Ennyi óra elteltével a rendszer kiépíti a felhasználót a bérlőtől. Az alkalmazás összetettségétől függően a 2-24 óra ajánlott értéknek kell lennie. A felhasználó mindig kérhet egy másik tesztvezetés, ha elfogynak az idő, és újra el szeretné érni a teszt meghajtót.
- **Példány URL-címe**: Adja meg azt az URL-címet, amelyet a test Drive-felhasználó kezdetben fog navigálni a tesztelési meghajtó indításakor. Ez általában annak a Dynamics 365-példánynak az URL-címe, amelyen az alkalmazás és a mintaadatok telepítve vannak. Példa értéke: https: \//testdrive. CRM. Dynamics. com
- **Azure ad-bérlő azonosítója**: Adja meg a Dynamics 365-példányhoz tartozó Azure-bérlő AZONOSÍTÓját. Az érték lekéréséhez jelentkezzen be Azure Portalre, és navigáljon a \'Azure Active Directory @ no__t-1 @ no__t-2 @ no__t-3 elemre, majd válassza a tulajdonságok lehetőséget a menüből a panel-\> másolja a címtár-azonosítót. Példa értéke: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure ad alkalmazás azonosítója**: A 7. lépésben létrehozott Azure AD alkalmazás azonosítója: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure ad alkalmazás kulcs**: A 7. lépésben létrehozott Azure AD alkalmazás titkos kulcsa: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure ad-bérlő neve**: Adja meg az Azure-bérlő nevét a Dynamics 365-példányhoz. Használja a \<tenantname. \>onmicrosoft.com formátumot. Példa értéke: testdrive.onmicrosoft.com
- **Példány webes API URL-címe**: Adja meg a Dynamics 365-példány webes API URL-címét. Ezt az értéket lekérheti, ha bejelentkezik a Microsoft Dynamics 365-példányba, és megkeresi a Setting-\> customization-\> fejlesztői erőforrás-\> példány webes API-ját (másolja ezt az URL-t). Példa értéke: https: \//testdrive. CRM. Dynamics. com/API/Value/v 9.0 
- **Szerepkör neve**: Adja meg a tesztelési meghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör nevét. Ez a szerepkör lesz hozzárendelve a felhasználókhoz a tesztelési meghajtón. Példa értéke: testdriverole

## <a name="next-steps"></a>További lépések

Ha készen áll az ajánlat **közzétételére** , az alkalmazás minősítésének ellenőrzése után **megtekintheti** az ajánlatát. Indítsa el a tesztelési meghajtót a felhasználói felületen, és ellenőrizze, hogy a tesztelési meghajtók megfelelően futnak-e. Ha már elégedett az előzetes verzió ajánlatával, most itt az ideje, hogy **élj!**
