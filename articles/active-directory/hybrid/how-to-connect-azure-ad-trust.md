---
title: Azure AD Connect – Az AD FS-megbízhatóság kezelése az Azure AD-vel az Azure AD Connect használatával | Microsoft dokumentumok
description: Az Azure AD megbízhatósági kezelésének működési részletei az Azure AD connect által.
keywords: AD FS, ADFS, AD FS-kezelés, AAD Connect, Connect, Azure AD, trust, AAD, jogcím, jogcím, jogcímszabályok, kiállítás, átalakítás, szabályok, biztonsági mentés, visszaállítás
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3e521fb7668305ce511aaddd63ed2cce8dfed0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331722"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>AD FS-megbízhatóság Azure AD általi kezelése az Azure AD Connect használatával

## <a name="overview"></a>Áttekintés

Az Azure AD Connect kezelheti a helyszíni Active Directory-összevonási szolgáltatás (AD FS) és az Azure AD közötti összevonást. Ez a cikk áttekintést nyújt a következőkről:

* Az Azure AD Connect által a bizalmi kapcsolaton konfigurált különböző beállítások
* Az Azure AD Connect által beállított kiállításátalakítási szabályok (jogcímszabályok)
* A frissítésés a konfigurációs frissítések közötti jogcímszabályok biztonsági másolatot és visszaállítását. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Az Azure AD Connect által vezérelt beállítások

Az Azure AD Connect **csak** az Azure AD megbízhatósági kapcsolathoz kapcsolódó beállításokat kezeli. Az Azure AD Connect nem módosítja az AD FS más függő entitás megbízhatósági beállításait. Az alábbi táblázat az Azure AD Connect által vezérelt beállításokat mutatja be.

| Beállítás | Leírás |
| :--- | :--- |
| Jogkivonat-aláíró tanúsítvány | Az Azure AD Connect segítségével alaphelyzetbe állíthatja és újra létrehozhatja a bizalmi kapcsolatot az Azure AD-vel. Az Azure AD Connect az AD FS tokenaláíró tanúsítványainak egyszeri azonnali átütemezését végzi el, és frissíti az Azure AD tartományösszevonási beállításait.|
| Token aláíró algoritmus | A Microsoft azt javasolja, hogy az SHA-256-ot használja jogkivonat-aláíró algoritmusként. Az Azure AD Connect észleli, ha a token aláíró algoritmus van beállítva, hogy egy kevésbé biztonságos értéket, mint az SHA-256. A következő lehetséges konfigurációs műveletben frissíti a beállítást SHA-256-ra. Az új jogkivonat-aláíró tanúsítvány használatához frissíteni kell a többi függő entitás megbízhatóságát. |
| Azure AD megbízhatósági azonosító | Az Azure AD Connect beállítja a megfelelő azonosító értéket az Azure AD megbízhatósági kapcsolat. Az AD FS egyedileg azonosítja az Azure AD megbízhatósági kapcsolatot az azonosító érték használatával. |
| Azure AD-végpontok | Az Azure AD Connect gondoskodik arról, hogy az Azure AD megbízhatósági beállításokhoz konfigurált végpontok mindig a rugalmasság és a teljesítmény ajánlott legfrissebb értékei szerint vannak. |
| Kiállításátalakítási szabályok | Az Azure AD-funkciók optimális teljesítményéhez összevont beállításban igénylő jogcímszabályok száma i. Az Azure AD Connect gondoskodik arról, hogy az Azure AD megbízhatósági mindig az ajánlott jogcímszabályok megfelelő készletével van konfigurálva. |
| Alternatív azonosító | Ha a szinkronizálás úgy van beállítva, hogy alternatív azonosítót használjon, az Azure AD Connect konfigurálja az AD FS-t az alternatív azonosító használatával történő hitelesítés végrehajtására. |
| Automatikus metaadat-frissítés | Az Azure AD megbízhatósága automatikus metaadat-frissítéshez van konfigurálva. Az AD FS rendszeres en rendszeresen ellenőrzi az Azure AD-megbízhatóság imetaadatait, és naprakészen tartja azt, ha az az Azure AD-oldalon megváltozik. |
| Integrált Windows-hitelesítés (IWA) | A hibrid Azure AD-csatlakozás során az IWA engedélyezve van az eszközregisztrációhoz, hogy megkönnyítse a hibrid Azure AD-csatlakozást az emelt szintű eszközökhöz |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Az Azure AD Connect által konfigurált végrehajtási folyamatok és összevonási beállítások

Az Azure AD connect nem frissíti az Azure AD megbízhatósági kapcsolat összes beállítását a konfigurációs folyamatok során. A módosított beállítások attól függnek, hogy melyik feladat vagy végrehajtási folyamat kerül végrehajtásra. Az alábbi táblázat a különböző végrehajtási folyamatokban érintett beállításokat sorolja fel.

| Végrehajtási folyamat | Érintett beállítások |
| :--- | :--- |
| Első menetes telepítés (expressz) | None |
| Első menetben történő telepítés (új AD FS farm) | Egy új AD FS-farm jön létre, és az Azure AD-vel egy bizalmi kapcsolat jön létre a semmiből. |
| Első lépés telepítése (meglévő AD FS-farm, meglévő Azure AD megbízhatóság) | Azure AD megbízhatósági azonosító, issuance átalakítási szabályok, Azure AD-végpontok, Alternatív azonosító (ha szükséges), automatikus metaadat-frissítés |
| Az Azure AD megbízhatóságának alaphelyzetbe állítása | Jogkivonat-aláíró tanúsítvány, jogkivonat-aláíró algoritmus, Azure AD megbízhatósági azonosító, kiállításátalakítási szabályok, Azure AD-végpontok, Alternatív azonosító (ha szükséges), automatikus metaadat-frissítés |
| Összevonási kiszolgáló hozzáadása | None |
| WAP-kiszolgáló hozzáadása | None |
| Eszközbeállítások | Kiállítási átalakítási szabályok, IWA az eszköz regisztrációhoz |
| Összevont tartomány hozzáadása | Ha a tartomány t adódik az első alkalommal, azaz a telepítő változik egy tartomány összevonása többtartományos összevonás – Az Azure AD Connect újra létrehozza a bizalmi kapcsolatot a semmiből. Ha az Azure AD-vel való bizalmi kapcsolat már több tartományhoz van konfigurálva, csak a kiállítási átalakítási szabályok módosulnak |
| Tls frissítése | None |

Az Azure AD Connect minden olyan művelet során, amelyben bármely beállítás módosul, biztonsági másolatot készít az aktuális megbízhatósági beállításokról a **%ProgramData%\AADConnect\ADFS mappában.**

![Az Azure AD Connect lap a meglévő Azure AD megbízhatósági biztonsági mentésével kapcsolatos üzenetet jeleníti meg](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Az 1.1.873.0-s verzió előtt a biztonsági másolat csak a kiállítási átalakítási szabályokból állt, és a varázsló nyomkövetési naplófájljában biztonsági másolat került róluk.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Az Azure AD Connect által beállított kibocsátás-átalakítási szabályok

Az Azure AD Connect gondoskodik arról, hogy az Azure AD megbízhatósági mindig az ajánlott jogcímszabályok megfelelő készletével van konfigurálva. A Microsoft azt javasolja, hogy az Azure AD connect használatával kezelje az Azure AD-megbízhatósági kapcsolat. Ez a szakasz a kiállításátalakítási szabályok készletét és azok leírását sorolja fel.

| Szabály neve | Leírás |
| --- | --- |
| Upn kiadása | Ez a szabály lekérdezi a userprincipalname értékét a userprincipalname szinkronizálási beállításaiban konfigurált attribútumból.|
| Az egyéni objektumialkó jogcím objectguid és msdsconsistencyguid lekérdezése | Ez a szabály ideiglenes értéket ad hozzá a folyamathoz az objectguid és msdsconsistencyguid értékhez, ha létezik. |
| Msdsconsistencyguid meglétének ellenőrzése | Attól függően, hogy az msdsconsistencyguid értéke létezik-e vagy sem, ideiglenes jelzőt állítunk be, amely az ImmutableId-ként használandó értéket irányítja. |
| Probléma msdsconsistencyguid a nem módosítható azonosító, ha létezik | Probléma msdsconsistencyguid as ImmutableId if the value exists |
| Issue objectGuidRule , ha az msdsConsistencyGuid szabály nem létezik | Ha az msdsconsistencyguid értéke nem létezik, az objectguid értéke ImmutableId lesz kiadva. |
| Probléma névazonosítója | Ez a szabály adja ki a névazonosító jogcím értékét.|
| Problémafiók-típus tartományhoz csatlakozó számítógépekhez | Ha a hitelesítés alatt lévő entitás tartományhoz csatlakozott eszköz, ez a szabály a fióktípust DJ-ként bocsátja ki, amely tartományhoz csatlakozott eszközt jelent. |
| A AccountType kiadása a USER értékkel, ha az nem számítógépfiók | Ha a hitelesítendő entitás felhasználó, ez a szabály a fióktípust felhasználóként bocsátja ki. |
| Probléma kiadása, ha nem számítógépfiók | Ez a szabály akkor adja ki a kiállítóid értékét, ha a hitelesítő entitás nem eszköz. Az érték egy regex en keresztül jön létre, amelyet az Azure AD Connect konfigurál. A regex jön létre, figyelembe véve az összes tartomány ban összevont az Azure AD Connect használatával. |
| Probléma issuerid dj számítógép auth | Ez a szabály adja ki a kibocsátó értékét, ha a hitelesítő entitás eszköz |
| Probléma onpremobjectguid tartományhoz csatlakozó számítógépekhez | Ha a hitelesítés alatt álló entitás tartományhoz csatlakozott eszköz, ez a szabály kiadja az eszköz helyszíni objektumguid-ját |
| Áthaladás elsődleges SID-n | Ez a szabály kiadja a hitelesítő entitás elsődleges BIZTONSÁGI azonosítóját |
| Áthaladási jogcím - insideCorporateNetwork | Ez a szabály olyan jogcímet ad ki, amely segít az Azure AD-nek megtudni, hogy a hitelesítés vállalati hálózaton belülről vagy külsőleg érkezik-e. |
| Pass Through Követelés - Psso |   |
| Jelszó lejárati jogcímek kiadása | Ez a szabály három jogcímet ad ki a jelszó lejárati idejére, a jelszó hitelesítése esetén eltelt napok számával, valamint azt az URL-címet, amelyet a jelszó módosításához irányítson.|
| Áthaladás ionduláció – authnmethodsreferences | Az e szabály alapján kibocsátott jogcím értéke azt jelzi, hogy milyen típusú hitelesítést hajtottak végre az entitáson. |
| Áthaladás jogcím - többtényezős hitelesítésinstant | A jogcím értéke azt az időt adja meg UTC-ben, amikor a felhasználó utoljára végzett többtényezős hitelesítést. |
| Áthaladási jogcím - AlternateLoginID | Ez a szabály kiadja az AlternateLoginID jogcímet, ha a hitelesítést alternatív bejelentkezési azonosítóval hajtották végre. |

> [!NOTE]
> A problémaegyszerű stafal és az ImmutableId jogcímszabályai eltérőek lesznek, ha nem alapértelmezett választási lehetőséget használ az Azure AD Connect konfigurációja során

## <a name="restore-issuance-transform-rules"></a>Kiállításátalakítási szabályok visszaállítása

Az Azure AD Connect 1.1.873.0-s vagy újabb verziója biztonsági másolatot készít az Azure AD megbízhatósági beállításairól, amikor az Azure AD megbízhatósági beállításait frissíti. Az Azure AD megbízhatósági beállításairól a **%ProgramData%\AADConnect\ADFS mappában készül biztonsági másolatot.** A fájlnév a következő formátumú&lt;AadTrust- dátum&gt;-&lt;idő&gt;.txt, például - AadTrust-20180710-150216.txt

![Az Azure AD megbízhatóságának példáját](./media/how-to-connect-azure-ad-trust/backup.png)

A kiállítási átalakítási szabályok az alábbi javasolt lépésekkel állíthatók vissza

1. Az AD FS felügyeleti felhasználói felületének megnyitása a Kiszolgálókezelőben
2. Nyissa meg az Azure AD megbízhatósági tulajdonságait az **AD FS &gt; függő entitás megbízhatóságának &gt; microsoft Office 365 identitásplatformon &gt; lévő jogcímkiállítási szabályzat szerkesztésével**
3. Kattintson a **Szabály hozzáadása gombra**
4. A jogcímszabály sablonban válassza a Jogcímek küldése egyéni szabály használatával lehetőséget, és kattintson a **Tovább** gombra.
5. Másolja a jogcímszabály nevét a biztonságimásolat-fájlból, és illessze be a **Jogcímszabály nevébe**
6. Másolja a jogcímszabályt a biztonságimásolat-fájlból az **Egyéni szabály** szövegmezőjébe, és kattintson a **Befejezés** gombra

> [!NOTE]
> Győződjön meg arról, hogy a további szabályok nem ütköznek az Azure AD Connect által konfigurált szabályokkal.

## <a name="next-steps"></a>További lépések
* [Az Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connect használatával](how-to-connect-fed-management.md)
