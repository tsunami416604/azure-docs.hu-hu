---
title: "Az Azure Active Directory B2C: Régió rendelkezésre állási & adatok rezidens |} Microsoft Docs"
description: "Az Azure Active Directory B2C-bérlők típusú témakör:"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: mtillman
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: gsacavdm
ms.openlocfilehash: 752a98ca7f3c77c434de296461790f2cf37e2d5c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Az Azure Active Directory B2C: Régió rendelkezésre állási & adatok rezidens
Régiónkénti elérhetőség és adatok rezidens olyan Azure többi részétől eltérően az Azure AD B2C vonatkozó két nagyon különböző fogalom. Ez a cikk fog szemléltetik az eltéréseket, ezek két fogalom között, és hasonlítsa össze, ezek hogyan vonatkoznak az Azure és az Azure AD B2C.

## <a name="summary"></a>Összefoglalás
Az Azure AD B2C jelenleg **általánosan elérhető világszerte** a beállítással **adatok rezidens Amerikai Egyesült Államokban vagy Európa**.

## <a name="concepts"></a>Alapelvek
* **Régiónkénti elérhetőség** hivatkozik, ahol a szolgáltatás nem használható.
* **Adatok rezidens** hivatkozik a felhasználói adatok tárolására.

## <a name="region-availability"></a>Regionális elérhetőség
Az Azure AD B2C érhető világszerte az Azure nyilvános felhőjében keresztül. 

Ez eltér a modell legtöbb más Azure-szolgáltatások hajtsa végre, amely az adatok rezidens gömbcsatlakozók a rendelkezésre állási. Erre példa látható, két Azure-ban [termékek elérhető területek szerint](https://azure.microsoft.com/regions/services/) oldal és a [Active Directory B2C díjszabási Számológép](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Adattárolás helye
Az Azure AD B2C az Amerikai Egyesült Államokban vagy Európa felhasználói adatait tárolja.

Adatok rezidens határozza meg, melyik ország vagy régió van kiválasztva alapján amikor [létrehozása az Azure AD B2C-bérlő](active-directory-b2c-get-started.md).

![A kép bérlő képernyőfelvétele](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Az Amerikai Egyesült Államokban, a következő országokban vagy régiókban található adatokat:

> Az Amerikai Egyesült Államok, Kanada, hondurasi, Dominikai Köztársaság, El Salvador, Guatemala, mexikói, Panama, Puerto Rico és Trinidad és Tobago

Az adatok a következő országokban vagy régiókban Európában találhatók:

> Algéria, Ausztria, Azerbajdzsán, Bahreini Királyság, Fehéroroszországból, Belgium, bolgár, horvát, Ciprus, Cseh Köztársaság, Dánia, Egyiptom, észt, Finnország, Franciaország, Németország, Görögország, Magyarország, Izland, Írországban, izraeli, Olaszország, Jordánia, Kazahsztán, Kenya, kuvaiti, Lativa, Libanon, Liechtenstein, Lituania, Luxembourgban, Macedónia FYRO, Málta, Montenegró, Marokkó, holland, Nigéria, Norvégia, Omán, pakisztáni, Lengyelország, Portugália, Katar, román, orosz, Szaúd-Arábia, szerb, Szlovákia, szlovén, Dél-afrikai, Spanyolország, Svédország, Svájc, Tunézia, Törökország, ukrán, Egyesült Arab emírségekbeli és Egyesült Királyság.

A fennmaradó országokban és régiókban vannak hozzáadva a listához.  Most, továbbra is használhatja az Azure AD B2C ki bármelyik fenti országokban és régiókban.

> Afganisztáni, argentin, Ausztrália, Brazília, chilei, Kolumbia, Ecuadori, Hongkong KKT, India, indonéziai, Irak, japán, koreai, malajziai, Új-Zéland, paraguayi, Perui, Fülöp-szigeteki, szingapúri, Srí Lanka, Tajvan, Thaiföld, Uruguayi és Bolivári.

## <a name="preview-tenant"></a>Bérlő előnézete
A B2C-bérlő során az Azure AD B2C előzetes verzió idejének hozna létre, ha valószínű, amely a **típus bérlői** szerint **Preview bérlői**. Ha ez a helyzet, csak a fejlesztési és tesztelési célra, és nem éles alkalmazások esetén a bérlő kell használnia.

> [!IMPORTANT]
> Nincs áttelepítési útvonal a B2C-bérlő előnézete egy éles környezetben való méretezésre B2C-bérlő. Vegye figyelembe, hogy vannak ismert ad ki, ha a B2C előzetes bérlő töröl, és hozza létre újból az azonos nevű tartomány egy éles környezetben való méretezésre B2C-bérlő. Kell egy éles környezetben való méretezésre B2C-bérlő létrehozása egy másik tartomány nevét.


![A kép bérlő képernyőfelvétele](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
