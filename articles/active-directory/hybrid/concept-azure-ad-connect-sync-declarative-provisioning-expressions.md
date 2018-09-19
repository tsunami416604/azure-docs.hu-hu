---
title: 'Az Azure AD Connect: A deklaratív kiépítés kifejezéseinek |} A Microsoft Docs'
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
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 26c835e6c2f658f7ad852fdd02dc8974db33e47b
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312066"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Az Azure AD Connect szinkronizálása: deklaratív kiépítés kifejezéseinek ismertetése
Az Azure AD Connect szinkronizálása a deklaratív kiépítés a Forefront Identity Manager 2010 rendszerben bevezetett épül. Ez lehetővé teszi, hogy a teljes körű identitás integrációs üzleti logikát lefordított kód írása nélkül.

Deklaratív kiépítés fontos része az attribútumfolyamok kifejezés nyelve. A használt nyelv alkészlete a Microsoft® Visual Basic® Applications (VBA). Ezen a nyelven a Microsoft Office használja, és a VBScript élménye rendelkező felhasználók is felismeri azt. A deklaratív kiépítés Kifejezésnyelveket csak funkciókat használ, és nem strukturált nyelvet. Nincsenek módszerek vagy utasításokat. Az express programot a folyamat inkább beágyazott függvények.

További részletekért lásd: [Üdvözli az alkalmazások nyelvi dokumentáció az Office 2013 a Visual Basic](https://msdn.microsoft.com/library/gg264383.aspx).

Az attribútumok vannak listaobjektum. A függvény csak a megfelelő típusú attribútumokat fogad el. Emellett akkor is kis-és nagybetűket. Mind a nevét, és a attribútumnevek rendelkeznie kell a megfelelő kis-és nagybetűhasználatot, vagy egy hiba lépett fel.

## <a name="language-definitions-and-identifiers"></a>Nyelvi definíciók és azonosítók
* Függvények, a neve, és a zárójelek argumentumok: (argument 1, argumentum N) függvénynév.
* Attribútumok szögletes zárójelek azonosítja: [attributeName]
* Paraméterek százalékjelek azonosítja: % ParameterName %
* Karakterlánc-állandókat idézőjelek között veszi körül: például "Contoso" (Megjegyzés: kell használnia a Egyenes idézőjel "", és nem az ajánlatok intelligens "")
* Numerikus értékek idézőjeleket kifejezett és kell lennie a tizedes tört. Hexadecimális értékek van fűzve előtagként & h Ha például a 98052 & HFF
* Az állandókat ki, a logikai értékek: True, False.
* Beépített állandók és az sztringkonstansoknak kell megadni, amelyek neve csak: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Számos függvényt deklaratív kiépítés használja ahhoz, hogy átalakítja a attribútumértékek lehetőségét. Ezek a függvények beágyazhatja, így az eredmény egy függvény egy másik függvénynek átadott.

`Function1(Function2(Function3()))`

A funkciók teljes listája megtalálható a [függvény hivatkozási](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Paraméterek
Egy paraméter van definiálva, vagy egy összekötőt, vagy PowerShell segítségével a rendszergazda. Paraméterek általában tartalmaznak, amelyek különböző operációs rendszerek közötti értékeket, például a neve annak a tartománynak a felhasználó található. Ezeket a paramétereket az attribútumfolyamok használható.

Az Active Directory-összekötő a következő paraméterek megadott bejövő szinkronizálási szabályok:

| Paraméter neve | Megjegyzés |
| --- | --- |
| Domain.Netbios |A tartomány jelenleg importált, például FABRIKAMSALES NetBIOS-formátum |
| Domain.FQDN |A tartomány jelenleg importált, például sales.fabrikam.com (FQDN) formátumban |
| Domain.LDAP |LDAP-formátum jelenleg importált, a tartomány például DC értékesítés, DC = fabrikam, DC = = com |
| Forest.Netbios |Az erdő nevét jelenleg importált, például FABRIKAMCORP NetBIOS formátuma |
| Forest.FQDN |Az erdő nevének jelenleg importált, például fabrikam.com (FQDN) formátumban |
| Forest.LDAP |LDAP formátumát az erdő nevét jelenleg importált, például: DC = fabrikam, DC = com |

A rendszer a következő paramétert, amely kéri le az aktuálisan futó összekötő azonosítóját tartalmazza:  
`Connector.ID`

Íme egy példa, amely feltölti a metaverzum attribútum tartományba a tartomány, ahol a felhasználó megtalálható a netbios-neve:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátorok
Az alábbi operátorok használhatók:

* **Összehasonlító**: <, < =, <>, =, >, > =
* **Matematika**: +, -, \*, -
* **Karakterlánc**: & (ÖSSZEFŰZ)
* **Logikai**: & & (és). (vagy)
* **Fontossági sorrendjének**:)

Operátorok értékeli ki a balról jobbra, és azonos prioritású kiértékelése. Ez azt jelenti, hogy a \* (szorzó) nem kerül kiértékelésre, mielőtt - (kivonás). 2\*(5 + 3) nem ugyanaz, mint 2\*5 + 3. A zárójelek közé () segítségével fontossági sorrendjének módosítása, ha a megfelelő fontossági sorrendjének balról nem megfelelő.

## <a name="multi-valued-attributes"></a>Többértékű attribútumok
Az funkciók egyaránt egyértékű és többértékű attribútumok működhet. Többértékű attribútumok a függvény minden egyes értékhez keresztül működik, és alkalmazhatja azokat ugyanannak a függvénynek minden egyes értékhez.

Példa:  
`Trim([proxyAddresses])` Hajtsa végre az proxyAddress attribútuma minden érték Trim.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Minden érték és a egy @-sign, cserélje le a tartományba @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Keresse meg a SIP-cím, és távolítsa el az értékeket.

## <a name="next-steps"></a>További lépések
* További információ a konfigurációs modell [ismertetése deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Lásd: hogyan deklaratív kiépítés használt out-of-box a [az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md).
* Tekintse meg gyakorlati módosítást használata a deklaratív kiépítés [hogyan lehet módosítani az alapértelmezett konfiguráció](how-to-connect-sync-change-the-configuration.md).

**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: ismertetése, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

**Referencia-témakörök**

* [Az Azure AD Connect szinkronizálása: Functions – referencia](reference-connect-sync-functions-reference.md)

