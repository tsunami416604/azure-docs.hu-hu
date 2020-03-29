---
title: 'Azure AD Connect: Deklaratív kiépítési kifejezések | Microsoft dokumentumok'
description: A deklaratív létesítési kifejezések magyarázata.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245507"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect szinkronizálás: A deklaratív létesítési kifejezések ismertetése
Az Azure AD Connect szinkronizálása a Forefront Identity Manager 2010-ben bevezetett deklaratív kiépítésre épül. Ez lehetővé teszi, hogy végre a teljes identitás-integrációs üzleti logika anélkül, hogy a lefordított kódot kell írni.

A deklaratív kiépítés lényeges része az attribútumfolyamatokban használt kifejezésnyelv. A használt nyelv a Microsoft® Visual Basic® for Applications (VBA) egy részhalmaza. Ezt a nyelvet használják a Microsoft Office-ban, és a VBScript-ben jártas felhasználók is felismerik azt. A deklaratív kiépítési kifejezés nyelv csak függvényeket használ, és nem strukturált nyelv. Nincsenek módszerek vagy utasítások. A függvények ehelyett egymásba vannak ágyazva a programáramlás kifejezésére.

További információt [az Üdvözli az Office 2013 Visual Basic for Applications nyelvi útmutatója című témakörben talál.](https://msdn.microsoft.com/library/gg264383.aspx)

Az attribútumok erősen bevannak gépelve. Egy függvény csak a megfelelő típusú attribútumokat fogadja el. A kis- és nagybetűket is figyelembe vevő. Mind a függvényneveknek, mind az attribútumneveknek megfelelő burkolattal kell rendelkezniük, különben hiba történik.

## <a name="language-definitions-and-identifiers"></a>Nyelvi definíciók és azonosítók
* A függvények neve argumentumokkal és szögletes zárójelekben lévő argumentumokkal rendelkezik: FunctionName(argument1 argumentum, N argumentum).
* Az attribútumokat szögletes zárójelek azonosítják: [attribútumnév]
* A paramétereket százalékjelek azonosítják: %ParameterName%
* A karakterlánc-állandókat idézőjelek veszik körül: Például "Contoso" (Megjegyzés: egyenes idézőjeleket kell használnia", és nem intelligens idézőjeleket "")
* A numerikus értékek idézőjelek nélkül vannak kifejezve, és várhatóan tizedesek. A hexadecimális értékek a H &előtaggal vannak rögzítve. Például: 98052, &HFF
* A logikai értékek állandókkal vannak kifejezve: Igaz, Hamis.
* A beépített állandók és konstansok csak a nevükkel vannak kifejezve: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
A deklaratív kiépítés számos függvényt használ az attribútumértékek átalakításának lehetővé tétele érdekében. Ezek a függvények egymásba ágyazhatók, így az egyik függvény eredménye átkerül egy másik függvénybe.

`Function1(Function2(Function3()))`

A függvények teljes listája megtalálható a [függvényhivatkozásban.](reference-connect-sync-functions-reference.md)

### <a name="parameters"></a>Paraméterek
A paramétert egy összekötő vagy egy PowerShell t használó rendszergazda határozza meg. A paraméterek általában rendszerről rendszerre eltérő értékeket tartalmaznak, például annak a tartománynak a nevét, amelyben a felhasználó található. Ezek a paraméterek attribútumfolyamatokban használhatók.

Az Active Directory-összekötő a következő paramétereket adta meg a bejövő szinkronizálási szabályokhoz:

| Paraméter neve | Megjegyzés |
| --- | --- |
| Tartomány.Netbios |A jelenleg importáló tartomány Netbios formátuma, például FABRIKAMSALES |
| Tartomány.FQDN |Az importáló tartomány teljes tartománynév-formátuma, például sales.fabrikam.com |
| Tartomány.LDAP |Az importáló tartomány LDAP-formátuma, például DC=sales,DC=fabrikam,DC=com |
| Erdő.Netbios |A jelenleg importáló erdőnév Netbios formátuma, például FABRIKAMCORP |
| Erdő.FQDN |Az importáló erdőnév Teljes tartományneve, például fabrikam.com |
| Erdő.LDAP |Az importáló erdőnév LDAP-formátuma, például DC=fabrikam,DC=com |

A rendszer a következő paramétert biztosítja, amely az összekötő jelenleg futó azonosítójának leéséhez szolgál:  
`Connector.ID`

Íme egy példa, amely feltölti a metaverzum attribútum tartománya a netbios nevét a tartomány, ahol a felhasználó található:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátorok
A következő operátorok használhatók:

* **Összehasonlítás**: <, <=, <>, =, >, >=
* **Matematika**: +, \*-, , -
* **Karakterlánc**: & (összefűzés)
* **Logikai**: && (és), || vagy az, hogy az
* **Értékelési végzés**: ( )

Az operátorok értékelése balról jobbra történik, és ugyanaz takarási prioritással rendelkeznek. Ez azt \* jelenti, hogy a (szorzót) nem értékelik ki - (kivonás). 2\*(5+3) nem ugyanaz,\*mint a 2 5+3. A szögletes zárójelek ( ) a kiértékelési sorrend módosítására szolgálnak, ha a balról jobbra történő értékelési sorrend nem megfelelő.

## <a name="multi-valued-attributes"></a>Többértékű attribútumok
A függvények egyértékű és többértékű attribútumokon is működhetnek. A többértékű attribútumok esetében a függvény minden értéken működik, és ugyanazt a függvényt alkalmazza minden értékre.

Példa:  
`Trim([proxyAddresses])`A proxyAddress attribútum minden értékének vágása.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Minden értéknél @-signcserélje le a @contoso.comtartományt a értékre.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Keresse meg a SIP-címet, és távolítsa el az értékeket.

## <a name="next-steps"></a>További lépések
* A konfigurációs modellről a [Deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című témakörben olvashat bővebben.
* Tekintse meg, hogyan használja a deklaratív kiépítést a beépített használat [az alapértelmezett konfiguráció ismertetése című](concept-azure-ad-connect-sync-default-configuration.md)témakörben.
* Tekintse meg, hogyan lehet gyakorlati módosítást eszközolni a deklaratív kiépítés használatával [az Alapértelmezett konfiguráció módosítása](how-to-connect-sync-change-the-configuration.md)című témakörben.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

**Referencia-témakörök**

* [Azure AD Connect szinkronizálás: Függvények – referencia](reference-connect-sync-functions-reference.md)

