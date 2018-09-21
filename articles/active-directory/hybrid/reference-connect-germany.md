---
title: Azure AD Connect a Microsoft Cloud németországi adatközpontjában
description: Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.
keywords: Azure AD Connect bemutatása, Azure AD Connect áttekintése, mi az Azure AD Connect, active directory telepítése, Németország, Fekete-erdő
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e5377078010e899c41b27ef0ea5248ff4a09df8e
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46301785"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect a Microsoft Cloud németországi adatközpontjában – nyilvános előzetes verzió
## <a name="introduction"></a>Bevezetés
Az Azure AD Connect szinkronizálást biztosít a helyszíni Active Directory és az Azure Active Directory között.
A [Microsoft Cloud németországi adatközpontja](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) esetében az eljárások jelentős részét a kezelőnek kell elvégeznie. A Microsoft Cloud németországi adatközpontjának használata esetén a következőkre kell ügyelnie:

* A következő URL-címeket meg kell nyitni egy proxykiszolgálón ahhoz, hogy sikeres lehessen a szinkronizálás:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Visszavont tanúsítványok listái (CRL-ek)
* Az Azure AD címtárba való bejelentkezéskor az onmicrosoft.de tartományba tartozó fiókot kell használnia.

 
## <a name="download"></a>Letöltés
Az Azure AD Connect a portál Azure AD Connect paneljéről tölthető le.  Az Azure AD Connect panelt az alábbi útmutatás alapján keresheti meg.

### <a name="the-azure-ad-connect-blade"></a>Az Azure AD Connect panel
Miután bejelentkezett az Azure Portalra, tegye a következőket:

1. Válassza a Tallózás lehetőséget.
2. Válassza az Azure Active Directory elemet.
3. Ezután válassza az Azure AD Connect elemet.

A következőnek kell megjelennie:

![Az Azure AD Connect panel](./media/reference-connect-germany/germany1.png)

A következő táblázat a panelen látható funkciókat ismerteti.

| Cím | Leírás |
| --- | --- |
| SZINKRONIZÁLÁS ÁLLAPOTA |Arról tájékoztatja, hogy a szinkronizálás engedélyezve van-e, vagy le van tiltva. |
| LEGUTÓBBI SZINKRONIZÁLÁS |A legutóbbi alkalom, amikor sikeresen befejeződött egy szinkronizálás. |
| ÖSSZEVONT TARTOMÁNYOK |Azt mutatja, hogy jelenleg hány összevont tartomány van konfigurálva. |

## <a name="installation"></a>Telepítés
Az Azure AD Connect telepítéséhez használhatja az [itt](how-to-connect-install-roadmap.md) található dokumentációt.

## <a name="advanced-features-and-additional-information"></a>Speciális szolgáltatások és további információk
Ha további tájékoztatást, illetve az egyéni beállításokkal vagy speciális konfigurációkkal kapcsolatos útmutatást keres, kezdje a [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md) című cikkel.  Ez a lap a további útmutatással kapcsolatos információkat és hivatkozásokat ismerteti.

