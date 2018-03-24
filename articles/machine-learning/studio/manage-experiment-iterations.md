---
title: Kísérletismétlések kezelése a Machine Learning Studio |} Microsoft Docs
description: Az Azure Machine Learning Studióban kísérletismétlések kezelése
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6a53530f-20d5-40ae-9b49-7b499ccb44b7
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: c5419eed1de50c29cf6e5bcaf7070c48d7a335ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Kísérlet ismétléseinek kezelése az Azure Machine Learning Studióban
A prediktív elemzési modellek fejlesztése iteratív folyamat - a különböző funkciók és a kísérlet paraméterek módosítása közben, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy rendelkezik-e a betanított, hatékony modellel rendelkezünk. A folyamathoz a különböző ismétlési kísérlet paraméterek és konfigurációk nyomon követéséhez.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A kísérletek előző fut ahhoz, hogy az ellenőrző kérdés, le újra, és végső soron ellenőrizze vagy pontosíthatja az előző feltételek bármikor tekintheti meg. A kísérlet futtatásakor a Machine Learning Studio tartja a Futtatás, beleértve az adatkészletet, a modul, és a port kapcsolatok és a paraméterek előzményeit. Az előzményekben az eredményeket, például indítási és leállítási időpontok naplóüzenetek vagy végrehajtásának állapota futásidejű adatok is rögzíti. Vessen egy pillantást vissza ezek futtatása bármelyikét bármikor áttekintheti a kísérletet, és a köztes eredmények időrendje. A kísérlet korábbi futtatásakor segítségével még be egy új lekérdezés és a felderítési fázisában a útvonalban egyszerű, bonyolult vagy akár ensemble modellezési megoldások létrehozásához indítsa el.

> [!NOTE]
> Amikor a korábbi futtatása során a kísérlet, a kísérlet verzió zárolva van, ezért nem szerkeszthető. Azonban mentheti egy példányát kattintva **SAVE AS** és a példányt egy új nevét. A Machine Learning Studio megnyílik az új, amely majd szerkesztésével és futtatásával. Ezt a példányt a kísérlet érhető el a **kísérletek** együtt a más kísérletek a listában.
> 
> 

## <a name="viewing-the-prior-run"></a>A korábbi futtatás megtekintése
Ha a kísérlet megnyitott, amelyek legalább egyszer futnak, akkor kattintva megtekintheti a fenti Futtatás a kísérlet **előzetes futtatása** a Tulajdonságok panelen.

Tegyük fel például, a kísérlet létrehozása és futtatása a verziókat 11:23, a 11:42 és 11:55. Ha, nyissa meg a legutóbbi futtatás a kísérlet (11:55), majd kattintson az **előzetes futtatása**, a 11:42 futtatta a verzió már meg van nyitva.

## <a name="viewing-the-run-history"></a>A futtatási előzmények megtekintése
Az előző fut egy kísérlet megtekintéséhez kattintson **futtatása előzményeinek megtekintése** egy megnyitott kísérletben.

Tegyük fel például, hogy a kísérlet létrehozásának a [lineáris regressziós] [ linear-regression] modul, és szeretné megfigyelni értékének módosításával hatásának **tanulási rátát** a kísérlet az eredmények. Futtatja a kísérlet többször különböző érték is ezt a paramétert, az alábbiak szerint:

| Learning sebesség értékének | Futtatás kezdő időpontja |
| --- | --- |
| 0.1 |9/11/2014 du. 4:18:58 |
| 0.2 |9/11/2014 du. 4:24:33 |
| 0.4 |9/11/2014 du. 4:28:36 |
| 0.5 |9/11/2014 du. 4:33:31 |

Ha **futtatása ELŐZMÉNYEINEK megtekintése**, ezek futtatása listájának megtekintéséhez:

![Példa futtatási előzményei][runhistory]

Kattintson bármelyik ezek futtatása a kísérlet futtatta azt időpontjában pillanatkép megtekintéséhez. A konfigurációs paraméter értéke, megjegyzések és eredmények összes megmaradnak, hogy a teljes rendszert futtató kísérletbe rekord biztosítson.

> [!TIP]
> Dokumentálja a közelítés a kísérlet, módosíthatja a cím minden alkalommal, amikor futtatja, frissítheti a **összegzés** tulajdonságai a kísérlet ablaktáblán, és hozzáadhatja vagy frissítheti megjegyzések az egyéni modulok jegyezze fel a módosításokat. A cím, összegzésének és modul megjegyzések menti a kísérlet minden egyes futtatásához.
> 
> 

A kísérletek a listában a **kísérletek** Machine Learning Studio lapja mindig a legújabb verzióját a kísérlet jeleníti meg. Korábbi futtatása során a kísérlet megnyitásakor (használatával **előzetes futtatása** vagy **futtatása ELŐZMÉNYEINEK megtekintése**), a vázlatként megjelölt verziót kattintva visszatérhet **futtatása ELŐZMÉNYEINEK megtekintése** , majd válassza az iteráció, amely rendelkezik egy **állapot** a **szerkeszthető**.

## <a name="iterating-on-a-previous-run"></a>Az előző léptetés
Amikor rákattint **előzetes futtatása** vagy **futtatása ELŐZMÉNYEINEK megtekintése** és nyissa meg az előző, akkor az egy végzett kísérlet csak olvasható módban.

Ha meg akarja kezdeni az ismétlések kísérletbe kezdődő, és az előző konfigurált módja, ehhez a Futtatás megnyitásával, majd **SAVE AS**. Ez az új nevet, egy üres futtatási előzményei, egy új kísérlet hoz létre, és az összetevők és a paraméterértékeket a korábbi futtatása. Az új kísérlet szerepel-e a **KÍSÉRLETEKET** lap a Machine Learning Studio kezdőlapjára, és módosíthatja, és kezdeményezi egy új futtatáshoz, futtassa az ismétlések kísérletbe előzményeit. 

Tegyük fel például, a kísérlet futtatási előzményei, az előző részben található. Szeretné megfigyelni, mi történik, ha úgy állítja be a **tanulási rátát** paraméter 0,4, és próbálja meg különböző érték is az **képzési alapidőszakkal száma** paraméter.

1. Kattintson a **futtatása ELŐZMÉNYEINEK megtekintése** , és nyissa meg a futtatott du. 4:28:36 (beállította a paraméter értéke a 0,4) kísérlet ismétléseinek.
2. Kattintson a **SAVE AS**.
3. Adjon meg egy új címet, majd kattintson a **OK** be van jelölve. Egy új példányt a kísérlet jön létre.
4. Módosítsa a **képzési alapidőszakkal száma** paraméter.
5. Kattintson a **futtatása**.

Most továbbra is módosíthatja, és ezt a verziót a kísérlet létrehozása a munkahelyi rögzítéséhez új futtatási előzményeit.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
