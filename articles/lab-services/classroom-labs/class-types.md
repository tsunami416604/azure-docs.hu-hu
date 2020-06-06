---
title: Példák a Azure Lab Services-osztályokra | Microsoft Docs
description: Bizonyos típusú osztályokat biztosít, amelyekhez a Azure Lab Services használatával állíthatja be a laborokat.
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
ms.openlocfilehash: c4842c673dc5d56cd57a2131f1c522b0bc682559
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84448019"
---
# <a name="class-types-overview---azure-lab-services"></a>Osztályok típusai – áttekintés – Azure Lab Services

Azure Lab Services lehetővé teszi, hogy gyorsan be lehessen állítani a Felhőbeli tantermi tesztkörnyezet környezeteit. Az ebben a szakaszban található cikkek segítséget nyújtanak a különböző típusú osztálytermi laborok beállításához Azure Lab Services használatával.

## <a name="deep-learning-in-natural-language-processing"></a>Mélytanulás a természetes nyelvfeldolgozásban

Beállíthat egy olyan labort, amely a természetes nyelvi feldolgozás (NLP) mély tanulására koncentrál a Azure Lab Services használatával. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) formája, amely lehetővé teszi, hogy a számítógépek fordítással, beszédfelismeréssel és más nyelvi felismerési képességekkel rendelkeznek. Az NLP osztályba tartozó diákok linuxos virtuális gépet (VM) kapnak, amelyből megtudhatja, hogyan alkalmazhat neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mély tanulási modellek fejlesztéséhez.

Az ilyen típusú laborok beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a természetes nyelvi feldolgozás terén a Azure Lab Services használatával](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Héjszkriptek használata Linuxon

Beállíthat egy labort a rendszerhéj-parancsfájlok Linuxon való tanításához. A parancsfájlok hasznos részét képezik a rendszerfelügyeletnek, amely lehetővé teszi a rendszergazdák számára az ismétlődő feladatok elkerülését. Ebben a példában az osztály a hagyományos bash-parancsfájlokat és a továbbfejlesztett parancsfájlokat tartalmazza. A továbbfejlesztett parancsfájlok olyan parancsfájlok, amelyek a bash-parancsokat és a Ruby-t ötvözik. Ez a módszer lehetővé teszi a Ruby számára, hogy a Shell használatával kommunikáljon a környékre és bash-parancsokra.

Az ilyen programozási osztályokat tartalmazó tanulók Linux rendszerű virtuális gépeket szereznek be a Linux alapjaihoz, és megismerhetik a bash rendszerhéj parancsfájljait is. A linuxos virtuális gépen engedélyezve van a távoli asztali hozzáférés, és telepítve van a [gedit](https://help.gnome.org/users/gedit/stable/) és a [Visual Studio Code](https://code.visualstudio.com/) szövegszerkesztő.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [rendszerhéj-parancsfájlok futtatása Linuxon](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Etikus hackertevékenység

Beállíthat egy olyan osztályhoz tartozó labort, amely az etikai feltörések kriminalisztikai oldalára koncentrál. A behatolási teszt, amelyet az etikai hackelési Közösség használ, akkor következik be, amikor valaki megpróbál hozzáférni a rendszerhez vagy a hálózathoz a rosszindulatú támadó által kihasználható biztonsági rések bemutatására.

Egy etikai hackelési osztályban a tanulók modern technikákat tanulnak a biztonsági rések elleni védelemhez. Minden tanuló egy olyan Windows Server Host virtuális gépet kap, amely két beágyazott virtuális géppel rendelkezik – egy [Metasploitable3](https://github.com/rapid7/metasploitable3) -lemezképpel rendelkező virtuális géppel és egy másik, [Kali Linux](https://www.kali.org/) -lemezképpel rendelkező géppel. A Metasploitable virtuális gép felhasználási célokra szolgál.  A Kali Linux rendszerű virtuális gép hozzáférést biztosít a kriminalisztikai feladatok végrehajtásához szükséges eszközökhöz.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása az etikai Hacking osztály megtanításához](class-type-ethical-hacking.md).

## <a name="database-management"></a>Adatbázis-kezelés
Az adatbázisok fogalmai az egyetemen bevezető tanfolyamok egyike. Beállíthat egy labort az alapszintű adatbázisok felügyeleti osztályához Azure Lab Servicesban. Beállíthat például egy virtuálisgép-sablont egy laborban egy [MySQL](https://www.mysql.com/) -adatbázis-kiszolgálóval vagy egy [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) -kiszolgálóval.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a kapcsolódó adatbázisok adatbázis-kezelésének tanításához](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Python-és Jupyter-jegyzetfüzetek
A [Jupyter-jegyzetfüzetek](http://jupyter-notebook.readthedocs.io)használatának megtanításához szükséges eszközökkel beállíthatja Azure Lab Servicesban a sablonhoz tartozó gépeket. A Jupyter notebookok egy nyílt forráskódú projekt, amely lehetővé teszi, hogy könnyedén kombinálja a Rich Text és a végrehajtható [Python](https://www.python.org/) -forráskódot egyetlen, jegyzetfüzet nevű vásznon. A jegyzetfüzetek futtatása a bemenetek és kimenetek lineáris rekordját eredményezi.  Ezek a kimenetek tartalmazhatnak szöveget, táblákat, valamint elszórt ábrákat és egyéb adatokat.

Az ilyen típusú laborok beállításával kapcsolatos részletes információkat a [tesztkörnyezet beállítása az adatelemzéshez Python-és Jupyter-jegyzetfüzetekkel](class-type-jupyter-notebook.md)című témakörben talál.

## <a name="mobile-app-development-with-android-studio"></a>Mobilalkalmazások fejlesztése az Android Studio használatával
Beállíthat egy labort Azure Lab Servicesban egy bevezető Mobile Application Development osztály betanításához. Ez az osztály a [Google Play áruház](https://play.google.com/store/apps)közzétett androidos mobil alkalmazásokra koncentrál.  A tanulók megtudhatják, hogyan hozhat létre alkalmazásokat a [Android Studio](https://developer.android.com/studio) használatával.  Az [Androidhoz készült Visual Studio Emulator](https://visualstudio.microsoft.com/vs/msft-android-emulator/) az alkalmazás helyi tesztelésére szolgál.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a mobileszközök fejlesztéséhez Android Studio használatával](class-type-mobile-dev-android-studio.md).

## <a name="big-data-analytics"></a>Big data elemzése
Beállíthat egy GPU-labort big data elemzési osztály tanításához. Ilyen típusú osztályok esetén a tanulók megismerhetik a nagy adatmennyiségek kezelését, valamint gépi és statisztikai tanulási algoritmusokat alkalmazhatnak az adatelemzések kinyeréséhez. A tanulók számára a fő cél az, hogy megtanulja az adatelemzési eszközök használatát, mint például a Apache Hadoop nyílt forráskódú szoftvercsomag, amely eszközöket biztosít a big data tárolásához, kezeléséhez és feldolgozásához. 

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása Big Data-elemzéshez a HortonWorks-adatplatform Docker-alapú üzembe helyezésével](class-type-big-data-analytics.md).

## <a name="matlab"></a>MATLAB
A mátrix-laboratóriumok számára készült [MATLAB](https://www.mathworks.com/products/matlab.html)a [MathWorks](https://www.mathworks.com/)programozási platformja.  A számítási teljesítményt és vizualizációt ötvözve a matematika, a mérnöki tudományok, a fizika és a kémia terén is népszerűvé teszi a számítástechnikai eszközöket.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása a MATLAB tanításához](class-type-matlab.md).

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks – számítógéppel segített kialakítás (CAD)
Beállíthat egy GPU-labort, amely mérnöki tanulók számára biztosít hozzáférést a [solidworkshez](https://www.solidworks.com/).  A SolidWorks 3D CAD-környezetet biztosít a Solid Objects modellezéséhez.  A SolidWorks segítségével a mérnökök könnyedén létrehozhatják, megjeleníthetik, szimulálják és dokumentálják a terveiket.

Az ilyen típusú tesztkörnyezet beállításával kapcsolatos részletes információkért lásd: [tesztkörnyezet beállítása mérnöki osztályokhoz a SolidWorks használatával](class-type-solidworks.md)

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- [Tesztkörnyezet létrehozása a természetes nyelvi feldolgozással kapcsolatos mély tanuláshoz Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Héjszkriptek használata Linuxon](class-type-shell-scripting-linux.md)
- [Etikus hackertevékenység](class-type-ethical-hacking.md)