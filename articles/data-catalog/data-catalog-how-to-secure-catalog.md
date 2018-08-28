---
title: Biztonságos hozzáférés az Azure Data Catalog hogyan
description: Ez a cikk azt ismertetik, hogyan secure data cataloghoz és annak adategységeket.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 6b82c71154edfe5fedab3b92e25c11007820c15c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053724"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>A data catalog és adategységeket való hozzáférés biztonságossá tétele
> [!IMPORTANT]
> Ez a funkció csak az Azure Data Catalog standard kiadása érhető el.

Az Azure Data Catalog lehetővé teszi a data catalog férhet hozzá, és milyen műveleteket (regisztrálásához, megjegyzésekkel, saját tulajdonba vétel), a katalógus metaadat hajthat végre. 

## <a name="catalog-users-and-permissions"></a>Katalógus felhasználói és engedélyek
A hozzáférést egy felhasználó vagy csoport az adatkatalógust, valamint engedélyeket állíthat be:

1. Az a [kezdőlapja a data Catalog](http://www.azuredatacatalog.com), kattintson a **beállítások** az eszköztáron.

    ![a Data catalog – beállítások](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. A beállítások lapon bontsa ki a **katalógus felhasználói** szakaszban.
    ![Felhasználók – a katalógus hozzáadása](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Kattintson a **Hozzáadás** parancsra.
4. Adja meg a teljes **felhasználónév** neve vagy a **biztonsági csoport** a az Azure Active Directory (AAD) társított a katalógusban. Vesszővel (', '), hozzáadásakor az elválasztó több mint egy felhasználónak vagy csoportnak.
    ![Katalógus felhasználói - felhasználók vagy csoportok](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Nyomja meg **ENTER** vagy **lapon** a szövegmezőben jelölje ki. 
6.  Ellenőrizze, hogy minden engedélyt (**jegyzetkészítés**, **regisztrálása**, és **saját tulajdonba vétel**) alapértelmezés szerint a felhasználókhoz vagy csoportokhoz tartoznak. Ez azt jelenti, hogy a felhasználó vagy csoport is [adategységeket]( data-catalog-how-to-register.md), [adategységek ellátása dekorációkkal]( data-catalog-how-to-annotate.md), és [adategységek saját tulajdonba]( data-catalog-how-to-manage.md). 
    ![Katalógus-felhasználók – az alapértelmezett engedélyek](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Adjon egy felhasználót vagy csoportot a csak olvasási hozzáférés a katalógus, törölje a jelet a **jegyzettel láthatja el** lehetőséget a felhasználó vagy csoport. Ha így tesz, a felhasználó vagy csoport nem adategységek ellátása dekorációkkal a katalógusban, de azok tudja őket tekinteni. 
8.  Adategységek regisztrálása megtagadása egy felhasználót vagy csoportot, törölje a jelet a **regisztrálása** lehetőséget a felhasználó vagy csoport.
9.  Adategység tulajdonba egy felhasználó hozzáférésének megtagadása, törölje a jelet a **saját tulajdonba vétel** lehetőséget a felhasználó vagy csoport. 
10. A katalógus felhasználóinak egy felhasználói csoport törléséhez kattintson **x** a felhasználó vagy csoport a lista alján. 
    ![Felhasználók a katalógus - felhasználó törlése](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Azt javasoljuk, hogy közvetlenül katalógus helyett felhasználók hozzáadása és engedélyek hozzárendelése a biztonsági csoportokat hoz létre. Ezt követően a biztonsági csoportokat, amelyek megfelelnek a szerepkörökhöz, és a megfelelő hozzáféréssel a katalógus vehet fel felhasználókat.

## <a name="special-considerations"></a>Különleges szempontok

- A biztonsági csoportokhoz rendelt engedélyek a következők additív. Tegyük fel a felhasználó két csoport van. Egy csoport engedélyekkel rendelkezik megjegyzésekkel és egyéb csoport nem rendelkezik megjegyzésekkel engedélyeket. Ezután felhasználói engedélyek rendelkezik megjegyzésekkel. 
- Az explicit módon a felhasználóhoz rendelt engedélyek felülbírálják az engedélyek hozzárendelve a csoport, amelyhez a felhasználó tartozik. Az előző példában tegyük fel, akkor explicit módon hozzáadva a felhasználót, hogy a felhasználók a katalógus, és végezze el a jegyzet nem rendelhet hozzá engedélyeket. A felhasználó nem adategységek ellátása dekorációkkal annak ellenére, hogy a felhasználó tagja egy csoportnak, amelyen telepítve jegyzettel láthatja el engedélyeket.

## <a name="next-steps"></a>További lépések
- [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)

