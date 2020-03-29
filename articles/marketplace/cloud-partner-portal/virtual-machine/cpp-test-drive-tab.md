---
title: A virtuálisgép-tesztmeghajtó lap az Azure Piactér felhőpartneri portálján
description: Ismerteti az Azure Marketplace virtuális gép ajánlatának létrehozásához használt Tesztmeghajtó lapot.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: d4fc8762e25825b21637b16b751d57a0dcbf369e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288801"
---
# <a name="virtual-machine-test-drive-tab"></a>Virtuálisgép tesztmeghajtó lapja

Az **Új ajánlat** lap **Tesztmeghajtó** lapja lehetővé teszi, hogy a leendő ügyfelek számára gyakorlati, saját irányítású bemutatót biztosítson a termék legfontosabb funkcióiról és előnyeiről, amelyeket egy szabványosított forgatókönyv mutat be.  A Test Drive a Test Drive-ot a Test Drive-ot támogató ajánlattípusok opcionális szolgáltatása.  A Test Drive-hoz a támogató eszközök megfelelő megvalósításához van szükség.  További információt az Azure Marketplace Test Drive című [cikkben talál.](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)  <!--TD: Replace with migrated version of Test Drive article! -->

A szolgáltatás engedélyezéséhez a **Tesztautó** lapon kattintson az **Igen** beállításra a **Tesztvezetés engedélyezése**lehetőségen.  A **Tesztvezetés** lapon megjelennek a szerkesztésre rendelkezésre álló mezők.  A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá.

![A Tesztmeghajtó lap az Új ajánlat űrlapon a virtuális gépekhez](./media/publishvm_007.png)


## <a name="field-values"></a>Mezőértékek

Az alábbi táblázat a mezők célját és tartalmát ismerteti.  A kötelező mezőket csillag (*) vádolja.


|    Mező                  |       Leírás                                                            |
|  ---------                |     ---------------                                                          |
|  *Részletek*   |  |
| **Leírás\***           | Áttekintést nyújt a Test Drive-forgatókönyvről. Ez a szöveg jelenik meg a felhasználó számára, miközben a Test Drive kiépítése folyamatban van. Ez a mező támogatja az alapvető HTML-kódot, ha formázott tartalmat szeretne biztosítani.  |
| **Használati utasítás\***           | Töltsön fel egy részletes felhasználói kézikönyvet (.pdf), amely segít a Test Drive-felhasználóknak megérteni, hogyan kell használni a megoldást.  |
| **Tesztvezetés bemutató videó** | Töltsön fel egy videót, amely bemutatja a megoldást.  Ha ezt a lehetőséget választottad, meg kell adnod egy nevet, egy URL-t a videóhoz (a YouTube-on vagy a Vimeo-n), valamint egy (533x324 képpontos) indexképet a videóhoz. |
| *Műszaki konfiguráció* |  |
| **példányszám\***             | Adja meg a régió elérhetőségét és a virtuálisgép-példány viszonylag rendelkezésre állását (további részletekért kattintson az információs ikonra).  <br/>A potenciális egyidejű tesztmeghajtó-munkamenetek nem léphetik túl az előfizetés kvótakorlátját.  Az előbbi kiszámítása a következőképpen történik: [Kijelölt régiók száma] x [Gyorspéldányok száma] + [Kijelölt régiók száma] x [Meleg példányok] + [Kijelölt régiók száma] x [Hideg példányok] |
| **A tesztvezetés időtartama\***   | A munkamenet maximális időtartama órákban. A Test Drive munkamenet automatikusan leáll ezen időtartam túllépése után.  |
|**Test Drive ARM sablon\***| Töltse fel a tesztmeghajtóhoz társított Azure Resource Manager-sablont. További információt a [Virtuálisgép-telepítési sablon átalakítása a Tesztmeghajtóhoz című témakörben talál.](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive) |
| **Hozzáférési adatok\***    | Az Azure Resource Manager hozzáférési és próbabejelentkezési adatok, egyszerű szövegként vagy egyszerű HTML-kódként írva. |
| *A Test Drive-alapú üzembe helyezési előfizetés részletei* |  |
| **Azure-előfizetés azonosítója\*** | A [Microsoft Azure portálra](https://ms.portal.azure.com) való bejelentkezéssel és a bal oldali menüsor **Előfizetések** elemére kattintva szerezhető be. (Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ennek az azonosítónak az űrlap `a83645ac-1234-5ab6-6789-1h234g764ghty`GUID azonosítójának kell lennie.|
| **Azure AD-bérlőazonosító\***    | Az Azure Active Directory bérlői azonosítója.  Beszerezhető a Microsoft [Azure portalra](https://ms.portal.azure.com) való bejelentkezéssel, majd a bal oldali menüsor **Azure Active Directory** könyvtárára, majd a középső menüsorban a **Tulajdonságok** elemre kattintva, majd a **címtárazonosító** másolása az űrlapról.  Ennek az azonosítónak guid azonosítónak is kell lennie.  Ha üres, akkor létre kell hoznia egy bérlői azonosítót a szervezet számára. |
| **Az Azure AD-alkalmazás azonosítója\***       | A regisztrált Azure virtuálisgép-megoldás azonosítója  |
| **Azure AD alkalmazáskulcs\***      | A regisztrált megoldás hitelesítési kulcsa |
|   |   |


## <a name="next-steps"></a>További lépések

A következő [Piactér](./cpp-marketplace-tab.md) lapon marketing- és jogi információkat fog megadni a megoldásról.
