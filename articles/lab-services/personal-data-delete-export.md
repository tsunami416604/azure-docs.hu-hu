---
title: Törlése és a személyes adatok exportálása az Azure DevTest Labs |} A Microsoft Docs
description: Ismerje meg, hogyan törlése és a személyes adatok exportálása az Azure DevLast Labs szolgáltatás az Ön kötelezettségei alatt az általános adatvédelmi rendelet (GDPR) támogatásához.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51642434"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportálja, vagy a személyes adatok törlése az Azure DevTest Labs szolgáltatásban
Ez a cikk lépéseit, törlése és a személyes adatok exportálása az Azure DevTest Labs szolgáltatás. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Milyen személyes adatokat gyűjt DevTest Labs?
DevTest Labs a személyes adatok két fő darab gyűjti össze a felhasználó. Ezek: felhasználó e-mail címének és a felhasználói objektum azonosítója. Ezt az információt, kritikus fontosságú, a szolgáltatás üzemelő labor-rendszergazdák és a tesztkörnyezet felhasználók alkalmazásszolgáltatások biztosítása érdekében.

### <a name="user-email-address"></a>Felhasználó e-mail-címe
DevTest Labs a felhasználó e-mail címének automatikus leállítási e-mail értesítések küldéséhez felhasználók labor használja. Az e-mailben értesíti a felhasználókat a gép leállítása folyamatban. A felhasználó elhalaszthatja, vagy hagyja ki a Leállítás, ha ehhez eljárhatnak. A lab szintjén, vagy a virtuális gép szintjén az e-mail-cím konfigurálása.

**A beállítás e-mailt a labor létrehozása:**

![A lab-szintű beállítás e-mailt](./media/personal-data-delete-export/lab-user-email.png)

**A virtuális gép beállítás e-mailt:**

![A Virtuálisgép-szintű beállítás e-mailt](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Felhasználói objektum azonosítója
DevTest Labs hónap hónapos over energiaköltségi trendjeinek és a költségek erőforrás adatai a labor-rendszergazdák megjelenítése használ a felhasználói objektum azonosítója. Lehetővé teszi számukra, hogy nyomon követni a költségeket, és kezelheti a küszöbértékeket a labor. 

**Becsült költség trend az aktuális hónap:**
![becsült költség trend az aktuális hónap](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Hónap elejétől számított költség becslése a erőforrás:**
![becsült hónap elejétől számított költségek erőforrás szerint](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Miért kell a személyes adatok?
A DevTest Labs szolgáltatás üzemeltetési célra használja a személyes adatok. Ezeket az adatokat, kritikus fontosságú, a szolgáltatás számára a legfontosabb funkciókat. Ha a felhasználó e-mail címének adatmegőrzési állít be, labor felhasználók nem kapnak időben történő automatikus leállítási e-mail-értesítések után e-mail-címükkel törlődik a rendszerből. Ehhez hasonlóan a labor-rendszergazda nem tekintheti meg havi over-havi költségek időbeli alakulását, és azok fejlesztőlaborokban lévő gépek erőforrás-azonosítók törlése felhasználóobjektum adatmegőrzési-alapú költséget. Ezért ezek az adatok őrzi meg, amíg a felhasználó erőforrás aktív a laborban szükséges.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hogyan lehet a rendszer elfelejti személyes adataim?
Labor felhasználóként Ha például a személyes adatokat, törlése, megteheti a Lab-ben a kapcsolódó erőforrás törlésével. A DevTest Labs szolgáltatás anonymizes a törölt személyes adatokat a felhasználó törlése után 30 napig.

Például ha törli a virtuális Gépet, vagy eltávolítja az e-mail-címét, a DevTest Labs szolgáltatás anonymizes ezeket az adatokat az erőforrás törlése után 30 napig. A 30 napos adatmegőrzési szabály törlése után győződjön meg arról, hogy biztosítunk egy pontos hónap over-havi költség leképezése a labor-rendszergazdának.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hogyan kérhetek-exportálás a személyes adataimat?
Labor felhasználóként kérheti a személyes adatokat, amely tárolja a DevTest Labs szolgáltatás exportálását. Az exportálási kérelem, lépjen a **személyes adatok** lehetőséget a **áttekintése** a labor létrehozása lap. Válassza ki a **exportálási kérelem** gomb elindít egy letölthető excel-fájlt a labor rendszergazda storage-fiók létrehozása. Ott a labor-rendszergazdát, hogy az adatok megtekintésére.

1. Válassza ki **személyes adatok** a bal oldali menüben. 

    ![Személyes adatok lap](./media/personal-data-delete-export/personal-data-page.png)
2. Válassza ki a **erőforráscsoport** , amely tartalmazza a tesztkörnyezetben.

    ![Erőforráscsoport kiválasztása](./media/personal-data-delete-export/select-resource-group.png)
3. Válassza ki a **tárfiók** az erőforráscsoportban.
4. Az a **tárfiók** lapon jelölje be **Blobok**.

    ![Blobok csempe kiválasztása](./media/personal-data-delete-export/select-blobs-tile.png)
5. Válassza ki a tárolót nevű **labresourceusage** a tárolók listájában.

    ![Blobtároló kiválasztása](./media/personal-data-delete-export/select-blob-container.png)
6. Válassza ki a **mappa** a labor elnevezve. Tapasztalni **csv** -fájlokat **lemezek** és **virtuális gépek** ebben a mappában a lab-ben. A csv-fájlok letöltéséhez, a tartalmát egy hozzáférést kér a lab-felhasználó szűrése és osztania azt velük.

    ![CSV-fájl letöltése](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Tesztkörnyezet szabályzatainak beállítása](devtest-lab-get-started-with-lab-policies.md)
- [Gyakori kérdések](devtest-lab-faq.md)
