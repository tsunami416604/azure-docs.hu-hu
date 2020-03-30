---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Adja meg az egyéni szabályzat BuildingBlocks elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf9c333af48e1c148dcd20ccf5ce91b284bd728b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189871"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **BuildingBlocks** elem a [TrustFrameworkPolicy](trustframeworkpolicy.md) elemen belül kerül hozzáadásra.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

A **BuildingBlocks** elem a következő elemeket tartalmazza, amelyeket meg kell adni a megadott sorrendben:

- [ClaimsSchema](claimsschema.md) – a házirend részeként hivatkozható jogcímtípusokat határozza meg. A jogcímséma az a hely, ahol deklarálja a jogcímtípusokat. A jogcímtípus sok programozott nyelven hasonló a változókhoz. A jogcímtípus segítségével adatokat gyűjthet az alkalmazás felhasználójától, jogcímeket kaphat a közösségi identitásszolgáltatóktól, adatokat küldhet és fogadhat egy egyéni REST API-ból, vagy tárolhatja az egyéni szabályzat által használt belső adatokat.

- [Predikátumok és PredicateValidationsInput](predicates.md) - Lehetővé teszi, hogy végre egy érvényesítési folyamat annak biztosítása érdekében, hogy csak megfelelően formázott adatokat kell bevinni a jogcím.

- [Jogcímátalakítások](claimstransformations.md) – a házirendben használható jogcímátalakítások listáját tartalmazza.  A jogcímek átalakítása átalakítja az egyik jogcímet egy másikba. A jogcímek transzformációjában meg kell adni egy átalakítási módszert, például:
  - Karakterlánc-jogcím esetének módosítása a megadottra. Egy karakterláncot például kisbetűről nagybetűsre módosítva.
  - Két jogcím összehasonlítása és a követelés visszaküldése igaz, jelezve, hogy a jogcímek megegyeznek, egyébként hamisak.
  - Karakterláncjogcím létrehozása a házirendben megadott paraméterből.
  - Véletlenszám-generátor használatával véletlenszerű karakterlánc létrehozása.
  - Jogcím formázása a megadott formátumkarakterlánc nak megfelelően. Ez az átalakítás `String.Format` a C# metódust használja.

- InputValidation - Ez az elem lehetővé teszi, hogy olyan logikai összesítéseket hajtson végre, amelyek hasonlóak a *és* *a hoz.*

- [ContentDefinitions](contentdefinitions.md) – A felhasználói út során használandó HTML5-sablonok URL-címeit tartalmazza. Az egyéni házirendben a tartalomdefiníció határozza meg a html5-oldal URI-ját, amelyet a felhasználói út egy adott lépéséhez használ. Például a bejelentkezési vagy feliratkozási, jelszó-visszaállítási vagy hibaoldalak. A HTML5-fájl LoadUri-jának felülbírálásával módosíthatja a megjelenést. Vagy létrehozhat új tartalomdefiníciókat az igényeinek megfelelően. Ez az elem honosítási azonosítót használó honosított erőforrás-hivatkozást tartalmazhat.

- [Honosítás](localization.md) - Lehetővé teszi, hogy több nyelven. A házirendek honosítási támogatása lehetővé teszi a támogatott nyelvek listájának beállítását egy házirendben, és az alapértelmezett nyelv kiválasztását. A nyelvspecifikus karakterláncok és gyűjtemények is támogatottak.

- [DisplayControls](display-controls.md) – Az oldalon megjelenítendő vezérlőket határozza meg. A kijelző-vezérlők speciális funkciókkal rendelkeznek, és a háttérszintű ellenőrzési technikai profilokkal kommunikálnak. A kijelzővezérlők jelenleg **előzetes verzióban**vannak.
