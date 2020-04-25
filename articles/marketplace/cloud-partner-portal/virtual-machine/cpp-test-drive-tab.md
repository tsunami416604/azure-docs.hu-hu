---
title: Virtuálisgép-tesztelési meghajtó lap az Azure Marketplace Cloud Partner Portal
description: Ismerteti az Azure Marketplace virtuálisgép-ajánlat létrehozásához használt tesztvezetés lapot.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: adac73d64feb6280c5043776249072e9f7595faa
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142986"
---
# <a name="virtual-machine-test-drive-tab"></a>Virtuális gép tesztelése lap

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az Azure-beli [virtuális gép létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

Az **új ajánlat** oldalának **tesztvezetés** lapja lehetővé teszi, hogy a leendő ügyfelek számára a termék legfontosabb funkcióit és előnyeit egy szabványosított forgatókönyvben mutatja be.  A test Drive egy választható szolgáltatás a test Drive-t támogató ajánlatok típusaihoz.  A tesztelési meghajtón a támogatási eszközök megfelelő megvalósítására van szükség.  További információt az [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)című cikkben talál.  <!--TD: Replace with migrated version of Test Drive article! -->

A funkció engedélyezéséhez **a tesztvezetés lapon kattintson** az **Igen** lehetőségre a **Test Drive engedélyezése**elemnél.  A **Test Drive (tesztvezetés** ) lapon a szerkesztéshez elérhető mezők láthatók.  A mező neve mezőben a hozzáfűzött csillag (*) érték azt jelzi, hogy szükség van rá.

![A teszt meghajtó lapja az új ajánlat űrlapján a virtuális gépekhez](./media/publishvm_007.png)


## <a name="field-values"></a>Mezőértékek

A következő táblázat a mezők célját és tartalmát ismerteti.  A kötelező mezőket csillag (*) alapján vádoljuk.


|    Mező                  |       Leírás                                                            |
|  ---------                |     ---------------                                                          |
|  *Részletek*   |  |
| **Leírás\***           | Áttekintést nyújt a test Drive-forgatókönyvről. Ez a szöveg jelenik meg a felhasználó számára a tesztelési meghajtó üzembe helyezésekor. Ez a mező támogatja az alapszintű HTML-kódot, ha formázott tartalmat szeretne megadni.  |
| **Felhasználói kézikönyv\***           | Töltsön fel egy részletes felhasználói kézikönyvet (. pdf), amely segítséget nyújt a felhasználóknak a megoldás használatának megismerésében.  |
| **A test Drive bemutató videója** | Töltsön fel egy videót, amely bemutatja a megoldást.  Ha ezt a lehetőséget választja, meg kell adnia egy nevet, egy URL-címet a videóhoz (amelyet a YouTube vagy a Vimeo tárol), valamint a videó (533x324 képpont) miniatűrjét. |
| *Technikai konfiguráció* |  |
| **példányszám\***             | A rendelkezésre álló régió elérhetőségének és a virtuálisgép-példány viszonylagos rendelkezésre állásának megadása (további részletekért kattintson az információ ikonra).  <br/>A lehetséges egyidejű tesztvezetés-munkamenetek nem haladhatják meg az előfizetéshez tartozó kvóta korlátját.  Az előző a következőképpen számítható ki: [kiválasztott régiók száma] x [forró példányok] + [kiválasztott régiók száma] x [meleg példányok] + [kiválasztott régiók száma] x [hideg példányok] |
| **Teszt meghajtójának időtartama\***   | Munkamenetek maximális időtartama (óra). A test Drive-munkamenet automatikusan leáll ezen időszak lejárta után.  |
|**Test Drive ARM-sablon\***| Töltse fel a tesztelési meghajtóhoz társított Azure Resource Manager sablont. További információ: [a virtuális gép központi telepítési sablonjának átalakítása a test Drive szolgáltatáshoz](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Hozzáférési információk\***    | Azure Resource Manager hozzáférés és a próbaverzió bejelentkezési adatait egyszerű szövegként vagy egyszerű HTML formátumban kell írni. |
| *Tesztelési meghajtó üzembe helyezésének előfizetése – részletek* |  |
| **Azure-előfizetés azonosítója\*** | Bejelentkezhet a [Microsoft Azure Portalba](https://ms.portal.azure.com) , és a bal oldali menüsorban található **előfizetések** elemre kattintva lehet bejelentkezni. (Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ennek az azonosítónak az űrlap `a83645ac-1234-5ab6-6789-1h234g764ghty`GUID azonosítójának kell lennie.|
| **Azure AD-bérlő azonosítója\***    | Azure Active Directory bérlő azonosítója.  Bejelentkezhet a [Microsoft Azure Portalba](https://ms.portal.azure.com) , majd a bal oldali menüsorban található **Azure Active Directory** elemre, majd a középső menüsor **Tulajdonságok** ELEMére, majd a **könyvtár azonosítójának** az űrlapról történő másolására.  Ennek az azonosítónak GUID azonosítónak is kell lennie.  Ha üres, akkor létre kell hoznia egy bérlői azonosítót a szervezet számára. |
| **Azure AD alkalmazás azonosítója\***       | A regisztrált Azure VM-megoldás azonosítója  |
| **Azure AD alkalmazás kulcs\***      | A regisztrált megoldás hitelesítési kulcsa |
|   |   |


## <a name="next-steps"></a>További lépések

A következő [piactér](./cpp-marketplace-tab.md) lapon a megoldásával kapcsolatos marketing-és jogi információkat is megadhat.
