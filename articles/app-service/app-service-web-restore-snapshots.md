---
title: Állítsa vissza biztonsági másolatból – az Azure App Service-ben
description: Ismerje meg, hogyan állíthatja vissza az alkalmazás egy pillanatképből.
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
ms.custom: seodec18
ms.openlocfilehash: ab1ae63352e66da557cad49bf5f320b577055877
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726561"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Az Azure-beli alkalmazás visszaállítása pillanatképből
Ez a cikk bemutatja, hogyan állíthatja vissza egy alkalmazás a [Azure App Service](../app-service/overview.md) egy pillanatképből. Az alkalmazás visszaállíthatja egy korábbi állapotát, az alkalmazás a pillanatképek alapján. Nem kell a pillanatképek biztonsági mentés engedélyezése, a platform automatikusan menti a helyreállítási célból minden alkalmazás pillanatképét.

Pillanatképeket növekményes pillanatképek és a normál felett több előnye is van [biztonsági mentések](manage-backup.md):
- Fájl másolása hibák fájl zárolása miatt.
- Storage mérete korlátozás nélkül.
- Nem szükséges konfiguráció.

A pillanatképek helyreállítása érhető el futó alkalmazásokra **prémium** szint vagy újabb verziója. Az alkalmazás vertikális felskálázásával kapcsolatos további információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md).

## <a name="limitations"></a>Korlátozások

- A szolgáltatás jelenleg előzetes verzióban érhető el.
- Ugyanazt az alkalmazást vagy egy adott alkalmazáshoz tartozó tárhely csak visszaállítása.
- App Service-ben a cél alkalmazásban és a céltárolóhelyen megadott leállítja a visszaállítás végrehajtása közben.
- Az App Service biztosítja, hogy három hónapnyi pillanatképek tartozó platform adat-helyreállítási céllal.
- A pillanatképek csak állíthatja vissza az elmúlt 30 napra vonatkozóan.
 

## <a name="restore-an-app-from-a-snapshot"></a>Alkalmazás visszaállítása pillanatképből

1. Az a **beállítások** az alkalmazás oldalán a [az Azure portal](https://portal.azure.com), kattintson a **biztonsági mentések** megjelenítéséhez a **biztonsági mentések** lap. Kattintson a **visszaállítása** alatt a **Snapshot(Preview)** szakaszban.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Az a **visszaállítása** lapra, jelölje be a pillanatkép visszaállítása.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Adja meg a cél az alkalmazás a visszaállításhoz a **visszaállítási célhelyének**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Ha úgy dönt, **felülírás**minden, az alkalmazás jelenlegi fájlrendszer-ben meglévő adatok törölve lesz, és felül. Gombra való kattintás előtt **OK**, győződjön meg arról, hogy mit szeretne tenni.
   > 
   > 
      
   > [!Note]
   > Jelenlegi technikai korlátok miatt csak visszaállíthatja a azonos skálázási egység alkalmazásaival. Ez a korlátozás egy későbbi kiadásban törlődni fog.
   > 
   > 
   
    Választhat **meglévő alkalmazás** állíthatja vissza a tárolóhelyet. Mielőtt ezt a beállítást használja, érdemes már létrehozott egy tárolóhely az alkalmazásban.

4. Ha szeretné, a webhely-konfiguráció visszaállítása.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kattintson az **OK** gombra.
