---
title: Labor létrehozása a relációs adatbázisok adatbázis-kezelésének oktatására | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be egy labort a relációs adatbázisok kezelésének tanítására.
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
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469918"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Labor beállítása a relációs adatbázisok adatbázis-kezelésének tanítására

Ez a cikk ismerteti, hogyan állíthat be egy tesztkörnyezet egy alapszintű adatbázisok felügyeleti osztály az Azure Lab Services. Adatbázisok fogalmak egyike a bevezető tanfolyamok tanított a legtöbb Computer Science osztályok az egyetemen. A strukturált lekérdezési nyelv (SQL) egy nemzetközi szabvány. Az SQL a kapcsolat-adatbázis-kezelés szabványos nyelve, beleértve az adatbázis tartalmának hozzáadását, elérését és kezelését.  Ez leginkább megjegyezte, a gyors feldolgozás, bizonyított megbízhatóság, könnyű, és a rugalmasság a használat.

Ebben a cikkben bemutatjuk, hogyan állíthat be egy virtuálisgép-sablont egy laborban a MySQL Database Server és az SQL Server 2019 server segítségével.  [MySQL](https://www.mysql.com/) egy szabadon elérhető nyílt forráskódú relációs adatbázis-kezelő rendszer (RDBMS).  [Az SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) a Microsoft RDBMS legújabb verziója.

## <a name="lab-configuration"></a>Labor konfigurációja

A tesztkörnyezet beállításához azure-előfizetésre és laborfiókra van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután megkapja az Azure-előfizetést, létrehozhat egy új laborfiókot az Azure Lab Servicesben. Az új tesztkörnyezet-fiók létrehozásáról az [Oktatóanyag a Laborfiók beállítása](tutorial-setup-lab-account.md)című témakörben talál további információt.  Meglévő tesztkörnyezet-fiókot is használhat.

### <a name="lab-account-settings"></a>Laborfiók beállításai

Engedélyezze az alábbi táblázatban leírt beállításokat a tesztkörnyezet-fiókhoz. A marketplace-lemezképek engedélyezéséről a [Piactér-képek megadása a laborkészítők számára elérhető képtárban](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)című témakörben talál további információt.

| Laborfiók beállítása | Utasítások |
| ------------------- | ------------ |
|Piactér képe| Engedélyezze az "SQL Server 2019 Standard on Windows Server 2019" lemezképet a laborfiókban való használatra.|

### <a name="lab-settings"></a>Labor beállításai

Az alábbi táblázatbeállításait használhatja az osztálytermi labor beállításakor.  Az osztálytermi labor létrehozásáról további információt az [osztálytermi laboroktatói bemutató beállítása című](tutorial-setup-classroom-lab.md)témakörben talál.

| Labor beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| Közepes. Ez a méret a legalkalmasabb relációs adatbázisokhoz, memórián belüli gyorsítótárazáshoz és elemzésekhez.|
|Virtuális gép képe| Sql Server 2019 szabvány Windows Server 2019 rendszeren|

## <a name="template-machine-configuration"></a>Sablongép konfigurációja

A MySQL Windows Server 2019 rendszeren való telepítéséhez kövesse a [MySQL Community Server telepítése és futtatása virtuális gépen](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)című témakörben említett lépéseket.

Az SQL Server 2019 előre telepítve van az általunk az új tesztkörnyezet létrehozásakor kiválasztott virtuálisgép-lemezképben.

## <a name="cost-estimate"></a>Költségbecslés

Fedjük le az osztály lehetséges költségbecslését.  Egy 25 tanulóból álló osztályt fogunk használni.  20 óra ütemezett óra van.  Emellett minden tanuló 10 óra kvótát kap a házi feladatra vagy a feladatokra az ütemezett óraidőn kívül.  Az általunk választott virtuális gépméret közepes volt, ami 42 laboregység.

Íme egy példa az osztály lehetséges költségbecslésére:

25 \* diák (20 menetrend szerinti óra + \* 10 kvótaóra) 0,42 USD/óra = 315,00 USD

További részletek a díjszabásról az [Azure Lab Services díjszabása.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Összegzés

Ez a cikk végigjárta a metszőfájl és az SQL Server használatával az alapvető adatbázis-kezelési fogalmak létrehozásához szükséges lépéseket. Más adatbázisosztályokhoz is használhat hasonló beállításokat.

## <a name="next-steps"></a>További lépések

A következő lépések gyakoriak a tesztkörnyezet beállításában.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail regisztrációs linkek diákok](how-to-configure-student-usage.md#send-invitations-to-users)
