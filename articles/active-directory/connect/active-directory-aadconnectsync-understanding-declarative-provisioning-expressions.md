---
title: 'Az Azure AD Connect: Deklaratív kiépítés kifejezéseinek |} Microsoft Docs'
description: A deklaratív kiépítés kifejezéseinek ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 797c0949aceea415652a72df5ee23ef9888ab975
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect szinkronizálása: deklaratív kiépítés kifejezések ismertetése
Azure AD Connect szinkronizálása épít deklaratív kiépítés először a Forefront Identity Manager 2010 verzióban jelent. Lehetővé teszi a teljes identity integration üzleti logika lefordított kód írása nélkül végrehajtásához.

Nagyon fontos részét képezik deklaratív kiépítés megegyezik kifejezés attribútumfolyamok szerepel. A használt nyelv része a Microsoft® Visual Basic® Applications (VBA). Ezen a nyelven a Microsoft Office szolgál, és a VBScript élménye rendelkező felhasználók is felismeri azt. A deklaratív kiépítés kifejezés nyelvi csak funkciókat használ, és nem strukturált nyelvet. Nincsenek módszerek vagy utasításokat. Funkciók helyette beágyazott express program folyamokhoz.

További részletekért lásd: [üdvözli a Visual Basic for Applications nyelvi referencia az Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Az attribútumok vannak erős típusmegadású. A függvény csak a megfelelő típusú attribútumokat fogad el. Egyúttal kis-és nagybetűket. Mind a nevét, és az attribútumok nevében kell rendelkeznie a megfelelő kis-és nagybetűk, vagy hiba történt.

## <a name="language-definitions-and-identifiers"></a>Nyelvi definíciókat és -azonosítók
* Funkciók argumentumokat szögletes zárójelbe követ nevet adni: (1 argumentum, N argumentum) függvénynév.
* Attribútumok szögletes zárójelbe azonosítja: [attributeName]
* Paraméterek százalékjelek azonosítja: % ParameterName %
* A karakterlánckonstansokat ajánlatok tette: például "Contoso" (Megjegyzés: Egyenes idézőjel kell használnia. "", illetve nem idézőjelek között "")
* Numerikus értékek nélkül idézőjelek között kifejezett és várhatóan decimális. Hexadecimális értékek fűzve előtagként & H. Például a 98052 & HFF
* Logikai értékek szerint van megadva, az állandókat: True, False.
* Beépített állandók és literálok szerint van megadva. csak a nevükkel: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Deklaratív kiépítés használja sok funkciók engedélyezéséhez attribútumértékek átalakítás lehetőségét. Ezek a funkciók egymásba, az eredmény egy függvényből másik függvény kerül átadásra.

`Function1(Function2(Function3()))`

A funkciók teljes listája megtalálható a [hivatkozás működéséhez](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Paraméterek
A paraméter PowerShell használó rendszergazda vagy egy összekötő van definiálva. Paraméterek általában tartalmaznak, amelyek különböző operációs rendszerek közötti értékek, például található, a tartomány a felhasználó nevét. Ezek a paraméterek attribútumfolyamok használható.

Az Active Directory-összekötő előírt bejövő szinkronizálási szabályok a következő paraméterekkel:

| Paraméter neve | Megjegyzés |
| --- | --- |
| Domain.Netbios |A tartomány jelenleg importált, például FABRIKAMSALES NetBIOS formátuma |
| Domain.FQDN |A tartomány jelenleg importált, például sales.fabrikam.com FQDN-formátumban |
| Domain.LDAP |LDAP-formátum jelenleg importált, a tartomány például DC értékesítés, DC = fabrikam, DC = = com |
| Forest.Netbios |Az erdő nevének jelenleg importált, például FABRIKAMCORP NetBIOS formátuma |
| Forest.FQDN |Az erdő nevének jelenleg importált, fabrikam.com például az FQDN-formátumban |
| Forest.LDAP |LDAP formátuma az erdő nevének jelenleg importált, például: DC = fabrikam, DC = com |

A rendszer biztosítja a következő paraméter, amely lekérni a folyamatban az összekötő azonosítója:  
`Connector.ID`

Íme egy példa a metaverse attribútum tartomány a felhasználó a tartomány netbios-nevét feltöltő:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátorok
Az alábbi operátorok használhatók:

* **Összehasonlítás**: <, < =, <>, =, >, > =
* **Matematikai**: +, -, \*, -
* **Karakterlánc**: & (ÖSSZEFŰZ)
* **Logikai**: & & (és). (vagy)
* **Kiértékelési sorrend**:)

Operátorok értékeli ki a rendszer balról jobbra, és értékelési azonos prioritású. Ez azt jelenti, hogy a \* (szorzója) nem kerül kiértékelésre, mielőtt - (kivonás). 2\*(5 + 3) célja nem ugyanaz, mint 2\*5 + 3. A zárójelek () segítségével a kiértékelési sorrend módosításakor balról jobbra kiértékelési sorrend nem megfelelő.

## <a name="multi-valued-attributes"></a>Többértékű attribútumok
A funkciók is egyértékű és többértékű attribútumok is működik. Többértékű attribútumok a függvény minden egyes értékhez keresztül működik, és ugyanazt a funkciót alkalmazza minden egyes értékhez.

Példa:  
`Trim([proxyAddresses])` Minden a proxyAddress attribútum értékének Trim tegye.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Minden értékekre, egy @-sign, cserélje le a tartomány @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Keresse meg a SIP-cím, és távolítsa el az értékeket.

## <a name="next-steps"></a>További lépések
* További információk a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Lásd: hogyan deklaratív kiépítés használt out-of-box a [az alapértelmezett konfiguráció ismertetése](active-directory-aadconnectsync-understanding-default-configuration.md).
* Lásd: how to gyakorlati módosítja a deklaratív kiépítés használatával [hogyan lehet módosítani az alapértelmezett konfiguráció](active-directory-aadconnectsync-change-the-configuration.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

**Referencia-témakörei**

* [Azure AD Connect szinkronizálása: funkciók referencia](active-directory-aadconnectsync-functions-reference.md)

