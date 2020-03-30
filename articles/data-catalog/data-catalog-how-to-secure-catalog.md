---
title: Az Azure Data Catalog hoz való hozzáférés biztonságossá tétele
description: Ez a cikk bemutatja, hogyan biztosítegy adatkatalógust és annak adateszközeit az Azure Data Catalogban.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976764"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Az adatkatalógushoz és adateszközökhöz való hozzáférés biztosítása

> [!IMPORTANT]
> Ez a funkció csak az Azure Data Catalog standard kiadásában érhető el.

Az Azure Data Catalog lehetővé teszi annak megadását, hogy ki férhet hozzá az adatkatalógushoz, és milyen műveleteket (regiszter, jegyzetelés, tulajdonba vétel) hajthatnak végre a katalógusmetaadatokon. 

## <a name="catalog-users-and-permissions"></a>Katalógusfelhasználók és engedélyek

Ha hozzáférést szeretne adni egy felhasználónak vagy csoportnak egy adatkatalógushoz, és engedélyeket szeretne beállítani:

1. Az [adatkatalógus kezdőlapján](https://www.azuredatacatalog.com)kattintson az eszköztár **Beállítások** gombjára.

   ![Az Azure Data Catalog kezdőlapjának beállításai gomb](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. A beállítások lapon bontsa ki a **Katalógusfelhasználók szakaszt.**

   ![Az Azure Data Catalog felhasználóinak hozzáadása gombja](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Kattintson a **Hozzáadás** gombra.

4. Adja meg a teljesen minősített **felhasználónevet** vagy a **biztonsági csoport** nevét a katalógushoz társított Azure Active Directoryban (AAD). Használjon vesszőt (',') elválasztóként, ha egynél több felhasználót vagy csoportot ad hozzá.

   ![Azure Data Catalog felhasználói - felhasználók vagy csoportok](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Nyomja le az **ENTER** vagy a **TAB billentyűt** a szövegmezőből. 

6. Győződjön meg arról, hogy alapértelmezés szerint minden engedély **(Jegyzetelés,** **Regisztráció**és **Tulajdonba vétel)** hozzá van rendelve ezekhez a felhasználókhoz vagy csoportokhoz. Ez azt jelenti , hogy a felhasználó vagy a csoport [adateszközöket regisztrálhat]( data-catalog-how-to-register.md), [adateszközöket jegyzetelhet]( data-catalog-how-to-annotate.md), és [átveheti az adateszközök tulajdonjogát]( data-catalog-how-to-manage.md). 

   ![Azure Data Catalog felhasználói – alapértelmezett engedélyek](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Ha azt szeretné, hogy egy felhasználó vagy csoport csak olvasási hozzáférést adjon a katalógushoz, törölje a jelölőnégyzetből az adott felhasználó vagy csoport **felirata** lehetőségét. Ha így tesz, a felhasználó vagy a csoport nem jegyzetelheti az adatelemeket a katalógusban, de megtekinthetiőket. 

8. Ha meg szeretne tagadni egy felhasználót vagy csoportot az adateszközök regisztrálásától, törölje a **regisztráció** lehetőségét az adott felhasználóhoz vagy csoporthoz.

9. Ha meg szeretné tagadni, hogy egy felhasználó átvegye az adateszköz tulajdonjogát, törölje a **tulajdonjog-átvételi** lehetőséget az adott felhasználó vagy csoport számára. 

10. Ha törölni szeretne egy felhasználót/csoportot a katalógusfelhasználók közül, kattintson az **x** gombra a lista alján található felhasználó/csoport esetében. 

   ![Azure Data Catalog katalógus felhasználói – x felhasználó törlése ikon](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Azt javasoljuk, hogy a felhasználók közvetlen hozzáadása és engedélyek hozzárendelése helyett inkább adjon hozzá biztonsági csoportokat a katalógusfelhasználókhoz. Ezután adja hozzá a felhasználókat a biztonsági csoportok, amelyek megfelelnek a szerepkörök és a katalógushoz szükséges hozzáférést.

## <a name="special-considerations"></a>Különleges szempontok

- A biztonsági csoportokhoz rendelt engedélyek additívak. Mondjuk, a felhasználó két csoportban van. Az egyik csoport jegyzetelési engedéllyel rendelkezik, a másik csoport pedig nem rendelkezik jegyzetelési engedélyekkel. Ezután a felhasználó jegyzetelési engedélyekkel rendelkezik. 
- A felhasználóhoz explicit módon hozzárendelt engedélyek felülbírálják azokat az engedélyeket, amelyekhez azokhoz a csoportokhoz vannak rendelve, amelyekhez a felhasználó tartozik. Az előző példában tegyük fel, hogy explicit módon hozzáadta a felhasználót a felhasználók katalógusához, és nem rendelt hozzá jegyzetelési engedélyeket. A felhasználó nem jegyzetelhet adatelemeket, még akkor sem, ha a felhasználó egy olyan csoport tagja, amely nem rendelkezik jegyzetekkel.

## <a name="next-steps"></a>További lépések

- [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md)
