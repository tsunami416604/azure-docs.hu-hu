---
title: Önkiszolgáló regisztráció külső identitásokhoz – Azure AD
description: Megtudhatja, hogyan teheti lehetővé a külső felhasználók számára az alkalmazások regisztrálását az önkiszolgáló regisztráció engedélyezésével. Személyre szabott regisztrációs élményt hozhat létre az önkiszolgáló bejelentkezési felhasználói folyamat testreszabásával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0325b43b6726f04d5994b60404f218ac58122d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597533"
---
# <a name="self-service-sign-up-preview"></a>Önkiszolgáló regisztráció (előzetes verzió)
|     |
| --- |
| Az önkiszolgáló regisztráció a Azure Active Directory nyilvános előzetes funkciója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Az alkalmazások külső felhasználókkal való megosztásakor előfordulhat, hogy nem mindig tudja előre, hogy kinek van szüksége az alkalmazáshoz való hozzáférésre. Az önkiszolgáló regisztráció engedélyezésével lehetővé teheti a külső felhasználók számára, hogy a meghívókat közvetlenül az egyéni felhasználóknak is regisztrálják. Személyre szabott regisztrációs élményt hozhat létre az önkiszolgáló bejelentkezési felhasználói folyamat testreszabásával. Megadhatja például az Azure AD-vagy a közösségi identitás-szolgáltatók beállításait, és információkat gyűjthet a felhasználóról.

## <a name="user-flow-for-self-service-sign-up"></a>Felhasználói folyamat önkiszolgáló regisztrációhoz

Az önkiszolgáló regisztrációs felhasználói folyamat létrehoz egy regisztrációs élményt a külső felhasználóknak a megosztani kívánt alkalmazáson keresztül. A felhasználói folyamat egy vagy több alkalmazáshoz is társítható. Először is engedélyeznie kell az önkiszolgáló regisztrációt a bérlőre, és összevonása minden olyan identitás-szolgáltatóval, amely számára engedélyezni kívánja a külső felhasználók számára a bejelentkezést. Ezután létrehozhatja és testreszabhatja a regisztrációs felhasználói folyamatot, és hozzárendelheti az alkalmazásait.
A felhasználói folyamat beállításait beállíthatja annak szabályozására, hogy a felhasználó hogyan regisztrálja az alkalmazást:

- A bejelentkezéshez használt fióktípus, például a Facebook vagy az Azure AD-fiókok
- A felhasználó által regisztrálható attribútumok, például Utónév, postai kód vagy tartózkodási ország

Ha egy felhasználó be szeretne jelentkezni az alkalmazásba, legyen szó webes, mobil-, asztali vagy egyoldalas alkalmazásról (SPA), akkor az alkalmazás egy engedélyezési kérelmet kezdeményez a felhasználó által megadott végpont számára. A felhasználói folyamat meghatározza és szabályozza a felhasználó élményét. Amikor elvégeznek egy regisztrációs felhasználói folyamatot, az Azure AD létrehoz egy jogkivonatot, majd visszairányítja a felhasználót az alkalmazáshoz. Több alkalmazás is használhatja ugyanazt a felhasználói folyamatot.

## <a name="example-of-self-service-sign-up"></a>Önkiszolgáló regisztráció – példa

Az alábbi példa azt szemlélteti, hogyan történik a közösségi identitás-szolgáltatók benyújtása az Azure AD-be önkiszolgáló bejelentkezési képességekkel a vendég felhasználói számára.  
A Woodgrove partnere megnyitja a Woodgrove alkalmazást. Úgy döntenek, hogy regisztráljanak egy szállítói fiókra, így a beszállítói fiók igénylése lehetőséget választja, amely az önkiszolgáló bejelentkezési folyamatot indítja el.

![Példa önkiszolgáló regisztráció kiindulási oldalára](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Az általuk választott e-mailt használják a regisztrációhoz.

![Példa a Facebook kiválasztására a bejelentkezéshez](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Az Azure AD kapcsolatot létesít a Woodgrove a partner Facebook-fiókjának használatával, és létrehoz egy új fiókot.

A Woodgrove többet szeretne megtudni a felhasználóról, például a név, az üzleti név, az üzleti regisztrációs kód, a telefonszám.

![Példa a felhasználói regisztrációs attribútumok megjelenítésére](media/self-service-sign-up-overview/example-enter-user-attributes.png)

A felhasználó belép az adatokba, folytatja a regisztrációs folyamatot, és hozzáfér a szükséges erőforrásokhoz.

![Példa a bejelentkezett felhasználóra](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>További lépések

 Részletekért lásd: önkiszolgáló [regisztráció hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md).