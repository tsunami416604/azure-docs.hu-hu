---
title: Az Azure AD Connect – Azure AD-bA az Azure AD Connect az AD FS-megbízhatóság felügyelt |} A Microsoft Docs
description: Műveleti adatokat az Azure AD-megbízhatóság kezelése az Azure AD connect.
keywords: Az AD FS, ADFS, AD FS-kezelőben, AAD Connect, a csatlakozás, az Azure AD-megbízhatóság, aad-ben jogcím, a jogcím, szabályok, kiállítási, átalakítási, szabályok, biztonsági mentés, visszaállítás jogcím
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
ms.openlocfilehash: 5a45d776ea241e04aacf39e45f7bf6d2cc58c689
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172165"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>AD FS-megbízhatóság kezelése az Azure AD Connect használatával az Azure AD-vel

## <a name="overview"></a>Áttekintés

Az Azure AD Connect kezelheti az összevonást a helyszíni Active Directory összevonási szolgáltatás (AD FS) és az Azure között AD. Ez a cikk áttekintést nyújt:

* A megbízhatósági kapcsolatot konfigurált az Azure AD Connect különböző beállításokkal
* Az Azure AD Connect által beállított jogcímkiadás-átalakítási szabályok (jogcímszabályok)
* Biztonsági mentése és visszaállítása a jogcím-szabályok frissítések és frissítéseket. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Az Azure AD Connect által vezérelt beállítások

Kezeli az Azure AD Connect **csak** Azure AD-megbízhatóság kapcsolatos beállításokat. Az Azure AD Connect nem módosítja bármely más függő entitások megbízhatóságainak az AD FS beállításának. Az alábbi táblázat azt jelzi, hogy az Azure AD Connect által vezérelt beállítások.

| Beállítás | Leírás |
| :--- | :--- |
| Jogkivonat-aláíró tanúsítvány | Az Azure AD Connect alaphelyzetbe állítása, és hozza létre újból a bizalmi kapcsolat az Azure ad-vel is használható. Az Azure AD Connect egy jogkivonat-aláíró tanúsítványok közvetlen egyszeri kapcsolódó kulcsváltást nem az AD FS-hez, és frissíti az Azure AD-tartomány összevonási beállításokat.|
| Jogkivonat-aláíró algoritmus | A Microsoft javasolja, hogy a jogkivonat-aláíró algoritmus SHA-256 algoritmust használ. Az Azure AD Connect képes észlelni, ha a jogkivonat-aláíró algoritmus értékre van állítva egy kevésbé biztonságos, mint az SHA-256 algoritmust. A következő lehetséges konfigurációs művelet frissíti a beállítás az SHA-256. Más függő entitás megbízhatóságához frissíteni kell, hogy az új jogkivonat-aláíró tanúsítványt használja. |
| Az Azure AD megbízhatóságának azonosítója | Az Azure AD Connect beállítja az Azure AD-megbízhatóság megfelelő azonosító értékét. Az AD FS egyedileg azonosítja az Azure AD-megbízhatóság azonosító értékének használatával. |
| Azure AD-végpontok | Az Azure AD Connect gondoskodik arról, hogy a végpont konfigurálva az Azure AD-megbízhatóság mindig a legújabb ajánlott értékeit a rugalmasság és teljesítmény alapján. |
| Kiadás átalakítási szabályai | Nincsenek számok jogcímszabályok, amelyek szükségesek az optimális teljesítmény érdekében az Azure AD-t egy összevont beállítás funkcióit. Az Azure AD Connect gondoskodik arról, hogy az Azure AD-megbízhatóság mindig van-e konfigurálva a megfelelő ajánlott jogcímszabályok együtt. |
| Alternate-id | Ha a szinkronizálás másodlagos-azonosító használatára van konfigurálva, az Azure AD Connect konfigurálja az AD FS azonosítójával másodlagos hitelesítés végrehajtásához. |
| Metaadatok automatikus frissítés | Az Azure AD-megbízhatóság konfigurálva van az automatikus metaadat-frissítést. Az AD FS rendszeresen ellenőrzi a metaadatokat az Azure AD-megbízhatóság és tartja azt naprakészen abban az esetben, ha változik az Azure AD-oldalán. |
| Integrált Windows-hitelesítés (IWA) | Hibrid Azure AD join művelet során a IWA engedélyezve van az eszköz regisztrációjához megkönnyítése érdekében a régi verziójú eszközök hibrid Azure AD joinnal |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Végrehajtási folyamatokat és az Azure AD Connect által konfigurált összevonási beállítások

Az Azure AD connect nem az Azure AD-megbízhatóság minden beállítások frissítése a konfigurációs folyamat során. A módosított beállítások attól függenek, mely a feladat vagy a végrehajtási folyamat folyamatban van. Az alábbi táblázat a különböző végrehajtási folyamatokat az érintett beállításokat.

| A folyamat végrehajtása | Érintett beállításai |
| :--- | :--- |
| Először át telepítési (express) | None |
| Először adja át a telepítési (új AD FS-farm) | Létrejön egy új AD FS-farm, és az Azure AD-megbízhatóság jön létre sablon nélkül. |
| Először át telepítése (meglévő AD FS-farm, meglévő Azure AD-megbízhatóság) | Az Azure AD megbízhatóságának azonosítója, a kiadás átalakítási szabályai, az Azure AD-végpontok, másodlagos-azonosítója (ha szükséges), az automatikus metaadat-frissítés |
| Azure AD-megbízhatóság alaphelyzetbe állítása | A jogkivonatot aláíró tanúsítvánnyal, jogkivonat-aláíró algoritmus, az Azure ad-ben megbízhatóságának azonosítója, a kiállítási átalakítási szabályok, az Azure AD-végpontok, másodlagos-azonosítója (ha szükséges), az automatikus metaadat-frissítés |
| Összevonási kiszolgáló hozzáadása | None |
| WAP-kiszolgáló hozzáadása | None |
| Eszközbeállítások | Kiadás átalakítási szabályai, az eszközök regisztrációjával kapcsolatos IWA |
| Összevont tartomány hozzáadása | Ha a tartomány első alkalommal ad hozzá, azt jelenti, a telepítő a egyetlen tartomány-összevonási több tartomány-összevonási módosítása – az Azure AD Connect létrehozza a megbízhatósági előzmények. A megbízhatósági kapcsolatot az Azure ad-vel már konfigurálva van a több tartomány, ha csak kiadás átalakítási szabályai módosultak |
| Update SSL | None |

Minden művelet, amely során bármilyen érték módosított, az Azure AD Connect teszi biztonsági másolatot a jelenlegi adatvédelmi beállítások, **%ProgramData%\AADConnect\ADFS**

![Az Azure AD Connect lapot ábrázoló üzenet meglévő az Azure AD-megbízhatóság biztonsági mentéssel kapcsolatos](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> 1.1.873.0 verziónál régebbi a biztonsági mentés csak jogcímkiadás-átalakítási szabályok foglalnak el, és a varázsló a nyomkövetési naplófájl másolatban.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Az Azure AD Connect által beállított jogcímkiadás-átalakítási szabályok

Az Azure AD Connect gondoskodik arról, hogy az Azure AD-megbízhatóság mindig van-e konfigurálva a megfelelő ajánlott jogcímszabályok együtt. A Microsoft javasolja az Azure AD connect kezeléséhez az Azure AD-megbízhatóság. Ez a szakasz felsorolja a kiállítási átalakítási szabálykészlet és azok leírását.

| Szabály neve | Leírás |
| --- | --- |
| A probléma egyszerű felhasználónév | Ez a szabály lekérdezi a szinkronizálási beállításai userprincipalname attribútum viselkedéselemzési userprincipalname értékét.|
| Lekérdezés objectguid és egyéni ImmutableId jogcím msdsconsistencyguid | Ez a szabály hozzáad egy ideiglenes értékkel a folyamat az objectguid és msdsconsistencyguid érték Ha létezik |
| Msdsconsistencyguid meglétének ellenőrzése | Állítunk alapján e msdsconsistencyguid értékét, vagy nem létezik-e, melyiket érdemes használni, ImmutableId közvetlen ideiglenes azt a jelzőt |
| Nem módosítható azonosító msdsconsistencyguid ki, ha létezik | Adja ki a msdsconsistencyguid ImmutableId, ha az érték már létezik |
| ObjectGuidRule ki, ha msdsConsistencyGuid szabály nem létezik. | Ha msdsconsistencyguid értéke nem létezik, az érték az objectguid adják ki, immutableid azonosítója |
| A probléma nameidentifier | Ez a szabály a nameidentifier jogcím értéke problémák.|
| A probléma accounttype tartományhoz csatlakoztatott számítógépek esetében | Ha az entitás a hitelesített egy tartományba léptetett eszköz, ez a szabály problémák a fióktípust, DJ hangsúlyozva egy tartományba léptetett eszköz |
| A következő értékkel: Ha az nem egy számítógép-fiók felhasználó problémát AccountType | Ha az entitás a hitelesített felhasználó, ez a szabály a fióktípust problémák felhasználóként |
| Issuerid ki, ha az nem egy számítógép-fiók | Ez a szabály kiállítja a issuerId érték, ha a hitelesítendő entitást nem egy eszközt. Az érték reguláris kifejezést, amely szerint az Azure AD Connect használatával jön létre. A reguláris kifejezés után, figyelembe véve az összes tartományt összevont Azure AD Connect használatával jön létre. |
| A probléma issuerid a számítógép-hitelesítési DJ | Ez a szabály a issuerId érték problémák, ha a hitelesítendő entitást eszköz |
| A probléma onpremobjectguid tartományhoz csatlakoztatott számítógépek esetében | Ha az entitás a hitelesített egy tartományba léptetett eszköz, ez a szabály problémák a helyszíni objectguid az eszköz |
| Elsődleges biztonsági azonosítója továbbítása | Ez a szabály problémák a hitelesítendő entitást elsődleges biztonsági azonosítója |
| Jogcím - insideCorporateNetwork továbbítása | Ez a szabály problémák egy jogcímet, amely segít az Azure ad-ben, ha a hitelesítést a vállalati hálózaton belül, vagy külsőleg származik |
| Továbbítja a jogcímet – Psso |   |
| Jelszólejárati jogcímek kiállítása | Ez a szabály problémák három jogcímek a jelszó lejárati ideje, a jelszó lejár az a entitás a hitelesített napok száma és URL-cím where irányíthatja a jelszó módosítására.|
| Továbbítja a jogcímet – authnmethodsreferences | A alapján ez a szabály kiadott jogcím értéke azt jelzi, hogy milyen típusú hitelesítést hajtottak végre az entitás |
| Jogcím - multifactorauthenticationinstant továbbítása | Ez a jogcím értékét idejét határozza meg, UTC formátumban, amikor a felhasználó által legutóbb elvégzett több többtényezős hitelesítést. |
| Jogcím - AlternateLoginID továbbítása | Ez a szabály a AlternateLoginID jogcím problémák, ha a hitelesítést hajtottak végre használata a másodlagos bejelentkezési azonosítót. |

> [!NOTE]
> Probléma UPN és ImmutableId jogcímszabályok eltérőek, ha nem alapértelmezett választás az Azure AD Connect konfigurálása során

## <a name="restore-issuance-transform-rules"></a>Állítsa vissza a kiadás átalakítási szabályai

Az Azure AD Connect verziója 1.1.873.0 vagy újabb teszi a biztonsági másolatot az Azure AD megbízhatósági beállítások, amikor egy frissítés jön létre az Azure AD-megbízhatóság beállításokat. Az Azure AD-megbízhatóság beállításokat készül biztonsági másolat **%ProgramData%\AADConnect\ADFS**. A fájlnév formátuma a következő formátumban AadTrust -&lt;dátum&gt;-&lt;idő&gt;.txt, például: AadTrust-20180710-150216.txt

![A példában egy sanpshot biztonsági mentése az Azure AD-megbízhatóság](./media/how-to-connect-azure-ad-trust/backup.png)

Visszaállíthatja az alábbi javasolt lépésekkel jogcímkiadás-átalakítási szabályok

1. Nyissa meg az AD FS felügyeleti felhasználói Felületét a Kiszolgálókezelőben
2. Nyissa meg az Azure ad-ben megbízhatósági kapcsolat tulajdonságai címen **az AD FS &gt; függő entitások Megbízhatóságainak &gt; a Microsoft Office 365 Identity Platform &gt; jogcím-kiállítási szabályzat szerkesztése**
3. Kattintson a **szabály hozzáadása**
4. A jogcímszabály-sablon, válassza a Küldés jogcímek egy egyéni szabályt, és kattintson a **tovább**
5. A jogcímszabály neve másolhat a biztonságimásolat-fájlt, és illessze be a mező **Jogcímszabály neve**
6. A jogcímszabály másolja a biztonságimásolat-fájlt a szövegmezőhöz a **egyéni szabály** kattintson **Befejezés**

> [!NOTE]
> Győződjön meg arról, hogy a további szabályok nem ütköznek a az Azure AD Connect által konfigurált szabályokat.

## <a name="next-steps"></a>További lépések
* [Kezelés és testreszabás Active Directory összevonási szolgáltatások az Azure AD Connect használatával](how-to-connect-fed-management.md)
