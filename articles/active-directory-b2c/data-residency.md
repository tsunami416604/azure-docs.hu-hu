---
title: A régió elérhetősége és az adatok területi adottsága
titleSuffix: Azure AD B2C
description: A régió elérhetősége, az adatok letelepedése és az Azure Active Directory B2C előzetes verzióbérlőivel kapcsolatos információk.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188849"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: A régió elérhetősége & az adatok rezidense

A régió elérhetősége és az adatok rezidenssége két nagyon különböző fogalom, amelyek eltérően vonatkoznak az Azure AD B2C-re az Azure többi részétől. Ez a cikk ismerteti a két fogalom közötti különbségeket, és összehasonlítja, hogyan vonatkoznak az Azure-ra és az Azure AD B2C-re.

Az Azure AD B2C **világszerte általánosan elérhető,** és az **Egyesült Államokban, Európában vagy Ázsia csendes-óceáni térségében**is elérhető az **adattárolási lehetőség.**

[A régió elérhetősége](#region-availability) arra utal, hogy egy szolgáltatás hol érhető el.

[Az adattárolási hely](#data-residency) a felhasználói adatok tárolási helyét nevezi meg.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure AD B2C világszerte elérhető az Azure nyilvános felhőn keresztül.

Ez eltér a modell, majd a legtöbb más Azure-szolgáltatások, amelyek általában pár *rendelkezésre állás* *idata rezidensi.* Erre példákat láthat az Azure [Elérhető termékek régiónként](https://azure.microsoft.com/regions/services/) lapon és az [Active Directory B2C díjkalkulátorában.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="data-residency"></a>Adattárolási hely

Az Azure AD B2C a felhasználói adatokat az Egyesült Államokban, Európában vagy az ázsiai és csendes-óceáni térségben tárolja.

Az adatok rezidensségét az [Azure AD B2C-bérlő létrehozásakor](tutorial-create-tenant.md)kiválasztott ország/régió határozza meg:

![Képernyőkép egy előzetes bérlőről](./media/data-residency/data-residency-b2c-tenant.png)

Az adatok az **Egyesült Államokban** találhatók a következő országok/régiók esetében:

> Egyesült Államok, Kanada, Costa Rica, Dominikai Köztársaság, Salvador, Guatemala, Mexikó, Panama, Puerto Rico és Trinidad & Tobago

Az adatok **európában** találhatók a következő országok/régiók esetében:

> Algéria, Ausztria, Azerbajdzsán, Bahrein, Fehéroroszország, Belgium, Bulgária, Horvátország, Ciprus, Cseh Köztársaság, Dánia, Egyiptom, Észtország, Finnország, Franciaország, Németország, Görögország, Magyarország, Izland, Írország, Izrael, Olaszország, Jordánia, Kazahsztán, Kenya, Kuvait, Lettország, Libanon, Liechtenstein, Litvánia, Luxemburg, Észak-Macedónia, Málta, Montenegró, Marokkó, Hollandia, Nigéria, Norvégia, Omán, Pakisztán, Lengyelország, Portugália, Katar, Románia, Oroszország, Szaúd-Arábia, Szerbia, Szlovákia, Szlovénia, Dél-Afrika, Spanyolország, Svédország, Svájc, Tunézia, Törökország, Ukrajna, egyesült arab emírségekés egyesült királyság.

Az adatok **az ázsiai és csendes-óceáni térségben** találhatók a következő országok/régiók esetében:

> Afganisztán, Hongkong KKT, India, Indonézia, Japán, Korea, Malajzia, Fülöp-szigetek, Szingapúr, Srí Lanka, Tajvan és Thaiföld.

A következő országok/régiók kerülnek fela listára. Egyelőre továbbra is használhatja az Azure AD B2C-t a fenti országok/régiók bármelyikének kiválasztásával.

> Argentína, Ausztrália, Brazília, Chile, Kolumbia, Ecuador, Irak, Új-Zéland, Paraguay, Peru, Uruguay és Venezuela.

## <a name="preview-tenant"></a>Bérlő előzetes megtekintése

Ha az Azure AD B2C előzetes verziójú időszaka során létrehozott egy B2C-bérlőt, akkor valószínű, hogy a **bérlői típus** az **előzetes bérlőt**mondja.

Ebben az esetben csak fejlesztési és tesztelési célokra kell használnia a bérlőt. NE használjon előzetes verziójú bérlőt éles alkalmazásokhoz.

**Nincs áttelepítési útvonal** egy előzetes B2C-bérlőtől egy éles méretű B2C-bérlőig. Létre kell hoznia egy új B2C-bérlőt az éles alkalmazásokhoz.

Vannak ismert problémák, amikor törli az előzetes B2C-bérlő, és hozzon létre egy éles méretű B2C bérlő azonos tartománynévvel. *Létre kell hoznia egy éles méretű B2C-bérlőt más tartománynévvel.*

![Képernyőkép egy előzetes bérlőről](./media/data-residency/preview-b2c-tenant.png)