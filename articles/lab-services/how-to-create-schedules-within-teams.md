---
title: Azure Lab Services-ütemtervek létrehozása a csapatokon belül
description: Ismerje meg, hogyan hozhat létre labor Services-ütemterveket a csapatokon belül.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946668"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Labor Services-ütemtervek létrehozása és kezelése a csapatokon belül

Az ütemtervek lehetővé teszik a tantermi laborok konfigurálását, hogy a laborban lévő virtuális gépek automatikusan elindulnak és leálljanak egy adott időpontban. Meghatározhat egy egyszeri vagy ismétlődő ütemtervet. Az alábbi eljárások egy osztályterem laborhoz tartozó ütemtervek létrehozását és kezelését ismertetik: 

A következő módon befolyásolja az ütemtervek a labor virtuális gépeket: 

- A sablon virtuális gépe nem szerepel az ütemtervekben. 
- Csak a hozzárendelt virtuális gépek vannak elindítva. Ez azt jelenti, hogy ha egy gépet nem igényel a végfelhasználó (tanuló), a gép nem indul el az ütemezett órákon. 
- Az összes virtuális gép (akár felhasználó által igényelt, akár nem) leáll a labor-ütemterv alapján. 

> [!IMPORTANT]
> A virtuális gépek ütemezett futási ideje nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>A laborhoz tartozó ütemterv létrehozása/szerkesztése/törlése

Hozzon létre egy ütemezett eseményt a laborhoz, hogy a laborban lévő virtuális gépek meghatározott időpontokban automatikusan elindulnak/leállnak. A korábban megadott felhasználói kvóta az egyes felhasználók számára az ütemezett időponton kívül hozzárendelt további idő. 

1. Váltson az **ütemezések** lapra, és válassza az **ütemezett esemény hozzáadása** lehetőséget az eszköztáron (az ablak tetején/bal oldalán). 
1. Állítsa be a következő paramétereket az ütemtervhez:
    1. Ellenőrizze, hogy a **standard** érték van-e kiválasztva az **esemény típusára**. A **csak indítás** gombra kattintva adhatja meg a virtuális gépek kezdési idejét. Ha csak a virtuális gépek leállítási idejét szeretné megadni, válassza a **Leállítás** lehetőséget. 
    1. A **kezdő dátum**meghatározása.
    1. Itt adhatja meg azt a **kezdési időpontot** , amikor a virtuális gépeket el szeretné indítani.
    1. Itt adhatja meg a **leállítási időt** , amikor a virtuális gépeket le kell állítani. 
    1. Adja meg az **időzónát** a megadott kezdési és befejezési időpontnál. 
    1. Az **ismétlés** szakaszban válassza ki a **hetente** vagy a **soha**lehetőséget. 
    1. A **Megjegyzések (nem kötelező)** mezőben adja meg az ütemterv leírását vagy megjegyzéseit. 
1. Kattintson a **Mentés** gombra. 

### <a name="view-schedules-in-calendar"></a>Ütemtervek megtekintése a naptárban

Megtekintheti a naptárban Kiemelt ütemezett dátumokat és időpontokat. Kattintson a jobb felső sarokban található **ma** gombra, hogy az aktuális dátumra váltson a naptárban. Kattintson a **balra mutató nyílra** az előző hétre és a **jobbra mutató nyílra** való váltáshoz a naptár következő hetében. 

### <a name="edit-a-schedule"></a>Ütemterv szerkesztése

Amikor kijelöl egy kiemelt ütemtervet a naptárban, a gombokkal **szerkesztheti** vagy **törölheti** az ütemtervet. 

Az **ütemezett esemény szerkesztése** lapon frissítheti az ütemezést, és a **Mentés**gombra kattinthat. 

### <a name="delete-a-schedule"></a>Ütemezés törlése

1. Az ütemterv törléséhez jelöljön ki egy kiemelt ütemtervet a naptárban, és válassza a Kuka ikon (Törlés) gombot:
1. Az **ütemezett esemény törlése** párbeszédpanelen válassza az **Igen** lehetőséget a törlés megerősítéséhez. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Automatikus leállítási és leválasztási beállítások

Az oldal tetején megjelenik egy hivatkozás, amely az automatikus **Leállítás** beállításaira mutat.

Több automatikus leállítási költség-vezérlési funkciót is engedélyezhet, hogy proaktív módon meggátolja a további költségeket, ha a virtuális gépeket nem aktívan használják. A következő három automatikus leállítási és leválasztási funkció kombinációja a legtöbb esetben megfogást nyújt, amikor a felhasználók véletlenül elhagyják a virtuális gépeket:
 
- A felhasználók automatikus leválasztása a virtuális gépekről, amelyeket az operációs rendszer tétlennek tekint.
- A virtuális gépek automatikus leállítása a felhasználók leválasztásakor.
- Automatikusan leállítja az elindított virtuális gépeket, de a felhasználók nem csatlakoznak.

További részletekért kattintson a beállítások elem melletti *információs* ikonra.

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból](how-to-get-started-create-lab-within-teams.md)
- [A labor Services felhasználói listájainak kezelése a csapatokból](how-to-manage-user-lists-within-teams.md)
- [Virtuálisgép-készlet kezelése a labor Servicesben a csapatoktól](how-to-manage-vm-pool-within-teams.md)
- [Hozzáférés egy virtuális géphez (Student View) a labor Servicesben a csapatoktól](how-to-access-vm-for-students-within-teams.md)