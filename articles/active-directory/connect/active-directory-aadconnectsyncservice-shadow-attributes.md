---
title: "Az Azure AD Connect szinkronizálási szolgáltatás árnyékmásolat attribútumok |} Microsoft Docs"
description: "Ismerteti az árnyékmásolat attribútumok működése az Azure AD Connect szinkronizálási szolgáltatást."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0b6a7f22d744480a40a878c979986cdd7667109c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Az Azure AD Connect szinkronizálási szolgáltatás árnyékmásolat attribútumok
A legtöbb attribútumok vannak megadva ugyanúgy Azure AD-ben, mivel ezek a helyszíni Active Directoryban. Azonban néhány attribútum néhány különleges kezelést és az Azure AD-ben attribútumérték eltérhet az Azure AD Connect szinkronizálása.

## <a name="introducing-shadow-attributes"></a>Árnyékmásolat attribútumok bemutatása
Egyes attribútumok két felelősséget rendelkezik az Azure ad-ben. A helyszíni érték és a számított érték tárolja. A további attribútumok árnyékmásolat attribútumok nevezzük. A két leggyakoribb attribútumok, ahol megjelenik ez a viselkedés **userPrincipalName** és **proxyAddress**. Az attribútum értékei módosítása történik, ha ezek az attribútumok nem ellenőrzött tartományok jelölő értékek vannak. De a csatlakozás a szinkronizálási motor beolvassa az árnyék attribútum értéke, a szempontjából, az attribútum visszaigazolását Azure ad.

Nem található az árnyékmásolat-attribútumok az Azure portál vagy a PowerShell használatával. De a koncepció ismertetése segít bizonyos elhárításában, ha az attribútum helyszíni eltérő értékek tartoznak, és a felhőben.

Jobb megértése érdekében működését, tekintse meg ebben a példában a Fabrikam:  
![Tartományok](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
A helyszíni Active Directoryban több egyszerű Felhasználónévi utótagot rendelkeznek, de azok csak ellenőrzését egy.

### <a name="userprincipalname"></a>UserPrincipalName
A felhasználó a következő attribútumértékeit a nem ellenőrzött tartomány van:

| Attribútum | Érték |
| --- | --- |
| a helyszíni userPrincipalName | lee.sperry@fabrikam.com |
| Az Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Az Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

A userPrincipalName attribútum a értéke megjelenik, amikor a PowerShell használatával.

Mivel a tényleges helyszíni attribútumérték rendszer az Azure AD, ha ellenőrizte, fabrikam.com tartomány, az Azure AD frissíti a userPrincipalName attribútum a shadowUserPrincipalName értékével. Nincs a módosításokat az frissítenie kell ezeket az értékeket az Azure AD Connect szinkronizálása.

### <a name="proxyaddresses"></a>proxyAddresses
Ugyanezt az eljárást csak beleértve az ellenőrzött tartományok esetében is a proxyAddresses, de néhány további logikával következik be. Az ellenőrzött tartományok jelölőnégyzet csak akkor zajlik le postaláda-felhasználók számára. Egy levelezési felhasználó vagy az ügyfél felel meg a felhasználó egy másik Exchange-szervezetben, és adhat hozzá az értékeket a proxyAddresses ezeket az objektumokat.

Postaláda-felhasználónak, a helyszíni vagy az Exchange Online esetén csak az ellenőrzött tartományok értékek jelennek meg. Az nézhet ki:

| Attribútum | Érték |
| --- | --- |
| a helyszíni proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Ebben az esetben  **smtp:abbie.spencer@fabrikam.com**  el lett távolítva, mert az adott tartomány nincs ellenőrizve. De adott Exchange  **SIP:abbie.spencer@fabrikamonline.com** . Fabrikam nem használt Lync/Skype a helyszínen, de az Azure AD és az Exchange Online előkészítése.

Ez a módszer a proxyAddresses nevezzük **ProxyCalc**. ProxyCalc minden változás, a felhasználó meghívása során:

- A felhasználói azonosítót, amely tartalmazza az Exchange Online, még akkor is, ha a felhasználó nem rendelkezik licenccel az Exchange service-csomag. Ha például a felhasználó hozzá van rendelve a Office E3 SKU, de csak hozzá volt rendelve a SharePoint Online. Ez igaz, akkor is, ha a postaláda továbbra is a helyszíni.
- Az attribútum msExchRecipientTypeDetails értéke.
- Módosítja a proxyAddresses vagy a userPrincipalName.

ProxyCalc eltarthat egy ideig, a felhasználó változás feldolgozni, és nem az Azure AD Connect exportálási folyamat szinkronban.

> [!NOTE]
> A ProxyCalc logika van néhány további eszközöket nem ebben a témakörben leírt speciális forgatókönyvek esetén. Ez a témakör átláthatja viselkedését, és nem az összes belső logikai dokumentum valósul meg.

### <a name="quarantined-attribute-values"></a>Karanténba helyezett attribútumértékek
Árnyékmásolat attribútumok is használják, amikor ismétlődő attribútum-érték. További információkért lásd: [ismétlődő attribútum rugalmassági](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Lásd még:
* [Az Azure AD Connect szinkronizálása](active-directory-aadconnectsync-whatis.md)
* [A helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
