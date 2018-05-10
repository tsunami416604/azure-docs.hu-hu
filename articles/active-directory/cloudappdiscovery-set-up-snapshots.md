---
title: A Cloud App Discovery pillanatkép jelentések létrehozása az Azure Active Directoryban |} Microsoft Docs
description: Információk keresése és alkalmazások kezelése az a Cloud App Discovery, milyen előnyökkel és annak működéséről.
services: active-directory
keywords: a cloud app discovery alkalmazások kezelése
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: ad4591223c72893a4488f5515d8ceb83e0d7f8cf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>A Cloud App Discovery pillanatkép jelentések létrehozása

Mielőtt beállítaná a automatikus naplógyűjtő, a napló manuális feltöltése, és lehetővé teszik a Cloud App Security elemzéséhez. Ha a napló még nem rendelkezik, és meg szeretné tekinteni, mit kell hasonlítania a napló mintát, az alábbi eljárással minta naplófájlt, tekintse meg, mi a napló kellene tűnik letöltéséhez.

## <a name="to-create-a-snapshot-report"></a>Pillanatkép-jelentés létrehozása

1. Gyűjtsön naplófájlokat azokról a tűzfalakról és proxykról kiszolgáló, amelyen keresztül a szervezetében lévő felhasználók férnek hozzá az internethez. Gyűjtse össze, hogy a felhasználói tevékenység a szervezet reprezentatív hányszor csúcs forgalom során készít naplókat.
2. A [a Cloud App Security menüsoron](https://portal.cloudappsecurity.com), jelölje be **felderítési**, majd **pillanatkép-jelentés létrehozása**.
  
  ![Új pillanatkép-jelentés létrehozása](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Adjon meg egy **jelentés neve** és egy **leírás**.
    
  ![Új pillanatkép-jelentés](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Válassza ki a **adatforrás** amelyen el szeretné a naplófájlok feltöltéséhez.
5. Győződjön meg arról, hogy megfelelően van formázva megfelelően a minta letöltheti a naplóformátumban ellenőrzése. Kattintson a **nézet, és ellenőrizze** majd **letöltési napló minta**. Győződjön meg arról, hogy a kompatibilis megadott mintával majd hasonlítsa össze a naplót.
  
  ![Naplóformátum ellenőrzése](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > Az FTP-minta formátum a pillanatképek támogatott, és automatikus feltöltés közben syslog csak az automatikus feltöltés esetén támogatott. Egy mintanaplót FTP egy mintanaplót letöltése tölti le.
6. **Válassza ki a forgalmi naplók** , amelyet szeretne feltölteni. Egyszerre legfeljebb 20 fájlt tölthet. Tömörített és tömörített fájlok is támogatottak.
  
7. Kattintson a **Create** (Létrehozás) gombra. Ha befejeződött a feltöltés, eltarthat egy ideig, hogy értelmezése és elemzése. Ha igen, a Cloud App Discovery e-mailben értesítést küld, amikor azok készen áll.

8. Válassza ki **pillanatkép készült jelentések kezelése** majd válassza a pillanatfelvétel.
  
  ![Pillanatkép-jelentések kezelése](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>További lépések

* [Az Azure AD Cloud App Discovery használatának megkezdése](cloudappdiscovery-get-started.md)
* [A folyamatos jelentési automatikus naplófeltöltés beállítása](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Egyéni naplóelemző használata](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
