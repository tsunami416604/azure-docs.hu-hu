---
title: Az Azure AD Connect szinkronizálási szolgáltatás árnyékmásolat attribútumok |} A Microsoft Docs
description: Ismerteti az árnyékmásolat-attribútumok működéséről az Azure AD Connect szinkronizálási szolgáltatás.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 62278fa031c28fb32f882e438df0194c4db04efd
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46315223"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Az Azure AD Connect szinkronizálási szolgáltatás árnyékmásolat attribútumok
A legtöbb attribútumok ugyanúgy szerepelnek az Azure ad-ben az, ha azok a helyszíni Active Directoryban. De néhány attribútumokhoz néhány speciális kezelést, és lehet, hogy az Azure ad-attribútum értéke eltér az Azure AD Connect szinkronizálja.

## <a name="introducing-shadow-attributes"></a>Árnyék attribútumok bemutatása
Néhány attribútumokhoz két értük felelősséget az Azure ad-ben. A helyszíni érték és a egy számított érték tárolja. Ezek az extra attribútumok árnyékmásolat attribútumok nevezzük. Itt láthatja ezt a viselkedést a két leggyakoribb attribútumok **userPrincipalName** és **proxyAddress**. Az attribútumértékek módosítása történik, ha ezek az attribútumok nem ellenőrzött tartományok jelölő értékek vannak. De a csatlakozás a szinkronizálási motor beolvassa az árnyékmásolat-attribútum értéke így annak szempontjából, az attribútumot is megerősítette Azure ad-ben.

Nem látja az árnyékmásolat-attribútumok az Azure portal vagy a PowerShell használatával. Azonban bizonyos forgatókönyvek hibaelhárítása, ha az attribútum értékei eltérőek a helyszíni és felhőbeli megismerése fogalmat segítségével.

A viselkedés segít jobban megérteni, tekintse meg ebben a példában a Fabrikam:  
![Tartományok](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Azok a helyszíni Active Directoryban több egyszerű Felhasználónévi utótagot rendelkeznek, de azok csak ellenőrizte az egyik.

### <a name="userprincipalname"></a>userPrincipalName
A felhasználó rendelkezik a következő attribútumértékeit a nem ellenőrzött tartomány:

| Attribútum | Érték |
| --- | --- |
| a helyszíni userPrincipalName | lee.sperry@fabrikam.com |
| Az Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Az Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

A userPrincipalName attribútum az az érték, amikor a PowerShell használatával.

Mivel az valós helyszíni attribútum értéke rendszer az Azure AD-ben amikor ellenőrizte a fabrikam.com tartományt, az Azure AD a userPrincipalName attribútum frissíti az értéket a shadowUserPrincipalName. Nem kell frissíteni kell az ezeket az értékeket az Azure AD Connect a módosítások szinkronizálása.

### <a name="proxyaddresses"></a>proxyAddresses
A ugyanezt az eljárást csak beleértve az ellenőrzött tartományok is jelentkezik a proxyAddresses, de néhány további logikát. Ellenőrzött tartomány az ellenőrzése csak akkor zajlik le a postaláda-felhasználók számára. Egy levelezési címmel rendelkező felhasználó vagy kapcsolat felel meg az Exchange-szervezet egy másik felhasználó, és ezeket az objektumokat a proxyAddresses minden olyan értékeket is hozzáadhat.

Postaláda-felhasználónak, a helyszínen vagy az Exchange online-ban csak ellenőrzött tartományok értékei jelennek meg. Ez nézhet ki:

| Attribútum | Érték |
| --- | --- |
| a helyszíni proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange online-hoz proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Ebben az esetben **smtp:abbie.spencer@fabrikam.com** el lett távolítva, mivel az adott tartomány nem ellenőriztük. Azonban az Exchange is hozzá **SIP:abbie.spencer@fabrikamonline.com**. Fabrikam nem használt helyszíni Lync vagy a Skype, de az Azure ad-ben, és hozzá tartozó Exchange online-hoz készítse elő.

A logikai a proxyAddresses nevezzük **ProxyCalc**. ProxyCalc minden módosítást, a felhasználó meghívása során:

- A felhasználó egy szolgáltatáscsomag, amely tartalmazza az Exchange online-hoz, akkor is, ha a felhasználó nem rendelkezik licenccel az Exchange-hez lett hozzárendelve. Például ha a felhasználó az Office E3 csomag Termékváltozata van hozzárendelve, de csak hozzá volt rendelve a SharePoint Online. Ez igaz, akkor is, ha a postaláda továbbra is a helyszíni.
- Az attribútum msExchRecipientTypeDetails értéke.
- Módosítja a proxyAddresses vagy userPrincipalName.

ProxyCalc feldolgozni egy módosítást, a felhasználó hosszabb időt vehet igénybe, és nem teljesen szinkron és az Azure AD Connect exportálási folyamatot.

> [!NOTE]
> A ProxyCalc logika rendelkezik néhány további eszközöket nem ebben a témakörben leírt speciális forgatókönyvekhez. Ez a témakör a viselkedés értelmezése és dokumentálja az összes belső logikai biztosítunk.

### <a name="quarantined-attribute-values"></a>Karanténba helyezett attribútum értékei
Árnyékmásolat-attribútumokat is használhatók, ha ismétlődő attribútumértékekkel. További információkért lásd: [ismétlődő attribútumok rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Lásd még
* [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* [A helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).
