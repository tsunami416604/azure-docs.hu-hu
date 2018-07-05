---
title: Régiónkénti elérhetőség és az adatok tárolási helye, az Azure Active Directory B2C |} A Microsoft Docs
description: A témakör az Azure Active Directory B2C-bérlők típusú.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c0348dad507b118af542c1bbc47f6ceb88ae7be
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443052"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Az Azure Active Directory B2C: Régiónkénti elérhetőség és az adatok tárolási helye
Régiónkénti elérhetőség és az adattárolási két különböző fogalmak eltérően az Azure AD B2C a többi Azure. Ez a cikk a két módszer közötti különbségeket ismertetik, és a ezek hogyan vonatkoznak az Azure és az Azure AD B2C összehasonlítása.

## <a name="summary"></a>Összegzés
Az Azure AD B2C **általánosan elérhető világszerte** a lehetőséggel a **adattárolás, az Egyesült Államokban vagy Európában**.

## <a name="concepts"></a>Alapelvek
* **Régiónkénti elérhetőség** utal arra, ahol a szolgáltatás használható.
* **Adattárolás helye** hivatkozik a felhasználói adatok tárolására.

## <a name="region-availability"></a>Régiónkénti elérhetőség
Az Azure AD B2C érhető el világszerte az Azure nyilvános felhőn keresztül. 

Ez eltér a modell legtöbb más Azure-szolgáltatások kövesse, amely a rendelkezésre állási párosítása adattárolás helye. Erre példa két Azure-ban látható [termékek elérhető területek szerint](https://azure.microsoft.com/regions/services/) oldal és a [Active Directory B2C-díjkalkulátor](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Adattárolás helye
Az Azure AD B2C felhasználói adatokat az Egyesült Államokban vagy Európában tárolja.

Adattárolás helye határozza meg a kiválasztott melyik ország/régió alapján amikor [létrehozása az Azure AD B2C-bérlő](active-directory-b2c-get-started.md).

![Képernyőfelvétel egy bérlő előnézete](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

A következő országban és régióban az Egyesült Államokban található adatokat:

> Egyesült Államok, Kanada, Costa Rica, Dominikai Köztársaság, El Salvador, Guatemala, Mexikó, Panama, Puerto Rico és Trinidad és Tobago

Adatok Európában találhatók, a következő országok és régiók:

> Algéria, Ausztria, Azerbajdzsán, Bahreini Királyság, Fehéroroszországból, Belgium, bolgár, horvát, Ciprus, Cseh Köztársaság, Dánia, Egyiptom, észt, Finnország, Franciaország, Németország, Görögország, Magyarország, Izland, Írországban, izraeli, Olaszország, Jordánia, Kazahsztán, Kenya, kuvaiti, Lativa, Libanon, Liechtenstein, Lituania, Luxembourgban, Macedónia VJK, Málta, Montenegró, Marokkó, holland, Nigéria, Norvégia, Omán, pakisztáni, Lengyelország, Portugália, Katar, román, orosz, Szaúd-Arábia, szerb, Szlovákia, szlovén, Dél-afrikai, Spanyolország, Svédország, Svájc, Tunézia, Törökország, ukrán, Egyesült Arab emírségekbeli és Egyesült Királyság.

A fennmaradó országban és régióban vannak hozzáadva a listához.  Most a kiválasztásával a fenti országok vagy régiók bármelyike továbbra is használhatja az Azure AD B2C-t.

> Afganisztán, Argentína, Ausztrália, Brazília, Chile, Kolumbia, Ecuador, Hongkong (KKT) (KKT), India, Indonézia, Irak, japán, koreai, Malajzia, Új-Zéland, Paraguay, Peru, Fülöp-szigetek, Szingapúr, Srí Lanka, Tajvan, Thaiföld, Uruguay és Venezuela.

## <a name="preview-tenant"></a>Bérlő előnézete
Ha a B2C-bérlő Azure AD B2C előzetes verzió ideje alatt hozta létre, akkor valószínű, hogy a **írja be a bérlői** szerint **bérlő előnézete**. Ez a helyzet, ha csak a fejlesztési és tesztelési célra, és nem éles alkalmazások számára a bérlő kell használnia.

> [!IMPORTANT]
> Üzemi szintű B2C-bérlőre egy előnézeti B2C-bérlő áttelepítési útvonal van. Vegye figyelembe, hogy vannak ismert problémák, amikor egy előnézeti B2C-bérlő törlése, és hozza létre újból az adott tartománynév egy üzemi szintű B2C-bérlő. Üzemi szintű B2C-bérlő létrehozása egy másik tartománynévvel rendelkezik.


![Képernyőfelvétel egy bérlő előnézete](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
