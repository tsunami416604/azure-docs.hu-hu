---
title: Állítson be egy labort az adatelemzés tanítására python- és Jupyter-jegyzetfüzetekkel | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be egy labort az adatelemzés python- és jupyter-jegyzetfüzetek használatával történő tanításához.
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
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444111"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Állítson be egy labort az adatelemzés tanítására a Python és a Jupyter notebookok segítségével

Ez a cikk bemutatja, hogyan állíthat be sablongépet a Lab Services programban a [Jupyter notebookok](http://jupyter-notebook.readthedocs.io)használatának tanításához szükséges eszközökkel.  A Jupyter Notebooks egy nyílt forráskódú projekt, amely lehetővé teszi a rich text és a végrehajtható [Python](https://www.python.org/) forráskód egyszerű kombinálását egyetlen vásznon, amelyet jegyzetfüzetnek neveznek.  A jegyzetfüzet futtatása a bemenetek és kimenetek lineáris rekordját eredményezi.  Ezek a kimenetek tartalmazhatnak szöveget, adattáblázatokat, pontdiagramokat stb.

## <a name="lab-configuration"></a>Labor konfigurációja

A tesztkörnyezet beállításához azure-előfizetésre és laborfiókra van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután megkapja az Azure-előfizetést, létrehozhat egy új laborfiókot az Azure Lab Servicesben. Az új tesztkörnyezet-fiók létrehozásáról további információt a [tesztkörnyezet-fiók beállításához](tutorial-setup-lab-account.md)szükséges oktatóanyag című témakörben talál.  Meglévő tesztkörnyezet-fiókot is használhat.

### <a name="lab-account-settings"></a>Lab-fiók beállításai

Engedélyezze az alábbi táblázatban leírt beállításokat a tesztkörnyezet-fiókhoz. A marketplace-lemezképek engedélyezéséről a [Piactér-készítők számára elérhető Marketplace-képek megadása című témakörben](specify-marketplace-images.md)talál további információt.

| Laborfiók beállítása | Utasítások |
| ------------------- | ------------ |
| Piactér képe | Engedélyezze az [adatelemzési virtuális gép – Windows 2016-lemezkép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) a laborfiókon belüli használatra. |

>[!TIP]
>Ez a cikk a Windows Server operációs rendszert használó sablongépek konfigurálására összpontosít.  Lehetőség van egy adatelemzési osztály beállítása python és Jupyter notebookok segítségével [Data Science Virtual Machine for Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) vagy Data Science Virtual Machine for Linux [(Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) rendszeraz Azure Marketplace-en.

### <a name="lab-settings"></a>Labor beállításai

Az alábbi táblázatbeállításait használhatja az osztálytermi labor beállításakor.  Az osztálytermi labor létrehozásáról további információt az [osztálytermi laboroktatói bemutató beállítása című](tutorial-setup-classroom-lab.md)témakörben talál.

| Labor beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| Kis GPU (Számítás). Ez a méret a legalkalmasabb olyan nagy számítási igényű és hálózatigényes alkalmazásokhoz, mint a mesterséges intelligencia és a mélytanulás. |
|Virtuális gép képe| Adatelemzési virtuális gép – Windows 2016|

## <a name="template-machine"></a>Sablongép

Az [adatelemzési virtuális gép – A Windows 2016-lemezkép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) biztosítja az ilyen típusú osztályhoz szükséges mélytanulási keretrendszereket és eszközöket.  A kép tartalmazza a Jupyter notebookokat és a Visual Studio-kódot.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) egy webes alkalmazás, amely lehetővé teszi az adatszakértők, hogy a nyers adatok, számítások futtatása, és az eredmények megjelenítése minden ugyanabban a környezetben.  A sablongépünk höz a webalkalmazás helyileg fog futni.  [A Visual Studio Code](https://code.visualstudio.com/) egy IDE, amely gazdag interaktív élményt nyújt a jegyzetfüzetírásés tesztelés során.  További információt a [Jupyter-jegyzetfüzetek munka a Visual Studio-kódban című témakörben talál.](https://code.visualstudio.com/docs/python/jupyter-support)

Az osztály beállításának fennmaradó feladata a helyi jegyzetfüzetek biztosítása.  Az Azure Machine Learning-minták használatával kapcsolatos tudnivalókat a [Jupyter-jegyzetfüzetekkel környezet konfigurálása című témakörben találja.](../../machine-learning/how-to-configure-environment.md#jupyter)  Saját jegyzetfüzeteket is megadhat a sablongépen.  A jegyzetfüzetek a sablon közzétételekor az összes tanulógépre átkerülnek.

## <a name="cost-estimate"></a>Költségbecslés

Fedjük le az osztály lehetséges költségbecslését.  Egy 25 tanulóból álló osztályt fogunk használni.  20 óra ütemezett óra van.  Emellett minden tanuló 10 óra kvótát kap a házi feladatra vagy a feladatokra az ütemezett óraidőn kívül.  Az általunk választott virtuális gép mérete kis GPU (számítási), amely 139 laboregység.

Íme egy példa az osztály lehetséges költségbecslésére:

25 \* diák (20 ütemezett óra + 10 kvóta óra) \* 139 laboregység \* 0,01 USD óránként = 1042,5 USD

További részletek a díjszabásról az [Azure Lab Services díjszabása.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Összegzés

Ebben a cikkben végigmentünk a lépéseket, hogy hozzon létre egy labor egy Jupyter Notebooks osztály. Más gépi tanulási osztályokhoz is használhat hasonló beállításokat.

## <a name="next-steps"></a>További lépések

A következő lépések gyakoriak a tesztkörnyezet beállításában.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail regisztrációs linkek diákok](how-to-configure-student-usage.md#send-invitations-to-users)
