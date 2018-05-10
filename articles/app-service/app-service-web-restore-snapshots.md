---
title: Alkalmazás visszaállítása az Azure-ban
description: Ismerje meg, hogyan lehet visszaállítani az alkalmazást egy pillanatképből.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.openlocfilehash: e1ae8fcc30323c865aa96937f43054515f293394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Az alkalmazások az Azure-ban visszaállítás egy pillanatképből
A cikkből megtudhatja, hogyan lehet visszaállítani az alkalmazásban [Azure App Service](../app-service/app-service-web-overview.md) egy pillanatképből. Az alkalmazás visszaállíthatja egy korábbi állapotára, az alkalmazás pillanatképeket valamelyike alapján. Nem kell ahhoz, hogy a pillanatképek biztonsági mentés, a platform automatikusan menti a helyreállítási célokra minden alkalmazás pillanatképet.

A pillanatképek növekményes árnyékmásolatok, és rendszeres több előnyöket biztosítanak [biztonsági mentések](web-sites-backup.md):
- Nem következnek be fájlmásolási hibák miatt zárolásokat.
- Tároló mérete korlátozás nélkül.
- Nem szükséges konfiguráció.

A pillanatképek visszaállítása a rendszer futó alkalmazások számára elérhető **prémium** réteg vagy újabb verzióját. Az alkalmazás vertikális felskálázásával kapcsolatos információkért lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).

## <a name="limitations"></a>Korlátozások

- A funkció jelenleg előzetes verzió.
- Csak visszaállíthatja ugyanahhoz az alkalmazáshoz, vagy egy adott alkalmazáshoz tartozó tárolóhelyre.
- App Service leállítja a cél alkalmazás vagy a cél-tárolóhely a visszaállítás végrehajtása közben.
- App Service tartja a három hónapnyi pillanatképek platform adatok helyreállítási célokra.
- Visszaállíthatja a pillanatképek csak az elmúlt 30 napban.
 

## <a name="restore-an-app-from-a-snapshot"></a>Egy alkalmazás visszaállítás egy pillanatképből

1. A a **beállítások** az alkalmazást a oldalán a [Azure-portálon](https://portal.azure.com), kattintson a **biztonsági mentések** megjelenítéséhez a **biztonsági mentések** lap. Kattintson a **visszaállítása** alatt a **Snapshot(Preview)** szakasz.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Az a **visszaállítása** lapon, válassza ki a pillanatkép visszaállítása.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Adja meg az alkalmazás visszaállításához **visszaállítási célhelyének ellenőrzése**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Ha úgy dönt, **felülírása**minden, az alkalmazás aktuális fájlrendszer meglévő adatok törlése és felül. Kattintás előtt **OK**, győződjön meg arról, hogy mit kíván tenni.
   > 
   > 
      
   > [!Note]
   > A jelenlegi technikai korlátozások miatt csak visszaállíthatja alkalmazások azonos skálázási egységben. Ez a korlátozás egy későbbi kiadásban fog szűnni.
   > 
   > 
   
    Kiválaszthatja **meglévő App** tárhely visszaállításához. Mielőtt ezt a beállítást használja, kell már létrehozott egy tárolóhely az alkalmazásban.

4. Ha szeretné, állítsa vissza a hely konfigurációjától.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kattintson az **OK** gombra.
