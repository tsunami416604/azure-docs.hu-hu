---
title: Példa osztálytípusokra az Azure Lab Services-en | Microsoft dokumentumok
description: Bizonyos típusú osztályokat biztosít, amelyekhez az Azure Lab Services használatával laborokat állíthat be.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296727"
---
# <a name="class-types-overview---azure-lab-services"></a>Osztálytípusok áttekintése – Azure Lab Services

Az Azure Lab Services lehetővé teszi, hogy gyorsan állítsa be az osztálytermi laborkörnyezetben a felhőben. Ebben a szakaszban található cikkek útmutatást nyújtanak, hogyan állíthat be többtípusú tantermi laborok az Azure Lab Services használatával.

## <a name="deep-learning-in-natural-language-processing"></a>Mélytanulás a természetes nyelvi feldolgozásban

Az Azure Lab Services használatával beállíthat egy, a természetes nyelvi feldolgozás (NLP) mélytanulásra összpontosító labort. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) egy formája, amely lehetővé teszi a számítógépek fordítását, beszédfelismerését és más nyelvismereti képességeit. NLP-osztályt használó diákok egy Linux virtuális gépet (VM) kapnak, hogy megtanulják, hogyan alkalmazhatnak neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mélytanulási modellek fejlesztéséhez.

Az ilyen típusú laborok beállításáról az [Azure Lab Services használatával a természetes nyelvi feldolgozás ban a mélytanulásra összpontosító labor beállítása című témakörben](class-type-deep-learning-natural-processing.md)talál részletes információt.

## <a name="shell-scripting-on-linux"></a>Héjszkriptek használata Linuxon

Beállíthatja a labor tanítani shell scripting Linuxon. A parancsfájlok használata a rendszerfelügyelet hasznos része, amely lehetővé teszi a rendszergazdák számára az ismétlődő feladatok elkerülését. Ebben a mintaforgatókönyvben az osztály a hagyományos bash-parancsfájlokat és a továbbfejlesztett parancsfájlokat ismerteti. A továbbfejlesztett parancsfájlok olyan parancsfájlok, amelyek bash parancsokat és Rubint kombinálnak. Ez a megközelítés lehetővé teszi, hogy Ruby adatokat továbbítson, és lejátssza a parancsokat a rendszerhéjlal való interakcióhoz.

A diákok figyelembe ezeket a szkriptek osztályok kap egy Linux virtuális gép, hogy megtanulják az alapokat a Linux, és megismerkednek a bash shell scripting. A Linux virtuális gép jön a távoli asztali hozzáférés engedélyezve van, és [gedit](https://help.gnome.org/users/gedit/stable/) és [Visual Studio Code](https://code.visualstudio.com/) szövegszerkesztők telepítve.

Az ilyen típusú laborok beállításáról a [Shell linuxos parancsfájljacímű](class-type-shell-scripting-linux.md)témakörben talál részletes információt.

## <a name="ethical-hacking"></a>Etikus hackertevékenység

Létrehozhategy labort egy olyan osztálynak, amely az etikus hackelés törvényszéki oldalára összpontosít. A behatolási tesztelés, amelyet az etikus feltörési közösség alkalmaz, akkor történik, amikor valaki megpróbál hozzáférni a rendszerhez vagy a hálózathoz, hogy bemutassa a rosszindulatú támadó által kihasználható biztonsági réseket.

Egy etikus hackelési osztályban a diákok modern technikákat tanulhatnak a sebezhetőségek elleni védekezésre. Minden diák kap egy Windows Server-gazdavirtuális gépet, amely két beágyazott virtuális géppel rendelkezik – egy [metasploitable3](https://github.com/rapid7/metasploitable3) lemezképpel rendelkező virtuális gép, egy másik pedig [Kali Linux-lemezképpel](https://www.kali.org/) rendelkező gép. A metasploitable virtuális gép célokra használható.  A Kali Linux virtuális gép hozzáférést biztosít a kriminalisztikai feladatok végrehajtásához szükséges eszközökhöz.

Az ilyen típusú laborok beállításáról az [Etikus hackelési osztály tanítására szolgáló labor beállítása című](class-type-ethical-hacking.md)témakörben talál részletes információt.

## <a name="database-management"></a>Adatbázis-kezelés
Adatbázisok fogalmak egyike a bevezető tanfolyamok tanított a legtöbb Computer Science osztályok az egyetemen. Az Azure Lab Services-ben beállíthat egy tesztkörnyezetet egy alapszintű adatbázis-felügyeleti osztályhoz. Beállíthat például egy virtuálisgép-sablont egy [MySQL](https://www.mysql.com/) Database Server vagy egy [SQL Server 2019-kiszolgálóval](https://www.microsoft.com/sql-server/sql-server-2019) rendelkező tesztkörnyezetben.

Az ilyen típusú tesztkörnyezet beállításáról a [Relációs adatbázisok adatbázis-kezelésének tanításához](class-type-database-management.md)labor beállítása című témakörben talál részletes információt.

## <a name="python-and-jupyter-notebooks"></a>Python- és Jupyter-jegyzetfüzetek
Az Azure Lab Servicesben sablongépet állíthat be a [Jupyter-jegyzetfüzetek](http://jupyter-notebook.readthedocs.io)használatának tanításához szükséges eszközökkel. A Jupyter Notebooks egy nyílt forráskódú projekt, amely lehetővé teszi a rich text és a végrehajtható [Python](https://www.python.org/) forráskód egyszerű kombinálását egyetlen vásznon, amelyet jegyzetfüzetnek neveznek. A jegyzetfüzet futtatása a bemenetek és kimenetek lineáris rekordját eredményezi.  Ezek a kimenetek tartalmazhatnak szöveget, adattáblázatokat, pontdiagramokat stb.

Az ilyen típusú laborok beállításáról az [Adattudomány python- és Jupyter-jegyzetfüzetekkel való tanításához labor beállítása című témakörben](class-type-jupyter-notebook.md)talál részletes információt.

## <a name="mobile-app-development-with-android-studio"></a>Mobilalkalmazások fejlesztése az Android Studio használatával
Az Azure Lab Services ben labort állíthat be egy bevezető mobilalkalmazás-fejlesztési osztály tanításához. Ez az osztály azokra az Android mobilalkalmazásokra összpontosít, amelyek közzétehetők a [Google Play Áruházban.](https://play.google.com/store/apps)  A diákok megtanulják, hogyan kell használni [az Android Studio](https://developer.android.com/studio) alkalmazásokat.  [Az Android visual Studio emulátor](https://visualstudio.microsoft.com/vs/msft-android-emulator/) az alkalmazás helyi tesztelésére szolgál.

Az ilyen típusú laborok beállításáról a [Tesztkörnyezet beállítása a mobilalkalmazás-fejlesztés androidos környezetben történő tanításához](class-type-mobile-dev-android-studio.md)című témakörben talál részletes információt.


## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [Az Azure Lab Services használatával a természetes nyelvi feldolgozás mélytanulására összpontosító labor beállítása](class-type-deep-learning-natural-processing.md)
- [Héjszkriptek használata Linuxon](class-type-shell-scripting-linux.md)
- [Etikus hackertevékenység](class-type-ethical-hacking.md)