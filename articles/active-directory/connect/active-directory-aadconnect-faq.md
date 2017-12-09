---
title: "Az Azure Active Directory Connect: Gyakori kérdések – |} Microsoft Docs"
description: "Ezen a lapon az Azure AD Connect vonatkozó gyakran ismételt kérdések."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: billmath
ms.openlocfilehash: be940b694ba95529b5f5a84acfd789240354dae2
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Gyakori kérdések az Azure Active Directory Connect

## <a name="general-installation"></a>Általános telepítési
**K: telepítési működnek, ha az Azure AD globális rendszergazda 2FA engedélyezve van?**  
A 2016. februári a buildek Ez támogatott.

**K: az Azure AD Connect felügyelet nélküli telepítéséhez úgy van?**  
Csak a telepítési varázsló segítségével az Azure AD Connect telepítése támogatott. Felügyelet nélküli és a csendes telepítés nem támogatott.

**K: erdővel rendelkezem ahol tartománya nem érhető el. Hogyan kell telepíteni az Azure AD Connect?**  
A 2016. februári a buildek Ez támogatott.

**K: az Active Directory tartományi szolgáltatások health-ügynök működik a server core?**  
Igen. Az ügynök telepítése után végezze el a regisztrációs folyamat során a következő PowerShell-parancsmag használatával: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: AADConnect támogatja az Azure ad-val a két tartomány szinkronizálását?**</br>
Igen, ez támogatott. Tekintse meg [több tartományban](active-directory-aadconnect-multiple-domains.md)
 
**K: nem támogatjuk kellene több összekötő az Active Directory-tartományhoz az Azure AD connect?**</br> Nem, ez nem támogatott 

## <a name="network"></a>Network (Hálózat)
**K: van egy tűzfal, a hálózati eszköz, vagy valami mást, amely korlátozza a maximális időt kapcsolatok maradhat, nyissa meg a hálózaton. Mennyi ideig kell a ügyfél ügyféloldali időkorlát küszöbértéke lehet, ha az Azure AD Connect használatával?**  
Minden hálózati szoftver, a fizikai eszközök vagy bármi más, amely korlátozza a maximális időt kapcsolatok nyitva maradhat a használata ajánlott a küszöbérték legalább 5 perc (300 másodperc) a kiszolgálóra, ahol az Azure AD Connect-ügyfél telepítve van és az Azure Active Directory közötti kapcsolatot. Ez vonatkozik az összes korábban kiadott Microsoft Identity szinkronizálási eszközöket is.

**K: vannak (egy címke tartományok) által támogatott?**  
Nem, az Azure AD Connect nem támogatja a helyi erdők/tartományok által használatával.

**K: támogatott különálló AD-tartományok erdők vannak?**  
Nem, az Azure AD Connect nem támogatja a helyszíni erdők tartalmazó különálló névtereket.

**K: vannak "pontozott" nevű NetBios támogatott?**  
Nem, az Azure AD Connect nem támogatja a helyi erdők/tartományok ahol a NetBIOS-név pontot tartalmaz "." a neve.

**K: támogatott tiszta IPv6-környezetben van?**  
Nem, az Azure AD Connect nem támogatja a csak IPv6-környezetben.

## <a name="federation"></a>Összevonás
**K: Mit tehetek, ha jelenik meg egy e-mailt, amely az Office 365 tanúsítvány megújítása kérése**  
Útmutatás a a [megújítani a tanúsítványokat](active-directory-aadconnect-o365-certs.md) témakör: a tanúsítvány megújításához.

**K: van "automatikus frissítése függő" állítsa be az Office 365 függő entitáshoz. Kell tennie semmit, amikor a jogkivonat-aláíró tanúsítványa automatikusan visszaállítja a?**  
A cikkben ismertetett útmutatás [megújítani a tanúsítványokat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Környezet
**K: támogatott a kiszolgáló átnevezése, az Azure AD Connect telepítése után?**  
Nem. A kiszolgálónév módosítása esetén a szinkronizálási motor nem lehet kapcsolódni az SQL-adatbázishoz, és a szolgáltatás nem fogja tudni elindítani.

## <a name="identity-data"></a>Azonosító adatok
**K: az egyszerű felhasználónév (userPrincipalName) attribútum az Azure ad-ben nem egyezik a helyszíni UPN - miért?**  
Ezek a cikkek lásd:

* [Office 365, az Azure vagy az Intune-ban szereplő felhasználónevek nem egyeznek meg, a helyszíni egyszerű Felhasználónévvel vagy másodlagos bejelentkezési Azonosítóval](https://support.microsoft.com/en-us/kb/2523192)
* [A felhasználói fiók egyszerű másik összevont tartományt használandó módosítása után a módosítások nem az Azure Active Directory szinkronizáló eszköz által szinkronizálása](https://support.microsoft.com/en-us/kb/2669550)

Beállíthatja úgy is engedélyezi a szinkronizálási motor frissítése a userPrincipalName leírtak szerint az Azure AD [az Azure AD Connect szinkronizálási szolgáltatás szolgáltatások](active-directory-aadconnectsyncservice-features.md).

**K: enyhe találatra támogatja azt a helyszíni AD-csoport vagy partner meglévő Azure AD-csoport vagy partner objektummal objektumokat?**  
Nem, ez jelenleg nem támogatott.

**K:, mert az azt manuális módon állítsa be a támogatott merevlemez egyező úgy, hogy a meglévő Azure AD-csoport vagy partner objektumok attribútum ImmutableId a helyszíni AD-csoport vagy partner objektumok?**  
Nem, ez jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: hol szerepelnek a PowerShell-parancsmagok az Azure AD Connect?**  
Kivételével a parancsmagokat ezen a helyen más található az Azure AD Connect PowerShell-parancsmagok nem támogatottak a ügyfél használja.

**K: használhatok "Kiszolgáló exportálási/kiszolgáló importálás" található *Synchronization Service Managert* helyezhető át a konfigurációs kiszolgáló között?**  
Nem. Ez a beállítás az összes konfigurációs beállítások beolvasása sikertelen lesz, és nem használható. Ehelyett használjon a varázsló az alapkonfiguráció létrehozása a második kiszolgálón, és a szerkesztővel szinkronizálási szabály létrehozása a PowerShell-parancsfájlokat egyéni szabályok áthelyezése kiszolgálók között. Lásd: [áttelepítési éppen](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**K: kell gyorsítótárazzák a jelszavakat az Azure bejelentkezési oldal, és ez megelőzhető ugyanis tartalmaz. a jelszó bemeneti elemnek az automatikus kiegészítés = "false" attribútum?**</br>
Jelenleg nem támogatjuk a jelszó bemeneti mező, beleértve az automatikus kiegészítés címke a HTML-attribútumok módosítását. Jelenleg dolgozunk egy szolgáltatás, amely lehetővé teszi a egyéni Javascript, amely lehetővé teszi azok az attribútumok hozzáadása a jelszó mező. 2017 elérhető későbbi része legyen.

**K: az Azure bejelentkezési lapon felhasználónevei, akik korábban már sikeresen bejelentkezett felhasználók jelennek meg.  Ez a viselkedés kikapcsolható?**</br>
Jelenleg nem támogatjuk a bejelentkezési oldal HTML attribútumainak módosítása. Jelenleg dolgozunk egy szolgáltatás, amely lehetővé teszi a egyéni Javascript, amely lehetővé teszi azok az attribútumok hozzáadása a jelszó mező. 2017 elérhető későbbi része legyen.

**K: akadályozza meg, hogy a munkamenetek van?**</br>
Nem.

## <a name="troubleshooting"></a>Hibaelhárítás
**K: hogyan kaphat segítséget az Azure AD Connect?**

[A Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Keresse meg a Microsoft Tudásbázis (KB) az Azure AD Connect-támogatással kapcsolatos gyakori javítás / csere problémákat műszaki megoldások keresése.

[A Microsoft Azure Active Directory-fórumok](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Keressen, és keresse meg a technikai kérdések és a közösségi válaszok vagy saját kérdés feltevése kattintva [Itt](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Hogyan kérhet támogatást az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

* Ez a hivatkozás segítségével segítségre van szüksége az Azure portálon keresztül.

