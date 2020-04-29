---
title: Tesztkörnyezet létrehozása az adatelemzéshez Python-és Jupyter-jegyzetfüzetekkel | Microsoft Docs
description: Ismerje meg, hogyan állíthat be olyan labort, amely a Python és a Jupyter notebookok használatával tanítja az adatelemzést.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257725"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Tesztkörnyezet létrehozása az adatelemzéshez Python és Jupyter notebookokkal

Ez a cikk azt ismerteti, hogyan állíthat be egy sablon-gépet a labor Servicesben a diákoknak a [Jupyter-jegyzetfüzetek](http://jupyter-notebook.readthedocs.io)használatának megtanításához szükséges eszközökkel.  A Jupyter notebookok egy nyílt forráskódú projekt, amely lehetővé teszi, hogy könnyedén kombinálja a Rich Text és a végrehajtható [Python](https://www.python.org/) -forráskódot egyetlen, jegyzetfüzet nevű vásznon.  A jegyzetfüzetek futtatása a bemenetek és kimenetek lineáris rekordját eredményezi.  Ezek a kimenetek tartalmazhatnak szöveget, táblákat, valamint elszórt ábrákat és egyéb adatokat.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) . Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).  Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért lásd: a [piactér-rendszerképek elérhetővé tétele a labor-készítők](specify-marketplace-images.md)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
| Piactéri rendszerkép | Engedélyezze a [Data Science Virtual Machine-Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) rendszerképet a labor-fiókban való használatra. |

>[!TIP]
>Ez a cikk a Windows Server operációs rendszert használó sablon-számítógép konfigurálására koncentrál.  Az Azure Marketplace-en az [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) rendszerképek használatával is beállíthat egy adatelemzési osztályt Python-és Jupyter-jegyzetfüzetekkel.

### <a name="lab-settings"></a>Tesztkörnyezet beállításai

A tantermi labor beállításakor használja az alábbi táblázatban szereplő beállításokat.  A tantermi laborok létrehozásával kapcsolatos további információkért tekintse [meg a tanterem Lab-oktatóanyagának beállítása](tutorial-setup-classroom-lab.md)című témakört.

| Tesztkörnyezet beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| Kis GPU (számítás). Ez a méret a legjobb megoldás a nagy számítási igényű és a hálózati igényű alkalmazások, például a mesterséges intelligencia és a mély tanulás számára. |
|Virtuálisgép-rendszerkép| Data Science Virtual Machine – Windows 2016|

## <a name="template-machine"></a>Sablon számítógép

A [Data Science Virtual Machine-Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) rendszerkép biztosítja az ilyen típusú osztályok számára szükséges mély tanulási keretrendszereket és eszközöket.  A kép tartalmazza a Jupyter notebookokat és a Visual Studio Code-ot.  A [Jupyter notebookok](http://jupyter-notebook.readthedocs.io) egy webalkalmazás, amely lehetővé teszi az adatszakértők számára a nyers adatok bevezetését, a számítások futtatását, valamint az eredmények megtekintését ugyanabban a környezetben.  A sablonos gépen a webalkalmazás helyileg fog futni.  A [Visual Studio Code](https://code.visualstudio.com/) egy olyan ide, amely gazdag interaktív élményt nyújt a jegyzetfüzetek írásakor és tesztelésekor.  További információ: [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support).

Az osztály beállításához fennmaradó feladat a helyi jegyzetfüzetek biztosítása.  Az Azure Machine Learning minták használatával kapcsolatos útmutatásért lásd: [környezet konfigurálása Jupyter notebookokkal](../../machine-learning/how-to-configure-environment.md#jupyter).  Saját jegyzetfüzeteket is megadhat a sablon számítógépén.  A jegyzetfüzeteket a rendszer a sablon közzétételekor másolja az összes tanulói gépre.

## <a name="cost-estimate"></a>Költségbecslés

Az osztályra vonatkozó lehetséges költségbecslés.  25 tanulós osztályt fogunk használni.  20 órányi ütemezett idő van.  Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára.  A kiválasztott virtuálisgép-méret kisméretű GPU (számítás) volt, amely 139 Lab egység.

Az alábbi példa egy lehetséges költségbecslés erre az osztályra vonatkozóan:

25 tanuló \* (20 ütemezett óra + 10 kvóta óra) \* 139 labor egység \* 0,01 USD/óra = 1042,5 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés

Ebből a cikkből megtudhatta, hogyan hozhat létre labort a Jupyter notebookok osztályhoz. Más gépi tanulási osztályokhoz is használhat hasonló beállításokat.

## <a name="next-steps"></a>További lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)
