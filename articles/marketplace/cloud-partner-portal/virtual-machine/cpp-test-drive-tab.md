---
title: Virtuális gép Test Drive lapján a Cloud Partner portálra, az Azure Marketplace-en
description: A Test Drive lapon, az Azure piactér-beli Virtuálisgép-ajánlat létrehozásakor használt ismerteti.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 31c7968d0d96a44ff166444f73807e0ccb5dc583
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938004"
---
# <a name="virtual-machine-test-drive-tab"></a>Virtuális gép Test Drive lap

A **Test Drive** lapján a **új ajánlat** lap lehetővé teszi, hogy a termék főbb funkciók és előnyök, mutatja be a gyakorlati, önálló irányítású bemutatója a leendő ügyfelek biztosít egy Ez a forgatókönyv szabványos.  Próbálja ki egy olyan opcionális szolgáltatás, az ajánlat esetében, amelyek támogatják a Test Drive.  Test Drive szükséges támogató eszközök megfelelően implementálni.  További információkért tekintse meg a cikket [Azure Marketplace-en Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Ez a funkció engedélyezéséhez a **Test Drive** lapra, majd a **Igen** beállítást **engedélyezése egy Test Drive**.  A **Test Drive** lap megjeleníti a mezők érhető el szerkesztésre.  A hozzáfűzött csillag (*) az a mező nevét jelzi, hogy szükséges.

![Virtuális gépek az új ajánlat űrlap meghajtó lap tesztelése](./media/publishvm_007.png)


## <a name="field-values"></a>Mezőértékek

A következő táblázat ismerteti a cél és ezek a mezők tartalmát.  Kötelező mezőt csillag (*) rendszer feltehetően.


|    Mező                  |       Leírás                                                            |
|  ---------                |     ---------------                                                          |
|  *Részletek*   |  |
| **Leírás\***           | Adja meg a Tesztverziós forgatókönyv áttekintése. Ez a szöveg a felhasználó nem jelenik meg, amíg a Test Drive kiépítése folyamatban van. Ez a mező alapszintű HTML kódot támogatja, ha lehetővé szeretné tenni formátumú tartalmat.  |
| **Felhasználói kézikönyv\***           | Töltse fel a részletes felhasználói útmutatója (.pdf), amely segít a Test Drive-felhasználóknak a megoldás használatához.  |
| **Test Drive – bemutató videó** | Töltsön fel egy videót, amely bemutatja a megoldáshoz.  Ha ezt a lehetőséget választja, meg kell adnia egy nevet, a videót (YouTube vagy Vimeo), és a egy (533 x 324 képpont) Miniatűr URL-címet a videó. |
| *Technikai konfiguráció* |  |
| **példányok\***             | Adja meg a régiók rendelkezésre állása, és viszonylag rendelkezésre állását a virtuálisgép-példány (kattintson a további részleteket az információs ikon).  <br/>Lehetséges egyidejű Test Drive-munkamenetek nem haladhatja meg az előfizetés kvótája.  A korábbi számítjuk ki, hogy: [száma / régiókban kijelölt] x [gyakori elérésű példányok] + [száma / régiókban kijelölt] [meleg példányok] x [száma / régiókban kijelölt] + [hideg példányok] x |
| **Test Drive időtartama\***   | Maximális munkamenet időtartama (óra). A Test Drive-munkamenet automatikusan leáll, ez az időtartam túllépése után.  |
|**Teszt meghajtó ARM-sablon\***| Töltse fel a Test Drive társított Azure Resource Manager-sablon. További információkért lásd: [alkalmazásán át a virtuális gép központi telepítési sablont a Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Hozzáférési információk\***    | Az Azure Resource Manager-hozzáférés és próba bejelentkezési adatait, egyszerű szöveges vagy egyszerű HTML-ként. |
| *Tesztelés üzembe helyezési előfizetés részletei* |  |
| **Azure Subscription Id\*** | Lekérheti, bejelentkezik a [Microsoft Azure-portálon](https://ms.portal.azure.com) kattintva **előfizetések** a bal oldali menüsáv a. (Példa: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ez az azonosító egy GUID Azonosítót a következő formában kell `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Az Azure AD-bérlő azonosítója\***    | Az Azure Active Directory-bérlő azonosítója.  Szerezhető be ehhez jelentkezzen be a [Microsoft Azure-portálon](https://ms.portal.azure.com) kattintva **Azure Active Directory** meg a bal oldali menüsáv, majd kattintson **tulajdonságok** a középső menüsávon a Ezután másolja a **címtár-azonosító** az űrlap.  Ez az azonosító is lehet egy GUID Azonosítót.  Ha üres, majd létre kell hoznia egy bérlői azonosító a szervezet számára. |
| **Azure AD App Id\***       | A regisztrált Azure VM-megoldás azonosítója  |
| **Az Azure AD alkalmazás-kulcs\***      | Az ajánlott megoldás a hitelesítési kulcs |
|   |   |


## <a name="next-steps"></a>További lépések

A következő [Marketplace](./cpp-marketplace-tab.md) lapon, marketinges és jogi információkkal szolgálnak a megoldásról.
