---
title: Az Azure AD Connect szinkronizálási szolgáltatásának árnyékattribútumai | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy az Azure AD Connect szinkronizálási szolgáltatásában hogyan működnek az árnyékattribútumok.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60384705"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Az Azure AD Connect szinkronizálási szolgáltatás árnyékattribútumai
A legtöbb attribútum ugyanúgy jelenik meg az Azure AD-ben, mint a helyszíni Active Directoryban. Egyes attribútumok azonban rendelkeznek valamilyen speciális kezeléssel, és az Azure AD attribútumértéke eltérhet az Azure AD Connect szinkronizálását.

## <a name="introducing-shadow-attributes"></a>Árnyékattribútumok bemutatása
Egyes attribútumok két ábrázolása az Azure AD-ben. Mind a helyszíni érték, mind a számított érték tárolásra kerül. Ezeket az extra attribútumokat árnyékattribútumnak nevezzük. A két leggyakoribb attribútum, ahol ezt a viselkedést látja, a **userPrincipalName** és **a proxyAddress**. Az attribútumértékek változása akkor következik be, ha ezekben az attribútumokban nem ellenőrzött tartományokat képviselő értékek vannak. De a szinkronizálási motor a Connect beolvassa az értéket az árnyék attribútum, így annak szemszögéből, az attribútum megerősítette az Azure AD.

Az árnyékattribútumok nem láthatók az Azure Portalon vagy a PowerShell használatával. De a koncepció megértése segít elhárítani bizonyos forgatókönyveket, ahol az attribútum különböző értékeket tartalmaz a helyszínen és a felhőben.

A viselkedés jobb megértéséhez tekintse meg ezt a példát a Fabrikam-tól:  
![Tartományok](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
A helyszíni Active Directoryban több UPN-utótagot is elkell, de csak egyet ellenőriztek.

### <a name="userprincipalname"></a>userPrincipalName
A felhasználó a következő attribútumértékekkel rendelkezik egy nem ellenőrzött tartományban:

| Attribútum | Érték |
| --- | --- |
| helyszíni felhasználóPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD felhasználóPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

A userPrincipalName attribútum a PowerShell használatakor látható érték.

Mivel a tényleges helyszíni attribútumértéke az Azure AD-ben tárolódik, amikor ellenőrzi a fabrikam.com tartományt, az Azure AD frissíti a userPrincipalName attribútumot a shadowUserPrincipalName értékével. Az értékek frissítéséhez nem kell szinkronizálnia az Azure AD Connect módosításait.

### <a name="proxyaddresses"></a>proxyAddresses
Ugyanez a folyamat csak ellenőrzött tartományok hozzáadásával is előfordul proxyAddresses, de néhány további logika. Az ellenőrzött tartományok ellenőrzése csak a postaláda-felhasználók esetében történik. A levelezésre képes felhasználó vagy kapcsolattartó egy másik Exchange-szervezet felhasználóját képviseli, és a proxycímek ben lévő bármely értéket hozzáadhatja ezekhez az objektumokhoz.

Egy postaláda-felhasználó ( helyszíni vagy Exchange Online) esetén csak az ellenőrzött tartományok értékei jelennek meg. Így is nézhet ki:

| Attribútum | Érték |
| --- | --- |
| helyszíni proxycímek | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxycímek | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Ebben az esetben **az smtp:abbie.spencer\@fabrikam.com** eltávolításra került, mivel a tartomány nem lett ellenőrizve. De az Exchange is hozzá **SIP:\@abbie.spencer fabrikamonline.com**. A Fabrikam nem használta a helyszíni Lyncet/Skype-ot, de az Azure AD és az Exchange Online felkészülhet rá.

Ezt a logikát proxycímeknek **nevezik ProxyCalc**. A ProxyCalc meghívása a felhasználó minden módosításával történik, ha:

- A felhasználóhoz olyan szolgáltatási csomag tartozik, amely tartalmazza az Exchange Online-t, még akkor is, ha a felhasználó nem rendelkezett licenccel az Exchange számára. Ha például a felhasználó az Office E3 termékváltozathoz van rendelve, de csak sharePoint Online-hoz van rendelve. Ez akkor is igaz, ha a postaláda még mindig a helyszínen van.
- Az msExchRecipientTypeDetails attribútumnak van egy értéke.
- Módosítja a proxyAddresses vagy a userPrincipalName.

A ProxyCalc egy ideig eltarthat egy felhasználó ni-változási feldolgozása, és nem szinkronban van az Azure AD Connect exportálási folyamatával.

> [!NOTE]
> A ProxyCalc logika rendelkezik néhány további viselkedést a témakörben nem dokumentált speciális forgatókönyvekhez. Ez a témakör a viselkedés megértéséhez szolgál, és nem dokumentálja az összes belső logikát.

### <a name="quarantined-attribute-values"></a>Karanténba helyezett attribútumértékek
Az árnyékattribútumok akkor is használatosak, ha ismétlődő attribútumértékek vannak. További információ: [duplikált attribútumrugalmasság](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Lásd még
* [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [A helyszíni identitások integrálása az Azure Active Directoryval.](whatis-hybrid-identity.md)
