---
title: 'Az Azure AD Connect szinkronizálása: Understanding felhasználók, csoportok és partnerek |} A Microsoft Docs'
description: Ismerteti a felhasználók, csoportok és névjegyeit, állítsa be az Azure AD Connect szinkronizálása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7605a8cee265822f133b3f72ce5de90add5fc0d0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210543"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Az Azure AD Connect szinkronizálása: Understanding felhasználók, csoportok és partnerek
Miért kell több Active Directory-erdők és számos különböző központi telepítési topológiák több különböző oka is van. Közös modellek között egy fiók-erőforrások üzembe helyezésének és GAL sync'ed erdők egy egyesülés & megszerzése után. De még akkor is, ha vannak tiszta modelleket, a hibrid modellek is. Az alapértelmezett konfiguráció az Azure AD Connect szinkronizálása nem feltételezi azt minden adott modell, de attól függően, hogyan egyező felhasználó kiválasztott a telepítési útmutatóban, másként figyelhető meg.

Ebben a témakörben végigvesszük azokat az alapértelmezett konfiguráció viselkedésének bizonyos topológiákat. Végigvesszük azokat a konfigurációs és szinkronizálási Szabályszerkesztővel segítségével tekintse meg a konfigurációt.

A konfiguráció feltételezi, hogy néhány általános szabályok vonatkoznak:
* Függetlenül attól, milyen sorrendben importáljuk a forrás-Active-könyvtárak a végeredmény mindig legyen ugyanaz.
* Egy aktív fiók mindig is hozzájárul a bejelentkezési adatait, beleértve a **userPrincipalName** és **sourceAnchor**.
* Letiltott fiók is hozzájárul a userPrincipalName és sourceAnchor, kivéve ha a hivatkozott postafiókkal, ha nincs aktív fiók található.
* A userPrincipalName és a sourceAnchor egy fiókra a hivatkozott postafiókkal soha nem használható. Azt feltételezzük, hogy aktív-fiók később található.
* Egy kapcsolatobjektummal előfordulhat, hogy üzembe helyezve az Azure AD-ügyfélként vagy felhasználóként. Nem igazán ismeri mindaddig, amíg az összes forrás Active Directory-erdők dolgozott.

## <a name="groups"></a>Csoportok
Fontos tudnivalók tisztában lennie a csoportok az Active Directoryból az Azure AD szinkronizálása során:

* Az Azure AD Connect kizárja a beépített biztonsági csoportokat a címtár-szinkronizálás.

* Az Azure AD Connect nem támogatja a szinkronizálás [elsődleges csoporttagságok](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) az Azure ad-hez.

* Az Azure AD Connect nem támogatja a szinkronizálás [dinamikus terjesztési csoporttagságok](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) az Azure ad-hez.

* Az Azure AD egy levelezési csoport, egy Active Directory-csoport szinkronizálása:

    * Ha a csoport *proxyAddress* attribútum értéke üres, a *mail* atribut musí mít hodnotu

    * Ha a csoport *proxyAddress* attribútum értéke nem lehet üres, akkor tartalmaznia kell legalább egy SMTP-proxycíme. Néhány példa:
    
      * Egy Active Directory-csoportot, amelynek proxyAddress attribútum értéke *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* nem lesz, levelezési címmel rendelkező Azure AD-ben. Nem rendelkezik egy SMTP-cím.
      
      * Egy Active Directory-csoportot, amelynek proxyAddress attribútuma értékkel rendelkezik *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe@contoso.com"}* lesz, levelezési címmel rendelkező Azure AD-ben.
      
      * Egy Active Directory-csoportot, amelynek proxyAddress attribútuma értékkel rendelkezik *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe@contoso.com"}* is lesz, levelezési címmel rendelkező Azure AD-ben.

## <a name="contacts"></a>Kapcsolatok
Névjegyek jelölő egy felhasználó egy másik erdőben találhatók, akkor az általános egy egyesülés & megszerzése után ahol GALSync megoldás az adatközponthíd-képzés két vagy több Exchange-erdő. A kapcsolattartó objektumot az összekötőtérben, mindig csatlakoztatását a, a mail attribútum használata a metaverzumba. Ha már van egy ügyfél vagy felhasználói objektum azonos e-mail-, az objektumok össze vannak kapcsolva. Ez a szabály konfigurálható **a az AD-ből – kapcsolattartó csatlakozzon**. Emellett van egy nevű szabályt **a az AD-ből – kapcsolattartó közös** a metaverzum-attribútum, az Attribútumfolyam- **sourceObjectType** az a konstans **ügyfél**. Ez a szabály rendelkezik nagyon alacsony prioritású, ha bármely felhasználói objektum csatlakozik ugyanahhoz a metaverzum-objektumhoz, akkor a szabály **a az AD-ből – felhasználói közös** is hozzájárul a felhasználói értéket ehhez az attribútumhoz. Ez a szabály a ezt az attribútumot lesz a kapcsolattartó, ha a felhasználó nem lett csatlakoztatva van és az értéke felhasználói Ha talált legalább egy felhasználót.

Üzembe helyezés az Azure AD, a kimenő szabály objektum **vette az AAD-csatlakozás forduljon** kapcsolattartási objektumot hoz létre, ha a metaverzum-attribútum **sourceObjectType** értékre van állítva **kapcsolatba**. Ha ez az attribútum értéke **felhasználói**, majd a szabály **ki az aad-be – a felhasználó csatlakozzon** egy felhasználói objektumot hoz létre.
Lehetséges, hogy az objektum lett előléptetve partner, felhasználói, ha több aktív adatforrás-könyvtár vannak importálva és szinkronizálva.

Például GALSync topológiában fog találtunk kapcsolattartási objektumok mindenki számára a második erdőben az első erdőt importálása során. Ez lesz automatikusan az AAD-összekötő az új ügyfél objektumokat. Ha később importálja és szinkronizálja a másik erdő, hogy megtalálja a valódi felhasználók, és csatlakoztassa őket a meglévő metaverzum-objektum. A Microsoft ezután az aad-ben a kapcsolattartási objektum törlése és inkább hozzon létre egy új felhasználói objektumot.

Ha egy topológia, ahol a felhasználók jelentésekként jelennek meg a névjegyek, mindenképpen jelölje ki a felhasználók a mail attribútum, a telepítési útmutatóban. Ha egy másik lehetőséget választja, akkor egy rendelés-függő konfigurációban fog működni. Kapcsolattartási objektumok mindig csatlakozni fog a mail attribútum, de a felhasználói objektumok csatlakozni a mail attribútum csak fognak, ha ezt a beállítást választotta, a telepítési útmutatóban. Ön sikerült majd végül két másik objektum azonos mail attribútuma a metaverzumban található ha az ügyfél objektum lett importálva, előtt a felhasználói objektum. Az Azure AD-exportálás során hiba történt, a rendszer hibajelzést. Ez a viselkedés a rendszer kialakításából fakad, és azt jelzi, helytelen adatok vagy az, hogy a topológia nem megfelelően észlelt a telepítés.

## <a name="disabled-accounts"></a>Letiltott fiókokat
Letiltott fiókokat, valamint szinkronizált Azure ad-hez. Letiltott fiókokat gyakoriak, amelyek Exchange-hez, például a konferenciahívások erőforrásokat. A kivétel a következő hivatkozott postafiókkal; rendelkező felhasználók Ahogy korábban említettük ezek soha nem kiépíti az Azure AD-fiókot.

Feltételezzük, hogy ha egy letiltott felhasználó fiókot talál, akkor lesz nem találtunk egy másik aktív fiók és az objektum ki van építve az Azure AD userPrincipalName és sourceAnchor található. Abban az esetben egy másik aktív fiók, akkor csatlakozik ugyanahhoz a metaverzum-objektumhoz, majd a userPrincipalName és sourceAnchor történik.

## <a name="changing-sourceanchor"></a>SourceAnchor módosítása
Ha egy objektum lett exportálva az Azure AD, majd a sourceAnchor többé módosítása nem engedélyezett. Ha az objektum lett exportálva a metaverzum-attribútum **cloudSourceAnchor** van beállítva a **sourceAnchor** Azure AD által elfogadott érték. Ha **sourceAnchor** módosul, és nem egyezik **cloudSourceAnchor**, a szabály **ki az aad-be – a felhasználó csatlakozzon** kivételt fogja kijelezni a hiba **sourceAnchor attribútum módosított**. Ebben az esetben a konfigurációs vagy az adatok ki kell javítani, így az azonos sourceAnchor megtalálható a metaverzumban újra előtt az objektum újra szinkronizálni kell.

## <a name="additional-resources"></a>További források
* [Az Azure AD Connect szinkronizálása: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

