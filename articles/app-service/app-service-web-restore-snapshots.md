---
title: Alkalmazás visszaállítása pillanatképből
description: További információ arról, hogyan állíthatja vissza az alkalmazást pillanatképből. Az automatikus árnyékmásolatokkal a prémium szintű váratlan adatvesztés helyreállítása.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: b17a49535541c8f75f65cdbe9986a895427f3877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255151"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Alkalmazás visszaállítása az Azure-ban pillanatképből
Ez a cikk bemutatja, hogyan állíthat vissza egy alkalmazást az [Azure App Service-ben](../app-service/overview.md) egy pillanatképből. Az alkalmazás visszaállíthatja az alkalmazást egy korábbi állapotba az alkalmazás egyik pillanatképe alapján. Nem kell engedélyeznie a pillanatképek biztonsági mentését, a platform automatikusan elmenti az összes alkalmazás pillanatképét az adatok helyreállításához.

A pillanatképek növekményes árnyékmásolatok, és számos előnnyel járnak a rendszeres [biztonsági másolatokkal](manage-backup.md)szemben:
- A fájlzárolások miatt nincs fájlmásolási hiba.
- Nincs tárhelyméret korlátozása.
- Nincs szükség konfigurációra.

A pillanatképek visszaállítása **a prémium szintű** vagy magasabb szinten futó alkalmazások számára érhető el. Az alkalmazás bővítéséről az [Alkalmazás felskálázása az Azure-ban](manage-scale-up.md)című témakörben talál további információt.

## <a name="limitations"></a>Korlátozások

- A funkció jelenleg előzetes verzióban érhető el.
- Csak ugyanabba az alkalmazásba vagy az adott alkalmazáshoz tartozó tárolóhelyre állíthatja vissza.
- Az App Service leállítja a célalkalmazást vagy a céltárolóhelyet a visszaállítás közben.
- Az App Service három hónapnyi pillanatképet tart meg platformadat-helyreállítási célokra.
- Csak az elmúlt 30 nap pillanatképeit állíthatja vissza.
- Az App Service-környezetben futó App Services nem támogatja a pillanatképeket.
 

## <a name="restore-an-app-from-a-snapshot"></a>Alkalmazás visszaállítása pillanatképből

1. Az [Azure Portalon](https://portal.azure.com)az alkalmazás **Beállítások** lapján kattintson a **Biztonsági mentések** elemre a **Biztonsági mentések** lap megjelenítéséhez. Ezután kattintson a **Visszaállítás gombra** a **Snapshot(Preview)** szakaszban.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. A **Visszaállítás** lapon jelölje ki a visszaállítani kívánt pillanatképet.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Adja meg az alkalmazás-visszaállítás célját a **Visszaállítás célban.**
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Ha a Felülírás lehetőséget **választja,** az alkalmazás jelenlegi fájlrendszerében lévő összes adat törlődik és felülíródik. Mielőtt az **OK**gombra kattintana, győződjön meg arról, hogy ezt szeretné tenni.
   > 
   > 
      
   > [!Note]
   > A jelenlegi technikai korlátok miatt csak az azonos méretezési egységben lévő alkalmazásokat állíthatja vissza. Ez a korlátozás egy későbbi kiadásban megszűnik.
   > 
   > 
   
    A **Meglévő alkalmazás** kiválasztásával visszaállíthatja a tárolóhelyet. A beállítás használata előtt már létre kellett volna hoznia egy tárolóhelyet az alkalmazásban.

4. Dönthet úgy, hogy visszaállítja a hely konfigurációját.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kattintson az **OK** gombra.
