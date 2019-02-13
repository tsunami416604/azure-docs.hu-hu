---
title: Azure AD Connect a Microsoft Cloud németországi adatközpontjában
description: Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.
keywords: Azure AD Connect bemutatása, Azure AD Connect áttekintése, mi az Azure AD Connect, active directory telepítése, Németország, Fekete-erdő
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62f0d82a543c0ae4e629eda3bca18b0a06322f2c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178192"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect a Microsoft Cloud németországi adatközpontjában – nyilvános előzetes verzió
## <a name="introduction"></a>Bevezetés
Az Azure AD Connect szinkronizálást biztosít a helyszíni Active Directory és az Azure Active Directory között.
A [Microsoft Cloud németországi adatközpontja](https://azure.microsoft.com/global-infrastructure/germany/
) esetében az eljárások jelentős részét a kezelőnek kell elvégeznie. A Microsoft Cloud németországi adatközpontjában használatakor vegye figyelembe a következő információkat:

* A következő URL-címeket meg kell nyitni egy proxykiszolgálón ahhoz, hogy sikeres lehessen a szinkronizálás:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Visszavont tanúsítványok listái (CRL-ek)
* Az Azure AD címtárba való bejelentkezéskor az onmicrosoft.de tartományba tartozó fiókot kell használnia.

 
## <a name="download"></a>Letöltés
Az Azure AD Connect a portál Azure AD Connect paneljéről tölthető le.  Az Azure AD Connect panelt az alábbi útmutatás alapján keresheti meg.

### <a name="the-azure-ad-connect-blade"></a>Az Azure AD Connect panel
Miután bejelentkezett az Azure Portalra:

1. Válassza a Tallózás lehetőséget.
2. Válassza az Azure Active Directory elemet.
3. Ezután válassza az Azure AD Connect elemet.

Ekkor megjelenik az alábbi adatokat:

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
Egyéni beállításokkal vagy speciális konfigurációkkal kapcsolatos további információkért látogasson el [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md). Ez a lap a további útmutatással kapcsolatos információkat és hivatkozásokat ismerteti.

