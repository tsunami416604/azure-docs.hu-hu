---
title: "Biztonságos hozzáférés az Azure Data Catalog hogyan |} Microsoft Docs"
description: "Ez a cikk azt ismertetik, hogyan biztosíthat a data catalog és az adategységeket."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.openlocfilehash: 9664a7bc8493b08c8e0797ac6f1b212079829833
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>A data catalog és adategységeket hozzáférésének biztonságossá tétele
> [!IMPORTANT]
> Ez a funkció csak az Azure Data Catalog standard kiadásában érhető el.

Az Azure Data Catalog lehetővé teszi, hogy ki férhet hozzá a data catalog és milyen műveleteket (regisztrálásához, megjegyzésekkel, vegye saját tulajdonba) a metaadatok a katalógus ellátására. 

## <a name="catalog-users-and-permissions"></a>Katalógus-felhasználók és engedélyek
Hozzáférést egy felhasználó vagy csoport a egy data Catalog és engedélyeinek beállítása:

1. Az a [a data CATALOG kezdőlap](http://www.azuredatacatalog.com), kattintson a **beállítások** az eszköztáron.

    ![a Data catalog - beállítások](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. A beállítások lapon bontsa ki a **katalógus felhasználói** szakasz.
    ![Katalógus - felhasználók hozzáadása](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Adja meg a teljesen minősített **felhasználónév** neve vagy a **biztonsági csoport** az az Azure Active Directory (AAD) társított a katalógusban. Használjon vesszőt (', '), egy elválasztó hozzáadásakor több mint egy felhasználó vagy csoport.
    ![Katalógus felhasználói - felhasználók és csoportok](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Nyomja le az **ENTER** vagy **lapon** kívül a szövegmezőben. 
6.  Ellenőrizze, hogy minden engedély (**jegyzet**, **regisztrálása**, és **tulajdonba**) alapértelmezés szerint a felhasználókhoz vagy csoportokhoz tartoznak. Ez azt jelenti, hogy a felhasználó vagy csoport is [adategységek regisztrálása]( data-catalog-how-to-register.md), [adhat megjegyzéseket az adategységekhez]( data-catalog-how-to-annotate.md), és [az adategységek saját tulajdonba]( data-catalog-how-to-manage.md). 
    ![Katalógus felhasználók – az alapértelmezett engedélyek](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Adjon egy felhasználót vagy csoportot a csak olvasási hozzáféréssel a katalógushoz, vagy törölje a **megjegyzésekkel** lehetőség az adott felhasználó vagy csoport. Ha így tesz, a felhasználó vagy csoport nem adhat megjegyzéseket az adategységekhez a katalógusban, de is megtekinthetik. 
8.  Megtagadja az egy felhasználó vagy csoport adategységeket regisztrálja, vagy törölje a **regisztrálása** lehetőség az adott felhasználó vagy csoport.
9.  Egy felhasználó egy adategységet tulajdonba hozzáférésének megtagadása, vagy törölje a **saját tulajdonba** lehetőség az adott felhasználó vagy csoport. 
10. A katalógus felhasználói egy felhasználói csoport törléséhez kattintson **x** a felhasználó vagy csoport a lista alján. 
    ![Katalógus-felhasználók - felhasználó törlése](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Azt javasoljuk, hogy a katalógus közvetlenül a felhasználók hozzáadásával helyett a felhasználók és az engedélyeket a biztonsági csoportok hozzáadása. Felhasználók hozzáadása, majd a biztonsági csoportokhoz szerepköreik és a megfelelő hozzáféréssel a katalógus megfelelő.

## <a name="special-considerations"></a>Különleges szempontok

- A biztonsági csoportokhoz rendelt engedélyek additívak. Tegyük fel a felhasználó két szerepel. Egy csoport rendelkezik megjegyzésekkel engedélyek és egyéb csoport nem rendelkezik megjegyzésekkel engedélyeket. Ezt követően felhasználó engedélyekkel rendelkezik megjegyzésekkel. 
- Az explicit módon a felhasználóhoz rendelt engedélyek bírálja felül a felhasználó tartalmazó csoportok jogosultságait. Az előző példában tegyük fel például, explicit módon hozzáadott szeretné a katalógus-felhasználók és a felhasználó nem hozzárendelése megjegyzésekkel engedélyeket. A felhasználó nem adhat megjegyzéseket az adategységekhez, annak ellenére, hogy a felhasználó nem tagja egy csoportot, amely rendelkezik engedélyekkel megjegyzésekkel.

## <a name="next-steps"></a>Következő lépések
- [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)

